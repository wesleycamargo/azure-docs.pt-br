---
title: Configurar Alcance Global do Azure ExpressRoute usando a CLI do Azure| Microsoft Docs
description: Este artigo ajuda você a vincular circuitos do ExpressRoute para criar uma rede privada entre suas redes locais e habilitar o Alcance Global.
documentationcenter: na
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: cherylmc
ms.openlocfilehash: 9d41ab26876d464187466f566bbfafc4861c799d
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52333201"
---
# <a name="configure-expressroute-global-reach-using-azure-cli-preview"></a>Configurar Alcance Global do Azure ExpressRoute Usando a CLI do Azure (visualização)
Este artigo ajuda você a configurar o Alcance Global do ExpressRoute usando a CLI do Azure. Para obter mais informações, confira [Alcance Global do ExpressRouteRoute](expressroute-global-reach.md).
 
## <a name="before-you-begin"></a>Antes de começar
> [!IMPORTANT]
> Essa versão prévia pública é fornecida sem um SLA e não deve ser usada para cargas de trabalho de produção. Determinados recursos podem não ter suporte, podem ter restrição ou podem não estar disponíveis em todos os locais do Azure. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.
> 


Antes de iniciar a configuração, você precisa verificar os requisitos a seguir.

* Instale a versão mais recente do CLI do Azure. Consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli) e [Introdução à CLI do Azure](/cli/azure/get-started-with-azure-cli).
* Entenda os [fluxos de trabalho](expressroute-workflows.md) de provisionamento de circuito do ExpressRoute.
* Certifique-se de que seus circuitos do ExpressRoute estejam no estado Provisionado.
* Certifique-se de que o emparelhamento privado do Azure esteja configurado em seus circuitos do ExpressRoute.  

### <a name="log-into-your-azure-account"></a>Fazer logon em sua conta do Azure
Para iniciar a configuração, você precisa fazer logon em sua conta do Azure. O comando abrirá seu navegador padrão e solicitará a credencial de logon para a sua conta do Azure.  

```azurecli
az login
```

Se você tiver várias assinaturas do Azure, verifique as assinaturas para a conta.

```azurecli
az account list
```

Especifique a assinatura que você deseja usar.

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Identifique seus circuitos do ExpressRoute para configuração
Você pode habilitar o Alcance Global do ExpressRoute entre quaisquer dois circuitos do ExpressRoute, desde que eles estejam localizados nos países com suporte e tenham sido criados em diferentes locais de emparelhamento. Se sua assinatura tiver os dois circuitos, será possível escolher qualquer circuito para executar a configuração nas seções a seguir. Se os dois circuitos estiverem em diferentes assinaturas do Azure, você precisará ter autorização de uma assinatura do Azure e precisará passar a chave de autorização quando executar o comando de configuração na outra assinatura.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Habilitar conectividade entre suas redes locais

Execute a CLI a seguir para se conectar com os circuitos do ExpressRoute.

> [!NOTE]
> *circuito de par* deve ser a ID de recurso completo, por exemplo,
> ```
> */subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}*
> ```
> 

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

> [!IMPORTANT]
> *-AddressPrefix* deve ser uma sub-rede IPv4 /29, por exemplo, "10.0.0.0/29". Usaremos endereços IP nesta sub-rede para estabelecer a conectividade entre os dois circuitos do ExpressRoute. Não é necessário usar endereços nessa sub-rede em suas redes virtuais do Azure ou em suas redes locais.
> 

A saída da CLI se parece com o seguinte.

```azurecli
{
  "addressPrefix": "<__.__.__.__/29>",
  "authorizationKey": null,
  "circuitConnectionStatus": "Connected",
  "etag": "W/\"48d682f9-c232-4151-a09f-fab7cb56369a\"",
  "expressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<ResourceGroupName>"
  },
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering/connections/<ConnectionName>",
  "name": "<ConnectionName>",
  "peerExpressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<Circuit2ResourceGroupName>"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "<ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/peerings/connections"
}
```

Quando a operação acima for concluída, você deverá ter conectividade entre suas redes locais nos dois lados por meio dos seus circuitos do ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Circuitos do ExpressRoute em diferentes assinaturas do Azure

Se os dois circuitos não estiverem na mesma assinatura do Azure, você precisará de autorização. Na configuração a seguir, a autorização é gerada na assinatura do circuito 2 e a chave de autorização é passada para o circuito 1.

Gere uma chave de autorização. 
```azurecli
az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
```

A saída da CLI se parece com o seguinte.

```azurecli
{
  "authorizationKey": "<authorizationKey>",
  "authorizationUseStatus": "Available",
  "etag": "W/\"cfd15a2f-43a1-4361-9403-6a0be00746ed\"",
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/authorizations/<AuthorizationName>",
  "name": "<AuthorizationName>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<Circuit2ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/authorizations"
}
```

Tome nota da ID recurso do circuito 2, bem como da chave de autorização.

Execute o seguinte comando no circuito 1. Passe na ID de recurso do circuito 2 e a chave de autorização 
```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
```

Quando a operação acima for concluída, você deverá ter conectividade entre suas redes locais nos dois lados por meio dos seus circuitos do ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Obter e verificar a configuração

Use o seguinte comando para verificar a configuração no circuito em que a configuração foi realizada, por exemplo, circuito 1 no exemplo acima.

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

Na saída de CLI, você verá *CircuitConnectionStatus*. Isso informará se a conectividade foi estabelecida, mostrando "Conectado", ou não, mostrando "Desconectado". 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Desabilitar a conectividade entre suas redes locais

Para desabilitá-la, execute os comandos no circuito em que a configuração foi realizada, por exemplo, circuito 1 no exemplo acima.

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Você pode executar Mostrar CLI para verificar o status. 

Depois de concluir a operação anterior, você deixará de ter conectividade entre suas redes locais por meio de seus circuitos do ExpressRoute. 


## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre o Alcance Global do ExpressRoute](expressroute-global-reach.md)
* [Verificar conectividade do ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Vincular circuito do ExpressRoute a uma rede virtual do Azure](expressroute-howto-linkvnet-arm.md)


