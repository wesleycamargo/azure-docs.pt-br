---
title: Exemplo de script do Azure PowerShell – Criar um tópico personalizado | Microsoft Docs
description: Exemplo de script do Azure PowerShell – Criar um tópico personalizado
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
ms.openlocfilehash: 5d5b8dc21e40f38aee8f082e84a5a5bea1baf11c
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276342"
---
# <a name="create-event-grid-custom-topic-with-powershell"></a>Criar um tópico de Grade de Eventos personalizado com o PowerShell

Este script cria um tópico personalizado da Grade de Eventos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/event-grid/create-custom-topic/create-custom-topic.ps1 "Create custom topic")]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa o seguinte comando para criar o tópico personalizado. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzureRmEventGridTopic](https://docs.microsoft.com/powershell/module/azurerm.eventgrid/new-azurermeventgridtopic) | Este script cria um tópico personalizado da Grade de Eventos. |

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados do Azure](../overview.md).
* Para obter mais informações sobre o PowerShell, confira a [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
