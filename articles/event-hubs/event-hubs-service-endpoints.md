---
title: Pontos de extremidade de serviço de Rede Virtual - Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece informações sobre como adicionar um ponto de extremidade de serviço de eventhub a uma rede virtual.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 03/12/2019
ms.author: shvija
ms.openlocfilehash: 7b5a62f81238d1ae2b627c395613066350b36efe
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57887588"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Usar pontos de extremidade de serviço de Rede Virtual com Hubs de Eventos do Azure

A integração de Hubs de Eventos com [Pontos de extremidade de serviço de VNet (rede virtual)][vnet-sep] permite acesso seguro a recursos de mensagens de cargas de trabalho, como máquinas virtuais associadas a redes virtuais, com o caminho de tráfego de rede sendo protegido em ambas as extremidades.

Uma vez configurado associados ao ponto de extremidade serviço subrede de pelo menos uma rede virtual, o respectivo namespace de Hubs de eventos não aceita tráfego de qualquer lugar, mas autorizado sub-redes em redes virtuais. Da perspectiva da rede virtual, a associação de um namespace de Hubs de Eventos a um ponto de extremidade de serviço configura um túnel de rede isolado da sub-rede da rede virtual para o serviço de sistema de mensagens. 

O resultado é um relacionamento privado e isolado entre as cargas de trabalho associadas à sub-rede e o respectivo namespace de Hubs de Eventos, apesar do endereço de rede observável do ponto de extremidade de serviço de mensagens estar em um intervalo de IP público. Há uma exceção a esse comportamento. Habilitar um ponto de extremidade de serviço, por padrão, permite que a regra denyall no firewall do IP associado com a rede virtual. Você pode adicionar endereços IP específicos no firewall do IP para habilitar o acesso para o ponto de extremidade público do Hub de eventos. 


>[!WARNING]
> Implementar a integração de redes virtuais pode impedir que outros serviços do Azure interajam com Hubs de Eventos.
>
> Não há suporte para serviços confiáveis da Microsoft quando as Redes Virtuais são implementadas.
>
> Cenários comuns do Azure que não funcionam com Redes Virtuais (observe que a lista **NÃO** é exaustiva):
> - Azure Monitor
> - Stream Analytics do Azure
> - Integração com a Grade de Eventos do Azure
> - Rotas do Hub IoT do Azure
> - Device Explorer do Azure IoT
> - Azure Data Explorer
>
> Os serviços da Microsoft abaixo devem estar em uma rede virtual
> - Aplicativos Web do Azure 
> - Funções do Azure

> [!IMPORTANT]
> As redes virtuais têm suporte nas camadas **standard** e **dedicada** dos Hubs de Eventos. Não tem suporte na camada básica.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Cenários avançados de segurança habilitados pela integração de VNet 

Soluções que exigem segurança forte e compartimentalizada e onde sub-redes da rede virtual fornecem segmentação entre os serviços compartmentalized, ainda precisam de caminhos de comunicação entre os serviços que residem nesses compartimentos.

Qualquer rota IP imediata entre os compartimentos, incluindo aqueles que transportam HTTPS sobre TCP/IP, acarreta o risco de exploração de vulnerabilidades a partir da camada de rede. Os serviços de mensagens fornecem caminhos de comunicação completamente isolados, onde as mensagens são gravadas no disco na medida em que fazem a transição entre as partes. Cargas de trabalho em duas redes virtuais distintas que estão associadas à mesma instância de Hubs de Eventos podem comunicar-se de maneira eficiente e confiável por meio de mensagens, enquanto a integridade de limite de isolamento da respectiva rede é preservada.
 
Isso significa que as soluções na nuvem de segurança confidencial não apenas obtêm acesso aos recursos de mensagens assíncronas confiáveis e escalonáveis líderes do mercado do Azure, mas agora podem usar o sistema de mensagens para criar caminhos de comunicação entre compartimentos de solução protegidos que são inerentemente mais seguros que o modo de comunicação ponto a ponto, incluindo HTTPS e outros protocolos de socket protegidos por TLS.

## <a name="bind-event-hubs-to-virtual-networks"></a>Associar Hubs de Eventos a Redes Virtuais

As *regras de rede virtual* são o recurso de segurança do firewall que controla se o namespace de Hubs de Eventos do Azure aceita conexões de uma sub-rede de rede virtual específica.

Associar um namespace de Hubs de Eventos a uma rede virtual é um processo de duas etapas. Primeiro é necessário criar um **ponto de extremidade de serviço de Rede Virtual** em uma sub-rede de Rede Virtual e habilitá-lo para "Microsoft.EventHub", conforme explicado na [visão geral do ponto de extremidade de serviço][vnet-sep]. Após adicionar o ponto de extremidade de serviço, você associa o namespace de Hubs de Eventos ao ponto de extremidade com uma *regra da rede virtual*.

A regra da rede virtual é uma associação do namespace de Hubs de Eventos com uma sub-rede de rede virtual. Embora a regra exista, todas as cargas de trabalho associadas à sub-rede recebem acesso ao namespace de Hubs de Eventos. Os próprios Hubs de Eventos nunca estabelecem conexões de saída, não precisam obter acesso e, portanto, nunca recebem acesso à sub-rede habilitando essa regra.

### <a name="create-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Criar uma regra da rede virtual com modelos do Azure Resource Manager

O modelo do Resource Manager a seguir permite incluir uma regra da rede virtual em um namespace de Hubs de Eventos existente.

Parâmetros de modelo:

* **namespaceName**: namespace dos Hubs de Eventos.
* **vnetRuleName**: nome da regra da Rede Virtual a ser criada.
* **virtualNetworkingSubnetId**: caminho do Resource Manager totalmente qualificado para a sub-rede da rede virtual, por exemplo, `subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` para a sub-rede padrão de uma rede virtual.

> [!NOTE]
> Embora não haja nenhuma regra de negação possível, o modelo do Azure Resource Manager tem a ação padrão definida como **"Allow"**, o que não restringe as conexões.
> Ao criar as regras de rede virtual ou de firewalls, devemos alterar a ***"defaultAction"***
> 
> de
> ```json
> "defaultAction": "Allow"
> ```
> para
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
        }
      },
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.EventHub"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Para implantar o modelo, siga as instruções para o [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre redes virtuais, consulte os links a seguir:

- [Pontos de extremidade de serviço de rede virtual do Azure][vnet-sep]
- [Filtragem IP de Hubs de Eventos do Azure][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: event-hubs-ip-filtering.md
