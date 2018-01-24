---
title: 'Azure Cosmos DB: desenvolver com a API Cassandra no Java | Microsoft Docs'
description: Saiba como desenvolver com a API Cassandra do Azure Cosmos DB usando o Java
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
tags: 
ms.assetid: 6732d883-835c-481f-98e1-287893530948
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 11/15/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: 53987e5863d9fc11b4fa377295d198293819269c
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2017
---
# <a name="azure-cosmosdb-develop-with-the-cassandra-api-in-java"></a>Azure Cosmos DB: desenvolver com a API Cassandra no Java

O Azure Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. É possível criar e consultar rapidamente documentos, chave/valor e bancos de dados do grafo. Todos se beneficiam de recursos de escala horizontal e distribuição global no núcleo do Azure Cosmos DB. 

Este tutorial demonstra como criar uma conta do Azure Cosmos DB usando o Portal do Azure e depois criar uma Tabela do Cassandra (sql-api-partition-data.md#partition-keys) usando a [API Cassandra](cassandra-introduction.md). Ao definir uma chave primária quando você cria uma Tabela, seu aplicativo fica preparado para dimensionar com facilidade à medida que seus dados crescem. 

Este tutorial cobre as seguintes tarefas usando a API Cassandra:

> [!div class="checklist"]
> * Criar uma conta do Azure Cosmos DB
> * Criar um keyspace e uma tabela com uma chave primária
> * Inserir dados
> * Consultar dados
> * Examinar os SLAs

## <a name="prerequisites"></a>pré-requisitos

Acesso ao programa de versão prévia da API Cassandra do Azure Cosmos DB. Caso ainda não tenha solicitado o acesso, [inscreva-se agora](https://aka.ms/cosmosdb-cassandra-signup).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Como alternativa, você pode [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma assinatura do Azure, sem ônus e sem compromisso.

Além disso: 

* [Java Development Kit (JDK) 1.7 +](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.
    * Defina a variável de ambiente JAVA_HOME para apontar para a pasta onde o JDK está instalado.
* [Baixar](http://maven.apache.org/download.cgi) e [instalar](http://maven.apache.org/install.html) um armazenamento binário [Maven](http://maven.apache.org/)
    * No Ubuntu, você pode executar `apt-get install maven` para instalar o Maven.
* [Git](https://www.git-scm.com/)
    * No Ubuntu, você pode executar `sudo apt-get install git` para instalar o Git.

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

Antes de criar um banco de dados de documentos, você precisa criar uma conta Cassandra com o Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora, vamos trabalhar com o código. Agora vamos clonar um aplicativo do Cassandra do GitHub, definir a cadeia de conexão e executá-lo. Você verá como é fácil trabalhar usando dados de forma programática. 

1. Abra uma janela de terminal de git, como git bash, e use o comando `cd` para alterar para uma pasta para instalar o aplicativo de exemplo. 

    ```bash
    cd "C:\git-samples"
    ```

2. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started.git
    ```

## <a name="review-the-code"></a>Examine o código

Esta etapa é opcional. Se você estiver interessado em aprender como os recursos de banco de dados são criados no código, poderá examinar os trechos de código a seguir. Caso contrário, você poderá pular para [Atualizar sua cadeia de conexão](#update-your-connection-string). Esses trechos de código são todos obtidos do src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java.  

* Host, porta, nome de usuário, senha e opções de SSL do Cassandra estão definidos. A informação da cadeia de conexão vem da página Cadeia de Conexão no Portal do Azure.

   ```java
   cluster = Cluster.builder().addContactPoint(cassandraHost).withPort(cassandraPort).withCredentials(cassandraUsername, cassandraPassword).withSSL(sslOptions).build();
   ```

* O `cluster` se conecta à API Cassandra do Azure Cosmos DB e retorna uma sessão para acessar.

    ```java
    return cluster.connect();
    ```

Os trechos de código a seguir são do arquivo src/main/java/com/azure/cosmosdb/cassandra/repository/UserRepository.java.

* Crie um novo keyspace.

    ```java
    public void createKeyspace() {
        final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {'class': 'SimpleStrategy', 'replication_factor': '3' } ";
        session.execute(query);
        LOGGER.info("Created keyspace 'uprofile'");
    }
    ```

* Crie uma nova tabela.

   ```java
   public void createTable() {
        final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        session.execute(query);
        LOGGER.info("Created table 'user'");
   }
   ```

* Insira entidades de usuário usando um objeto de instrução preparado.

    ```java
    public PreparedStatement prepareInsertStatement() {
        final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
        return session.prepare(insertStatement);
    }

    public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
    }
    ```

* Consulte para obter todas as informações de usuário.

    ```java
   public void selectAllUsers() {
        final String query = "SELECT * FROM uprofile.user";
        List<Row> rows = session.execute(query).all();

        for (Row row : rows) {
            LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
        }
    }
    ```

* Consulte para obter uma informação única de usuário.

    ```java
    public void selectUser(int id) {
        final String query = "SELECT * FROM uprofile.user where user_id = 3";
        Row row = session.execute(query).one();

        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
    }
    ```

## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

Agora, volte ao portal do Azure para obter informações sobre a cadeia de conexão e copiá-las para o aplicativo. Isso permite que seu aplicativo se comunique com o banco de dados hospedado.

1. No [Portal do Azure](http://portal.azure.com/), clique em **Cadeia de Conexão**. 

    ![Exibir e copiar um nome de usuário do Portal do Azure, página Cadeia de Conexão](./media/tutorial-develop-cassandra-java/keys.png)

2. Use o ![botão Copiar](./media/tutorial-develop-cassandra-java/copy.png) no lado direito da tela para copiar o valor do PONTO DE CONTATO.

3. Abra o arquivo `config.properties` da pasta C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples\src\main\resources. 

3. Cole o valor do PONTO DE CONTATO do portal sobre `<Cassandra endpoint host>` na linha 2.

    A linha 2 do config.properties agora deve ser semelhante a 

    `cassandra_host=cosmos-db-quickstarts.documents.azure.com`

3. Volte ao portal e copie o valor do NOME DE USUÁRIO. Cole o valor do NOME DE USUÁRIO do portal sobre `<cassandra endpoint username>` na linha 4.

    A linha 4 do config.properties agora deve ser semelhante a 

    `cassandra_username=cosmos-db-quickstart`

4. Volte ao portal e copie o valor da SENHA. Cole o valor da SENHA do portal sobre `<cassandra endpoint password>` na linha 5.

    A linha 5 do config.properties agora deve ser semelhante a 

    `cassandra_password=2Ggkr662ifxz2Mg...==`

5. Na linha 6, se você quiser usar um certificado SSL específico, substitua `<SSL key store file location>` pelo local do certificado SSL. Se um valor não for fornecido, será usado o certificado JDK instalado em <JAVA_HOME>/jre/lib/security/cacerts. 

6. Se você alterou a linha 6 para usar um certificado SSL específico, atualize a linha 7 para usar a senha desse certificado. 

7. Salve o arquivo config.properties.

## <a name="run-the-app"></a>Execute o aplicativo

1. Na janela do terminal git, `cd` para a pasta azure-cosmosdb-cassandra-java-getting-started\java-examples.

    ```git
    cd "C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples"
    ```

2. Na janela do terminal git, use o seguinte comando para gerar o arquivo cosmosdb-cassandra-examples.jar.

    ```git
    mvn clean install
    ```

3. Na janela do terminal git, execute os comandos a seguir para iniciar o aplicativo Java.

    ```git
    java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
    ```

    A janela do terminal exibe notificações de que o keyspace e a tabela foram criados. Em seguida, todos os usuários na tabela são selecionados e retornados, e é exibida a saída. Depois, é selecionada uma linha por id, e o valor é exibido.  
    
    Agora, é possível voltar ao Data Explorer e ver a consulta, modificar e trabalhar com esses novos dados. 

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu como fazer o seguinte:

> [!div class="checklist"]
> * Criar uma conta do Azure Cosmos DB
> * Criar um keyspace e uma tabela com uma chave primária
> * Inserir dados
> * Consultar dados
> * Examinar SLAs

Agora, é possível importar dados adicionais na coleção do Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importar dados do Cassandra para o Azure Cosmos DB](cassandra-import-data.md)
