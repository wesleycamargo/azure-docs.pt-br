---
title: Métricas do Armazenamento do Azure no Azure Monitor | Microsoft Docs
description: Saiba mais sobre as novas métricas oferecidas pelo Azure Monitor.
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 09/05/2017
ms.author: fryu
ms.openlocfilehash: 5316013631670ab3612e441e64e2f330f01941b7
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2018
---
# <a name="azure-storage-metrics-in-azure-monitor-preview"></a>Métricas do Armazenamento do Azure no Azure Monitor (visualização)

Com as métricas do Armazenamento do Azure, você pode analisar tendências de uso, rastrear solicitações e diagnosticar problemas com a sua conta de armazenamento.

O Azure Monitor fornece interfaces de usuário unificadas para monitoramento em diferentes serviços do Azure. Para saber mais, consulte [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md). O Armazenamento do Azure integra-se ao Azure Monitor, enviando dados de métrica para a plataforma do Azure Monitor.

## <a name="access-metrics"></a>Métricas de acesso

O Azure Monitor fornece várias maneiras de acessar as métricas. Você pode acessá-las a partir do [portal do Azure](https://portal.azure.com), das APIs do Azure Monitor (REST e .NET) e de soluções de análise como o Operation Management Suite e Hubs de Eventos. Para saber mais, consulte [Métricas do Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

As métricas estão habilitadas por padrão e você pode acessar os dados dos últimos 30 dias. Se você precisar manter os dados por um período de tempo maior, você pode arquivar os dados de métrica em uma conta de Armazenamento do Azure. Isso pode ser configurado em [configurações de diagnóstico](../../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) no Azure Monitor.

### <a name="access-metrics-in-the-azure-portal"></a>Acessar as métricas no portal do Azure

Você pode monitorar as métricas ao longo do tempo no portal do Azure. O exemplo a seguir mostra como exibir **UsedCapacity** no nível da conta.

![captura de tela de acesso às métricas no portal do Azure](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

Para métricas com suporte para dimensões, você deve filtrar pelo valor da dimensão desejado. O exemplo a seguir que mostra como exibir **Transações** no nível de conta com uma resposta de **Sucesso**.

![captura de tela de acesso às métricas com dimensão no portal do Azure](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Acessar métricas com a API REST

O Azure Monitor fornece [APIs REST](/rest/api/monitor/) para ler valores e definição de métricas. Esta seção mostra como ler as métricas de armazenamento. A ID do recurso é usada em todas as APIs REST. Para obter mais informações, leia [Noções básicas sobre o ID de recurso para serviços no Armazenamento](#understanding-resource-id-for-services-in-storage).

O exemplo a seguir mostra como usar [ArmClient](https://github.com/projectkudu/ARMClient) na linha de comando para simplificar o teste com a API REST.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Listar a definição de métrica no nível conta com a API REST

O exemplo a seguir mostra como listar a definição de métrica no nível da conta:

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2017-05-01-preview

```

Se você deseja listar as definições de métrica para o blob, tabela, arquivo ou fila, você deve especificar IDs de recurso diferentes para cada serviço com a API.

A resposta contém a definição da métrica no formato JSON:

```Json
{
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions/UsedCapacity",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
      "category": "Capacity",
      "name": {
        "value": "UsedCapacity",
        "localizedValue": "Used capacity"
      },
      "isDimensionRequired": false,
      "unit": "Bytes",
      "primaryAggregationType": "Average",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D"
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D"
        }
      ]
    },
    ... next metric definition
  ]
}

```

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Ler os valores de métrica no nível conta com a API REST

O exemplo a seguir mostra como ler os dados de métrica no nível da conta:

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metric=Availability&api-version=2017-05-01-preview&aggregation=Average&interval=PT1H"

```

No exemplo acima, se você deseja ler os valores de métrica para o blob, tabela, arquivo ou fila, você deve especificar IDs de recurso diferentes para cada serviço com a API.

A resposta a seguir contém os valores da métrica no formato JSON:

```Json
{
  "cost": 0,
  "timespan": "2017-09-07T17:27:41Z/2017-09-07T18:27:41Z",
  "interval": "PT1H",
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/Microsoft.Insights/metrics/Availability",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Availability",
        "localizedValue": "Availability"
      },
      "unit": "Percent",
      "timeseries": [
        {
          "metadatavalues": [],
          "data": [
            {
              "timeStamp": "2017-09-07T17:27:00Z",
              "average": 100.0
            }
          ]
        }
      ]
    }
  ]
}

```

### <a name="access-metrics-with-the-net-sdk"></a>Acessar métricas com o SDK do .Net

O Azure Monitor fornece [SDK do .Net](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) para ler valores e definição de métricas. O [código de exemplo](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) mostra como usar o SDK com parâmetros diferentes. Você precisa usar `0.18.0-preview` ou versão posterior para métricas de armazenamento. A ID do recurso é usada no SDK do .Net. Para obter mais informações, leia [Noções básicas sobre o ID de recurso para serviços no Armazenamento](#understanding-resource-id-for-services-in-storage).

O exemplo a seguir mostra como usar o SDK do .Net do Azure Monitor para ler a métrica de armazenamento.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>Listar a definição de métrica no nível conta com o SDK do .Net

O exemplo a seguir mostra como listar a definição de métrica no nível da conta:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        //How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        Using metrics in Azure Monitor is currently free. However, if you use additional solutions ingesting metrics data, you may be billed by these solutions. For example, you are billed by Azure Storage if you archive metrics data to an Azure Storage account. Or you are billed by Operation Management Suite (OMS) if you stream metrics data to OMS for advanced analysis.
        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            //Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

Se você deseja listar as definições de métrica para o blob, tabela, arquivo ou fila, você deve especificar IDs de recurso diferentes para cada serviço com a API.

#### <a name="read-metric-values-with-the-net-sdk"></a>Ler os valores de métrica com o SDK do .Net

O exemplo a seguir mostra como ler os dados `UsedCapacity` no nível da conta:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        //How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToString("o");
        string endDate = DateTime.Now.ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metric: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

No exemplo acima, se você deseja ler os valores de métrica para o blob, tabela, arquivo ou fila, você deve especificar IDs de recurso diferentes para cada serviço com a API.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>Ler os valores da métrica multidimensionais com o SDK do .Net

Para métricas multidimensionais, você precisa definir o filtro de metadados se você quiser ler dados da métrica no valor de dimensão específica.

O exemplo a seguir mostra como ler dados da métrica na métrica que suportam várias dimensões:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "{SubscriptionID}";
        //How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToString("o");
        string endDate = DateTime.Now.ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metric: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Noções básicas sobre a ID de recurso para serviços no Armazenamento do Azure

A ID de recurso é um identificador exclusivo de um recurso no Azure. Quando você usa a API REST do Azure Monitor para ler definições ou valores de métricas, você deve usar a ID de recurso para o recurso no qual você pretende operar. O modelo de ID de recurso segue este formato:

`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
`

O Armazenamento fornece métricas no nível da conta de armazenamento e no nível de serviço com o Azure Monitor. Por exemplo, você pode recuperar as métricas somente para armazenamento de Blob. Cada nível tem sua própria ID de recurso, que é usada para recuperar as métricas somente para aquele nível.

### <a name="resource-id-for-a-storage-account"></a>ID de recurso para uma conta de armazenamento

O exemplo a seguir mostra o formato para especificar a ID de recurso para uma conta de armazenamento.

`
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
`

### <a name="resource-id-for-the-storage-services"></a>ID de recurso para os serviços de armazenamento

O exemplo a seguir mostra o formato para especificar a ID de recurso para cada um dos serviços de armazenamento.

* ID de recurso do serviço Blob `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
`
* ID de recurso do serviço Tabela `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
`
* ID de recurso do serviço Fila `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
`
* ID de recurso do serviço Arquivo `
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
`

### <a name="resource-id-in-azure-monitor-rest-api"></a>ID de recurso na API REST do Azure Monitor

O exemplo a seguir mostra o padrão usado ao chamar a API REST do Azure Monitor.

`
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
`

## <a name="capacity-metrics"></a>Métricas de capacidade
Os valores de métricas de capacidade são enviados para o Azure Monitor a cada hora. Os valores são atualizados diariamente. O intervalo de agregação define o intervalo de tempo para o qual os valores das métricas são apresentados. O intervalo de agregação compatível com todas as métricas de capacidade é uma hora (PT1H).

O Armazenamento do Azure fornece as seguintes métricas de capacidade no Azure Monitor.

### <a name="account-level"></a>Nível de conta

| Nome da métrica | DESCRIÇÃO |
| ------------------- | ----------------- |
| UsedCapacity | A quantidade de armazenamento utilizada pela conta de armazenamento. Para contas de armazenamento Standard, é a soma da capacidade usada por blob, tabela, arquivo e fila. Para contas de armazenamento Premium e contas de armazenamento de Blob, é o mesmo que Capacidade de Blob. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

### <a name="blob-storage"></a>Armazenamento de blob

| Nome da métrica | DESCRIÇÃO |
| ------------------- | ----------------- |
| BlobCapacity | O total de armazenamento de Blob usado na conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 <br/> Dimensão: BlobType ([Definição](#metrics-dimensions)) |
| BlobCount    | O número de objetos blob armazenados na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 <br/> Dimensão: BlobType ([Definição](#metrics-dimensions)) |
| ContainerCount    | O número de contêineres na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

### <a name="table-storage"></a>Armazenamento de tabela

| Nome da métrica | DESCRIÇÃO |
| ------------------- | ----------------- |
| TableCapacity | A quantidade de armazenamento de Tabelas usada pela conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| TableCount   | O número de tabelas em uma conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| TableEntityCount | O número de entidades de tabela na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

### <a name="queue-storage"></a>Armazenamento de filas

| Nome da métrica | DESCRIÇÃO |
| ------------------- | ----------------- |
| QueueCapacity | A quantidade de armazenamento de fila usada pela conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| QueueCount   | O número de filas em uma conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| QueueMessageCount | O número de mensagens da fila não expiradas na conta de armazenamento. <br/><br/>Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

### <a name="file-storage"></a>Armazenamento de arquivos

| Nome da métrica | DESCRIÇÃO |
| ------------------- | ----------------- |
| FileCapacity | A quantidade de armazenamento de Arquivos usada pela conta de armazenamento. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| FileCount   | O número de arquivos na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |
| FileShareCount | O número de compartilhamentos de arquivos na conta de armazenamento. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Média <br/> Exemplo de valor: 1024 |

## <a name="transaction-metrics"></a>Métricas de transação

As métricas de transação são enviadas do Armazenamento do Azure para o Azure Monitor a cada minuto. Todas as métricas de transação estão disponíveis no nível de conta e de serviço (armazenamento de Blob, armazenamento de Tabelas, Azure Files e armazenamento de fila). O intervalo de agregação define o intervalo de tempo para o qual os valores das métricas são apresentados. Os intervalos de agregação para todas as métricas de transação são PT1H e PT1M.

O Armazenamento do Azure fornece as seguintes métricas de transação no Azure Monitor.

| Nome da métrica | DESCRIÇÃO |
| ------------------- | ----------------- |
| Transações | O número de solicitações feitas a um serviço de armazenamento ou à operação de API especificada. Esse número inclui solicitações bem-sucedidas e com falha, bem como solicitações que produziram erros. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensões aplicáveis: ResponseType, GeoType, ApiName ([Definição](#metrics-dimensions))<br/> Exemplo de valor: 1024 |
| Entrada | A quantidade de dados de entrada. Esse número inclui a entrada de um cliente externo no Armazenamento do Azure, bem como a entrada no Azure. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensões aplicáveis: GeoType, ApiName ([Definição](#metrics-dimensions)) <br/> Exemplo de valor: 1024 |
| Saída | A quantidade de dados de saída. Esse número inclui a saída de um cliente externo no Armazenamento do Azure, bem como a saída no Azure. Como resultado, esse número não reflete a saída faturável. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensões aplicáveis: GeoType, ApiName ([Definição](#metrics-dimensions)) <br/> Exemplo de valor: 1024 |
| SuccessServerLatency | O tempo médio usado para processar uma solicitação bem-sucedida pelo Armazenamento do Azure. Esse valor não inclui a latência de rede especificada em SuccessE2ELatency. <br/><br/> Unidade: Milissegundos <br/> Tipo de agregação: Média <br/> Dimensões aplicáveis: GeoType, ApiName ([Definição](#metrics-dimensions)) <br/> Exemplo de valor: 1024 |
| SuccessE2ELatency | A latência média de ponta a ponta das solicitações bem-sucedidas feitas a um serviço de armazenamento ou à operação de API especificada. Esse valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler a solicitação, enviar a resposta e receber a confirmação da resposta. <br/><br/> Unidade: Milissegundos <br/> Tipo de agregação: Média <br/> Dimensões aplicáveis: GeoType, ApiName ([Definição](#metrics-dimensions)) <br/> Exemplo de valor: 1024 |
| Disponibilidade | O percentual de disponibilidade para o serviço de armazenamento ou a operação de API especificada. A disponibilidade é calculada considerando o valor total de solicitações faturáveis e dividindo-o pelo número de solicitações aplicáveis, incluindo as que produziram erros inesperados. Todos os erros inesperados resultam na redução da disponibilidade para o serviço de armazenamento ou a operação de API especificada. <br/><br/> Unidade: Porcentagem <br/> Tipo de agregação: Média <br/> Dimensões aplicáveis: GeoType, ApiName ([Definição](#metrics-dimensions)) <br/> Exemplo de valor: 99.99 |

## <a name="metrics-dimensions"></a>Dimensões das métricas

O Armazenamento do Azure oferece suporte às seguintes dimensões para métricas no Azure Monitor.

| Nome da dimensão | DESCRIÇÃO |
| ------------------- | ----------------- |
| BlobType | O tipo de blob somente para métricas de Blob. Os valores suportados são **BlockBlob** e **PageBlob**. Acrescentar Blob está incluído no BlockBlob. |
| ResponseType | Tipo de resposta da transação. Os valores disponíveis incluem: <br/><br/> <li>ServerOtherError: Todos os outros erros do lado do servidor, exceto aqueles descritos </li> <li> ServerBusyError: Solicitação autenticada que retornou um código de status HTTP 503. </li> <li> ServerTimeoutError: Solicitação autenticada que ultrapassou o tempo limite e retornou um código de status HTTP 500. O tempo limite foi ultrapassado devido a um erro no servidor. </li> <li> AuthorizationError: Solicitação autenticada que falhou devido a um acesso não autorizado de dados ou a uma falha de autorização. </li> <li> NetworkError: Solicitação autenticada que falhou devido a erros de rede. Geralmente ocorre quando um cliente fecha prematuramente uma conexão antes da expiração do tempo limite. </li> <li>    ClientThrottlingError: Erro de limitação do lado do cliente. </li> <li> ClientTimeoutError: Solicitação autenticada que ultrapassou o tempo limite e retornou um código de status HTTP 500. Se o tempo limite de rede do cliente ou o tempo limite da solicitação for definido como um valor menor do que o esperado pelo serviço de armazenamento, ele é considerado um tempo limite esperado. Caso contrário, ele será relatado como um ServerTimeoutError. </li> <li> ClientOtherError: Todos os outros erros do lado do cliente, exceto aqueles descritos. </li> <li> Success: Solicitação bem-sucedida|
| GeoType | Transação de cluster primário ou secundário. Os valores disponíveis incluem Primário e Secundário. Aplica-se ao acesso de leitura ao armazenamento com redundância geográfica (RA-GRS) ao ler objetos de um locatário secundário. |
| ApiName | O nome da operação. Por exemplo:  <br/> <li>CreateContainer</li> <li>DeleteBlob</li> <li>GetBlob</li> Para saber todos os nomes de operação, consulte [documento](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages#logged-operations.md). |

Para saber as dimensões de métricas com suporte, você precisa especificar o valor da dimensão para exibir os valores correspondentes das métricas. Por exemplo, se você pesquisar o valor de **Transações** para respostas com sucesso, você precisa filtrar a dimensão **ResponseType** por **Sucesso**. Ou se você pesquisar o valor de **BlobCount** para o Blob de Blocos, você precisa filtrar a dimensão **BlobType** por **BlockBlob**.

## <a name="service-continuity-of-legacy-metrics"></a>Continuidade do serviço de métricas herdadas

As métricas herdadas estão disponíveis paralelamente às métricas gerenciadas do Azure Monitor. O suporte é o mesmo até que o Armazenamento do Azure conclua o serviço nas métricas herdados.

## <a name="next-steps"></a>Próximas etapas

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
