---
title: Alta disponibilidade de Máquinas Virtuais do Azure para SAP NetWeaver no SUSE Linux Enterprise Server para aplicativos SAP | Microsoft Docs
description: Guia de alta disponibilidade do SAP NetWeaver no SUSE Linux Enterprise Server para aplicativos SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: sedusch
ms.openlocfilehash: 12efeba68f30aa8723acc32449ae05ffac4c1ac4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658750"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications"></a>Alta disponibilidade do SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server para aplicativos SAP

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
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Este artigo descreve como implantar as máquinas virtuais, configurar as máquinas virtuais, instalar a estrutura de cluster e instalar um sistema SAP NetWeaver 7.50 com alta disponibilidade.
Nas configurações de exemplo, comandos de instalação etc. O número da instância ASCS 00, o número da instância ERS 02 e a ID do sistema SAP NW1 são usados. Os nomes dos recursos (por exemplo, máquinas virtuais, redes virtuais) no exemplo assumem que você usou o [modelo convergido][template-converged] com a ID do sistema SAP NW1 para criar os recursos.

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
* [Cenário otimizado para desempenho da SR SAP HANA][suse-hana-ha-guide]  
  O guia contém todas as informações necessárias para configurar a Replicação do Sistema SAP HANA no local. Use este guia como uma linha de base.
* [Armazenamento de NFS altamente disponível com DRBD e Pacemaker][suse-drbd-guide] O guia contém todas as informações necessárias para configurar um servidor NFS altamente disponível. Use este guia como uma linha de base.


## <a name="overview"></a>Visão geral

Para obter alta disponibilidade, o SAP NetWeaver requer um servidor NFS. O servidor NFS está configurado em um cluster separado e pode ser usado por vários sistemas SAP.

![Visão geral da Alta Disponibilidade do SAP NetWeaver](./media/high-availability-guide-suse/img_001.png)

O servidor NFS, ASCS do SAP NetWeaver, SCS do SAP NetWeaver, ERS do SAP NetWeaver e o banco de dados SAP HANA usam um nome do host virtual e endereços IP virtuais. No Azure, um balanceador de carga é necessário para usar um endereço IP virtual. A lista a seguir mostra a configuração do balanceador de carga (A) SCS e ERS.

### <a name="ascs"></a>(A)SCS

* Configuração de front-end
  * Endereço IP 10.0.0.7
* Configuração de back-end
  * Conectado aos adaptadores de rede primários de todas as máquinas virtuais que devem ser parte do cluster (A)SCS/ERS
* Porta de Investigação
  * Porta 620**&lt;nr&gt;**
* Regras de balanceamento de carga
  * 32**&lt;nr&gt;** TCP
  * 36**&lt;nr&gt;** TCP
  * 39**&lt;nr&gt;** TCP
  * 81**&lt;nr&gt;** TCP
  * 5**&lt;nr&gt;** 13 TCP
  * 5**&lt;nr&gt;** 14 TCP
  * 5**&lt;nr&gt;** 16 TCP

### <a name="ers"></a>ERS

* Configuração de front-end
  * Endereço IP 10.0.0.8
* Configuração de back-end
  * Conectado aos adaptadores de rede primários de todas as máquinas virtuais que devem ser parte do cluster (A)SCS/ERS
* Porta de Investigação
  * Porta 621**&lt;nr&gt;**
* Regras de balanceamento de carga
  * 33**&lt;nr&gt;** TCP
  * 5**&lt;nr&gt;** 13 TCP
  * 5**&lt;nr&gt;** 14 TCP
  * 5**&lt;nr&gt;** 16 TCP

## <a name="setting-up-a-highly-available-nfs-server"></a>Configuração de um servidor NFS altamente disponível

O SAP NetWeaver requer um armazenamento compartilhado para o diretório de perfil e transporte. Leia [Alta disponibilidade para NFS em VMs do Azure no SUSE Linux Enterprise Server][nfs-ha] sobre como configurar um servidor NFS para SAP NetWeaver.

## <a name="setting-up-ascs"></a>Configuração do (A)SCS

