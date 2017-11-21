---
title: Criar um banco de dados de documentos do Azure Cosmos DB com Java | Microsoft Docs | Microsoft Docs
description: "Apresenta um exemplo de código Java que pode ser usado para se conectar à API do DocumentDB do Azure Cosmos DB e consultá-la"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmos-db
ms.custom: quick start connect, mvc, devcenter
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: quickstart
ms.date: 10/30/2017
ms.author: mimig
ms.openlocfilehash: 5a793abdc24387ae2b758d29b9dfb25f134097d3
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2017
---
# <a name="azure-cosmos-db-create-a-document-database-using-java-and-the-azure-portal"></a>Azure Cosmos DB: Criar um banco de dados de documentos usando o Java e o portal do Azure

O BD Cosmos do Azure é o serviço multimodelo de banco de dados distribuído globalmente da Microsoft. Usando o Azure Cosmos DB, você pode criar e consultar rapidamente documentos, tabelas e bancos de dados de grafo gerenciados.

Este início rápido cria um banco de dados de documentos usando as ferramentas do portal do Azure para o Azure Cosmos DB. Este início rápido também mostra como criar rapidamente um aplicativo de console do Java usando a [API Java do DocumentDB](documentdb-sdk-java.md). Estas instruções podem ser seguidas em qualquer sistema operacional compatível com Java. Ao concluir este início rápido, você estará familiarizado com a criação e a modificação dos recursos do banco de dados de documentos na interface do usuário ou programaticamente, o que for sua preferência.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Além disso: 

