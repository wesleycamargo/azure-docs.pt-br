---
title: Amostra de script da CLI do Azure – Criar um Cache do Azure para Redis Premium com clustering | Microsoft Docs
description: Amostra de script da CLI do Azure – Criar uma camada Premium do Cache do Azure para Redis com clustering
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 07bcceae-2521-4fe3-b88f-ed833104ddd2
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: abd69e27cf95a882ae7c299cbe19e2c30c35a918
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53096818"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>Criar um Cache do Azure para Redis Premium com clustering

Neste cenário, você aprenderá a criar uma camada Premium de 6 GB do Cache do Azure para Redis com clustering habilitado e dois fragmentos.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos para criar um grupo de recursos e uma camada Premium do Cache do Azure para Redis com clustering habilitado. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az redis create](https://docs.microsoft.com/cli/azure/redis#az_redis_create) | Criar instância de Cache do Azure para Redis. |


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Exemplos adicionais de scripts da CLI do Cache do Azure para Redis podem ser encontrados na [Documentação do Cache do Azure para Redis](../cli-samples.md).