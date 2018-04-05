---
title: Configurar a replicação de sistema do SAP HANA em VMs (Máquinas Virtuais) do Azure | Microsoft Docs
description: Estabeleça alta disponibilidade do SAP HANA em VMs (máquinas virtuais) do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/24/2018
ms.author: sedusch
ms.openlocfilehash: b84b523f919e6b253462139b6888e5eb16248084
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="high-availability-of-sap-hana-on-azure-virtual-machines-vms"></a>Alta disponibilidade do SAP HANA em VMs (máquinas virtuais) do Azure

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged%2Fazuredeploy.json

No local, você pode usar a Replicação do Sistema HANA ou o armazenamento compartilhado para estabelecer alta disponibilidade para SAP HANA.
Na replicação de sistema do HANA em VMs do Azure no Azure é a única função de Alta Disponibilidade com suporte até o momento. A replicação do SAP HANA consiste em um nó primário e, pelo menos, um nó secundário. As alterações nos dados do nó primário são replicadas no nó secundário de modo síncrono ou assíncrono.

Este artigo descreve como implantar as máquinas virtuais, configurar as máquinas virtuais, instalar a estrutura de cluster, bem como a instalar e configurar a Replicação do Sistema SAP HANA.
Nos exemplos de configurações, comandos de instalação etc., são usados o número de instância 03 e o HDB da ID JN1 do Sistema HANA.

Primeiro, leia os seguintes documentos e Notas SAP

* A Nota SAP [1928533], que tem:
  * Lista de tamanhos de VM do Azure que têm suporte para a implantação de software SAP
  * Informações importantes sobre capacidade para tamanhos de VM do Azure
  * Software SAP e combinações de SO (sistema operacional) e banco de dados com suporte
  * A versão do kernel do SAP necessária para Windows e para Linux no Microsoft Azure
