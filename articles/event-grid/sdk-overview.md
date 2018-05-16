---
title: SDKs da Grade de Eventos do Azure
description: Descreve os SDKs de Grade de Eventos do Azure. Esses SDKs fornecem gerenciamento, publicação e o consumo.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 05/04/2018
ms.author: tomfitz
ms.openlocfilehash: d9bb4b3b161060f20fca34760872a24cbfcabf30
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>SDKs de Grade de Eventos para o gerenciamento e publicação

Grade de Eventos fornece SDKs que permitem gerenciar seus recursos e lançar eventos programaticamente.

## <a name="management-sdks"></a>SDKs de gerenciamento

Os SDKs de gerenciamento permitem criar, atualizar e excluir assinaturas e tópicos de grade de eventos. No momento, os seguintes SDKs estão disponíveis:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure.eventgrid-2018-01-01/azure-mgmt-eventgrid)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>SDKs de plano de dados

Os SDKs de plano de dados permitem que você publique eventos para tópicos, tomando cuidado de autenticar, formar o evento, e publicar de forma assíncrona ao endpoint especificado. Elas também permitem que você consuma eventos de terceiros primeiro. No momento, os seguintes SDKs estão disponíveis:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos, confira [O que é uma Grade de eventos?](overview.md)
* Para comandos de grade de eventos em CLI do Azure, consulte [CLI do Azure](/cli/azure/eventgrid).
* Para comandos de grade de eventos em PowerShell, consulte [PowerShell](/powershell/module/azurerm.eventgrid).