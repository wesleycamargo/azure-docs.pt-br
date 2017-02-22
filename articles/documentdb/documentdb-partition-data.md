---
title: Particionando e dimensionando no Azure DocumentDB | Microsoft Docs
description: "Saiba mais sobre como o particionamento funciona no Banco de Dados de Documentos do Azure, como configurar o particionamento e chaves de partição e como escolher a chave de partição correta para seu aplicativo."
services: documentdb
author: arramac
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 702c39b4-1798-48dd-9993-4493a2f6df9e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 876e0fd12d045bba85d1e30d4abfcb8ce421213a
ms.openlocfilehash: ed58e623ff74a21df25fc93346e571edec7b40da


---
# <a name="partitioning-and-scaling-in-azure-documentdb"></a>Particionamento e dimensionamento no Banco de Dados de Documentos do Azure
[Banco de Dados de Documentos do Microsoft Azure](https://azure.microsoft.com/services/documentdb/) foi criado para ajudá-lo a obter um desempenho rápido e previsível e para escalar continuamente com seu aplicativo conforme ele cresce. Este artigo fornece uma visão geral de como o particionamento funciona no Banco de Dados de Documentos e descreve como você pode configurar as coleções do Banco de Dados de Documentos para escalar seus aplicativos com eficiência.

Depois de ler este artigo, você poderá responder as seguintes perguntas:   

* Como funciona o particionamento no Banco de Dados de Documentos do Azure?
* Como configurar o particionamento no Banco de Dados de Documentos
* O que são chaves de partição e como escolher a chave de partição correta para meu aplicativo?

Para começar com o código, baixe o projeto do [Exemplo de driver de teste de desempenho do Banco de Dados de Documentos](https://github.com/Azure/azure-documentdb-dotnet/tree/a2d61ddb53f8ab2a23d3ce323c77afcf5a608f52/samples/documentdb-benchmark). 

Particionamento e chaves de partição também são cobertas neste vídeo do Azure Friday com Scott Hanselman e o gerente de engenharia principal do DocumentDB, Shireesh Thota.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-DocumentDB-Elastic-Scale-Partitioning/player]
> 

## <a name="partitioning-in-documentdb"></a>Particionamento no Banco de Dados de Documentos
No Banco de Dados de Documentos, você pode armazenar e consultar documentos JSON sem esquema com tempos de resposta da ordem de milissegundo em qualquer escala. O Banco de Dados de Documentos fornece contêineres para o armazenamento de dados chamados **coleções**. Coleções são recursos lógicos e podem abranger um ou mais servidores ou partições físicas. O número de partições é determinado pelo Banco de Dados de Documentos com base no tamanho do armazenamento e a produtividade provisionada da coleção. Cada partição no Banco de Dados de Documentos tem uma quantidade fixa de armazenamento com suporte de SSD associado a ela e é replicada para alta disponibilidade. O gerenciamento de partição é totalmente gerenciado pelo Banco de Dados de Documentos do Azure e você não precisa escrever um código complexo ou gerenciar suas partições. As coleções do Banco de Dados de Documentos são **praticamente ilimitadas** em termos de armazenamento e produtividade. 

O particionamento é completamente transparente para seu aplicativo. O Banco de Dados de Documentos dá suporte a leituras e gravações rápidas, consultas SQL e LINQ, lógica transacional baseada em JavaScript, níveis de consistência e controle de acesso refinado por meio de chamadas à API REST para um recurso de coleção única. O serviço lida com a distribuição de dados em partições e com o roteamento de solicitações de consulta para a partição correta. 

Como isso funciona? Ao criar uma coleção no Banco de Dados de Documentos, você observa que há um valor de configuração de **propriedade de chave de partição** que você pode especificar. Essa é a propriedade JSON (ou caminho) nos documentos que pode ser usada pelo Banco de Dados de Documentos para distribuir dados entre vários servidores ou partições. O Banco de Dados de Documentos realizará o hash do valor da chave de partição e usará o resultado de hash para determinar a partição em que o documento JSON será armazenado. Todos os documentos com a mesma chave de partição serão armazenados na mesma partição. 

Por exemplo, considere um aplicativo que armazena dados sobre funcionários e seus departamentos no Banco de Dados de Documentos. Vamos escolher `"department"` como a propriedade de chave de partição, para que seja possível escalar horizontalmente os dados por departamento. Cada documento no Banco de Dados de Documentos deve conter uma propriedade `"id"` obrigatória que deve ser exclusiva para cada documento com o mesmo valor de chave de partição, por exemplo, `"Marketing`". Todos os documentos armazenados em uma coleção devem ter uma combinação exclusiva de ID e chave de partição, por exemplo, `{ "Department": "Marketing", "id": "0001" }`, `{ "Department": "Marketing", "id": "0002" }` e `{ "Department": "Sales", "id": "0001" }`. Em outras palavras, a propriedade composta de (ID, chave de partição) é a chave primária para sua coleção.

## <a name="partition-keys"></a>Chaves de partição
A escolha da chave de partição é uma decisão importante que você precisará fazer no momento do design. Você deve escolher um nome de propriedade JSON que tenha uma ampla variedade de valores e provavelmente tenha padrões de acesso distribuídos uniformemente. A chave de partição é especificada como um caminho JSON, por exemplo, `/department` representa o departamento de propriedade. 

A tabela a seguir mostra exemplos de definições de chave de partição e os valores JSON correspondentes a cada uma.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Caminho de Chave de Partição</strong></p></td>
            <td valign="top"><p><strong>Descrição</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>/department</p></td>
            <td valign="top"><p>Corresponde ao valor JSON de doc.department, em que o doc é o documento.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/properties/name</p></td>
            <td valign="top"><p>Corresponde ao valor JSON de doc.properties.name, em que o doc é o documento (propriedade aninhada).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/id</p></td>
            <td valign="top"><p>Corresponde ao valor JSON de doc.id (a id e a chave de partição são a mesma propriedade).</p></td>
        </tr>
        <tr>
            <td valign="top"><p>/"nome do departamento"</p></td>
            <td valign="top"><p>Corresponde ao valor JSON de doc.["nome do departamento"], em que o doc é o documento.</p></td>
        </tr>
    </tbody>
</table>

> [!NOTE]
> A sintaxe de caminho de chave de partição é semelhante à especificação de caminho para caminhos de política de indexação, com a principal diferença de que o caminho corresponde à propriedade em vez do valor, ou seja, não há nenhum caractere curinga no final. Por exemplo, você especificaria /department/? para indexar os valores de departamento, mas especificaria /department como a definição de chave de partição. O caminho da chave de partição é indexado explicitamente e não pode ser excluído da indexação usando substituições de política de indexação.
> 
> 

Vamos analisar como a opção da chave de partição afeta o desempenho do seu aplicativo.

## <a name="partitioning-and-provisioned-throughput"></a>Particionamento e produtividade provisionada
O Banco de Dados de Documentos foi projetado para um desempenho previsível. Ao criar uma coleção, você reserva a produtividade em termos de RUs (**[unidades de solicitação](documentdb-request-units.md)) por segundo**. A cada solicitação, é atribuído um custo de unidade de solicitação proporcional à quantidade de recursos do sistema como CPU e I/O consumida pela operação. Uma leitura de um documento de 1 KB com consistência de sessão consome 1 unidade de solicitação. Uma leitura é 1 RU, independentemente do número de itens armazenados ou do número de solicitações simultâneas em execução ao mesmo tempo. Documentos maiores exigem mais unidades de solicitação, dependendo do tamanho. Se você souber o tamanho de suas entidades e o número de leituras de que precisa para dar suporte para o seu aplicativo, poderá provisionar a quantidade exata produtividade necessária para as necessidades de leitura do seu aplicativo. 

Quando o Banco de Dados de Documentos armazena documentos, ele os distribui uniformemente entre partições com base no valor da chave de partição. A produtividade também é distribuída uniformemente entre as partições disponíveis, isto é, a produtividade por partição = (produtividade total por coleção) / (número de partições). 

> [!NOTE]
> Para obter a produtividade total da coleção, você deve escolher uma chave de partição que permita distribuir uniformemente as solicitações entre vários valores de chave de partição distintos.
> 
> 

## <a name="single-partition-and-partitioned-collections"></a>Partição única e coleções particionadas
O Banco de Dados de Documentos dá suporte à criação de coleções de partição única e particionadas. 

* **Coleções particionadas** podem abranger várias partições e dar suporte a armazenamento e taxa de transferência ilimitados. Você deve especificar uma chave de partição para a coleção. 
* **Coleções de partição única** têm opções de preço mais baixo, mas são limitadas em termos de armazenamento máximo de taxa de transferência. Você não precisa especificar uma chave de partição para essas coleções. Recomendamos o uso de coleções particionadas em detrimento das coleções com partição única para todos os cenários, exceto em situações nas quais você espera apenas uma pequena quantidade de armazenamento de dados e solicitações.

![Coleções particionadas no Banco de Dados de Documentos][2] 

As coleções particionadas podem dar suporte a armazenamento e taxa de transferência ilimitados.

A tabela a seguir lista as diferenças entre trabalhar com coleções de partição única e particionadas:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p></p></td>
            <td valign="top"><p><strong>Coleção de Partição Única</strong></p></td>
            <td valign="top"><p><strong>Coleção Particionada</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Chave de partição</p></td>
            <td valign="top"><p>Nenhum</p></td>
            <td valign="top"><p>Obrigatório</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Chave primária para o documento</p></td>
            <td valign="top"><p>"id"</p></td>
            <td valign="top"><p>chave composta &lt;chave da partição&gt; e "id"</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Armazenamento mínimo</p></td>
            <td valign="top"><p>0 GB</p></td>
            <td valign="top"><p>0 GB</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Armazenamento máximo</p></td>
            <td valign="top"><p>10 GB</p></td>
            <td valign="top"><p>Ilimitado</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Produtividade mínima</p></td>
            <td valign="top"><p>400 unidades de solicitação por segundo</p></td>
            <td valign="top"><p>2.500 unidades de solicitação por segundo</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Produtividade máxima</p></td>
            <td valign="top"><p>10.000 unidades de solicitação por segundo</p></td>
            <td valign="top"><p>Ilimitado</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Versões de API</p></td>
            <td valign="top"><p>Todos</p></td>
            <td valign="top"><p>API 2015-12-16 e mais recentes</p></td>
        </tr>
    </tbody>
</table>

## <a name="working-with-the-sdks"></a>Trabalhando com os SDKs
O Banco de Dados de Documentos do Azure adicionou suporte para particionamento automático com a [API REST versão 2015-12-16](https://msdn.microsoft.com/library/azure/dn781481.aspx). Para criar coleções particionadas, você deve baixar versões do SDK 1.6.0 ou mais recentes em uma das plataformas do SDK com suporte (.NET, Node.js, Java, Python). 

### <a name="creating-partitioned-collections"></a>Criando coleções particionadas
O exemplo a seguir mostra um trecho de código .NET para criar uma coleção para armazenar dados de telemetria do dispositivo de 20.000 unidades de solicitação por segundo de produtividade. O SDK define o valor de OfferThroughput (que por sua vez define o cabeçalho de solicitação `x-ms-offer-throughput` na API REST). Aqui, definimos `/deviceId` como a chave de partição. A opção de chave de partição é salva com o restante dos metadados de coleção, como o nome e a política de indexação.

Para este exemplo, escolhemos `deviceId` , pois sabemos que (a) uma vez que há um grande número de dispositivos, as gravações podem ser distribuídas uniformemente nas partições e nos permite dimensionar o banco de dados para incluir grandes volumes de dados e (b) muitas solicitações como buscar a última leitura para um dispositivo limitam-se a um único deviceId e podem ser recuperadas de uma única partição.

    DocumentClient client = new DocumentClient(new Uri(endpoint), authKey);
    await client.CreateDatabaseAsync(new Database { Id = "db" });

    // Collection for device telemetry. Here the JSON property deviceId will be used as the partition key to 
    // spread across partitions. Configured for 10K RU/s throughput and an indexing policy that supports 
    // sorting against any number or string property.
    DocumentCollection myCollection = new DocumentCollection();
    myCollection.Id = "coll";
    myCollection.PartitionKey.Paths.Add("/deviceId");

    await client.CreateDocumentCollectionAsync(
        UriFactory.CreateDatabaseUri("db"),
        myCollection,
        new RequestOptions { OfferThroughput = 20000 });


> [!NOTE]
> Para criar coleções particionadas sando o SDK, você deve especificar um valor de produtividade igual ou maior que 10.100 RU/s. Para definir um valor de taxa de transferência entre 2.500 e 10.000 para coleções particionadas, você deve usar o portal do Azure temporariamente, pois esses novos valores mais baixos ainda não estão disponíveis no SDK.
> 
> 

Esse método faz uma chamada à API REST ao Banco de Dados de Documentos e o serviço provisionará várias partições com base na produtividade solicitada. Você pode alterar a produtividade de uma coleção conforme suas necessidades de desempenho aumentarem. 

### <a name="reading-and-writing-documents"></a>Ler e gravar documentos
Agora, vamos inserir dados no Banco de Dados de Documentos. Aqui está uma classe de exemplo que contém uma leitura de dispositivo e uma chamada para CreateDocumentAsync para inserir uma nova leitura de dispositivo em uma coleção.

    public class DeviceReading
    {
        [JsonProperty("id")]
        public string Id;

        [JsonProperty("deviceId")]
        public string DeviceId;

        [JsonConverter(typeof(IsoDateTimeConverter))]
        [JsonProperty("readingTime")]
        public DateTime ReadingTime;

        [JsonProperty("metricType")]
        public string MetricType;

        [JsonProperty("unit")]
        public string Unit;

        [JsonProperty("metricValue")]
        public double MetricValue;
      }

    // Create a document. Here the partition key is extracted as "XMS-0001" based on the collection definition
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new DeviceReading
        {
            Id = "XMS-001-FE24C",
            DeviceId = "XMS-0001",
            MetricType = "Temperature",
            MetricValue = 105.00,
            Unit = "Fahrenheit",
            ReadingTime = DateTime.UtcNow
        });


Vamos ler o documento pela chave da partição e ID, atualizá-lo e, então, como uma etapa final, excluí-lo pela chave de partição e ID. Observe que as leituras incluem um valor de PartitionKey (correspondente ao cabeçalho de solicitação `x-ms-documentdb-partitionkey` na API REST).

    // Read document. Needs the partition key and the ID to be specified
    Document result = await client.ReadDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });

    DeviceReading reading = (DeviceReading)(dynamic)result;

    // Update the document. Partition key is not required, again extracted from the document
    reading.MetricValue = 104;
    reading.ReadingTime = DateTime.UtcNow;

    await client.ReplaceDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      reading);

    // Delete document. Needs partition key
    await client.DeleteDocumentAsync(
      UriFactory.CreateDocumentUri("db", "coll", "XMS-001-FE24C"), 
      new RequestOptions { PartitionKey = new PartitionKey("XMS-0001") });



