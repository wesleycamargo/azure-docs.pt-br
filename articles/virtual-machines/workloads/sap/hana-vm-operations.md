---
title: "Operações do SAP HANA no Azure | Microsoft Docs"
description: "Operações do SAP HANA em VMs nativas do Azure"
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: juergent
manager: patfilot
editor: 
tags: azure-resource-manager
keywords: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2017
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0328bdc40429e1e82a76f290f5bde39089db0a9d
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/29/2017
---
# <a name="sap-hana-on-azure-operations-guide"></a>SAP HANA no guia de operações do Azure
Este guia fornece orientações para a operação de sistemas SAP HANA que foram implantados em Máquinas Virtuais do Azure. Este documento não pretende substituir documentações SAP padrão. Os guias e as notas SAP podem ser encontradas nos seguintes locais:

- [Guia de administração do SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Guias de Instalação do SAP](https://service.sap.com/instguides)
- [Nota SAP](https://sservice.sap.com/notes)

O pré-requisito é que você tenha um conhecimento básico dos diferentes componentes do Azure:

- [Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [VNets e Rede do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Armazenamento do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Demais documentos sobre o SAP NetWeaver e outros componentes SAP no Azure podem ser encontrados na seção [SAP no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) da [documentação do Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Considerações básicas sobre a instalação
### <a name="connecting-into-azure"></a>Conectar-se ao Azure
Conforme documentado em [Planejamento e implementação de Máquinas Virtuais do Azure para SAP NetWeaver] [planning-guide], há dois métodos básicos para se conectar a máquinas virtuais do Azure. 

- Conexão pela Internet e por pontos de extremidade públicos em uma VM de atalho ou VM que executa o SAP HANA
- Conexão por meio de [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) ou do Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/)

Para cenários de produção ou situações em que cenários de não produção alimentam cenários de produção em conjunto com o software SAP, você precisa ter uma conectividade site a site por meio de VPN ou ExpressRoute, conforme mostrado neste gráfico:

![Conectividade entre sites](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choice-of-azure-vm-types"></a>Escolha dos tipos de VM do Azure
Os tipos de VM do Azure que podem ser usados em cenários de produção podem ser pesquisados [aqui](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Para cenários de não produção, você pode escolher uma variedade maior de VMs do Azure nativas. No entanto, restrinja-se aos tipos de VM que estejam na [Nota SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). A implantação dessas VMs no Azure pode ser feita por meio de:

- Portal do Azure
- Cmdlets do Azure PowerShell
- CLI do Azure

Você também pode implantar uma plataforma completa de SAP HANA instalada nos serviços de máquina virtual do Azure por meio da [plataforma SAP Cloud](https://cal.sap.com/) conforme documentado [aqui](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h).

### <a name="choice-of-azure-storage"></a>Opção de armazenamento do Azure
O Azure fornece dois tipos de armazenamento principais adequados a VMs do Azure executando o SAP HANA

- [Armazenamento Standard do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Armazenamento Premium do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

O Azure oferece dois métodos de implantação para VHDs nos Armazenamentos do Azure Standard e Premium. Se o cenário geral permitir, é recomendável aproveitar implantações do [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/).

Para os tipos de armazenamento e SLAs exatos para esses tipos de armazenamento, verifique [esta documentação](https://azure.microsoft.com/pricing/details/managed-disks/)

É recomendável usar discos do Azure Premium para volumes /hana/data e /hana/log. Há suporte para criar um RAID LVM em vários discos de armazenamento Premium e usar os volumes RAID como /hana/data e /hana/log.

Uma configuração possível para diferentes tipos de VM comuns que os clientes usam por enquanto para hospedar o SAP HANA em VMs do Azure é mais ou menos assim:

| SKU da VM | RAM | hana/data e hana/log<br /> distribuídos com LVM ou MDADM | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | -- |
| E16v3 | 128GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S10 |
| E32v3 | 256 GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443 GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M64s | 1 TB | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1.7 TB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2 TB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128ms | 3.8 TB | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 5 x S30 |


### <a name="azure-networking"></a>Rede do Azure
Supondo que você tenha uma conectividade ExpressRoute ou VPN site a site no Azure, será necessário, no mínimo, uma [VNet do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) conectada por meio de um Gateway Virtual para o circuito do ExpressRoute ou VPN. O Gateway Virtual reside em uma sub-rede na rede virtual do Azure. Para instalar o HANA, você deve criar outras duas sub-redes na VNet. Uma sub-rede que hospeda as VMs que executam as instâncias do SAP HANA e outra sub-rede que executa eventuais VMs Jumpbox de Gerenciamento que pode hospedar o SAP HANA Studio ou outro software de gerenciamento.
Quando você instala as VMs que devem executar HANA, elas devem ter:

- Duas NICs virtuais instaladas que se conectam à sub-rede de gerenciamento, e uma NIC é usada para se conectar no local ou em outras redes para a instância do SAP HANA na VM do Azure.
- Endereços IP estáticos privados implantados para ambas as vNICs

Uma visão geral das diferentes possibilidades de atribuição de endereço IP pode ser encontrada [aqui](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

O roteamento de tráfego diretamente para a instância do SAP HANA ou para o jumpbox é direcionado por [grupos de segurança de rede do Azure](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg) que estão associados à sub-rede do HANA e à sub-rede de gerenciamento.

Em geral, o esquema de implantação aproximado fica mais ou menos assim:

![Esquema de implantação aproximado para o SAP HANA](media/hana-vm-operations/hana-simple-networking.PNG)


Se você implantar apenas SAP HANA no Azure, sem a necessidade de um site a site (VPN ou ExpressRoute para o Azure), acessará a instância do SAP HANA por um endereço IP público atribuído à VM do Azure que executa sua VM Jumpbox. Em um caso simples, você também depende de serviços DNS internos do Azure para resolver nomes de host. Especialmente ao usar endereços IP voltados ao público, é ideal usar grupos de segurança de rede do Azure para limitar as portas ou intervalos de endereços IP abertos que têm permissão para se conectar a sub-redes do Azure que executam ativos com endereços IP voltados ao público. O esquema do tipo de implantação seria semelhante ao seguinte:
  
![Esquema de implantação aproximado para o SAP HANA sem conexão site a site](media/hana-vm-operations/hana-simple-networking2.PNG)
 


## <a name="operations"></a>Operações
### <a name="backup-and-restore-operations-on-azure-vms"></a>Operações de backup e restauração em VMs do Azure
As possibilidades de Backup e Restauração do SAP HANA Backup estão descritas nestes documentos:

- [Visão geral de backup do SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Backup de nível de arquivo do SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Parâmetro de comparação de instantâneos de armazenamento do SAP HANA](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)



### <a name="start-and-restart-of-vms-containing-sap-hana"></a>Início e reinício de VMs que contêm SAP HANA
Uma das vantagens da nuvem pública do Azure é o fato de que você é cobrado apenas pelos minutos de computação que são gastos. Isso significa que se você desligar uma VM com o SAP HANA em execução, somente os custos de armazenamento serão cobrados durante esse período. Ao iniciar a VM com o SAP HANA novamente, a VM é reiniciada tem os mesmos endereços IP (se você implantou com endereços IP estáticos). 


### <a name="saprouter-enabling-sap-remote-support"></a>SAPRouter que habilita suporte remoto SAP
Se você tiver uma conexão site a site entre o local e o Azure e já estiver executando componentes SAP, é muito provável que você já tenha executado o SAProuter. Nesse caso, não há nada que você precise fazer com as instâncias do SAP HANA implantadas no Azure. Exceto manter o endereço IP estático e privado da VM que hospeda o HANA na configuração do SAPRouter e ter o NSG da sub-rede que hospeda a VM HANA adaptado (tráfego pela porta de TCP/IP porta 3299 permitido).

Se você estiver implantando o SAP HANA e se conectar ao Azure pela Internet e não tiver um roteador SAP instalado na rede virtual que executa uma VM com o SAP HANA, deverá instalar o SAPRouter em uma VM separada na sub-rede de gerenciamento, como mostrado aqui:


![Esquema de implantação aproximado para o SAP HANA sem conexão site a site e SAProuter](media/hana-vm-operations/hana-simple-networking3.PNG)

Você deve instalar o SAPRouter em uma VM separada e não em sua VM Jumpbox. A VM separada precisa de um endereço IP estático. Para conectar o SAPRouter ao SAPRouter que hospeda o SAP (equivalente da instância do SAPRouter em que você instala a VM), é necessário entrar em contato com o SAP para obter um endereço IP do SAP a fim de configurar sua instância do SAPRouter. A única porta necessária é a porta TCP 3299.
Para saber mais sobre como configurar e manter conexões de suporte remoto por meio do SAPRouter, confira esta [fonte SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Alta disponibilidade com o SAP HANA em VMs nativas do Azure
Executando o SUSE Linux 12 SP1 ou versão mais recente, você pode estabelecer um cluster Pacemaker com dispositivos STONITH para definir uma configuração do SAP HANA que usa replicação síncrona com Replicação do Sistema HANA e failover automático. O procedimento de instalação está descrito no artigo [Alta disponibilidade do SAP HANA em Máquinas Virtuais do Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-high-availability).

 










