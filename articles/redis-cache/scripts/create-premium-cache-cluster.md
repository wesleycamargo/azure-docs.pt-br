---
title: "Amostra de Script de Azure CLI – Criar um Cache Redis Premium do Azure com o cluster | Microsoft Docs"
description: "Amostra de Script de Azure CLI – Criar um Cache Redis de camada Premium do Azure com o cluster"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: 07bcceae-2521-4fe3-b88f-ed833104ddd2
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: sdanie
ms.translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: 87d0fe4c3eaa8f7b75343a36a069ecdac8241d74
ms.contentlocale: pt-br
ms.lasthandoff: 04/15/2017

---

# <a name="create-a-premium-azure-redis-cache-with-clustering"></a>Criar um Cache Redis Premium do Azure com cluster

Nesse cenário, você aprenderá como criar um Cache Redis Premium do Azure de camada de 6 GB com o cluster habilitado e dois fragmentos.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[principal](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Cache Redis do Azure")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar um grupo de recursos e um Cache Redis de camada Premium com cluster habilitado. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az redis create](https://docs.microsoft.com/cli/azure/redis#create) | Crie uma instância do Cache Redis. |


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos adicionais de script da CLI de Cache Redis do Azure podem ser encontrados na [documentação de Cache Redis do Azure](../cli-samples.md).
