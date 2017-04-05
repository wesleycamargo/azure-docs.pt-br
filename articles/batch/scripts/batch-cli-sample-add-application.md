---
title: "Amostra de Script da CLI do Azure – Adicionar um aplicativo no Lote | Microsoft Docs"
description: "Amostra de Script da CLI do Azure – Adicionar um aplicativo no Lote"
services: batch
documentationcenter: 
author: annatisch
manager: daryls
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/20/2017
ms.author: antisch
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 342cc2c93304f7a8f651769139e28ebf2e30d412
ms.lasthandoff: 03/24/2017

---

# <a name="adding-applications-to-azure-batch-with-azure-cli"></a>Adicionar aplicativos ao Lote do Azure com a CLI do Azure

Este script demonstra como configurar um aplicativo para uso com um pool ou tarefa do Lote do Azure. Para configurar um aplicativo, coloque seu executável, junto com quaisquer dependências, em um arquivo .zip. Neste exemplo, o arquivo executável zip é chamado 'my-application-exe.zip'.
A execução deste script pressupõe que uma conta do Lote já foi configurada. Para saber mais, veja o [exemplo de script para criar uma conta do Lote](./batch-cli-sample-create-account.md).

Se necessário, instale a CLI do Azure usando as instruções encontradas no [guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e, em seguida, execute `az login` para fazer logon no Azure.

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[principal](../../../cli_scripts/batch/add-application/add-application.sh "Adicionar aplicativo")]

## <a name="clean-up-application"></a>Limpar aplicativo

Depois de executar o script de exemplo acima, execute os comandos a seguir para remover o aplicativo e todos os seus pacotes de aplicativo carregados.

```azurecli
az batch application package delete -g myresourcegroup -n mybatchaccount --application-id myapp --version 1.0 --yes
az batch application delete -g myresourcegroup -n mybatchaccount --application-id myapp --yes
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar um aplicativo e carregar um pacote de aplicativo.
Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az batch application create](https://docs.microsoft.com/cli/azure/batch/application#create) | Criar um aplicativo.  |
| [az batch application set](https://docs.microsoft.com/cli/azure/batch/application#set) | Atualiza as propriedades de um aplicativo.  |
| [az batch application package create](https://docs.microsoft.com/cli/azure/batch/application/package#create) | Adiciona um pacote de aplicativos ao aplicativo especificado.  |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

As amostras de script da CLI do Lote adicionais podem ser encontrados na [documentação do Lote do Azure](../batch-cli-samples.md).

