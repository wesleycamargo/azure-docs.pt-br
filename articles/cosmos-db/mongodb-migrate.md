---
title: Migrar os dados do MongoDB para o Azure Cosmos DB usando o mongoimport e o mongorestore
description: Você aprenderá como usar o mongoimport e o mongorestore para importar dados para o Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
Customer intent: As a developer, I want to migrate the data from my existing MongoDB to Cosmos DB.
ms.openlocfilehash: 9226a49af67659ebd7bebd9beca397830ee808bb
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54039037"
---
# <a name="migrate-your-mongodb-data-to-azure-cosmos-db"></a>Migrar os dados do MongoDB para o Azure Cosmos DB

 Este tutorial fornece instruções sobre como migrar os dados armazenados no MongoDB para o Azure Cosmos DB configurado para usar a API para MongoDB do Azure Cosmos DB. Se você importar dados do MongoDB e se pretende usá-los com a API SQL do Azure Cosmos DB, deverá usar a [Ferramenta de Migração de Dados](import-data.md) para importar os dados.

Neste tutorial, você irá:

> [!div class="checklist"]
> * Preparar um plano de migração.
> * Migrar dados usando o mongoimport.
> * Migrar dados usando o mongorestore.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Analise e conclua os pré-requisitos a seguir antes de iniciar a migração.

### <a name="plan-for-the-migration"></a>Planejar a migração

Esta seção descreve como planejar a migração de dados. Estimaremos os encargos de RU, determinaremos a latência do seu computador para o serviço de nuvem e calcularemos o tamanho do lote e o número de trabalhadores de inserção.


#### <a name="pre-create-and-scale-your-collections"></a>Criar previamente e dimensionar suas coleções

