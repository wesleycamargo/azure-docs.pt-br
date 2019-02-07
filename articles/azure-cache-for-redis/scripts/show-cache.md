---
title: Exemplo de script da CLI do Azure – Obter detalhes de um Cache Redis do Azure| Microsoft Docs
description: Exemplo de script da CLI do Azure – Obter detalhes de um Cache Redis do Azure
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 155924e6-00d5-4a8c-ba99-5189f300464a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: 7bcdd999a4954766398800e6e6a0ddb8c9727a99
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55749152"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>Obter detalhes de um Cache Redis do Azure

Nesse cenário, você aprende como recuperar os detalhes de uma instância de Cache Redis do Azure, incluindo seu status de provisionamento.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para recuperar detalhes de uma instância de Cache Redis do Azure. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [apresentação de redis az](https://docs.microsoft.com/cli/azure/redis) | Recuperar os detalhes de uma instância do Cache Redis do Azure. |


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Exemplos adicionais de scripts da CLI do Cache Redis do Azure podem ser encontrados na [Documentação do Cache Redis do Azure](../cli-samples.md).
