---
title: Configurar Alcance Global do Azure ExpressRoute | Microsoft Docs
description: Este artigo ajuda você a vincular circuitos do ExpressRoute para criar uma rede privada entre suas redes locais e habilitar o Alcance Global.
documentationcenter: na
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: mialdrid
ms.openlocfilehash: 67fbf9dc430d615efe3ef894add1a26bbce792bc
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50237971"
---
# <a name="configure-expressroute-global-reach-preview"></a>Configurar o Alcance Global do ExpressRoute (versão prévia)
Este artigo ajuda você a configurar o Alcance Global do ExpressRoute usando o PowerShell. Para obter mais informações, confira [Alcance Global do ExpressRouteRoute](expressroute-global-reach.md).
 
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
Você pode habilitar o Alcance Global do ExpressRoute entre quaisquer dois circuitos do ExpressRoute, desde que eles estejam localizados nos países com suporte e tenham sido criados em diferentes locais de emparelhamento. Se sua assinatura tiver os dois circuitos, será possível escolher qualquer circuito para executar a configuração nas seções a seguir. Se os dois circuitos estiverem em diferentes assinaturas do Azure, você precisará ter autorização de uma assinatura do Azure e precisará passar a chave de autorização quando executar o comando de configuração na outra assinatura.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Habilitar conectividade entre suas redes locais

Use os seguintes comandos para obter o circuito 1 e o circuito 2. Os dois circuitos estão na mesma assinatura.

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

Execute o comando a seguir no circuito 1 e passe a ID de emparelhamento privado do circuito 2.

> [!NOTE]
> A ID do emparelhamento privado tem esta aparência */subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering*
> 
>

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

> [!IMPORTANT]
> *-AddressPrefix* deve ser uma sub-rede IPv4 /29, por exemplo, "10.0.0.0/29". Usaremos endereços IP nesta sub-rede para estabelecer a conectividade entre os dois circuitos do ExpressRoute. Não é necessário usar endereços nessa sub-rede em suas redes virtuais do Azure ou em suas redes locais.
> 



Salve a configuração no circuito 1
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Quando a operação acima for concluída, você deverá ter conectividade entre suas redes locais nos dois lados por meio dos seus circuitos do ExpressRoute.

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

Salve a configuração no circuito 1
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Quando a operação acima for concluída, você deverá ter conectividade entre suas redes locais nos dois lados por meio dos seus circuitos do ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Obter e verificar a configuração

Use o seguinte comando para verificar a configuração no circuito em que a configuração foi realizada, por exemplo, circuito 1 no exemplo acima.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Se você simplesmente executar *$ckt1* no PowerShell, você verá *CircuitConnectionStatus* na saída. Ele informará se a conectividade foi estabelecida, mostrando "Conectado", ou não, mostrando "Desconectado". 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Desabilitar a conectividade entre suas redes locais

Para desabilitá-la, execute os comandos no circuito em que a configuração foi realizada, por exemplo, circuito 1 no exemplo acima.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Você pode executar a operação Get para verificar o status. 

Depois de concluir a operação anterior, você deixará de ter conectividade entre suas redes locais por meio de seus circuitos do ExpressRoute. 


## <a name="next-steps"></a>Próximas etapas
1. [Saiba mais sobre o Alcance Global do ExpressRoute](expressroute-global-reach.md)
2. [Verificar conectividade do ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Vincular circuito do ExpressRoute a uma rede virtual do Azure](expressroute-howto-linkvnet-arm.md)