Você pode usar um modelo do Azure do github para implantar todos os recursos do Azure necessários, incluindo as máquinas virtuais, o conjunto de disponibilidade e balanceador de carga ou você pode implantar os recursos manualmente.

### <a name="deploy-linux-via-azure-template"></a>Implantar o Linux por meio do Modelo do Azure

O Azure Marketplace contém uma imagem para SUSE Linux Enterprise Server for SAP Applications 12 que você pode usar para implantar novas máquinas virtuais. A imagem do Marketplace contém o agente de recurso para SAP NetWeaver.

Você pode usar um dos modelos de início rápido no github para implantar todos os recursos necessários. O modelo implanta as máquinas virtuais, o balanceador de carga, o conjunto de disponibilidade etc. Siga estas etapas para implantar o modelo:

1. Abra o [modelo multi-SID do ASCS/SCS][template-multisid-xscs] ou o [modelo convergido][template-converged] no Portal do Azure. O modelo de ASCS/SCS cria apenas as regras de balanceamento de carga para instâncias ERS (somente Linux) e ASCS/SCS do SAP NetWeaver, enquanto o modelo convergido também cria as regras de balanceamento de carga para um banco de dados (por exemplo, Microsoft SQL Server ou SAP HANA). Se você planeja instalar um sistema baseado no SAP NetWeaver e também quer instalar o banco de dados nos mesmos computadores, use o [modelo convergido][template-converged].
1. Defina os seguintes parâmetros
   1. Prefixo de recurso (somente modelo multi-SID do ASCS/SCS)  
      Digite o prefixo que você deseja usar. O valor é usado como um prefixo para os recursos que serão implantados.
   3. ID do sistema SAP (somente modelo convergido)  
      Insira a ID do sistema SAP do sistema SAP que você deseja instalar. A ID é usada como um prefixo para os recursos que serão implantados.
   4. Tipo de Pilha  
      Selecionar o tipo de pilha do SAP NetWeaver
   5. Tipo de sistema operacional  
      Selecione uma das distribuições do Linux. Para este exemplo, selecione SLES 12 BYOS
   6. Tipo de banco de dados  
      Selecionar HANA
   7. Tamanho do sistema SAP  
      A quantidade de SAPs que o novo sistema fornece. Se não tiver certeza de quantos SAPS o sistema precisará, pergunte ao Parceiro de Tecnologia SAP ou ao Integrador de Sistemas
   8. Disponibilidade do sistema  
      Selecione HA
   9. Nome de Usuário de Administrador e Senha do Administrador  
      É criado um novo usuário que pode ser usado para fazer logon no computador.
   10. ID da Sub-rede  
   A ID da sub-rede à qual as máquinas virtuais devem estar conectadas.  Deixe em branco se você deseja criar uma nova rede virtual ou selecione a mesma sub-rede usada ou criada como parte da implantação do servidor NFS. A ID geralmente tem esta aparência: /subscriptions/**&lt;ID da assinatura&gt;**/resourceGroups/**&lt;nome do grupo de recursos&gt;**/providers/Microsoft.Network/virtualNetworks/**&lt;nome de rede virtual&gt;**/subnets/**&lt;nome da sub-rede&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Implantar o Linux manualmente por meio do portal do Azure

Primeiro, você precisa criar as máquinas virtuais para este cluster NFS. Posteriormente, você pode cria um balanceador de carga e usar as máquinas virtuais nos pools de back-end.

1. Criar um grupo de recursos
1. Criar uma rede virtual
1. Crie um Conjunto de Disponibilidade  
   Defina o máximo de domínio de atualização
1. Crie a Máquina Virtual 1   
   Use pelo menos o SLES4SAP 12 SP1, neste exemplo, a imagem do SLES4SAP 12 SP1 https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES For SAP Applications 12 SP1 é usado  
   Selecione o Conjunto de Disponibilidade criado anteriormente  
1. Crie a Máquina Virtual 2   
   Use pelo menos o SLES4SAP 12 SP1, neste exemplo, a imagem do SLES4SAP 12 SP1 https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES For SAP Applications 12 SP1 é usado  
   Selecione o Conjunto de Disponibilidade criado anteriormente  
1. Adicione pelo menos um disco de dados para ambas as máquinas virtuais  
   Os discos de dados são usados para o diretório /usr/sap/`<SAPSID`>
1. Crie um Balanceador de Carga (interno)  
   1. Criar os endereços IP de front-end
      1. Endereço IP 10.0.0.7 para ASCS
         1. Abra o balanceador de carga, selecione o pool de IPs de front-end e clique em Adicionar
         1. Insira o nome do novo pool de IP de front-end (por exemplo, **nw1-ascs-frontend**)
         1. Defina a Atribuição como Estática e insira o endereço IP (por exemplo, **10.0.0.7**)
         1. Clique em OK
      1. Endereço IP 10.0.0.8 para ERS do ASCS
         * Repita as etapas acima para criar um endereço IP para ERS (por exemplo, **10.0.0.8** e **nw1-aers-backend**)
   1. Criar os pools de back-end
      1. Crie um pool de back-end para o ASCS
         1. Abra o balanceador de carga, selecione os pools de back-end e clique em Adicionar
         1. Insira o nome do novo pool de back-end (por exemplo, **nw1-ascs-backend**)
         1. Clique em Adicionar uma máquina virtual.
         1. Selecione o Conjunto de Disponibilidade criado anteriormente
         1. Selecione as máquinas virtuais do cluster (A)SCS
         1. Clique em OK
      1. Crie um pool de back-end para o ERS do ASCS
         * Repita as etapas acima para criar um pool de back-end para o ERS (por exemplo, **nw1-aers-backend**)
   1. Crie as investigações de integridade
      1. Porta 620**00** para ASCS
         1. Abra o balanceador de carga, selecione as investigações de integridade e clique em Adicionar
         1. Insira o nome da nova investigação de integridade (por exemplo, **nw1-ascs-hp**)
         1. Selecione TCP como protocolo, porta 620**00**, mantenha o Intervalo 5 e o limite Não Íntegro 2
         1. Clique em OK
      1. Porta 621**02** para ASCS ERS
         * Repita as etapas acima para criar uma investigação de integridade para ERS (por exemplo, 621**02** e **nw1-aers-hp**)
   1. Regras de balanceamento de carga
      1. 32**00** TCP para ASCS
         1. Clique no balanceador de carga, escolha as regras de balanceamento de carga e clique em Adicionar
         1. Insira o nome da nova regra do balanceador de carga (por exemplo, **nw1-lb-3200**)
         1. Selecione o endereço IP de front-end, o pool de back-end e a investigação de integridade criados anteriormente (por exemplo, **nw1-ascs-frontend**)
         1. Mantenha o protocolo **TCP**, insira a porta **3200**
         1. Aumente o tempo limite de ociosidade para 30 minutos
         1. **Habilite o IP Flutuante**
         1. Clique em OK    
      1. Portas adicionais para ASCS
         * Repita as etapas acima para as portas 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 e TCP para o ASCS
      1. Portas adicionais para ERS do ASCS
         * Repita as etapas acima para portas 33**02**, 5**02**13, 5**02**14, 5**02**16 e TCP para o ERS do ASCS

### <a name="create-pacemaker-cluster"></a>Criar cluster do Pacemaker

Siga as etapas em [Configurar Pacemaker no SUSE Linux Enterprise Server no Azure](high-availability-guide-suse-pacemaker.md) para criar um cluster básico do Pacemaker para esse servidor (A)SCS.

### <a name="installation"></a>Instalação

Os itens a seguir são prefixados com **[A]** – aplicável a todos os nós, **[1]** – aplicável somente ao nó 1 ou **[2]** – aplicável somente ao nó 2.

1. **[A]** Instalar o conector SUSE
   
   <pre><code>
   sudo zypper install sap_suse_cluster_connector
   </code></pre>

1. **[A]** Atualizar agentes de recurso SAP  
   
   Um patch para o pacote de agentes de recurso é necessário para usar a nova configuração, que é descrita neste artigo. Você pode verificar, se o patch já está instalado com o comando a seguir

   <pre><code>
   sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   A saída deverá ser semelhante a

   <pre><code>
   &lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Se o comando grep não localizar o parâmetro IS_ERS, você precisará instalar o patch listado na [página de download do SUSE](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)

   <pre><code>
   # example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

1. **[A]** Configurar a resolução de nome do host   

   Você pode usar um servidor DNS ou modificar /etc/hosts em todos os nós. Este exemplo mostra como usar o arquivo /etc/hosts.
   Substitua o endereço IP e o nome do host nos comandos a seguir

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   Insira as seguintes linhas para /etc/hosts. Altere o endereço IP e o nome do host para corresponder ao seu ambiente   
   
   <pre><code>
   # IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>Preparar para a instalação do SAP NetWeaver

1. **[A]** Criar os diretórios compartilhados

   <pre><code>
   sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>NW1</b>/SYS
   sudo mkdir -p /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>NW1</b>/ERS<b>02</b>

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>NW1</b>/SYS
   sudo chattr +i /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[A]**  Configurar o autofs
 
   <pre><code>
   sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Crie um arquivo com

   <pre><code>
   sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   /usr/sap/<b>NW1</b>/SYS -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sidsys
   /usr/sap/<b>NW1</b>/ASCS<b>00</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/ASCS
   /usr/sap/<b>NW1</b>/ERS<b>02</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/ASCSERS
   </code></pre>

   Reiniciar o autofs para montar os novos compartilhamentos

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]** Configurar arquivo de permuta

   <pre><code>
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Reiniciar o agente para ativar a alteração

   <pre><code>
   sudo service waagent restart
   </code></pre>


### <a name="installing-sap-netweaver-ascsers"></a>Instalação do ASCS/ERS do SAP NetWeaver

1. **[1]** Criar um recurso de IP virtual e uma investigação de integridade para a instância do ASCS

   <pre><code>
   sudo crm node standby <b>nw1-cl-1</b>
   
   sudo crm configure primitive vip_<b>NW1</b>_ASCS IPaddr2 \
     params ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ASCS anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 620<b>00</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW1</b>_ASCS nc_<b>NW1</b>_ASCS vip_<b>NW1</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Verifique se o status do cluster é ok e se todos os recursos estão iniciados. Não importa em qual nó os recursos estão sendo executados.

   <pre><code>
   sudo crm_mon -r
   
   # Node nw1-cl-1: standby
   # <b>Online: [ nw1-cl-0 ]</b>
   # 
   # Full list of resources:
   # 
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-0</b>
   # rsc_st_azure    (stonith:fence_azure_arm):      <b>Started nw1-cl-0</b>
   #  Resource Group: g-NW1_ASCS
   #      nc_NW1_ASCS        (ocf::heartbeat:anything):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   </code></pre>

1. **[1]** Instalar o ASCS do SAP NetWeaver  

   Instalar o ASCS do SAP NetWeaver como raiz no primeiro nó usando um nome do host virtual que mapeia para o endereço IP da configuração de front-end do balanceador de carga para o ASCS, por exemplo <b>nw1-ascs</b>, <b>10.0.0.7</b> e o número de instância utilizado para a investigação do balanceador de carga, por exemplo <b>00</b>.

   Você pode usar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um usuário não raiz se conecte ao sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Se a instalação não conseguir criar uma subpasta em /usr/sap/**NW1**/ASCS**00**, tente definir o proprietário e o grupo da pasta do ASCS**00** e tente novamente.

   <pre><code>
   chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]** Criar um recurso de IP virtual e uma investigação de integridade para a instância do ERS

   <pre><code>
   sudo crm node online <b>nw1-cl-1</b>
   sudo crm node standby <b>nw1-cl-0</b>
   
   sudo crm configure primitive vip_<b>NW1</b>_ERS IPaddr2 \
     params ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ERS anything \
    params binfile="/usr/bin/nc" cmdline_options="-l -k 621<b>02</b>" \
    op monitor timeout=20s interval=10 depth=0
   
   # WARNING: Resources nc_NW1_ASCS,nc_NW1_ERS violate uniqueness for parameter "binfile": "/usr/bin/nc"
   # Do you still want to commit (y/n)? y
   
   sudo crm configure group g-<b>NW1</b>_ERS nc_<b>NW1</b>_ERS vip_<b>NW1</b>_ERS
   </code></pre>
 
   Verifique se o status do cluster é ok e se todos os recursos estão iniciados. Não importa em qual nó os recursos estão sendo executados.

   <pre><code>
   sudo crm_mon -r
   
   # Node <b>nw1-cl-0: standby</b>
   # <b>Online: [ nw1-cl-1 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   # rsc_st_azure    (stonith:fence_azure_arm):      <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      nc_NW1_ASCS        (ocf::heartbeat:anything):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      nc_NW1_ERS (ocf::heartbeat:anything):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   </code></pre>

