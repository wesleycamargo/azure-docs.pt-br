---
title: Compile um aplicativo Java com o SDK do Java Assíncrono para gerenciar dados da API de SQL do Azure Cosmos DB
description: Este tutorial mostra como usar contas de API de SQL do Azure Cosmos DB para armazenar e acessar dados por meio de um aplicativo Java assíncrono.
keywords: tutorial do nosql, banco de dados online, aplicativo de console java
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: sngun
ms.openlocfilehash: d9adaaaaf64204e27c07b0728ae4bbc473a5c70a
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52875545"
---
# <a name="tutorial-build-a-java-app-with-async-java-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Tutorial: Compilar um aplicativo Java com o SDK do Java Assíncrono para gerenciar dados da API de SQL do Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET (versão prévia)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core (versão prévia)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Este tutorial mostra como criar um aplicativo Java com o SDK do Java Assíncrono para armazenar e acessar dados da API de SQL do Azure Cosmos DB.

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Criar e conectar-se a uma conta do Azure Cosmos DB
> * Configurar a sua solução
> * Criar uma coleção
> * Criando documentos JSON
> * Consultar a coleção

## <a name="prerequisites"></a>Pré-requisitos

Verifique se você tem os seguintes recursos:

* Uma conta ativa do Azure. Se não tiver uma, você poderá se inscrever em uma [conta gratuita](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Git](https://git-scm.com/downloads).
* [Java Development Kit (JDK) 8+](https://aka.ms/azure-jdks).
* [Maven](http://maven.apache.org/download.cgi).

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Etapa 1: Criar uma conta de banco de dados do Azure Cosmos DB
Vamos criar uma conta do Azure Cosmos DB. Se você já tiver uma conta que deseja usar, poderá pular para [Clonar o projeto do GitHub](#GitClone). Se você estiver usando o Emulador do Azure Cosmos DB, siga as etapas no [Emulador do Azure Cosmos DB](local-emulator.md) para configurar o emulador e pular para [Clonar o projeto do GitHub](#GitClone).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>Etapa 2: clonar o repositório do GitHub

Você pode começar clonando o repositório do GitHub para a [Introdução ao Azure Cosmos DB e Java](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started). Por exemplo, de um diretório local, execute o seguinte para recuperar o projeto de exemplo localmente.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started
```

O diretório contém `pom.xml` e uma pasta `src/main/java/com/microsoft/azure/cosmosdb/sample` que contém o código-fonte em Java, incluindo `Main.java`. O projeto contém o código necessário para executar operações com o Azure Cosmos DB, como criar documentos e consultar dados em uma coleção. O `pom.xml` inclui uma dependência no [SDK do Java do Azure Cosmos DB no Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a id="Connect"></a>Etapa 3: Conectar-se a uma conta do Azure Cosmos DB

Em seguida, retorne ao [portal do Azure](https://portal.azure.com) para recuperar o ponto de extremidade e a chave mestra primária. O ponto de extremidade e a chave primária do Azure Cosmos DB são necessários para que seu aplicativo reconheça onde deve se conectar e para que o Azure Cosmos DB confie na conexão do seu aplicativo. O arquivo `AccountSettings.java` contém a chave primária e os valores de URI. 

No Portal do Azure, navegue até sua conta do Azure Cosmos DB e clique em **Chaves**. Copie o URI e a CHAVE PRIMÁRIA do portal e cole-a no arquivo `AccountSettings.java`. 

```java
public class AccountSettings 
{
  // Replace MASTER_KEY and HOST with values from your Azure Cosmos DB account.
    
  // <!--[SuppressMessage("Microsoft.Security", "CS002:SecretInNextLine")]-->
  public static String MASTER_KEY = System.getProperty("ACCOUNT_KEY", 
          StringUtils.defaultString(StringUtils.trimToNull(
          System.getenv().get("ACCOUNT_KEY")), "<Fill your Azure Cosmos DB account key>"));

  public static String HOST = System.getProperty("ACCOUNT_HOST",
           StringUtils.defaultString(StringUtils.trimToNull(
           System.getenv().get("ACCOUNT_HOST")),"<Fill your Azure Cosmos DB URI>"));
}
```

![Obter chaves da captura de tela do portal][keys]

## <a name="step-4-initialize-the-client-object"></a>Etapa 4: Inicializar o objeto de cliente

Inicializar o objeto cliente usando os valores de URI e de chave primária do host definidos no arquivo “AccountSettings.java”

```java
client = new AsyncDocumentClient.Builder()
         .withServiceEndpoint(AccountSettings.HOST)
         .withMasterKey(AccountSettings.MASTER_KEY)
         .withConnectionPolicy(ConnectionPolicy.GetDefault())
         .withConsistencyLevel(ConsistencyLevel.Session)
         .build();
```

## <a id="CreateDatabase"></a>Etapa 5: Criar um banco de dados

Seu [banco de dados](databases-containers-items.md#azure-cosmos-databases) do Azure Cosmos DB pode ser criado com o método createDatabaseIfNotExists() da classe DocumentClient. Um banco de dados é o contêiner lógico de armazenamento de documentos JSON particionado em coleções.

```java
private void createDatabaseIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if database " + databaseName + " exists.");

    String databaseLink = String.format("/dbs/%s", databaseName);

    Observable<ResourceResponse<Database>> databaseReadObs = client.readDatabase(databaseLink, null);

    Observable<ResourceResponse<Database>> databaseExistenceObs = databaseReadObs
                .doOnNext(x -> {System.out.println("database " + databaseName + " already exists.");}).onErrorResumeNext(e -> {
   // if the database doesn't already exists
   // readDatabase() will result in 404 error
   if (e instanceof DocumentClientException) 
   {
       DocumentClientException de = (DocumentClientException) e;
       // if database 
       if (de.getStatusCode() == 404) {
       // if the database doesn't exist, create it.
       System.out.println("database " + databaseName + " doesn't existed," + " creating it...");
       Database dbDefinition = new Database();
       dbDefinition.setId(databaseName);
       return client.createDatabase(dbDefinition, null);
     }
     }

    // some unexpected failure in reading database happened.
    // pass the error up.
    System.err.println("Reading database " + databaseName + " failed.");
        return Observable.error(e);     
    });

   // wait for completion
   databaseExistenceObs.toCompletable().await();

   System.out.println("Checking database " + databaseName + " completed!\n");
}
```

## <a id="CreateColl"></a>Etapa 6: Criar uma coleção

> [!WARNING]
> **createCollection** criará uma nova coleção com uma taxa de transferência reservada, que tem implicações de preço. Para obter mais detalhes, visite a nossa [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 
É possível criar uma coleção com o método createDocumentCollectionIfNotExists() da classe DocumentClient. Uma coleção é um contêiner de documentos JSON e uma lógica de aplicativo JavaScript associada.

```java
private void createDocumentCollectionIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if collection " + collectionName + " exists.");

    // query for a collection with a given id
    // if it exists nothing else to be done
    // if the collection doesn't exist, create it.

    String databaseLink = String.format("/dbs/%s", databaseName);

    // we know there is only single page of result (empty or with a match)
    client.queryCollections(databaseLink, new SqlQuerySpec("SELECT * FROM r where r.id = @id", 
            new SqlParameterCollection(new SqlParameter("@id", collectionName))), null).single() 
            .flatMap(page -> {
            if (page.getResults().isEmpty()) {
             // if there is no matching collection create the collection.
             DocumentCollection collection = new DocumentCollection();
             collection.setId(collectionName);
             System.out.println("Creating collection " + collectionName);

             return client.createCollection(databaseLink, collection, null);
            } 
            else {
              // collection already exists, nothing else to be done.
              System.out.println("Collection " + collectionName + "already exists");
              return Observable.empty();
            }
      }).toCompletable().await();

 System.out.println("Checking collection " + collectionName + " completed!\n");
    }
```

## <a id="CreateDoc"></a>Etapa 7: Criar documentos JSON

Um documento pode ser criado usando o método createDocument da classe DocumentClient. Os documentos são conteúdo JSON (arbitrário) definido pelo usuário. Agora podemos inserir um ou mais documentos. O arquivo “src/main/java/com/microsoft/azure/cosmosdb/sample/Families.java” define os documentos da família JSON 

```java
public static Family getJohnsonFamilyDocument() {
     Family andersenFamily = new Family();
     andersenFamily.setId("Johnson" + System.currentTimeMillis());
     andersenFamily.setLastName("Johnson");

      Parent parent1 = new Parent();
      parent1.setFirstName("John");

      Parent parent2 = new Parent();
      parent2.setFirstName("Lili");

      return andersenFamily;
    }
```

## <a id="Query"></a>Etapa 8: Consultar recursos do Azure Cosmos DB

O Azure Cosmos DB tem suporte para [consultas](how-to-sql-query.md) avançadas de documentos JSON armazenados em cada coleção. O código de exemplo a seguir mostra como consultar documentos no Azure Cosmos DB usando a sintaxe SQL com o método [queryDocuments](/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.querydocuments).

```java
private void executeSimpleQueryAsyncAndRegisterListenerForResult(CountDownLatch completionLatch) 
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setMaxItemCount(100);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(collectionLink,
           "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    queryObservable.subscribe(queryResultPage -> {
            System.out.println("Got a page of query result with " +
            queryResultPage.getResults().size() + " document(s)"
            + " and request charge of " + queryResultPage.getRequestCharge());
    },
   // terminal error signal
        e -> {
          e.printStackTrace();
          completionLatch.countDown();
        },

   // terminal completion signal
         () -> {
            completionLatch.countDown();
         });
}
```

## <a id="Run"></a>Etapa 9: executar o aplicativo de console Java

Para executar o aplicativo de console, navegue até a pasta de projeto e compile usando o Maven:

```bash
mvn package
```

A execução de `mvn package` baixa a biblioteca mais recente do Azure Cosmos DB do Maven e produz `GetStarted-0.0.1-SNAPSHOT.jar`. Em seguida, execute o aplicativo por meio de:

```bash
mvn exec:java -DACCOUNT_HOST=<YOUR_COSMOS_DB_HOSTNAME> -DACCOUNT_KEY= <YOUR_COSMOS_DB_MASTER_KEY>
```

Parabéns! Você concluiu este tutorial do NoSQL e tem um aplicativo de console em Java funcional!

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar um aplicativo Java com o SDK do Java Assíncrono para gerenciar dados da API de SQL do Azure Cosmos DB. Prossiga agora para o próximo artigo:

> [!div class="nextstepaction"]
> [Compilar um aplicativo de console do Node.js com o SDK JavaScript e o Azure Cosmos DB](sql-api-nodejs-get-started.md)

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
