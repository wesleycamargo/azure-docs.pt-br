---
title: "Alta disponibilidade e recuperação de desastre do SAP HANA no Azure (instâncias grandes) | Microsoft Docs"
description: "Estabeleça a alta disponibilidade e planeje a recuperação de desastre do SAP HANA no Azure (instâncias grandes)."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: 1a133d7710b1790241c0ff774c2fd40e86b64268
ms.openlocfilehash: f0248ce46991d23c2f5bb0f1c8bf212207b7ad5f


---
# <a name="high-availability-and-disaster-recovery-of-sap-hana-on-azure-large-instances"></a>Alta disponibilidade e recuperação de desastre do SAP HANA no Azure (instâncias grandes)

A alta disponibilidade e a recuperação de desastre são aspectos importantes da execução crucial do SAP HANA em servidores do Azure (instâncias grandes). É importante trabalhar junto com a SAP, seu integrador de sistemas ou a Microsoft para arquitetar e implementar adequadamente a estratégia de alta disponibilidade e recuperação de desastre adequada para você. Também é importante considerar o objetivo do ponto de recuperação e o objetivo do tempo de recuperação, que são específicos para seu ambiente.

## <a name="high-availability"></a>Alta disponibilidade

A Microsoft dá suporte aos métodos de alta disponibilidade do SAP HANA “prontos”, que incluem:

- **Replicação de armazenamento:** a capacidade do próprio sistema de armazenamento replicar todos os dados para outro local (no mesmo data center ou em outro, separado). O SAP HANA funciona independentemente desse método.
- **Replicação do sistema HANA:** a replicação de todos os dados no SAP HANA para um sistema separado. O objetivo de tempo de recuperação é minimizado por meio da replicação de dados em intervalos regulares. O SAP HANA dá suporte os modos assíncronos, síncronos in-memory e síncronos (recomendados para sistemas SAP HANA somente dentro do mesmo data center ou a menos de 100 km de distância). No design atual dos carimbos de instância grande HANA, a replicação de sistema HANA pode ser usada apenas para alta disponibilidade.
- **Failover automático de host:** uma solução de recuperação de falhas local para ser usada como uma alternativa à replicação do sistema. Quando o nó mestre fica indisponível, um ou mais nós SAP HANA em espera são configurados no modo de escala horizontal e o SAP HANA faz automaticamente o failover para outro nó.

Para saber mais sobre a Alta Disponibilidade do SAP HANA, veja as seguintes informações do SAP:

- [Whitepaper de Alta Disponibilidade do SAP HANA](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [Guia de administração do SAP HANA](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [Vídeo do SAP Academy sobre Replicação do Sistema SAP HANA](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP Note de Suporte nº 1999880 – perguntas frequentes sobre replicação do sistema SAP HANA](https://bcs.wdf.sap.corp/sap/support/notes/1999880)
- [SAP Note de Suporte SAP nº 2165547 – backup e restauração do SAP HANA no ambiente de replicação do sistema SAP HANA](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP Note de Suporte SAP nº 1984882 – uso da replicação do sistema SAP HANA para troca de hardware com tempo de inatividade mínimo/zero](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="disaster-recovery"></a>Recuperação de desastre

O SAP HANA no Azure (Instâncias Grandes) é oferecido em duas regiões do Azure em uma região geopolítica. Entre os dois carimbos de Instância Grande de duas regiões diferentes, há uma conectividade de rede direta para replicar dados durante a Recuperação de Desastre. A replicação dos dados se baseia na infraestrutura de armazenamento. A replicação não é feita por padrão. Isso é feito para as configurações de clientes que solicitaram a recuperação de desastre. No design atual, replicação de sistema HANA não pode ser usada para recuperação de desastre.

No entanto, para aproveitar os benefícios da recuperação de desastre, você precisa começar a projetar a conectividade de rede para as duas regiões diferentes do Azure. Para tal, você precisaria de uma conexão de circuito do Azure ExpressRoute conectado aos locais em sua região principal do Azure e outro circuito conectado aos locais em sua região de recuperação de desastre. Essa medida abrange uma situação em que uma região do Azure completa, incluindo local do MSEE (Microsoft Enterprise Edge Router), tem um problema.

Como uma segunda medida, você pode se conectar a todas as Redes Virtuais do Azure que se conectam ao SAP HANA no Azure (Instâncias Grandes) em uma das regiões para ambos os circuitos do ExpressRoute. Essa medida resolve casos em que apenas um dos locais de MSEE que se conecta ao seu local com o Azure está fora de serviço.

A figura a seguir mostra a configuração ideal para a Recuperação de Desastre:

![Configuração ideal para a recuperação de desastre](./media/sap-hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)

O caso ideal para uma configuração de recuperação de desastre da rede é ter dois circuitos do ExpressRoute do local para as duas regiões do Azure diferentes. Um circuito vai para a região nº 1, executando uma instância de produção. O segundo circuito do ExpressRoute vai para a região 2, executando algumas instâncias do HANA de não produção. (Isso é importante se toda a região do Azure, incluindo os carimbos de instância grande e MSEE ficarem inativos.)

Como uma segunda medida, as várias redes virtuais estão conectadas aos diversos circuitos do ExpressRoute que estão conectados ao SAP HANA no Azure (instâncias grandes). Você pode ignorar o local em que um MSEE está falhando ou você pode diminuir o objetivo de ponto de recuperação para recuperação de desastre, como abordaremos posteriormente.

Os próximos requisitos para uma configuração de recuperação de desastre são:

- Você precisa solicitar SKUs do SAP HANA no Azure (Instâncias Grandes) do mesmo tamanho das suas SKUs de produção ordenadas e implantadas na região de recuperação de desastre. Essas instâncias podem ser usadas para executar testes, áreas restritas e instância de garantia de qualidade do HANA.
- Você precisa solicitar um perfil de recuperação de desastre para cada uma das SKUs do SAP HANA no Azure (Instâncias Grandes) que você deseja recuperar no site de recuperação de desastre, se necessário. Essa ação leva à alocação de volumes de armazenamento, que são o destino de replicação de armazenamento de sua região de produção para a região de recuperação de desastre.

Depois que você atender aos requisitos acima, é sua responsabilidade iniciar a replicação do armazenamento. Na infraestrutura de armazenamento usada para o SAP HANA no Azure (Instâncias Grandes), a base de replicação de armazenamento é composta por instantâneos de armazenamento. Para iniciar a replicação de recuperação de desastre, você precisa executar:

- Um instantâneo do LUN de inicialização conforme descrito anteriormente.
- Um instantâneo dos volumes relacionados ao HANA conforme descrito anteriormente.

Após a execução desses instantâneos, a réplica inicial de volumes é propagada nos volumes associados a seu perfil de recuperação de desastre na região de recuperação de desastre.

Posteriormente, o instantâneo de armazenamento mais recente é usado a cada hora para replicar os deltas que se desenvolvem nos volumes de armazenamento.

O objetivo de ponto de recuperação que é obtido com essa configuração é de 60 a 90 minutos. Para alcançar um melhor objetivo de ponto de recuperação, no caso de recuperação de desastre, você precisa copiar os backups de log de transações do HANA do SAP HANA no Azure (Instâncias Grandes) e para outra região do Azure. Para atingir esse objetivo de ponto de recuperação, faça o seguinte:

1. Faça backup do log de transações do HANA com a maior frequência possível para /hana/log/backup.
2. Copie os backups de log de transações quando eles terminarem em uma máquina virtual (VM) do Azure, que está em uma rede virtual conectada ao servidor do SAP HANA no Azure (Instâncias Grandes).
3. Da VM, copie o backup para uma VM que está em uma rede virtual na região de recuperação de desastre.
4. Mantenha os backups do log de transações nessa região da VM.

Em caso de desastre, depois que o perfil de recuperação de desastre tiver sido implantado em um servidor real, copie os backups de log de transações da VM para o SAP HANA no Azure (instâncias grandes), que agora é o servidor primário na região de recuperação de desastre e restaure esses backups. Essa recuperação é possível porque o estado do HANA nos discos de recuperação de desastre é igual ao do instantâneo do HANA. Este é o ponto de deslocamento para outras restaurações de backups de log de transações.

## <a name="backup-and-restore"></a>Backup e restauração

Um dos aspectos mais importantes para bancos de dados operacionais é verificar se o banco de dados pode ser protegido de diversos eventos catastróficos. Esses eventos podem ser causados por qualquer coisa, desde desastres naturais até erros de usuário simples.

O backup de um banco de dados, com a capacidade de restaurar para qualquer ponto no tempo (como antes de alguém excluir dados críticos), permite a restauração para um estado o mais próximo possível à forma como ele estava antes da interrupção.

Dois tipos de backup devem ser executados para obter melhores resultados:

- Backups de banco de dados
- Backups do log de transações

Além de backups completos do banco de dados executados no nível de aplicativo, você pode ser ainda mais cuidadoso ao executar backups com instantâneos de armazenamento. A execução de backups de log também é importante para restaurar o banco de dados (e limpar os logs das transações já confirmadas).

O SAP HANA no Azure (grandes instâncias) oferece duas opções de backup e de restauração:

- DIY (Faça Você Mesmo). Depois de calcular para garantir que há espaço em disco suficiente, realize backups completos de banco de dados e de log usando os métodos de backup em disco (para os discos). Ao longo do tempo, os backups são copiados para a conta de armazenamento do Azure (depois de configurar um servidor de arquivos do Azure com o armazenamento praticamente ilimitado) ou usam o Cofre de Backup do Azure ou armazenamento do Azure frio. Outra opção é usar uma ferramenta de proteção de dados de terceiros, como Commvault, para armazenar os backups depois que eles são copiados para uma conta de armazenamento. A opção de backup DIY também pode ser necessária para dados que precisam ser armazenados por períodos mais longos devido à conformidade e para fins de auditoria.
- Use o backup e restaurar a funcionalidade que fornece a infraestrutura subjacente do SAP HANA no Azure (Instâncias Grandes). Essa opção atende a necessidade de backups e faz backups manuais praticamente obsoleta (exceto quando os backups de dados são necessários para fins de conformidade). O restante desta seção aborda a funcionalidade de backup e restauração oferecida com o HANA em Instâncias Grandes.

> [!NOTE]
> A tecnologia de instantâneo que é usada pela infraestrutura subjacente do HANA (instâncias grandes) tem uma dependência de instantâneos do SAP HANA. Instantâneos do SAP HANA não funcionam junto com os Contêineres de Banco de Dados Multilocatário do SAP HANA. Como resultado, esse método de backup não pode ser usado para implantar contêineres de banco de dados multilocatário do SAP HANA.

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Usando instantâneos de armazenamento do SAP HANA no Azure (Instâncias Grandes)

A infraestrutura de armazenamento subjacente SAP HANA no Azure (Instâncias Grandes) dá suporte a noção de um instantâneo de volumes de armazenamento. O backup e a restauração de um determinado volume têm suporte com as seguintes considerações:

- Em vez de backups de banco de dados, instantâneos de volume de armazenamento são executados com frequência.
- O instantâneo de armazenamento inicia um instantâneo do SAP HANA antes de executar o instantâneo de armazenamento. Esse instantâneo SAP HANA é o ponto de instalação para restaurações de log eventual após a recuperação do instantâneo de armazenamento.
- No ponto em que o instantâneo de armazenamento é executado com êxito, o instantâneo SAP HANA será excluído.
- Backups de log são usados frequentemente e armazenados no volume de backup de log ou no Azure.
- Se o banco de dados tiver de ser restaurado para um determinado ponto no tempo, será feita uma solicitação para o Suporte do Microsoft Azure (interrupção de produção) ou do SAP HANA no Gerenciamento de Serviços do Azure para restaurar para um determinado instantâneo de armazenamento (por exemplo, uma restauração planejada de um sistema de área restrita para seu estado original).
- O instantâneo do SAP HANA incluído no instantâneo de armazenamento é um ponto de deslocamento para a aplicação de backups de log que foram executados e armazenados após o armazenamento de instantâneo foi tirado.
- Esses backups de log são aplicados para restaurar o banco de dados para um determinado ponto no tempo.

A especificação \_do nome de backup criará um instantâneo dos volumes a seguir:

- hana/data
- Hana/log
- Hana/log\_backup (montado como backup no hana/log)
- hana/shared

### <a name="storage-snapshot-considerations"></a>Considerações sobre o instantâneo de armazenamento

>[!NOTE]
>Os instantâneos de armazenamento _não_ são fornecidos gratuitamente, já que espaço de armazenamento adicional deve ser alocado.

A mecânica específica de instantâneos de armazenamento para SAP HANA no Azure (Instâncias Grandes) inclui:

- Um instantâneo de armazenamento específico (no ponto no tempo em que é obtido) consome muito pouco de armazenamento.
- Como as alterações de conteúdo de dados e o conteúdo na alteração dos arquivos no volume de armazenamento de dados do SAP HANA, o instantâneo precisa armazenar o conteúdo do bloco original.
- O instantâneo de armazenamento aumenta de tamanho. Quanto mais tempo o instantâneo existe, quanto maior o instantâneo de armazenamento ficar.
- Mais alterações feitas no volume de banco de dados do SAP HANA durante o tempo de vida de um instantâneo do armazenamento, maior o consumo de espaço do instantâneo do armazenamento.

SAP HANA no Azure (instâncias grande) vem com tamanhos de volume fixo para o volume de dados e de log do SAP HANA. Execução de instantâneos de volumes será comer em seu espaço de volume, portanto, é sua responsabilidade para agendar instantâneos de armazenamento (no processo SAP HANA no Azure Instâncias Grandes).

As seções a seguir fornecem informações para executar esses instantâneos incluindo recomendações gerais:

- Embora o hardware possa sustentar 255 snapshots por volume, é altamente recomendável ficar bem abaixo desse limite.
- Antes de executar instantâneos de armazenamento, monitore e mantenha o controle do espaço livre.
- Reduza o número de instantâneos de armazenamento com base em espaço livre. Pode ser necessário reduzir o número de instantâneos que você mantém ou talvez você precise estender os volumes. (Você pode solicitar armazenamento adicional em unidades de 1 TB).
- Durante as atividades como a movimentação de dados em SAP HANA com ferramentas de migração do sistema (com R3load ou restaurando bancos de dados do SAP HANA de backups), é altamente recomendável não executar quaisquer instantâneos de armazenamento. (Se uma migração de sistema estiver sendo feita em um novo sistema SAP HANA, instantâneos de armazenamento não precisarão ser executados.)
- Durante a maior reorganizações de tabelas do SAP HANA, instantâneos de armazenamento devem ser evitados se possível.
- Os instantâneos de armazenamento são um pré-requisito para envolver os recursos de recuperação de desastre do SAP HANA no Azure (Instâncias Grandes).

### <a name="setting-up-storage-snapshots"></a>Configurar instantâneos de armazenamento

1. Verifique se o Perl está instalado no sistema operacional Linux no servidor HANA (grandes instâncias).
2. Modifique /etc/ssh/ssh\_config para adicionar alinha _MACs hmac-sha1_.
3. Crie uma conta de usuário de backup do SAP HANA no nó principal para cada instância do SAP HANA que estão em execução (se aplicável).
4. O cliente SAP HANA HDB deve ser instalado em todos os servidores HANA (grandes instâncias).
5. No primeiro servidor SAP HANA (grandes instâncias) de cada região, deve ser criada uma chave pública para acessar a infraestrutura básica de armazenamento que controla a criação do instantâneo.
6. Copie o script azure\_hana\_backup.pl de /scripts para o local do **hdbsql** da instalação do SAP HANA.
7. Copie o arquivo HANABackupDetails.txt de /scripts para o mesmo local do script do Perl.
8. Modifique o arquivo HANABackupDetails.txt conforme necessário para as especificações do cliente apropriado.

### <a name="step-1-install-sap-hana-hdbclient"></a>Etapa 1: Instalar o HDBClient do SAP HANA

O Linux instalado em SAP HANA no Azure (grandes instâncias) inclui as pastas e os scripts necessários para executar instantâneos de armazenamento SAP HANA para fins de backup e recuperação de desastre. No entanto, é sua responsabilidade instalar o SAP HANA HDBclient durante a instalação do SAP HANA. (A Microsoft não instala o HDBclient nem o SAP HANA.)

### <a name="step-2-change-etcsshsshconfig"></a>Etapa 2: Alterar/etc/ssh/ssh\_config

Altere /etc/ssh/ssh\_config adicionando a linha _MACs hmac-sha1_ conforme mostrado aqui:
```
#   RhostsRSAAuthentication no
#   RSAAuthentication yes
#   PasswordAuthentication yes
#   HostbasedAuthentication no
#   GSSAPIAuthentication no
#   GSSAPIDelegateCredentials no
#   GSSAPIKeyExchange no
#   GSSAPITrustDNS no
#   BatchMode no
#   CheckHostIP yes
#   AddressFamily any
#   ConnectTimeout 0
#   StrictHostKeyChecking ask
#   IdentityFile ~/.ssh/identity
#   IdentityFile ~/.ssh/id_rsa
#   IdentityFile ~/.ssh/id_dsa
#   Port 22
Protocol 2
#   Cipher 3des
#   Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-cbc
#   MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd160
MACs hmac-sha1
#   EscapeChar ~
#   Tunnel no
#   TunnelDevice any:any
#   PermitLocalCommand no
#   VisualHostKey no
#   ProxyCommand ssh -q -W %h:%p gateway.example.com
```

### <a name="step-3-create-a-public-key"></a>Etapa 3: Criar uma chave pública

No primeiro servidor SAP HANA no Azure (grandes instâncias) em cada região do Azure, crie uma chave pública para ser usada para acessar a infraestrutura de armazenamento, permitindo a criação de instantâneos. A chave pública garante que uma senha não será necessária para se conectar no armazenamento e que as credenciais de senha não são mantidas. No Linux no servidor SAP HANA (grandes instâncias), execute o seguinte comando para gerar a chave pública:
```
  ssh-keygen –t dsa –b 1024
```
O novo local é _/root/.ssh/id\_dsa.pub. Não insira uma senha real, caso contrário, você precisará inserir a senha todas as vezes que entrar. Em vez disso, pressione **Enter** duas vezes para remover o requisito de senha para entrar.

Verifique se a chave pública foi corrigida conforme o esperado alterando as pastas para /root/.ssh/ e executando o comando **ls**. Se a chave estiver presente, você poderá copiá-la executando o seguinte comando:

![Chave pública é copiada executando este comando](./media/sap-hana-overview-high-availability-disaster-recovery/image2-public-key.png)

Neste ponto, entre em contato com SAP HANA em gerenciamento de serviços do Azure e forneça a chave. O representante de serviço uma a chave pública para registrá-la na infraestrutura de armazenamento subjacente.

### <a name="step-4-create-an-sap-hana-user-account"></a>Etapa 4: Criar uma conta de usuário do SAP HANA

Crie uma conta de usuário do SAP HANA no Studio de SAP HANA para fins de backup. Essa conta deve ter os seguintes privilégios: _Administrador de Backup_ e _Leitura de Catálogo_. Neste exemplo, o nome de usuário SCADMIN é criado.

![Criando um usuário no Studio HANA](./media/sap-hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

### <a name="step-5-authorize-the-sap-hana-user-account"></a>Etapa 5: Autorizar a conta de usuário do SAP HANA

Autorize a conta de usuário SAP HANA (que será usada pelos scripts sem exigir autorização toda vez que o script for executado). O comando `hdbuserstore` do SAP HANA permite criar uma chave de usuário do SAP HANA, que é armazenada em um ou mais nós do SAP HANA. A chave de usuário também permite que o usuário acesse o SAP HANA sem a necessidade de gerenciar suas senhas no processo de script que será discutido posteriormente.

>[!IMPORTANT]
>Execute o comando a seguir como `_root_`. Caso contrário, o script não funcionará corretamente.

Insira o comando `hdbuserstore` da seguinte maneira:

![Digite comando hdbuserstore](./media/sap-hana-overview-high-availability-disaster-recovery/image4-hdbuserstore-command.png)

No exemplo a seguir, onde o usuário é SCADMIN01 e o nome do host é lhanad01, o comando é:
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
Gerencie todos os scripts de servidor único para instâncias de escala horizontal do HANA. Neste exemplo, a chave SCADMIN01 do SAP HANA deve ser alterada para cada host de forma a refletir o host que está relacionado à chave. Ou seja, a conta de backup de SAP HANA é redefinida com o número da instância do HANA DB, **lhanad**. A chave deve ter privilégios administrativos no host ao qual está atribuída e o usuário de backup para escala horizontal deve ter direitos de acesso a todas as instâncias do SAP HANA.
```
hdbuserstore set SCADMIN01 lhanad:30015 SCADMIN <password>
hdbuserstore set SCADMIN02 lhanad:30115 SCADMIN <password>
hdbuserstore set SCADMIN03 lhanad:30215 SCADMIN <password>
```

### <a name="step-6-copy-items-from-the-scripts-folder"></a>Etapa 6: Copiar itens da pasta /scripts

Copie os seguintes itens na pasta /scripts, incluída na imagem de ouro da instalação, para o diretório de trabalho **hdbsql**. Para instalações atuais do HANA, esse diretório é /hana/shared/D01/exe/linuxx86\_64/hdb.
```
azure\_hana\_backup.pl
testHANAConnection.pl
testStorageSnapshotConnection.pl
removeTestStorageSnapshot.pl
HANABackupCustomerDetails.txt
```
Copie os seguintes itens se eles estiverem executando escala horizontal ou OLAP:
```
azure\_hana\_backup\_bw.pl
testHANAConnectionBW.pl
testStorageSnapshotConnectionBW.pl
removeTestStorageSnapshotBW.pl
HANABackupCustomerDetailsBW.txt
```
O arquivo HANABackupCustomerDetails.txt pode ser modificado conforme segue, para uma implantação de expansão. Esse é o arquivo de configuração e controle para o script que executará os instantâneos de armazenamento. Você deve ter recebido a _Nome do Backup de Armazenamento_ e _Endereço IP do Armazenamento_ do SAP HANA em gerenciamento de serviços do Azure quando suas instâncias foram implantadas. Não é possível modificar a sequência, ordenação ou o espaçamento de qualquer uma das variáveis ou o script não será executado corretamente.

Para uma implantação de expansão, o arquivo de configuração deve parecer com:
```
#Provided by Microsoft Service Management
Storage Backup Name: lhanad01backup
Storage IP Address: 10.250.20.21
#Created by customer using hdbuserstore
HANA Backup Name: SCADMIND01
```
Para uma configuração de expansão, o arquivo HANABackupCustomerDetailsBW.txt seria:
```
#Provided by Microsoft Service Management
Storage Backup Name: lhanad01backup
Storage IP Address: 10.250.20.21
#Node IP addresses, instance numbers, and HANA backup name
#provided by customer.  HANA backup name created using
#hdbuserstore utility.
Node 1 IP Address: 10.254.15.21
Node 1 HANA instance number: 01
Node 1 HANA Backup Name: SCADMIN01
Node 2 IP Address: 10.254.15.22
Node 2 HANA instance number: 02
Node 2 HANA Backup Name: SCADMIN02
Node 3 IP Address: 10.254.15.23
Node 3 HANA instance number: 03
Node 3 HANA Backup Name: SCADMIN03
Node 4 IP Address: 10.254.15.24
Node 4 HANA instance number: 04
Node 4 HANA Backup Name: SCADMIN04
Node 5 IP Address: 10.254.15.25
Node 5 HANA instance number: 05
Node 5 HANA Backup Name: SCADMIN05
Node 6 IP Address: 10.254.15.26
Node 6 HANA instance number: 06
Node 6 HANA Backup Name: SCADMIN06
Node 7 IP Address: 10.254.15.27
Node 7 HANA instance number: 07
Node 7 HANA Backup Name: SCADMIN07
Node 8 IP Address: 10.254.15.28
Node 8 HANA instance number: 08
Node 8 HANA Backup Name: SCADMIN08
```
>[!NOTE]
>Atualmente, apenas os detalhes do Nó 1 são usados no script de instantâneo do armazenamento HANA real. É recomendável que você teste o acesso de ou para todos os nós do HANA para que, se o nó de backup mestre for alterado, já esteja garantido que qualquer outro nó poderá assumir seu lugar modificando os detalhes no Nó 1.

Para verificar as configurações corretas no arquivo de configuração ou a conectividade apropriada para as instâncias do HANA, execute um dos seguintes scripts:
- Para uma configuração de expansão (independente na carga de trabalho do SAP):

 ```
testHANAConnection.pl
```
- Para configurações de escala horizontal:

 ```
testHANAConnectionBW.pl
```

Verifique se a instância mestre do HANA tem acesso a todos os itens necessários servidores HANA. Não existem parâmetros para o script, porém é necessário concluir o arquivo HANABackupCustomerDetails/ HANABackupCustomerDetailsBW apropriado para que o script seja executado corretamente. Como somente os códigos de erro de comando shell são retornados, não é possível para o script verificar os erros de cada instância. Ainda assim, o script ainda oferece alguns comentários úteis para você verificar novamente.

Para executar o script:
```
 ./testHANAConnection.pl
```
 O script exibirá a mensagem de que a conexão do HANA foi bem-sucedida se for bem-sucedido ao obter o status da instância do HANA.

Além disso, há um segundo tipo de script que pode ser usado para verificar a capacidade do servidor de instância mestre do HANA de se conectar no armazenamento. Antes de executar o script azure\_hana\_backup(\_bw).pl, você deve executar o próximo script. Se um volume não contiver nenhum instantâneo, será impossível determinar se o volume está simplesmente vazio ou se há um falha de ssh ao obter os detalhes do instantâneo. Por esse motivo, script é executado em duas etapas:

- Ele verifica se o console de armazenamento está acessível.
- Ele cria um instantâneo de teste ou fictício para cada volume por instância do HANA.

Por esse motivo, a instância do HANA é incluída como um argumento. Novamente, não é possível fornecer a verificação de erros para a conexão de armazenamento, mas o script fornece algumas dicas úteis se a execução falhará.

O script é executado como:
```
 ./testStorageSnapshotConnection.pl <hana instance>
```
Ou é executado como:
```
./testStorageSnapshotConnectionBW.pl <hana instance>
```
O script também exibe uma mensagem indicando que você pode conectar corretamente no seu locatário de armazenamento implantado, que é organizado LUNs (números de unidade lógica) usados pelas instâncias do servidor que você possui.

Antes de executar os backups de baseados em instantâneo de armazenamento primeiro, execute esses scripts para verificar se a configuração está correta.

Depois de executar esses scripts, você pode excluir os instantâneos executando:
```
./removeTestStorageSnapshot.pl <hana instance>
```
ou o
```
./removeTestStorageSnapshot.pl <hana instance>
```

### <a name="step-7-perform-on-demand-snapshots"></a>Etapa 7: Executar instantâneos sob demanda

Execute instantâneos sob demanda (bem como agendar instantâneos regulares usando cron) conforme descrito aqui.

Para configurações de expansão, execute o seguinte script:
```
./azure_hana_backup.pl lhanad01 customer 20
```
Para configurações de escala horizontal, execute o seguinte script:
```
./azure_hana_backup_bw.pl lhanad01 customer 20
```
O script de escala horizontal realiza verificações adicionais para garantir que todos os servidores HANA sejam acessados e todas as instâncias HANA retornem o status apropriado da instância antes de prosseguir com a criação de instantâneos de armazenamento ou SAP HANA.

Os argumentos a seguir são necessários:

- O HANA instância que necessitam de backup.
- O prefixo de instantâneo para o instantâneo do armazenamento.
- O número de instantâneos a serem mantidos para o prefixo específico.

```
./azure_hana_backup.pl lhanad01 customer 20
```

A execução do script cria o instantâneo de armazenamento nessas três fases distintas:

- Execute um instantâneo do HANA.
- Execute um instantâneo do armazenamento.
- Remova o instantâneo HANA.

Execute o script chamando-o na pasta executável HDB para a qual ele foi copiado. Ele fará backup de pelo menos os seguintes volumes, mas também fará backup de qualquer volume que tem o nome de instância explícito do SAP HANA no nome do volume.
```
hana_data_<hana instance>_prod_t020_vol
hana_log_<hana instance>_prod_t020_vol
hana_log_backup_<hana instance>_prod_t020_vol
hana_shared_<hana instance>_prod_t020_vol
```
O período de retenção é estritamente administrado, com o número de instantâneos enviado como um parâmetro ao executar o script (tal como 20, mostrado acima). Portanto a quantidade de tempo é uma função de período de execução e o número de instantâneos na chamada do script. Se o número de instantâneos que é mantido exceder o número identificado como parâmetro na chamada do script, o instantâneo de armazenamento mais antigo deste rótulo (no caso anterior, _personalizado_) será excluído antes de executar um novo instantâneo. Isso significa que o número você conceder como o último parâmetro da chamada é o número que você pode usar para controlar o número de instantâneos.

>[!NOTE]
>Como alterar o rótulo, a contagem começa novamente.

Você precisa incluir o nome da instância HANA fornecido pelo SAP HANA em gerenciamento de serviços do Azure como um argumento se eles são ambientes de vários nós de instantâneos. Em ambientes de nó único, o nome do SAP HANA na unidade do Azure (instância grande) é suficiente, mas o nome da instância HANA é recomendado.

Além disso, você pode fazer backup volumes\LUNs de inicialização usando o mesmo script. Você deve fazer backup do volume de inicialização pelo menos uma vez ao executar o HANA pela primeira vez, embora seja recomendável o agendamento de backup semanal ou todas as noite para inicialização em cron. Em vez de adicionar um nome de instância do SAP HANA, insira _inicialização_ como o argumento para o script da seguinte maneira:
```
./azure_hana_backup boot customer 20
```
A mesma política de retenção é que o volume de inicialização. Use os instantâneos sob demanda, conforme descrito acima, somente para casos especiais, tais como durante uma atualização do SAP EHP ou em horários em que você precisa criar um instantâneo de armazenamento distinto.

Recomendamos que você realize os instantâneos de armazenamento usando cron, além de usar o mesmo script para todas as necessidades de backup e recuperação de desastres (modificando as entradas do script para corresponder aos diferentes tempos de backup solicitados). Esses são todos agendadas de forma diferente no cron dependendo de seu tempo de execução: por hora, 12 horas, diária ou semanal. A agenda de cron é projetada para criar instantâneos de armazenamento que correspondem à rotulagem de retenção discutida anteriormente para backup externo de longo prazo. O script inclui comandos para fazer o backup de todos os volumes de produção, dependendo da frequência solicitada (arquivos de log e dados de backup por hora, enquanto o volume de inicialização é realizado diariamente).

As entradas no script a seguir cron executado a cada hora no décimo minuto, cada 12 horas no décimo minuto e diariamente no décimo minuto. Os trabalhos cron são criados de forma que apenas um instantâneo do armazenamento SAP HANA ocorra em uma determinada qualquer hora, para que um backup horário e diário não ocorra ao mesmo tempo (12:10 AM). Para ajudar a otimizar a criação e replicação de instantâneos, o SAP HANA no Gerenciamento de Serviços do Azure fornecerá o momento indicado para executar os backups.

O cron padrão /etc/crontab de agendamento é o seguinte:
```
10 1-11,13-23 * * * ./azure_hana_backup.pl lhanad01 hourly 66
10 12 * * *  ./azure_hana_backup.pl lhanad01 12hour 14
```
Nas instruções de cron anteriores, os volumes do HANA (sem o volume de inicialização), obtém um instantâneo por hora com o rótulo. Esses instantâneos, 66 são mantidos. Além disso, são mantidos 14 instantâneos com o rótulo de 12 horas. Você possivelmente obterá instantâneos a cada hora por três dias, além de instantâneos de 12 horas por outro quatro dias, o que lhe dá uma semana inteira de instantâneos.

Agendamento em cron pode ser complicado, pois somente um script deve ser executado em um determinado momento, a menos que o script é alternado por vários minutos. Se você quiser backups diários para retenção de longo prazo, um instantâneo diário será mantido junto com um de 12 horas (com contagem uma retenção de sete cada) ou o instantâneo será irregular e ocorrerá 10 minutos depois. Apenas um instantâneo diário é mantido no volume de produção.
```
10 1-11,13-23 * * * ./azure_hana_backup.pl lhanad01 hourly 66
10 12 * * *  ./azure_hana_backup.pl lhanad01 12hour 7
10 0 * * * ./azure_hana_backup.pl lhanad01 daily 7
```
As frequências listadas aqui são apenas exemplos. Você precisa obter o número ideal de instantâneos com base nos seguintes critérios:

- Requisitos de objetivo de tempo de recuperação para recuperação pontual.
- Uso de espaço.
- Requisitos do objetivo de ponto de recuperação e objetivo de tempo de recuperação de desastre em potencial.
- Execução eventual de backups completos de banco de dados do HANA em discos. Sempre que um backup completo do banco de dados em discos ou da interface _backint_, for realizado, a execução de instantâneos de armazenamento falhará. Se você planeja executar backups de banco de dados completos sobre instantâneos de armazenamento, verifique se a execução de instantâneos de armazenamento está desabilitada durante esse momento.

>[!IMPORTANT]
> O uso de instantâneos de armazenamento para backups do SAP HANA só é válido quando os instantâneos são executados junto com backups de log do SAP HANA. Esses backups de log precisam ser capaz de abordar os períodos de tempo entre os instantâneos de armazenamento. Se você firmou um compromisso com os usuários de uma recuperação pontual de 30 dias, será necessário o seguinte:

- Capacidade de acessar um instantâneo de armazenamento que é de 30 dias.
- Backups de log contíguo nos últimos 30 dias.

No intervalo de backups de log, crie também um instantâneo do volume do log de backup. No entanto, lembre-se de executar backups de log regulares para que você possa:

- Tenha os backups de log contíguo necessários para realizar recuperações point-in-time.
- Impeça o volume do log do SAP HANA de ficar sem espaço.

Uma das últimas etapas é agendar logs de backup SAP HANA no Studio de SAP HANA. O destino de backup de log do SAP HANA é o hana/log criado especialmente\_volume backups com o ponto de montagem de /hana/log/backups.

![Agendar logs de backup SAP HANA no Studio de SAP HANA](./media/sap-hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Você pode escolher os backups com frequência maior que 15 minutos. Alguns usuários até mesmo executam backups de log a cada minuto, entretanto não é recomendado _ficar acima_ de 15 minutos.

A última etapa é criar um backup baseado em arquivo (após a instalação inicial do SAP HANA) para criar uma entrada de backup única que deve existir no catálogo de backup. Caso contrário, SAP HANA não pode iniciar os backups de log indicados.

![Faça um backup baseado em arquivo para criar uma única entrada de backup](./media/sap-hana-overview-high-availability-disaster-recovery/image6-make-backup.png)

### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Monitoramento do número e tamanho dos instantâneos no volume de disco

Em um volume de armazenamento específico, você pode monitorar o número de instantâneos e o consumo de armazenamento de instantâneos. O comando `ls` não mostra o diretório de instantâneo ou arquivos. No entanto, o comando `du` do sistema operacional Linux realiza, com os seguintes comandos:

- `du –sh .snapshot` apresenta o total de todos os instantâneos dentro do diretório de instantâneo.
- `du –sh --max-depth=1` lista todos os instantâneos que são salvos na pasta .snapshot e o tamanho de cada um deles.
- `du –hc` fornece o tamanho total usado por todos os instantâneos.

Use estes comandos para verificar se os instantâneos foram obtidos e armazenado não estão consumindo todo o armazenamento nos volumes.

### <a name="reducing-the-number-of-snapshots-on-a-server"></a>Reduzindo o número de instantâneos em um servidor

Conforme explicado anteriormente, você pode reduzir o número armazenado de certos rótulos de instantâneos. Os dois últimos parâmetros do comando para iniciar um instantâneo são um rótulo e o número de instantâneos que você deseja manter.
```
./azure_hana_backup.pl lhanad01 customer 20
```
No exemplo anterior, o rótulo de instantâneo é _cliente_ e o número de instantâneos com este rótulo a ser retido é _20_. Ao responder ao consumo de espaço em disco, pode ser útil reduzir o número de instantâneos armazenados. É a maneira fácil de reduzir o número de instantâneos é executar o script com o último parâmetro definido como 5:
```
./azure_hana_backup.pl lhanad01 customer 5
```
Como resultado da execução do script com essa configuração, o número de instantâneos, incluindo o novo instantâneo de armazenamento é _5_.

 >[!NOTE]
 > Esse script reduz o número de instantâneos somente se o mais recente tiver mais de uma hora. O script não excluir instantâneos que com menos de uma hora de idade.

Essas restrições estão relacionadas à funcionalidade de recuperação de desastre opcional oferecida.

Se você não deseja manter um conjunto de instantâneos com o prefixo, você pode executar o script com _0_ como o número de retenção para remover todos os instantâneos de correspondência de prefixo. Entretanto, remover todos os instantâneos pode afetar os recursos de recuperação de desastre.

### <a name="recovering-to-the-most-recent-hana-snapshot"></a>Recuperando para o instantâneo de HANA mais recente

No caso de ocorrer um cenário de inatividade na produção, o processo de recuperação de um instantâneo de armazenamento pode ser iniciado como um incidente do cliente com o SAP HANA no Gerenciamento de Serviços do Azure. Um cenário inesperado poderá ser caso de alta urgência se os dados foram excluídos em um sistema de produção e é a única maneira de recuperá-los é restaurar o banco de dados de produção.

Por outro lado, uma recuperação pontual pode ter baixa urgência e ser planejada em com dias de antecedência. Você pode planejar essa recuperação com o SAP HANA no Gerenciamento de Serviços do Azure em vez de gerar um problema de alta prioridade. Por exemplo, você pode tentar realizar uma atualização do software SAP aplicando um novo pacote de melhorias e, em seguida, precisa reverter para um instantâneo que representa o estado antes da atualização de EHP.

Antes de emitir a solicitação, você precisa fazer algumas preparações. O SAP HANA na equipe de Gerenciamento de Serviços do Azure poderá manipular a solicitação e fornecer os volumes restaurados. Depois, caberá a você restaurar o banco de dados do HANA com base em instantâneos. Veja aqui como se preparar para a solicitação:

>[!NOTE]
>A interface do usuário pode variar de capturas de tela seguintes, dependendo da versão do SAP HANA usada.

1. Decida qual instantâneo para restaurar. Somente o volume de dados/hana será restaurado, salvo instrução em contrário.

2. Desligar a instância do HANA.

 ![Desligar a instância do HANA](./media/sap-hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

3. Desmonte os volumes de dados em cada nó do banco de dados do HANA. A restauração do instantâneos falhará se os volumes de dados não forem desmontados.

 ![Desmonte os volumes de dados em cada nó do banco de dados HANA](./media/sap-hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

4. Abra uma solicitação de suporte do Azure para instruir a restauração de um instantâneo específico.

 - Durante a restauração: o SAP HANA no Gerenciamento de Serviços do Azure pode solicitar que você participe de uma chamada em conferência para garantir que nenhum dado se perca.

 - Depois da restauração: o SAP HANA no Gerenciamento de Serviços do Azure será notificado quando o instantâneo de armazenamento tiver sido restaurado.

5. Depois que o processo de restauração for concluído, monte novamente todos os volumes de dados.

 ![Montar novamente todos os volumes de dados](./media/sap-hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

6. Selecione opções de recuperação no SAP HANA Studio, caso elas não apareçam automaticamente ao reconectar ao HANA DB por meio do SAP HANA Studio. O exemplo a seguir mostra uma restauração para o último instantâneo HANA. Um instantâneo do armazenamento incorpora um instantâneo do HANA e, se você estiver restaurando para o instantâneo de armazenamento mais recente, ele deverá ser o instantâneo mais recente do HANA. (Se você está restaurando os instantâneos de armazenamento mais antigos, você precisa localizar o instantâneo do HANA baseado na hora em que o armazenamento de instantâneo foi tirado).

 ![Selecionar as opções de recuperação no SAP HANA Studio](./media/sap-hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

7. Selecione **Recuperar o banco de dados para um instantâneo de backup ou armazenamento de dados específico**.

 ![A janela “Especificar o tipo de recuperação”](./media/sap-hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

8. Selecione **Especificar backup sem catálogo**.

 ![A janela “Especificar o local de backup”](./media/sap-hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

9. Na lista **Tipo de Destino**, selecione **Instantâneo**.

 ![A janela “Especificar o Backup a ser Recuperado”](./media/sap-hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

10. Clique em **Concluir** para iniciar o processo de recuperação.

 ![Clique em “Concluir” para iniciar o processo de recuperação](./media/sap-hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

11. O banco de dados do HANA é restaurado e recuperado para o instantâneo HANA incluído pelo instantâneo de armazenamento.

 ![O banco de dados do HANA é restaurado e recuperado para o instantâneo do HANA](./media/sap-hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recovering-to-the-most-recent-state"></a>Recuperando para o estado mais recente

O processo a seguir restaura um instantâneo HANA que foi incluído no instantâneo de armazenamento. Ele restaura os backups de log de transação para o estado mais recente do banco de dados antes de restaurar o instantâneo de armazenamento.

>[!IMPORTANT]
>Antes de continuar, verifique se você tem uma cadeia completa e contígua de backups de log de transações. Sem esses backups, você não poderá restaurar o estado atual do banco de dados.

1. Conclua as etapas 1 a 6 do procedimento anterior em “Recuperando o instantâneo mais recente do HANA”.

2. Selecione **Recuperar o banco de dados para seu estado mais recente**.

 ![Selecione “Recuperar o banco de dados para seu estado mais recente”](./media/sap-hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

3. Especifique o local dos backups de log mais recentes do HANA. O local especificado deve conter todos os backups de log de transação do instantâneo do HANA para o estado mais recente.

 ![Especifique o local dos backups de log mais recentes do HANA](./media/sap-hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

4. Selecione um backup como uma base recuperar o banco de dados. Em nosso exemplo, este é o instantâneo do HANA que foi incluído no instantâneo de armazenamento. (Apenas um instantâneo está listado na seguinte captura de tela.)

 ![Selecione um backup como uma base recuperar o banco de dados](./media/sap-hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

5. Desmarque a caixa de seleção **Usar Backups Delta** se não existirem deltas entre a hora do instantâneo do HANA e o estado mais recente.

 ![Desmarque a caixa de seleção “Usar Backups de Delta” se nenhum delta existir](./media/sap-hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

6. Na tela de resumo, clique em **Concluir** para iniciar o procedimento de restauração.

 ![Clique em “Concluir” na tela de resumo](./media/sap-hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recovering-to-another-point-in-time"></a>Recuperar para outro ponto no tempo
Para recuperar para um ponto de tempo entre o instantâneo HANA (incluído no instantâneo de armazenamento) e um posterior àquele da recuperação pontual, faça o seguinte:

1. Certifique-se de que você tenha todas as dos backups do log de transações do instantâneo do HANA até o momento em que você deseja recuperar.
2. Inicie o procedimento em “Recuperando para o estado mais recente”.
3. Na etapa 2 do procedimento, na janela **Especificar o Tipo de Recuperação**, selecione **Recuperar o banco de dados para o seguinte ponto**, especifique o ponto em questão e conclua as etapas 3 a 6.

## <a name="monitoring-the-execution-of-snapshots"></a>Monitorando a execução de instantâneos

Você precisa monitorar a execução de instantâneos de armazenamento. O script que executa um instantâneo de armazenamento grava a saída em um arquivo e salva no mesmo local que os scripts Perl. Um arquivo separado é criado para cada instantâneo. A saída de cada arquivo mostra claramente as várias fases de execução do script de instantâneo:

- Localizando os volumes necessários para criar um instantâneo
- Localizando os instantâneos tirados desses volumes
- Excluindo instantâneos existentes eventuais para corresponder ao número de instantâneos que você especificou
- Criando um instantâneo do HANA
- Criação do instantâneo de armazenamento sobre os volumes
- Excluindo o instantâneo de HANA
- Renomeando o instantâneo mais recente para **.0**

A parte mais importante do script é:
```
**********************Creating HANA snapshot**********************
Creating the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data create snapshot"" ...
HANA snapshot created successfully.
**********************Creating Storage snapshot**********************
Taking snapshot hourly.recent for hana_data_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_backup_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_shared_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for sapmnt_lhanad01_t020_vol ...
Snapshot created successfully.
**********************Deleting HANA snapshot**********************
Deleting the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data drop snapshot"" ...
HANA snapshot deletion successfully.
```
Você pode ver a este exemplo como o script registros a criação do instantâneo HANA. No caso de escala horizontal, esse processo é iniciado no nó principal. O nó principal iniciará a criação síncrona de instantâneos em cada um de nós de trabalho. Em seguida, o instantâneo de armazenamento é criado. Após a execução bem-sucedida de instantâneos de armazenamento, o instantâneo do HANA será excluído.



<!--HONumber=Jan17_HO3-->


