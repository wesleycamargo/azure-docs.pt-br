---
title: "Tipos de endereços IP no Azure | Microsoft Docs"
description: "Saiba mais sobre endereços IP públicos e privados no Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 610b911c-f358-4cfe-ad82-8b61b87c3b7e
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/16/2017
ms.author: jdial
ms.openlocfilehash: 3840ed000d5a9fe5d3c8fd01c061bf13674c0ce5
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="ip-address-types-and-allocation-methods-in-azure"></a>Tipos de endereço IP e métodos de alocação no Azure

Você pode atribuir endereços IP aos recursos do Azure para se comunicar com outros recursos do Azure, sua rede local e a Internet. Há dois tipos de endereços IP que você pode usar no Azure:

* **Endereços IP públicos**: usados para comunicação com a Internet, incluindo serviços do Azure voltados para o público.
* **Endereços IP privados**: usados para comunicação em uma VNet (rede virtual) do Azure e na sua rede local quando você usa um gateway de VPN ou circuito de ExpressRoute para estender sua rede para o Azure.

> [!NOTE]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  Este artigo aborda usando o modelo de implantação do Gerenciador de Recursos, que a Microsoft recomenda para a maioria das novas implantações em vez de do [modelo de implantação clássico](virtual-network-ip-addresses-overview-classic.md).
> 

Se estiver familiarizado com o modelo de implantação clássico, verifique as [diferenças de endereçamento IP entre o clássico e o Gerenciador de Recursos](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments).

## <a name="public-ip-addresses"></a>Endereços IP públicos

