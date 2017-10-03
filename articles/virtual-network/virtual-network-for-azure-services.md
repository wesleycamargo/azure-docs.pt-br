---
title: "Rede virtual para os serviços do Azure | Microsoft Docs"
description: "Saiba mais sobre os benefícios da implantação de recursos em uma rede virtual. Recursos em redes virtuais podem se comunicar entre si e com recursos locais, sem que o tráfego atravesse a Internet."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/25/2017
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 36ff77a4205132d1dcc3b743f11791fc5e98e9e7
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---

# <a name="virtual-network-integration-for-azure-services"></a>Integração de rede virtual para os serviços do Azure

A integração de serviços do Azure a uma VNet (rede virtual) do Azure permite que os serviços sejam acessíveis de maneira privada a instâncias implantadas nessa VNet.

Você pode integrar serviços do Azure à sua rede virtual com as seguintes opções:
- Implantando diretamente instâncias dedicadas do serviço em uma VNet. As instâncias dedicadas desses serviços podem ser acessadas de maneira privada dentro da rede virtual e localmente.
- Estendendo uma rede virtual ao serviço, por meio de pontos de extremidade de serviço. Isso permite que os recursos de serviço individuais sejam protegidos para a rede virtual.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Implantar os serviços do Azure em redes virtuais

Você pode se comunicar com a maioria dos recursos do Azure pela Internet por meio de endereços IP públicos. Quando você implantar os serviços do Azure em uma [rede virtual](virtual-networks-overview.md), você poderá se comunicar com os recursos de serviço de modo privado, por meio de endereços IP privados.


![Serviços implantados em uma rede virtual](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Implantar serviços em uma rede virtual fornece as seguintes funcionalidades:

- Recursos dentro da rede virtual podem se comunicar uns com os outros de modo privado, por meio de endereços IP privados. Por exemplo, transferir dados diretamente entre o HDInsight e o SQL Server em execução em uma máquina virtual, na VNet.
- Recursos locais podem acessar recursos em uma rede virtual usando endereços IP privados por [VPN Site a Site (Gateway de VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) ou [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Redes virtuais podem ser [emparelhadas](virtual-network-peering-overview.md) para permitir que recursos nas redes virtuais se comuniquem entre si usando endereços IP privados.
- Instâncias de serviço em uma VNet são totalmente gerenciadas pelo serviço do Azure, para monitorar a integridade das instâncias e fornecer a escala necessária com base na carga.
- Instâncias de serviço são implantadas em uma sub-rede dedicada na VNet de um cliente. O acesso de entrada e de saída deve ser aberto por meio de NSGs (grupos de segurança de rede) para a sub-rede, segundo as diretrizes fornecidas pelos serviços.


### <a name="list-of-services-that-can-be-deployed-into-a-virtual-network"></a>Lista de serviços que podem ser implantados em uma rede virtual

Cada serviço implantado diretamente em rede virtual tem requisitos específicos para o roteamento e os tipos de tráfego que devem ser permitidos em entrada e em saída nas sub-redes. Consulte os links a seguir para obter mais detalhes: 
 
- Máquinas virtuais [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Service Fabric](../service-fabric/service-fabric-patterns-networking.md?toc=%2fazure%2fvirtual-network%2ftoc.json#existingvnet)
- [Conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Ambiente do Serviço de Aplicativo](../app-service-web/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [RedisCache](../redis-cache/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Gerenciamento da API](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Gateway de Aplicativo (interno)](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Mecanismo do Serviço de Contêiner do Azure](../container-service/container-service-intro.md?toc=%2fazure%2fvirtual-network%2ftoc.json): o serviço de contêiner do Azure cria uma rede virtual padrão. Você pode criar uma rede virtual personalizada para usar com o [Mecanismo do Serviço de Contêiner do Azure](https://github.com/Azure/acs-engine/tree/master/examples/vnet).
- [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json): somente rede virtual (clássico)
- [Lote do Azure](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration): somente rede virtual (clássico)
- [Serviços de nuvem](https://msdn.microsoft.com/library/azure/jj156091): somente rede virtual (clássico)

Você pode implantar um [Azure Load Balancer Interno](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para fazer o balanceamento de carga de muitos dos recursos na lista anterior. Em alguns casos, o serviço cria e implanta automaticamente um balanceador de carga quando você cria um recurso.

## <a name="service-endpoints-for-azure-services"></a>Pontos de extremidade de serviços do Azure

Alguns serviços do Azure não podem ser implantados em redes virtuais. Você pode restringir o acesso a alguns dos recursos do serviço apenas para sub-redes de rede virtual específicas, se você escolher, habilitando um ponto de extremidade de serviço de rede virtual. Saiba mais sobre [pontos de extremidade de serviço de rede virtual](virtual-network-service-endpoints-overview.md).

Atualmente, os pontos de extremidade de serviço têm suporte para os serviços abaixo: 
- Armazenamento do Azure: [Protegendo contas de Armazenamento do Azure para redes virtuais](https://docs.microsoft.com/azure/storage/common/storage-network-security)

- Banco de Dados SQL do Azure: [Protegendo Banco de dados SQL do Azure para redes virtuais](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)

## <a name="virtual-network-integration-across-multiple-azure-services"></a>Integração de rede virtual entre vários serviços do Azure

Você pode implantar um serviço do Azure em uma sub-rede em uma rede virtual e proteger recursos de serviço críticos para essa sub-rede. 

Por exemplo, você pode implantar o HDInsight em sua rede virtual e proteger uma conta de armazenamento para a sub-rede do HDInsight.






