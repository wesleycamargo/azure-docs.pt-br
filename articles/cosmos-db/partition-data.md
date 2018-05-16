---
title: Particionamento e escala horizontal no BD Cosmos do Azure | Microsoft Docs
description: Saiba mais sobre como o particionamento funciona no DB Cosmos do Azure, como configurar o particionamento e as chaves de partição e como escolher a chave de partição correta para seu aplicativo.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: cac9a8cd-b5a3-4827-8505-d40bb61b2416
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: rimman
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1976ab5ab0bd0037163b2ad8048fcee10b204ea2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="partition-and-scale-in-azure-cosmos-db"></a>Partição e escala no Azure Cosmos DB

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) é um serviço de banco de dados globalmente distribuído, multimodelo projetado para ajudá-lo a atingir um desempenho rápido e previsível. Ele pode ser dimensionado perfeitamente junto com o seu aplicativo à medida que cresce. Este artigo fornece uma visão geral de como modelos de particionamento funcionam para todos os modelos de dados no Azure Cosmos DB. Ele também descreve como você pode configurar contêineres do Azure Cosmos DB contêineres para dimensionar com eficiência seus aplicativos.

Particionamento e chaves de partição são discutidas neste vídeo:

> [!VIDEO https://www.youtube.com/embed/SS6WrQ-HJ30]
> 

## <a name="partitioning-in-azure-cosmos-db"></a>Particionamento no BD Cosmos do Azure
No Azure Cosmos DB, você pode armazenar e consultar dados sem esquema com uma latência de milissegundos de dígito único em qualquer escala. O Azure Cosmos DB fornece contêineres para armazenamento de dados denominados *coleções* (para documentos) *grafos*, ou *tabelas*. 

Contêineres são recursos lógicos e podem abranger um ou mais servidores ou partições físicas. O número de partições é determinado pelo Azure Cosmos DB com base no tamanho do armazenamento e na taxa de transferência provisionada para um contêiner ou conjunto de contêineres. 

Uma partição *física* é uma quantidade fixa de armazenamento reservado baseado em SSD, combinado com o valor da variável de recursos de computação (CPU e memória). Cada partição física é replicada para alta disponibilidade. Cada conjunto de contêineres pode compartilhar uma ou mais partições físicas. O gerenciamento de partição física é totalmente gerenciado pelo Azure Cosmos DB e você não precisa escrever um código complexo ou gerenciar suas partições. Os contêineres do Azure Cosmos DB são ilimitados em termos de armazenamento e taxa de transferência. 

Uma partição *lógica* é uma partição em uma partição física que armazena todos os dados associados a um valor de chave de partição única. Várias partições lógicas podem terminar na mesma partição física. No diagrama a seguir, um único contêiner tem três partições lógicas. Cada partição lógica armazena os dados para uma chave de partição, LAX, AMS e MEL, respectivamente. Cada uma das partições lógicas LAX, AMS e MEL não pode crescer além do limite de partição lógico máximo de 10 GB. 

![Particionamento de recursos](./media/introduction/azure-cosmos-db-partitioning.png) 

Quando um contêiner atende aos [pré-requisitos de particionamento](#prerequisites), o particionamento é completamente transparente ao seu aplicativo. O Azure Cosmos DB dá suporte a leituras e gravações rápidas, consultas, lógica transacional, níveis de consistência e controle de acesso refinado por meio de métodos/APIs para um recurso de contêiner único. O serviço lida com a distribuição de dados em partições físicas e lógicas e com o roteamento de solicitações de consulta para a partição correta. 

## <a name="how-does-partitioning-work"></a>Como o particionamento funciona

Como o particionamento funciona? Cada item deve ter uma *chave de partição* e uma *chave de linha*, que o identificam exclusivamente. Sua chave de partição atua como uma partição lógica para seus dados e fornece ao Azure Cosmos DB um limite natural para a distribuição de dados entre partições físicas. Os dados para uma única partição lógica devem residir em uma única partição física, mas o gerenciamento de partição física é gerenciado pelo Azure Cosmos DB. 

De forma resumida, veja como o particionamento funciona no Azure Cosmos DB:

* Você pode provisionar um conjunto de contêineres do Azure Cosmos DB com uma taxa de transferência **T** RU/s (solicitações por segundo).
* Nos bastidores, o Azure Cosmos DB provisiona as partições físicas necessárias para atender as solicitações **T** por segundo. Se **T** for maior que a taxa de transferência máxima por partição física **t**, o Azure Cosmos DB provisionará as partições físicas **N = T/t**. O valor da taxa de transferência máxima por partição(t) é configurado com o Azure Cosmos DB. Esse valor é atribuído com base na taxa de transferência total provisionada e a configuração de hardware utilizada. 
* O Azure Cosmos DB aloca o espaço de chave dos hashes de chave de partição uniformemente entre as partições físicas **N**. Sendo assim, cada partição física hospeda os valores de chave de partição (partições lógicas) **1/N**.
* Quando uma partição física **p** atinge seu limite de armazenamento, o Azure Cosmos DB divide perfeitamente **p** em duas novas partições físicas, **p1** e **p2**. Ele distribui valores correspondentes para cerca de metade das chaves para cada uma das novas partições físicas. Essa operação de divisão é completamente invisível para o aplicativo. Se uma partição física atingir seu limite de armazenamento e todos os dados na partição física pertencerem à mesma chave de partição lógica, a operação de divisão não ocorrerá. Isso ocorre porque todos os dados para uma chave única de partição lógica devem residir na mesma partição física. Nesse caso, uma estratégia de chave de partição diferente deve ser empregada.
* Quando você provisiona uma taxa de transferência maior que **t*N**, o Azure Cosmos DB divide uma ou mais de suas partições físicas para dar suporte à taxa de transferência mais alta.

A semântica das chaves de partição é ligeiramente diferente para corresponder à semântica de cada API, conforme mostrado na tabela a seguir:

| API | Chave de partição | Chave de linha |
| --- | --- | --- |
| SQL | Caminho da chave de partição personalizada | `id` fixo | 
| MongoDB | Chave de fragmentação personalizada  | `_id` fixo | 
| Gremlin | Propriedade da chave de partição personalizada | `id` fixo | 
| Tabela | `PartitionKey` fixo | `RowKey` fixo | 

O Azure Cosmos DB usa o particionamento baseado em hash. Quando você grava um item, o Azure Cosmos DB faz o hash do valor da chave de partição e usa o resultado com hash para determinar em qual partição deve armazenar o item. O Azure Cosmos DB armazena todos os itens com a mesma chave de partição na mesma partição física. A escolha da chave de partição é uma decisão importante que você precisará fazer no momento do design. Escolha um nome de propriedade que tenha uma ampla gama de valores e tenha padrões de acesso uniformes. Se uma partição física atingir o seu limite de armazenamento e os dados na partição tiverem a mesma chave de partição, o Azure Cosmos DB retorna a mensagem *“Chave de partição atingiu o tamanho máximo de 10 GB"*, e a partição não é dividida. Escolher uma chave de partição boa é uma decisão muito importante.

> [!NOTE]
> É uma prática recomendada para ter uma chave de partição com um grande número de valores distintos (por exemplo, centenas ou milhares). Ele permite distribuir a carga de trabalho uniformemente entre esses valores. Uma chave de partição ideal é aquela que é exibida com frequência, como um filtro nas suas consultas, e tem cardinalidade suficiente para garantir que sua solução seja escalonável.
>

Os contêineres do Azure Cosmos DB podem ser criados como *fixos* ou *ilimitados* no portal do Azure. Contêineres de tamanho fixo têm um limite máximo de 10 GB e taxa de transferência de 10.000 RU/s. Para criar um contêiner ilimitado, você deve especificar uma chave de partição e uma taxa de transferência mínima de 1.000 RU/s. 

Os contêineres do Azure Cosmos DB também podem ser configurados para compartilhar a taxa de transferência entre um conjunto de contêineres, no qual cada contêiner deve especificar uma partição de chave e pode aumentar ilimitado.

É uma boa ideia verificar como os seus dados estão distribuídos nas partições. Para verificar uma distribuição de dados no portal, acesse sua conta do Azure Cosmos DB e clique em **Métricas** na seção **Monitoramento** e, em seguida, clique na guia **Armazenamento** para ver como os seus dados estão particionados nas diferentes partições físicas.

![Particionamento de recursos](./media/partition-data/partitionkey-example.png)

A imagem à esquerda acima mostra o resultado de uma chave de partição mal feita e a imagem à direita acima mostra o resultado quando uma chave de partição boa foi escolhida. Na imagem à esquerda, você pode ver que os dados não estão distribuídos uniformemente nas partições. Você deve tentar escolher uma chave de partição que distribui os dados de forma semelhante à da imagem à direita.

<a name="prerequisites"></a>
## <a name="prerequisites-for-partitioning"></a>Pré-requisitos para o particionamento

Para partições físicas para autodivisão em **p1** e **p2**, conforme descrito em [como funciona o particionamento](#how-does-partitioning-work), o contêiner deve ser criado com uma taxa de transferência de 1.000 RU/s ou mais (ou compartilhar taxa de transferência em um conjunto de contêineres), e uma chave de partição deve ser fornecida. Ao criar um contêiner (por exemplo, uma coleção, um gráfico ou uma tabela) no portal do Azure, selecione a opção de capacidade de armazenamento **Ilimitada** para aproveitar o dimensionamento ilimitado. 

Se você criou um contêiner no portal do Azure ou por meio de programação e a taxa de transferência inicial foi de 1.000 RU/s ou mais e você forneceu uma chave de partição, você pode tirar proveito de dimensionamento ilimitado sem alterações no seu contêiner. Isso inclui contêineres **fixos**, contanto que o contêiner inicial tenha sido criado com pelo menos 1.000 RU/s de taxa de transferência e uma chave de partição seja especificada.

Todos os contêineres configurados para compartilhar a taxa de transferência como parte de um conjunto de contêineres são tratados como contêineres **ilimitados**.

Se você tiver criado um contêiner **fixo** sem nenhuma chave de partição ou taxa de transferência inferior a 1.000 RU/s, o contêiner não poderá fazer o dimensionamento automático, conforme descrito neste artigo. Para migrar dados de um contêiner fixo para um contêiner ilimitado (por exemplo, um com pelo menos 1.000 RU/s e uma chave de partição), você precisa usar a [ferramenta de migração de dados](import-data.md) ou a [biblioteca de feed de alterações](change-feed.md). 

## <a name="partitioning-and-provisioned-throughput"></a>Particionamento e produtividade provisionada
O Azure Cosmos DB foi projetado para ter um desempenho previsível. Ao criar um contêiner ou conjunto de contêineres, você reserva a taxa de transferência em termos de *[RUs](request-units.md) (Unidades de Solicitação) por segundo*. Cada solicitação faz uma carga de RU que é proporcional à quantidade de recursos de sistema, como CPU, memória e I/O consumido pela operação. Uma leitura de um documento de 1 KB com consistência de sessão consome 1 RU. Uma leitura é 1 RU, independentemente do número de itens armazenados ou do número de solicitações simultâneas em execução ao mesmo tempo. Itens maiores exigem mais RUs, dependendo do tamanho. Se você souber o tamanho de suas entidades e o número de leituras de que precisa para dar suporte para o seu aplicativo, poderá provisionar a quantidade exata produtividade necessária para as necessidades do seu aplicativo. 

> [!NOTE]
> Para utilizar totalmente a taxa de transferência provisionada para um contêiner ou conjunto de contêineres, você deve escolher uma chave de partição que permita distribuir uniformemente as solicitações em todos os valores diferentes de chave de partição.
> 
> 

<a name="designing-for-partitioning"></a>
## <a name="work-with-the-azure-cosmos-db-apis"></a>Trabalhando com APIs do Azure Cosmos DB
Você pode usar o portal do Azure ou a CLI do Azure para criar contêineres e dimensioná-los a qualquer momento. Esta seção mostra como criar contêineres e especificar a chave de partição e a taxa de transferência provisionada usando cada uma das APIs.


### <a name="sql-api"></a>API do SQL
O exemplo a seguir mostra como criar um contêiner (uma coleção) usando a API do SQL. 

```csharp
DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
await client.CreateDatabaseAsync(new Database { Id = "db" });

DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "coll";
myCollection.PartitionKey.Paths.Add("/deviceId");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("db"),
    myCollection,
    new RequestOptions { OfferThroughput = 20000 });
```

Você pode ler um item (documento) usando o método `GET` na API REST ou usando `ReadDocumentAsync` em um dos SDKs.

```csharp
// Read document. Needs the partition key and the ID to be specified
DeviceReading document = await client.ReadDocumentAsync<DeviceReading>(
  UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
  new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });
```

Para obter mais informações, consulte [Particionamento no Azure Cosmos DB usando a API do SQL](sql-api-partition-data.md).

### <a name="mongodb-api"></a>API do MongoDB
Com a API do MongoDB, você pode criar uma coleção fragmentada por meio de sua ferramenta, driver ou SDK favorito. Neste exemplo, usamos o Shell do Mongo para criar uma coleção.

No Shell do Mongo:

```
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

### <a name="table-api"></a>API de Tabela

Para criar uma tabela usando a API de Tabela, use o método `CreateIfNotExists`. 

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

CloudTable table = tableClient.GetTableReference("people");
table.CreateIfNotExists(throughput: 800);
```

A taxa de transferência provisionada é definida como um argumento de `CreateIfNotExists`. A chave de partição é criada implicitamente como o valor `PartitionKey`. 

Você pode recuperar uma única entidade usando o código a seguir:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
Para obter mais informações, consulte [Desenvolver com a API de Tabela](tutorial-develop-table-dotnet.md).

### <a name="gremlin-api"></a>API do Gremlin

Com a API de Gremlin, você pode usar o portal do Azure ou a CLI do Azure para criar um contêiner que representa um gráfico. Como alternativa, uma vez que o Azure Cosmos DB tem vários modelos, você pode usar uma das outras APIs para criar e dimensionar seu contêiner de grafo.

Você pode ler qualquer vértice ou borda usando a chave de partição e a ID no Gremlin. Por exemplo, para um grafo com uma região ("EUA") como a chave de partição e "Seattle" como a chave de linha, você pode encontrar um vértice usando a seguinte sintaxe:

```
g.V(['USA', 'Seattle'])
```

Você pode referenciar uma borda usando a chave de partição e a chave de linha.

```
g.E(['USA', 'I5'])
```

Para obter mais informações, consulte [Usando um gráfico particionado no Azure Cosmos DB](graph-partitioning.md).


<a name="designing-for-scale"></a>
## <a name="design-for-scale"></a>Design para dimensionamento
Para dimensionar de forma eficiente com o BD Cosmos do Azure, você precisa escolher uma boa chave de partição quando criar seu contêiner. Há dois aspectos principais a considerar ao escolher uma chave de partição boa:

* **Limite de consulta e transações**. Sua escolha de chave de partição deve equilibrar a necessidade de usar transações em relação à necessidade de distribuir suas entidades por várias chaves de partição para garantir uma solução escalonável. Por um lado, você pode definir a mesma chave de partição para todos os seus itens, mas isso pode limitar a escalabilidade da sua solução. Por outro lado, você pode atribuir uma chave de partição exclusiva para cada item. Essa opção é altamente escalonável, mas impede que você use transações entre documentos por meio de procedimentos armazenados e gatilhos. Uma chave de partição ideal é aquela que permite o uso de consultas eficientes, e que tenha cardinalidade suficiente para garantir que sua solução seja escalonável. 
* **Sem gargalos de armazenamento e desempenho**. É importante escolher uma propriedade que permita que as gravações sejam distribuídas entre vários valores distintos. Solicitações para a mesma chave de partição não podem exceder a taxa de transferência provisionada alocada para uma partição e serão limitadas por taxa. Portanto, é importante escolher uma chave de partição que não resulte em "pontos de acesso" dentro de seu aplicativo. Como todos os dados de uma única chave de partição devem ser armazenados em uma partição, você deve evitar chaves de partição com grandes volumes de dados para o mesmo valor. 

Vejamos alguns cenários do mundo real e boas chaves de partição para cada um deles:
* Se você estiver implementando um perfil do usuário de back-end, a *ID de usuário* será uma boa opção para uma chave de partição.
* Se você estiver armazenando dados de IoT, por exemplo, o estado do dispositivo, uma *ID do dispositivo* será uma boa opção para uma chave de partição.
* Se você estiver usando o Azure Cosmos DB para registrar em log dados de série temporal, o *nome do host* ou a *ID do processo* serão boas opções para uma chave de partição.
* Se você tiver uma arquitetura de multilocatário, a *ID do locatário* será uma boa opção para uma chave de partição.

Em alguns casos de uso, como perfis de usuário e IoT, a chave de partição pode ser igual à sua *ID* (chave de documento). Em outros, como os dados de série temporal, você pode ter uma chave de partição que é diferente da *ID*.

### <a name="partitioning-and-loggingtime-series-data"></a>Dados de particionamento e registro em log/da série temporal
Um dos casos de uso comuns no Azure Cosmos DB é registro em log e telemetria. É importante escolher uma boa chave de partição nesse cenário, já que você pode precisar ler/gravar enormes volumes de dados. A escolha de uma chave de partição depende das suas taxas de leitura e gravação e dos tipos de consultas que você pretende executar. Aqui estão algumas dicas sobre como escolher uma boa chave de partição:

* Se o caso de uso envolve uma pequena taxa de gravações que se acumulam durante longo período de tempo e você precisa consultar por intervalos de carimbos de data/hora e outros filtros, use um rollup de carimbo de data/hora. Por exemplo, uma boa abordagem é usar a data como uma chave de partição. Com essa abordagem, você pode consultar todos os dados para uma data determinada a partir de uma única partição. 
* Se sua carga de trabalho pesada de gravação, que é muito comum neste cenário, use uma chave de partição que não se baseia o carimbo de data/hora. Como tal, o Azure Cosmos DB pode distribuir e dimensionar gravações uniformemente por várias partições. Aqui, um *nome do host*, *ID do processo*, *ID da atividade* ou outra propriedade com alta cardinalidade é uma boa opção. 
* A outra abordagem é híbrida, onde você tem vários contêineres, um para cada dia/mês e a chave de partição é uma propriedade mais granular, como *nome do host*. Essa abordagem tem a vantagem de que você pode definir a taxa de transferência diferente para cada contêiner ou um conjunto de contêineres com base na janela de tempo e as necessidades de desempenho e dimensionamento. Por exemplo, um contêiner para o mês atual pode ser configurado com uma taxa de transferência maior porque ele serve leituras e gravações. Meses anteriores podem ser provisionados com uma taxa de transferência menor porque só servem leituras.

### <a name="partitioning-and-multitenancy"></a>Particionamento e multilocação
Se você estiver implementando um aplicativo multilocatário usando o Azure Cosmos DB, haverá dois designs populares a considerar: *uma chave de uma partição por locatário* e *um contêiner por locatário*. Aqui estão os prós e contras de cada um:

* **Chave de uma partição por locatário**. Nesse modelo, os locatários são colocados em um único contêiner. Consultas e inserções de um único locatário podem ser executadas em relação a uma única partição. Você também pode implementar a lógica transacional em todos os itens que pertencem a um locatário. Como vários locatários compartilham um contêiner, você pode utilizar melhor o armazenamento e taxa de transferência provisionada realizando o pooling de recursos para todos locatários em um único contêiner em vez de provisionar para cada locatário. A desvantagem é que você não tem o isolamento de desempenho por locatário. A taxa de transferência aumentada para assegurar o desempenho será aplicada a todo o contêiner com todos os locatários em vez de aumentar para um locatário individual.
* **Um contêiner por locatário**. Nesse modelo, cada locatário tem seu próprio contêiner, e você poderá reservar a taxa de transferência com desempenho garantido por locatário. Esse modelo é mais econômico para aplicativos multilocatários com um número pequeno de locatários.

Você também pode usar uma abordagem híbrida que coloca os locatários pequenos juntos e isola os locatários maiores para seu próprio contêiner.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, fornecemos uma visão geral dos conceitos e práticas recomendadas para o dimensionamento e particionamento no Azure Cosmos DB. 

* Saiba mais sobre a [taxa de transferência provisionada no Azure Cosmos DB](request-units.md).
* Saiba mais sobre [distribuição global no Azure Cosmos DB](distribute-data-globally.md).