1. **[2]** Instalar o ERS do SAP NetWeaver  

   Instalar o ERS do SAP NetWeaver como raiz no segundo nó usando um nome do host virtual que mapeia para o endereço IP de configuração de front-end do balanceador de carga para o ERS, por exemplo, <b>nw1-aers</b>, <b>10.0.0.8</b> e o número de instância que você usou para a investigação do balanceador de carga, por exemplo, <b>02</b>.

   Você pode usar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um usuário não raiz se conecte ao sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   > [!NOTE]
   > Use SWPM SP 20 PL 05 ou superior. Versões anteriores não configurarão as permissões corretamente e a instalação falhará.

   Se a instalação falhar ao criar uma subpasta em /usr/sap/**NW1**/ERS**02**, tente definir o proprietário e o grupo da pasta do ERS**02** e tente novamente.

   <pre><code>
   chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>
   > 

1. **[1]** Adaptar os perfis de instância do ASCS/SCS e do ERS
 
   * Perfil do ASCS/SCS

   <pre><code> 
   sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * Perfil do ERS

   <pre><code> 
   sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   </code></pre>


1. **[A]** Configurar Keep Alive

   A comunicação entre o servidor de aplicativos do SAP NetWeaver e o ASCS/SCS é roteada por meio de um balanceador de carga de software. O balanceador de carga desconecta conexões inativas após um tempo limite configurável. Para evitar isso, você precisa definir um parâmetro no perfil do SAP NetWeaver ASCS/SCS e alterar as configurações do sistema Linux. Leia a [Nota SAP 1410736][1410736] para obter mais informações.
   
   O parâmetro enque/encni/set_so_keepalive do perfil do ASCS/SCS já foi adicionado na última etapa.

   <pre><code> 
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]** Configurar os usuários do SAP após a instalação
 
   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>nw1</b>adm   
   </code></pre>

