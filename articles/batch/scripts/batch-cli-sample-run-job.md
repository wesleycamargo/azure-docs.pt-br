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
ms.date: 05/02/2017
ms.author: antisch
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 5fe1e3595d9459e60b2fd54d6f17f6822731f453
ms.contentlocale: pt-br
ms.lasthandoff: 05/15/2017

---

# <a name="running-jobs-on-azure-batch-with-azure-cli"></a>Executar trabalhos no Lote do Azure com a CLI do Azure

Esse script cria um trabalho no Lote e adiciona uma série de tarefas ao trabalho. Ele também demonstra como monitorar um trabalho e suas tarefas. Por fim, ele mostra como consultar o serviço de lote com eficiência para obter informações sobre tarefas do trabalho.

## <a name="prerequisites"></a>Pré-requisitos

- Instale a CLI do Azure usando as instruções fornecidas no [Guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) se ainda não tiver feito isso.
- Crie uma conta do lote do Azure caso ainda não tenha uma. Consulte [Criar uma conta do lote com a CLI do Azure](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-create-account) para um script de exemplo que cria uma conta.
- Configure um aplicativo para ser executado de uma tarefa de inicialização se ainda não tiver feito isso. Consulte [Adicionando aplicativos ao Lote do Azure com a CLI do Azure](https://docs.microsoft.com/azure/batch/scripts/batch-cli-sample-add-application) para obter um script de exemplo que cria um aplicativo e carrega um pacote de aplicativos no Azure.
- Configure um pool no qual o trabalho será executado. Consulte [Gerenciando pools de Lote do Azure com a CLI do Azure](https://docs.microsoft.com/azure/batch/batch-cli-sample-manage-pool) para obter um script de exemplo que cria um pool com uma Configuração de serviço de nuvem ou uma Configuração de máquina virtual.

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[principal](../../../cli_scripts/batch/run-job/run-job.sh "Executar trabalhos")]

## <a name="clean-up-job"></a>Trabalho de limpeza

Depois de executar o exemplo de script acima, execute o comando a seguir para remover o trabalho e todas as suas tarefas. Observe que o pool precisará ser excluído separadamente. Consulte [Gerenciando pools de Lote do Azure com a CLI do Azure](./batch-cli-sample-manage-pool.md) para obter mais informações sobre como criar e excluir grupos.

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
| [az batch task list](https://docs.microsoft.com/cli/azure/batch/task#list) | Lista as tarefas associadas ao trabalho especificado.  |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

As amostras de script da CLI do Lote adicionais podem ser encontrados na [documentação do Lote do Azure](../batch-cli-samples.md).

