---
title: "Exemplo de script de CLI do Azure – Obtenha o nome de host, portas e chaves de Cache Redis do Azure | Microsoft Docs"
description: "Exemplo de script de CLI do Azure – Obtenha o nome de host, portas e chaves da instância Cache Redis do Azure"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: 761eb24e-2ba7-418d-8fc3-431153e69a90
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: sdanie
ms.translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: cd9adc784bceb0fff5e7c2bbee2be0950c51c8f6
ms.contentlocale: pt-br
ms.lasthandoff: 04/15/2017

---

# <a name="get-the-hostname-ports-and-keys-for-azure-redis-cache"></a>Obtenha o nome de host, portas e chaves de Cache Redis do Azure

Neste cenário, você aprende como recuperar nome de host, portas e as chaves usadas para conexão com uma instância de Cache Redis do Azure.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[principal](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Cache Redis do Azure")]


## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para recuperar nome de host, chaves e portas de uma instância de Cache Redis do Azure. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [apresentação de redis az](https://docs.microsoft.com/cli/azure/redis#show) | Recupere os detalhes de uma instância de Cache Redis do Azure. |
| [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#list-keys) | Recupere chaves de acesso para uma instância de Cache Redis do Azure. |


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos adicionais de script da CLI de Cache Redis do Azure podem ser encontrados na [documentação de Cache Redis do Azure](../cli-samples.md).
