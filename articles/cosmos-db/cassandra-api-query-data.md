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
ms.openlocfilehash: a06e7e6159953bfeffa966759d29b91bbcbafd37
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739202"
---
# <a name="tutorial-query-data-from-an-azure-cosmos-db-cassandra-api-account"></a>Tutorial: Consultar dados de uma conta da API do Cassandra do Azure Cosmos DB

Este tutorial mostra como consultar dados de usuário da conta de API do Cassandra do Azure Cosmos DB usando um aplicativo Java. O aplicativo Java usa o [driver Java](https://github.com/datastax/java-driver) e consulta dados do usuário, como ID do usuário, nome do usuário e cidade do usuário. 

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Consultar dados da tabela do Cassandra
> * Execute o aplicativo

## <a name="prerequisites"></a>Pré-requisitos

* Este artigo pertence a um tutorial com várias partes. Antes de começar, conclua as etapas anteriores para [criar a conta, o keyspace e a tabela de API do Cassandra e [carregar dados de exemplo na tabela](cassandra-api-load-data.md). 

## <a name="query-data"></a>Consultar dados

Use as etapas a seguir para consultar dados de sua conta de API do Cassandra:

1. Abra o arquivo `UserRepository.java` na pasta `src\main\java\com\azure\cosmosdb\cassandra`. Acrescente o seguinte bloco de código. Esse código fornece três métodos: 

   * Para consultar todos os usuários no banco de dados
   * Para consultar um usuário específico, filtrado por ID de usuário
   * Para excluir uma tabela.

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

2. Abra o arquivo `UserProfile.java` na pasta `src\main\java\com\azure\cosmosdb\cassandra`. Essa classe contém o método principal que chama createKeyspace e createTable. Insira os métodos de dados definidos anteriormente. Agora, acrescente o seguinte código que consulta todos os usuários ou um usuário específico:

   ```java
   LOGGER.info("Select all users");
   repository.selectAllUsers();

   LOGGER.info("Select a user by id (3)");
   repository.selectUser(3);

   LOGGER.info("Delete the users profile table");
   repository.deleteTable();
   ```

## <a name="run-the-java-app"></a>Executar o aplicativo Java
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


