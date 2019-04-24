---
title: Configurações e operações de infraestrutura do SAP HANA no Azure | Microsoft Docs
description: Guia de operações para sistemas SAP HANA que são implantados em máquinas virtuais (VMs) do Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: acccc553c5b63b2acd0f9793b0397b25145449dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60477332"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Configurações e operações de infraestrutura do SAP HANA no Azure
Este artigo fornece orientação sobre como configurar a infraestrutura do Azure e operar sistemas SAP HANA que são implantados em máquinas de virtuais nativas do Azure (VMs). Este artigo também inclui informações de configuração de expansão para a SKU de VM M128s do SAP HANA. Este artigo não se destina a substituir a documentação padrão do SAP, que inclui o seguinte conteúdo:

- [Guia de administração do SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Guias de instalação do SAP](https://service.sap.com/instguides)
- [Notas do SAP](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>Pré-requisitos
Para usar este guia, você precisa ter um conhecimento básico dos seguintes componentes do Azure:

- [Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Rede do Azure e redes virtuais](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Armazenamento do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Para saber mais sobre o SAP NetWeaver e outros componentes SAP no Azure, consulte a seção [SAP no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) na [documentação do Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Considerações básicas sobre a instalação
As seções a seguir descrevem considerações de configuração básica para implantar sistemas SAP HANA em máquinas virtuais do Azure.

### <a name="connect-to-azure-virtual-machines"></a>Conectar máquinas virtuais do Azure
Conforme documentado na [máquinas virtuais do Azure, guia de planejamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), dois métodos básicos são usados para se conectar às VMs do Azure:

- Conecte-se por meio da internet e pontos de extremidade públicos em uma VM de JumpBox ou na VM que executa o SAP HANA.
- Conexão por meio de uma [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) ou do Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Uma conectividade site a site por meio de VPN ou rota expressa é necessária para cenários de produção. Esse tipo de conexão também é necessário para cenários de não produção alimentam cenários de produção em que o software SAP é usado. A imagem a seguir mostra um exemplo de conectividade entre sites:

![Conectividade entre sites](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Escolha os tipos de VM do Azure
Os tipos de VM do Azure a ser usado para cenários de produção são listados na [documentação do SAP para IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Para cenários de não produção, uma variedade maior de VMs nativas do Azure está disponível.

>[!NOTE]
> Para cenários de não produção, use os tipos de VM são listados na [nota SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Para o uso de VMs do Azure para cenários de produção, verifique certificadas para SAP HANA VMs no SAP publicados [certified lista de plataformas de IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

Para implantar as VMs no Azure, use:

- O portal do Azure.
- Cmdlets do Azure PowerShell.
- A CLI do Azure.

Você também pode implantar uma plataforma completa de SAP HANA instalada nos serviços de VM do Azure por meio de [da SAP cloud platform](https://cal.sap.com/). Para obter informações sobre o processo de instalação, consulte [implantar SAP 4hana/s ou BW/4HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h). Para obter informações sobre a automação liberada, consulte [neste artigo do GitHub](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

### <a name="choose-an-azure-storage-type"></a>Escolha um tipo de armazenamento do Azure
O Azure fornece dois tipos de armazenamento que são adequados para VMs do Azure que executam o SAP HANA:  

- Padrão unidades de disco rígido (HDDs)
- Unidades de estado sólido (SSDs) Premium

Para saber mais sobre esses tipos de disco, consulte [selecione um tipo de disco](../../windows/disks-types.md).

O Azure oferece dois métodos de implantação para VHDs no armazenamento standard do Azure e o armazenamento premium. Se o cenário geral permitir, aproveite as implantações do [Azure Managed disk](https://azure.microsoft.com/services/managed-disks/).

Para obter uma lista de tipos de armazenamento e seus SLAs na taxa de transferência IOPS e armazenamento, consulte [documentação do Azure para discos gerenciados](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="configure-the-storage-for-azure-virtual-machines"></a>Configurar o armazenamento para máquinas virtuais do Azure

Você provavelmente nunca se preocupa com subsistemas de e/s e seus recursos porque o fornecedor do dispositivo feita se os requisitos mínimos de armazenamento foram atendidos para o SAP HANA. Quando você cria a infraestrutura do Azure por conta própria, você precisa estar ciente de alguns desses requisitos. Você também deve entender os requisitos de configuração sugeridos nas seções a seguir. Para casos em que você configure as máquinas virtuais você deseja que o SAP HANA para ser executado, talvez seja necessário:

- Habilite o volume de leitura/gravação em /hana/log de no mínimo de 250 MB/s para tamanhos de e/s de 1 MB.
- Habilite a atividade de leitura de pelo menos 400 MB/s para /hana/data para tamanhos de e/s de 16 MB e 64 MB.
- Habilite a atividade de gravação de pelo menos 250 MB/s para /hana/data com tamanhos de e/s de 16 MB e 64 MB.

Armazenamento de baixa latência é essencial para sistemas DBMS, mesmo que o DBMS, como o SAP HANA, mantém os dados na memória. O caminho crítico no armazenamento é geralmente em torno das gravações de log de transações dos sistemas DBMS. Mas, operações como gravar pontos de salvamento ou carregar dados na memória após a recuperação de pane também pode ser fundamental. 

Uso de discos premium do Azure para volumes /hana/data e /Hana/log. é obrigatório. Para obter a taxa de transferência mínima de /hana/log e /hana/data conforme desejado pelo SAP, compile um RAID 0 utilizando mdadm ou um Gerenciador de Volume lógico (LVM) em vários discos de armazenamento premium do Azure. Use também os volumes RAID como /hana/data e /Hana/log. É recomendável que você use os seguintes tamanhos de faixa para RAID 0:

- 64 KB ou 128 KB para hana/data
- 32 KB para hana/log

> [!NOTE]
> Você não precisa configurar um nível de redundância usando volumes RAID, pois o armazenamento standard e premium do Azure mantém três imagens de um VHD. O uso de um volume RAID é puramente para configurar os volumes que fornecem a taxa de transferência de e/s suficiente.

Acumulação de vários VHDs do Azure sob um RAID é cumulativa de um lado de taxa de transferência IOPS e armazenamento. Se você colocar um RAID 0 em 3 discos de armazenamento premium do Azure x P30, ele fornece três vezes a IOPS e três vezes a produtividade de armazenamento de um disco de P30 de armazenamento único premium do Azure.

As seguintes recomendações de armazenamento em cache supõem as características de e/s para o SAP HANA:

- Não há quase qualquer carga de trabalho de leitura em relação aos arquivos de dados do HANA. As exceções são grandes e/SS após a reinicialização da instância do HANA ou quando dados são carregados no HANA. Outro caso de E/Ss de leitura maiores em arquivos de dados podem ser backups de banco de dados do HANA. Como resultado, o cache de leitura não faz sentido porque, na maioria dos casos, todos os volumes de dados de arquivos devem ser lidas completamente.
- A gravação nos arquivos de dados está ocorrendo em intermitências com base nos pontos de salvamento e na recuperação de pane do HANA. Gravar pontos de salvamento é assíncrona e não se mantém todas as transações de usuário. A gravação de dados durante a recuperação de pane é crítica para o desempenho para que o sistema responda com rapidez novamente. Recuperação de pane deve ser situações excepcionais em vez disso.
- Praticamente, não há nenhuma leitura dos arquivos de refazer do HANA. As exceções são grandes e/SS quando você executa backups de log de transações, recuperação de pane ou a fase de reinicialização de uma instância do HANA.  
- A principal carga contra o arquivo de log de restauração do SAP HANA é gravações. Dependendo da natureza da carga de trabalho, você pode ter e/SS tão pequenas de 4 KB ou em outros casos, a e/SS de tamanho de 1 MB ou mais. A latência de gravação no log de refazer do SAP HANA é crítica para o desempenho.
- Todas as gravações devem ser persistidas no disco de forma confiável.

Como resultado desses padrões de e/s observados pelo SAP HANA, defina o armazenamento em cache dos diferentes volumes que usam o armazenamento premium do Azure para:

- **/hana/data**: Sem cache.
- **/hana/log**: Sem cache, com uma exceção para VMs da série M (veja mais mais adiante neste artigo).
- **/hana/shared**: Cache de leitura.


Além disso, tenha em mente a taxa de transferência de e/s de VM geral ao dimensionar ou decidir sobre uma VM. Geral, taxa de transferência de armazenamento VM está documentada no [tamanhos de máquina virtual com otimização de memória](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

#### <a name="linux-io-scheduler-mode"></a>Modo de Agendador de e/s do Linux
O Linux possui vários modos de agendamento de E/S diferentes. Uma recomendação comum de fornecedores do Linux e do SAP é definir o modo de Agendador de e/s para volumes de disco dos **cfq** modo para o **noop** modo. Para obter mais informações, consulte [SAP Observação #1984798](https://launchpad.support.sap.com/#/notes/1984787). 


#### <a name="storage-solution-with-write-accelerator-for-azure-m-series-virtual-machines"></a>Solução de armazenamento com acelerador de gravação para o Azure máquinas virtuais da série M
 Gravar que Accelerator é um recurso do Azure que está implantando para VMs da série M do Azure exclusivamente. O objetivo da funcionalidade é melhorar a latência de e/s das gravações no armazenamento premium do Azure. Para o SAP HANA, o Acelerador de Gravação deve ser utilizado somente no volume /hana/log. Por esse motivo, as configurações mostradas até agora devem ser alteradas. A principal mudança é a divisão entre /hana/data e /hana/log para usar o Acelerador de gravação somente no volume /hana/log. 

> [!IMPORTANT]
> Certificação do SAP HANA do Azure série M máquinas virtuais é exclusivo para o Acelerador de gravação para o volume de hana/log. Como resultado, as implantações do SAP HANA de cenário de produção no Azure série M máquinas virtuais devem ser configurados com o Acelerador de gravação para o hana/log volume.

> [!NOTE]
> Para cenários de produção, verifique se um determinado tipo de VM tem suporte pelo SAP HANA pelo SAP na [documentação do SAP para IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

A tabela a seguir mostra as configurações recomendadas.

| SKU da VM | RAM | E/s máxima de VM<br /> throughput | /hana/data | /hana/log | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1.000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1.000 giB | 1.000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1,750 giB | 1.000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2.000 giB | 2.000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3.800 giB | 2.000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

Verifique se a taxa de transferência de armazenamento para os diferentes volumes recomendados atende a carga de trabalho que você deseja executar. Se a carga de trabalho exigir volumes maiores para /hana/data e /hana/log, aumente o número de armazenamento premium do Azure VHDs. Dimensionar um volume com mais VHDs que listados aumenta a IOPS e taxa de transferência de e/s dentro dos limites do tipo de máquina virtual do Azure.

O Acelerador de Gravação funciona somente em conjunto com o [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Pelo menos, os discos de armazenamento premium do Azure que formam o volume /hana/log devem ser implantados como discos gerenciados.

Há limites para o armazenamento premium do Azure VHDs por VM acelerador de gravação pode dar suporte. Os limites atuais são:

- 16 VHDs para uma M128xx VM.
- 8 VHDs para uma M64xx VM.
- 4 VHDs para uma M32xx VM.

Para obter mais informações sobre como habilitar o Acelerador de gravação, consulte [acelerador de gravação](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).

Para obter mais informações sobre e restrições para o Acelerador de gravação, consulte a documentação do mesma.


#### <a name="cost-conscious-azure-storage-configuration"></a>Configuração de armazenamento do Azure atento aos custos
A tabela a seguir mostra uma configuração de tipos de VM que os clientes geralmente utilizam para hospedar o SAP HANA em VMs do Azure. Pode haver alguns tipos de VM que não atendem a todos os critérios mínimos para o SAP HANA. Também pode haver alguns tipos de VM que não têm suporte oficial com o SAP HANA pelo SAP. Até agora, essas VMs têm executado bem para cenários de não produção. 

> [!NOTE]
> Para cenários de produção, verifique se um determinado tipo de VM tem suporte pelo SAP HANA pelo SAP na [documentação do SAP para IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).


| SKU da VM | RAM | E/s máxima de VM<br /> throughput | hana/data e hana/log<br /> distribuídos com LVM ou mdadm | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128 GiB | 384 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 432 GiB | 1.200 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GiB | 2.000 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M64ls | 512 GiB | 1.000 MB/s | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 |1 x S30 |
| M64s | 1.000 giB | 1.000 MB/s | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1,750 giB | 1.000 MB/s | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2.000 giB | 2.000 MB/s |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3.800 giB | 2.000 MB/s | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


Os discos que são recomendados para tipos de VM menor com 3 acima do normal x P20 os volumes em relação ao que é fornecidos na recomendação de espaço de [white paper de armazenamento do SAP TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). A opção exibida na tabela foi feita para fornecer a taxa de transferência de disco suficiente para o SAP HANA. Se você precisar alterar o **hana/backup** volume, que foi dimensionado para manter os backups que representam o dobro do volume de memória, fique à vontade fazer ajustes. 

Verifique se a taxa de transferência de armazenamento para os diferentes volumes recomendados atende a carga de trabalho que você deseja executar. Se a carga de trabalho exigir volumes maiores para /hana/data e /hana/log, aumente o número de armazenamento premium do Azure VHDs. Dimensionar um volume com mais VHDs que listados aumenta a IOPS e taxa de transferência de e/s dentro dos limites do tipo de máquina virtual do Azure. 

> [!NOTE]
> As configurações anteriores não se beneficiar [máquina virtual do Azure SLA de única VM](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) porque ele usa uma combinação de armazenamento premium do Azure e armazenamento standard do Azure. A seleção foi escolhida para otimizar os custos. Escolha o armazenamento premium para todos os discos na tabela que são listados como armazenamento do Azure padrão (Sxx) para tornar a configuração da VM em conformidade com o SLA de única VM do Azure.


> [!NOTE]
> As recomendações de configuração de disco de destino requisitos mínimos para SAP fornece seus provedores de infra-estrutura. Em implantações de clientes reais e cenários de carga de trabalho, essas recomendações não fornecem recursos suficientes em algumas situações. Por exemplo, um cliente precisasse de um recarregamento mais rápido dos dados após uma reinicialização do HANA ou de configurações de backup necessária maior largura de banda para o armazenamento. Outros casos incluem /hana/log, onde a 5.000 IOPS não eram suficientes para a carga de trabalho específica. Use essas recomendações como um ponto de partida e adaptá-los com base nos requisitos da carga de trabalho.
>  

### <a name="set-up-azure-virtual-networks"></a>Configurar redes virtuais do Azure
Quando você tiver conectividade site a site no Azure por meio de VPN ou ExpressRoute do Azure, você deve ter pelo menos uma rede virtual do Azure que está conectada por meio de um gateway virtual ao circuito de ExpressRoute ou VPN. Em implantações simples, o gateway virtual pode ser implantado em uma sub-rede da rede virtual do Azure que hospeda o SAP HANA em instâncias muito. 

Para instalar o SAP HANA, crie duas sub-redes adicionais na rede virtual do Azure. Uma sub-rede hospeda as VMs para executar as instâncias do SAP HANA. A outra sub-rede executa JumpBox ou gerenciamento de VMs para hospedar o SAP HANA Studio, outros softwares de gerenciamento ou o software de aplicativo.

> [!IMPORTANT]
> Configurando [soluções de virtualização de rede do Azure](https://azure.microsoft.com/solutions/network-appliances/) no caminho de comunicação entre o aplicativo de SAP e a camada de DBMS do NetWeaver um SAP - Hybris- ou sistema baseado em S/4HANA SAP não tem suporte. Essa restrição é por motivos de desempenho e funcionalidade. O caminho de comunicação entre a camada de aplicativo do SAP e a camada de DBMS deve ser um direto. A restrição não inclui [regras (NSG) do grupo de segurança de rede e o grupo de segurança de aplicativo (ASG)](https://docs.microsoft.com/azure/virtual-network/security-overview) se essas regras ASG e NSG permitem que um caminho de comunicação direta. 
>
> Outros cenários em que não têm suporte a dispositivos de rede virtual estão em: 
>
> - Caminhos de comunicação entre VMs do Azure que representam o Linux Pacemaker cluster nós e os dispositivos SBD, conforme descrito em [alta disponibilidade do SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server for SAP Applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).
> - Conjunto de caminhos de comunicação entre VMs do Azure e o servidor de arquivos de escalabilidade horizontal do Windows Server até conforme descrito em [agrupar instância SAP ASCS/SCS em um cluster de failover do Windows usando um compartilhamento de arquivos do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). 
>
> Soluções de virtualização de rede nos caminhos de comunicação podem facilmente double a latência de rede entre os parceiros de comunicação de dois. Eles também podem restringir a taxa de transferência em caminhos críticos entre a camada de aplicativo do SAP e a camada de DBMS. Em alguns cenários de cliente, dispositivos de rede virtual podem causar clusters Linux Pacemaker falhe. Esses são casos em que as comunicações entre os nós de cluster do Linux Pacemaker se comunicar com o dispositivo SBD por meio de um dispositivo de rede virtual.  
> 

> [!IMPORTANT]
> Outro que de design do *não* com suporte é a segregação de camada do aplicativo SAP e a camada de DBMS em diferentes redes virtuais do Azure que não estão [emparelhadas](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) uns com os outros. É recomendável separar a camada de DBMS e camada do aplicativo SAP usando sub-redes em uma rede virtual do Azure, em vez de por meio de diferentes redes virtuais do Azure. 
>
>Se você decidir não a seguir a recomendação e em vez disso, separar as duas camadas em diferentes redes virtuais, as duas redes virtuais devem ser [emparelhadas](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 
>
> Lembre-se que tráfego de rede entre dois [emparelhadas](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) redes virtuais do Azure está sujeito a custos de transferência. Volume de dados grande que consiste em muitos terabytes é trocado entre a camada de DBMS e camada do aplicativo SAP. Se a camada de DBMS e camada do aplicativo SAP são separadas entre duas redes emparelhadas de virtuais do Azure, você pode acumular custos substanciais. 

Quando você instala as VMs para executar o SAP HANA, as VMs necessitam de:

- Duas NICs virtuais instaladas. Uma NIC se conecta à sub-rede de gerenciamento. Outra NIC conecta-se da rede local ou outras redes para a instância do SAP HANA na VM do Azure.
- Endereços IP privados estáticos que são implantados para as duas NICS virtuais.

> [!NOTE]
> Atribuição de endereços IP estáticos por meio do Azure significa atribuí-los a NICs virtuais individuais. Não atribuir endereços IP estáticos dentro do sistema operacional convidado a uma NIC virtual. Alguns serviços do Azure, como Backup do Azure dependem do fato de que pelo menos a NIC virtual primária é definida como o DHCP e não para endereços IP estáticos. Para obter mais informações, consulte [backup da máquina virtual do Azure de solucionar problemas de](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Para atribuir vários endereços IP estáticos a uma VM, atribua várias NICs virtuais a uma VM.
>
>

Para implantações que são permanentes, crie uma arquitetura de rede do datacenter virtual no Azure. Essa arquitetura recomenda a separação entre o gateway de rede virtual do Azure que se conecta ao local em uma rede virtual do Azure separada. Essa rede virtual separada hospeda todo o tráfego que sai no local ou na internet. Usando essa abordagem, você pode implantar o software para auditar e registrar o tráfego que entra o datacenter virtual no Azure nessa rede virtual do hub separado. Dessa forma, você tem uma rede virtual que hospeda todos os software e configurações relacionadas ao tráfego de entrada e saído para a implantação do Azure.


Para obter mais informações sobre a abordagem do datacenter virtual e o design de rede de virtual do Azure relacionados, consulte: 

- [Datacenter virtual do Azure: Uma perspectiva de rede](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter).
- [Datacenter virtual do Azure e o plano de controle de enterprise](https://docs.microsoft.com/azure/architecture/vdc/).


>[!NOTE]
>O tráfego que flui entre uma rede virtual de hub e uma rede virtual do spoke usando [emparelhamento de rede virtual do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) está sujeita a adicionais [custos](https://azure.microsoft.com/pricing/details/virtual-network/). Com base nesses custos, talvez você precise que assumir compromissos entre executar um projeto de rede de hub e spoke estrito e executar várias [gateways de ExpressRoute do Azure](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) que você se conectar a spokes para ignorar o emparelhamento de rede virtual. 
>
> Os gateways de ExpressRoute do Azure apresentam adicionais [custos](https://azure.microsoft.com/pricing/details/vpn-gateway/) muito. Você também pode encontrar os custos adicionais de software de terceiros, que você pode usar para fazer logon, auditoria e monitorar o tráfego de rede. Considere os custos para troca de dados por meio do emparelhamento versus os custos criados por licenças de software e gateways de ExpressRoute adicionais de rede virtual. Você pode decidir sobre microssegmentação dentro de uma rede virtual usando sub-redes como unidades de isolamento em vez de redes virtuais.


Para obter uma visão geral dos diferentes métodos que você pode usar para atribuir endereços IP, consulte [endereço IP, tipos e métodos de alocação no Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Para VMs que executam o SAP HANA, trabalhe com os endereços IP estáticos que são atribuídos. O motivo é que alguns atributos de configuração para o HANA referenciam endereços IP.

[Os NSGs do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) são usados para direcionar o tráfego é roteado para a instância do SAP HANA ou o JumpBox. Os NSGs e eventualmente [grupos de segurança de aplicativo](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) estão associados com a sub-rede do SAP HANA e a sub-rede de gerenciamento.

A imagem a seguir mostra uma visão geral de um esquema aproximado de implantação para o SAP HANA que segue uma arquitetura de rede virtual de hub-and-spoke:

![Esquema de implantação aproximado para o SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)

Para implantar o SAP HANA no Azure sem uma conexão site a site, proteger a instância do SAP HANA da internet pública e ocultá-lo por trás de um proxy de encaminhamento. Neste cenário básico, a implantação se baseia em serviços DNS internos do Azure para resolver nomes de host. Em uma implantação mais complexa, em que endereços IP públicos são usados, os serviços DNS internos do Azure são especialmente importantes. Use os NSGs do Azure e [soluções de virtualização de rede do Azure](https://azure.microsoft.com/solutions/network-appliances/) para monitorar o roteamento da internet em sua arquitetura de rede virtual do Azure no Azure. 

A imagem a seguir mostra um esquema aproximado para saber como implantar o SAP HANA sem uma conexão site a site em uma arquitetura de rede virtual de hub-and-spoke:
  
![Esquema de implantação aproximado para o SAP HANA sem conexão site a site](media/hana-vm-operations/hana-simple-networking2.PNG)
 

Para obter outra descrição de como usar as soluções de virtualização de rede do Azure para controlar e monitorar o acesso da internet sem a arquitetura de rede virtual hub e spoke, consulte [implantar soluções de virtualização de rede altamente disponível](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configure-azure-infrastructure-for-sap-hana-scale-out"></a>Configurar a infraestrutura do Azure para expansão do SAP HANA
A Microsoft tem um SKU de VM série M que é certificado para uma configuração de expansão do SAP HANA. O tipo de VM M128s é certificado para uma expansão de até 16 nós. Para alterações em certificações de expansão do SAP HANA em VMs do Azure, consulte a [certified lista de plataformas de IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).

As versões de sistema operacional mínimo usadas para implantar configurações de escala horizontal em VMs do Azure são:

- SUSE Linux 12 SP3.
- Red Hat Linux 7.4.

Para a certificação de escalabilidade horizontal de 16 nós:

- Um nó é o nó mestre.
- Um máximo de 15 nós são nós de trabalho.

>[!NOTE]
>Em implantações de expansão de VM do Azure, não é possível usar um nó em espera.
>

Há duas razões por que você não pode configurar um nó em espera:

- O Azure neste momento não possui nenhum serviço NFS nativo. Como resultado, os compartilhamentos NFS devem ser configurados com a Ajuda da funcionalidade de terceiros.
- Nenhuma das configurações de NFS de terceiros pode atender aos critérios de latência de armazenamento para o SAP HANA com suas soluções implantadas no Azure.

Como resultado, os volumes /hana/data e /Hana/log. não podem ser compartilhados. Não compartilhe esses volumes de nós únicos impede o uso de um nó em espera do SAP HANA em uma configuração de expansão.

A imagem a seguir mostra o design básico para um único nó em uma configuração de expansão:

![Princípios básicos de expansão de um único nó](media/hana-vm-operations/scale-out-basics.PNG)

A configuração básica de um nó de VM para scale-out do SAP HANA é semelhante a:

- Para /hana/shared, crie um cluster NFS altamente disponível com base no SUSE Linux 12 SP3. Este cluster hospeda compartilhamentos NFS /hana/shared de sua configuração de expansão e SAP NetWeaver ou serviços centrais do BW/4HANA. Para obter informações sobre como criar essa configuração, consulte [alta disponibilidade para NFS em VMs do Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
- Todos os outros volumes de disco não são compartilhados entre os nós diferentes e não são baseados em NFS. Configurações de instalação e etapas para instalações do HANA de expansão com não compartilhado /hana/data e /hana/log são fornecidas neste artigo.

>[!NOTE]
>O cluster NFS altamente disponível, conforme exibido nos gráficos até o momento, é suportado apenas pelo SUSE Linux. Uma solução NFS altamente disponível baseada na Red Hat será avisada posteriormente.

Os volumes para os nós de dimensionamento é a mesma escala vertical, exceto para /hana/shared. A tabela a seguir mostra os tamanhos sugeridos e tipos para a SKU de VM M128s.

| SKU da VM | RAM | E/s máxima de VM<br /> throughput | /hana/data | /hana/log | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M128s | 2.000 giB | 2.000 MB/s |3 x P30 | 2 x P20 | 1 x P6 | 1 x P6 | 2 x P40 |


Verifique se a taxa de transferência de armazenamento para os diferentes volumes recomendados atende a carga de trabalho que você deseja executar. Se a carga de trabalho exigir volumes maiores para /hana/data e /hana/log, aumente o número de armazenamento premium do Azure VHDs. Dimensionar um volume com mais VHDs que listados aumenta a IOPS e taxa de transferência de e/s dentro dos limites do tipo de máquina virtual do Azure. Também se aplicam o Acelerador de gravação para os discos que formam o volume /hana/log.
 

Para uma fórmula que define o tamanho do volume hana/shared para escalabilidade horizontal como o tamanho da memória de um nó único trabalhador por quatro nós de trabalho, consulte [requisitos de armazenamento do SAP HANA TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

Supondo que você executar o SAP HANA expansão certificada M128s VM do Azure com cerca de 2 TB de memória, as recomendações do SAP são resumidas como:

- Para um nó mestre e até quatro nós de trabalho, o tamanho do volume /hana/shared é de 2 TB.
- Para um nó mestre e cinco e oito nós de trabalho, o tamanho do volume /hana/shared é 4 TB.
- Para um nó mestre e nós de trabalho de 9 a 12, o tamanho do volume /hana/shared é 6 TB.
- Para um nó mestre e nós de trabalho de 12 a 15, o tamanho do volume /hana/shared é 8 TB.

O design importantes que é exibido nos gráficos da configuração de nó único para uma VM do SAP HANA de expansão é a rede virtual com a configuração de sub-rede. SAP recomenda uma separação do tráfego e aplicativo – voltada ao cliente das comunicações entre os nós do HANA. 

Para atingir esse objetivo, anexe duas NICs virtuais diferentes à VM, conforme mostrado no gráfico. As duas NICs virtuais estiverem em sub-redes diferentes e tem dois endereços IP diferentes. Você então controlar o fluxo de tráfego com regras de roteamento usando NSGs ou rotas definidas pelo usuário.

Particularmente no Azure, não há nenhum significa e métodos para impor a qualidade de serviço e cotas em NICs virtuais específicas. Como resultado, a separação de comunicação e aplicativo – voltada ao cliente e nó interior não abre nenhuma oportunidade para priorizar um fluxo de tráfego em detrimento do outro. Em vez disso, a separação permanece uma medida de segurança protegendo as comunicações dentro do próprio nó das configurações de expansão.  

>[!IMPORTANT]
>SAP recomenda enfaticamente que você separe o tráfego de rede para o aplicativo cliente e lado e dentro do próprio nó tráfego conforme descrito neste artigo. É recomendável que você coloque uma arquitetura em vigor, conforme mostrado no gráfico anterior.
>

A imagem a seguir mostra a arquitetura de rede necessárias e mínimas do ponto de vista de rede:

![Princípios básicos de expansão de um único nó](media/hana-vm-operations/scale-out-networking-overview.PNG)

Os limites de suporte para até agora são 15 nós de trabalho, além de um nó mestre.

A imagem a seguir mostra a arquitetura de armazenamento do ponto de vista de armazenamento:


![Princípios básicos de expansão de um único nó](media/hana-vm-operations/scale-out-storage-overview.PNG)

O volume /Hana/Shared. está localizado na configuração de compartilhamento de NFS altamente disponível. Todos os outros discos são montados localmente para as VMs individuais. 

### <a name="highly-available-nfs-share"></a>Compartilhamento de NFS altamente disponível
Até agora, o cluster NFS altamente disponível está trabalhando somente com SUSE Linux. Para obter informações de instalação, consulte [alta disponibilidade para NFS em VMs do Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs). Se você não compartilhe cluster NFS com qualquer outra configuração fora da rede virtual do Azure que executa o SAP HANA em instâncias do HANA, instale-o na mesma rede virtual. Instale-o em sua própria sub-rede e certifique-se de que não todo o tráfego arbitrário possa acessar a sub-rede. Em vez disso, limite o tráfego para essa sub-rede para os endereços IP da VM que executam o tráfego para o volume /Hana/Shared.

As recomendações relacionadas à NIC virtual de uma VM de expansão do HANA que roteia o tráfego de /hana/shared, são:

- Como o tráfego para /hana/shared é moderado, encaminhá-lo por meio da NIC virtual que é atribuída à rede do cliente em uma configuração mínima.
- Por fim, para o tráfego para hana/shared, implante uma terceira sub-rede na rede virtual em que você implantar a configuração de expansão do SAP HANA. Atribua uma terceira NIC virtual que está hospedada na sub-rede. Use a terceira NIC virtual e o endereço IP associado para o tráfego para o compartilhamento NFS. Em seguida, você pode aplicar regras de roteamento e de acesso separados.

>[!IMPORTANT]
>Tráfego de rede entre as VMs com SAP HANA de uma maneira de escalabilidade horizontal implantada e o NFS altamente disponível em nenhuma circunstância pode ser roteado por meio de um [solução de virtualização de rede](https://azure.microsoft.com/solutions/network-appliances/) ou dispositivos virtuais semelhantes. Os NSGs do Azure são sem esses dispositivos. Verifique suas regras de roteamentos para certificar-se de que as soluções de virtualização de rede ou dispositivos virtuais semelhantes são desviados quando eles acessarem o NFS altamente disponível compartilham das VMs que executam o SAP HANA.
> 

Se você deseja compartilhar o cluster NFS altamente disponível entre as configurações do SAP HANA, mova todas essas configurações do HANA na mesma rede virtual. 
 

### <a name="install-an-sap-hana-scale-out-in-azure"></a>Instalar uma expansão do SAP HANA no Azure
Para instalar uma configuração de SAP de expansão, siga estas etapas gerais:

- Implantar novos ou adaptar-se a nova infra-estrutura de rede virtual do Azure.
- Implante novas VMs usando volumes de armazenamento gerenciado do Azure premium.
- Implantar um novo ou adaptar um cluster existente de NFS altamente disponível.
- Adaptar o roteamento de rede para certificar-se de que, por exemplo, a comunicação entre nós entre as VMs não é roteada por meio de um [solução de virtualização de rede](https://azure.microsoft.com/solutions/network-appliances/). O mesmo é verdadeiro para o tráfego entre as VMs e o cluster NFS altamente disponível.
- Instale o nó mestre SAP HANA.
- Adapte os parâmetros de configuração do nó mestre SAP HANA.
- Continue com a instalação de nós de trabalho do SAP HANA.

#### <a name="install-sap-hana-in-a-scale-out-configuration"></a>Instalar o SAP HANA em uma configuração de expansão
Sua infraestrutura de VM do Azure é implantada e todos os outros preparativos a serem forem concluída. Agora, para instalar as configurações de expansão do SAP HANA, siga estas etapas:

- Instale o nó mestre do SAP HANA de acordo com a documentação do SAP.
- *Após a instalação, altere o arquivo ini e adicione o parâmetro ' basepath_shared = nenhum ' para o ini*. Esse parâmetro permite que o SAP HANA executar na expansão sem compartilhado /hana/data e /Hana/log. entre os nós. Para obter mais informações, consulte [SAP Observação #2080991](https://launchpad.support.sap.com/#/notes/2080991).
- Depois de alterar o parâmetro ini, reinicie a instância do SAP HANA.
- Adicione nós de trabalho adicionais. Para obter mais informações, consulte [guia de administração do SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html). Especifique a rede interna para a comunicação entre nós do SAP HANA durante a instalação ou posteriormente, usando, por exemplo, o hdblcm local. Para obter mais informações, consulte [SAP Observação #2183363](https://launchpad.support.sap.com/#/notes/2183363). 

Seguindo esta rotina de instalação, a configuração de expansão que você instalou usa discos não compartilhados para executar /hana/data e /hana/log. O volume hana/shared é colocado em altamente disponíveis de compartilhamento NFS.


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>SAP HANA 2.0 em camadas dinâmica para máquinas virtuais do Azure

Além das certificações de SAP HANA em VMs da série M do Azure, também há suporte para SAP HANA 2.0 dinâmico em camadas (2.0 DT) no Microsoft Azure. Para obter links para documentação dinâmica de disposição em camadas do SAP HANA, consulte a seção "Links para DT documentação 2.0" neste artigo. Não há nenhuma diferença na instalação do produto ou operacional, por exemplo, por meio da ferramenta Cockpit do SAP HANA dentro de uma VM do Azure, mas alguns itens importantes são obrigatórios para suporte oficial no Azure. Estes pontos-chave são descritos nas seções a seguir. 

DT do SAP HANA 2.0 não tem suporte pelo SAP BW ou S4HANA. Os casos de uso principal agora são aplicativos nativos do HANA.


### <a name="overview"></a>Visão geral

A imagem a seguir fornece uma visão geral do suporte DT 2.0 no Microsoft Azure. Siga esses requisitos obrigatórios para estar em conformidade com a certificação oficial:

- DT 2.0 deve ser instalado em uma VM do Azure dedicada. Ele não pode ser executado na mesma VM em que o SAP HANA é executado.
- SAP HANA e DT 2.0 VMs devem ser implantadas dentro da mesma rede virtual do Azure.
- O SAP HANA e DT 2.0 VMs devem ser implantadas com rede acelerada do Azure habilitado.
- O tipo de armazenamento para as VMs de 2.0 DT deve ser o armazenamento premium do Azure.
- Vários discos do Azure devem ser anexados à VM DT 2.0.
- Criando um software RAID ou volume distribuído, por meio de LVM ou mdadm, é exigido pelo uso da distribuição entre os discos do Azure.

As seções a seguir fornecem mais de explicação.

![Visão geral da arquitetura do SAP HANA DT 2.0](media/hana-vm-operations/hana-dt-20.PNG)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>VM do Azure dedicada para SAP HANA DT 2.0

No Azure IaaS, DT 2.0 tem suporte apenas em uma VM dedicada. DT 2.0 não é permitido executar na mesma VM do Azure onde a instância do HANA está em execução. Inicialmente, os dois tipos VM podem ser usados para executar o SAP HANA DT 2.0:

- M64-32ms 
- E32sv3 

Para obter descrições de tipo VM, consulte [tamanhos de máquina virtual com otimização de memória](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

Dada a ideia básica do 2.0 DT, que é sobre o descarregamento de dados passivos para economizar custos, faz sentido usar tamanhos VM correspondentes. Não há nenhuma regra rígida para as combinações possíveis. Isso depende da carga de trabalho de cliente específico.

A tabela a seguir mostra as configurações recomendadas.

| Tipo de VM do SAP HANA | Tipo de VM do DT 2.0 |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


Todas as combinações de VMs da série M certificadas para SAP HANA com DT 2.0 VMs com suporte, como M64 32ms e E32sv3, são possíveis.


### <a name="azure-networking-and-sap-hana-dt-20"></a>Rede do Azure e SAP HANA DT 2.0

Instalar DT 2.0 em uma VM dedicada requer a taxa de transferência de rede entre a VM de 2.0 DT e VM do SAP HANA com um mínimo de 10 GB. Como resultado, é obrigatório para colocar todas as VMs dentro da mesma rede virtual do Azure e habilitar a rede acelerada do Azure.

Para obter mais informações sobre a rede acelerada do Azure, consulte [criar uma máquina virtual Linux com rede acelerada](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).

### <a name="vm-storage-for-sap-hana-dt-20"></a>Armazenamento VM para SAP HANA DT 2.0

De acordo com a orientação de práticas recomendadas de DT 2.0, a produtividade de e/s de disco mínimo é 50 MB/s por núcleo físico. Observando a especificação para os dois tipos de VM do Azure, que têm suporte para DT 2.0, o limite de taxa de transferência de e/s para a VM de disco máximo é:

- **E32sv3**:   768 MB/s, eliminado do cache, que significa uma razão de 48 MB/s por núcleo físico
- **M64-32ms**:  1.000 MB/s, eliminado do cache, que significa uma razão de 62,5 MB/s por núcleo físico

É necessário anexar vários discos do Azure para a VM de 2.0 DT e criar um RAID de software por meio de distribuição no nível do sistema operacional para atingir o limite máximo de taxa de transferência de disco por VM. Um único disco do Azure não pode fornecer a taxa de transferência para alcançar o limite máximo de VM. Armazenamento premium do Azure é obrigatório para executar o DT 2.0. 

- Para obter mais informações sobre os tipos de disco do Azure disponíveis, consulte [selecione um tipo de disco para VMs do Windows Azure IaaS](../../windows/disks-types.md).
- Para obter mais informações sobre como criar um RAID de software por meio de mdadm, consulte [configurar um RAID de software no Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid).
- Para obter mais informações sobre como configurar o LVM para criar um volume distribuído para taxa de transferência máxima, consulte [configurar o LVM em uma VM do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm).

Dependendo dos requisitos de tamanho, há opções diferentes para alcançar a taxa de transferência máxima de uma VM. Aqui estão as configurações de disco de volume de dados possíveis para cada tipo de VM de 2.0 DT atingir o limite de taxa de transferência da VM. A VM E32sv3 é considerada um nível de entrada para cargas de trabalho menores. Se não for rápida o suficiente, pode ser necessário redimensionar a VM para M64 32ms.

Como a VM M64 32ms tem uma grande quantidade de memória, a carga de e/s pode não alcançar o limite, especialmente para cargas de trabalho de leitura intensa. Menos discos em conjunto de distribuição podem ser suficientes, dependendo da carga de trabalho específica do cliente. Para ser seguro, as seguintes configurações de disco foram escolhidas para garantir a taxa de transferência máxima.


| SKU da VM | Configuração de disco 1 | Configuração do disco 2 | Configuração de disco 3 | Configuração de disco 4 | 5 de configuração de disco | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20 -> 3.5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20 -> 2.5 TB | 6 x P15 -> 1.5 TB | 


Especialmente se a carga de trabalho é leitura intensa, ativar a configuração "somente leitura" do cache de host do Azure, conforme recomendado para os volumes de dados do software de banco de dados pode melhorar o desempenho de e/s. Para o log de transações, o cache de disco do host do Azure deve ser "none". 

O ponto de partida, recomendamos que para o tamanho do volume de log é uma heurística de 15% do tamanho dos dados. Para criar o volume de log, use os tipos de disco do Azure diferente dependendo dos requisitos de custo e a taxa de transferência. Para o volume de log, alta taxa de transferência de e/s é necessária. 

Se você usar a VM digite M64 32ms, recomendamos que você habilite [acelerador de gravação](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator). Acelerador de gravação é um recurso do Azure que fornece a latência de gravação de disco ideais para o log de transações. Ele está disponível somente para a série M. Há alguns itens a serem considerados no entanto, como o número máximo de discos por tipo de VM. Para obter mais informações sobre o Acelerador de gravação, consulte [habilitar o Acelerador de gravação](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


A tabela a seguir mostra alguns exemplos para ajudar a dimensionar o volume de log.

| tipo de disco e tamanho do volume de dados | configuração 1 de tipo de disco e volume de log | volume do log e disco digite config 2 |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 -> 2.5 TB | 3 x P30 -> 3 TB |
| 6 x P15 -> 1.5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


Semelhante a expansão do SAP HANA, o diretório /hana/shared deve ser compartilhado entre a VM do SAP HANA e VM DT 2.0. É recomendável que você use a mesma arquitetura de expansão do SAP HANA por meio de VMs dedicadas, que atuam como um servidor NFS altamente disponível. Para fornecer um volume compartilhado de backup, use o design idêntico. Mas cabe a você decidir se a alta disponibilidade é necessária ou se ele for suficiente para usar uma VM dedicada com capacidade de armazenamento suficiente para atuar como um servidor de backup.



### <a name="links-to-dt-20-documentation"></a>Links para documentação DT 2.0 

- [Guia de atualização e instalação dinâmica de disposição em camadas do SAP HANA](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [Recursos e tutoriais em camadas dinâmicas do SAP HANA](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [SAP HANA dinâmico em camadas à prova de conceito](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [Aprimoramentos do SAP HANA 2.0 SPS 02](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Operações de implantação do SAP HANA em VMs do Azure
As seções a seguir descrevem algumas das operações relacionadas à implantação de sistemas SAP HANA  em VMs do Azure.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Operações de backup e restauração em VMs do Azure
Os documentos a seguir descrevem como fazer backup e restaurar a sua implantação do SAP HANA:

- [Visão geral de backup do SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Backup em nível de arquivo do SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Parâmetro de comparação de instantâneos de armazenamento do SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Inicie e reinicie as VMs que contêm o SAP HANA
Uma característica importante da nuvem pública do Azure é que você será cobrado somente pelos seus minutos de computação. Por exemplo, quando você desligar uma VM que executa o SAP HANA, você será cobrado somente pelos custos de armazenamento durante esse período. Outro recurso fica disponível quando você especificar endereços IP estáticos para as VMs na sua implantação inicial. Quando você reiniciar uma VM que contém o SAP HANA, a VM é reiniciada com seus endereços IP anteriores. 


### <a name="use-saprouter-for-sap-remote-support"></a>Use o SAProuter para suporte remoto SAP
Se você tiver uma conexão site a site entre suas localizações locais e o Azure e você estiver executando componentes SAP, você provavelmente já estiver executando o SAProuter. Nesse caso, siga estas etapas para suporte remoto:

- Manter o endereço IP estático e privado da VM que hospeda o SAP HANA na configuração do SAProuter.
- Configure o NSG da sub-rede que hospeda a VM do HANA para permitir o tráfego pela porta TCP/IP 3299.

Se você se conectar ao Azure através da internet e você não tiver um roteador SAP para a VM com o SAP HANA, instale o componente. Instale o SAProuter em uma VM separada na sub-rede de gerenciamento. 

A imagem a seguir mostra um esquema aproximado para a implantação do SAP HANA sem uma conexão site a site:

![Esquema de implantação aproximado para o SAP HANA sem conexão site a site e SAProuter](media/hana-vm-operations/hana-simple-networking3.PNG)

Certifique-se de instalar o SAProuter em uma VM separada e não em sua VM JumpBox. A VM separada precisa de um endereço IP estático. Para conectar o SAProuter ao SAProuter que é hospedado pela SAP, entre em contato com o SAP para um endereço IP. O SAProuter que é hospedado pela SAP é a contraparte da instância do SAProuter que você instala em sua VM. Use o endereço IP da SAP para configurar sua instância do SAProuter. Nas definições de configuração, a única porta necessária é a porta TCP 3299.

Para obter mais informações sobre como configurar e manter conexões de suporte remoto por meio do SAProuter, confira [documentação do SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Alta disponibilidade com o SAP HANA em VMs nativas do Azure
Se você executar o SUSE Linux Enterprise Server para aplicativos SAP 12 SP1 ou posterior, você pode estabelecer um cluster Pacemaker com dispositivos STONITH. Use os dispositivos para definir uma configuração de SAP HANA que usa replicação síncrona com replicação de sistema HANA e failover automático. Para obter mais informações sobre o procedimento de instalação, consulte [guia de alta disponibilidade do SAP HANA para máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).
