---
title: Exemplo de script do Azure PowerShell – Inscrever-se em um tópico personalizado | Microsoft Docs
description: Exemplo de script do Azure PowerShell – Inscrever-se em um tópico personalizado
services: event-grid
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/10/2018
ms.author: tomfitz
ms.openlocfilehash: f16a02cd110397b1ef6bb3aa00ea12c44e4b9563
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58176507"
---
# <a name="subscribe-to-events-for-a-custom-topic-with-powershell"></a>Inscreva-se em eventos para um tópico personalizado com o PowerShell

Este script cria uma assinatura de Grade de Eventos para os eventos para um tópico personalizado.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

O script de exemplo de visualização requer o módulo de Grade de Eventos. Para instalar, execute `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

## <a name="sample-script---stable"></a>Exemplo de script - estável

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-custom-topic/subscribe-to-custom-topic.ps1 "Subscribe to custom topic")]

## <a name="sample-script---preview-module"></a>Exemplo de script – módulo de visualização

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-custom-topic-preview/subscribe-to-custom-topic-preview.ps1 "Subscribe to custom topic")]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa o seguinte comando para criar a assinatura do evento. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | Criar uma assinatura na Grade de Eventos. |

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados do Azure](../overview.md).
* Para obter mais informações sobre o PowerShell, confira a [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
