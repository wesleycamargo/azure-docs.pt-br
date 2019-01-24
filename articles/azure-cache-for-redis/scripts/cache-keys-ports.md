---
title: Exemplo de script da CLI do Azure – Obter o nome de host, portas e chaves do Cache do Azure para Redis | Microsoft Docs
description: Exemplo de script da CLI do Azure – Obter o nome de host, portas e chaves de uma instância do Cache do Azure para Redis
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 761eb24e-2ba7-418d-8fc3-431153e69a90
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: f9a963ec81b78cfcc6ded7d8f35f4066f931e53e
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54847308"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Obtenha o nome de host, portas e chaves do Cache do Azure para Redis

Neste cenário, você aprende a recuperar o nome de host, as portas e as chaves usadas para conexão com uma instância do Cache do Azure para Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos para recuperar o nome de host, as chaves e as portas de uma instância do Cache do Azure para Redis. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [apresentação de redis az](https://docs.microsoft.com/cli/azure/redis) | Recuperar os detalhes de uma instância do Cache do Azure para Redis. |
| [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys) | Recuperar as chaves de acesso de uma instância do Cache do Azure para Redis. |


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Exemplos adicionais de scripts da CLI do Cache Redis do Azure podem ser encontrados na [Documentação do Cache Redis do Azure](../cli-samples.md).