* [Java Development Kit (JDK) 1.7 +](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.
    * Defina a variável de ambiente JAVA_HOME para apontar para a pasta onde o JDK está instalado.
* [Baixar](http://maven.apache.org/download.cgi) e [instalar](http://maven.apache.org/install.html) um armazenamento binário [Maven](http://maven.apache.org/)
    * No Ubuntu, você pode executar `apt-get install maven` para instalar o Maven.
* [Git](https://www.git-scm.com/)
    * No Ubuntu, você pode executar `sudo apt-get install git` para instalar o Git.

## <a name="create-a-database-account"></a>Crie uma conta de banco de dados

Antes de criar um banco de dados de documentos, você precisa criar uma conta do banco de dados SQL (DocumentDB) com o Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Adicionar uma coleção

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Adicionar dados de exemplo

Agora você pode adicionar dados à sua nova coleção usando o Data Explorer.

1. Expanda a coleção **Itens**, clique em **Documentos** > **Novo Documento**.

   ![Criar novos documentos no Data Explorer no portal do Azure](./media/create-documentdb-java/azure-cosmosdb-data-explorer-new-document.png)
  
2. Agora, adicione um documento à coleção com a seguinte estrutura e clique em **Salvar**.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

    ![Copie nos dados json e clique em Salvar no Data Explorer no portal do Azure](./media/create-documentdb-java/azure-cosmosdb-data-explorer-save-document.png)

3.  Crie e salve mais um documento em que você altera `id` para 2 e altere as outras propriedades quando achar adequado. Os novos documentos podem ter qualquer estrutura que você deseje, pois o Azure Cosmos DB não impõe nenhum esquema para seus dados.

## <a name="query-your-data"></a>Consultar seus dados

Agora você pode usar consultas no Data Explorer para recuperar e filtrar os dados.

1. Veja que por padrão, a consulta está definida como `SELECT * FROM c`. Essa consulta padrão recupera e exibe todos os documentos na coleção. 

    ![A consulta padrão no Data Explorer é `SELECT * FROM c`](./media/create-documentdb-java/azure-cosmosdb-data-explorer-query.png)

2. Altere a consulta clicando no botão **Editar Filtro**, adicionando `ORDER BY c._ts DESC` à caixa de predicado de consulta e clicando em **Aplicar Filtro**.

    ![Altere a consulta padrão adicionando ORDER BY c._ts DESC e clicando Aplicar Filtro](./media/create-documentdb-java/azure-cosmosdb-data-explorer-edit-query.png)

Essa consulta modificada lista os documentos em ordem decrescente com base em seu carimbo de data/hora, portanto, agora o segundo documento é listado primeiro. Se você estiver familiarizado com a sintaxe do SQL, poderá inserir quaisquer [consultas SQL](documentdb-sql-query.md) nesta caixa. 

Isso conclui nosso trabalho no Data Explorer. Antes de prosseguirmos para trabalhar com código, observe que você também pode usar o Data Explorer para criar procedimentos armazenados, UDFs e gatilhos para executar a lógica de negócios do servidor, além de dimensionar a taxa de transferência. O Data Explorer expõe todo o acesso a dados interno via programação disponível nas APIs, mas oferece acesso fácil aos dados no Portal do Azure.

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora, vamos trabalhar com o código. Vamos clonar um aplicativo de API do DocumentDB do GitHub, definir a cadeia de conexão e executá-la. Você verá como é fácil trabalhar usando dados de forma programática. 

1. Abra uma janela de terminal de git, como git bash, e use o comando `cd` para alterar para uma pasta para instalar o aplicativo de exemplo. 

    ```bash
    cd "C:\git-samples"
    ```

2. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git
    ```

## <a name="review-the-code"></a>Examine o código

Esta etapa é opcional. Se você estiver interessado em aprender como os recursos de banco de dados são criados no código, poderá examinar os trechos de código a seguir. Os trechos de código são obtidos do arquivo `Program.java` instalado na pasta C:\git-samples\azure-cosmos-db-documentdb-java-getting-started\src\GetStarted. Caso contrário, você poderá pular para [Atualizar sua cadeia de conexão](#update-your-connection-string). 

* Inicialização de `DocumentClient`. O [DocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client) fornece a representação lógica do lado do cliente para o serviço de banco de dados do Azure Cosmos DB. Esse cliente é usado para configurar e executar solicitações no serviço.

    ```java
    this.client = new DocumentClient("https://FILLME.documents.azure.com",
            "FILLME", 
            new ConnectionPolicy(),
            ConsistencyLevel.Session);
    ```

* Criação do [Banco de Dados](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._database).

    ```java
    Database database = new Database();
    database.setId(databaseName);
    
    this.client.createDatabase(database, null);
    ```

* Criação de [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_collection).

    ```java
    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId(collectionName);

    ...

    this.client.createCollection(databaseLink, collectionInfo, requestOptions);
    ```

* Criação de documentos usando o método [createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createdocument).

    ```java
    // Any Java object within your code can be serialized into JSON and written to Azure Cosmos DB
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen");
    // More properties

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    this.client.createDocument(collectionLink, family, new RequestOptions(), true);
    ```

* Consultas SQL em JSON são executadas usando o método [queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.querydocuments).

    ```java
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setPageSize(-1);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    FeedResponse<Document> queryResults = this.client.queryDocuments(
        collectionLink,
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }
    ```    

## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

Agora, volte ao portal do Azure para obter informações sobre a cadeia de conexão e copiá-las para o aplicativo. Isso permite que seu aplicativo se comunique com o banco de dados hospedado.

1. No [Portal do Azure](http://portal.azure.com/), clique em **Chaves**. 

    Use os botões de cópia no lado direito da tela para copiar o valor superior, o URI.

    ![Exibir e copiar uma chave de acesso no Portal do Azure, página Chaves](./media/create-documentdb-java/keys.png)

2. Abra o arquivo `Program.java` da pasta C:\git-samples\azure-cosmos-db-documentdb-java-getting-started\src\GetStarted. 

3. Cole o valor do URI do portal em `https://FILLME.documents.azure.com` na linha 45.

4. Volte ao portal e copie o valor de CHAVE PRIMÁRIA conforme mostrado na captura de tela. Cole o valor de CHAVE PRIMÁRIA do portal em `FILLME` na linha 46.

    O método getStartedDemo agora deve ser semelhante a isto: 
    
    ```java
    private void getStartedDemo() throws DocumentClientException, IOException {
        this.client = new DocumentClient("https://youraccountname.documents.azure.com:443/",
                "your-primary-key...RJhQrqQ5QQ==", 
                new ConnectionPolicy(),
                ConsistencyLevel.Session);
    ```

5. Salve o arquivo Program.java.

## <a name="run-the-app"></a>Execute o aplicativo

1. Na janela do terminal git, `cd` para a pasta azure-cosmos-db-documentdb-java-getting-started.

    ```git
    cd "C:\git-samples\azure-cosmos-db-documentdb-java-getting-started"
    ```

2. Na janela do terminal git, digite `mvn package` para instalar os pacotes necessários do Java.

3. Na janela do terminal de git, execute `mvn exec:java -D exec.mainClass=GetStarted.Program` para iniciar o aplicativo Java.

    A janela de terminal exibe uma notificação de que o banco de dados FamilyDB foi criado. Pressione uma tecla para criar a coleção, em seguida, alterne para o Data Explorer e verá que agora ele contém um banco de dados FamilyDB.
    
    Continue a pressionar teclas para criar os documentos, em seguida, faça uma consulta.
    
    No final do programa, todos os recursos deste aplicativo são excluídos de sua conta para que você não seja cobrado. 

    ![Saída do console](./media/create-documentdb-java/console-output.png)


## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como criar uma conta do Azure Cosmos DB, uma coleção usando o Data Explorer e executar um aplicativo para fazer a mesma coisa programaticamente. Agora, é possível importar dados adicionais na coleção do Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importar dados no Azure Cosmos DB](import-data.md)


