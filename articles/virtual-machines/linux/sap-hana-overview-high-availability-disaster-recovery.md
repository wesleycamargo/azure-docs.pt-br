---
title: "Alta Disponibilidade e Recuperação de Desastre do SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs"
description: "Estabeleça a Alta Disponibilidade e planeje a Recuperação de Desastre do SAP HANA no Azure (Instâncias Grandes)."
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
ms.sourcegitcommit: 047eeddecc33b637a4f0dec3aa591c42ada83706
ms.openlocfilehash: 9e988659dac046067c53765fc1ddc90ee152e27f


---
# <a name="high-availability-and-disaster-recovery-of-sap-hana-on-azure-large-instances"></a>Alta Disponibilidade e Recuperação de Desastre do SAP HANA no Azure (Instâncias Grandes)

A HA (Alta Disponibilidade) e a DR (Recuperação de Desastre) são aspectos importantes da execução do SAP HANA de missão crítica em servidores do Azure (Instâncias Grandes). É importante trabalhar com a SAP, o integrador de sistema, e/ou a Microsoft, a fim de arquitetar e implementar adequadamente a estratégia de HA/DR certa para você. Deve-se levar em conta considerações importantes, como RPO (Objetivo de Ponto de Recuperação) e RTO (Objetivo de Tempo de Recuperação), específicas do seu ambiente.

## <a name="high-availability"></a>Alta disponibilidade

A Microsoft dá suporte aos métodos de Alta Disponibilidade do SAP HANA &quot;prontos&quot;, que incluem:

- **Replicação de Armazenamento:** capacidade de o próprio sistema de armazenamento replicar todos os dados para outra localização (no mesmo data center ou em outro, separado). O SAP HANA funciona independentemente desse método.
- **Replicação do Sistema:** replicação de todos os dados no SAP HANA em um sistema separado do SAP HANA. A RTO é minimizada por meio de replicação de dados em intervalos regulares. O SAP HANA dá suporte os modos assíncronos, síncronos na memória e síncronos (recomendados para sistemas SAP HANA somente dentro do mesmo data center ou a menos de 100 km de distância).
- **Failover Automático do Host:** um ou mais nós do SAP HANA em espera são configurados no modo de &quot;escala horizontal&quot; e o SAP HANA faz automaticamente o &quot;failover&quot; para outro nó quando o nó mestre se torna indisponível.

Para saber mais sobre a Alta Disponibilidade do SAP HANA, veja as seguintes informações do SAP:

