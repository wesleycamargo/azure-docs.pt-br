---
title: Alcance Global do Azure ExpressRoute | Microsoft Docs
description: Este artigo ajuda você a vincular circuitos do ExpressRoute para criar uma rede privada entre suas redes locais e habilitar o Alcance Global.
documentationcenter: na
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 90f34da10e0193e31a0f70187463799013f52be8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966799"
---
# <a name="link-expressroute-circuits-to-enable-expressroute-global-reach-preview"></a>Vincular circuitos do ExpressRoute para habilitar o Alcance Global do ExpressRoute (versão prévia)

O ExpressRoute é uma maneira particular e resiliente de conectar suas redes locais ao Microsoft Cloud. Você pode acessar muitos serviços em nuvem da Microsoft, como o Azure, o Office 365 e o Dynamics 365, de seu datacenter ou rede corporativa particular. Por exemplo, você pode ter uma filial em São Francisco com um circuito do ExpressRoute no Vale do Silício e outra filial em Baltimore com um circuito do ExpressRoute em Washington DC. 

As duas filiais podem ter conectividade de alta velocidade com recursos do Azure no Leste e no Oeste dos EUA. No entanto, as filiais não podem trocar dados diretamente entre si. Em outras palavras, 10.0.1.0/24 pode trocar dados com 10.0.3.0/24 e 10.0.4.0/24, mas NÃO com 10.0.2.0/24.

![sem][1]

Com o **Alcance Global do ExpressRoute**, você pode vincular circuitos do ExpressRoute para criar uma rede de dados particular entre suas redes locais. No exemplo acima, com o acréscimo do Alcance Global do ExpressRoute, a filial de São Francisco (10.0.1.0/24) pode trocar dados diretamente com a filial de Baltimore (10.0.2.0/24) por meio dos circuitos existentes do ExpressRoute e da rede global da Microsoft. 

![com][2]

Atualmente, o Alcance Global do ExpressRoute tem suporte nos seguintes países:

* Estados Unidos
* Reino Unido 
* Japão

Os circuitos do ExpressRoute devem ser criados nos [locais de emparelhamento do ExpressRoute](expressroute-locations.md) nos países acima. Para habilitar o Alcance Global do ExpressRoute entre [diferentes regiões geopolíticas](expressroute-locations.md), seus circuitos devem ter o SKU Premium.


## <a name="before-you-begin"></a>Antes de começar

> [!IMPORTANT]
> Essa versão prévia pública é fornecida sem um SLA e não deve ser usada para cargas de trabalho de produção. Determinados recursos podem não ter suporte, podem ter restrição ou podem não estar disponíveis em todos os locais do Azure. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.
>

Antes de iniciar a configuração, você precisa verificar os requisitos a seguir.

* Instale a versão mais recente do Azure PowerShell. Confira [Instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps).
* Entenda os [fluxos de trabalho](expressroute-workflows.md) de provisionamento de circuito do ExpressRoute.
* Certifique-se de que seus circuitos do ExpressRoute estejam no estado Provisionado.
* Certifique-se de que o emparelhamento privado do Azure esteja configurado em seus circuitos do ExpressRoute.  

### <a name="log-into-your-azure-account"></a>Fazer logon em sua conta do Azure
Para iniciar a configuração, você precisa fazer logon em sua conta do Azure. 

Abra o console do PowerShell com privilégios elevados e conecte-se à sua conta. O comando solicitará as credenciais de logon de sua conta do Azure.  

```powershell
Connect-AzureRmAccount
```

Se você tiver várias assinaturas do Azure, verifique as assinaturas para a conta.

```powershell
Get-AzureRmSubscription
```

Especifique a assinatura que você deseja usar.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Identifique seus circuitos do ExpressRoute para configuração
Você pode habilitar o Alcance Global do ExpressRoute entre quaisquer dois circuitos do ExpressRoute, desde que eles estejam localizados nos países com suporte e tenham sido criados em diferentes locais de emparelhamento. Se sua assinatura tiver os dois circuitos, você poderá escolher qualquer circuito para executar a configuração nas seções a seguir. Se os dois circuitos estiverem em diferentes assinaturas do Azure, você precisará ter autorização de uma assinatura do Azure e precisará passar a chave de autorização quando executar o comando de configuração na outra assinatura.

## <a name="to-enable-connectivity-between-your-on-premises-networks"></a>Para habilitar a conectividade entre suas redes locais

Use os seguintes comandos para obter o circuito 1 e o circuito 2. Os dois circuitos estão na mesma assinatura.

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

Execute o comando a seguir no circuito 1 e passe a ID de emparelhamento privado do circuito 2.

Observe qual é a aparência da ID de emparelhamento privado:

`/subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering`


```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

*-AddressPrefix* deve ser uma sub-rede IPv4 /29, por exemplo, "10.0.0.0/29". Usaremos endereços IP nesta sub-rede para estabelecer a conectividade entre os dois circuitos do ExpressRoute. Você não deve usar endereços dessa sub-rede em suas redes virtuais do Azure ou em suas redes locais. 


Salve a configuração no circuito 1.

```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Quando a operação anterior for concluída, você deverá ter conectividade entre suas redes locais nos dois lados por meio de seus dois circuitos do ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Circuitos do ExpressRoute em diferentes assinaturas do Azure

Se os dois circuitos não estiverem na mesma assinatura do Azure, você precisará de autorização. Na configuração a seguir, a autorização é gerada na assinatura do circuito 2 e a chave de autorização é passada para o circuito 1.

Gere uma chave de autorização. 
```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```
Tome nota da ID de emparelhamento privado do circuito 2 e da chave de autorização.

Execute o seguinte comando no circuito 1. Passe a ID de emparelhamento privado do circuito 2 e a chave de autorização

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
```

Salve a configuração no circuito 1.

```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Quando as operações anteriores forem concluídas, você deverá ter conectividade entre suas redes locais nos dois lados por meio de seus dois circuitos do ExpressRoute.

## <a name="to-get-and-verify-the-configuration"></a>Para obter e verificar a configuração

Use o seguinte comando para verificar a configuração no circuito em que ela foi feita. Este exemplo usa o circuito 1 do exemplo anterior.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Basta executar *$ckt1* no PowerShell e você verá *CircuitConnectionStatus* na saída. Ele informará se a conectividade foi estabelecida, mostrando "Conectado", ou não, mostrando "Desconectado". 

## <a name="to-disable-connectivity-between-your-on-premises-networks"></a>Para desabilitar a conectividade entre suas redes locais

Para desabilitar, execute os comandos no circuito em que a configuração foi feita. Este exemplo usa o circuito 1 dos exemplos anteriores.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Você pode executar a operação Get para verificar o status. 

Depois de concluir a operação anterior, você deixará de ter conectividade entre suas redes locais por meio de seus circuitos do ExpressRoute. 

## <a name="next-steps"></a>Próximas etapas
1. [Saiba mais sobre o Alcance Global do ExpressRoute](expressroute-faqs.md#globalreach)
2. [Verificar conectividade do ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Vincular circuito do ExpressRoute a uma rede virtual do Azure](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "diagrama sem alcance global"
[2]: ./media/expressroute-global-reach/2.png "diagrama com alcance global"