1. **[1]** Adicionar os serviços de ERS SAP ASCS ao arquivo sapservice

   Adicione a entrada de serviço do ASCS ao segundo nó e copie a entrada de serviço de ERS para o primeiro nó.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>nw1-cl-1</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>nw1-cl-1</b> "cat /usr/sap/sapservices" | grep ERS<b>02</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

1. **[1]** Criar os recursos de cluster do SAP

   <pre><code>
   sudo crm configure property maintenance-mode="true"   
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure location loc_sap_<b>NW1</b>_failover_to_ers rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>NW1</b> eq 1
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   Verifique se o status do cluster é ok e se todos os recursos estão iniciados. Não importa em qual nó os recursos estão sendo executados.

   <pre><code>
   sudo crm_mon -r
   
   # Online: <b>[ nw1-cl-0 nw1-cl-1 ]</b>
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   # rsc_st_azure    (stonith:fence_azure_arm):      <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      nc_NW1_ASCS        (ocf::heartbeat:anything):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #      rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      nc_NW1_ERS (ocf::heartbeat:anything):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   #      rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-0</b>
   </code></pre>

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Preparação do servidor de aplicativos do SAP NetWeaver

Alguns bancos de dados exigem que a instalação da instância do banco de dados seja executada em um servidor de aplicativos. Prepare as máquinas virtuais do servidor de aplicativos para poder usá-las nesses casos.