Endereços IP públicos permitem recursos de Internet para comunicar a entrada para recursos do Azure. Endereços IP públicos também habilitam recursos do Azure para comunicar a saída à Internet e serviços do Azure voltados ao público com um endereço IP atribuído ao recurso. O endereço é dedicado para o recurso, até que ele seja desatribuído por você. Se um endereço IP público não está atribuído a um recurso, o recurso ainda pode comunicar a saída à Internet, mas o Azure atribui dinamicamente um endereço IP disponível que não é dedicado ao recurso. Para obter mais informações sobre conexões de saída no Azure, consulte [Entender as conexões de saída](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

No Gerenciador de recursos do Azure, um endereço [IP público](virtual-network-public-ip-address.md) é um recurso com as próprias propriedades. Alguns dos recursos aos quais você pode associar um recurso de endereço IP público são:

* Interfaces de rede de máquina virtual
* Balanceadores de carga para Internet
* Gateways VPN
* Application gateways

### <a name="ip-address-version"></a>Versão do endereço IP

Os endereços IP públicos são criados com um endereço IPv4 ou IPv6. Os endereços IPv6 públicos só podem ser atribuídos a balanceadores de carga voltados para a Internet.

### <a name="sku"></a>SKU

Os endereços IP públicos são criados com um dos seguintes SKUs:

#### <a name="basic"></a>Basic

Todos os endereços IP públicos criados antes da introdução dos SKUs são endereços IP públicos do SKU Básico. Com a introdução dos SKUs, você tem a opção de especificar qual SKU o endereço IP público deve ser. Endereços do SKU Básico são:

- Atribuídos com o método de alocação estática ou dinâmica.
- Atribuídos a qualquer recurso do Azure ao qual possa ser atribuído um endereço IP público, tal como adaptadores de rede, Gateways de VPN, Gateways de Aplicativo e balanceadores de carga voltados para a Internet.
- Podem ser atribuídos a uma zona específica.
- Sem redundância de zona. Para saber mais sobre as zonas de disponibilidade, confira [Visão geral das zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="standard"></a>Standard

Endereços IP públicos do SKU Padrão são:

- Atribuídos somente com o método de alocação estática.
- Atribuídos a adaptadores de rede ou balanceadores de carga padrão voltados para a Internet. Para saber mais sobre os SKUs do balanceador de carga do Azure, confira [SKU padrão do balanceador de carga do Azure](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Com redundância de zona por padrão. Podem ser criados como zonal e com presença garantida em uma zona de disponibilidade específica.  Para saber mais sobre as zonas de disponibilidade, confira [Visão geral das zonas de disponibilidade](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
 
> [!NOTE]
> Quando você atribui um endereço IP público de SKU padrão ao adaptador de rede de uma máquina virtual, deve permitir explicitamente o tráfego pretendido com um [grupo de segurança de rede](security-overview.md#network-security-groups).  A comunicação com o recurso falha até que você crie e associe um grupo de segurança de rede e permita o tráfego desejado explicitamente.

O SKU padrão está em versão prévia. Antes de criar um endereço IP público do SKU padrão, você deve primeiro se registrar para a versão prévia e criar o endereço em um local com suporte. Para se registrar para a versão prévia, confira [Registrar-se para a versão prévia do SKU padrão](virtual-network-public-ip-address.md#register-for-the-standard-sku-preview). Para obter uma lista de localizações com suporte (regiões), confira [Disponibilidade da região](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region-availability) e monitore a página [Atualizações de Rede Virtual do Azure](https://azure.microsoft.com/updates/?product=virtual-network) para obter suporte de região adicional.


### <a name="allocation-method"></a>Método de alocação

Há dois métodos de alocar um endereço IP para um recurso de endereço IP público - *dinâmico* ou *estático*. O método de alocação padrão é *dinâmico*, em que o endereço IP **não** é alocado no momento de sua criação. Em vez disso, o endereço IP público é alocado quando você inicia (ou cria) o recurso associado (como um balanceador de carga ou VM). O endereço IP é liberado quando você para (ou exclui) o recurso. Depois de ser liberado do recurso A, por exemplo, o endereço IP pode ser atribuído a um recurso diferente. Se o endereço IP for atribuído a um recurso diferente enquanto o recurso A for interrompido, quando você reiniciar o recurso A, um endereço IP diferente será atribuído.

Para garantir que o endereço IP para o recurso associado permaneça o mesmo, você pode definir o método de alocação explicitamente como *estático*. Um endereço IP estático é atribuído imediatamente. O endereço é liberado apenas quando você exclui o recurso ou altera seu método de alocação para *dinâmico*.

> [!NOTE]
> Mesmo quando você define o método de alocação para *estático*, não é possível especificar o endereço IP real atribuído ao recurso de endereço IP público. O Azure atribui o endereço IP de um pool de endereços IP disponíveis no local do Azure onde o recurso é criado.
>

Os endereços IP públicos estáticos são comumente usados nas seguintes situações:

* Quando você precisa atualizar regras de firewall para se comunicar com os recursos do Azure.
* Resolução de nome DNS, em que uma alteração no endereço IP exigiria a atualização de registros A.
* Seus recursos do Azure comunicam-se com outros aplicativos ou serviços que usam um endereço IP baseado em um modelo de segurança.
* Você usa certificados SSL vinculados a um endereço IP.

> [!NOTE]
> O Azure aloca endereços IP públicos de um intervalo exclusivo para cada região do Azure. Para obter mais detalhes, confira [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653).
>

### <a name="dns-hostname-resolution"></a>Resolução de nome de host DNS
Você pode especificar um rótulo de nome de domínio DNS para um recurso IP público que cria um mapeamento para *domainnamelabel*.*location*.cloudapp.azure.com para o endereço IP público nos servidores DNS gerenciados pelo Azure. Por exemplo, se você criar um recurso IP público com **contoso** como um *domainnamelabel* no *local* do Azure no **Oeste dos EUA**, o nome de domínio totalmente qualificado (FQDN) **contoso.westus.cloudapp.azure.com** será resolvido para o endereço IP público do recurso. Você pode usar o FQDN para criar um registro CNAME do domínio personalizado apontando para o endereço IP público no Azure. Em vez de ou além de usar o rótulo de nome DNS com o sufixo padrão, você pode usar o serviço DNS do Azure para configurar um nome DNS com um sufixo personalizado que seja resolvido para o endereço IP público. Para obter mais informações, consulte [Usar o DNS do Azure com o endereço IP público do Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).

> [!IMPORTANT]
> Cada rótulo de nome do domínio criado deve ser exclusivo dentro de seu local do Azure.  
>

### <a name="virtual-machines"></a>Máquinas virtuais

Você pode associar um endereço IP público a uma máquina virtual [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) atribuindo-o ao seu **adaptador de rede**. Você pode atribuir um endereço IP público dinâmico ou estático a uma máquina virtual. Saiba mais lendo o artigo [Atribuindo endereços IP a adaptadores de rede](virtual-network-network-interface-addresses.md).

### <a name="internet-facing-load-balancers"></a>Balanceadores de carga para Internet

Você pode associar um endereço IP público criado com um dos [SKUs](#SKU) ao [Azure Load Balancer](../load-balancer/load-balancer-overview.md), atribuindo-o à configuração de **front-end** do balanceador de carga. O endereço IP público serve como um endereço IP virtual de balanceamento de carga (VIP). Você pode atribuir um endereço IP público estático ou dinâmico a um front-end de balanceador de carga. Você também pode atribuir vários endereços IP públicos a um front-end de balanceador de carga, que permite cenários [multi-VIP](../load-balancer/load-balancer-multivip-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) como um ambiente de multilocatário com sites baseados em SSL. Para saber mais sobre os SKUs do balanceador de carga do Azure, confira [SKU padrão do balanceador de carga do Azure](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="vpn-gateways"></a>Gateways VPN

Um [Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) conecta uma rede virtual do Azure a outras redes virtuais do Azure ou a uma rede local. Um endereço IP público é atribuído ao Gateway de VPN para permitir que ele se comunique com a rede remota. Você pode atribuir apenas um endereço IP público *dinâmico* a um gateway de VPN.

### <a name="application-gateways"></a>Application gateways

Você pode associar um endereço IP público do Azure [Application Gateway](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json), atribuindo-o à configuração de **front-end** do gateway. Esse endereço IP público serve como um VIP com balanceamento de carga. Você pode atribuir apenas um endereço IP público *dinâmico* a uma configuração de front-end do Application Gateway.

### <a name="at-a-glance"></a>Imediato
A tabela a seguir mostra a propriedade específica por meio da qual um endereço IP público pode ser associado a um recurso de nível superior e os métodos possíveis de alocação (dinâmico ou estático) que podem ser usados.

| Recurso de nível superior | Associação de Endereço IP | dinâmico | estático |
| --- | --- | --- | --- |
| Máquina virtual |interface de rede |Sim |Sim |
| Balanceador de carga voltado para a Internet |Configuração de front-end |Sim |Sim |
| Gateway de VPN |Configuração de IP do gateway |Sim |Não |
| Gateway de Aplicativo |Configuração de front-end |Sim |Não |

## <a name="private-ip-addresses"></a>Endereços IP privados
Endereços IP privados permitem que os recursos do Azure comuniquem-se com outros recursos de uma [rede virtual](virtual-networks-overview.md) , ou na rede local por meio de um gateway de VPN ou circuito de ExpressRoute, sem usar um endereço IP acessível pela Internet.

No modelo de implantação do Azure Resource Manager, um endereço IP privado é associado aos seguintes tipos de recursos do Azure:

* Interfaces de rede de máquina virtual
* ILBs (balanceadores de carga internos)
* Application gateways

### <a name="ip-address-version"></a>Versão do endereço IP

Os endereços IP privados são criados com um endereço IPv4 ou IPv6. Os endereços IPv6 privados podem ser atribuídos apenas com o método de alocação dinâmica. Você não pode se comunicar entre endereços IPv6 em uma rede virtual. Você pode se comunicar para um endereço IPv6 da Internet por meio de um balanceador de carga voltado para a Internet. Confira [Criar um Balanceador de carga voltado para a Internet com IPv6](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para obter detalhes.

### <a name="allocation-method"></a>Método de alocação

Um endereço IP privado é alocado no intervalo de endereços de sub-rede da rede virtual em que um recurso é implantado. O Azure reserva os primeiros quatro endereços em cada intervalo de endereços de sub-rede, de modo que os endereços não podem ser atribuídos a recursos. Por exemplo, se o intervalo de endereços da sub-rede for 10.0.0.0/16, endereços 10.0.0.0-10.0.0.3 não podem ser atribuídos a recursos. Endereços IP no intervalo de endereços da sub-rede só podem ser atribuídos a um recurso por vez. 

Há dois métodos para alocar um endereço IP privado:

- **Dinâmico**: o Azure atribui o próximo endereço IP disponível não atribuído ou não reservado no intervalo de endereços da sub-rede. Por exemplo, o Azure atribui o 10.0.0.10 para um novo recurso se endereços 10.0.0.4-10.0.0.9 já estiverem atribuídos a outros recursos. Dinâmico é o método de alocação padrão. Uma vez atribuído, os endereços IP dinâmicos só são liberados se um adaptador de rede é excluído, atribuído a uma sub-rede diferente na mesma rede virtual ou se o método de alocação é alterado para estático e um endereço IP diferente é especificado. Por padrão, o Azure atribui o endereço anterior atribuído dinamicamente como o endereço estático quando você altera o método de alocação de dinâmico para estático.
- **Estático**: você seleciona e atribui qualquer endereço IP disponível não atribuído ou não reservado no intervalo de endereços da sub-rede. Por exemplo, se um intervalo de endereços da sub-rede for 10.0.0.0/16 e os endereços 10.0.0.4-10.0.0.9 já estiverem atribuídos a outros recursos, você pode atribuir qualquer endereço entre 10.0.0.10 - 10.0.255.254. Os endereços estáticos só são liberados se um adaptador de rede é excluído. Se você alterar o método de alocação para estático, o Azure atribui dinamicamente o endereço IP estático atribuído anteriormente como o endereço dinâmico, mesmo que o endereço não seja o próximo endereço disponível no intervalo de endereços da sub-rede. O endereço também será alterado se o adaptador de rede for atribuído a uma sub-rede diferente na mesma rede virtual. No entanto, para atribuir o adaptador de rede a outra sub-rede, primeiro você deve alterar o método de alocação de estático para dinâmico. Depois de atribuir o adaptador de rede a uma sub-rede diferente, você poderá alterar o método de alocação novamente para estático e atribuir um endereço IP do intervalo de endereços da nova sub-rede.

### <a name="virtual-machines"></a>Máquinas virtuais

Um ou mais endereços IP privados são atribuídos a uma ou mais **interfaces de rede** de uma máquina virtual [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Você pode especificar o método de alocação como dinâmico ou estático para um endereço IP privado.

#### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>Resolução do nome do host DNS interno (para máquinas virtuais)

Todas as máquinas virtuais do Azure são configuradas com [servidores DNS gerenciados pelo Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) por padrão, a menos que você explicitamente configure servidores DNS personalizados. Esses servidores DNS fornecem resolução de nomes interna para máquinas virtuais que residem na mesma rede virtual.

Quando você cria uma máquina virtual, um mapeamento para o nome de host para seu endereço IP privado é adicionado aos servidores DNS gerenciados pelo Azure. Se uma máquina virtual tiver várias interfaces de rede, ou várias configurações de IP para uma interface de rede, o nome do host será mapeado para o endereço IP privado da configuração IP primária da interface de rede principal.

Máquinas virtuais configuradas com os servidores DNS gerenciados pelo Azure são capazes de resolver os nomes de host de todas as máquinas virtuais na mesma rede virtual para seus endereços IP privados. Para resolver os nomes de host das máquinas virtuais nas redes virtuais conectadas, você deve usar um servidor DNS personalizado.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Balanceadores de carga internos (ILB) e gateways de aplicativo

Você pode atribuir um endereço IP privado para a configuração de **front-end** de um [Balanceador de Carga Interno do Azure](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (ILB) ou um [Gateway de Aplicativo do Azure](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Esse endereço IP privado serve como ponto de extremidade interno, acessível somente aos recursos dentro da sua rede virtual e de redes remotas conectadas à rede virtual. Você pode atribuir um endereço IP privado dinâmico ou estático à configuração de front-end.

### <a name="at-a-glance"></a>Imediato
A tabela a seguir mostra a propriedade específica por meio da qual um endereço IP privado pode ser associado a um recurso de nível superior e os métodos possíveis de alocação (dinâmico ou estático) que podem ser usados.

| Recurso de nível superior | Associação de Endereço IP | dinâmico | estático |
| --- | --- | --- | --- |
| Máquina virtual |interface de rede |Sim |Sim |
| Balanceador de carga |Configuração de front-end |Sim |Sim |
| Gateway de Aplicativo |Configuração de front-end |Sim |Sim |

## <a name="limits"></a>Limites
Os limites impostos sobre o endereçamento IP são indicados em todo o conjunto de [limites de rede](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) no Azure. Os limites são por região e por assinatura. Você pode [entrar em contato com o suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar os limites padrão até os limites máximos com base nas necessidades de sua empresa.

## <a name="pricing"></a>Preços
Endereços IP públicos podem ter um custo nominal. Para saber mais sobre preços de endereços IP no Azure, confira a página [Preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="next-steps"></a>Próximas etapas
* [Implantar uma VM com um IP público estático usando o Portal do Azure](virtual-network-deploy-static-pip-arm-portal.md)
* [Implantar uma VM com um IP público estático usando um modelo](virtual-network-deploy-static-pip-arm-template.md)
* [Implantar uma VM com um endereço IP privado estático](virtual-networks-static-private-ip-arm-pportal.md)