Antes de migrar com o mongoimport ou o mongorestore, crie previamente todas as coleções por meio do [portal do Azure](https://portal.azure.com) ou de ferramentas e drivers do MongoDB. 

Do [portal do Azure](https://portal.azure.com), aumente a taxa de transferência de coleções para a migração. Com uma taxa de transferência mais elevada, você pode evitar sofrer limitação de taxa e migrar em menos tempo. Para economizar custos, é possível reduzir a taxa de transferência imediatamente após a migração.

Além de taxa de transferência de provisionamento em um nível de coleção, você também pode provisionar a taxa de transferência no nível de banco de dados para um conjunto de coleções para compartilhar a taxa de transferência provisionada. Você precisa criar previamente o banco de dados e as coleções e definir uma chave de fragmentação para cada coleção no banco de dados de taxa de transferência compartilhada.

Você pode criar coleções fragmentadas por meio de sua ferramenta, driver ou SDK favorito. Neste exemplo, usamos o Shell do Mongo para criar uma coleção fragmentada:

```bash
db.runCommand( { shardCollection: "admin.people", key: { region: "hashed" } } )
```
    
O comando retorna os seguintes resultados:

```JSON
{
    "_t" : "ShardCollectionResponse",
    "ok" : 1,
    "collectionsharded" : "admin.people"
}
```

#### <a name="calculate-the-approximate-ru-charge-for-a-single-document-write"></a>Calcular a taxa de RU aproximada para gravação de um documento único

Do MongoDB Shell, conecte-se à sua conta do Cosmos configurada para usar a API para MongoDB do Azure Cosmos DB. É possível localizar as instruções em [Conectar um aplicativo do MongoDB ao Cosmos DB](connect-mongodb-account.md).

Em seguida, execute um comando de inserção de exemplo utilizando um dos documentos de exemplo:
   
```bash
db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })
```
        
Execute o comando `db.runCommand({getLastRequestStatistics: 1})`.

Uma resposta semelhante à seguinte saída será exibida:
     
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
        
Tome nota da cobrança da solicitação.
    
#### <a name="determine-the-latency-from-your-machine-to-cosmos-db"></a>Determinar a latência do computador para o Cosmos DB
    
Habilite o log detalhado do MongoDB Shell com o comando `setVerboseShell(true)`.
    
Execute uma consulta básica no banco de dados com o comando `db.coll.find().limit(1)`.

Uma resposta semelhante à seguinte saída será exibida:

```bash
Fetched 1 record(s) in 100(ms)
```
        
Antes de executar a migração, remova o documento inserido para garantir que não haja documentos duplicados. É possível remover documentos com o comando `db.coll.remove({})`.

#### <a name="calculate-the-approximate-values-for-the-batchsize-and-numinsertionworkers-properties"></a>Calcular os valores aproximados das propriedades batchSize e numInsertionWorkers

Para a propriedade **batchSize**, divida a taxa de transferência provisionada total (RUs/s) pelas RUs consumidas para uma única gravação de documento, conforme concluído na seção "Determinar a latência do seu computador para o Cosmos DB". Se o valor calculado for menor ou igual a 24, use esse número como o valor da propriedade. Se o valor calculado for maior que 24, defina o valor da propriedade para 24.
    
Para obter o valor da propriedade **numInsertionWorkers**, use esta equação:

`numInsertionWorkers = (Provisioned RUs throughput * Latency in seconds) / (batchSize * Consumed RUs for a single write)`

Podemos usar os seguintes valores para calcular um valor para a propriedade **numInsertionWorkers**:

| Propriedade | Valor |
|--------|-----|
| **batchSize** | 24 |
| RUs provisionadas | 10.000 |
| Latency | 0,100 s |
| RUs consumidas | 10 RUs |
| **numInsertionWorkers** | (10.000 RUs x 0,100 s) / (24 x 10 RUs) = **4,1666** |

Execute o comando de migração **monogoimport**. Os parâmetros de comando são descritos posteriormente neste artigo.

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
```

Você também pode usar o comando **monogorestore**. Verifique se todas as coleções têm a taxa de transferência definida com valor igual ou superior ao número de RUs usados em cálculos anteriores.
   
```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07 --numInsertionWorkersPerCollection 4 --batchSize 24
```

### <a name="complete-the-prerequisites"></a>Complete os pré-requisitos

Depois de planejar a migração, conclua as etapas a seguir: 

* **Obter dados de exemplo**: Verifique se que você tem alguns dados de exemplo antes de iniciar a migração. 

* **Aumentar a taxa de transferência**: A duração da sua migração de dados depende da taxa de transferência que você provisione para uma coleção individual ou um banco de dados. Certifique-se de aumentar a taxa de transferência para migrações de dados maiores. Depois de concluir a migração, diminua a taxa de transferência para economizar custos. 

* **Habilitar SSL**:  O Cosmos DB tem padrões e requisitos de segurança rígidos. Não deixe de habilitar o SSL ao interagir com sua conta do Cosmos. Os procedimentos neste artigo incluem como habilitar o SSL para os comandos do mongoimport e do mongorestore.

* **Criar recursos do Cosmos DB**: Antes de começar a migração, crie previamente todas as suas coleções no portal do Azure. Se você migrar para uma conta do Cosmos que tenha produtividade provisionada de nível de banco de dados, não deixe de fornecer uma chave de partição ao criar as coleções.

* **Obter sua cadeia de conexão**: No [portal do Azure](https://portal.azure.com), selecione a entrada **Azure Cosmos DB** na esquerda. Em **Assinaturas**, selecione o nome da conta. Em **Cadeia de Conexão**, selecione **Cadeia de Conexão**. O lado direito do portal mostra as informações de que você precisa para conectar-se à conta:

    ![Informações da Cadeia de Conexão](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="use-mongoimport"></a>Usar o mongoimport

Para importar dados para sua conta do Cosmos, use o modelo a seguir.

```bash
mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file "C:\sample.json"
```

Substitua os parâmetros \<nome_do_seu_host>, \<seu_nome_de usuário> e \<sua_senha> pelos valores específicos para a conta. No exemplo a seguir, usamos **sampleDB** como o valor para \<seu_banco_de_dados> e **sampleColl** como o valor para \<sua_coleção>:

```bash
mongoimport.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file "C:\Users\admin\Desktop\*.json"
```

## <a name="use-mongorestore"></a>Usar o mongorestore

Para restaurar dados para sua conta do Cosmos configurada com a API para MongoDB do Cosmos DB, use o modelo a seguir para executar a importação.

```bash
mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>
```

Substitua os parâmetros \<nome_do_seu_host>, \<seu_nome_de usuário> e \<sua_senha> pelos valores específicos para a conta. No exemplo a seguir, usamos **./dumps/dump-2016-12-07** como o valor para \<caminho_para_o_backup>:

```bash
mongorestore.exe --host cosmosdb-mongodb-account.documents.azure.com:10255 -u cosmosdb-mongodb-account -p <Your_MongoDB_password> --db mydatabase --collection mycollection --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando esses recursos já não forem necessários, você poderá excluir o grupo de recursos, a conta do Cosmos e todos os recursos relacionados. Use as etapas a seguir para excluir o grupo de recursos:

1. Vá para o grupo de recursos em que você criou a conta do Cosmos.
1. Selecione **Excluir grupo de recursos**.
1. Confirme o nome do grupo de recursos a excluir e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Siga para o próximo tutorial e aprenda a consultar dados usando a API para MongoDB do Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Como consultar dados do MongoDB](../cosmos-db/tutorial-query-mongodb.md)
