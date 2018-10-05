---
title: Carregar dados de exemplo em uma tabela de API do Cassandra do Azure Cosmos DB usando um aplicativo Java | Microsoft Docs
description: Este artigo mostra como carregar dados de usuário de exemplo em uma tabela na conta de API do Cassandra do Azure Cosmos DB usando um aplicativo java.
services: cosmos-db
author: kanshiG
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 662d4b8812ca4b92c1130b9c2c38771e7ec30a06
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393973"
---
# <a name="load-sample-data-into-an-azure-cosmos-db-cassandra-api-table"></a>Carregar dados de exemplo em uma tabela de API do Cassandra do Azure Cosmos DB

Este tutorial mostra como carregar dados de usuário de exemplo em uma tabela na conta da API do Cassandra do Azure Cosmos DB usando um aplicativo Java. O aplicativo Java usa o [driver Java](https://github.com/datastax/java-driver) e carrega dados do usuário, como ID, nome e cidade do usuário. 

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Carregar dados na tabela do Cassandra
> * Execute o aplicativo

## <a name="prerequisites"></a>Pré-requisitos

* Este artigo pertence a um tutorial com várias partes. Antes de começar com este documento, certifique-se [criar a conta de API do Cassandra, o keyspace e a tabela](create-cassandra-api-account-java.md).   

## <a name="load-data-into-the-table"></a>Carregar dados na tabela

Abra o arquivo "UserRepository.java" na pasta "src\main\java\com\azure\cosmosdb\cassandra" e acrescente o código para inserir os campos user_id, user_name e user_bcity na tabela:

```java
/**
* Insert a row into user table
*
* @param id   user_id
* @param name user_name
* @param city user_bcity
*/
public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
}

/**
* Create a PrepareStatement to insert a row to user table
*
* @return PreparedStatement
*/
public PreparedStatement prepareInsertStatement() {
    final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
    return session.prepare(insertStatement);
}
```
 
Abra o arquivo "UserProfile.java" na pasta "src\main\java\com\azure\cosmosdb\cassandra". Essa classe contém o método principal que chama os métodos createKeyspace e createTable definidos anteriormente. Agora, acrescente o seguinte código para inserir alguns dados de exemplo na tabela de API do Cassandra.

```java
//Insert rows into user table
PreparedStatement preparedStatement = repository.prepareInsertStatement();
    repository.insertUser(preparedStatement, 1, "JohnH", "Seattle");
    repository.insertUser(preparedStatement, 2, "EricK", "Spokane");
    repository.insertUser(preparedStatement, 3, "MatthewP", "Tacoma");
    repository.insertUser(preparedStatement, 4, "DavidA", "Renton");
    repository.insertUser(preparedStatement, 5, "PeterS", "Everett");
```

## <a name="run-the-app"></a>Execute o aplicativo

Abra o prompt de comando ou a janela do terminal e altere o caminho para a pasta onde você criou o projeto. Execute o comando de "mvn clean install" para gerar o arquivo cosmosdb-cassandra-Examples.jar dentro da pasta de destino e executar o aplicativo. 

```bash
cd "cassandra-demo"

mvn clean install

java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
```

Agora você pode abrir o Data Explorer no portal do Azure para confirmar se as informações do usuário foram adicionadas à tabela.
    
## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como carregar dados de exemplo para a conta de API do Cassandra do Azure Cosmos DB. Prossiga agora para o próximo artigo:

> [!div class="nextstepaction"]
> [Consultar dados da conta de API do Cassandra](cassandra-api-query-data.md)
