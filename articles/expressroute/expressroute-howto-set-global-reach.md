---
title: 'Configure o Alcance Global – ExpressRoute: Azure | Microsoft Docs'
description: Este artigo ajuda você a vincular circuitos do ExpressRoute para criar uma rede privada entre suas redes locais e habilitar o Alcance Global.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 3df107f8854469b50c5e8483515388b5c93fb244
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53383265"
---
# <a name="configure-expressroute-global-reach-preview"></a>Configurar o Alcance Global do ExpressRoute (versão prévia)
Este artigo ajuda você a configurar o Alcance Global do ExpressRoute usando o PowerShell. Para obter mais informações, confira [Alcance Global do ExpressRouteRoute](expressroute-global-reach.md).
 
## <a name="before-you-begin"></a>Antes de começar
> [!IMPORTANT]
> Essa versão prévia pública é fornecida sem um Contrato de Nível de Serviço e não deve ser usada para cargas de trabalho de produção. Talvez alguns recursos não tenham suporte, tenham recursos restritos ou não estejam disponíveis em todos os locais do Azure. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 


Antes de iniciar a configuração, confirme o seguinte:

* Se você instalou a versão mais recente do Azure PowerShell. Para obter mais informações, consulte [Instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps).
* Que você entende os [fluxos de trabalho](expressroute-workflows.md) de provisionamento de circuito do ExpressRoute.
* Que seus circuitos do ExpressRoute estão no estado provisionado.
* Que o emparelhamento privado do Azure está configurado em seus circuitos do ExpressRoute.  

### <a name="sign-in-to-your-azure-account"></a>Entre na sua conta do Azure
Para iniciar a configuração, entrar na sua conta do Azure. 

Abra o console do PowerShell com privilégios elevados e conecte-se à sua conta. O comando solicita as credenciais de conexão para sua conta do Microsoft Azure.  

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
Você pode habilitar o Alcance Global do ExpressRoute entre quaisquer dois circuitos do ExpressRoute, desde que eles estejam localizados nos países com suporte e tenham sido criados em diferentes locais de emparelhamento. Se sua assinatura tiver os dois circuitos, será possível escolher qualquer circuito para executar a configuração nas seções a seguir. 

Se os dois circuitos estiverem em diferentes assinaturas do Azure, você precisará de autorização de uma assinatura do Azure. Em seguida, você passa a chave de autorização quando executa o comando de configuração em outra assinatura do Azure.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Habilitar conectividade entre suas redes locais

Use os seguintes comandos para obter o circuito 1 e o circuito 2. Os dois circuitos estão na mesma assinatura.

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

Execute o comando a seguir no circuito 1 e passe a ID de emparelhamento privado do circuito 2. Ao executar o comando, observe o seguinte:

* A ID de emparelhamento privado é semelhante ao exemplo a seguir: 

  ```
  /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
  ```
* *-AddressPrefix* deve ser uma sub-rede IPv4 /29, por exemplo, "10.0.0.0/29". Usamos endereços IP nesta sub-rede para estabelecer a conectividade entre os dois circuitos do ExpressRoute. Você não deve usar os endereços nessa sub-rede em suas redes virtuais do Azure nem em sua rede local.

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

Salve a configuração no circuito 1 da seguinte maneira:
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
Anote a ID de emparelhamento privada do circuito 2, bem como a chave de autorização.

Execute o seguinte comando no circuito 1. Passe a ID de emparelhamento privada do circuito 2 e a chave de autorização.
```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
```

Salve a configuração no circuito 1.
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Quando a operação anterior for concluída, você deverá ter conectividade entre suas redes locais nos dois lados por meio de seus dois circuitos do ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Obter e verificar a configuração

Use o seguinte comando para verificar a configuração no circuito em que a configuração foi realizada (por exemplo, circuito 1 no exemplo anterior).

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Se você simplesmente executar *$ckt1* no PowerShell, verá *CircuitConnectionStatus* na saída. Ele informa se a conectividade foi estabelecida, "Conectada" ou "Desconectada". 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Desabilitar a conectividade entre suas redes locais

Para desabilitar a conectividade, execute os comandos com relação ao circuito em que a configuração foi feita (por exemplo, o circuito 1 no exemplo anterior).

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
3. [Vincular um circuito do ExpressRoute a uma rede virtual do Azure](expressroute-howto-linkvnet-arm.md)


