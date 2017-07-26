---
title: Escalar verticalmente automaticamente unidade de produtividade do Hub de Eventos do Azure | Microsoft Docs
description: "Habilitar Inflação automática em um namespace para escalar verticalmente automaticamente as unidades de produtividade"
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2017
ms.author: shvija;sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 5bbeb9d4516c2b1be4f5e076a7f63c35e4176b36
ms.openlocfilehash: b085091ea7bfd601efb0eee84144ddd091422d6e
ms.contentlocale: pt-br
ms.lasthandoff: 06/13/2017


---

# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Escalar verticalmente automaticamente unidade de produtividade do Hub de Eventos do Azure

## <a name="overview"></a>Visão geral

Hubs de Eventos do Azure é uma plataforma de streaming de dados altamente escalonável. Dessa forma, os clientes do Hubs de Eventos geralmente aumentam seu uso após a migração para o serviço. Tal aumento exige o aumento de TUs (unidades de produtividade) predeterminadas para dimensionar os Hubs de Eventos e lidar com taxas de transferência maiores. O recurso *inflar automaticamente* dos Hubs de Eventos escala verticalmente automaticamente o número delas para atender às necessidades de uso. O aumento de TUs evita cenários com limitação, nos quais:

* As taxas de entrada de dados excedem as TUs definidas.
* As taxas de solicitação de saída de dados excedem as TUs definidas.

## <a name="how-auto-inflate-works"></a>Como o Inflar automaticamente funciona

O tráfego dos Hubs de Eventos é controlado por unidades de produtividade. Uma única TU permite o ingresso de 1 MB/s ou saída com duas vezes essa quantidade. Hubs de Evento Standard podem ser configurados com 1-20 unidades de produtividade. Inflar automaticamente permite que você comece pequeno, com o mínimo de unidades de produtividade necessárias. O recurso então dimensiona automaticamente para o limite máximo de unidades de produtividade que você precisa, dependendo do aumento de seu tráfego. O Inflar automaticamente oferece os seguintes benefícios:

- Um mecanismo eficiente de colocação em escala para começar pequeno e escalar verticalmente conforme o crescimento.
- Dimensione automaticamente para o limite superior especificado sem problemas de limitação.
- Mais controle sobre a colocação em escala, já que você controla quando e quanto dimensionar.

## <a name="enable-auto-inflate-on-a-namespace"></a>Habilitar o Inflar automaticamente em um namespace

Você pode habilitar ou desabilitar o Inflar automaticamente em um namespace usando qualquer um dos seguintes métodos:

1. O [Portal do Azure](https://portal.azure.com).
2. Um modelo do Azure Resource Manager.

### <a name="enable-auto-inflate-through-the-portal"></a>Habilitar Inflar automaticamente por meio do Portal

Você pode habilitar o recurso Inflar automaticamente em um namespace durante a criação de um namespace de Hubs de Eventos:
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Com essa opção habilitada, você pode começar pequeno em suas unidades de produtividade e escalar verticalmente à medida que suas necessidades de seu uso aumentam. O limite superior para inflação não afeta os preços, que dependem do número de TUs usadas por hora.

Você também pode habilitar o Inflar automaticamente usando a opção **Dimensionar** na folha de configurações no portal:
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate2.png)

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Habilitar Inflar automaticamente usando um modelo do Azure Resource Manager

Você pode habilitar o Inflar automaticamente durante uma implantação de modelo do Azure Resource Manager. Por exemplo, defina a propriedade `isAutoInflateEnabled` como **true** e defina `maximumThroughputUnits` como 10.

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

Para ver o modelo completo, consulte o modelo [Criar namespace de Hubs de Eventos e habilitar inflar](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) no GitHub.

## <a name="next-steps"></a>Próximas etapas

Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

* [Visão Geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Criar um Hub de Eventos](event-hubs-create.md)