As etapas abaixo pressupõem que você instale o servidor de aplicativos em um servidor diferente dos servidores do ASCS/SCS e do HANA. Caso contrário, algumas das etapas abaixo (como configurar a resolução de nome do host) não são necessárias.

1. Configurar a resolução de nome do host

   Você pode usar um servidor DNS ou modificar /etc/hosts em todos os nós. Este exemplo mostra como usar o arquivo /etc/hosts.
   Substitua o endereço IP e o nome do host nos comandos a seguir
   ```bash
   sudo vi /etc/hosts
   ```
   Insira as seguintes linhas para /etc/hosts. Altere o endereço IP e o nome do host para corresponder ao seu ambiente    
    
   <pre><code>
   # IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   # IP address of all application servers
   <b>10.0.0.20 nw1-di-0</b>
   <b>10.0.0.21 nw1-di-1</b>
   </code></pre>

1. Criar o diretório sapmnt

   <pre><code>
   sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Configurar o autofs
 
   <pre><code>
   sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Criar um novo arquivo com

   <pre><code>
   sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   </code></pre>

   Reiniciar o autofs para montar os novos compartilhamentos

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. Configurar arquivo de permuta
 
   <pre><code>
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Reiniciar o agente para ativar a alteração

   <pre><code>
   sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Instalar banco de dados

