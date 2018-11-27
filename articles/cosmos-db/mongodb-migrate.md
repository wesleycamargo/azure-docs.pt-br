---
title: Usar mongoimport e o mongorestore com a API para MongoDB do Azure Cosmos DB | Microsoft Docs
description: Saiba como usar mongoimport e mongorestore para importar dados para uma conta da API para MongoDB
keywords: mongoimport, mongorestore
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 13422434e6392ec7681ec4478533c45a84f40c9a
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706969"
---
# <a name="tutorial-migrate-your-data-to-azure-cosmos-db-mongodb-api-account"></a>Tutorial: Migrar seus dados para a conta de API de MongoDB do Azure Cosmos DB

Este tutorial fornece instruções sobre como migrar os dados armazenados no MongoDB para a conta de API do MongoDB do Azure Cosmos DB. Se você estiver importando dados do MongoDB e se pretende usá-los com o API do SQL do Azure Cosmos DB, deverá usar a [Ferramenta de Migração de Dados](import-data.md) para importar os dados.

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Planejar a migração
> * Pré-requisitos para migração
> * Migrar dados usando o mongoimport
> * Migrar dados usando o mongorestore

Antes de migrar dados para a conta de API do MongoDB, verifique se você tem algum exemplo de dados do MongoDB. Se não tiver um banco de dados do MongoDB de exemplo, você poderá baixar e instalar o [servidor de comunidade do MongoDB](https://www.mongodb.com/download-center), criar um banco de dados de exemplo e usar mongoimport.exe ou mongorestore.exe para carregar dados de exemplo. 

## <a name="plan-for-migration"></a>Planejar a migração

1. Crie previamente e dimensione suas coleções:
        
   * Por padrão, o Azure Cosmos DB prevê uma nova coleção MongoDB com 1.000 unidades de solicitação por segundo (RU/s). Antes de iniciar a migração utilizando mongoimport ou mongorestore, crie previamente todas as coleções a partir do [portal do Azure](https://portal.azure.com) ou de ferramentas e drivers do MongoDB. Se o tamanho dos dados for superior a 10 GB, certifique-se de criar uma [coleção particionada](partition-data.md) com uma chave de fragmentos apropriada. O MongoDB recomenda o armazenamento de dados de entidade em coleções. Você pode colocalizar entidades com tamanho e taxa de transferência de provisionamento comparáveis no nível do banco de dados Cosmos do Azure.

   * No [portal do Azure](https://portal.azure.com), aumente a taxa de transferência das coleções de 1000 RUs/s para uma coleção de partição única, e 2.500 RUs/s para uma coleção fragmentada, apenas durante a migração. Com a taxa de transferência mais elevada, você pode evitar a limitação de taxa e migrar em menos tempo. Para economizar custos, é possível reduzir a taxa de transferência imediatamente após a migração.

   * Além do provisionamento RUs/s no nível da coleção, você também pode provisionar RU/s para um conjunto de coleções no nível do banco de dados pai. Isso exige criar previamente o banco de dados e as coleções, além da definição de uma chave de fragmentação para cada coleção.

   * Você pode criar coleções fragmentadas por meio de sua ferramenta, driver ou SDK favorito. Neste exemplo, usamos o Shell do Mongo para criar uma coleção fragmentada:

        ```bash
        db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
        ```
    
        Resultados:

        ```JSON
        {
            "_t" : "ShardCollectionResponse",
            "ok" : 1,
            "collectionsharded" : "admin.people"
        }
        ```

1. Calcule a taxa de RU aproximada para gravação de um documento único:

    a. Conecte-se à sua conta da API do MongoDB do Azure Cosmos DB a partir do MongoDB Shell. É possível localizar as instruções em [Conectar um aplicativo do MongoDB ao Azure Cosmos DB](connect-mongodb-account.md).
    
   b. Execute um comando de inserção de exemplo utilizando um dos documentos de exemplo do MongoDB Shell:
   
      ```bash
      db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })
      ```
        
   c. Execute ```db.runCommand({getLastRequestStatistics: 1})``` e uma resposta semelhante à seguinte será exibida:
     
      ```bash
        globaldb:PRIMARY> db.runCommand({getLastRequestStatistics: 1})
        {
            "_t": "GetRequestStatisticsResponse",
            "ok": 1,
            "CommandName": "insert",
            "RequestCharge": 10,
            "RequestDurationInMilliSeconds": NumberLong(50)
        }
      ```
        
    d. Tome nota da cobrança da solicitação.
    
1. Determine a latência d computador para o serviço de nuvem do Azure Cosmos DB:
    
    a. Habilite o log detalhado do MongoDB Shell utilizando esse comando: ```setVerboseShell(true)```
    
    b. Execute uma consulta simples no banco de dados: ```db.coll.find().limit(1)```. Uma resposta semelhante à seguinte será exibida:

       ```bash
       Fetched 1 record(s) in 100(ms)
       ```
        
1. Remova o documento inserido antes da migração para garantir que não haja documentos duplicados. É possível remover documentos utilizando esse comando: ```db.coll.remove({})```

1. Calcule os valores aproximados de *batchSize* e *numInsertionWorkers*:

    * Para *batchSize*, divida as RUs provisionadas totais pelas RUs consumidas a partir do seu documento único, gravado na etapa 3.
    
    * Se o *batchSize* calculado for <= 24, utilize esse número como seu valor de *batchSize*.
    
    * Se o *batchSize* calculado for > 24, defina o valor de *batchSize* para 24.
    
    * Para *numInsertionWorkers*, use esta equação: *numInsertionWorkers =  (taxa de transferência provisionada * latência em segundos) / (tamanho do lote * RUs consumidas para uma gravação única)*.
        
    |Propriedade|Valor|
    |--------|-----|
    |batchSize| 24 |
    |RUs provisionadas | 10000 |
    |Latency | 0,100 s |
    |RU cobrada por 1 doc gravado | 10 RUs |
    |numInsertionWorkers | ? |
    
    *numInsertionWorkers = (10000 RUs x 0,1 s) / (24 x 10 RUs) = 4.1666*

1. Execute o comando de migração. As opções para migrar os dados são descritas nas próximas seções.

   ```bash
   mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
   ```
   Ou com mongorestore (verifique se todas as coleções tem a taxa de transferência definida de forma igual ou superior ao número de RUs usados em cálculos anteriores):
   
   ```bash
   mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07 --numInsertionWorkersPerCollection 4 --batchSize 24
   ```

## <a name="prerequisites-for-migration"></a>Pré-requisitos para migração

* **Aumentar a taxa de transferência:** a duração da sua migração de dados depende da taxa de transferência que você configurar para uma coleção individual ou um conjunto de coleções. Certifique-se de aumentar a taxa de transferência para migrações de dados maiores. Depois de concluir a migração, diminua a taxa de transferência para economizar custos. Para obter mais informações sobre como aumentar a produtividade no [portal do Azure](https://portal.azure.com), consulte [Níveis de desempenho e tipos de preço no Azure Cosmos DB](performance-levels.md).

* **Ativar SSL:** o Azure Cosmos DB tem requisitos e padrões de segurança rígidos. Certifique-se de habilitar o SSL ao interagir com sua conta. Os procedimentos no restante do artigo incluem como habilitar o SSL para mongoimport e mongorestore.

* **Criar recursos do Azure Cosmos DB:** antes de iniciar a migração de dados, crie previamente todas as suas coleções do portal do Azure. Se você estiver migrando para uma conta do Azure Cosmos DB com produtividade de nível de banco de dados, não deixe de fornecer uma chave de partição ao criar os coleções do Azure Cosmos DB.

## <a name="get-your-connection-string"></a>Obter sua cadeia de conexão 

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, clique na entrada **Azure Cosmos DB**.
1. No painel **Assinaturas**, selecione o nome da sua conta.
1. No folha **Cadeia de Conexão**, clique em **Cadeia de Conexão**.

   O painel direito contém todas as informações necessárias para conectar-se à sua conta com êxito.

   ![Folha Cadeia de conexão](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="migrate-data-by-using-mongoimport"></a>Migrar dados usando o mongoimport

Para importar dados para sua conta Azure Cosmos DB, utilize o seguinte modelo. Preencha *host*, *nome de usuário* e *senha* com os valores que são específicos para a sua conta.  

Modelo:

```bash
mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file "C:\sample.json"
```

Exemplo:  

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file "C:\Users\admin\Desktop\*.json"
```

## <a name="migrate-data-by-using-mongorestore"></a>Migrar dados usando o mongorestore

Para restaurar dados para sua conta da API para MongoDB, use o modelo a seguir para executar a importação. Preencha *host*, *nome de usuário* e *senha* com os valores que são específicos para a sua conta.

Modelo:

```bash
mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>
```

Exemplo:

```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
```

## <a name="next-steps"></a>Próximas etapas

Agora, você pode seguir para o próximo tutorial e saber como consultar dados do MongoDB usando o Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Como consultar dados do MongoDB?](../cosmos-db/tutorial-query-mongodb.md)
