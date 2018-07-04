---
title: Filtros de conexão IP de Barramento de Serviço do Azure | Microsoft Docs
description: Como usar a filtragem IP para bloquear conexões de endereços IP específicos para Barramento de Serviço do Azure.
services: service-bus
documentationcenter: ''
author: clemensv
manager: timlt
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: clemensv
ms.openlocfilehash: e009bb9120fafc6edf60b68fab3336b9d1add507
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034209"
---
# <a name="use-ip-filters"></a>Usar filtros IP

Para cenários em que os Barramento de Serviço do Azure são acessíveis apenas de determinados sites bem conhecidos, o recurso *filtro IP* permite que você configure regras para rejeitar ou aceitar tráfego originado de endereços IPv4 específicos. Por exemplo, esses endereços podem ser aqueles de um gateway NAT corporativo.

## <a name="when-to-use"></a>Quando usar

Há dois casos de uso específicos nos quais é útil bloquear os pontos de extremidade do Barramento de Serviço para determinados endereços IP:

- O Barramento de Serviço deve receber tráfego somente de um intervalo especificado de endereços IP e rejeitar todo o restante. Por exemplo, caso esteja usando o Barramento de Serviço com [Azure ExpressRoute][express-route] para criar conexões privadas com a infraestrutura local.
- Você deve rejeitar o tráfego de endereços IP que foram identificados como suspeitos pelo administrador do Barramento de Serviço.

## <a name="how-filter-rules-are-applied"></a>Como são aplicadas as regras de filtro

As regras de filtro IP são aplicadas no nível de namespace do Barramento de Serviço. Portanto, as regras se aplicam a todas as conexões de clientes que usam qualquer protocolo com suporte.

Qualquer tentativa de conexão de um endereço IP que corresponda a uma regra IP rejeitada no namespace dos Barramentos de Serviço é rejeitada como não autorizada. A resposta não menciona a regra IP.

## <a name="default-setting"></a>Configuração padrão

Por padrão, a grade **Filtro IP** o portal do Barramento de Serviço é vazia. Essa configuração padrão significa que o namespace aceita conexões com qualquer endereço IP. Essa configuração padrão é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Avaliação da regra de filtro IP

As regras de filtro IP são aplicadas na ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

Por exemplo, se você quiser aceitar endereços no intervalo 70.37.104.0/24 e rejeitar todo o resto, a primeira regra na grade deverá aceitar o intervalo de endereços 70.37.104.0/24. A próxima regra deve rejeitar todos os endereços usando o intervalo 0.0.0.0/0.

> [!NOTE]
> Rejeitar endereços IP pode impedir que outros serviços do Azure (como Azure Stream Analytics, Máquinas Virtuais do Microsoft Azure ou o Device Explorer no portal) interajam com o Barramento de Serviço.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Criar uma regra de rede virtual com modelos do Azure Resource Manager

O modelo do Resource Manager a seguir permite incluir uma regra de rede virtual em um namespace de Barramento de Serviço existente.

Parâmetros de modelo:

- O **ipFilterRuleName** deve ser uma cadeia exclusiva de caracteres alfanuméricos, que não diferencia maiúsculas de minúsculas, de até 128 caracteres.
- A **ipFilterAction** é **Rejeitar** ou **Aceitar**, conforme a ação de aplicar para a regra de filtro IP.
- A **ipMask** é um endereço IPv4 único ou um bloco de endereços IP na notação CIDR. Por exemplo, na notação CIDR 70.37.104.0/24, representa os 256 endereços IPv4 de 70.37.104.0 a 70.37.104.255, em que 24 indica o número de bits de prefixo significativos para o intervalo.

```json
{  
   "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{     
          "namespaceName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the namespace"
             }
          },
          "ipFilterRuleName":{  
             "type":"string",
             "metadata":{  
                "description":"Name of the Authorization rule"
             }
          },
          "ipFilterAction":{  
             "type":"string",
             "allowedValues": ["Reject", "Accept"],
             "metadata":{  
                "description":"IP Filter Action"
             }
          },
          "IpMask":{  
             "type":"string",
             "metadata":{  
                "description":"IP Mask"
             }
          }
      },
    "resources": [
        {
            "apiVersion": "2018-01-01-preview",
            "name": "[concat(parameters('namespaceName'), '/', parameters('ipFilterRuleName'))]",
            "type": "Microsoft.ServiceBus/Namespaces/IPFilterRules",
            "properties": {
                "FilterName":"[parameters('ipFilterRuleName')]",
                "Action":"[parameters('ipFilterAction')]",              
                "IpMask": "[parameters('IpMask')]"
            }
        } 
    ]
}
```

Para implantar o modelo, siga as instruções para o [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Próximas etapas

Para restringir o acesso a Barramento de Serviço para redes virtuais do Azure, consulte o link a seguir:

- [Pontos de extremidade de serviço de Rede Virtual para Barramento de Serviço][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services