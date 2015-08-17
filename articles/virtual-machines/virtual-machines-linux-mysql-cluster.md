<properties
	pageTitle="Usando conjuntos de carga balanceada para clusterizar MySQL em Linux"
	description="Um artigo que ilustra padrões para configurar um cluster Linux de alta disponibilidade e carga balanceada no Azure, utilizando o MySQL como exemplo"
	services="virtual-machines"
	documentationCenter=""
	authors="bureado"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/14/2015"
	ms.author="jparrel"/>

# Usando conjuntos de carga balanceada para clusterizar MySQL em Linux

* [Preparando-se](#getting-ready)
* [Configurando o cluster](#setting-up-the-cluster)
* [Configurando o MySQL](#setting-up-mysql)
* [Configurando o Corosync](#setting-up-corosync)
* [Configurando o Pacemaker](#setting-up-pacemaker)
* [Testando](#testing)
* [STONITH](#stonith)
* [Limitações](#limitations)

## Introdução

A finalidade deste artigo é explorar e ilustrar as diferentes abordagens disponíveis para implantar serviços com base em Linux, altamente disponíveis no Microsoft Azure, explorando a alta disponibilidade do MySQL Server como elemento principal. Há um vídeo ilustrando essa abordagem disponível no [Channel 9](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL).

Descrevemos uma solução de alta disponibilidade do MySQL de mestre único, dois nós e sem compartilhar nada, com base em DRBD, Corosync e Pacemaker. Apenas um nó executa o MySQL por vez. A leitura e a gravação do recurso DRBD também está limitada a apenas um nó por vez.

Não há necessidade de uma solução VIP como LVS porque usamos conjuntos de balanceamento de carga do Microsoft Azure para fornecer a funcionalidade de round robin e a detecção do ponto de extremidade, além da remoção e da recuperação amigável do VIP. O VIP é um endereço IPv4 globalmente roteável, atribuído pelo Microsoft Azure ao criarmos o serviço de nuvem pela primeira vez.

Existem outras arquiteturas possíveis para MySQL, inclusive NBD Cluster, Percona e Galera, bem como diversas soluções de middleware, inclusive pelo menos uma disponível como uma VM em [Repositório de VM](http://vmdepot.msopentech.com). Como essas soluções podem replicar em unicast X multicast ou difusão e não dependem de armazenamento compartilhado ou de várias interfaces de rede, os cenários devem ser fáceis de implantar no Microsoft Azure.

Obviamente, essas arquiteturas de clustering podem ser estendidas até outro produtos como PostgreSQL e OpenLDAP de maneira semelhante. Por exemplo, esse procedimento de balanceamento de carga sem compartilhar nada foi testado com êxito com o OpenLDAP multimestres, e é possível ver isso em nosso blog Channel 9.

## Preparando-se

Você precisará de uma conta do Microsoft Azure com uma assinatura válida para criar pelo menos duas (2) VMs (XS foi usado neste exemplo), uma rede e uma sub-rede, além de um grupo de afinidade e um conjunto de disponibilidade, bem como da possibilidade de criar novos VHDs na mesma região do serviço de nuvem e de anexá-los às VMs do Linux.

### Ambiente testado

- Ubuntu 13.10
  - DRBD
  - MySQL Server
  - Corosync e Pacemaker

### Grupo de afinidade

Um grupo de afinidade para a solução é criada pelo registro em log no Portal do Azure rolando-se a tela para baixo até Configurações e criando-se um novo grupo de afinidade. Os recursos alocados criados posteriormente serão atribuídos a esse grupo de afinidade.

### Redes

Uma nova rede é criada, e uma sub-rede é criada dentro da rede. Escolhemos uma rede 10.10.10.0/24 com apenas uma sub-rede /24 dentro.

### Máquinas virtuais

A primeira VM do Ubuntu 13.10 é criada usando-se uma imagem da Endorsed Ubuntu Gallery e chamada `hadb01`. Um novo serviço de nuvem é criado no processo, chamado hadb. Demos esse nome para ilustrar a natureza compartilhada, de balanceamento de carga, que o serviço terá quando adicionarmos mais recursos. A criação de `hadb01` é tranquila e concluída usando o portal. Um ponto de extremidade para SSH é criado automaticamente e a nossa rede criada é selecionada. Também optamos por criar um novo conjunto de disponibilidade para as VMs.

Assim que a primeira VM é criada (tecnicamente, quando o serviço de nuvem é criado), passamos à criação da segunda VM, `hadb02`. Para a segunda VM, também usaremos a VM Ubuntu 13.10 da Gallery usando o Portal, mas optaremos por usar um serviço de nuvem existente, `hadb.cloudapp.net`, em vez de criar uma nova. A rede e o conjunto de disponibilidade devem ser selecionadas automaticamente para nós. Também será criado um ponto de extremidade SSH.

Depois que ambas as VMs forem criadas, anotaremos a porta SSH de `hadb01` (TCP 22) e de `hadb02` (atribuída automaticamente pelo Azure)

### Armazenamento anexado

Anexamos um novo disco a ambas as VMs e criamos novos discos de 5 GB no processo. Os discos serão hospedados no contêiner VHD em uso para os discos do nosso sistema operacional principal. Depois que os discos são criados e anexados não há necessidade de reiniciarmos o Linux, porque o kernel verá o novo dispositivo (normalmente, `/dev/sdc`; é possível verificar `dmesg` para ver a saída)

Em cada VM, passamos à criação de uma nova partição usando `cfdisk` (principalmente a partição do Linux) e gravamos a nova tabela de partição. **Não crie um sistema de arquivos nessa partição** .

## Configurando o cluster

Em ambas as VMs Ubuntu, precisamos usar o APT para instalar Corosync, Pacemaker e DRBD. Usando `apt-get`:

    sudo apt-get install corosync pacemaker drbd8-utils.

**Não instale o MySQL neste momento** . Os scripts de instalação do Debian e do Ubuntu inicializarão um diretório de dados do MySQL em `/var/lib/mysql`, mas como o diretório será substituído por um sistema de arquivos DRBD, precisamos fazer isso depois.

A esta altura, também devemos verificar (usando `/sbin/ifconfig`) se ambas as VMs estão usando endereços na sub-rede 10.10.10.0/24 e se podem executar ping uma na outra, pelo nome. Se quiser, você também pode usar `ssh-keygen` e `ssh-copy-id` para verificar se ambas as VMs conseguem se comunicar via SSH sem exigir uma senha.

### Configurando DRBD

Criaremos um recurso DRBD que usa a partição subjacente `/dev/sdc1` para produzir um recurso `/dev/drbd1` capaz de ser formatado usando ext3 e usado em nós primários e secundários. Para isso, abra `/etc/drbd.d/r0.res` e copie a definição de recurso a seguir. Faça isso em ambas as VMs:

    resource r0 {
      on `hadb01` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.4:7789;
        meta-disk internal;
      }
      on `hadb02` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.5:7789;
        meta-disk internal;
      }
    }

Depois de fazer isso, inicialize o recurso usando `drbdadm` em ambas as VMs:

    sudo drbdadm -c /etc/drbd.conf role r0
    sudo drbdadm up r0

E, por fim, na propriedade forçada primária (`hadb01`) (principal) do recurso DRBD:

    sudo drbdadm primary --force r0

Se examinar o conteúdo de /proc/drbd (`sudo cat /proc/drbd`) em ambas as VMs, você deve ver `Primary/Secondary` em `hadb01` e `Secondary/Primary` em `hadb02`, consistente com a solução até o momento. O disco de 5 GB será sincronizado na rede 10.10.10.0/24 sem encargos para os clientes.

Assim que o disco for sincronizado, será possível criar o sistema de arquivos em `hadb01`. Para finalidades de testes, usamos ext2, mas a seguinte instrução criará um sistema de arquivos ext3:

    mkfs.ext3 /dev/drbd1

### Montando o recurso DRBD

No `hadb01`, agora estamos prontos para montar os recursos DRBD. Debian e derivativos usam `/var/lib/mysql` como diretório de dados do MySQL. Como não instalamos o MySQL, iremos criar o diretório e montar o recurso DRBD. No `hadb01`:

    sudo mkdir /var/lib/mysql
    sudo mount /dev/drbd1 /var/lib/mysql

## Configurando o MySQL

Agora você está pronto para instalar o MySQL em `hadb01`:

    sudo apt-get install mysql-server

Você tem duas opções para `hadb02`. Você pode instalar o mysql-server, que criará /var/lib/mysql e o preencherá com um novo diretório de dados e, em seguida, passará à remoção do conteúdo. No `hadb02`:

    sudo apt-get install mysql-server
    sudo service mysql stop
    sudo rm –rf /var/lib/mysql/*

A segunda opção é de failover para `hadb02` e instalar lá o mysql-server (os scripts de instalação perceberão a instalação existente e não tocarão nela)

No `hadb01`:

    sudo drbdadm secondary –force r0

No `hadb02`:

    sudo drbdadm primary –force r0
    sudo apt-get install mysql-server

Se não pretende usar failover no DRBD agora, a primeira opção é mais fácil, embora indiscutivelmente menos elegante. Depois de configurá-lo, você pode começar a trabalhar no banco de dados do MySQL. Em `hadb02` (ou qualquer um dos servidores ativo, de acordo com o DRBD):

    mysql –u root –p
    CREATE DATABASE azureha;
    CREATE TABLE things ( id SERIAL, name VARCHAR(255) );
    INSERT INTO things VALUES (1, "Yet another entity");
    GRANT ALL ON things.* TO root;

**Aviso**: a última instrução desabilita efetivamente a autenticação para o usuário raiz nessa tabela. Ela deve ser substituída pelas instruções GRANT de nível de produção e só é incluída para fins de ilustração.

Você também precisa habilitar a rede para MySQL caso queira fazer consultas externas às VMs, que é a finalidade deste guia. Em ambas as VMs, abra `/etc/mysql/my.cnf` e navegue até `bind-address`; altere-as de 127.0.0.1 para 0.0.0.0. Depois de salvar o arquivo, emita uma `sudo service mysql restart` no primário atual.

### Criando o conjunto de balanceamento de carga do MySQL

Retornaremos ao Portal do Azure e navegaremos até a VM `hadb01` e, em seguida, aos pontos de extremidade. Iremos criar um novo ponto de extremidade, escolher MySQL (TCP 3306) no menu suspenso e marcar na caixa *Criar novo conjunto de balanceamento de carga*. Chamaremos nosso ponto de extremidade de balanceamento de carga `lb-mysql`. Deixaremos a maioria das opções sozinha, exceto o tempo, que reduziremos para 5 (segundos, o mínimo)

Depois que o ponto de extremidade for criado, vamos até `hadb02`, Pontos de extremidade, e criaremos um novo ponto de extremidade, mas escolheremos `lb-mysql` e selecionaremos MySQL no menu suspenso. Também é possível usar a CLI do Azure nesta etapa.

No momento, temos tudo o que precisamos para uma operação manual do cluster.

### Testando o conjunto de balanceamento de carga

Os testes podem ser realizados em uma máquina externa usando qualquer cliente do MySQL, bem como aplicativos (ex.: phpMyAdmin em execução como um Site do Azure). Nesse caso, usamos a ferramenta de linha de comando do MySQL em outra caixa do Linux:

    mysql azureha –u root –h hadb.cloudapp.net –e "select * from things;"

### Executando failover manualmente

Agora você pode simular failovers desativando o MySQL, alternando o primário do DRBD e reiniciando o MySQL.

Em hadb01:

    service mysql stop && umount /var/lib/mysql ; drbdadm secondary r0

Em seguida, em hadb02:

    drbdadm primary r0 ; mount /dev/drbd1 /var/lib/mysql && service mysql start

Depois de executar failover manualmente, você poderá repetir a consulta remota e ela tem que estar funcionando perfeitamente.

## Configurando o Corosync

Corosync é a infraestrutura de cluster subjacente necessária ao funcionamento do Pacemaker. Para usuários do Heartbeat v1 e v2 (e outras metodologias como Ultramonkey), Corosync é uma divisão das funcionalidades de CRM, enquanto o Pacemaker continua mais semelhante ao Heartbeat em funcionalidade.

A restrição principal do Corosync no Azure é que o Corosync prefere comunicação multicast via broadcast via unicast, mas a rede do Microsoft Azure só é compatível com unicast.

Felizmente, o Corosync tem um modo unicast funcional, a única restrição real é que, como todos os nós não estão se comunicando entre si *automaticamente*, você precisa definir os nós em arquivos de configuração, inclusive seus endereços IP. Podemos usar os arquivos de exemplo do Corosync para unicast e apenas alterar o endereço de associação, as listas de nós e o diretório do registro em log (o Ubuntu usa `/var/log/corosync` e os arquivos de exemplo usam `/var/log/cluster`), além de habilitar as ferramentas de quorum.

**Observe a diretiva `transport: udpu` abaixo e os endereços IP definidos manualmente para os nós**.

Em `/etc/corosync/corosync.conf` para ambos os nós:

    totem {
      version: 2
      crypto_cipher: none
      crypto_hash: none
      interface {
        ringnumber: 0
        bindnetaddr: 10.10.10.0
        mcastport: 5405
        ttl: 1
      }
      transport: udpu
    }

    logging {
      fileline: off
      to_logfile: yes
      to_syslog: yes
      logfile: /var/log/corosync/corosync.log
      debug: off
      timestamp: on
      logger_subsys {
        subsys: QUORUM
        debug: off
        }
      }

    nodelist {
      node {
        ring0_addr: 10.10.10.4
        nodeid: 1
      }

      node {
        ring0_addr: 10.10.10.5
        nodeid: 2
      }
    }

    quorum {
      provider: corosync_votequorum
    }

Copiamos esse arquivo de configuração em ambas as VMs e iniciamos o Corosync em ambos os nós:

    sudo service start corosync

Logo depois de iniciar o serviço, o cluster deverá estar estabelecido no anel atual e o quorum deverá estar constituído. Podemos verificar essa funcionalidade examinando logs ou:

    sudo corosync-quorumtool –l

Deve se seguir uma saída semelhante à da imagem abaixo:

![corosync-quorumtool -l sample output](media/virtual-machines-linux-mysql-cluster/image001.png)

## Configurando o Pacemaker

O Pacemaker usa o cluster para monitorar recursos, definir quando os primários são desativados e alternar esses recursos para os secundários. Os recursos podem ser definidos com base em um conjunto de scripts disponíveis ou de scripts LSB (init-like), entre outras opções.

Queremos que o Pacemaker seja o “proprietário” do recurso DRBD, do ponto de montagem e do serviço MySQL. Se o Pacemaker puder ativar e desativar o DRBD, monte/desmonte-o e inicie/pare o MySQL na ordem certa quando algo ruim acontecer com o primário, então nossa instalação está concluída.

Ao instalar o Pacemaker pela primeira vez, a configuração deve ser bem simples, algo como:

    node $id="1" hadb01
      attributes standby="off"
    node $id="2" hadb02
      attributes standby="off"

Verifique isso executando `sudo crm configure show`. Agora, crie um arquivo (digamos, `/tmp/cluster.conf`) com os seguintes recursos:

    primitive drbd_mysql ocf:linbit:drbd \
          params drbd_resource="r0" \
          op monitor interval="29s" role="Master" \
          op monitor interval="31s" role="Slave"

    ms ms_drbd_mysql drbd_mysql \
          meta master-max="1" master-node-max="1" \
            clone-max="2" clone-node-max="1" \
            notify="true"

    primitive fs_mysql ocf:heartbeat:Filesystem \
          params device="/dev/drbd/by-res/r0" \
          directory="/var/lib/mysql" fstype="ext3"

    primitive mysqld lsb:mysql

    group mysql fs_mysql mysqld

    colocation mysql_on_drbd \
           inf: mysql ms_drbd_mysql:Master

    order mysql_after_drbd \
           inf: ms_drbd_mysql:promote mysql:start

    property stonith-enabled=false

    property no-quorum-policy=ignore

E agora carregue-o na configuração (você só precisa fazer isso em um nó):

    sudo crm configure
      load update /tmp/cluster.conf
      commit
      exit

Além disso, verifique se o Pacemaker é iniciado durante a inicialização em ambos os nós:

    sudo update-rc.d pacemaker defaults

Depois de alguns segundos, e usando `sudo crm_mon –L`, verifique se um dos nós se tornou o mestre do cluster e está executando todos os recursos. Você pode usar montar e verificar se os recursos estão em execução.

A captura de tela a seguir mostra `crm_mon` com um nó parado (saia usando Control-C)

![crm\_mon node stopped](media/virtual-machines-linux-mysql-cluster/image002.png)

E essa captura de tela mostra ambos os nós, com um mestre e um subordinado:

![crm\_mon operational master/slave](media/virtual-machines-linux-mysql-cluster/image003.png)

## Testando

Estamos prontos para uma simulação de failover automático. Existem duas maneiras de fazer isso: a fácil e a difícil. A maneira fácil é usar a função de desligamento do cluster: ``crm_standby -U `uname -n` -v on``. Usando isso no mestre, o subordinado assumirá. Não se esqueça de desativá-lo (ou crm\_mon informará que um nó está em espera)

A maneira difícil é desligando a VM primária (hadb01) por meio do Portal ou alterando o nível de execução na VM (ou seja: parada, desligamento) e, assim, estamos ajudando o Corosync e o Pacemaker sinalizando o desligamento do mestre. Podemos testar isso (útil para janelas de manutenção), mas também podemos forçar o cenário apenas congelando a VM.

## STONITH

Deve ser possível emitir um desligamento da VM por meio da CLI do Azure, em vez de um script STONITH que controle um dispositivo físico. É possível usar `/usr/lib/stonith/plugins/external/ssh` como base e habilitar STONITH na configuração do cluster. A CLI do Azure deve estar instalada globalmente e as configurações/perfil de publicação devem ser carregadas para o usuário do cluster.

Código de exemplo para o recurso disponível em [GitHub](https://github.com/bureado/aztonith). Você precisa alterar a configuração do cluster adicionando o seguinte a `sudo crm configure`:

    primitive st-azure stonith:external/azure \
      params hostlist="hadb01 hadb02" \
      clone fencing st-azure \
      property stonith-enabled=true \
      commit

**Observação:** o script não realiza verificações para cima/para baixo. O recurso SSH original apresentava 15 verificações de ping, mas o tempo de recuperação de uma VM do Azure pode ser mais variável.

## Limitações

As seguintes limitações se aplicam:

- O script do recurso DRBD linbit que gerencia DRBD como um recurso no Pacemaker usa `drbdadm down` durante a desativação de um nó, mesmo que o nó esteja apenas em modo de espera. Isso não é o ideal, pois o subordinado não sincronizará o recurso DRBD enquanto o mestre receber gravações. Se o mestre não falhar, o escravo poderá assumir um estado do sistema de arquivos mais antigo. Existem duas maneiras em potencial de resolver isso:
  - Impondo um `drbdadm up r0` a todos os nós de cluster por meio de um watchdog local (não clusterizado) ou
  - Editando o script DRBD linbit, verificando se `down` não é chamado, em `/usr/lib/ocf/resource.d/linbit/drbd`.
- Como o balanceador de carga precisa de pelo menos 5 segundos para responder, os aplicativos devem reconhecer o cluster e ser mais tolerantes ao tempo limite. Outras características também podem ajudar, por exemplo, filas em aplicativo, middleware de consulta, etc.
- O ajuste do MySQL é necessário para garantir que a gravação seja feita em um ritmo confortável e os caches sejam liberados para o disco com a maior frequência possível para minimizar a perda de memória
- O desempenho da gravação dependerá da interconexão da VM no comutador virtual porque esse é o mecanismo usado pelo DRBD para replicar o dispositivo
 

<!---HONumber=August15_HO6-->