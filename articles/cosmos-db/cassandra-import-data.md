---
title: Migrar seus dados para a conta da API do Cassandra do Azure Cosmos DB
description: Aprenda a usar o comando CQL Copy e o Spark para copiar dados do Apache Cassandra para a API do Azure Cosmos DB Cassandra.
services: cosmos-db
author: kanshiG
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.author: govindk
ms.topic: tutorial
ms.date: 09/24/2018
ms.reviewer: sngun
ms.openlocfilehash: f73a201a25bb2f975e8a261a6c21aa7b066c3a7c
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48247843"
---
# <a name="migrate-your-data-to-azure-cosmos-db-cassandra-api-account"></a>Migrar seus dados para a conta da API do Cassandra do Azure Cosmos DB

Este tutorial fornece instruções sobre como migrar dados do Apache Cassandra para a API do Cassandra do Azure Cosmos DB. 

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Planejar a migração
> * Pré-requisitos para migração
> * Migrar dados usando o comando cqlsh COPY
> * Migrar dados usando Spark 

## <a name="plan-for-migration"></a>Planejar a migração

Antes de migrar dados para a API do Azure Cosmos DB Cassandra, você deve estimar as necessidades de taxa de transferência de sua carga de trabalho. Em geral, é recomendado começar com a taxa de transferência média exigida pelas operações CRUD e, em seguida, incluir a taxa de transferência adicional necessária para o Extract Transform Load (ETL) ou operações pontiagudas. Você precisa dos seguintes detalhes para planejar a migração: 

* **Tamanho de dados existente ou tamanho de dados estimado:** Define o tamanho mínimo do banco de dados e o requisito de taxa de transferência. Se você estiver estimando o tamanho dos dados para um novo aplicativo, poderá assumir que os dados são distribuídos uniformemente pelas linhas e estimar o valor multiplicando-os pelo tamanho dos dados. 

* **Taxa de transferência necessária:** Taxa aproximada de leitura (consulta / obtenção) e gravação (atualização / exclusão / inserção). Esse valor é necessário para calcular as unidades de solicitação necessárias junto com o tamanho dos dados de estado estável.  

* **Obtenha o esquema:** Conecte-se ao cluster do Cassandra existente por meio do cqlsh e do esquema de exportação do Cassandra: 

  ```bash
  cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
  ```

Depois de identificar os requisitos da sua carga de trabalho existente, você deve criar uma conta, banco de dados e contêineres do Azure Cosmos DB de acordo com os requisitos de taxa de transferência reunidos.  

* **Determine a cobrança do RU para uma operação:** Você pode determinar as RUs usando o SDK da API do Azure Cosmos DB Cassandra de sua escolha. Este exemplo mostra a versão do .NET da obtenção de custos de RU.

  ```csharp
  var tableInsertStatement = table.Insert(sampleEntity);
  var insertResult = await tableInsertStatement.ExecuteAsync();

  foreach (string key in insertResult.Info.IncomingPayload)
    {
       byte[] valueInBytes = customPayload[key];
       string value = Encoding.UTF8.GetString(valueInBytes);
       Console.WriteLine($"CustomPayload:  {key}: {value}");
    }
  ```

* **Aloque a taxa de transferência necessária:** o Azure Cosmos DB pode dimensionar automaticamente o armazenamento e a taxa de transferência conforme seus requisitos aumentam. Você pode estimar suas necessidades de taxa de transferência usando a [calculadora da unidade de solicitação do Azure Cosmos DB](https://www.documentdb.com/capacityplanner). 

## <a name="prerequisites-for-migration"></a>Pré-requisitos para migração

* **Crie tabelas na conta da API do Azure Cosmos DB Cassandra:** Antes de iniciar a migração de dados, crie previamente todas as suas tabelas no portal do Azure ou no cqlsh. Se você estiver migrando para uma conta do Azure Cosmos DB com produtividade de nível de banco de dados, não deixe de fornecer uma chave de partição ao criar os contêineres do Azure Cosmos DB.

* **Aumentar a taxa de transferência:** a duração da migração de dados depende da quantidade de taxa de transferência provisionada para as tabelas no Azure Cosmos DB. Aumente o rendimento durante a migração. Com a taxa de transferência mais elevada, você pode evitar a limitação de taxa e migrar em menos tempo. Depois de concluir a migração, diminua a taxa de transferência para economizar custos. Para obter mais informações sobre como aumentar o rendimento, consulte [definir taxa de transferência](set-throughput.md) para os contêineres de banco de dados do Azure Cosmos. Também é recomendável ter uma conta do Azure Cosmos DB na mesma região que seu banco de dados de origem. 

* **Ativar SSL:** o Azure Cosmos DB tem requisitos e padrões de segurança rígidos. Certifique-se de habilitar o SSL ao interagir com sua conta. Quando você usar a CQL com SSH, terá a opção de fornecer informações de SSL.

## <a name="options-to-migrate-data"></a>Opções para migrar dados

Você pode mover dados de cargas de trabalho existentes do Cassandra para o Azure Cosmos DB usando as seguintes opções:

* [Usando o comando cqlsh COPY](#using-cqlsh-copy-command)  
* [Usando o Spark](#using-spark) 

## <a name="migrate-data-using-cqlsh-copy-command"></a>Migrar dados usando o comando cqlsh COPY

[O comando CQL COPY](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) é usado para copiar dados locais para a conta da API do Azure Cosmos DB Cassandra. Use as etapas a seguir para copiar dados:

1. Receba as informações da string de conexão da sua conta da Cassandra:

   * Faça login no [Portal do Azure](https://portal.azure.com) e navegue até sua conta do Azure Cosmos DB.

   * Abra o painel **Connection String** que contém todas as informações necessárias para se conectar à sua conta da Cassandra API a partir do cqlsh.

2. Entre no cqlsh usando as informações de conexão do portal.

3. Use o comando CQL COPY para copiar dados locais para a conta da API do Cassandra.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-using-spark"></a>Migrar dados usando Spark 

Use as etapas a seguir para migrar dados para a API do Cassandra do Azure Cosmos DB com o Spark:

- Provisione um [Banco de Dados do Azure](cassandra-spark-databricks.md) ou um cluster do [HDInsight](cassandra-spark-hdinsight.md) 

- Mover dados para o terminal da API do Cassandra de destino usando [operação de cópia de tabela](cassandra-spark-table-copy-ops.md) 

A migração de dados usando trabalhos de ignição é uma opção recomendada se você tiver dados residindo em um cluster existente nas máquinas virtuais do Azure ou em qualquer outra nuvem. Isso requer que a faísca seja configurada como intermediária por um período ou ingestão regular. Você pode acelerar essa migração usando a conectividade de rota expressa entre o local e o Azure. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como migrar seus dados para a conta da API do Azure Cosmos DB Cassandra. Agora você pode seguir para a seção Conceitos para saber mais sobre o Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Níveis ajustáveis de consistência de dados no Azure Cosmos DB](../cosmos-db/consistency-levels.md)