### <a name="querying-partitioned-collections"></a>Consultando coleções particionadas
Quando você consulta dados em coleções particionadas, o Banco de Dados de Documentos roteia automaticamente a consulta para as partições que correspondem aos valores de chave de partição especificados no filtro (caso haja algum). Por exemplo, esta consulta é roteada para apenas a partição que contém a chave de partição "XMS-0001".

    // Query using partition key
    IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"))
        .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");

A consulta a seguir não tem um filtro na chave de partição (DeviceId) e é distribuída para todas as partições em que ela é executada no índice da partição. Observe que você precisa especificar o EnableCrossPartitionQuery (`x-ms-documentdb-query-enablecrosspartition` na API REST) para fazer com que o SDK execute uma consulta em partições.

    // Query across partition keys
    IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"), 
        new FeedOptions { EnableCrossPartitionQuery = true })
        .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);

### <a name="parallel-query-execution"></a>Execução de consulta paralela
Os SDKs do Banco de Dados de Documentos 1.9.0 e superiores são compatíveis com execução de consulta paralela, que permite realizar consultas de baixa latência em coleções particionadas, mesmo quando elas precisam tocar um grande número de partições. Por exemplo, a consulta a seguir é configurada para ser executada paralelamente entre partições.

    // Cross-partition Order By Queries
    IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
        UriFactory.CreateDocumentCollectionUri("db", "coll"), 
        new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
        .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
        .OrderBy(m => m.MetricValue);

