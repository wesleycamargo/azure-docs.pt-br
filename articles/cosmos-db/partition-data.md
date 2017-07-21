---
title: Particionamento e escala horizontal no BD Cosmos do Azure | Microsoft Docs
description: "Saiba mais sobre como o particionamento funciona no BD Cosmos do Azure, como configurar o particionamento e as chaves de partição e como escolher a chave de partição correta para seu aplicativo."
services: cosmos-db
author: arramac
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: cac9a8cd-b5a3-4827-8505-d40bb61b2416
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: 5f501bdb0a3c478a436d729dfe045ad8e39bd3bb
ms.contentlocale: pt-br
ms.lasthandoff: 06/14/2017


---

# <a name="how-to-partition-and-scale-in-azure-cosmos-db"></a>Como particionar e dimensionar no BD Cosmos do Azure

O [BD Cosmos do Microsoft Azure](https://azure.microsoft.com/services/cosmos-db/) é um serviço com vários modelos de banco de dados, distribuído globalmente, que foi criado para ajudá-lo a obter um desempenho rápido e previsível e a dimensionar continuamente conforme o crescimento de seu aplicativo. Este artigo fornece uma visão geral de como o particionamento funciona para todos os modelos de dados no BD Cosmos do Azure e descreve como você pode configurar os contêineres do BD Cosmos do Azure para dimensionar seus aplicativos com eficiência.

O particionamento e as chaves de partição também são abordados neste vídeo do Azure Friday com Scott Hanselman e o gerente de engenharia principal do BD Cosmos do Azure, Shireesh Thota.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-DocumentDB-Elastic-Scale-Partitioning/player]
> 

## <a name="partitioning-in-azure-cosmos-db"></a>Particionamento no BD Cosmos do Azure
No BD Cosmos do Azure, você pode armazenar e consultar dados sem esquema com tempos de resposta da ordem de milissegundos em qualquer escala. O BD Cosmos fornece contêineres para o armazenamento de dados chamados **coleções (para documentos), gráficos ou tabelas**. Contêineres são recursos lógicos e podem abranger um ou mais servidores ou partições físicas. O número de partições é determinado pelo BD Cosmos com base no tamanho do armazenamento e na taxa de transferência provisionada do contêiner. Cada partição no BD Cosmos tem uma quantidade fixa de armazenamento com suporte de SSD associado a ela e é replicada para alta disponibilidade. O gerenciamento de partição é totalmente gerenciado pelo BD Cosmos do Azure e você não precisa escrever um código complexo ou gerenciar suas partições. Os contêineres do BD Cosmos são ilimitados em termos de armazenamento e taxa de transferência. 

![horizontal](./media/introduction/azure-cosmos-db-partitioning.png) 

O particionamento é transparente para seu aplicativo. O BD Cosmos dá suporte a leituras e gravações rápidas, consultas, lógica transacional, níveis de consistência e controle de acesso refinado por meio de métodos/APIs para um recurso de contêiner único. O serviço lida com a distribuição de dados em partições e com o roteamento de solicitações de consulta para a partição correta. 

Como o particionamento funciona? Cada item deve ter uma chave de partição e uma chave de linha, que o identificam exclusivamente. Sua chave de partição atua como uma partição lógica para seus dados e fornece ao BD Cosmos um limite natural para a distribuição de dados entre partições. De forma resumida, veja como o particionamento funciona no BD Cosmos do Azure:

* Você pode provisionar um contêiner do BD Cosmos com uma taxa de transferência de `T` solicitações/s
* Nos bastidores, o BD Cosmos provisiona as partições necessárias para atender `T` solicitações/s. Se `T` for maior que a taxa de transferência máxima por partição `t`, o BD Cosmos provisionará `N` = `T/t` partições
* O BD Cosmos aloca o espaço de chave dos hashes de chave de partição uniformemente entre as `N` partições. Sendo assim, cada partição (partição física) hospeda 1-N valores de chave de partição (partições lógicas)
* Quando uma partição física `p` atinge seu limite de armazenamento, o BD Cosmos divide `p` em duas novas partições `p1` e `p2` e distribui os valores correspondentes a cerca de metade das chaves para cada uma das partições. Essa operação de divisão é invisível para o aplicativo.
* Da mesma forma, quando você provisiona uma taxa de transferência superior à taxa de transferência `t*N`, o BD Cosmos divide uma ou mais de suas partições para dar suporte à taxa de transferência mais alta

A semântica das chaves de partição é ligeiramente diferente para corresponder à semântica de cada API, conforme mostrado na tabela a seguir:

| API | Chave de partição | Chave de linha |
| --- | --- | --- |
| DocumentDB | caminho da chave de partição personalizada | `id` fixo | 
| MongoDB | chave de fragmentação personalizada  | `_id` fixo | 
| Gráfico | propriedade da chave de partição personalizada | `id` fixo | 
| Tabela | `PartitionKey` fixo | `RowKey` fixo | 

O BD Cosmos usa o particionamento baseado em hash. Quando você grava um item, o BD Cosmos faz o hash do valor da chave de partição e usa o resultado com hash para determinar em qual partição deve armazenar o item. O BD Cosmos armazena todos os itens com a mesma chave de partição na mesma partição física. A escolha da chave de partição é uma decisão importante que você precisará fazer no momento do design. Você deve escolher um nome de propriedade que tenha uma ampla gama de valores e tenha padrões de acesso uniformes.

> [!NOTE]
> É uma prática recomendada ter uma chave de partição com muitos valores distintos (100s a 1000s, no mínimo).
>

Os contêineres do BD Cosmos do Azure podem ser criados como "fixos" ou "ilimitados". Contêineres de tamanho fixo têm um limite máximo de 10 GB e taxa de transferência de 10.000 RU/s. Algumas APIs permitem que a chave de partição seja omitida para contêineres de tamanho fixo. Para criar um contêiner ilimitado, você deve especificar uma taxa de transferência mínima de 2.500 RU/s.

## <a name="partitioning-and-provisioned-throughput"></a>Particionamento e produtividade provisionada
O BD Cosmos foi projetado para ter um desempenho previsível. Quando cria um contêiner, você reserva a taxa de transferência em termos de **[RUs](request-units.md) (unidades de solicitação) por segundo, com um complemento potencial de RUs por minuto**. A cada solicitação é atribuído um custo de unidade de solicitação, que é proporcional à quantidade de recursos do sistema, como CPU, memória e E/S consumida pela operação. Uma leitura de um documento de 1 KB com consistência de sessão consome uma unidade de solicitação. Uma leitura é 1 RU, independentemente do número de itens armazenados ou do número de solicitações simultâneas em execução ao mesmo tempo. Itens maiores exigem mais unidades de solicitação, dependendo do tamanho. Se você souber o tamanho de suas entidades e o número de leituras de que precisa para dar suporte para o seu aplicativo, poderá provisionar a quantidade exata produtividade necessária para as necessidades de leitura do seu aplicativo. 

> [!NOTE]
> Para atingir a taxa de transferência total do contêiner, você deve escolher uma chave de partição que permita distribuir uniformemente as solicitações entre alguns valores diferentes de chave de partição.
> 
> 

<a name="designing-for-partitioning"></a>
## <a name="working-with-the-azure-cosmos-db-apis"></a>Trabalhando com APIs do BD Cosmos do Azure
Você pode usar o portal do Azure ou a CLI do Azure para criar contêineres e dimensioná-los a qualquer momento. Esta seção mostra como criar contêineres e especificar a definição de chave de partição e de taxa de transferência em cada uma das APIs com suporte.

### <a name="documentdb-api"></a>API do DocumentDB
O exemplo a seguir mostra como criar um contêiner (coleção) usando a API do DocumentDB. Você pode encontrar mais detalhes em [Particionamento com a API do DocumentDB](partition-data.md).

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

