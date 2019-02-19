---
title: 'Início Rápido: Criar um cluster e o banco de dados do Azure Data Explorer usando a CLI'
description: Neste início rápido, você aprenderá a criar um cluster e um banco de dados do Azure Data Explorer usando a CLI do Azure
services: data-explorer
author: radennis
ms.author: radennis
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: quickstart
ms.date: 2/4/2019
ms.openlocfilehash: 9e0ae547df34594674dc03702310a1537717a4ed
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55881109"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-using-cli"></a>Criar um cluster e o banco de dados do Azure Data Explorer usando a CLI

Este início rápido descreve como criar um cluster e um banco de dados do Azure Data Explorer usando a CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa de uma assinatura do Azure. Se você não tiver [uma conta gratuita](https://azure.microsoft.com/free/), crie uma antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI do Azure localmente, este início rápido exigirá a CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para verificar sua versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="configure-the-cli-parameters"></a>Configurar os parâmetros da CLI

As etapas a seguir não são necessárias se você estiver executando comandos no Cloud Shell. Se você estiver executando a CLI localmente, execute as seguintes etapas para entrar no Azure e configure sua assinatura atual:

1. Execute o comando a seguir para entrar no Azure:

    ```azurecli-interactive
    az login
    ```

2. Defina a assinatura em que você gostaria que seu cluster fosse criado. Substitua `MyAzureSub` pelo nome da assinatura do Azure que deseja usar:

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Criar o cluster do Azure Data Explorer

1. Crie o cluster usando o comando a seguir:

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
    ```

   |**Configuração** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | Nome | *azureclitest* | O nome desejado do cluster.|
   | sku | *D13_v2* | A SKU que será usada para o cluster. |
   | resource-group | *testrg* | O nome do grupo de recursos em que o cluster será criado. |

    Há outros parâmetros opcionais que podem ser usados, como a capacidade do cluster.

2. Execute o comando a seguir para verificar se o cluster foi criado com êxito:

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

Se o resultado contiver "provisioningState" com o valor "Succeeded", o cluster terá sido criado com êxito.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Criar o banco de dados no cluster do Azure Data Explorer

1. Crie o banco de dados usando o comando a seguir:

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period 3650:00:00:00 --hot-cache-period 3650:00:00:00
    ```

   |**Configuração** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | nome do cluster | *azureclitest* | O nome do cluster em que o banco de dados deve ser criado.|
   | Nome | *clidatabase* | O nome desejado do seu banco de dados.|
   | resource-group | *testrg* | O nome do grupo de recursos em que o cluster será criado. |
   | soft-delete-period | *3650:00:00:00* | Tempo pelo qual os dados devem ser mantidos para que estejam disponíveis para consulta. |
   | hot-cache-period | *3650:00:00:00* | Tempo pelo qual os dados devem ser mantidos no cache. |

2. Execute o comando a seguir para ver o banco de dados criado:

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

Agora você tem um cluster e um banco de dados.

## <a name="clean-up-resources"></a>Limpar recursos

* Caso você planeje seguir nossos outros guias de início rápido e tutoriais, mantenha os recursos que você criou.
* Para limpar recursos, exclua o cluster. Quando você exclui um cluster, também exclui todos os bancos de dados nele. Use o comando abaixo para excluir o cluster:

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Início Rápido: Ingerir dados usando a biblioteca Python do Azure Data Explorer](python-ingest-data.md)
