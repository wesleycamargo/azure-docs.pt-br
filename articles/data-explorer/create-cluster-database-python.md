---
title: 'Início Rápido: Criar um cluster e um banco de dados do Azure Data Explorer usando Python'
description: Saiba como criar um cluster e banco de dados do Azure Data Explorer usando Python
services: data-explorer
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/17/2019
ms.openlocfilehash: 4f87c5996ea323c26c32c1680ba6f627bf8f95c2
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287345"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Criar um cluster e um banco de dados do Azure Data Explorer usando Python

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

Este início rápido descreve como criar um cluster e um banco de dados do Azure Data Explorer usando Python.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa de uma assinatura do Azure. Se você não tiver [uma conta gratuita](https://azure.microsoft.com/free/), crie uma antes de começar.

## <a name="install-python-package"></a>Instalar o pacote do Python

Para instalar o pacote Python para o Azure Data Explorer (Kusto), abra um prompt de comando que tenha Python no caminho e, em seguida, execute este comando:

```
pip install azure-mgmt-kusto
```

## <a name="create-the-azure-data-explorer-cluster"></a>Criar o cluster do Azure Data Explorer

1. Crie o cluster usando o seguinte comando:

    

   |**Configuração** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | cluster_name | *mykustocluster* | O nome desejado do cluster.|
   | sku | *D13_v2* | O SKU que será usado para o cluster. |
   | resource_group_name | *testrg* | O nome do grupo de recursos em que o cluster será criado. |

    Há outros parâmetros opcionais que podem ser usados, como a capacidade do cluster.
    
    Defina as 'credenciais' para as suas credenciais (para obter mais informações, consulte https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python )

2. Execute o comando a seguir para verificar se o cluster foi criado com êxito:

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= clusterName, custom_headers=None, raw=False)
    ```

Se o resultado contém `provisioningState` com o valor `Succeeded`, o cluster foi criado com êxito.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Criar o banco de dados no cluster do Azure Data Explorer

1. Crie o banco de dados usando o seguinte comando:

    ```Python
    from azure.mgmt.kusto.models import Database
    from datetime import timedelta
    
    softDeletePeriod = timedelta(days=3650)
    hotCachePeriod = timedelta(days=3650)
    databaseName="mykustodatabase"
    
    database_operations = kusto_management_client.databases 
    _database = Database(location=location,
                        soft_delete_period=softDeletePeriod,
                        hot_cache_period=hotCachePeriod)
    
    database_operations.create_or_update(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName, parameters = _database)
    ```

   |**Configuração** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | cluster_name | *mykustocluster* | O nome do cluster em que o banco de dados será criado.|
   | database_name | *mykustodatabase* | O nome do banco de dados.|
   | resource_group_name | *testrg* | O nome do grupo de recursos em que o cluster será criado. |
   | soft_delete_period | *3650 dias, 0:00:00* | O tempo durante o qual os dados serão mantidos disponíveis para consulta. |
   | hot_cache_period | *3650 dias, 0:00:00* | O tempo durante o qual os dados serão mantidos no cache. |

2. Execute o seguinte comando para ver o banco de dados criado:

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

Agora você tem um cluster e um banco de dados.

## <a name="clean-up-resources"></a>Limpar recursos

* Caso você planeje seguir nossos outros guias de início rápido e tutoriais, mantenha os recursos que você criou.
* Para limpar recursos, exclua o cluster. Quando você exclui um cluster, também exclui todos os bancos de dados nele. Use o seguinte comando para excluir o cluster:

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Início Rápido: Ingerir dados usando a biblioteca Python do Azure Data Explorer](python-ingest-data.md)