Você pode gerenciar a execução de consulta paralela ajustando os seguintes parâmetros:

* Ao definir `MaxDegreeOfParallelism`, é possível controlar o grau de paralelismo, isto é, o número máximo de conexões de rede simultâneas com as partições da coleção. Se você definir esse valor como -1, o grau de paralelismo será gerenciado pelo SDK. Se o `MaxDegreeOfParallelism` não for especificado nem definido para 0, que é o valor padrão, haverá uma única conexão de rede para as partições da coleção.
* Ao definir `MaxBufferedItemCount`, é possível compensar a latência da consulta e a utilização da memória no lado do cliente. Se você omitir esse parâmetro ou defini-lo como -1, o número de itens armazenados em buffer durante a execução da consulta paralela será gerenciado pelo SDK.

Dado o mesmo estado da coleção, uma consulta paralela retornará resultados na mesma ordem da execução serial. Ao executar uma consulta entre partições que inclui classificação (ORDER BY e/ou TOP), o SDK do Banco de Dados de Documentos emite a consulta paralelamente entre partições e mescla os resultados parcialmente classificados no lado do cliente para produzir resultados ordenados globalmente.

### <a name="executing-stored-procedures"></a>Executando procedimentos armazenados
Você também poderá executar transações atômicas em documentos com a mesma ID de dispositivo, por exemplo, se você estiver mantendo agregações ou o estado mais recente de um dispositivo em um único documento. 

    await client.ExecuteStoredProcedureAsync<DeviceReading>(
        UriFactory.CreateStoredProcedureUri("db", "coll", "SetLatestStateAcrossReadings"),
        new RequestOptions { PartitionKey = new PartitionKey("XMS-001") }, 
        "XMS-001-FE24C");

