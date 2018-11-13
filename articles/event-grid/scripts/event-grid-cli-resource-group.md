---
title: Exemplo de script da CLI do Azure - Inscrever-se para o grupo de recursos | Microsoft Docs
description: Exemplo de script da CLI do Azure - Inscrever-se para o grupo de recursos
services: event-grid
documentationcenter: na
author: tfitzmac
ms.service: event-grid
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2018
ms.author: tomfitz
ms.openlocfilehash: 7bc07ec294e341c7f96c60fd2c9916b0c6b9f215
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51035826"
---
# <a name="subscribe-to-events-for-a-resource-group-with-azure-cli"></a>Inscrever-se em eventos para um grupo de recursos com a CLI do Azure

Este script cria uma assinatura de Grade de Eventos para os eventos para um grupo de recursos. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

O script de exemplo de visualização requer a extensão de Grade de Eventos. Para instalar, execute `az extension add --name eventgrid`.

## <a name="sample-script---stable"></a>Exemplo de script - estável

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-resource-group/subscribe-to-resource-group.sh "Subscribe to resource group")]

## <a name="sample-script---preview-extension"></a>Exemplo de script - extensão de visualização

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-resource-group-preview/subscribe-to-resource-group-preview.sh "Subscribe to resource group")]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa o seguinte comando para criar a assinatura do evento. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create) | Criar uma assinatura na Grade de Eventos. |
| [az eventgrid event-subscription create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) - versão de extensão | Criar uma assinatura na Grade de Eventos. |

## <a name="next-steps"></a>Próximas etapas

* Para obter informações sobre como consultar as assinaturas, confira [Assinaturas da Grade de Eventos de Consulta](../query-event-subscriptions.md).
* Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).