Neste exemplo, o SAP NetWeaver está instalado no SAP HANA. Você pode usar todos os bancos de dados com suporte para esta instalação. Para obter mais informações sobre como instalar o SAP HANA no Azure, consulte [Alta disponibilidade do SAP HANA em VMs (máquinas virtuais) do Azure][sap-hana-ha]. Para obter uma lista de bancos de dados com suporte, consulte [Nota SAP 1928533][1928533].

1. Executar a instalação da instância do banco de dados SAP

   Instale a instância de banco de dados do SAP NetWeaver como raiz usando um nome de host virtual que mapeia para o endereço IP de configuração de front-end do balanceador de carga para o banco de dados, por exemplo, <b>nw1-db</b> e <b>10.0.0.13</b>.

   Você pode usar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um usuário não raiz se conecte ao sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Instalação de servidor de aplicativos do SAP NetWeaver

Siga estas etapas para instalar um servidor de aplicativos SAP. 

1. Preparar o servidor de aplicativos

Siga as etapas no capítulo [Preparação do servidor de aplicativos SAP NetWeaver](high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) acima para preparar o servidor de aplicativos.

1. Instalar o servidor de aplicativos do SAP NetWeaver

   Instale um servidor de aplicativos do SAP NetWeaver primário ou adicional.

   Você pode usar o parâmetro sapinst SAPINST_REMOTE_ACCESS_USER para permitir que um usuário não raiz se conecte ao sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Atualizar o repositório seguro do SAP HANA

   Atualize o repositório seguro do SAP HANA para apontar para o nome virtual do programa de instalação da replicação de sistema do SAP HANA.

   Executar o comando a seguir para listar as entradas
   <pre><code>
   hdbuserstore List
   </code></pre>

   Isso deve listar todas as entradas e deve ser semelhante a
   <pre><code>
   DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: HN1
   </code></pre>

   A saída mostra que o endereço IP da entrada padrão está apontando para a máquina virtual e não para o endereço IP do balanceador de carga. Essa entrada precisa ser alterada para apontar para o nome do host virtual do balanceador de carga. Certifique-se de usar a mesma porta (**30313** na saída acima)!

   <pre><code>
   su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db</b>:<b>30313</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="next-steps"></a>Próximas etapas
* [Planejamento e implementação de Máquinas Virtuais do Azure para SAP][planning-guide]
* [Implantação de Máquinas Virtuais do Azure para SAP][deployment-guide]
* [Implantação DBMS de Máquinas Virtuais do Azure para SAP][dbms-guide]
* Para saber como estabelecer a alta disponibilidade e o plano de recuperação de desastres do SAP HANA no Azure (instâncias grandes), confira [Alta disponibilidade e recuperação de desastres do SAP HANA (instâncias grandes) no Azure](hana-overview-high-availability-disaster-recovery.md).
* Para saber como estabelecer a alta disponibilidade e o plano de recuperação de desastre do SAP HANA em VMs do Azure, confira [Alta disponibilidade do SAP HANA em VMs (Máquinas Virtuais) do Azure][sap-hana-ha]
