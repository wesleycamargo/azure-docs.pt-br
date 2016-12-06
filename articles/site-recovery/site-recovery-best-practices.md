---
title: "Preparar-se para a implantação de Recuperação de Site | Microsoft Docs"
description: "Este artigo descreve como se preparar para implantar a replicação com o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/05/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 5614c39d914d5ae6fde2de9c0d9941e7b93fc10f
ms.openlocfilehash: 8a4d265694e5eef438b0560a42ea5a95c04f9b02


---
# <a name="prepare-for-azure-site-recovery-deployment"></a>Preparar-se para a implantação do Azure Site Recovery
Leia este artigo para obter uma visão de alto nível dos requisitos de implantação para cada cenário de replicação com suporte do serviço Azure Site Recovery. Depois de ler os requisitos gerais de cada de cenário, você poderá vincular os detalhes específicos da implantação para cada implantação.

Depois de ler este artigo, publique quaisquer comentários ou perguntas ao final dele ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Visão geral
As organizações precisam de uma estratégia de BCDR que determine como os aplicativos, as cargas de trabalho e os dados permanecerão em execução e disponíveis durante o tempo de inatividade planejado e não planejado, e como recuperarão as condições normais de trabalho assim que possível. Sua estratégia de BCDR devem manter os dados comerciais seguros e passíveis de recuperação e garantir que as cargas de trabalho permaneçam continuamente disponíveis mediante um desastre.

A Recuperação de Site é um serviço do Azure que colabora com sua estratégia de BCDR por meio da coordenação da replicação de servidores físicos e máquinas virtuais locais na nuvem (Azure) ou em um datacenter secundário. Quando ocorrem paralisações em seu local primário, você realiza o failover em um local secundário a fim de manter os aplicativos e cargas de trabalho disponíveis. Quando o local primário retoma as operações normais, você realiza o failback. Saiba mais em [O que é a Recuperação de Site?](site-recovery-overview.md)

