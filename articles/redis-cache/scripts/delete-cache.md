---
title: "Exemplo de Script da CLI do Azure – Excluir um Cache Redis do Azure | Microsoft Docs"
description: "Exemplo de Script da CLI do Azure – excluir um Cache Redis do Azure"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: 7beded7a-d2c9-43a6-b3b4-b8079c11de4a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/14/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: f959823b3a7c5b0262f693ecad1e6efc4eec4f35
ms.lasthandoff: 04/15/2017

---

# <a name="delete-an-azure-redis-cache"></a>Excluir um Cache Redis do Azure

Nesse cenário, você aprenderá como excluir um Cache Redis do Azure.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[principal](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Cache Redis do Azure")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para excluir uma instância de Cache Redis do Azure. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [exclusão de redis az](https://docs.microsoft.com/cli/azure/redis#delete) | Excluir uma instância do Cache Redis. |


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos adicionais de script da CLI de Cache Redis do Azure podem ser encontrados na [documentação de Cache Redis do Azure](../cli-samples.md).
