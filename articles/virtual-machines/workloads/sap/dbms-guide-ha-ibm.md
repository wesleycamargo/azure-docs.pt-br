---
title: Configurar o IBM Db2 HADR em máquinas virtuais (VMs) | Microsoft Docs
description: Estabeleça a alta disponibilidade do IBM Db2 LUW em máquinas virtuais (VMs).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/10/2019
ms.author: juergent
ms.openlocfilehash: a74dd1a932cac41081786f76938a5b35de62d878
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64689707"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105

[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm
[sles-pacemaker]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker
[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[nfs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Alta disponibilidade do IBM Db2 LUW em VMs do Azure no SUSE Linux Enterprise Server com o Pacemaker

IBM Db2 para Linux, UNIX e Windows (LUW) no [configuração de HADR (recuperação) de desastres e disponibilidade alta](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) consiste em um nó que executa uma instância de banco de dados primário e pelo menos um nó que executa uma instância de banco de dados secundário. Alterações para a instância de banco de dados primário são replicadas para uma instância de banco de dados secundário de forma síncrona ou assíncrona, dependendo da sua configuração. 

Este artigo descreve como implantar e configurar as máquinas virtuais do Azure (VMs), instale a estrutura de cluster e instale o IBM Db2 LUW com configuração HADR. 

O artigo não aborda como instalar e configurar o IBM Db2 LUW com a instalação de software SAP ou HADR. Para ajudá-lo a realizar essas tarefas, podemos fornecer referências aos manuais de instalação do SAP e IBM. Este artigo se concentra em partes que são específicas para o ambiente do Azure. 

As versões do IBM Db2 com suporte são 10.5 e posteriores, conforme documentado na nota do SAP [1928533].

Antes de iniciar uma instalação, consulte a documentação e o seguintes notas SAP:

| Nota do SAP | DESCRIÇÃO |
| --- | --- |
| [1928533] | Aplicativos SAP no Azure: produtos com suporte e tipos de VM do Azure |
| [2015553] | SAP no Azure: pré-requisitos de suporte |
| [2178632] | Principais métricas de monitoramento para SAP no Azure |
| [2191498] | SAP no Linux com o Azure: monitoramento aprimorado |
| [2243692] | Linux no Azure (IaaS) VM: Problemas de licença do SAP |
| [1984787] | SUSE Linux Enterprise Server 12: Notas de instalação |
| [1999351] | Solução de problemas de monitoramento aprimorado do Azure para SAP |
| [2233094] | DB6: Aplicativos SAP no Azure que usam o IBM Db2 para Linux, UNIX e Windows - informações adicionais |
| [1612105] | DB6: Perguntas Frequentes sobre o Db2 com HADR |


| Documentação | 
| --- |
| [Wiki da comunidade SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Tem todas as notas SAP para Linux |
| [Máquinas virtuais de planejamento e implementação para SAP no Linux do Azure] [ planning-guide] guia |
| [Implantação de máquinas virtuais do Azure para SAP no Linux] [ deployment-guide] (Este artigo) |
| [Implantação de system(DBMS) de gerenciamento para o SAP no Linux de banco de dados de máquinas virtuais do Azure] [ dbms-guide] guia |
| [Carga de trabalho do SAP na lista de verificação de planejamento e implantação do Azure][azr-sap-plancheck] |
| [Guias de melhores práticas do SUSE Linux Enterprise Server for SAP Applications 12 SP3][sles-for-sap-bp] |
| [SUSE Linux Enterprise alta disponibilidade extensão 12 SP3][sles-ha-guide] |
| [Implantação de DBMS de máquinas virtuais do IBM Db2 do Azure para carga de trabalho do SAP][dbms-db2] |
| [IBM Db2 HADR 11.1][db2-hadr-11.1] |
| [IBM Db2 HADR R 10.5][db2-hadr-10.5] |

## <a name="overview"></a>Visão geral
Para obter alta disponibilidade, IBM Db2 LUW com HADR é instalado em pelo menos duas máquinas virtuais do Azure, que são implantadas em um [conjunto de disponibilidade do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) ou de vários [zonas de disponibilidade do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones). 

Os gráficos a seguir exibem uma instalação do servidor de banco de dados de duas VMs do Azure. Tanto o servidor de banco de dados as VMs do Azure têm seu próprio armazenamento anexado e estão em execução. No HADR, uma instância do banco de dados em uma das VMs do Azure tem a função da instância primária. Todos os clientes estão conectados a esta instância primária. Todas as alterações em transações de banco de dados mantidas localmente no log de transações do Db2. Conforme os registros de log de transação são mantidos localmente, os registros são transferidos por meio de TCP/IP para a instância de banco de dados no segundo servidor de banco de dados, o servidor em espera ou instância em espera. A instância em espera atualiza o banco de dados local, roll forward da transação transferida registros de log. Dessa forma, o servidor em espera é mantido em sincronizado com o servidor primário.

HADR é apenas uma funcionalidade de replicação. Ele possui nenhuma detecção de falhas e nenhum recurso de tomada de controle ou o failover automático. Uma tomada de controle ou a transferência para o servidor em espera deve ser iniciada manualmente por um administrador de banco de dados. Para obter uma tomada de controle automático e detecção de falha, você pode usar o recurso de cluster do Linux Pacemaker. Pacemaker monitora as instâncias de servidor de dois bancos de dados. Quando a instância do servidor de banco de dados primário falhar, inicia Pacemaker uma *automática* tomada de controle HADR pelo servidor em espera. Pacemaker também garante que o endereço IP virtual é atribuído ao novo servidor primário.

![Visão geral de alta disponibilidade do IBM Db2](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Para ter SAP servidores de aplicativo se conectar ao banco de dados primário, você precisa de um nome de host virtual e um endereço IP virtual. No caso de um failover, os servidores de aplicativos SAP se conectará à nova instância de banco de dados primário. Em um ambiente do Azure, uma [balanceador de carga do Azure](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) é necessário para usar um endereço IP virtual da maneira que é necessária para HADR do IBM Db2. 

Para ajudar você a compreender totalmente como IBM Db2 LUW com HADR e o Pacemaker se adapta a uma configuração do sistema SAP altamente disponível, a imagem a seguir apresenta uma visão geral de uma configuração altamente disponível de um sistema SAP com base no banco de dados IBM Db2. Este artigo aborda somente o IBM Db2, mas ele fornece referências a outros artigos sobre como configurar outros componentes de um sistema SAP.

![Visão geral de todo o ambiente de alta disponibilidade do IBM DB2](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>Visão geral de alto nível das etapas necessárias
Para implantar uma configuração do IBM Db2, você precisa seguir estas etapas:

  + Planeje seu ambiente.
  + Implante as VMs.
  + Atualizar o SUSE Linux e configurar sistemas de arquivos.
  + Instalar e configurar o Pacemaker.
  + Instale [NFS altamente disponível][nfs-ha].
  + Instale [ASCS/ERS em um cluster separado][ascs-ha].
  + Instale o banco de dados do IBM Db2 com a opção Distributed/alta disponibilidade (SWPM).
  + Instalar e criar um nó de banco de dados secundário e a instância e configurar HADR.
  + Confirme se o HADR está funcionando.
  + Aplica a configuração do Pacemaker ao controle do IBM Db2.
  + Configure balanceador de carga do Azure.
  + Instalação principal e os servidores de aplicativos de caixa de diálogo.
  + Verifique e adaptar-se a configuração de servidores de aplicativos SAP.
  + Execute failover e testes de tomada de controle.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Planejar a infraestrutura do Azure para hospedar o IBM Db2 LUW com HADR

Conclua o processo de planejamento antes de executar a implantação. Planejamento cria a base para a implantação de uma configuração do Db2 com HADR no Azure. Elementos principais que precisam fazer parte do planejamento de IMB Db2 LUW (parte do banco de dados do ambiente do SAP) são listados na tabela a seguir:

| Tópico | Descrição breve |
| --- | --- |
| Definir grupos de recursos do Azure | Grupos de recursos em que você implantar a VM, rede virtual, balanceador de carga do Azure e outros recursos. Pode ser novo ou existente. |
| Rede virtual / definição de sub-rede | Em que as VMs para IBM Db2 e o balanceador de carga do Azure estão sendo implantadas. Pode ser existente ou recém-criado. |
| Máquinas virtuais que hospedam LUW do IBM Db2 | Tamanho da VM, armazenamento, rede, endereço IP. |
| Nome de host virtual e o IP virtual para o banco de dados IBM Db2| O virtual IP ou nome do host que é usado para conexão de servidores de aplicativos SAP. **db-virt-hostname**, **db-virt-ip**. |
| Isolamento do Azure | Isolamento do Azure ou o isolamento do SBD (altamente recomendado). Método para evitar situações de dupla personalidade de divisão é evitado. |
| VM DE SBD | Tamanho da máquina virtual SBD, armazenamento, rede. |
| Azure Load Balancer | Uso de Basic ou Standard (recomendado), a porta para o banco de dados do Db2 (nossa recomendação 62500) de investigação **porta de investigação**. |
| Resolução de nomes| Como funciona a resolução de nome no ambiente. Serviço DNS é altamente recomendável. Arquivo de hosts local pode ser usado. |
    
Para obter mais informações sobre o Linux Pacemaker no Azure, consulte [Configurando o Pacemaker no SUSE Linux Enterprise Server no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker).

## <a name="deployment-on-suse-linux"></a>Implantação no SUSE Linux

O agente de recursos para IBM Db2 LUW está incluído no SUSE Linux Enterprise Server para aplicativos SAP. Para a configuração descrita neste documento, você deve usar o servidor do SUSE Linux para aplicativos SAP. O Azure Marketplace contém uma imagem para SUSE Enterprise Server for SAP Applications 12 que você pode usar para implantar novas máquinas virtuais do Azure. Lembre-se dos vários modelos de suporte ou serviço que são oferecidos pelo SUSE no Azure Marketplace quando você escolhe uma imagem de VM no Marketplace de VM do Azure. 

### <a name="hosts-dns-updates"></a>Hosts: Atualizações DNS
Faça uma lista de todos os nomes de host, incluindo nomes de host virtual e atualizar seus servidores DNS para habilitar o endereço IP apropriado para a resolução de nome de host. Se um servidor DNS não existe ou você não pode atualizar e crie entradas DNS, você precisa usar os arquivos de host local das VMs individuais que estão participando nesse cenário. Se você estiver usando entradas de arquivos de host, certifique-se de que as entradas sejam aplicadas a todas as VMs no ambiente do sistema SAP. No entanto, recomendamos que você use seu DNS que o ideal é que se estende para o Azure


### <a name="manual-deployment"></a>Implantação manual

Certifique-se de que o sistema operacional selecionado é suportado pela IBM/SAP LUW do IBM Db2. A lista de versões de sistema operacional com suporte para versões do Db2 e VMs do Azure está disponível na nota do SAP [1928533]. A lista de versões do sistema operacional por versão do Db2 individual está disponível na matriz de disponibilidade de produto SAP. É altamente recomendável que um mínimo de SLES 12 SP3 por causa das melhorias de desempenho relacionados ao Azure desta ou posterior versões do SUSE Linux.

1. Crie ou selecione um grupo de recursos.
1. Crie ou selecione uma rede virtual e sub-rede.
1. Criar um conjunto de disponibilidade do Azure ou implantar uma zona de disponibilidade.
    + Para o conjunto de disponibilidade, defina os domínios de atualização máximo como 2.
1. Crie a máquina Virtual 1.
    + Use o SLES para imagens SAP no Azure Marketplace.
    + Selecione o conjunto de disponibilidade do Azure que você criou na etapa 3, ou selecione a zona de disponibilidade.
1.  Crie a máquina Virtual 2.
    + Use o SLES para imagens SAP no Azure Marketplace.
    + Selecione o conjunto de disponibilidade do Azure no criado na etapa 3, ou selecione a zona de disponibilidade (não a mesma zona como na etapa 3).
1. Adicionar discos de dados para as máquinas virtuais e, em seguida, verifique a recomendação de uma configuração de sistema de arquivos no artigo [implantação de DBMS de máquinas virtuais do IBM Db2 do Azure para carga de trabalho do SAP][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Criar o cluster do Pacemaker
    
Para criar um cluster Pacemaker básico para esse servidor IBM Db2, consulte [Configurando o Pacemaker no SUSE Linux Enterprise Server no Azure][sles-pacemaker]. 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>Instalar o ambiente de SAP e IBM Db2 LUW

Antes de iniciar a instalação de um ambiente do SAP com base em IBM Db2 LUW, examine a documentação a seguir:

+ Documentação do Azure
+ Documentação do SAP
+ Documentação do IBM

Links para essa documentação são fornecidos na seção de introdução deste artigo.

Consulte os manuais de instalação do SAP sobre a instalação de aplicativos com base no NetWeaver no IBM Db2 LUW.

Você pode encontrar os guias no portal do SAP para usando o [localizador de guia de instalação do SAP][sap-instfind].

Você pode reduzir o número de guias exibidas no portal, definindo os seguintes filtros:

- Eu quero: "Instalar um novo sistema"
- Meu banco de dados: "IBM Db2 para Linux, Unix e Windows"
- Filtros adicionais para as versões do SAP NetWeaver, configuração da pilha ou sistema operacional

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Dicas de instalação para configurar o IBM Db2 LUW com HADR

Para configurar a instância de banco de dados IBM Db2 LUW primária:

- Use a opção distribuída ou alta disponibilidade.
- Instale a instância ASCS/ERS do SAP e o banco de dados.
- Faça um backup do banco de dados recentemente instalado.


> [!IMPORTANT] 
> Anote a "porta de comunicação de banco de dados" que é definida durante a instalação. Ele deve ser o mesmo número de porta para ambas as instâncias de banco de dados

Para configurar o servidor de banco de dados em espera usando o procedimento de cópia homogênea do sistema SAP, execute estas etapas:

1. Selecione o **cópia do sistema** opção > **sistemas de destino** > **Distributed** > **instância de banco de dados**.
1. Como um método de cópia, selecione **homogênea de sistema** para que você pode usar o backup para restaurar um backup na instância do servidor em espera.
1. Quando você chegar à etapa de saída para restaurar o banco de dados para cópia homogênea de sistema, sair do instalador. Restaure o banco de dados de um backup do host primário. Todas as fases subsequentes de instalação já foram executadas no servidor de banco de dados primário.
1. Configure HADR para IBM Db2.

   > [!NOTE]
   > Para instalação e configuração é específica do Azure e o Pacemaker: Durante o procedimento de instalação por meio do SAP Software Provisioning Manager, há uma questão explícita sobre a alta disponibilidade para LUW do IBM Db2:
   >+ Não marque **IBM Db2 pureScale**.
   >+ Não marque **instalar o IBM Tivoli sistema de automação para Multiplatforms**.
   >+ Não marque **gerar arquivos de configuração de cluster**.

   Quando você usa um dispositivo SBD para Linux Pacemaker, defina os seguintes parâmetros de HADR do Db2:
   + Duração da janela de ponto a ponto HADR (segundos) (HADR_PEER_WINDOW) = 300  
   + O valor de tempo limite HADR (HADR_TIMEOUT) = 60

   Quando você usa um agente de isolamento do Pacemaker do Azure, defina os seguintes parâmetros:
   + Duração da janela de ponto a ponto HADR (segundos) (HADR_PEER_WINDOW) = 900  
   + O valor de tempo limite HADR (HADR_TIMEOUT) = 60

Recomendamos que os parâmetros anteriores, com base nos testes de tomada de controle/failover inicial. É obrigatório que você teste para a funcionalidade correta do failover e a tomada de controle com essas configurações de parâmetro. Como as configurações individuais podem variar, os parâmetros podem exigir ajuste. 

> [!IMPORTANT]
> Específico ao IBM Db2 com configuração de HADR com a inicialização normal: A instância de banco de dados secundário ou em espera deve estar em execução antes de iniciar a instância de banco de dados primário.

Para fins de demonstração e os procedimentos descritos neste artigo, o banco de dados SID é **PTR**.

#### <a name="ibm-db2-hadr-check"></a>Seleção do IBM Db2 HADR
Depois que você configurou o HADR e o status é par e o conectado em nós primários e em espera, execute a verificação a seguir:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
# Database Member 0 -- Database PTR -- Active -- Up 1 days 01:51:38 -- Date 2019-02-06-15.35.28.505451
# 
#                             <b>HADR_ROLE = PRIMARY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 1
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.170561 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6137
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 13
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000025
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223713
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 374400
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#        STANDBY_RECV_REPLAY_GAP(bytes) = 0
#                      PRIMARY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#                      STANDBY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:40:25.000000 (1549467625)
#              READS_ON_STANDBY_ENABLED = N

#Secondary output:
# Database Member 0 -- Database PTR -- Standby -- Up 1 days 01:46:43 -- Date 2019-02-06-15.38.25.644168
# 
#                             <b>HADR_ROLE = STANDBY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 0
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.205067 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6186
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 5
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000023
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223725
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 372480
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#        STANDBY_RECV_REPLAY_GAP(bytes) = 155
#                      PRIMARY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#                      STANDBY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:43:19.000000 (1549467799)
#              READS_ON_STANDBY_ENABLED = N
</code></pre>



## <a name="db2-pacemaker-configuration"></a>Configuração do Pacemaker do Db2

Quando você usa o Pacemaker para failover automático no caso de falha de nó, você precisa configurar suas instâncias de Db2 e o Pacemaker adequadamente. Esta seção descreve esse tipo de configuração.

Os itens a seguir são prefixados com:

- **[A]**: Aplicável a todos os nós
- **[1]**: Aplicável somente ao nó 1 
- **[2]**: Aplicável somente ao nó 2

**[A]**  Pré-requisitos para configuração do Pacemaker:
1. Desligar de ambos os servidores de banco de dados com o usuário db2\<sid > com db2stop.
1. Alterar o ambiente de shell para db2\<sid > usuário *ksh/bin/*. É recomendável que você use a ferramenta Yast. 


### <a name="pacemaker-configuration"></a>Configuração do pacemaker

**[1]**  Configuração do Pacemaker de HADR específicas do IBM Db2:
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]**  Recursos criar IBM Db2:
<pre><code># Replace **bold strings** with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virtual IP - same as Azure Load Balancer IP
sudo crm configure primitive rsc_ip_db2ptr_<b>PTR</b> IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="<b>10.100.0.10</b>"

# Configure probe port for Azure load Balancer
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> anything \
        params binfile="/usr/bin/nc" cmdline_options="-l -k <b>62500</b>" \
        op monitor timeout="20s" interval="10" depth="0"

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]**  Recursos iniciar IBM Db2:
* Coloque o Pacemaker do modo de manutenção.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]**  Certifique-se de que o status do cluster é Okey e que todos os recursos sejam iniciados. Não é importante qual nó os recursos estão em execução no.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 nós configurados
# <a name="5-resources-configured"></a>5 recursos configurados

# <a name="online--azibmdb01-azibmdb02-"></a>Online: [ azibmdb01 azibmdb02 ]

# <a name="full-list-of-resources"></a>Lista completa de recursos:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-sbd    (stonith:external/sbd): Azibmdb02 iniciado
#  <a name="resource-group-gipdb2ptrptr"></a>Grupo de recursos: g_ip_db2ptr_PTR
#      <a name="rscipdb2ptrptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Azibmdb02 iniciado
#      <a name="rscncdb2ptrptr--ocfheartbeatanything------started-azibmdb02"></a>rsc_nc_db2ptr_PTR (ocf::heartbeat: nada):      Azibmdb02 iniciado
#  <a name="masterslave-set-msldb2db2ptrptr-rscdb2db2ptrptr"></a>Conjunto de mestre/escravo: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Mestres: [azibmdb02]
#      <a name="slaves--azibmdb01-"></a>Servidores subordinados: [azibmdb01]
</pre>

> [!IMPORTANT]
> Você deve gerenciar o Pacemaker instância clusterizada do Db2 usando as ferramentas do Pacemaker. Se você usar comandos do db2 como db2stop, Pacemaker detecta a ação como uma falha de recurso. Se você estiver fazendo a manutenção, você pode colocar os nós ou recursos no modo de manutenção. Pacemaker suspende o monitoramento de recursos e, em seguida, você pode usar comandos de administração do db2 normal.


### <a name="configure-azure-load-balancer"></a>Configurar o Azure Load Balancer
Para configurar o balanceador de carga do Azure, recomendamos que você use o [SKU do Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) e, em seguida, faça o seguinte:

1. Crie um pool IP de front-end:

    a. No portal do Azure, abra o balanceador de carga do Azure, selecione **pool IP de front-end**e, em seguida, selecione **Add**.

   b. Insira o nome do novo pool de IPS de front-end (por exemplo, **conexão Db2**).

   c. Defina as **atribuição** para **estático**e insira o endereço IP **IP Virtual** definido no início.

   d. Selecione **OK**.

   e. Depois que o novo pool de IP de front-end for criado, anote o endereço IP do pool.

1. Crie um pool de back-end:

    a. No portal do Azure, abra o balanceador de carga do Azure, selecione **pools de back-end**e, em seguida, selecione **Add**.

   b. Insira o nome do novo pool de back-end (por exemplo, **back-end do Db2**).

   c. Selecione **Adicionar uma máquina virtual**.

   d. Selecione o conjunto de disponibilidade ou as máquinas virtuais que hospeda o banco de dados do IBM Db2 criado na etapa anterior.

   e. Selecione as máquinas virtuais do cluster IBM Db2.

   f. Selecione **OK**.

1. Crie uma investigação de integridade:

    a. No portal do Azure, abra o balanceador de carga do Azure, selecione **investigações de integridade**e selecione **Add**.

   b. Insira o nome da nova investigação de integridade (por exemplo, **Db2-hp**).

   c. Selecione **TCP** como o protocolo e porta **62500**. Manter o **intervalo** valor definido como **5**e mantenha o **limite não íntegro** valor definido como **2**.

   d. Selecione **OK**.

1. Crie as regras de balanceamento de carga:

    a. No portal do Azure, abra o balanceador de carga do Azure, selecione **regras de balanceamento de carga**e, em seguida, selecione **Add**.

   b. Insira o nome da nova regra de Balanceador de carga (por exemplo, **Db2 SID**).

   c. Selecione o endereço IP de front-end, o pool de back-end e a investigação de integridade que você criou anteriormente (por exemplo, **front-end de Db2**).

   d. Manter o **protocolo** definido como **TCP**e insira a porta *porta de comunicação de banco de dados*.

   e. Aumente o **tempo limite de ociosidade** para 30 minutos.

   f. Certifique-se de **habilitar IP Flutuante**.

   g. Selecione **OK**.


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Fazer alterações em perfis SAP para usar o IP virtual para conexão
Para conectar-se à instância primária da configuração HADR, o SAP camada de aplicativo precisa usar o endereço IP virtual que você definiu e configurado para o balanceador de carga do Azure. As seguintes alterações são necessárias:

/sapmnt/\<SID >/perfil/padrão. PFL
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Instalação principal e os servidores de aplicativos de caixa de diálogo

Quando você instala o primário e servidores de aplicativos de caixa de diálogo em relação a uma configuração HADR do Db2, use o host virtual nome que você escolheu para a configuração. 

Se você tiver executado a instalação antes de você criar a configuração de Db2 HADR, faça as alterações conforme descrito na seção anterior e para as pilhas de Java do SAP, da seguinte maneira.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>Seleção de sistemas de pilha ABAP + Java ou Java URL JDBC

Use a ferramenta de configuração do J2EE para verificar ou atualizar a URL do JDBC. Como a ferramenta de configuração do J2EE é uma ferramenta gráfica, você precisa ter o X server instalado:
 
1. Entrar para o servidor de aplicativos principal da instância do J2EE e execute:
     <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>
1. No quadro esquerdo, escolha **repositório de segurança**.
1. No quadro à direita, escolha o chave/pool de jdbc / \<SAPSID>/url.
1. Altere o nome do host na URL do JDBC para o nome de host virtual.
     <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>
1. Selecione **adicionar**.
1. Para salvar suas alterações, selecione o ícone de disco no canto superior esquerdo.
1. Feche a ferramenta de configuração.
1. Reinicie a instância de Java.

## <a name="configure-log-archiving-for-hadr-setup"></a>Configurar o arquivamento de log para a instalação do HADR
Para configurar o log de Db2 para a instalação do HADR de arquivamento, é recomendável que você configure o primário e o banco de dados em espera ter a capacidade de recuperação de log automático de todos os locais de arquivo de log. O primário e de espera banco de dados deve ser capaz de recuperar arquivos de log de todos os locais de arquivo de log na qual qualquer um do banco de dados instâncias podem arquivar os arquivos de log. 

O arquivamento de log é executado somente pelo banco de dados primário. Se você alterar as funções HADR dos servidores de banco de dados ou se ocorrer uma falha, o novo banco de dados primário é responsável para arquivamento de log. Se você tiver configurado vários locais de arquivo de log, seus logs podem ser arquivados duas vezes. No caso de uma atualização local ou remota, talvez também precise copiar manualmente os logs arquivados do servidor principal antigo para o local do log ativo do novo servidor primário.

Recomendamos configurar um compartilhamento de NFS comuns em que os logs são gravados nos dois nós. O compartilhamento NFS precisa estar altamente disponível. 

Você pode usar o existentes compartilhamentos NFS altamente disponíveis para os transportes ou um diretório de perfil. Para obter mais informações, consulte:

- [Alta disponibilidade do NFS em VMs do Azure no SUSE Linux Enterprise Server][nfs-ha] 
- [Alta disponibilidade do SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server com arquivos do Azure NetApp para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- [Os arquivos do Azure do NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (para criar compartilhamentos NFS)


## <a name="test-the-cluster-setup"></a>Testar a configuração do cluster

Esta seção descreve como você pode testar a instalação do HADR do Db2. *Cada teste supõe que você efetuou logon como usuário raiz* e o IBM Db2 primário está em execução o *azibmdb01* máquina virtual.

O status inicial para todos os casos de teste é explicado aqui: (crm_mon - r ou crm status)

- **status de CRM** é um instantâneo do status do Pacemaker em tempo de execução 
- **crm_mon - r** contínua de saída do status do Pacemaker

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

O status original em um sistema SAP está documentado no DBACOCKPIT da transação > Configuração > Visão geral, conforme mostrado na imagem a seguir:

![DBACockpit - anterior à migração](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Tomada de controle de teste do IBM Db2


> [!IMPORTANT] 
> Antes de iniciar o teste, verifique se:
> * Pacemaker não tem quaisquer ações com falha (status do crm).
> * Não há nenhuma restrição de local (sobras do teste de migração)
> * A sincronização do IBM Db2 HADR está funcionando. Entre em contato com o usuário db2\<sid > <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migre o nó que está executando o banco de dados primário do Db2, executando o seguinte comando:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

Após a migração, a saída de status de crm é semelhante:
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

O status original em um sistema SAP está documentado no DBACOCKPIT da transação > Configuração > Visão geral, conforme mostrado na imagem a seguir:

![DBACockpit - após a migração](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

Recursos de migração com "migrar recursos de crm" cria restrições de local. Restrições de local devem ser excluídas. Se restrições de local não são excluídas, o recurso não é possível realizar o failback ou você pode enfrentar aquisições indesejadas. 

Migrar o recurso de volta para *azibmdb01* e desmarque as restrições de local
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **migrar recursos de CRM \<res_name > <host>:** Cria restrições de local e pode causar problemas com a tomada de controle
- **recursos de CRM claro \<res_name >**: Limpa as restrições de local
- **Limpeza de recursos do CRM \<res_name >**: Limpa todos os erros do recurso

### <a name="test-the-fencing-agent"></a>O agente de isolamento de teste

Nesse caso, podemos testar o isolamento do SBD, recomendamos que você faça quando você usa o SUSE Linux.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

Nó de cluster *azibmdb01* deve ser reinicializado. Vai ser movidos para a função do IBM Db2 primária HADR *azibmdb02*. Quando *azibmdb01* é voltam a ficar online, o Db2 instância está prestes a mover a função de uma instância de banco de dados secundário. 

Se o serviço Pacemaker não iniciar automaticamente no primeiro reinicializado primário, certifique-se de iniciá-lo manualmente com:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Testar uma tomada de controle manual

Você pode testar uma tomada de controle manual interrompendo o serviço Pacemaker no *azibmdb01* nó:
<pre><code>service pacemaker stop</code></pre>

status em *azibmdb02*
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

Após o failover, você pode iniciar o serviço novamente no *azibmdb01*.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Elimine o processo do Db2 no nó que executa o banco de dados primário de HADR

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

A instância do Db2 está prestes a falhar e o Pacemaker relatará o status a seguir:

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

Pacemaker reiniciará a instância de banco de dados primário do Db2 no mesmo nó, ou ele realizará failover para o nó que está executando a instância de banco de dados secundário e um erro será relatado.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Elimine o processo do Db2 no nó que executa a instância de banco de dados secundário

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

O nó obtém em falha indicado e erro relatado
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

A instância do Db2 for reiniciada na função secundária que tivesse atribuída antes.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Parar o banco de dados por meio de força db2stop no nó que executa a instância de banco de dados primário de HADR

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

Como usuário db2\<sid > Executar força de db2stop de comando:
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

Falha detectada
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

A instância de banco de dados secundário HADR Db2 foi promovida para a função primária
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Falhas de VM com a reinicialização do nó que executa a instância de banco de dados primário de HADR

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Pacemaker promoverá a instância secundária para a função da instância primária. A instância primária antiga moverá para a função secundária após a VM e todos os serviços sejam totalmente restaurados após a reinicialização de VM:

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>A VM que executa a instância de banco de dados primário de HADR com "halt" de falhas

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Nesse caso, o Pacemaker detectará que o nó que está executando a instância de banco de dados primário não está respondendo.

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

a próxima etapa é verificar se há um *divisão de dupla personalidade* situação. Depois que o nó sobrevivente determinou que o nó que é executado pela última vez a instância de banco de dados primário está inativo, um failover de recursos é executado.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


No caso de um "parada" do nó, o nó com falha deve ser reiniciado por meio de ferramentas de gerenciamento do Azure (no portal do Azure, PowerShell ou a CLI do Azure). Depois que o nó com falha estiver online novamente, ele inicia a instância do Db2 em função secundária.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:anything):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>Próximas etapas
- [Arquitetura de alta disponibilidade e cenários para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Configurando o Pacemaker no SUSE Linux Enterprise Server no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

     