## <a name="select-your-deployment-model"></a>Selecione o modelo de implantação
O Azure tem dois [modelos de implantação](../resource-manager-deployment-model.md) diferentes para criar e trabalhar com recursos: o modelo Azure Resource Manager e o modelo de gerenciamento de serviços clássico. O Azure também tem dois portais: o [portal clássico do Azure](https://manage.windowsazure.com/), que dá suporte ao modelo de implantação clássico, e o [Portal do Azure](https://ms.portal.azure.com/), com suporte aos dois modelos de implantação.

A Recuperação de Site está disponível no portal clássico e no portal do Azure. No portal clássico do Azure, você pode dar suporte à Recuperação de Site com o modelo de gerenciamento de serviços clássico. No portal do Azure, você pode suportar o modelo clássico ou as implantações do Gerenciador de Recursos. [Leia mais](site-recovery-overview.md#site-recovery-in-the-azure-portal) sobre a implantação com o Portal do Azure.

Ao escolher um modelo de implantação, observe que:

* Recomendamos usar o [Portal do Azure](https://portal.azure.com) e o modelo do Resource Manager sempre que possível.
* A Recuperação de Site fornece uma experiência de introdução mais simples e intuitiva no Portal do Azure.
* Usando o Portal do Azure, é possível replicar computadores de ambos os armazenamentos clássico e do Resource Manager no Azure. Além disso, no Portal do Azure, você pode usar contas de armazenamento LRS ou GRS.
* O Portal do Azure combina os serviços de Recuperação de Site e de Backup em um único cofre dos Serviços de Recuperação para que você possa configurar e gerenciar os serviços BCDR de uma única localização.
* Agora, os usuários com assinaturas do Azure provisionadas pelo programa CSP (Provedor de Soluções na Nuvem) podem gerenciar as operações de Recuperação de Site no portal do Azure.
* Replicar VMs ou computadores físicos VMware para o Azure no Portal do Azure oferece diversos novos recursos, incluindo o suporte para armazenamento premium e a capacidade de excluir discos específicos da replicação.

## <a name="select-your-deployment-scenario"></a>Selecione o cenário de implantação
| **Implantação** | **Detalhes** | **Portal do Azure** | **Portal clássico** | **PowerShell** |
| --- | --- | --- | --- | --- |
| **VMs VMware para o Azure** |Replicar VMs do VMware para o armazenamento do Azure |No Portal do Azure, as VMs podem fazer failover para o armazenamento clássico ou do Resource Manager<br/><br/> A implantação no [Portal do Azure](site-recovery-vmware-to-azure.md) fornece uma experiência de implantação simplificada e uma série de benefícios de recursos. |No portal clássico do Azure, você pode implantar com o [modelo aprimorado](site-recovery-vmware-to-azure-classic.md) e fazer failover para o armazenamento clássico do Azure.<br/><br/>  Há também um modelo herdado que não deve ser usado para novas implantações. |Atualmente não há suporte para o PowerShell. |
| **VMs Hyper-V para o Azure** |VMs Hyper-V para o armazenamento do Azure. As VMs podem estar em hosts Hyper-V gerenciados em nuvens VMM do System Center ou sem o VMM. |No Portal do Azure, as VMs podem realizar failover no armazenamento clássico ou do Resource Manager.<br/><br/>  O Portal do Azure fornece uma experiência de implantação simplificada. [Saiba mais](site-recovery-vmm-to-azure.md) sobre a replicação de VMs Hyper-V em nuvens VMM. [Saiba mais](site-recovery-hyper-v-site-to-azure.md) sobre a replicação de VMs Hyper-V (sem VMM). |No Portal do Azure Clássico, você pode failover de VMs no armazenamento clássico do Azure |Há suporte para a implantação do PowerShell. |
| **Servidores físicos para o Azure** |Replicar servidores físicos do Windows e Linux para armazenamento do Azure |No Portal do Azure, os servidores podem realizar failover no armazenamento clássico ou do Resource Manager.<br/><br/> A implantação no [Portal do Azure](site-recovery-vmware-to-azure.md) fornece uma experiência de implantação simplificada e uma série de benefícios de recurso. |No portal clássico do Azure, você pode implantar com o [modelo aprimorado](site-recovery-vmware-to-azure-classic.md) e fazer failover para o armazenamento clássico do Azure.<br/><br/>  Há também um modelo herdado que não deve ser usado para novas implantações. |Não há suporte para implantação no PowerShell no momento. |
| **VMs VMware/servidores físicos para um site secundário* |Replicar VMs VMware ou servidores físicos Windows/Linux para um site secundário. [Saiba mais e baixe](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) o guia do usuário do InMage Scout. |Não disponível no Portal do Azure |No Portal Clássico, baixe o InMage Scout de um cofre da Recuperação de Site. |Não há suporte para implantação no PowerShell no momento |
| **VMs do Hyper-V para um site secundário** |Replicar as VMs do Hyper-V em nuvens de VMM para uma nuvem secundária |No [Portal do Azure](site-recovery-vmm-to-vmm.md) , você pode replicar VMs do Hyper-V em nuvens do VMM para um site secundário usando apenas a Réplica do Hyper-V. Não há suporte para rede SAN no momento. |No portal clássico do Azure, você pode replicar VMs Hyper-V em nuvens VMM para um site secundário usando a [Réplica do Hyper-V](site-recovery-vmm-to-vmm-classic.md) ou a [replicação de rede SAN](site-recovery-vmm-san.md) |Há suporte para a implantação do PowerShell |

## <a name="check-what-you-need-for-deployment"></a>Confira o que você precisa para a implantação
### <a name="replicate-to-azure"></a>Replicar no Azure
| **Requisito** | **Detalhes** |
| --- | --- |
| **Conta do Azure** |Você precisará de uma conta do [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre os preços da Recuperação de Site. |
| **Armazenamento do Azure** |Os dados replicados são armazenados no armazenamento do Azure e as VMs do Azure são criadas quando ocorre failover. Para replicar no Azure, você precisará de uma [conta de armazenamento do Azure](../storage/storage-introduction.md).<br/><br/>Se estiver implantando o Site Recovery no Portal Clássico, você precisará de uma ou mais [contas de armazenamento padrão do GRS](../storage/storage-redundancy.md#geo-redundant-storage).<br/><br/> Se estiver implantando no Portal do Azure, você poderá usar armazenamento GRS ou LRS.<br/><br/>  Se você estiver replicando VMs de VMware ou servidores físicos no Portal do Azure, haverá suporte para o armazenamento premium. Observe que se estiver usando uma conta de armazenamento premium, você também precisará de uma conta de armazenamento padrão para armazenar os logs de replicação que capturam as alterações contínuas nos dados locais. O [Armazenamento premium](../storage/storage-premium-storage.md) normalmente é usada para máquinas virtuais que precisam de um desempenho de E/S consistentemente alto e baixa latência para hospedar cargas de trabalho intensivas de E/S de host.<br/><br/>  Se você desejar usar uma conta premium para armazenar dados replicados, também precisará de uma conta de armazenamento padrão para armazenar os logs de replicação que capturam as alterações contínuas nos dados locais. |
| **Rede do Azure** |Para replicar para o Azure, você precisará de uma rede do Azure com a qual as máquinas virtuais do Azure se conectarão quando forem criadas após o failover.<br/><br/>  Se estiver implantando no Portal Clássico, você usará uma rede clássica. Se estiver implantando no Portal do Azure, você poderá usar uma rede clássica ou do Resource Manager.<br/><br/>  A rede deve estar na mesma região do que o cofre. |
| **Mapeamento de rede (VMM para Azure)** |Se você estiver replicando do VMM para o Azure, o [mapeamento de rede](site-recovery-network-mapping.md), garanta que as VMs do Azure estejam conectadas às redes corretas depois do failover.<br/><br/>  Para configurar o mapeamento de rede, você precisará configurar redes VM no portal do VMM. |
| **Configuração local** |**VMs VMware**: você precisará de um computador local executando componentes da Recuperação de Site, servidor do VMware vSphere hosts/vCenter e VMs que você deseja replicar. [Leia mais](site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites).<br/><br/> **Servidores físicos**: se estiver replicando servidores físicos, você precisará de computadores locais que executam componentes da Recuperação de Site e servidores físicos que você deseja replicar. [Leia mais](site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites). Se quiser realizar [failback](site-recovery-failback-azure-to-vmware.md) após o failover para o Azure, você precisará de uma infraestrutura de VMware para fazer isso.<br/><br/> **VMs Hyper-V**: If you want to replicate VMs Hyper-V in VMM clouds you'll need a VMM server, and Hyper-V hosts on which VMs you want to protect are located. [Leia mais](site-recovery-vmm-to-azure.md#on-premises-prerequisites).<br/><br/>  Se você quiser replicar VMs Hyper-V sem VMM, precisará de hosts Hyper-V nos quais as VMs estejam localizadas. [Leia mais](site-recovery-hyper-v-site-to-azure.md#on-premises-prerequisites). |
| **Computadores protegidos** |Computadores protegidos que serão replicados para o Azure devem cumprir os [pré-requisitos do Azure](#azure-virtual-machine-requirements) descritos abaixo. |

### <a name="replicate-to-a-secondary-site"></a>Replicar para um site secundário
| **Requisito** | **Detalhes** |
| --- | --- |
| **Conta do Azure** |Você precisará de uma conta do [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre os preços da Recuperação de Site. |
| **Configuração local** |**VMs VMware**: no site primário, você precisará de um processo para armazenar em cache, compactar e criptografar os dados de replicação antes de enviá-los para o site secundário. No site secundário, você instalará um servidor de configuração para gerenciar e monitorar a implantação e um servidor de destino mestre. Também é recomendável usar um servidor vContinuum para facilitar o gerenciamento. Além disso, você precisa de um ou mais o hosts VMware vSphere e, opcionalmente, um servidor vCenter. [Leia mais](site-recovery-vmware-to-vmware.md)<br/><br/> **VMs Hyper-V em nuvens VMM**: você precisará configurar os servidores VMM e hosts Hyper-V que contém as VMs que você deseja replicar. [Leia mais](site-recovery-vmm-to-vmm.md#on-premises-prerequisites). |
| **Mapeamento de rede (VMM para VMM)** |Se você estiver mapeando de VMM para VMM, o [mapeamento de rede](site-recovery-network-mapping.md) garantirá que as VMs de réplica estejam conectadas às redes apropriadas após o failover e sejam colocadas da forma ideal em hosts Hyper-V. Para configurar o mapeamento de rede, você precisará configurar as redes de VM no seu VMM. |
| **Mapeamento de armazenamento** |Se você estiver replicando de VMM para VMM, poderá opcionalmente definir o [mapeamento de armazenamento](site-recovery-storage-mapping.md) para especificar o destino de armazenamento para as VMs replicadas. O mapeamento de armazenamento pode ser configurado para Réplica do Hyper-V e replicação da rede SAN.<br/><br/>  Não há suporte para o mapeamento de armazenamento no Portal do Azure no momento. |

## <a name="verify-url-access"></a>Verificar o acesso à URL
Verifique se essas URLs podem ser acessadas dos servidores.

| **URL** | **VMM no VMM** | **VMM para Azure** | **Hyper-V para Azure** | **VMware no Azure** |
| --- | --- | --- | --- | --- |
| *.accesscontrol.windows.net |Permitir |Permitir |Permitir |Permitir |
| *.backup.windowsazure.com |Não requerido |Permitir |Permitir |Permitir |
| *.hypervrecoverymanager.windowsazure.com |Permitir |Permitir |Permitir |Permitir |
| *.store.core.windows.net |Permitir |Permitir |Permitir |Permitir |
| *.blob.core.windows.net |Não requerido |Permitir |Permitir |PERMITIR |
| https://www.msftncsi.com/ncsi.txt |PERMITIR |Permitir |Permitir |PERMITIR |
| https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi |Não requerido |Não requerido |Não requerido |Permitir |

## <a name="azure-virtual-machine-requirements"></a>Requisitos de máquina virtual do Azure
Você pode implantar a Recuperação de Site para replicar máquinas virtuais e servidores físicos executando qualquer sistema operacional com suporte do Azure. Isso inclui a maioria das versões do Windows e do Linux. Será necessário verificar se as máquinas virtuais locais que você deseja proteger atendem aos requisitos do Azure.

| **Recurso** | **Requisitos** | **Detalhes** |
| --- | --- | --- |
| Host Hyper-V |Deve estar executando o Windows Server 2012 R2 |A verificação de pré-requisitos falhará se o sistema operacional não tiver suporte |
| Hipervisor VMware |Sistema operacional com suporte |[Verifique os requisitos](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) |
| Sistema operacional convidado |Replicação do Hyper-V para o Azure: a Recuperação de Site dá suporte para todos os sistemas operacionais [com suporte no Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Para replicação de VMware e servidor físico: verifique os [pré-requisitos](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) |A verificação de pré-requisitos falhará se não houver suporte. |
| Arquitetura do sistema operacional convidado |64 bits |A verificação de pré-requisitos falhará se não houver suporte |
| Tamanho do disco do sistema operacional |Até 1023 GB |A verificação de pré-requisitos falhará se não houver suporte |
| Contagem do disco do sistema operacional |1 |A verificação de pré-requisitos falhará se não houver suporte. |
| Contagem de disco de dados |16 ou menos (o valor máximo é uma função do tamanho da máquina virtual que está sendo criada. 16 = XL) |A verificação de pré-requisitos falhará se não houver suporte |
| Tamanho do VHD do disco de dados |Até 1023 GB |A verificação de pré-requisitos falhará se não houver suporte |
| Adaptadores de rede |Há suporte para vários adaptadores | |
| Endereço IP estático |Suportado |Se a máquina virtual primária estiver usando um endereço IP estático, você poderá especificar o endereço IP estático da máquina virtual que será criada no Azure. Observe que não há suporte para endereço IP estático para uma máquina virtual Linux em execução no Hyper-v. |
| Disco iSCSI |Sem suporte |A verificação de pré-requisitos falhará se não houver suporte |
| VHD compartilhado |Sem suporte |A verificação de pré-requisitos falhará se não houver suporte |
| Disco FC |Sem suporte |A verificação de pré-requisitos falhará se não houver suporte |
| Formato de disco rígido |VHD  <br/><br/>  VHDX |Apesar de atualmente o VHDX não ter suporte no Azure, a Recuperação de Site converterá automaticamente o VHDX em VHD ao realizar o failover para o Azure. Quando você executa o failback para o local, as máquinas virtuais continuam a usar o formato VHDX. |
| BitLocker |Sem suporte |O BitLocker deve ser desabilitado antes de proteger uma máquina virtual. |
| Nome da máquina virtual |Entre 1 e 63 caracteres. Restrito a letras, números e hifens. Deve começar e terminar com uma letra ou um número |Atualize o valor nas propriedades da máquina virtual na Recuperação de Site |
| Tipo de máquina virtual |<p>Geração 1</p> <p>Geração 2 - Windows</p> |Há suporte para a máquina virtual de 2ª geração com o tipo de disco do sistema operacional de um disco básico que inclui os volumes de dados 1 ou 2 com o formato de disco como VHDX, que é menor do que 300GB. Não há suporte para as máquinas virtuais Linux Geração 2. [Para obter mais informações](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/) |

## <a name="optimizing-your-deployment"></a>Otimizando sua implantação
Use as dicas a seguir para ajudá-lo a otimizar e dimensionar sua implantação.

* **Tamanho de volume do sistema operacional**: quando você replica uma máquina virtual no Azure, o volume do sistema operacional deve ser inferior a 1TB. Se você tiver mais volumes do que isso, poderá movê-los manualmente para um disco diferente antes de iniciar a implantação.
* **Tamanho do disco de dados**: se você estiver replicando para o Azure, poderá ter até 32 discos de dados em uma máquina virtual, cada uma com um máximo de 1 TB. Você pode replicar e realizar de forma efetiva o failover de uma máquina virtual de aproximadamente 32 TB.
* **Limites de plano de recuperação**: o Site Recovery pode ser ampliada para milhares de máquinas virtuais. Os planos de recuperação são projetados como um modelo para aplicativos que devem sofrer o failover juntos. Por isso, limitamos para 50 o número de máquinas em um plano de recuperação.
* **Limites de serviço do Azure**: toda assinatura do Azure vem com um conjunto de limites padrão de núcleos, serviços de nuvem etc. Recomendamos a execução de um failover de teste para validar a disponibilidade de recursos em sua assinatura. Você pode modificar esses limites por meio do suporte do Azure.
* **Planejamento de capacidade**: leia sobre o [planejamento de capacidade](site-recovery-capacity-planner.md) para o Site Recovery.
* **Largura de banda de replicação**: se você tiver pouca largura de banda de replicação, observe o seguinte:
  * **ExpressRoute**: a Recuperação de Site funciona com o Azure ExpressRoute e com otimizadores de WAN, por exemplo, Riverbed. [Leia mais](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) sobre o ExpressRoute.
  * **Tráfego de replicação**: a Recuperação de Site realiza uma replicação inicial inteligente usando apenas os blocos de dados e não o VHD inteiro. Somente as alterações são replicadas durante a replicação contínua.
  * **Tráfego de rede**: você pode controlar o tráfego de rede usado para replicação configurando o [Windows QoS](https://technet.microsoft.com/library/hh967468.aspx) com uma política baseada no endereço IP de destino e na porta.  Além disso, se você estiver replicando para o Azure Site Recovery usando o agente de Backup do Azure, poderá configurar a limitação desse agente. [Leia mais](https://support.microsoft.com/kb/3056159).
* **RTO**: para medir o RTO (Objetivo do Tempo de Recuperação) que você pode esperar com o Site Recovery, sugerimos executar um failover de teste e exibir os trabalhos do Site Recovery a fim de analisar o tempo até a conclusão das operações. Se você estiver efetuando o failover no Azure, para obter o melhor RTO, recomendamos a automatização de todas as ações manuais, integrando com os planos de recuperação e de automação do Azure.
* **RPO**: a Recuperação de Site oferece suporte a um objetivo de ponto de recuperação quase síncrono (RPO) quando você replica para o Azure. Isso pressupõe uma largura de banda suficiente entre seu armazenamento de dados e o Azure.

## <a name="service-urls"></a>URLs de serviço
Verifique se essas URLs podem ser acessadas no servidor

| **URLs** | **VMM no VMM** | **VMM para Azure** | **Site do Hyper-V no Azure** | **VMware no Azure** |
| --- | --- | --- | --- | --- |
|  \*.accesscontrol.windows.net |Acesso necessário |Acesso necessário |Acesso necessário |Acesso necessário |
|  \*.backup.windowsazure.com | |Acesso necessário |Acesso necessário |Acesso necessário |
|  \*.hypervrecoverymanager.windowsazure.com |Acesso necessário |Acesso necessário |Acesso necessário |Acesso necessário |
|  \*.store.core.windows.net |Acesso necessário |Acesso necessário |Acesso necessário |Acesso necessário |
|  \*.blob.core.windows.net | |Acesso necessário |Acesso necessário |Acesso necessário |
|  https://www.msftncsi.com/ncsi.txt |Acesso necessário |Acesso necessário |Acesso necessário |Acesso necessário |
|  https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi | | | |Acesso necessário |

## <a name="next-steps"></a>Próximas etapas
Depois de aprender e comparando os requisitos de implantação geral, você pode ler os pré-requisitos detalhados e iniciar a implantação de cada cenário.

* [Replique as máquinas virtuais VMware para o Azure](site-recovery-vmware-to-azure-classic.md)
* [Replicar servidores físicos para o Azure](site-recovery-vmware-to-azure-classic.md)
* [Replicar servidor Hyper-V em nuvens do VMM para o Azure](site-recovery-vmm-to-azure.md)
* [Replicar máquinas virtuais Hyper-V (sem VMM) para o Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replicar máquinas virtuais do Hyper-V para um site secundário](site-recovery-vmm-to-vmm.md)
* [Replicar máquinas virtuais do Hyper-V para um site secundário com SAN](site-recovery-vmm-san.md)
* [Replicar máquinas virtuais do Hyper-V com um único servidor VMM](site-recovery-single-vmm.md)



<!--HONumber=Nov16_HO3-->


