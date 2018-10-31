---
title: Filtros IP do Hubs de Eventos do Azure | Microsoft Docs
description: Use a filtragem de IP para bloquear conexões de endereços IP específicos para Hubs de Eventos do Azure.
services: event-hubs
documentationcenter: ''
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: spelluru
ms.openlocfilehash: d0114821b5239146f64dde0b01652dc320994585
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2018
ms.locfileid: "49408142"
---
# <a name="use-ip-filters"></a>Usar filtros IP

Para cenários em que os Hubs de Eventos do Azure só devem ser acessíveis de determinados sites bem conhecidos, o recurso *filtro IP* permite que você configure regras para rejeitar ou aceitar tráfego originado de endereços IPv4 específicos. Por exemplo, esses endereços podem ser aqueles de um gateway NAT corporativo.

## <a name="when-to-use"></a>Quando usar

Dois importantes casos de uso em que é útil bloquear Hubs de Eventos de determinados endereços IP são os seguintes:

- Os hubs de eventos devem receber tráfego somente de um intervalo específico de endereços IP e rejeitar todo o resto. Por exemplo, caso você esteja usando Hubs de Eventos com o [Azure Express Route][express-route] para criar conexões privadas entre um para sua infraestrutura local. 
- Você precisa rejeitar tráfego de endereços IP que foram identificados como suspeitos pelo administrador dos Hubs de Eventos.

## <a name="how-filter-rules-are-applied"></a>Como são aplicadas as regras de filtro

As regras de filtro IP são aplicadas no nível do namespace dos Hubs de Eventos. Portanto, as regras se aplicam a todas as conexões de clientes que usam qualquer protocolo com suporte.

Qualquer tentativa de conexão de um endereço IP que corresponda a uma regra IP rejeitada no namespace dos Hubs de Eventos é rejeitada como não autorizada. A resposta não menciona a regra IP.

## <a name="default-setting"></a>Configuração padrão

Por padrão, a grade **Filtro IP** no portal para Hubs de Eventos fica vazia. Essa configuração padrão significa que o hub de eventos aceita conexões de qualquer endereço IP. Essa configuração padrão é equivalente a uma regra que aceita o intervalo de endereços IP 0.0.0.0/0.

## <a name="ip-filter-rule-evaluation"></a>Avaliação da regra de filtro IP

As regras de filtro IP são aplicadas na ordem e a primeira regra que corresponde ao endereço IP determina a ação de aceitar ou rejeitar.

Por exemplo, se você quiser aceitar endereços no intervalo 70.37.104.0/24 e rejeitar todo o resto, a primeira regra na grade deverá aceitar o intervalo de endereços 70.37.104.0/24. A próxima regra deve rejeitar todos os endereços usando o intervalo 0.0.0.0/0.

> [!NOTE]
> Rejeitar endereços IP pode impedir que outros serviços do Azure (como Stream Analytics do Azure, Máquinas Virtuais do Azure ou o Device Explorer no portal) interajam com Hubs de Eventos.

### <a name="creating-an-ip-filter-rule-with-azure-resource-manager-templates"></a>Como criar uma regra de filtro IP com modelos do Azure Resource Manager

> [!IMPORTANT]
> As redes virtuais têm suporte nas camadas **standard** e **dedicada** dos Hubs de Eventos. Não tem suporte na camada básica. 

O modelo do Resource Manager a seguir permite adicionar uma regra de filtro IP a um namespace de Hubs de Eventos existente.

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
            "type": "Microsoft.EventHub/Namespaces/IPFilterRules",
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

Para restringir o acesso a Hubs de Eventos para redes virtuais do Azure, consulte o link a seguir:

- [Pontos de extremidade de serviço de rede virtual para Hubs de Eventos][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: event-hubs-service-endpoints.md