- [Whitepaper de Alta Disponibilidade do SAP HANA](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [Guia de administração do SAP HANA](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [Vídeo do SAP Academy sobre Replicação do Sistema SAP HANA](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP Note de Suporte nº 1999880 – perguntas frequentes sobre replicação do sistema SAP HANA](https://bcs.wdf.sap.corp/sap/support/notes/1999880)
- [SAP Note de Suporte SAP nº 2165547 – backup e restauração do SAP HANA no ambiente de replicação do sistema SAP HANA](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP Note de Suporte SAP nº 1984882 – uso da replicação do sistema SAP HANA para troca de hardware com tempo de inatividade mínimo/zero](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="disaster-recovery"></a>Recuperação de desastre

O SAP HANA no Azure (Instâncias Grandes) é oferecido em duas regiões do Azure em uma região geopolítica. Entre os dois carimbos de Instância Grande de duas regiões diferentes, há uma conectividade de rede direta para replicar dados durante a Recuperação de Desastre. A replicação dos dados se baseia na infraestrutura de armazenamento. A replicação dos dados não é feita por padrão — isso é feito para configurações de cliente que solicitaram a DR.

No entanto, para aproveitar a DR, você precisa começar a projetar a conectividade de rede para as duas regiões diferentes do Azure de forma adequada. Isso significa que você precisaria de um circuito do ExpressRoute conectando de locais em sua principal região do Azure, e outro circuito conectando de locais em sua região de DR. Essa medida abrange uma situação em que uma região do Azure completa, incluindo a localização do MSEE, tem um problema.

Como uma segunda medida, idealmente você precisaria se conectar a todas as Redes Virtuais do Azure que se conectam ao SAP HANA no Azure (Instâncias Grandes) em uma das regiões para ambos os circuitos do ExpressRoute. Isso deve resolver o caso de apenas um dos locais MSEE que se conecta a seu local com o Azure fora de serviço.

A figura a seguir mostra a configuração ideal para a Recuperação de Desastre:

![Configuração ideal para a Recuperação de Desastre](./media/sap-hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)

O caso ideal para uma configuração de DR da rede é ter dois circuitos do ExpressRoute do local para as duas regiões do Azure diferentes. Um circuito vai para a região nº 1, executando uma instância de produção. O segundo circuito do ExpressRoute vai para a região nº 2, executando algumas instâncias do HANA de não produção (isso é importante no caso de toda a região do Azure, incluindo carimbo do MSEE e de Instância Grande, ficar fora da rede).

Como uma segunda medida, todas as redes virtuais diferentes estão conectadas a diferentes circuitos do ExpressRoute que se conectam ao SAP HANA no Azure (Instâncias Grandes). Você pode ignorar o local onde um MSEE está falhando, ou você pode diminuir o RPO para Recuperação de Desastre, como você verá posteriormente.

Os próximos requisitos para uma configuração de DR são:

- Você precisa de SKUs do SAP HANA no Azure (Instâncias Grandes) do mesmo tamanho das suas SKUs de produção ordenadas e implantadas na região de DR. Essas instâncias podem ser usadas para executar suas instâncias de teste, de área restrita e de QA do HANA.
- Além disso, você precisa solicitar um perfil de DR para cada uma das SKUs do SAP HANA no Azure (Instâncias Grandes) que você deseja recuperar no site de DR, se necessário. Isso leva à alocação de volumes de armazenamento, que são o destino de replicação de armazenamento de sua região de produção para a região de DR.

Após todas as configurações, você deverá começar a replicação de armazenamento. Na infraestrutura de armazenamento usada para o SAP HANA no Azure (Instâncias Grandes), a base de replicação de armazenamento é composta por instantâneos de armazenamento. Para iniciar a replicação de DR, você precisa executar:

- Um instantâneo do LUN de inicialização conforme descrito anteriormente.
- Um instantâneo dos volumes relacionados ao HANA conforme descrito anteriormente.

Após a execução desses instantâneos, a réplica inicial de volumes é propagada nos volumes associados a seu perfil de DR na região de DR.

Subsequentemente, a cada hora o instantâneo mais recente de armazenamento é utilizado para replicar o delta desenvolvido nos volumes de armazenamento nesse meio tempo.

O RPO obtido com essa configuração é de 60 a 90 minutos. Para alcançar um RPO melhor no caso de DR, você precisa copiar os backups de log de transações do HANA do SAP HANA no Azure (Instâncias Grandes) e em outra região do Azure. Para fazer isso, você pode:

- Fazer backup do Log de transação do HANA tão frequentemente quanto possível em /hana/log/backup.
- Em seguida, copie os backups de log de transações quando eles terminarem em uma VM do Azure, que está em uma rede virtual conectada ao servidor do SAP HANA no Azure (Instâncias Grandes).
- Dessa VM, copie o backup para uma VM que esteja em uma rede virtual na região de DR.
- Mantenha os backups do log de transações nessa região da VM.

Em caso de desastre, depois que o perfil de DR tiver sido implantado em um servidor real, copie os backups de log de transações da VM para o SAP HANA no Azure (Instâncias Grandes), que agora é o principal na região de DR, e restaure esses backups. Isso é possível porque o estado do HANA nos discos de DR é igual ao do instantâneo do HANA. Este é o ponto de deslocamento para outras restaurações de backups de log de transações.

## <a name="backup-and-restore"></a>Backup e restauração

Um dos aspectos mais importantes para bancos de dados operacionais é verificar se o banco de dados pode ser protegido de vários tipos diferentes de eventos catastróficos. Esses eventos podem ser causados por qualquer coisa, desde desastres naturais até erros de usuário simples.

O backup de um banco de dados, com a capacidade de restaurar para qualquer ponto no tempo (como antes de alguém excluir dados críticos), permite a restauração para um estado o mais próximo possível à forma como ele estava antes da interrupção.

Dois tipos de backup devem ser executados para obter melhores resultados:

- Backups de banco de dados
- Backups do Log de Transações

Além de backups completos do banco de dados executados no nível de aplicativo, você pode ser ainda mais cuidadoso ao executar backups com instantâneos de armazenamento. A execução de backups de log também é importante para restaurar o banco de dados (e limpar os logs das transações já confirmadas).

Há duas opções de backup e de restauração para o SAP HANA no Azure (Instâncias Grandes):

- DIY (Faça Você Mesmo). Depois de calcular e garantir espaço em disco suficiente, realize backups completos de banco de dados e de log usando os métodos de backup em disco (para os discos). Ao longo do tempo, os backups são copiados para o armazenamento do Azure (depois de configurar um servidor de arquivos com base no Azure com o armazenamento padrão praticamente ilimitado), ou usam o Cofre de Backup do Azure ou armazenamento do Azure frio. Outra opção seria usar uma ferramenta de proteção de dados de terceiros, como Commvault, para armazenar os backups depois que eles são copiados para o armazenamento do Azure padrão. A opção de backup DIY também pode ser necessária para dados que precisam ser armazenados por períodos mais longos devido à conformidade e para fins de auditoria.
- Use o backup e restaurar a funcionalidade que fornece a infraestrutura subjacente do SAP HANA no Azure (Instâncias Grandes). Essa opção atende a necessidade de backups e faz backups manuais praticamente obsoleta (exceto nos casos em que os backups de dados são necessários para fins de conformidade). O restante desta seção aborda a funcionalidade de backup e restauração oferecida com o HANA em Instâncias Grandes.

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Usando instantâneos de armazenamento do SAP HANA no Azure (Instâncias Grandes)

A infraestrutura de armazenamento subjacente SAP HANA no Azure (Instâncias Grandes) oferece suporte a noção de um instantâneo de volumes de armazenamento. O backup e a restauração de um determinado volume têm suporte com as seguintes considerações:

- Em vez de backups de banco de dados, instantâneos de volume de armazenamento são executados com frequência.
- O instantâneo de armazenamento inicia um instantâneo do SAP HANA antes de executar o instantâneo de armazenamento. Esse instantâneo HANA será o ponto para configurar o log eventual restaurações após a recuperação do instantâneo de armazenamento.
- No ponto em que o instantâneo de armazenamento é executado com êxito, o instantâneo SAP HANA será excluído.
- Backups de log são usados frequentemente e armazenados no volume de backup de log ou no Azure.
- Se o banco de dados tiver de ser restaurado para um determinado ponto no tempo, será feita uma solicitação para o Suporte do Microsoft Azure (interrupção de produção) ou do SAP HANA no Gerenciamento de Serviços do Azure para restaurar para um determinado instantâneo de armazenamento (por exemplo, uma restauração planejada de um sistema de área restrita para seu estado original).
- O instantâneo do SAP HANA incluído no instantâneo de armazenamento é um ponto de deslocamento para a aplicação de backups de log que foram executados e armazenados após o armazenamento de instantâneo foi tirado.
- Esses backups de log são aplicados para restaurar o banco de dados para um determinado ponto no tempo.

A especificação do nome\_do backup criará um instantâneo dos volumes a seguir:

- hana/data
- Hana/log
- Hana/log\_backup (montado como backup no hana/log)
- hana/shared

### <a name="storage-snapshot-considerations"></a>Considerações sobre o instantâneo de armazenamento

>[!NOTE] 
>Os instantâneos de armazenamento _não_ são fornecidos gratuitamente, já que espaço de armazenamento adicional deve ser alocado.

Mecânica específica de instantâneos de armazenamento para SAP HANA no Azure (Instâncias Grandes):

- Um instantâneo de armazenamento específico (no ponto no tempo que é obtido) consome muito pouco de armazenamento.
- Como as alterações de conteúdo de dados e o conteúdo na alteração dos arquivos no volume de armazenamento de dados do SAP HANA, o instantâneo precisa armazenar o conteúdo do bloco original.
- O instantâneo de armazenamento aumenta de tamanho. Quanto mais tempo o instantâneo existe, quanto maior o instantâneo de armazenamento ficar.
- Mais alterações feitas no volume de banco de dados do SAP HANA durante a vida útil de um instantâneo do armazenamento, maior o consumo de espaço do instantâneo do armazenamento.

SAP HANA no Azure (instâncias grande) vem com tamanhos de volume fixo para o volume de dados e de log do SAP HANA. Execução de instantâneos de volumes será comer em seu espaço de volume, portanto, é sua responsabilidade para agendar instantâneos de armazenamento (no processo SAP HANA no Azure Instâncias Grandes).

As seções a seguir fornecem informações para executar esses instantâneos incluindo recomendações gerais:

- Embora o hardware pode sustentar 255 snapshots por volume, é altamente recomendável fique bem abaixo desse limite.
- Espaço livre deve ser monitorado e conhecido antes de executar instantâneos de armazenamento.
- Reduza o número de instantâneos de armazenamento com base em espaço livre. Pode ser necessário reduzir o número de instantâneos mantidos ou os volumes talvez precise ser estendido (armazenamento adicionais pode ser solicitado em unidades de 1 TB).
- Durante as atividades como a movimentação de dados em SAP HANA com ferramentas de migração do sistema (com R3Load ou restaurando bancos de dados do SAP HANA de backups), é altamente recomendável não executar quaisquer instantâneos de armazenamento. (Geralmente uma migração de sistema estiver sendo feita em um novo sistema SAP HANA, portanto instantâneos de armazenamento não precisa ser executada nesse caso).
- Durante a maior reorganizações de tabelas do SAP HANA, instantâneos de armazenamento devem ser evitados se possível.
- Além disso, os instantâneos de armazenamento são um pré-requisito para envolver os recursos de DR do SAP HANA no Azure (Instâncias Grandes).

### <a name="setting-up-storage-snapshots"></a>Configurar instantâneos de armazenamento

1. Certifique-se de que Perl está instalado no sistema operacional Linux no servidor do HANA grandes instâncias.

2. Modifique /etc/ssh/ssh\_config para adicionar alinha _MACs hmac-sha1_.

3. Crie uma conta de usuário de backup do SAP HANA no nó principal para cada instância do SAP HANA que estão em execução (se aplicável).

4. O cliente SAP HANA HDB deve ser instalado em todos os servidores do HANA em Instâncias Grandes.

5. No primeiro servidor HANA grandes instâncias de cada região, deve ser criada uma chave pública para acessar a infraestrutura básica de armazenamento que controla a criação do instantâneo.

6. Copie o script azure\_hana\_backup.pl de /scripts para o local do **hdbsql** da instalação do SAP HANA.

7. Copie o arquivo HANABackupDetails.txt /scripts mesmo local que o script Perl.

8. Modifique o arquivo HANABackupDetails.txt conforme necessário para as especificações do cliente apropriado.

### <a name="detailed-storage-snapshot-steps"></a>Etapas de instantâneo detalhado de armazenamento

**Etapa 1: Instalar o HDBClient do SAP HANA**

O Linux instalado em SAP HANA no Azure (Instâncias Grandes) inclui as pastas e os scripts necessários para executar instantâneos de armazenamento SAP HANA para fins de DR e backup. No entanto, é sua responsabilidade instalar SAP HANA HDBclient durante a instalação do SAP HANA.  (Microsoft não instalará o HDBclient nem SAP HANA).

**Etapa 2: Alterar/etc/ssh/ssh\_config**

Alterar/etc/ssh/ssh\_config adicionando _MACs hmac-sha1_ linha conforme mostrado abaixo.
Edite a seção abaixo para adicionar **MACs hmac-sha1**:
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

**Etapa 3: Criar uma chave pública**

Do SAP HANA primeiro no servidor do Azure (Instâncias Grandes) em cada região do Azure, uma chave pública deve ser criado que é usada para acessar a infraestrutura de armazenamento, permitindo a criação de instantâneos. A chave pública garante uma senha não é necessária fazer logon para o armazenamento e que as credenciais de senha não são mantidas. No Linux no servidor do HANA em Instâncias Grandes, execute o seguinte comando para gerar a chave pública:
```
  ssh-keygen –t dsa –b 1024
```
O novo local é _/root/.ssh/id\_dsa.pub.__Não_ insira uma senha real ou cada logon exigirá que você insira a senha antes de continuar. Em vez disso, pressione **Enter** duas vezes para remover o requisito de senha enter após o logon.

Verifique se a chave pública foi corrigida conforme o esperado, alterar pastas para /root/.ssh/ e, em seguida, executando o comando **ls** para_._ Se a chave estiver presente, a chave pública é copiada, executando o comando a seguir e, em seguida, a cadeia de caracteres inteira é copiada:

![Chave pública é copiada executando este comando](./media/sap-hana-overview-high-availability-disaster-recovery/image2-public-key.png)

Neste ponto, entre em contato com SAP HANA em gerenciamento de serviços do Azure e forneça a chave. Eles usarão a chave pública para registrá-lo na infraestrutura de armazenamento subjacente.

**Etapa 4: Criar uma conta de usuário do SAP HANA**

Crie uma conta de usuário do SAP HANA no Studio de SAP HANA para fins de backup. Essa conta deve ter os seguintes privilégios: administrador de BACKUP e leia do catálogo. Neste exemplo, a conta de usuário do SCADMIN é criada.

![Criando um usuário no Studio HANA](./media/sap-hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

**Etapa 5: Autorizar a conta de usuário do SAP HANA**

Autorize a conta de usuário SAPA HANA (a ser usado pelos scripts sem exigir autorização toda vez que o script é executado). O comando **hdbuserstore** do SAP HANA permite a criação de uma chave de usuário do SAP HANA armazenada em um ou mais nós do SAP HANA e permite que o usuário acesse o SAP HANA sem a necessidade de gerenciar suas senhas no processo de script discutidas abaixo.

>[!IMPORTANT]
>O comando a seguir deve ser executado como _raiz_. Caso contrário, o script não funcionará corretamente.

O comando **hdbuserstore** é inserido da seguinte maneira:

![O comando hdbuserstore é inserido ](./media/sap-hana-overview-high-availability-disaster-recovery/image4-hdbuserstore-command.png)

No exemplo a seguir, onde o usuário é SCADMIN01 e o nome do host é lhanad01, o comando é:
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
Escolha esta opção gerenciar todos os scripts de servidor único para instâncias do HANA de expansão. Neste exemplo, a chave do SAP HANA de SCADMIN01 deve ser alterada para cada host de forma que reflita o host que está relacionado à chave (ou seja, a conta de backup SAP HANA é redefinida com o número da instância do banco de dados do HANA **lhanad**). A chave deve ter privilégios administrativos no host que é atribuído a, e o usuário de backup para expansão deve ter direitos de acesso a todas as instâncias do SAP HANA.
```
hdbuserstore set SCADMIN01 lhanad:30015 SCADMIN <password>
hdbuserstore set SCADMIN02 lhanad:30115 SCADMIN <password>
hdbuserstore set SCADMIN03 lhanad:30215 SCADMIN <password>
```

**Etapa 6: Copiar itens da pasta /scripts**

Copie os seguintes itens na pasta /scripts, incluída na imagem de ouro da instalação, para o diretório de trabalho **hdbsql**. Para instalações atuais do HANA, isso é /hana/shared/D01/exe/linuxx86\_64/hdb.
```
azure\_hana\_backup.pl
testHANAConnection.pl
testStorageSnapshotConnection.pl
removeTestStorageSnapshot.pl
HANABackupCustomerDetails.txt
```
ou os seguintes itens se eles estiverem executando expansão ou OLAP:
```
azure\_hana\_backup\_bw.pl
testHANAConnectionBW.pl
testStorageSnapshotConnectionBW.pl
removeTestStorageSnapshotBW.pl
HANABackupCustomerDetailsBW.txt
```
O arquivo HANABackupCustomerDetails.txt pode ser modificado conforme segue, para uma implantação de expansão. Basicamente, é o arquivo de configuração e controle para o script que executará os instantâneos de armazenamento. Observe que você deve ter recebido a _nome do Backup de armazenamento_ e _endereço de IP do armazenamento_ do SAP HANA em gerenciamento de serviços do Azure quando suas instâncias foram implantadas. Você _não é possível_ modificar a sequência de ordenação, ou o espaçamento de qualquer uma das variáveis, ou o script não será executado corretamente.

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
É importante observar que apenas o nó 1 detalhes são usados no script de instantâneo de armazenamento real do HANA atualmente, mas é recomendável que você teste o acesso de todos os nós do HANA para que, se o nó de backup mestre deve mudar, você já tiver assegurado que qualquer outro nó poderá assumir seu lugar, modificando os detalhes no nó 1.

Para verificar as configurações corretas no arquivo de configuração ou a conectividade apropriada para as instâncias do HANA, você deve executar a primeira abaixo de script (independente na carga de trabalho do SAP) listado para uma configuração de escala vertical e o segundo script na lista para uma configuração de expansão.
```
testHANAConnection.pl
```
ou para configurações de expansão:
```
testHANAConnectionBW.pl
```
Verifique se a instância mestre do HANA tem acesso a todos os itens necessários servidores HANA. Não existem parâmetros para o script, mas o arquivo HANABackupCustomerDetails/ HANABackupCustomerDetailsBW apropriado deve ser concluído para que o script seja executado corretamente. Embora não seja possível para o script verificar erros em cada instância específica, já que somente os códigos de erro de comando do shell são retornados, o script fornece alguns comentários úteis para você verificar duas vezes.

Para executar o script:
```
 ./testHANAConnection.pl
```
O script produzirá a mensagem de que a conexão do HANA foi bem-sucedida se o script for bem-sucedido ao obter o status da instância do HANA.

Além disso, há um segundo tipo de script para verificar a capacidade do HANA instância do servidor mestre para fazer logon no armazenamento. É necessário que você execute o seguinte script antes de executar o azure\_hana\_backup (\_bw). pl script. Sempre que não houver instantâneos em um volume, será impossível determinar a diferença entre um volume sem instantâneos ou uma falha de ssh na obtenção de detalhes do instantâneo.  Por esse motivo, a execução de script a seguir executa duas etapas:

- Verifica se o console de armazenamento é acessível.
- Cria um instantâneo de teste ou fictício, para cada volume por instância do HANA.

Por esse motivo, a instância do HANA é incluída como um argumento. Novamente, não é possível fornecer a verificação de erros como ele se relaciona com a conexão de armazenamento, mas o script fornece algumas dicas úteis se a execução falhará.

O script é executado como:
```
 ./testStorageSnapshotConnection.pl <hana instance>
```
ou o
```
./testStorageSnapshotConnectionBW.pl <hana instance>
```
O script também retornará a mensagem que você pode fazer logon adequadamente para seu locatário de armazenamento implantado é organizado em torno de seu LUNs usados pelas instâncias do servidor que você possui.

Antes de executar os backups de baseados em instantâneo de armazenamento primeiro, você deve executar esses scripts para certificar-se de que a configuração esteja correta.

Depois de executar esses scripts, você deverá excluir os instantâneos executando:
```
./removeTestStorageSnapshot.pl <hana instance>
```
ou o
```
./removeTestStorageSnapshot.pl <hana instance>
```

**Etapa 7: Executar instantâneos sob demanda**

Executar sob demanda instantâneos (bem como agendar instantâneos regular usando cron) conforme descrito abaixo, executando o seguinte script para configurações de escala vertical:
```
./azure_hana_backup.pl lhanad01 customer 20
```
Ou executá-lo da seguinte maneira para configurações de expansão:
```
./azure_hana_backup_bw.pl lhanad01 customer 20
```
O script de expansão faz alguma verificação adicional para verificar se todos os servidores do HANA podem ser acessados e todas as instâncias do HANA retornam o status apropriado da instância antes de prosseguir com a criação de instantâneos de armazenamento ou SAP HANA.

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

Execute o script chamando-o na pasta executável HDB que ele foi copiado em. Ele fará backup de pelo menos os seguintes volumes, mas também fará backup de qualquer volume que tem o nome de instância explícito do SAP HANA no nome do volume.
```
hana_data_<hana instance>_prod_t020_vol
hana_log_<hana instance>_prod_t020_vol
hana_log_backup_<hana instance>_prod_t020_vol
hana_shared_<hana instance>_prod_t020_vol
```
O período de retenção é estritamente administrado com o número de instantâneos enviado como um parâmetro ao executar o script (como 20, mostrada acima). Portanto a quantidade de tempo é uma função de período de execução e o número de instantâneos na chamada do script. Se o número de instantâneos que são mantidos exceder o número chamado como um parâmetro na chamada do script, o instantâneo mais antigo de armazenamento deste rótulo (no caso acima, _personalizado_), serão excluídos antes de executar um novo instantâneo. Isso significa que o número você conceder como o último parâmetro da chamada é o número que você pode usar para controlar o número de instantâneos.

>[!NOTE] 
>Como alterar o rótulo, a contagem começa novamente.

Você precisa incluir o nome da instância HANA fornecido pelo SAP HANA em gerenciamento de serviços do Azure como um argumento se eles são ambientes de vários nós de instantâneos. Em ambientes de nó único, o nome do SAP HANA na unidade do Azure (instância grande) é suficiente, mas o nome da instância HANA é recomendado.

Além disso, a capacidade é fornecida para volumes de inicialização de backup \luns usando o mesmo script. Você deve fazer backup do volume de inicialização pelo menos uma vez ao primeiro obter HANA em execução, embora um agendamento de backup semanal ou todas as noite para inicialização em cron é recomendado. Em vez de adicionar um nome de instância do SAP HANA, basta inserir _inicialização_ como o argumento para o script da seguinte maneira:
```
./azure_hana_backup boot customer 20
```
A mesma política de retenção é que o volume de inicialização. Instantâneos sob demanda, conforme descrito acima, devem ser usados para somente os casos especiais (tais como durante uma atualização do SAP EHP ou em horários quando você precisa criar um instantâneo de armazenamento distintos).

Você é altamente incentivados a realizar instantâneos de armazenamento agendado usando cron e é recomendável que o mesmo script é usado para todos os backups e necessidades de DR (modificando as entradas do script para corresponder os diferentes tempos de backup solicitados). Esses são todos agendadas de forma diferente no cron dependendo de seu tempo de execução: por hora, 12 horas, diária ou semanal (dependendo das circunstâncias). A agenda de cron é projetada para criar instantâneos de armazenamento que correspondem à rotulagem de retenção discutido anteriormente para backup externo de longo prazo. O script inclui comandos para fazer o backup de todos os volumes de produção, dependendo da frequência solicitada (arquivos de log e dados de backup por hora, enquanto o volume de inicialização só é feito backup diário).

As entradas no script a seguir cron executado a cada hora no décimo minuto, cada 12 horas no décimo minuto e diariamente no décimo minuto. O cron trabalhos são criados de forma que apenas um instantâneo do armazenamento SAP HANA ocorre durante qualquer determinada hora, para que um backup horário e diário não ocorre ao mesmo tempo (12:10 AM). SAP HANA em gerenciamento de serviços do Azure fornecerá o momento indicado para executar os backups para otimizar sua criação de instantâneos e replicação.

O cron padrão /etc/crontab de agendamento é o seguinte:
```
10 1-11,13-23 * * * ./azure_hana_backup.pl lhanad01 hourly 66
10 12 * * *  ./azure_hana_backup.pl lhanad01 12hour 14
```
As instruções de cron acima, os volumes do HANA (sem o volume de inicialização), obtenha um instantâneo com o rótulo por hora. Esses instantâneos, 66 são mantidos. Além disso, 14 instantâneos com o rótulo de 12 horas são mantidos, portanto, você tem a possibilidade de instantâneos por hora de três dias, além de instantâneos de 12 horas para mais de quatro dias, basicamente para cobrir uma semana inteira com esses instantâneos. (Não os instantâneos por hora estão agendados em cada um dos 12 horas de completo. Apenas um instantâneo é criado em 12 horas, em vez de por hora e um instantâneo de 12 horas).

Agendamento em cron pode ser complicado, pois somente um script deve ser executado em um determinado momento, a menos que o script é alternado por vários minutos. Se você quiser backups diários para retenção de longo prazo, um diário é mantido junto com uma 12 horas (com contagem uma retenção de sete cada) ou o instantâneo por hora é alternado ocorra dez minutos mais tarde mas apenas um instantâneo diário é mantido no volume de produção.
```
10 1-11,13-23 * * * ./azure_hana_backup.pl lhanad01 hourly 66
10 12 * * *  ./azure_hana_backup.pl lhanad01 12hour 7 
10 0 * * * ./azure_hana_backup.pl lhanad01 daily 7
```
As frequências listadas são apenas exemplos. Você precisa obter o número ideal de instantâneos com base nos seguintes critérios:

- Requisitos de RTO para recuperação point-in-time.
- Uso de espaço.
- Requisitos de RTO e RPO para potenciais recuperações de desastre.
- Execução eventual de backups completos de banco de dados do HANA em discos. Sempre que um backup completo do banco de dados em discos, ou a interface _backint_ interface, for executado, a execução de instantâneos de armazenamento falhará. Se você planeja executar backups de banco de dados completo sobre instantâneos de armazenamento, você precisa certificar-se de que a execução de instantâneos de armazenamento é desabilitada durante esse tempo.

>[!IMPORTANT]
> O uso de instantâneos de armazenamento para backups do SAP HANA só é válido quando executada em conjunto com backups de log do SAP HANA. Esses backups de log precisam ser capaz de abordar os períodos de tempo entre os instantâneos de armazenamento. Se um compromisso para usuários tiver sido definido para um ponto de recuperação pontual de 30 dias, é necessário o seguinte:

- Capacidade de acessar um instantâneo de armazenamento que é de 30 dias.
- Backups de log contíguo nos últimos 30 dias.

O intervalo de backups de log, instantâneo também o volume do log de backup. No entanto, você deve garantir que os backups de log regulares são executados para:

- Tenha os backups de log contíguo necessários para realizar recuperações point-in-time.
- O volume do log Hana SAP impedir a execução sem espaço.

Uma das últimas etapas é agendar logs de backup SAP HANA no Studio de SAP HANA. O destino de backup de log do SAP HANA é o hana/log criado especialmente\_volume backups com o ponto de montagem de /hana/log/backups.

![Agendar logs de backup SAP HANA no Studio de SAP HANA](./media/sap-hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

É possível escolher backups mais frequentes que a cada 15 minutos. Alguns usuários até mesmo executam backups de log a cada minuto, entretanto _sobre_ 15 minutos não é recomendado.

A última etapa é criar um backup baseado em arquivo (após a instalação inicial do SAP HANA) para criar uma entrada de backup única que deve existir no catálogo de backup. Caso contrário, SAP HANA não iniciará o log especificado backups definidos anteriormente.

![Faça um backup baseado em arquivo para criar uma única entrada de backup](./media/sap-hana-overview-high-availability-disaster-recovery/image6-make-backup.png)

### <a name="monitoring-number-and-size-of-snapshots-on-disk-volume"></a>Monitoramento de número e tamanho dos instantâneos no volume de disco

Em um volume de armazenamento específico, você pode monitorar o número de instantâneos e o consumo de armazenamento de instantâneos. O **ls** comando não mostrará o diretório de instantâneo ou arquivos. No entanto, o comando do sistema operacional Linux **du** será, com os seguintes comandos:
```
- du –sh .snapshot will provide a total of all snapshots within the snapshot directory.
- du –sh --max-depth=1 will list all snapshots saved in the .snapshot folder and the size of each snapshot.
- du –hc will just provide the total size used by all snapshots.
```
Use estes comandos para certificar-se de que os instantâneos feito e armazenado não estão consumindo todo o armazenamento nos volumes.

### <a name="reducing-the-number-of-snapshots-on-a-server"></a>Reduzindo o número de instantâneos em um servidor

Conforme explicado anteriormente, você pode reduzir o número armazenado de etiqueta de instantâneos. Os dois últimos parâmetros do comando para iniciar um instantâneo são um rótulo e o número de instantâneos que você deseja manter.
```
./azure_hana_backup.pl lhanad01 customer 20
```
No exemplo acima, o rótulo de instantâneo é _cliente_ e o número de instantâneos com este rótulo a ser retido é _20_. Como responder ao consumo de espaço em disco, convém reduzir o número de instantâneos armazenados. É a maneira fácil de reduzir os instantâneos executar o script com o último parâmetro definido como 5:
```
./azure_hana_backup.pl lhanad01 customer 5
```
Como resultado da execução do script com configuração acima, os instantâneos são sendo reduzidos, portanto, o número de instantâneos, incluindo o novo instantâneo de armazenamento é 5. Lembre-se:

- Uma redução só pode ser obtida se o último instantâneo criado antes da execução com a redução do número mais de uma hora de duração.
- O script também não exclua os instantâneos com menos de uma hora.

Essas restrições estão relacionadas à funcionalidade de DR opcional oferecida.

Suponha que você não estiver mais interessado em manter um conjunto de instantâneos com o prefixo, você pode executar o script com _0_ como a retenção de número e o script irá remover todos os instantâneos de correspondência de prefixo e, em seguida, o script será encerrado. No entanto, isso afetará os recursos de DR.

### <a name="recovering-to-the-latest-hana-snapshot"></a>Recuperar o instantâneo mais recente do HANA

No caso de ocorrer uma inatividade cenário de produção, o processo de recuperação de um instantâneo de armazenamento pode ser iniciado como um incidente do cliente com SAP HANA em gerenciamento de serviços do Azure. Em alguns casos, isso seria uma questão inesperados e alta urgência se dados foram excluídos em um sistema de produção e é a única maneira de obter dados através de uma restauração do banco de dados de produção.

Por outro lado, uma recuperação point-in-time pode ser baixa urgência e planejados em frente para dias de antecedência. Você pode planejar isso com o SAP HANA em gerenciamento de serviços do Azure em vez de gerar um problema de alta prioridade. Isso seria o caso se você planeja tentar uma atualização do software SAP aplicando um novo pacote de aprimoramento e, em seguida, é necessário reverter para um instantâneo que representa o estado antes da atualização EHP.

Há algumas preparações que você precisa fazer antes de emitir a solicitação, conforme mostrado abaixo. SAP HANA na equipe de gerenciamento de serviços do Azure será capaz de manipular a solicitação e fornecer os volumes restaurados. Cabe, em seguida, restaurar o banco de dados do HANA com base em instantâneos.

>[!NOTE]  
>As capturas de tela abaixo podem variar com base na versão SAP HANA que você usar.

1. Decida qual instantâneo para restaurar. Somente o volume de dados/hana voltará a menos que indicado o contrário.

2. Desligar a instância do HANA.
![Desligar a instância do HANA](./media/sap-hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

3. Desmonte os volumes de dados em cada nó do banco de dados do HANA. A restauração do instantâneo falhará se os volumes de dados não são desmontados.
![Desmontar os volumes de dados em cada nó de banco de dados do HANA](./media/sap-hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

4. Abra uma solicitação de suporte do Azure para instruir a restauração de um instantâneo específico.

    **Durante a restauração do instantâneo de armazenamento,** o SAP HANA no Gerenciamento de Serviços do Azure está restaurando os instantâneos de volumes e poderá pedir que você participe de uma chamada de conferência para garantir que nenhum dado seja perdido.

    **Depois de restaurar o armazenamento de instantâneo,** o SAP HANA no Gerenciamento de Serviços do Azure será notificado quando o instantâneo de armazenamento tiver sido restaurado. 

5. Quando o processo de restauração for concluído, monte novamente todos os volumes de dados.
![Monte novamente todos os volumes de dados](./media/sap-hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

6. Selecione opções de recuperação no estúdio do SAP HANA, caso elas não apareçam automaticamente na reconexão ao BD do HANA por meio do HANA Studio. O exemplo a seguir mostra uma restauração para o último instantâneo HANA. Um instantâneo do armazenamento incorpora um instantâneo do HANA e, se você estiver restaurando para o último instantâneo de armazenamento, ele deverá ser o instantâneo mais recente do HANA. (Se você está restaurando os instantâneos de armazenamento mais antigos, você precisa localizar o instantâneo do HANA baseado na hora em que o armazenamento de instantâneo foi tirado). ![Selecione as opções de recuperação no SAP HANA Studio](./media/sap-hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

7. Selecione o tipo de recuperação **Recuperar o banco de dados para um instantâneo de backup ou armazenamento de dados específico**.
![Selecione o tipo de recuperação Recuperar o banco de dados para um instantâneo de backup ou armazenamento de dados específico](./media/sap-hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

8. Escolha a opção **especificar backup sem catálogo**.
![Escolha a opção Especificar backup sem catálogo](./media/sap-hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

9. Escolha **Tipo de Destino: Instantâneo**.
![Escolha Tipo de Destino: Instantâneo](./media/sap-hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

10. Clique em **Concluir** para iniciar o processo de recuperação.
![Clique em Concluir para iniciar o processo de recuperação](./media/sap-hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

11. O banco de dados do HANA é restaurado e recuperado para o instantâneo do HANA incluído pelo instantâneo de armazenamento.
![O banco de dados do HANA é restaurado e recuperado para o instantâneo do HANA incluído pelo instantâneo de armazenamento](./media/sap-hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recovering-to-most-recent-state"></a>Recuperando o estado mais recente

Esse processo restaura a um instantâneo do HANA que está incluído no instantâneo de armazenamento e, em seguida, restaura os backups de log de transações para o estado mais recente do banco de dados antes de restaurar o instantâneo de armazenamento.

>[!Important] 
>Certifique-se de que você tenha uma cadeia completa e contígua de backups de log de transações antes de continuar. Sem que você não vai para poder voltar para o estado atual do banco de dados.

Siga as etapas 1 a 6 do procedimento de recuperação para o instantâneo mais recente do HANA descrito acima.

1. Escolha **recuperar o banco de dados para seu estado mais recente**.
![Escolha recuperar o banco de dados para seu estado mais recente](./media/sap-hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

2. Especifique o local dos backups de log mais recentes do HANA. O local especificado deve conter todos os HANA transação backups de log do instantâneo do HANA estado mais recente.
![Especifique o local dos backups de log mais recentes do HANA](./media/sap-hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

3. Escolha um backup como uma base recuperar o banco de dados. Em nosso exemplo, este é o instantâneo do HANA que foi incluído no instantâneo de armazenamento. (Apenas um desses instantâneos foi feito na seguinte captura de tela). ![Escolha um backup como uma base recuperar o banco de dados](./media/sap-hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

4. Desmarque **Usar Backups Delta** se eles não existirem entre a hora do instantâneo do HANA e o estado mais recente.
![Desmarque Usar Backups Delta se eles não existirem entre a hora do instantâneo do HANA e o estado mais recente](./media/sap-hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

5. Clique em **Concluir** na tela de resumo para iniciar o procedimento de restauração.
![Clique em Concluir na tela de resumo para iniciar o procedimento de restauração](./media/sap-hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

Recuperar para um ponto de tempo entre o instantâneo HANA (incluído no instantâneo de armazenamento) e posterior HANA de instantâneo, a recuperação point-in-time:

- Certifique-se de que você tenha todas as dos backups do log de transações do instantâneo do HANA até o momento em que você deseja recuperar.
- Escolha **recuperar o banco de dados para o seguinte ponto no tempo** (consulte _restaurar o estado mais recente_, etapa 1).
- As etapas restantes são o mesmo que acima.

## <a name="monitoring-snapshot-execution"></a>Monitorando a execução de instantâneo

A execução de instantâneos de armazenamento precisa ser monitorado por você. O script que executa um instantâneo de armazenamento grava a saída em um arquivo e salva no mesmo local que os scripts Perl. Um arquivo separado é criado para cada instantâneo. A saída desse arquivo mostra claramente diferentes fases que executará o script de instantâneo, que são:

- Localizando os volumes que precisa para criar um instantâneo.
- Localizando os instantâneos tirados desses volumes.
- Excluindo instantâneos existentes eventuais para corresponder ao número de instantâneos que você especificou.
- Criando um instantâneo do HANA.
- Criação do instantâneo de armazenamento sobre os volumes.
- Excluindo o instantâneo HANA.
- Renomeando o instantâneo mais recente **.0**.

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
Você pode ver a este exemplo como o script registros a criação do instantâneo HANA. No caso de expansão, isso é iniciado no nó principal. O nó mestre iniciará a criação síncrona de instantâneos em cada um de nós de trabalho.

Em seguida, o instantâneo de armazenamento é criado. Após a execução bem-sucedida de instantâneos de armazenamento, o instantâneo do HANA será excluído.



<!--HONumber=Dec16_HO2-->