* A Nota SAP [2015553] lista pré-requisitos para implantações de software SAP com suporte do SAP no Azure.
* A Nota SAP [2205917] tem configurações de SO recomendadas para SUSE Linux Enterprise Server para aplicativos SAP
* A Nota SAP [1944799] tem Diretrizes SAP HANA para SUSE Linux Enterprise Server para aplicativos SAP
* A Nota SAP [2178632] contém informações detalhadas sobre todas as métricas de monitoramentos relatadas para o SAP no Azure.
* A Nota SAP [2191498] tem a versão necessária do SAP Host Agent para Linux no Azure.
* A Nota SAP [2243692] tem informações sobre o licenciamento do SAP no Linux no Azure.
* A Nota SAP [1984787] tem informações gerais sobre o SUSE Linux Enterprise Server 12.
* A Nota SAP [1999351] tem informações de solução de problemas adicionais para a Extensão de Monitoramento Avançado do Azure para SAP.
* [WIKI da comunidade do SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as Notas SAP necessárias para Linux.
* [Planejamento e implementação de Máquinas Virtuais do Azure para SAP no Linux][planning-guide]
* [Implantação de máquinas virtuais do Azure para SAP no Linux (este artigo)][deployment-guide]
* [Implantação de Máquinas Virtuais do Azure do DBMS para SAP no Linux][dbms-guide]
* [Cenário otimizado de desempenho de SAP HANA SR][suse-hana-ha-guide] O guia contém todas as informações necessárias para definir a configuração local da Replicação do Sistema SAP HANA. Use este guia como uma linha de base.

## <a name="overview"></a>Visão geral

Para obter a alta disponibilidade, o SAP HANA é instalada em duas máquinas virtuais. Os dados são replicados usando a replicação de sistema do HANA.

![Visão geral de Alta Disponibilidade do SAP HANA](./media/sap-hana-high-availability/ha-suse-hana.png)

O SAP HANA SR usa um nome de host virtual dedicado e endereços de IP virtuais. No Azure, um balanceador de carga é necessário para usar um endereço IP virtual. A lista a seguir mostra a configuração do balanceador de carga.

* Configuração de front-end
  * Endereço de IP 10.0.0.13 para hn1 db
* Configuração de back-end
  * Conectado aos adaptadores de rede primários de todas as máquinas virtuais que devem ser parte do HANA System Replication
* Porta de Investigação
  * Porta 62503
* Regras de balanceamento de carga
  * 30313 TCP
  * 30315 TCP
  * 30317 TCP

## <a name="deploying-linux"></a>Implantação do Linux

O agente de recursos para HANA SAP está incluído no SUSE Linux Enterprise Server for SAP Applications.
O Azure Marketplace contém uma imagem para SUSE Linux Enterprise Server for SAP Applications 12 que você pode usar para implantar novas máquinas virtuais.

### <a name="deploy-with-template"></a>Implantar com modelo
Você pode usar um dos modelos de início rápido no github para implantar todos os recursos necessários. O modelo implanta as máquinas virtuais, o balanceador de carga, o conjunto de disponibilidade etc. Para implantar o modelo, siga estas etapas:

1. Abra o [modelo de banco de dados][template-multisid-db] ou o [modelo convergido][template-converged] no portal do Azure. 
   O modelo de banco de dados cria apenas as regras de balanceamento de carga para um banco de dados, enquanto o modelo convergido também cria as regras de balanceamento de carga para uma instância do ASCS/SCS e ERS (apenas Linux). Se você planeja instalar um sistema baseado no SAP NetWeaver e também quer instalar a instância ASCS/SCS nas mesmas máquinas, use o [modelo convergido][template-converged].
1. Defina os seguintes parâmetros
    1. ID do sistema SAP  
       Insira a ID do sistema SAP do sistema SAP que você deseja instalar. A ID será usada como um prefixo para os recursos implantados.
    1. Tipo de pilha (aplicável somente se você usar o modelo convergido)   
       Selecionar o tipo de pilha do SAP NetWeaver
    1. Tipo de sistema operacional  
       Selecione uma das distribuições do Linux. Para este exemplo, selecione SLES 12
    1. Tipo de banco de dados  
       Selecionar HANA
    1. Tamanho do sistema SAP  
       A quantidade de SAPS que será fornecida pelo novo sistema. Se não tiver certeza de quantos SAPS o sistema precisará, pergunte ao Parceiro de Tecnologia SAP ou ao Integrador de Sistemas
    1. Disponibilidade do sistema  
       Selecione HA
    1. Nome de Usuário de Administrador e Senha do Administrador  
       É criado um novo usuário que pode ser usado para fazer logon no computador.
    1. Sub-rede Nova ou Existente  
       Determina se uma nova rede virtual e sub-rede devem ser criadas ou se uma sub-rede existente deve ser usada. Se você já tiver uma rede virtual conectada à sua rede local, selecione a rede existente.
    1. ID da Sub-rede  
    A ID da sub-rede à qual as máquinas virtuais devem estar conectadas. Para conectar a máquina virtual à rede local, selecione a sub-rede da VPN ou a rede virtual do ExpressRoute. A ID geralmente é semelhante a /subscriptions/`<subscription ID`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`>

### <a name="manual-deployment"></a>Implantação manual

1. Criar um grupo de recursos
1. Criar uma rede virtual
1. Crie um Conjunto de Disponibilidade  
   Defina o máximo de domínio de atualização
1. Crie um Balanceador de Carga (interno)  
   Selecione a VNET criada no segundo
1. Crie a Máquina Virtual 1  
   Use pelo menos SLES4SAP 12 SP1 neste exemplo, vamos usar a imagem SLES4SAP 12 SP2 https://ms.portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP2PremiumImage-ARM  
   SLES para SAP SP2 12 (Premium)  
   Selecione o Conjunto de Disponibilidade criado anteriormente  
1. Crie a Máquina Virtual 2  
   Use no mínimo SLES4SAP 12 SP1, neste exemplo usaremos a imagem SLES4SAP 12 SP1 BYOS https://ms.portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP2PremiumImage-ARM  
   SLES para SAP SP2 12 (Premium)  
   Selecione o Conjunto de Disponibilidade criado anteriormente  
1. Adicione Discos de Dados
1. Configure o balanceador de carga
    1. Crie um pool de IPs de front-end
        1. Abra o balanceador de carga, selecione o pool de IPs de front-end e clique em Adicionar
        1. Digite o nome do novo pool de IPs de front-end (por exemplo hana-front-end)
        1. Defina a Atribuição ao Estático e digite o endereço IP (por exemplo **10.0.0.13**)
        1. Clique em OK
        1. Depois que o novo pool de IPs de front-end for criado, anote seu endereço IP
    1. Crie um pool de back-end
        1. Abra o balanceador de carga, selecione os pools de back-end e clique em Adicionar
        1. Digite o nome do novo pool de back-end (por exemplo hana-back-end)
        1. Clique em Adicionar uma máquina virtual
        1. Selecione o Conjunto de Disponibilidade criado anteriormente
        1. Selecione as máquinas virtuais do cluster SAP HANA
        1. Clique em OK
    1. Criar uma investigação de integridade
        1. Abra o balanceador de carga, selecione as investigações de integridade e clique em Adicionar
        1. Digite o nome da nova investigação de integridade (por exemplo, hana-hp)
        1. Escolha TCP como protocolo, porta 625**03**, mantenha o Intervalo como 5 e o Limite de não integridade como 2
        1. Clique em OK
    1. Criar regras de balanceamento de carga
        1. Clique no balanceador de carga, escolha as regras de balanceamento de carga e clique em Adicionar
        1. Insira o nome da nova regra de balanceador de carga (por exemplo hana-lb-3**03**15)
        1. Selecione o endereço IP de front-end, o pool de back-end e a investigação de integridade criados anteriormente (por exemplo, hana-frontend)
        1. Mantenha o protocolo TCP, insira a porta 3**03**13
        1. Aumente o tempo limite de ociosidade para 30 minutos
        1. **Habilite o IP Flutuante**
        1. Clique em OK
        1. Repita as etapas acima para a porta 3**03**15 e 3**03**17

## <a name="create-pacemaker-cluster"></a>Criar cluster do Pacemaker

Siga as etapas em [Configurar Pacemaker no SUSE Linux Enterprise Server no Azure](high-availability-guide-suse-pacemaker.md) para criar um cluster Pacemaker básico para esse servidor HANA. Você também pode usar o mesmo cluster Pacemaker para o SAP HANA e SAP NetWeaver (A) SCS.

## <a name="installing-sap-hana"></a>Instalando o SAP HANA

Os itens a seguir são prefixados com **[A]** – aplicável a todos os nós, **[1]** – aplicável somente ao nó 1 ou **[2]** – aplicável somente ao nó 2 do cluster do Pacemaker.

1. **[A]** Configure o layout do disco
    1. LVM  
       
       Normalmente, é recomendável usar LVM para volumes que armazenam arquivos de log e dados. O exemplo a seguir pressupõe que as máquinas virtuais tenham quatro discos de dados anexados que devem ser usados para criar dois volumes.

       Relacionar todas as funções disponíveis
       
       <pre><code>
       ls /dev/disk/azure/scsi1/lun*
       </code></pre>
       
       Saída de exemplo
       
       ```
       /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
       ```
       
       Crie volumes físicos para todos os discos que queira usar.    
       <pre><code>
       sudo pvcreate /dev/disk/azure/scsi1/lun0
       sudo pvcreate /dev/disk/azure/scsi1/lun1
       sudo pvcreate /dev/disk/azure/scsi1/lun2
       sudo pvcreate /dev/disk/azure/scsi1/lun3
       </code></pre>

       Crie um grupo de volume para os arquivos de dados, um grupo de volume para os arquivos de log e um para o diretório compartilhado do SAP HANA

       <pre><code>
       sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
       sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
       sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
       </code></pre>
       
       Criar os volumes lógicos

       <pre><code>
       sudo lvcreate -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
       sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
       sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
       sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
       sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
       sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
       </code></pre>
       
       Crie os diretórios de montagem e copie a UUID de todos os volumes lógicos
       
       <pre><code>
       sudo mkdir -p /hana/data/<b>HN1</b>
       sudo mkdir -p /hana/log/<b>HN1</b>
       sudo mkdir -p /hana/shared/<b>HN1</b>
       # write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
       sudo blkid
       </code></pre>
       
       Crie entradas fstab para os três volumes lógicos
       
       <pre><code>
       sudo vi /etc/fstab
       </code></pre>
       
       Insira esta linha para /etc/fstab
       
       <pre><code>
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
       /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
       </code></pre>
       
       Monte os novos volumes
       
       <pre><code>
       sudo mount -a
       </code></pre>
    
    1. Discos simples  
       Para sistemas de demonstração, você pode colocar os arquivos de log e dados do HANA em um disco. Os comandos a seguir criam uma partição em /dev/disk/azure/scsi1/lun0 e a formatam com xfs.

       <pre><code>
       sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
       sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
       
       # write down the ID of /dev/disk/azure/scsi1/lun0-part1
       sudo /sbin/blkid
       sudo vi /etc/fstab
       </code></pre>

       Insira esta linha para /etc/fstab
       <pre><code>
       /dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
       </code></pre>

       Crie o diretório de destino e monte o disco.

       <pre><code>
       sudo mkdir /hana
       sudo mount -a
       </code></pre>

1. **[A]** Configure a resolução de nome do host para todos os hosts  
    Você pode usar um servidor DNS ou modificar /etc/hosts em todos os nós. Este exemplo mostra como usar o arquivo /etc/hosts.
   Substitua o endereço IP e o nome do host nos comandos a seguir
    ```bash
    sudo vi /etc/hosts
    ```
    Insira as seguintes linhas para /etc/hosts. Altere o endereço IP e o nome do host para corresponder ao seu ambiente    
    
   <pre><code>
   <b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** Instale os pacotes HANA HA  
    ```bash
    sudo zypper install SAPHanaSR
    
    ```

Para instalar a Replicação de Sistema do SAP HANA, siga o capítulo 4 do [guia Cenário otimizado para desempenho do SR do SAP HANA][suse-hana-ha-guide].

1. **[A]** Executar hdblcm no DVD do HANA
    * Escolha instalação -> 1
    * Selecione os componentes adicionais para instalação -> 1
    * Insira o caminho de instalação [/hana/shared]: -> ENTER
    * Insira o Nome do Host Local [..]: -> ENTER
    * Você deseja adicionar outros hosts ao sistema? (s/n) [n]: -> ENTER
    * Insira a ID do Sistema SAP HANA: <SID of HANA e.g. HN1>
    * Insira o Número da Instância [00]:   
  Número da instância do HANA. Use 03 se você usou o Modelo do Azure ou seguiu a implantação manual
    * Selecione Modo de Banco de Dados/Inserir Índice [1]: -> ENTER
    * Selecione o Uso do Sistema/Inserir Índice [4]:  
  Selecione o Uso do sistema
    * Informe o Local dos Volumes de Dados [/hana/data/HN1]: -> ENTER
    * Informe o Local dos Volumes de Log [/hana/log/HN1]: -> ENTER
    * Restringir a alocação máxima de memória? [n]: -> ENTER
    * Insira o Nome do Host do Certificado para o Host '...' [...]: -> ENTER
    * Insira a Senha de Usuário do Agente do Host SAP (sapadm):
    * Confirme a Senha de Usuário do Agente do Host SAP (sapadm):
    * Insira a Senha do Administrador de Sistema (hdbadm):
    * Confirme a Senha do Administrador de Sistema (hdbadm):
    * Insira o Diretório Base do Administrador de Sistema [/usr/sap/HN1/home]: -> ENTER
    * Insira o Shell de Logon do Administrador de Sistema [/bin/sh]: -> ENTER
    * Insira a ID de Usuário do Administrador de Sistema [1001]: -> ENTER
    * Insira a ID do Grupo de Usuários (sapsys) [79]: -> ENTER
    * Insira a Senha de Usuário do Banco de Dados (SYSTEM):
    * Confirme a Senha de Usuário do Banco de Dados (SYSTEM):
    * Reiniciar o sistema após a reinicialização do computador? [n]: -> ENTER
    * Deseja continuar? (s/n):   
  Valide o resumo e digite s para continuar

1. **[A]** Atualizar o Agente do Host do SAP  
  Baixe o arquivo mais recente do Agente do Host do SAP em [SAP Softwarecenter][sap-swcenter] e execute o comando a seguir para fazer upgrade do agente. Substitua o caminho do arquivo de modo a apontar para o arquivo que você baixou.
    ```bash
    sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
    ```

## <a name="configure-sap-hana-20-system-replication"></a>Configurar a replicação do Sistema SAP HANA 2.0

Os itens a seguir são prefixados com **[A]** – aplicável a todos os nós, **[1]** – aplicável somente ao nó 1 ou **[2]** – aplicável somente ao nó 2 do cluster do Pacemaker.

1. **[1]** Criar Banco de Dados de locatário

   Se você estiver usando o SAP HANA 2.0 ou MDC, crie um banco de dados de locatário para o sistema SAP NetWeaver. Substitua NW1 pelo SID do seu sistema SAP.

   Faça login como `<hanasid`>adm e execute o seguinte comando

   <pre><code>
   hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]**  Configurar a Replicação de Sistema no Primeiro Nó
   
   Faça logon como `<hanasid`> adm e faça backup dos bancos de dados

   <pre><code>
   hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Copie os arquivos PKI do sistema para secundário

   <pre><code>
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   Criar o site primário

   <pre><code>
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]**  Configurar a Replicação do Sistema no Segundo Nó
    
    Registre o segundo nó para iniciar a replicação de sistema. Faça login como `<hanasid`>adm e execute o seguinte comando

    <pre><code>
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>Configurar a replicação do Sistema SAP HANA 1.0

1. **[1]**  Criar os Usuários Necessários

    Faça login como raiz e execute o seguinte comando. Não se esqueça de substituir cadeias de caracteres em negrito (HN1 da ID do Sistema HANA e o número de instância 03) pelos valores da sua instalação SAP HANA.

    <pre><code>
    PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"' 
    hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync' 
    hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME' 
    </code></pre>

1. **[A]** Criar entrada de repositório de chaves
   
    Faça logon como raiz e execute o seguinte comando para criar uma nova entrada de repositório de chaves.

    <pre><code>
    PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
    hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
    </code></pre>

1. **[1]**Fazer Backup do Banco de Dados

   Faça logon como raiz e faça backup dos bancos de dados

   <pre><code>
   PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Se você usar uma instalação de vários locatários, faça também o backup do banco de dados do locatário

   <pre><code>   
   hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]**  Configurar a Replicação de Sistema no Primeiro Nó
    
    Faça logon como `<hanasid`> adm e crie o site primário.

    <pre><code>
    su - <b>hdb</b>adm
    hdbnsutil -sr_enable –-name=<b>SITE1</b>
    </code></pre>

1. **[2]**  Configurar a Replicação do Sistema no Nó Secundário

    Faça login como `<hanasid`> adm e registre o site secundário.

    <pre><code>
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="create-sap-hana-cluster-resources"></a>Crie os recursos de cluster do SAP HANA

   Primeiro, crie a topologia HANA. Execute os seguintes comandos em um dos nós de cluster do Pacemaker.
   
   <pre><code>
   sudo crm configure property maintenance-mode=true

   # replace the bold string with your instance number and HANA system ID
   
   sudo crm configure primitive rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
     operations \$id="rsc_sap2_<b>HN1</b>_HDB<b>03</b>-operations" \
     op monitor interval="10" timeout="600" \
     op start interval="0" timeout="600" \
     op stop interval="0" timeout="300" \
     params SID="<b>HN1</b>" InstanceNumber="<b>03</b>"
   
   sudo crm configure clone cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
     meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
   </code></pre>
   
   Em seguida, crie os recursos HANA.
   
   <pre><code>
   # replace the bold string with your instance number, HANA system ID and the frontend IP address of the Azure load balancer. 
      
   sudo crm configure primitive rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> ocf:suse:SAPHana \
     operations \$id="rsc_sap_<b>HN1</b>_HDB<b>03</b>-operations" \
     op start interval="0" timeout="3600" \
     op stop interval="0" timeout="3600" \
     op promote interval="0" timeout="3600" \
     op monitor interval="60" role="Master" timeout="700" \
     op monitor interval="61" role="Slave" timeout="700" \
     params SID="<b>HN1</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
     DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"
   
   sudo crm configure ms msl_SAPHana_<b>HN1</b>_HDB<b>03</b> rsc_SAPHana_<b>HN1</b>_HDB<b>03</b> \
     meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
     target-role="Started" interleave="true"
   
   sudo crm configure primitive rsc_ip_<b>HN1</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \
     meta target-role="Started" is-managed="true" \
     operations \$id="rsc_ip_<b>HN1</b>_HDB<b>03</b>-operations" \
     op monitor interval="10s" timeout="20s" \
     params ip="<b>10.0.0.13</b>"
   
   sudo crm configure primitive rsc_nc_<b>HN1</b>_HDB<b>03</b> anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g_ip_<b>HN1</b>_HDB<b>03</b> rsc_ip_<b>HN1</b>_HDB<b>03</b> rsc_nc_<b>HN1</b>_HDB<b>03</b>
   
   sudo crm configure colocation col_saphana_ip_<b>HN1</b>_HDB<b>03</b> 2000: g_ip_<b>HN1</b>_HDB<b>03</b>:Started \
     msl_SAPHana_<b>HN1</b>_HDB<b>03</b>:Master  
   
   sudo crm configure order ord_SAPHana_<b>HN1</b>_HDB<b>03</b> 2000: cln_SAPHanaTopology_<b>HN1</b>_HDB<b>03</b> \
     msl_SAPHana_<b>HN1</b>_HDB<b>03</b>
   
   # Cleanup the HANA resources. The HANA resources might have failed because of a known issue.
   sudo crm resource cleanup rsc_SAPHana_<b>HN1</b>_HDB<b>03</b>

   sudo crm configure property maintenance-mode=false
   </code></pre>

   Verifique se o status do cluster é ok e se todos os recursos estão iniciados. Não importa em qual nó os recursos estão sendo executados.

   <pre><code>
   sudo crm_mon -r
   
   # Online: [ hn1-db-0 hn1-db-1 ]
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): Started hn1-db-0
   # rsc_st_azure    (stonith:fence_azure_arm):      Started hn1-db-1
   # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
   #     Started: [ hn1-db-0 hn1-db-1 ]
   # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
   #     Masters: [ hn1-db-0 ]
   #     Slaves: [ hn1-db-1 ]
   # Resource Group: g_ip_HN1_HDB03
   #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hn1-db-0
   #     rsc_nc_HN1_HDB03   (ocf::heartbeat:anything):      Started hn1-db-0
   </code></pre>

### <a name="test-cluster-setup"></a>Testar configuração do cluster
Este capítulo descreve como você pode testar a instalação. Cada teste supõe que você é a raiz e o mestre do SAP HANA está em execução na máquina virtual hn1-db-0.

#### <a name="fencing-test"></a>Teste de isolamento

Você pode testar a configuração do agente de isolamento desabilitando a interface de rede no nó hn1-db-0.

<pre><code>
sudo ifdown eth0
</code></pre>

A máquina virtual agora deve ser reiniciada ou interrompida, dependendo a configuração do cluster.
Se você definir a ação de stonith como desativada, a máquina virtual será interrompida e os recursos serão migrados para a máquina virtual em execução.

Depois de reiniciar a máquina virtual, o recurso do SAP HANA falhará ao ser iniciado como secundário se você definir AUTOMATED_REGISTER="false". Nesse caso, configure a instância do HANA como secundária executando este comando:

<pre><code>
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

#### <a name="testing-a-manual-failover"></a>Teste de um failover manual

Você pode testar um failover manual interrompendo o serviço pacemaker no nó hn1-db-0.
<pre><code>
service pacemaker stop
</code></pre>

Após o failover, você pode iniciar o serviço novamente. Se você definir AUTOMATED_REGISTER="false", o recurso do SAP HANA em hn1-db-0 falhará ao ser iniciado como secundário. Nesse caso, configure a instância do HANA como secundária, executando este comando:

<pre><code>
service pacemaker start
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 


# Switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

#### <a name="testing-a-migration"></a>Teste de uma migração

Você pode migrar o nó mestre SAP HANA executando o seguinte comando
<pre><code>
crm resource migrate msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
crm resource migrate g_ip_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-1</b>
</code></pre>

Se você definir AUTOMATED_REGISTER="false", esta sequência de comandos deverá migrar o nó mestre do SAP HANA e o grupo que contém o endereço IP virtual para hn1-db-1.
O recurso do SAP HANA em hn1-db-0 falhará em ser iniciado como secundário. Nesse caso, configure a instância do HANA como secundária, executando este comando:

<pre><code>
su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 
</code></pre>

A migração cria restrições de local que precisam ser excluídas novamente.

<pre><code>
crm configure edited

# Delete location constraints that are named like the following contraint. You should have two constraints, one for the SAP HANA resource and one for the IP address group.
location cli-prefer-g_ip_<b>HN1</b>_HDB<b>03</b> g_ip_<b>HN1</b>_HDB<b>03</b> role=Started inf: <b>hn1-db-1</b>
</code></pre>

Também é necessário limpar o estado do recurso de nó secundário

<pre><code>
# Switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HN1</b>_HDB<b>03</b> <b>hn1-db-0</b>
</code></pre>

## <a name="next-steps"></a>Próximas etapas
* [Planejamento e implementação de Máquinas Virtuais do Azure para SAP][planning-guide]
* [Implantação de Máquinas Virtuais do Azure para SAP][deployment-guide]
* [Implantação DBMS de Máquinas Virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer a alta disponibilidade e planejar a recuperação de desastre do SAP HANA no Azure (instâncias grandes), confira [Alta disponibilidade e recuperação de desastre do SAP HANA (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md). 