Na próxima seção, examinaremos como é possível passar de coleções de partição única para coleções particionadas.

<a name="migrating-from-single-partition"></a>

## <a name="migrating-from-single-partition-to-partitioned-collections"></a>Migração de coleções de partição única para coleções particionadas
Quando um aplicativo usando uma coleção de partição única precisar de maior produtividade (mais de&10;.000 RU/s) ou maior armazenamento de dados (mais de&10; GB), você poderá usar a [Ferramenta de Migração de Dados do DocumentDB](http://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) para migrar os dados da coleção de partição única para uma coleção particionada. 

Para migrar de uma coleção de partição única para uma coleção particionada

1. Exporte dados da coleção de partição única para JSON. Consulte [Exportar para arquivo JSON](documentdb-import-data.md#export-to-json-file) para obter detalhes adicionais.
2. Importe os dados para uma coleção particionada criada com uma definição de chave de partição e produtividade de mais de 2.500 unidades de solicitação por segundo, conforme mostrado no exemplo a seguir. Consulte [Importar para DocumentDB](documentdb-import-data.md#DocumentDBSeqTarget) para obter detalhes adicionais.

![Migrando Dados para uma Coleção particionada no Banco de Dados de Documentos][3]  

> [!TIP]
> Para tempos de importação mais rápidos, considere aumentar o Número de Solicitações Paralelas para 100 ou mais para aproveitar a produtividade mais alta disponível para coleções particionadas. 
> 
> 

Agora que concluímos as noções básicas, vejamos algumas considerações de design importantes ao trabalhar com chaves de partição no Banco de Dados de Documentos.

## <a name="designing-for-partitioning"></a>Design de particionamento
A escolha da chave de partição é uma decisão importante que você precisará fazer no momento do design. Esta seção descreve algumas das compensações envolvidas na seleção de uma chave de partição para a coleção.

### <a name="partition-key-as-the-transaction-boundary"></a>Chave de partição como o limite de transação
Sua escolha de chave de partição deve equilibrar a necessidade de habilitar o uso de transações em relação à necessidade de distribuir suas entidades por várias chaves de partição para garantir uma solução escalonável. Por um lado, você pode definir a mesma chave de partição para todos os seus documentos, mas isso pode limitar a escalabilidade da solução. Por outro lado, você pode atribuir uma chave de partição exclusiva para cada documento, o que seria altamente escalonável, mas impediria o uso de transações entre documentos por meio de procedimentos e gatilhos armazenados. Uma chave de partição ideal é aquela que permite o uso de consultas eficientes, e que tenha cardinalidade suficiente para garantir que sua solução seja escalonável. 

### <a name="avoiding-storage-and-performance-bottlenecks"></a>Evitando gargalos de armazenamento e desempenho
Também é importante escolher uma propriedade que permita que as gravações sejam distribuídas entre vários de valores distintos. Solicitações para a mesma chave de partição não podem exceder a produtividade de uma única partição e serão limitadas. Portanto, é importante escolher uma chave de partição que não resulte em **"pontos de acesso"** dentro de seu aplicativo. Como todos os dados de uma única chave de partição devem ser armazenados em uma partição, também é recomendável evitar chaves de partição com grandes volumes de dados para o mesmo valor. 

### <a name="examples-of-good-partition-keys"></a>Exemplos de boas chaves de partição
Aqui estão alguns exemplos de como escolher a chave de partição para seu aplicativo:

* Se você estiver implementando um perfil do usuário de back-end, a ID de usuário será uma boa opção para a chave de partição.
* Se você estiver armazenando dados de IoT, por exemplo, o estado do dispositivo, uma ID do dispositivo será uma boa opção para a chave de partição.
* Se você estiver usando o Banco de Dados de Documentos para registrar em log dados de série temporal, a ID do processo ou o nome do host será uma boa opção para a chave de partição.
* Se você tiver uma arquitetura de multilocatário, a ID do locatário será uma boa opção para a chave de partição.

Observe que em alguns casos de uso (como os perfis de usuário e IoT descritos acima), a chave de partição pode ser igual à (chave de documento). Em outros, como os dados de série temporal, você pode ter uma chave de partição que é diferente da ID.

### <a name="partitioning-and-loggingtime-series-data"></a>Dados de particionamento e registro em log/da série temporal
Um dos casos de uso mais comuns do Banco de Dados de Documentos é para registro em log e telemetria. É importante escolher uma boa chave de partição, já que você pode precisar ler/gravar enormes volumes de dados. A escolha dependerá das suas taxas de leitura e gravação e dos tipos de consultas que você pretende executar. Aqui estão algumas dicas sobre como escolher uma boa chave de partição.

* Se o seu caso de uso envolve uma pequena taxa de gravações se acumulando por um longo período de tempo e necessidade de consulta por intervalos de carimbos de data/hora e outros filtros, usar um rollup do carimbo de data/hora, por exemplo, data como uma chave de partição, é uma boa abordagem. Isso permite que você consulte todos os dados para uma data de uma única partição. 
* Se sua carga de trabalho for pesada quanto à gravação, o que é geralmente mais comum, você deverá usar uma chave de partição que não esteja baseada em carimbo de data/hora para que o Banco de Dados de Documentos possa distribuir gravações uniformemente por várias partições. Aqui, um nome do host, ID do processo, ID da atividade ou outra propriedade com alta cardinalidade é uma boa opção. 
* Uma terceira abordagem é híbrida, em que você tem várias coleções, um para cada dia/mês e a chave de partição é uma propriedade granular, como nome do host. Isso tem o benefício de permitir que você defina diferentes produtividades com base na janela de tempo, por exemplo, a coleção para o mês atual é provisionada com uma produtividade maior porque ela só atua para operações de leitura e gravação, enquanto aquelas para os meses anteriores são provisionadas com produtividade menor por atuarem apenas para operações de leitura.

### <a name="partitioning-and-multi-tenancy"></a>Particionamento e multilocação
Se você estiver implementando um aplicativo multilocatário usando o Banco de Dados de Documentos, haverá dois padrões principais para a implementação de locação com o Banco de Dados de Documentos: uma chave de partição por locatário e uma coleção por locatário. Aqui estão os prós e contras de cada um:

* Uma chave de partição por locatário: nesse modelo, os locatários são colocados em uma única coleção. Mas, consultas e inserções de documentos de um único locatário podem ser executadas em relação a uma única partição. Você também pode implementar a lógica transacional em todos os documentos dentro de um locatário. Como vários locatários compartilham uma coleção, você pode economizar os custos de armazenamento e produtividade realizando o pool de recursos para locatários em uma única coleção em vez de provisionando de capacidade extra para cada locatário. A desvantagem é que você não tem o isolamento de desempenho por locatário. Aumentos de desempenho/produtividade se aplicam a toda a coleção vs aumentos direcionados para locatários.
* Uma coleção por locatário: cada locatário tem sua própria coleção. Nesse modelo, você pode reservar o desempenho por locatário. Com novo consumo com base em modelo de preços do Banco de Dados de Documentos, esse modelo é mais econômico para aplicativos multilocatários com um número pequeno de locatários.

Você também pode usar uma abordagem de combinação/em camadas que coloca os locatários pequenos juntos e migra os locatários maiores para sua própria coleção.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, descrevemos como o particionamento funciona no Banco de Dados de Documentos do Azure, como você pode criar coleções particionadas e como pode escolher uma chave de partição boa para seu aplicativo. 

* Execute testes de desempenho e escalabilidade com o Banco de Dados de Documentos. Consulte [Teste de Desempenho e Escada com o DocumentDB do Azure](documentdb-performance-testing.md) para obter um exemplo.
* Introdução à codificação com os [SDKs](documentdb-sdk-dotnet.md) ou a [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx)
* Saiba mais sobre a [produtividade provisionada no DocumentDB](documentdb-performance-levels.md)

[1]: ./media/documentdb-partition-data/partitioning.png
[2]: ./media/documentdb-partition-data/single-and-partitioned.png
[3]: ./media/documentdb-partition-data/documentdb-migration-partitioned-collection.png  





<!--HONumber=Feb17_HO2-->


