---
title: Consultar dados de uma conta da API do Cassandra do Azure Cosmos DB
description: Este artigo mostra como carregar dados de usuário de consulta de uma conta de API do Cassandra do Azure Cosmos DB usando um aplicativo Java.
services: cosmos-db
ms.service: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.component: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: c1fb4c27f897e3c0952ed6419e167613ac8204f7
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223484"
---
# <a name="query-data-from-an-azure-cosmos-db-cassandra-api-account"></a>Consultar dados de uma conta da API do Cassandra do Azure Cosmos DB

Este tutorial mostra como consultar dados de usuário da conta de API do Cassandra do Azure Cosmos DB usando um aplicativo Java. O aplicativo Java usa o [driver Java](https://github.com/datastax/java-driver) e consulta dados do usuário, como ID do usuário, nome do usuário e cidade do usuário. 

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Consultar dados da tabela do Cassandra
> * Execute o aplicativo

## <a name="prerequisites"></a>Pré-requisitos

* Este artigo pertence a um tutorial com várias partes. Antes de começar, certifique-se de concluir as etapas anteriores para [criar a conta, o keyspace e a tabela de API do Cassandra](create-cassandra-api-account-java.md) e [carregar dados de exemplo na tabela](cassandra-api-load-data.md). 

## <a name="query-data"></a>Consultar dados

Abra o arquivo `UserRepository.java` na pasta `src\main\java\com\azure\cosmosdb\cassandra`. Acrescente o seguinte bloco de código. Esse código fornece três funções: consultar todos os usuários no banco de dados, consultar um usuário específico filtrado por ID de usuário e excluir uma tabela. 

```java
/**
* Select all rows from user table
*/
public void selectAllUsers() {

    final String query = "SELECT * FROM uprofile.user";
    List<Row> rows = session.execute(query).all();

    for (Row row : rows) {
       LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
    }
}

/**
* Select a row from user table
*
* @param id user_id
*/
public void selectUser(int id) {
    final String query = "SELECT * FROM uprofile.user where user_id = 3";
    Row row = session.execute(query).one();

    LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
}

/**
* Delete user table.
*/
public void deleteTable() {
   final String query = "DROP TABLE IF EXISTS uprofile.user";
   session.execute(query);
}
```

Abra o arquivo `UserProfile.java` na pasta `src\main\java\com\azure\cosmosdb\cassandra`. Essa classe contém o método principal que chama createKeyspace e createTable. Insira os métodos de dados definidos anteriormente. Agora, acrescente o seguinte código que consulta todos os usuários ou um usuário específico:

```java
LOGGER.info("Select all users");
repository.selectAllUsers();

LOGGER.info("Select a user by id (3)");
repository.selectUser(3);

LOGGER.info("Delete the users profile table");
repository.deleteTable();
```

## <a name="run-the-java-app"></a>Execute o aplicativo Java
1. Abra um prompt de comando ou uma janela de terminal. Cole o seguinte bloco de código. 

   Esse código altera o diretório (cd) para o caminho da pasta onde você criou o projeto. Em seguida, ele executa o comando `mvn clean install` para gerar o arquivo `cosmosdb-cassandra-examples.jar` dentro da pasta de destino. Por fim, ele executa o aplicativo Java.

   ```bash
   cd "cassandra-demo"
   
   mvn clean install
   
   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
   ```

2. Agora, no portal do Azure, abra o **Data Explorer** e confirme se a tabela de usuário foi excluída.

## <a name="next-steps"></a>Próximas etapas

* Neste tutorial, você aprendeu como carregar dados da conta de API do Cassandra do Azure Cosmos DB. Prossiga agora para o próximo artigo:

> [!div class="nextstepaction"]
> [Migrar dados para a conta de API do Cassandra](cassandra-import-data.md)


