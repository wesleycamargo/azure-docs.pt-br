---
title: SDKs da Grade de Eventos do Azure
description: "Descreve os SDKs de Grade de Eventos do Azure. Esses SDKs fornecem gerenciamento, publicação e o consumo."
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 9c56e4c3314090ad55017d5c681a0cfd7bf5722c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>SDKs de Grade de Eventos para o gerenciamento e publicação

Grade de Eventos fornece SDKs que permitem gerenciar seus recursos e lançar eventos programaticamente.

## <a name="management-sdks"></a>SDKs de gerenciamento

Os SDKs de gerenciamento permitem criar, atualizar e excluir assinaturas e tópicos de grade de eventos. No momento, os seguintes SDKs estão disponíveis:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="publish-sdks"></a>SDKs de publicação

Os SDKs de publicação permitem que você publique eventos para tópicos, tomando cuidado de autenticar, formar o evento, e publicar de forma assíncrona ao endpoint especificado. No momento, os seguintes SDKs estão disponíveis:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos, confira [O que é uma Grade de eventos?](overview.md)
* Para comandos de grade de eventos em CLI do Azure, consulte [CLI do Azure](/cli/azure/eventgrid).
* Para comandos de grade de eventos em PowerShell, consulte [PowerShell](/powershell/module/azurerm.eventgrid).