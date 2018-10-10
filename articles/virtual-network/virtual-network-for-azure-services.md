---
title: Rede virtual para os serviços do Azure | Microsoft Docs
description: Saiba mais sobre os benefícios da implantação de recursos em uma rede virtual. Recursos em redes virtuais podem se comunicar entre si e com recursos locais, sem que o tráfego atravesse a Internet.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial
ms.openlocfilehash: 06af3351f5669f5cd9aeeb9c4cb2168666476b52
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994625"
---
# <a name="virtual-network-integration-for-azure-services"></a>Integração de rede virtual para os serviços do Azure

A integração dos serviços do Azure a uma rede virtual do Azure permite o acesso privado ao serviço de máquinas virtuais ou de recursos de computação na rede virtual.
É possível integrar serviços do Azure em sua rede virtual às seguintes opções: implantando diretamente instâncias dedicadas do serviço em uma rede virtual. Os serviços podem ser acessados de maneira privada dentro da rede virtual e de redes locais.
Estendendo uma rede virtual ao serviço, por meio de pontos de extremidade de serviço. Pontos de extremidade de serviço permitem que os recursos de serviço individuais da rede virtual sejam protegidos.

Para integrar vários serviços do Azure à sua rede virtual, é possível combinar um ou mais dos padrões acima. Por exemplo, é possível implantar o HDInsight em sua rede virtual e proteger uma conta de armazenamento para a sub-rede do HDInsight por meio de pontos de extremidade de serviço.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Implantar os serviços do Azure em redes virtuais

Quando você implanta serviços dedicados do Azure em uma [rede virtual](virtual-networks-overview.md), é possível comunicar-se com os recursos de serviço de modo privado, por meio de endereços IP privados.

![Serviços implantados em uma rede virtual](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Implantar serviços em uma rede virtual fornece as seguintes funcionalidades:

- Recursos dentro da rede virtual podem se comunicar uns com os outros de modo privado, por meio de endereços IP privados. Por exemplo, transferindo dados diretamente entre o HDInsight e o SQL Server em execução em uma máquina virtual, na rede virtual.
- Recursos locais podem acessar recursos em uma rede virtual usando endereços IP privados por um [VPN Site a Site (Gateway de VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) ou [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Redes virtuais podem ser [emparelhadas](virtual-network-peering-overview.md) para permitir que recursos nas redes virtuais se comuniquem entre si usando endereços IP privados.
- Instâncias de serviço em uma rede virtual são totalmente gerenciadas pelo serviço do Azure, para monitorar a integridade das instâncias e fornecer a escala necessária com base na carga.
- Instâncias de serviço são implantadas em uma sub-rede em uma rede virtual. O acesso de rede de entrada e de saída deve ser aberto por meio de [Grupos de Segurança de Rede](security-overview.md#network-security-groups) para a sub-rede, segundo as diretrizes fornecidas pelos serviços.
- Opcionalmente, os serviços podem exigir uma [sub-rede delegada](virtual-network-manage-subnet.md#add-a-subnet) como um identificador explícito de que uma sub-rede pode hospedar um serviço específico. A delegação da sub-rede concede permissões explícitas ao serviço para criar recursos específicos do serviço na sub-rede.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Serviços que podem ser implantados em uma rede virtual

Cada serviço implantado diretamente em rede virtual tem requisitos específicos para o roteamento e os tipos de tráfego que devem ser permitidos em entrada e em saída nas sub-redes. Os serviços diferentes que podem ser implantados em uma rede virtual estão categorizados abaixo. Selecione o serviço específico na tabela para saber mais sobre ele e como integrá-lo à sua rede virtual. 


|Categoria|Serviço|
|-|-|
| Computação | Máquinas virtuais [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Serviço de nuvem](https://msdn.microsoft.com/library/azure/jj156091): apenas rede virtual (clássico)<br/> [Lote do Azure](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)  |
| Rede | [Gateway de Aplicativo – WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Firewall do Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Dispositivos de rede virtual](/windowsserverdocs/WindowsServerDocs/networking/sdn/manage/Use-Network-Virtual-Appliances-on-a-VN.md) 
|Dados|[RedisCache](../redis-cache/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Instância Gerenciada do Banco de Dados SQL do Azure](../sql-database/sql-database-managed-instance-vnet-configuration.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
Análise | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |
| Identidade | [Serviços de Domínio do Active Directory do Azure](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |
| Contêineres | [AKS (Serviço do Kubernetes do Azure)](../aks/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[ACI (Instância de Contêiner do Azure)](http://www.aka.ms/acivnet)<br/>[Mecanismo do Serviço de Contêiner do Azure](https://github.com/Azure/acs-engine) com o [plug-in](https://github.com/Azure/acs-engine/tree/master/examples/vnet) CNI de Rede Virtual do Microsoft Azure||
| Web | [Gerenciamento da API](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Ambiente do Serviço de Aplicativo](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[WebApps](http://www.aka.ms/WebAppsVNet)
| Hospedado*| [ANF (Azure NetApp Files)](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Módulo de segurança de hardware dedicado do Azure](http://www.aka.ms/azurededicatedhsm)
| | |
<br/>
*Normalmente, serviços especializados em execução no hardware especialmente concebido.



## <a name="service-endpoints-for-azure-services"></a>Pontos de extremidade de serviços do Azure

Alguns serviços do Azure não podem ser implantados em redes virtuais. Você pode restringir o acesso a alguns dos recursos do serviço apenas para sub-redes de rede virtual específicas, se você escolher, habilitando um ponto de extremidade de serviço de rede virtual.  Saiba mais sobre [ pontos de extremidade de serviços de rede virtual ](virtual-network-service-endpoints-overview.md) e os serviços para os quais os terminais podem ser ativados.