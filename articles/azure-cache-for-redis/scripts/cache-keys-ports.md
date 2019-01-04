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
ms.openlocfilehash: 0c6f7b637e56d2bf39d8f03122ccb28bd7b1c773
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53096738"
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
| [apresentação de redis az](https://docs.microsoft.com/cli/azure/redis#az_redis_show) | Recuperar os detalhes de uma instância do Cache do Azure para Redis. |
| [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys) | Recuperar as chaves de acesso de uma instância do Cache do Azure para Redis. |


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Exemplos adicionais de scripts da CLI do Cache do Azure para Redis podem ser encontrados na [Documentação do Cache do Azure para Redis](../cli-samples.md).