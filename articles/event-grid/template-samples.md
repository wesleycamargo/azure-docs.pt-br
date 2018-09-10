---
title: Exemplos de modelo do Azure Resource Manager – Grade de Eventos | Microsoft Docs
description: Exemplos de modelo do Azure Resource Manager para Grade de Eventos
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 03/27/2018
ms.author: tomfitz
ms.openlocfilehash: f4d6a663b4e0d2c2166028051e713668534b20bc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
ms.locfileid: "30246263"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Modelos do Azure Resource Manager para Grade de Eventos

A tabela a seguir contém links para modelos do Azure Resource Manager para Grade de Eventos.

| | |
|-|-|
|**Assinaturas de Grade de Eventos**||
| [Tópico e assinatura personalizados com o ponto de extremidade do WebHook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Implanta um tópico personalizado de Grade de Eventos. Cria uma assinatura para esse tópico personalizado que usa um ponto de extremidade do WebHook. |
| [Assinatura de tópico personalizada com o ponto de extremidade do EventHub](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeCustomTopicToEventHub.json)| Cria uma assinatura de Grade de Eventos para um tópico personalizada que já existe. A assinatura usa um Hub de Evento para o ponto de extremidade. |
| [Assinatura de grupo de recursos](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/subscribeResourceGroupToWebHook.json)| Assina eventos para um grupo de recursos. O grupo de recursos que você especificar como o de destino durante a implantação é a origem de eventos. A assinatura usa um Hub de Evento para o ponto de extremidade. |
| [Conta de armazenamento de blobs e assinatura](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/createBlobAndSubscribe.json)| Implanta uma conta de armazenamento de blobs do Azure e assina eventos para essa conta de armazenamento. |
| | |
