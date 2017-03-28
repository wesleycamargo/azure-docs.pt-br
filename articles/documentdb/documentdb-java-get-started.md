---
title: 'Tutorial do oSQL: SDK do Java do Azure DocumentDB | Microsoft Docs'
description: "Um tutorial do NoSQL que cria um banco de dados online e um aplicativo de console Java usando o SDK do Java do DocumentDB. O Azure DocumentDB é um banco de dados NoSQL para JSON."
keywords: tutorial do nosql, banco de dados online, aplicativo de console java
services: documentdb
documentationcenter: Java
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: 75a9efa1-7edd-4fed-9882-c0177274cbb2
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: hero-article
ms.date: 01/05/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: ddd676df429c20d1c07cfe64abc9ab69ef11bd8c
ms.openlocfilehash: 845858c3df6456293a2552f55ffb35254024931b
ms.lasthandoff: 01/06/2017


---
# <a name="nosql-tutorial-build-a-documentdb-java-console-application"></a>Tutorial do NoSQL: Criar um aplicativo de console em Java do DocumentDB
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Bem-vindo ao tutorial do NoSQL para o SDK do Java do Azure DocumentDB! Após seguir este tutorial, você terá um aplicativo de console que cria e consulta recursos de Banco de Dados de Documentos.

Abordamos:

* Criando e se conectando a uma conta do Banco de Dados de Documentos
* Configurar a sua Solução do Visual Studio
* Criando um banco de dados online
* Criar uma coleção
* Criando documentos JSON
* Consultar a coleção
* Criando documentos JSON
* Consultar a coleção
* Substituição de um documento
* Exclusão de um documento
* Excluir o banco de dados

Agora vamos começar!

## <a name="prerequisites"></a>Pré-requisitos
Verifique se você tem o seguinte:

* Uma conta ativa do Azure. Se não tiver uma, você poderá se inscrever em uma [conta gratuita](https://azure.microsoft.com/free/). Como alternativa, você pode usar o [Emulador do DocumentDB do Azure](documentdb-nosql-local-emulator.md) para este tutorial.
* [Git](https://git-scm.com/downloads)
* [Java Development Kit (JDK) 7 +](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Maven](http://maven.apache.org/download.cgi).

## <a name="step-1-create-a-documentdb-account"></a>Etapa 1: Criar uma conta do Banco de Dados de Documentos
Vamos criar uma conta de Banco de Dados de Documentos. Se você já tiver uma conta que deseja usar, poderá pular para [Clonar o projeto do Github](#GitClone). Se você estiver usando o Emulador do DocumentDB, siga as etapas em [Emulador do Azure DocumentDB](documentdb-nosql-local-emulator.md) para configurar o emulador e pular para a [Clonar o projeto do Github](#GitClone).

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="GitClone"></a>Etapa 2: Clonar o projeto do Github
Você pode começar pela clonagem de repositório Github para [Introdução ao DocumentDB e Java](https://github.com/Azure-Samples/documentdb-java-getting-started). Por exemplo, de um diretório local, execute o seguinte para recuperar o projeto de exemplo localmente.

    git clone git@github.com:Azure-Samples/documentdb-java-getting-started.git

    cd documentdb-java-getting-started

O diretório contém um `pom.xml` para o projeto e uma pasta `src` que contém o código de origem de Java incluindo `Program.java`, que mostra como executar operações simples com o Azure DocumentDB, como criação de documentos e consulta de dados em uma coleção. O `pom.xml` inclui uma dependência no [SDK do Java do DocumentDB no Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-documentdb</artifactId>
        <version>LATEST</version>
    </dependency>

## <a id="Connect"></a>Etapa 3: conectar-se a uma conta do Banco de Dados de Documentos
Em seguida, retorne ao [Portal do Azure](https://portal.azure.com) para recuperar o ponto de extremidade e a chave mestra primária. O ponto de extremidade e a chave primária do DocumentDB são necessários para que seu aplicativo entenda onde deve se conectar e para que o DocumentDB confie na conexão do seu aplicativo.

No Portal do Azure, navegue até sua conta do Banco de Dados de Documentos e clique em **Chaves**. Copie o URI do portal e cole-o em `<your endpoint URI>` no arquivo Program.java. Em seguida, copie a CHAVE PRIMÁRIA do portal e cole-a em `<your key>`.

    this.client = new DocumentClient(
        "<your endpoint URI>",
        "<your key>"
        , new ConnectionPolicy(),
        ConsistencyLevel.Session);

![Captura de tela do Portal do Azure usado pelo tutorial do NoSQL para criar um aplicativo de console em Java. Mostra uma conta do Banco de Dados de Documentos com o hub ATIVO realçado, o botão CHAVES realçado na folha da conta do Banco de Dados de Documentos e os valores de URI, de CHAVE PRIMÁRIA e de CHAVE SECUNDÁRIA realçados na folha Chaves][keys]

## <a name="step-4-create-a-database"></a>Etapa 4: criar um banco de dados
Seu [banco de dados](documentdb-resources.md#databases) do DocumentDB pode ser criado usando o método [createDatabase](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createDatabase-com.microsoft.azure.documentdb.Database-com.microsoft.azure.documentdb.RequestOptions-) da classe **DocumentClient**. Um banco de dados é o contêiner lógico de armazenamento de documentos JSON particionado em coleções.

    Database database = new Database();
    database.setId("familydb");
    this.client.createDatabase(database, null);

## <a id="CreateColl"></a>Etapa 5: Criar uma coleção
> [!WARNING]
> **createCollection** criará uma nova coleção com uma taxa de transferência reservada, que tem implicações de preço. Para obter mais detalhes, visite a nossa [página de preços](https://azure.microsoft.com/pricing/details/documentdb/).
> 
> 

Uma [coleção](documentdb-resources.md#collections) pode ser criada usando o método [createCollection](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createCollection-java.lang.String-com.microsoft.azure.documentdb.DocumentCollection-com.microsoft.azure.documentdb.RequestOptions-) da classe **DocumentClient**. Uma coleção é um contêiner de documentos JSON e uma lógica de aplicativo JavaScript associada.


    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId("familycoll");

    // DocumentDB collections can be reserved with throughput specified in request units/second. 
    // Here we create a collection with 400 RU/s.
    RequestOptions requestOptions = new RequestOptions();
    requestOptions.setOfferThroughput(400);

    this.client.createCollection("/dbs/familydb", collectionInfo, requestOptions);

## <a id="CreateDoc"></a>Etapa 6: Criar documentos JSON
Um [documento](documentdb-resources.md#documents) pode ser criado usando o método [createDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#createDocument-java.lang.String-java.lang.Object-com.microsoft.azure.documentdb.RequestOptions-boolean-) da classe **DocumentClient**. Os documentos são conteúdo JSON (arbitrário) definido pelo usuário. Agora podemos inserir um ou mais documentos. Se já tiver dados que deseja armazenar no banco de dados, você poderá usar a [ferramenta de migração de dados](documentdb-import-data.md) do Document DB para importar os dados para um banco de dados.

    // Insert your Java objects as documents 
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen")

    // More initialization skipped for brevity. You can have nested references
    andersenFamily.setParents(new Parent[] { parent1, parent2 });
    andersenFamily.setDistrict("WA5");
    Address address = new Address();
    address.setCity("Seattle");
    address.setCounty("King");
    address.setState("WA");

    andersenFamily.setAddress(address);
    andersenFamily.setRegistered(true);

    this.client.createDocument("/dbs/familydb/colls/familycoll", family, new RequestOptions(), true);

![Diagrama que ilustra a relação hierárquica entre a conta, o banco de dados online, a coleção e os documentos usados pelo tutorial do NoSQL para criar um aplicativo de console em Java](./media/documentdb-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Etapa 7: Recursos de consulta do Banco de Dados de Documentos
O Banco de Dados de Documentos tem suporte para [consultas](documentdb-sql-query.md) avançadas de documentos JSON armazenados em cada coleção.  O código de exemplo a seguir mostra como consultar documentos no DocumentDB usando a sintaxe SQL com o método [queryDocuments](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#queryDocuments-java.lang.String-com.microsoft.azure.documentdb.SqlQuerySpec-com.microsoft.azure.documentdb.FeedOptions-).

    FeedResponse<Document> queryResults = this.client.queryDocuments(
        "/dbs/familydb/colls/familycoll",
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", 
        null);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }

## <a id="ReplaceDocument"></a>Etapa 8: substituir o documento JSON
O DocumentDB dá suporte à atualização de documentos JSON usando o método [replaceDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#replaceDocument-com.microsoft.azure.documentdb.Document-com.microsoft.azure.documentdb.RequestOptions-).

    // Update a property
    andersenFamily.Children[0].Grade = 6;

    this.client.replaceDocument(
        "/dbs/familydb/colls/familycoll/docs/Andersen.1", 
        andersenFamily,
        null);

## <a id="DeleteDocument"></a>Etapa 9: excluir o documento JSON
Da mesma forma, o DocumentDB dá suporte à exclusão de documentos JSON usando o método [deleteDocument](http://azure.github.io/azure-documentdb-java/com/microsoft/azure/documentdb/DocumentClient.html#deleteDocument-java.lang.String-com.microsoft.azure.documentdb.RequestOptions-).  

    this.client.delete("/dbs/familydb/colls/familycoll/docs/Andersen.1", null);

## <a id="DeleteDatabase"></a>Etapa 10: excluir o banco de dados
Excluir o banco de dados criado remove o banco de dados e todos os recursos filhos (coleções, documentos etc.).

    this.client.deleteDatabase("/dbs/familydb", null);

## <a id="Run"></a>Etapa 11: Executar o aplicativo de console Java inteiro!
Para executar o aplicativo do console, primeiro compile usando o Maven:
    
    mvn package

A execução de `mvn package` baixa a biblioteca mais recente do DocumentDB do Maven e produz `GetStarted-0.0.1-SNAPSHOT.jar`. Em seguida, execute o aplicativo por meio de:

    mvn exec:java -D exec.mainClass=GetStarted.Program

Parabéns! Você concluiu este tutorial do NoSQL e tem um aplicativo de console em Java funcional!

## <a name="next-steps"></a>Próximas etapas
* Quer um tutorial do aplicativo Web Java? Veja [Criar um aplicativo Web com Java usando o DocumentDB](documentdb-java-application.md).
* Saiba como [monitorar uma conta do Banco de Dados de Documentos](documentdb-monitor-accounts.md).
* Executar consultas em nosso conjunto de dados de exemplo no [Query Playground](https://www.documentdb.com/sql/demo).
* Saiba mais sobre o modelo de programação na seção Desenvolvimento da [Página de documentação do Banco de Dados de Documentos](https://azure.microsoft.com/documentation/services/documentdb/).

[documentdb-create-account]: documentdb-create-account.md
[keys]: media/documentdb-get-started/nosql-tutorial-keys.png

