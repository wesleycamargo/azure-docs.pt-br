---
title: 'Início rápido: Criar um cluster e banco de dados do Azure Data Explorer usando C#'
description: Saiba como criar um cluster e banco de dados do Azure Data Explorer usando C#
services: data-explorer
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/17/2019
ms.openlocfilehash: d861eba6ce905ccaf0d08a08cdd9998a199889da
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287342"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>Criar um cluster e banco de dados do Azure Data Explorer usando C#

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  


Este início rápido descreve como criar um cluster e um banco de dados do Azure Data Explorer usando C#.

## <a name="prerequisites"></a>Pré-requisitos

- Se você ainda não tem o Visual 2017 Studio instalado, poderá baixar e usar o **Visual Studio 2017 Community Edition** [gratuito](https://www.visualstudio.com/downloads/). Verifique se você habilitou o **desenvolvimento do Azure** durante a instalação do Visual Studio.

- Para concluir este início rápido, você precisa de uma assinatura do Azure. Se você não tiver [uma conta gratuita](https://azure.microsoft.com/free/), crie uma antes de começar.

## <a name="install-c-nuget"></a>Instalar NuGet C#

- Será necessário ter o pacote NuGet para Azure Data Explorer (Kusto), o qual pode ser encontrado aqui: https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/
- Além disso, você precisará do NuGet Microsoft.IdentityModel.Clients.ActiveDirectory para autenticação https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/


## <a name="create-the-azure-data-explorer-cluster"></a>Criar o cluster do Azure Data Explorer

1. Crie o cluster usando o código a seguir:

    ```C#-interactive
    string resourceGroupName = "testrg";    
    string clusterName = "mykustocluster";
    string location = "Central US";
    AzureSku sku = new AzureSku("D13_v2", 5);
    Cluster cluster = new Cluster(location, sku);
    
    ar authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantName}");
    var credential = new ClientCredential(clientId: "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx", clientSecret: "xxxxxxxxxxxxxx");
    var result = authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential).Result;
    
    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);
     
    KustoManagementClient KustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    };

    KustoManagementClient.Clusters.CreateOrUpdate(resourceGroupName, clusterName, cluster);
    ```

   |**Configuração** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | clusterName | *mykustocluster* | O nome desejado do cluster.|
   | sku | *D13_v2* | O SKU que será usado para o cluster. |
   | resourceGroupName | *testrg* | O nome do grupo de recursos em que o cluster será criado. |

    Há outros parâmetros opcionais que podem ser usados, como a capacidade do cluster.
    
    Defina as 'credenciais' para as suas credenciais (para obter mais informações, consulte https://docs.microsoft.com/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet )

2. Execute o comando a seguir para verificar se o cluster foi criado com êxito:

    ```C#-interactive
    KustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

Se o resultado contém `ProvisioningState` com o valor `Succeeded`, o cluster foi criado com êxito.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Criar o banco de dados no cluster do Azure Data Explorer

1. Crie o banco de dados usando o código a seguir:

    ```c#-interactive
    TimeSpan hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    TimeSpan softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    string databaseName = "mykustodatabase";
    Database database = new Database(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);
    
    KustoManagementClient.Databases.CreateOrUpdate(resourceGroupName, clusterName, databaseName, database);
    ```

   |**Configuração** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | clusterName | *mykustocluster* | O nome do cluster em que o banco de dados será criado.|
   | databaseName | *mykustodatabase* | O nome do banco de dados.|
   | resourceGroupName | *testrg* | O nome do grupo de recursos em que o cluster será criado. |
   | softDeletePeriod | *3650:00:00:00* | O tempo durante o qual os dados serão mantidos disponíveis para consulta. |
   | hotCachePeriod | *3650:00:00:00* | O tempo durante o qual os dados serão mantidos no cache. |

2. Execute o seguinte comando para ver o banco de dados criado:

    ```c#-interactive
    KustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName);
    ```

Agora você tem um cluster e um banco de dados.

## <a name="clean-up-resources"></a>Limpar recursos

* Caso você planeje seguir nossos outros guias de início rápido e tutoriais, mantenha os recursos que você criou.
* Para limpar recursos, exclua o cluster. Quando você exclui um cluster, também exclui todos os bancos de dados nele. Use o seguinte comando para excluir o cluster:

    ```C#-interactive
    KustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Início Rápido: Ingerir dados usando o SDK do .NET Standard no Azure Data Explorer (Versão prévia)](net-standard-ingest-data.md)
