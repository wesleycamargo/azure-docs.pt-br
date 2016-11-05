---
title: Adaptadores de rede | Microsoft Docs
description: Saiba mais sobre adaptadores de rede no Azure Resource Manager.
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: jdial

---
# <a name="network-interfaces"></a>Interfaces de rede
Uma NIC (adaptador de rede) é a interconexão entre uma VM (máquina virtual) e a rede do software subjacente. Este artigo explica o que é um adaptador de rede e como ele é usado no modelo de implantação do Azure Resource Manager.

A Microsoft recomenda a implantação de novos recursos usando o modelo de implantação do Resource Manager, mas você também pode implantar VMs com conectividade de rede no modelo de implantação [clássico](virtual-network-ip-addresses-overview-classic.md) . Se você está familiarizado com o modelo clássico, há diferenças importantes na rede da VM no modelo de implantação do Resource Manager. Saiba mais sobre as diferenças, lendo o artigo [Rede da máquina virtual – clássico](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments) .

No Azure, um adaptador de rede:

1. É um recurso que pode ser criado, excluído e tem suas próprias configurações.
2. Deve estar conectado a uma sub-rede em uma VNet (Rede Virtual do Azure) quando ele é criado. Se você não está familiarizado com VNets, saiba mais sobre elas lendo o artigo [Visão geral da rede virtual](virtual-networks-overview.md) . A NIC deve ser conectada a uma VNet que existe nas mesmas [localização](https://azure.microsoft.com/regions) e [assinatura](../azure-glossary-cloud-terminology.md#subscription) do Azure que a NIC. Depois que uma NIC é criada, você pode alterar a sub-rede à qual ela está conectada, mas você não pode alterar a VNet à qual ela está conectada.
3. Tem um nome atribuído a ele que não pode ser alterado depois que a NIC é criada. O nome deve ser único dentro [grupo de recursos](../resource-group-overview.md#resource-groups)do Azure, mas não precisa ser único dentro da assinatura, da localização do Azure onde ela foi criada ou da VNet à qual ele está conectada. Várias NICs normalmente são criadas dentro de uma assinatura do Azure. É recomendável criar uma convenção de nomenclatura que facilite o gerenciamento de várias NICs do que usar os nomes padrão. Consulte o artigo [Convenções de nomenclatura recomendadas para recursos do Azure](../guidance/guidance-naming-conventions.md) para obter sugestões.
4. Pode estar conectada a uma VM, mas só pode ser anexada a uma única VM existente na mesma localização que a NIC.
5. Tem um endereço MAC, que é mantido com a NIC pelo tempo que ela permanecer conectada a uma VM. O endereço MAC será mantido se a VM for reiniciada (de dentro do sistema operacional) ou interrompida (desalocada) e tiver começado a usar o Portal do Azure, Azure PowerShell ou a interface de linha de comando do Azure. Se ele for desconectado de uma VM e conectado a uma VM diferente, a NIC receberá um endereço MAC diferente. Se a NIC for excluída, o endereço MAC será atribuído a outras NICs.
6. Deve ter um endereço IP **IPv4** *privado* dinâmico ou estático atribuído a ele.
7. Pode ter um recurso de endereço IP público associado a ele.
8. Dá suporte à rede acelerada com virtualização de E/S de raiz única (SR-IOV) para tamanhos de VM específicos que executam versões específicas do sistema operacional do Microsoft Windows Server. Para saber mais sobre esse recurso de PREVIEW, leia o artigo [Rede acelerada para uma máquina virtual](virtual-network-accelerated-networking-powershell.md) .
9. Poderá receber tráfego não destinado a endereços IP privados atribuídos a ele se o encaminhamento de IP estiver habilitado para a NIC. Se uma VM estiver executando o software de firewall, por exemplo, ele encaminhará pacotes não destinados a seus próprios endereços IP. A VM ainda deverá executar o software capaz de rotear ou encaminhar o tráfego, mas, para isso, o encaminhamento IP deverá estar habilitado para uma NIC.
10. Geralmente, ele é criado no mesmo grupo de recursos ao qual a VM está conectada ou a mesma VNet à qual ele está conectado, embora isso não seja obrigatório.

Várias NICs podem ser conectadas à mesma VM, desde que o tamanho da VM dê suporte a isso. Para saber mais sobre quais tamanhos de VM comportam várias NICs, leia os artigos [Tamanhos de VM do Windows Server](../virtual-machines/virtual-machines-windows-sizes.md) ou [Tamanhos de VM do Linux](../virtual-machines/virtual-machines-linux-sizes.md).

## <a name="next-steps"></a>Próximas etapas
* Saiba como criar uma VM com uma única NIC lendo o artigo [Criar uma VM](../virtual-machines/virtual-machines-windows-hero-tutorial.md) .
* Saiba como criar uma VM com várias NICs lendo o artigo [Implantar uma VM com várias NICs](virtual-network-deploy-multinic-arm-ps.md) .
* Saiba como criar uma NIC com várias configurações de IP lendo o artigo [Vários endereços IP para máquinas virtuais do Azure](virtual-network-multiple-ip-addresses-powershell.md) .

<!--HONumber=Oct16_HO2-->


