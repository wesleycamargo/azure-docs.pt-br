---
title: 'Início rápido: Criar um cluster e banco de dados do Azure Data Explorer usando PowerShell'
description: Saiba como criar um cluster e banco de dados do Azure Data Explorer usando PowerShell
services: data-explorer
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/17/2019
ms.openlocfilehash: 650bdc5cdf99645bc2be6c8e85737dacd10a6b27
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287344"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>Criar um cluster e banco de dados do Azure Data Explorer usando PowerShell

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  


Este início rápido descreve como criar um cluster e um banco de dados do Azure Data Explorer usando PowerShell.

É possível executar cmdlets e scripts do PowerShell no Windows, no Linux ou no [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) para criar e configurar o [Azure Data Explorer](https://docs.microsoft.com/azure/kusto/ ).

O [**Az.Kusto**](https://docs.microsoft.com/powershell/module/az.kusto/?view=azps-1.4.0#kusto ). Com o Azure PowerShell e o **Az.Kusto**, é possível executar as seguintes tarefas:

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa de uma assinatura do Azure. Se você não tiver [uma conta gratuita](https://azure.microsoft.com/free/), crie uma antes de começar.

## <a name="configure-parameters"></a>Configurar parâmetros

As etapas a seguir não serão necessárias se você estiver executando comandos no Azure Cloud Shell. Se você estiver executando a CLI localmente, siga estas etapas para entrar no Azure e definir a assinatura atual:

1. Execute o comando a seguir para entrar no Azure:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Defina a assinatura em que deseja criar o cluster.

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
3. Instale o módulo Az.Kusto no dispositivo:
    
    ```azurepowershell-interactive
     Install-Module -Name Az.Kusto  
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Criar o cluster do Azure Data Explorer

1. Crie o cluster usando o seguinte comando:

    ```azurepowershell-interactive
     New-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster -Location 'Central US' -Sku D13_v2 -Capacity 10
    ```

   |**Configuração** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | NOME | *mykustocluster* | O nome desejado do cluster.|
   | Sku | *D13_v2* | O SKU que será usado para o cluster. |
   | ResourceGroupName | *testrg* | O nome do grupo de recursos em que o cluster será criado. |

    Há outros parâmetros opcionais que podem ser usados, como a capacidade do cluster.

2. Execute o comando a seguir para verificar se o cluster foi criado com êxito:

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name mykustocluster --ResourceGroupName testrg
    ```

Se o resultado contém `provisioningState` com o valor `Succeeded`, o cluster foi criado com êxito.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Criar o banco de dados no cluster do Azure Data Explorer

1. Crie o banco de dados usando o seguinte comando:

    ```azurepowershell-interactive
    New-AzKustoDatabase -ResourceGroupName testrg -ClusterName mykustocluster -Name mykustodatabase -SoftDeletePeriod 3650:00:00:00 -HotCachePeriod 3650:00:00:00
    ```

   |**Configuração** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | ClusterName | *mykustocluster* | O nome do cluster em que o banco de dados será criado.|
   | NOME | *mykustodatabase* | O nome do banco de dados.|
   | ResourceGroupName | *testrg* | O nome do grupo de recursos em que o cluster será criado. |
   | SoftDeletePeriod | *3650:00:00:00* | O tempo durante o qual os dados serão mantidos disponíveis para consulta. |
   | HotCachePeriod | *3650:00:00:00* | O tempo durante o qual os dados serão mantidos no cache. |

2. Execute o seguinte comando para ver o banco de dados criado:

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster --ResourceGroupName testrg -Name mykustodatabase
    ```

Agora você tem um cluster e um banco de dados.

## <a name="clean-up-resources"></a>Limpar recursos

* Caso você planeje seguir nossos outros guias de início rápido e tutoriais, mantenha os recursos que você criou.
* Para limpar recursos, exclua o cluster. Quando você exclui um cluster, também exclui todos os bancos de dados nele. Use o seguinte comando para excluir o cluster:

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>Próximas etapas

Você pode encontrar mais comandos do Az.Kusto [**aqui**](https://docs.microsoft.com/powershell/module/az.kusto/?view=azps-1.4.0#kusto )

> [!div class="nextstepaction"]
> [Início Rápido: Ingerir dados usando o SDK do .NET Standard no Azure Data Explorer (Versão prévia)](net-standard-ingest-data.md)
