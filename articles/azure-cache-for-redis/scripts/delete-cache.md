---
title: Exemplo de Script da CLI do Azure – Excluir um Azure Cache para Redis| Microsoft Docs
description: Exemplo de Script da CLI do Azure – Excluir um Azure Cache para Redis
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 7beded7a-d2c9-43a6-b3b4-b8079c11de4a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: 58ee6c7239e3a71fed9a9ab987b962abcc9c0508
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53085188"
---
# <a name="delete-an-azure-cache-for-redis"></a>Excluir um Azure Cache para Redis

Neste cenário, você aprende a excluir um Azure Cache para Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para excluir uma instância do Azure Cache para Redis. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [exclusão de redis az](https://docs.microsoft.com/cli/azure/redis#az_redis_delete) | Exclua o Azure Cache para Redis para instância do Redis. |


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Exemplos adicionais de scripts da CLI do Azure Cache para Redis podem ser encontrados na [Documentação do Azure Cache para Redis](../cli-samples.md).