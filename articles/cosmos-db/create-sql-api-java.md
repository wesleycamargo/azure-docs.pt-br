---
title: Criar um banco de dados de documentos do Azure Cosmos DB com Java'
description: Apresenta um exemplo de código Java que pode ser usado para se conectar à API do SQL do Azure Cosmos DB e consultá-la
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.custom: quick start connect, mvc, devcenter
ms.devlang: java
ms.topic: quickstart
ms.date: 10/24/2018
ms.author: moderakh
ms.openlocfilehash: 65af8a27d864231e6302d813836e2f63803234d2
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52844770"
---
# <a name="create-and-manage-resources-of-an-azure-cosmos-db-sql-api-account-using-a-java-application"></a>Criar e gerenciar recursos de uma conta da API de SQL do Azure Cosmos DB usando um aplicativo Java

> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [.NET (versão prévia)](create-sql-api-dotnet-preview.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Este início rápido mostra como criar e gerenciar recursos de uma conta da [API de SQL](sql-api-introduction.md) do Azure Cosmos DB usando um aplicativo Java. Primeiro, crie uma conta da API de SQL do Azure Cosmos DB usando o portal do Azure; crie um aplicativo Java usando o [SDK do Java para SQL](sql-api-sdk-async-java.md) e adicione recursos à conta do Cosmos DB usando o aplicativo Java. Estas instruções podem ser seguidas em qualquer sistema operacional compatível com Java. Ao concluir este início rápido, você estará familiarizado com a criação e a modificação de coleções e bancos de dados do Cosmos DB na interface do usuário ou programaticamente, o que preferir.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Além disso: 

* [JDK (Java Development Kit) 1.8+](https://aka.ms/azure-jdks)
    * No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.
    * Defina a variável de ambiente JAVA_HOME para apontar para a pasta onde o JDK está instalado.
* [Baixar](http://maven.apache.org/download.cgi) e [instalar](http://maven.apache.org/install.html) um armazenamento binário [Maven](http://maven.apache.org/)
    * No Ubuntu, você pode executar `apt-get install maven` para instalar o Maven.
* [Git](https://www.git-scm.com/)
    * No Ubuntu, você pode executar `sudo apt-get install git` para instalar o Git.

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

Antes de criar um banco de dados de documentos, você precisa criar uma conta de API do SQL com o Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Adicionar uma coleção

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Adicionar dados de exemplo

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Consultar seus dados

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora, vamos trabalhar com o código. Vamos clonar um aplicativo de API do SQL do GitHub, definir a cadeia de conexão e executá-lo. Você verá como é fácil trabalhar usando dados de forma programática. 

1. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started
    ```

## <a name="review-the-code"></a>Examine o código

Esta etapa é opcional. Se você estiver interessado em aprender como os recursos de banco de dados são criados no código, poderá examinar os snippets de código a seguir. Caso contrário, você poderá seguir para [Executar o aplicativo](#run-the-app). 

* Inicialização de `AsyncDocumentClient`. O [AsyncDocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client) fornece a representação lógica do lado do cliente para o serviço de banco de dados do Azure Cosmos DB. Esse cliente é usado para configurar e executar solicitações no serviço.

    ```java
    client = new AsyncDocumentClient.Builder()
             .withServiceEndpoint(YOUR_COSMOS_DB_ENDPOINT)
             .withMasterKeyOrResourceToken(YOUR_COSMOS_DB_MASTER_KEY)
             .withConnectionPolicy(ConnectionPolicy.GetDefault())
             .withConsistencyLevel(ConsistencyLevel.Eventual)
             .build();
    ```

* Criação do [Banco de Dados](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb._database).

    ```java
    Database databaseDefinition = new Database();
    databaseDefinition.setId(databaseName);
    
    client.createDatabase(databaseDefinition, null)
            .toCompletable()
            .await();
    ```

* Criação de [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb._document_collection).

    ```java
    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.setId(collectionName);

    //...

    client.createCollection(databaseLink, collectionDefinition, requestOptions)
            .toCompletable()
            .await();
    ```

* Criação de documentos usando o método [createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb._document).

    ```java
    // Any Java object within your code
    // can be serialized into JSON and written to Azure Cosmos DB
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen");
    // More properties

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    client.createDocument(collectionLink, family, null, true)
            .toCompletable()
            .await();

    ```

* Consultas SQL em JSON são executadas usando o método [queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.querydocuments?view=azure-java-stable).

    ```java
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setPageSize(-1);
    queryOptions.setEnableCrossPartitionQuery(true);
    queryOptions.setMaxDegreeOfParallelism(-1);

    String collectionLink = String.format("/dbs/%s/colls/%s",
            databaseName,
            collectionName);
    Iterator<FeedResponse<Document>> it = client.queryDocuments(
            collectionLink,
            "SELECT * FROM Family WHERE Family.lastName = 'Andersen'",
            queryOptions).toBlocking().getIterator();

    System.out.println("Running SQL query...");
    while (it.hasNext()) {
        FeedResponse<Document> page = it.next();
        System.out.println(
                String.format("\tRead a page of results with %d items",
                        page.getResults().size()));
        for (Document doc : page.getResults()) {
            System.out.println(String.format("\t doc %s", doc));
        }
    }
    ```    

## <a name="run-the-app"></a>Execute o aplicativo

Agora volte ao portal do Azure para obter as informações de cadeia de conexão e iniciar o aplicativo com as informações de ponto de extremidade. Isso permite que seu aplicativo se comunique com o banco de dados hospedado.


1. Na janela do terminal do Git, execute `cd` na pasta do código de exemplo.

    ```bash
    cd azure-cosmos-db-sql-api-async-java-getting-started/azure-cosmosdb-get-started
    ```

2. Na janela do terminal git, use o seguinte comando para instalar os pacotes necessários do Java.

    ```bash
    mvn package
    ```

3. Na janela do terminal git, use o seguinte comando para iniciar o aplicativo Java (substitua YOUR_COSMOS_DB_HOSTNAME pelo valor entre aspas do URI do portal e substitua YOUR_COSMOS_DB_MASTER_KEY pela chave primária entre aspas do portal)

    ```bash
    mvn exec:java -DACCOUNT_HOST=YOUR_COSMOS_DB_HOSTNAME -DACCOUNT_KEY=YOUR_COSMOS_DB_MASTER_KEY

    ```

    A janela de terminal exibe uma notificação de que o banco de dados FamilyDB foi criado. 
    
4. Pressione uma tecla para criar o banco de dados e outra tecla para criar a coleção. 

    Alterne para o Data Explorer no navegador para ver que agora ele contém um banco de dados FamilyDB e uma coleção FamilyCollection.

5. Alterne para a janela do console e pressione uma tecla para criar o primeiro documento, depois pressione outra tecla para criar o segundo documento. Depois, alterne de volta para o Data Explorer para exibi-los. 

6. Pressione uma tecla para executar uma consulta e ver a saída na janela do console. 

7. O aplicativo não exclui os recursos criados. Volte para o portal a fim de [limpar os recursos](#clean-up-resources).  da sua conta para que você não incorra em encargos.

    ![Saída do console](./media/create-sql-api-java/rxjava-console-output.png)


## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como criar uma conta do Azure Cosmos DB, uma coleção usando o Data Explorer e executar um aplicativo para fazer a mesma coisa programaticamente. Agora, é possível importar dados adicionais na coleção do Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importar dados no Azure Cosmos DB](import-data.md)
