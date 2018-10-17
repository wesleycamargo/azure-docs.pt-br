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
ms.date: 09/18/2018
ms.author: tomfitz
ms.openlocfilehash: c462334597b41b914b6a0a0e3c8a67ad97e2bd7a
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46295573"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Modelos do Azure Resource Manager para Grade de Eventos

A tabela a seguir contém links para modelos do Azure Resource Manager para Grade de Eventos.

| | |
|-|-|
|**Assinaturas de Grade de Eventos**||
| [Tópico e assinatura personalizados com o ponto de extremidade do WebHook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Implanta um tópico personalizado de Grade de Eventos. Cria uma assinatura para esse tópico personalizado que usa um ponto de extremidade do WebHook. |
| [Assinatura de tópico personalizada com o ponto de extremidade do EventHub](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Cria uma assinatura da Grade de Eventos para um tópico personalizado. A assinatura usa um Hub de Evento para o ponto de extremidade. |
| [Assinatura do Azure ou assinatura do grupo de recursos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook)| Assina eventos para um grupo de recursos ou assinatura do Azure. O grupo de recursos que você especificar como o de destino durante a implantação é a origem de eventos. A assinatura usa um WebHook para o ponto de extremidade. |
| [Conta de armazenamento de blobs e assinatura](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)| Implanta uma conta de armazenamento de blobs do Azure e assina eventos para essa conta de armazenamento. |
| | |