### <a name="mongodb-api"></a>API do MongoDB
Com a API do MongoDB, você pode criar uma coleção fragmentada por meio de sua ferramenta, driver ou SDK favorito. Neste exemplo, usamos o Shell do Mongo para a criação da coleção.

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

Com a API de Tabela, você especifica a taxa de transferência para tabelas na configuração appSettings de seu aplicativo:

```xml
<configuration>
    <appSettings>
      <!--Table creation options -->
      <add key="TableThroughput" value="700"/>
    </appSettings>
</configuration>
```

Em seguida, você cria uma tabela usando o SDK do Armazenamento de Tabelas do Azure. A chave de partição é criada implicitamente como o valor `PartitionKey`. 

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

CloudTable table = tableClient.GetTableReference("people");
table.CreateIfNotExists();
```

Você pode recuperar uma única entidade usando o trecho a seguir:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
Consulte [Desenvolvendo com a API de Tabela](tutorial-develop-table-dotnet.md) para obter mais detalhes.

### <a name="graph-api"></a>Graph API

Com a API do Graph, você deve usar o portal ou a CLI do Azure para criar contêineres. Como alternativa, uma vez que o BD Cosmos do Azure tem vários modelos, você pode usar um dos outros modelos para criar e dimensionar seu contêiner de gráfico.

Você pode ler qualquer vértice ou borda usando a chave de partição e a ID no Gremlin. Por exemplo, para um gráfico com uma região ("EUA") como a chave de partição e "Seattle" como a chave de linha, você pode encontrar um vértice usando a seguinte sintaxe:

```
g.V(['USA', 'Seattle'])
```

O mesmo acontece com as bordas, você pode referenciar uma borda usando a chave de partição e a chave de linha.

```
g.E(['USA', 'I5'])
```

Consulte [Suporte do Gremlin para o BD Cosmos](gremlin-support.md) para obter mais detalhes.


<a name="designing-for-partitioning"></a>
## <a name="designing-for-partitioning"></a>Design de particionamento
Para dimensionar de forma eficiente com o BD Cosmos do Azure, você precisa escolher uma boa chave de partição quando criar seu contêiner. Há dois aspectos principais a considerar ao escolher uma chave de partição:

* **Limite para consultas e transações**: sua escolha de chave de partição deve equilibrar a necessidade de habilitar o uso de transações em relação à necessidade de distribuir suas entidades entre várias chaves de partição para garantir uma solução escalonável. Por um lado, você pode definir a mesma chave de partição para todos os seus itens, mas isso pode limitar a escalabilidade da solução. Por outro lado, você pode atribuir uma chave de partição exclusiva para cada itens, o que seria altamente escalonável, mas impediria o uso de transações entre documentos por meio de procedimentos e gatilhos armazenados. Uma chave de partição ideal é aquela que permite o uso de consultas eficientes, e que tenha cardinalidade suficiente para garantir que sua solução seja escalonável. 
* **Sem afunilamentos de desempenho e armazenamento**: é importante escolher uma propriedade que permita que as gravações sejam distribuídas entre vários valores distintos. Solicitações para a mesma chave de partição não podem ultrapassar a taxa de transferência de uma única partição e sofrem restrições. Portanto, é importante escolher uma chave de partição que não resulte em "pontos de acesso" dentro de seu aplicativo. Como todos os dados de uma única chave de partição devem ser armazenados em uma partição, também é recomendável evitar chaves de partição com grandes volumes de dados para o mesmo valor. 

Vejamos alguns cenários do mundo real e boas chaves de partição para cada um deles:
* Se você estiver implementando um perfil do usuário de back-end, a ID de usuário será uma boa opção para a chave de partição.
* Se você estiver armazenando dados de IoT, por exemplo, o estado do dispositivo, uma ID do dispositivo será uma boa opção para a chave de partição.
* Se você estiver usando o Banco de Dados de Documentos para registrar em log dados de série temporal, a ID do processo ou o nome do host será uma boa opção para a chave de partição.
* Se você tiver uma arquitetura de multilocatário, a ID do locatário será uma boa opção para a chave de partição.

Em alguns casos de uso, como perfis de usuário e IoT, a chave de partição pode ser igual à sua ID (chave de documento). Em outros, como os dados de série temporal, você pode ter uma chave de partição que é diferente da ID.

### <a name="partitioning-and-loggingtime-series-data"></a>Dados de particionamento e registro em log/da série temporal
Um dos casos de uso comuns do BD Cosmos é para registro em log e telemetria. É importante escolher uma boa chave de partição, já que você pode precisar ler/gravar enormes volumes de dados. A escolha depende das suas taxas de leitura e gravação e dos tipos de consultas que você pretende executar. Aqui estão algumas dicas sobre como escolher uma boa chave de partição.

* Se o seu caso de uso envolve uma pequena taxa de gravações que se acumulam por um longo período e existe a necessidade de consultar por intervalos de carimbos de data/hora e outros filtros, usar um pacote cumulativo de atualizações do carimbo de data/hora, como a data, como uma chave de partição é uma boa abordagem. Isso permite que você consulte todos os dados para uma data de uma única partição. 
* Se sua carga de trabalho for intensa em termos de gravação, o que é mais comum, você deverá usar uma chave de partição que não esteja baseada em carimbo de data/hora para que o BD Cosmos possa distribuir as gravações uniformemente entre várias partições. Aqui, um nome do host, ID do processo, ID da atividade ou outra propriedade com alta cardinalidade é uma boa opção. 
* Uma terceira abordagem é híbrida, em que você tem vários contêineres, um para cada dia/mês e a chave de partição é uma propriedade granular, como nome do host. Isso tem o benefício de permitir que você defina diferentes taxas de transferência com base na janela de tempo, por exemplo, o contêiner para o mês atual é provisionado com uma taxa de transferência maior porque só atua em operações de leitura e gravação, enquanto aqueles para os meses anteriores são provisionados com taxa de transferência menor por atuarem apenas para operações de leitura.

### <a name="partitioning-and-multi-tenancy"></a>Particionamento e multilocação
Se você estiver implementando um aplicativo multilocatário usando o BD Cosmos, haverá dois padrões populares – uma chave de uma partição por locatário e um contêiner por locatário. Aqui estão os prós e contras de cada um:

* Uma chave de partição por locatário: nesse modelo, os locatários são colocados em um único contêiner. No entanto, consultas e inserções de itens em um único locatário podem ser executadas em relação a uma única partição. Você também pode implementar a lógica transacional em todos os itens dentro de um locatário. Como vários locatários compartilham um contêiner, você pode economizar custos de armazenamento e taxa de transferência realizando o pooling de recursos para locatários em um único contêiner em vez de provisionar capacidade extra para cada locatário. A desvantagem é que você não tem o isolamento de desempenho por locatário. Aumentos de taxa de transferência/produtividade se aplicam a todo o contêiner, versus aumentos direcionados para locatários.
* Um contêiner por locatário: cada locatário tem seu próprio contêiner. Nesse modelo, você pode reservar o desempenho por locatário. Com o novo modelo de preços de provisionamento do BD Cosmos, esse modelo é mais econômico para aplicativos multilocatário com um número pequeno de locatários.

Você também pode usar uma abordagem de combinação/em camadas que coloca os locatários pequenos juntos e migra os locatários maiores para seu próprio contêiner.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, fornecemos uma visão geral dos conceitos e práticas recomendadas para o particionamento com qualquer API do BD Cosmos do Azure. 

* Saiba mais sobre a [taxa de transferência provisionada no BD Cosmos do Azure](request-units.md)
* Saiba mais sobre [distribuição global no BD Cosmos do Azure](distribute-data-globally.md)




