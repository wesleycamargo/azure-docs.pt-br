---
title: "Exemplo de script de CLI do Azure – Obtenha o nome de host, portas e chaves de Cache Redis do Azure | Microsoft Docs"
description: "Exemplo de script de CLI do Azure – Obtenha o nome de host, portas e chaves da instância Cache Redis do Azure"
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: 761eb24e-2ba7-418d-8fc3-431153e69a90
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: 6cfff74ed037835abb6d3e3975fe33d71b9730a3
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
# <a name="get-the-hostname-ports-and-keys-for-azure-redis-cache"></a>Obtenha o nome de host, portas e chaves de Cache Redis do Azure

Neste cenário, você aprende como recuperar nome de host, portas e as chaves usadas para conexão com uma instância de Cache Redis do Azure.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Redis Cache")]


## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para recuperar nome de host, chaves e portas de uma instância de Cache Redis do Azure. Cada comando na tabela redireciona para a documentação específica do comando.

| Get-Help | Observações |
|---|---|
| [apresentação de redis az](https://docs.microsoft.com/cli/azure/redis#az_redis_show) | Recupere os detalhes de uma instância de Cache Redis do Azure. |
| [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys) | Recupere chaves de acesso para uma instância de Cache Redis do Azure. |


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos adicionais de scripts da CLI do Cache Redis do Azure podem ser encontrados na [Documentação do Cache Redis do Azure](../cli-samples.md).