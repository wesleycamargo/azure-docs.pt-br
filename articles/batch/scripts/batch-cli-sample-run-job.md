---
title: "Amostra de Script da CLI do Azure – Executar um trabalho com o Lote | Microsoft Docs"
description: "Amostra de Script da CLI do Azure – Executar um trabalho com o Lote"
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/20/2017
ms.author: antisch
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: d5ef87e6e7092820a65c5736c1942fd5cec57462
ms.lasthandoff: 03/24/2017

---

# <a name="running-jobs-on-azure-batch-with-azure-cli"></a>Executar trabalhos no Lote do Azure com a CLI do Azure

Esse script cria um trabalho no Lote e adiciona uma série de tarefas ao trabalho. Ele também demonstra como monitorar um trabalho e suas tarefas.
A execução desse script indica que você já configurou uma conta no Lote, e tanto um pool quanto um aplicativo já foram configurados. Para saber mais, consulte os [scripts de exemplo](../batch-cli-samples.md) que abrangem cada um desses tópicos.

Se necessário, instale a CLI do Azure usando as instruções encontradas no [guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e, em seguida, execute `az login` para fazer logon no Azure.

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[principal](../../../cli_scripts/batch/run-job/run-job.sh "Executar trabalhos")]

## <a name="clean-up-job"></a>Trabalho de limpeza

Depois de executar o exemplo de script acima, execute o comando a seguir para remover o trabalho e todas as suas tarefas. Observe que o pool precisará ser excluído separadamente; consulte o [tutorial sobre como gerenciar pools](./batch-cli-sample-manage-pool.md).

```azurecli
az batch job delete --job-id myjob
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar um trabalho do Lote e suas tarefas. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#login) | Autentica em uma conta do Lote.  |
| [az batch job create](https://docs.microsoft.com/cli/azure/batch/job#create) | Cria um trabalho do Lote.  |
| [az batch job set](https://docs.microsoft.com/cli/azure/batch/job#set) | Atualiza as propriedades de um trabalho do Lote.  |
| [az batch job show](https://docs.microsoft.com/cli/azure/batch/job#show) | Recupera detalhes de um trabalho especificado do Lote.  |
| [az batch task create](https://docs.microsoft.com/cli/azure/batch/task#create) | Adiciona uma tarefa ao trabalho do Lote especificado.  |
| [az batch task show](https://docs.microsoft.com/cli/azure/batch/task#show) | Recupera os detalhes de uma tarefa do trabalho do Lote especificado.  |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

As amostras de script da CLI do Lote adicionais podem ser encontrados na [documentação do Lote do Azure](../batch-cli-samples.md).

