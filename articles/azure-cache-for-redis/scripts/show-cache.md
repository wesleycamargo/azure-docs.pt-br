---
title: Exemplo de script da CLI do Azure – Obter detalhes de um Cache Redis do Azure| Microsoft Docs
description: Exemplo de script da CLI do Azure – Obter detalhes de um Cache Redis do Azure
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
tags: azure-service-management
ms.assetid: 155924e6-00d5-4a8c-ba99-5189f300464a
ms.service: cache
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: 8b4c0311f935fa141170206f51e01b90eb4b98ae
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56234876"
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
