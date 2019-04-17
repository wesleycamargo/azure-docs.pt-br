---
title: 'Tutorial: Ingerir dados de diagnóstico e de log de atividades no Azure Data Explorer sem uma linha de código'
description: Neste tutorial, você aprenderá a ingerir dados no Azure Data Explorer sem uma linha de código e a consultar esses dados.
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: tutorial
ms.date: 04/07/2019
ms.openlocfilehash: 9f4b7ee0dcc87ca03fd051be0dacedf0912b5320
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59262900"
---
# <a name="tutorial-ingest-data-in-azure-data-explorer-without-one-line-of-code"></a>Tutorial: Ingerir dados no Azure Data Explorer sem uma linha de código

Este tutorial ensinará você a ingerir dados dos logs de diagnóstico e de atividades em um cluster do Azure Data Explorer sem nenhuma codificação. Com esse método de ingestão simples, você poderá começar a consultar o Azure Data Explorer rapidamente para análise de dados.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Crie tabelas e mapeamento de ingestão em um banco de dados do Azure Data Explorer.
> * Formate os dados ingeridos usando uma política de atualização.
> * Crie um [hub de eventos](/azure/event-hubs/event-hubs-about) e conecte-o ao Azure Data Explorer.
> * Transmita dados para um hub de eventos dos [logs de diagnóstico do Azure Monitor](/azure/azure-monitor/platform/diagnostic-logs-overview) e dos [logs de atividades do Azure Monitor](/azure/azure-monitor/platform/activity-logs-overview).
> * Consulte os dados ingeridos usando o Azure Data Explorer.

> [!NOTE]
> Crie todos os recursos na mesma localização ou região do Azure. Esse é um requisito dos logs de diagnóstico do Azure Monitor.

## <a name="prerequisites"></a>Pré-requisitos

* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* [Um cluster e um banco de dados do Azure Data Explorer](create-cluster-database-portal.md). Neste tutorial, o nome do banco de dados é *TestDatabase*.

## <a name="azure-monitor-data-provider-diagnostic-and-activity-logs"></a>Provedor de dados do Azure Monitor: logs de diagnóstico e de atividades

Exiba e compreenda os dados fornecidos pelos logs de diagnóstico e de atividades do Azure Monitor abaixo. Criaremos um pipeline de ingestão com base nesses esquemas de dados. Observe que cada evento em um log tem uma matriz de registros. Essa matriz de registros será dividida adiante no tutorial.

### <a name="diagnostic-logs-example"></a>Exemplo de logs de diagnóstico

Os logs de diagnóstico do Azure são métricas emitidas por um serviço do Azure que fornecem dados sobre a operação desse serviço. Os dados são agregados com um intervalo de agregação de 1 minuto. Este é um exemplo de um esquema de evento de métrica do Azure Data Explorer sobre a duração da consulta:

```json
{
    "records": [
    {
        "count": 14,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/F3101802-8C4F-4E6E-819C-A3B5794D33DD/RESOURCEGROUPS/KEDAMARI/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/KEREN",
        "time": "2018-12-20T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    },
    {
        "count": 12,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/F3101802-8C4F-4E6E-819C-A3B5794D33DD/RESOURCEGROUPS/KEDAMARI/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/KEREN",
        "time": "2018-12-21T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    }
    ]
}
```

### <a name="activity-logs-example"></a>Exemplo de logs de atividades

Os logs de atividades do Azure são logs de nível de assinatura que fornecem insight sobre as operações executadas nos recursos em sua assinatura. Este é um exemplo de um evento do log de atividades para verificar o acesso:

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Start",
        "resultSignature": "Started.",
        "durationMs": 0,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            ...
        }
    },
    {
        "time": "2018-12-26T16:23:06.3040244Z",
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Success",
        "resultSignature": "Succeeded.OK",
        "durationMs": 194,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            "statusCode": "OK",
            "serviceRequestId": "87acdebc-945f-4c0c-b931-03050e085626"
        }
    }]
}
```

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>Configurar um pipeline de ingestão no Azure Data Explorer

A configuração de um pipeline do Azure Data Explorer envolve várias etapas, como [criação de tabela e ingestão de dados](/azure/data-explorer/ingest-sample-data#ingest-data). Também é possível manipular, mapear e atualizar os dados.

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>Conectar-se à IU da Web do Azure Data Explorer

No banco de dados *TestDatabase* do Azure Data Explorer, selecione **Consulta** para abrir a IU da Web do Azure Data Explorer.

![Página Consulta](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>Criar as tabelas de destino

A estrutura dos logs do Azure Monitor não é tabular. Você manipulará os dados e expandirá cada evento para um ou mais registros. Os dados brutos serão ingeridos a uma tabela intermediária denominada *ActivityLogsRawRecords* para os logs de atividade e *DiagnosticLogsRawRecords* para logs de diagnóstico. Nesse momento, os dados serão manipulados e expandidos. Usando uma política de atualização, os dados expandidos serão, em seguida, ingeridos na tabela *ActivityLogsRecords* para logs de atividade e *DiagnosticLogsRecords* para logs de diagnóstico. Isso significa que você precisará criar duas tabelas separadas para os logs de atividade de ingestão e duas tabelas separadas para a ingestão dos logs de diagnóstico.

Use a IU da Web do Azure Data Explorer para criar as tabelas de destino no banco de dados do Azure Data Explorer.

#### <a name="the-diagnostic-logs-table"></a>A tabela dos logs de diagnóstico

1. No banco de dados *TestDatabase*, crie uma tabela chamada *DiagnosticLogsRecords* para armazenar os registros do log de diagnóstico. Use o seguinte comando de controle `.create table`:

    ```kusto
    .create table DiagnosticLogsRecords (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Selecione **Executar** para criar a tabela.

    ![Executar consulta](media/ingest-data-no-code/run-query.png)

1. Crie a tabela de dados intermediários denominada *DiagnosticLogsRawRecords* no banco de dados *TestDatabase* para manipulação de dados usando a consulta a seguir. Selecione **Executar** para criar a tabela.

    ```kusto
    .create table DiagnosticLogsRawRecords (Records:dynamic)
    ```

#### <a name="the-activity-logs-tables"></a>As tabelas dos logs de atividades

1. Crie uma tabela chamada *ActivityLogsRecords* no banco de dados *TestDatabase* para receber os registros do log de atividades. Para criar a tabela, execute a seguinte consulta do Azure Data Explorer:

    ```kusto
    .create table ActivityLogsRecords (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Crie a tabela de dados intermediária chamada *ActivityLogsRawRecords* no banco de dados *TestDatabase* para manipulação de dados:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

<!--
     ```kusto
     .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    <[Retention](/azure/kusto/management/retention-policy) for an intermediate data table is set at zero retention policy.
-->

### <a name="create-table-mappings"></a>Criar mapeamentos de tabela

 Como o formato de dados é `json`, o mapeamento de dados é necessário. O mapeamento `json` mapeia cada caminho JSON para um nome de coluna de tabela.

#### <a name="table-mapping-for-diagnostic-logs"></a>Mapeamento de tabela para os logs de diagnóstico

Para mapear os dados dos logs de diagnóstico para a tabela, use a seguinte consulta:

```kusto
.create table DiagnosticLogsRawRecords ingestion json mapping 'DiagnosticLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

#### <a name="table-mapping-for-activity-logs"></a>Mapeamento de tabela para os logs de atividades

Para mapear os dados dos logs de atividades para a tabela, use a seguinte consulta:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

### <a name="create-the-update-policy-for-log-data"></a>Criar a política de atualização para os dados dos logs

#### <a name="activity-log-data-update-policy"></a>Política de atualização dos dados de log de atividade

1. Crie uma [função](/azure/kusto/management/functions) que expande a coleção de registros de log de atividades de forma que cada valor na coleção receba uma linha separada. Use o operador [`mv-expand`](/azure/kusto/query/mvexpandoperator):

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events["time"]),
            ResourceId = tostring(events["resourceId"]),
            OperationName = tostring(events["operationName"]),
            Category = tostring(events["category"]),
            ResultType = tostring(events["resultType"]),
            ResultSignature = tostring(events["resultSignature"]),
            DurationMs = toint(events["durationMs"]),
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events["location"]),
            Level = tostring(events["level"])
    }
    ```

2. Adicione a [política de atualização](/azure/kusto/concepts/updatepolicy) à tabela de destino. Essa política executará a consulta automaticamente nos dados recém-ingeridos na tabela de dados intermediária *ActivityLogsRawRecords* e ingerirá os resultados na tabela *ActivityLogsRecords*:

    ```kusto
    .alter table ActivityLogsRecords policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

#### <a name="diagnostic-log-data-update-policy"></a>Política de atualização de dados de log de diagnóstico

1. Crie uma [função](/azure/kusto/management/functions) que expande a coleção dos registros do log de diagnóstico de forma que cada valor na coleção receba uma linha separada. Use o operador [`mv-expand`](/azure/kusto/query/mvexpandoperator):
     ```kusto
    .create function DiagnosticLogRecordsExpand() {
        DiagnosticLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events["time"]),
            ResourceId = tostring(events["resourceId"]),
            MetricName = tostring(events["metricName"]),
            Count = toint(events["count"]),
            Total = todouble(events["total"]),
            Minimum = todouble(events["minimum"]),
            Maximum = todouble(events["maximum"]),
            Average = todouble(events["average"]),
            TimeGrain = tostring(events["timeGrain"])
    }
    ```

2. Adicione a [política de atualização](/azure/kusto/concepts/updatepolicy) à tabela de destino. Essa política executará a consulta automaticamente nos dados recém-ingeridos na tabela de dados intermediária *DiagnosticLogsRawRecords* e ingerirá os resultados na tabela *DiagnosticLogsRecords*:

    ```kusto
    .alter table DiagnosticLogsRecords policy update @'[{"Source": "DiagnosticLogsRawRecords", "Query": "DiagnosticLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

## <a name="create-an-azure-event-hubs-namespace"></a>Criar um namespace de Hubs de Eventos do Azure

Os logs de diagnóstico do Azure permitem a exportação de métricas para uma conta de armazenamento ou um hub de eventos. Neste tutorial, rotearemos as métricas por meio de um hub de eventos. Você criará um namespace dos Hubs de Eventos e um hub de eventos para os logs de diagnóstico nas etapas a seguir. O Azure Monitor criará o hub de eventos *insights-operational-logs* para os logs de atividades.

1. Crie um hub de eventos usando um modelo do Azure Resource Manager no portal do Azure. Para seguir o restante das etapas deste artigo, clique com o botão direito do mouse no botão **Implantar no Azure** e, em seguida, selecione **Abrir em uma nova janela**. O botão **Implantar no Azure** leva você ao portal do Azure.

    [![DBotão Implantar no Azure](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Crie um namespace dos Hubs de Eventos e um hub de eventos para os logs de diagnóstico.

    ![Criação do hub de eventos](media/ingest-data-no-code/event-hub.png)

1. Preencha o formulário com as seguintes informações. Para as configurações não listadas na tabela a seguir, use os valores padrão.

    **Configuração** | **Valor sugerido** | **DESCRIÇÃO**
    |---|---|---|
    | **Assinatura** | *Sua assinatura* | Selecione a assinatura do Azure que você deseja usar para o seu hub de eventos.|
    | **Grupo de recursos** | *test-resource-group* | Crie um novo grupo de recursos. |
    | **Local padrão** | Selecione a região que melhor atende às suas necessidades. | Crie o namespace dos Hubs de Eventos na mesma localização dos outros recursos.
    | **Nome do Namespace** | *AzureMonitoringData* | Escolha um nome exclusivo que identifique seu namespace.
    | **Nome do Hub de Eventos** | *DiagnosticLogsData* | O hub de eventos fica sob o namespace, que fornece um contêiner de determinação de escopo exclusivo. |
    | **Nome do grupo de consumidor** | *adxpipeline* | Crie um nome de grupo de consumidores. Grupos de consumidores permitem que vários aplicativos de consumo tenham uma visão separada do fluxo de eventos. |
    | | |

## <a name="connect-azure-monitor-logs-to-your-event-hub"></a>Conectar os logs do Azure Monitor ao hub de eventos

Agora, você precisa conectar os logs de diagnóstico e os logs de atividades ao hub de eventos.

### <a name="connect-diagnostic-logs-to-your-event-hub"></a>Conectar os logs de diagnóstico ao hub de eventos

Selecione um recurso do qual exportar métricas. Vários tipos de recurso dão suporte à exportação dos logs de diagnóstico, incluindo o namespace dos Hubs de Eventos, o Azure Key Vault, o Hub IoT do Azure e clusters do Azure Data Explorer. Neste tutorial, usaremos um cluster do Azure Data Explorer como nosso recurso.

1. Selecione o cluster Kusto no portal do Azure.
1. Selecione **Configurações de diagnóstico** e, em seguida, selecione o link **Ativar diagnóstico**. 

    ![Configurações de Diagnóstico](media/ingest-data-no-code/diagnostic-settings.png)

1. O painel **Configurações de diagnóstico** será aberto. Siga estas etapas:
   1. Dê aos dados de log de diagnóstico o nome *ADXExportedData*.
   1. Em **MÉTRICA**, marque a caixa de seleção **AllMetrics** (opcional).
   1. Marque a caixa de seleção **Transmitir para um hub de eventos**.
   1. Selecione **Configurar**.

      ![Painel Configurações de diagnóstico](media/ingest-data-no-code/diagnostic-settings-window.png)

1. No painel **Selecionar um hub de eventos**, configure como exportar os dados dos logs de diagnóstico para o hub de eventos criado:
    1. Na lista **Selecionar um namespace do hub de eventos**, selecione *AzureMonitoringData*.
    1. Na lista **Selecionar o nome do hub de evento**, selecione *diagnosticlogsdata*.
    1. Na lista **Selecionar o nome da política do hub de eventos**, selecione **RootManagerSharedAccessKey**.
    1. Selecione **OK**.

1. Clique em **Salvar**.

### <a name="connect-activity-logs-to-your-event-hub"></a>Conectar os logs de atividades ao hub de eventos

1. No menu à esquerda do portal do Azure, selecione **Log de atividades**.
1. A janela **Log de atividades** será aberta. Selecione **Exportar para o Hub de Eventos**.

    ![Janela Log de atividades](media/ingest-data-no-code/activity-log.png)

1. A janela **Exportar log de atividades** será aberta:
 
    ![Janela Exportar log de atividades](media/ingest-data-no-code/export-activity-log.png)

1. Na janela **Exportar log de atividades**, execute as seguintes etapas:
      1. Selecione sua assinatura.
      1. Na lista **Regiões**, escolha **Selecionar tudo**.
      1. Marque a caixa de seleção **Exportar para um hub de eventos**.
      1. Escolha **Selecionar um namespace de barramento de serviço** para abrir o painel **Selecionar um hub de eventos**.
      1. No painel **Selecionar um hub de eventos**, selecione sua assinatura.
      1. Na lista **Selecionar um namespace do hub de eventos**, selecione *AzureMonitoringData*.
      1. Na lista **Selecionar o nome da política do hub de eventos**, selecione o nome da política padrão do hub de eventos.
      1. Selecione **OK**.
      1. No canto superior esquerdo da janela, selecione **Salvar**.
   Será criado um hub de eventos com o nome *insights-operational-logs*.

### <a name="see-data-flowing-to-your-event-hubs"></a>Ver os dados fluindo para os hubs de eventos

1. Aguarde alguns minutos até a conexão ser definida e a exportação do log de atividades para o hub de eventos ser concluída. Acesse o namespace dos Hubs de Eventos para ver os hubs de eventos criados.

    ![Hubs de eventos criados](media/ingest-data-no-code/event-hubs-created.png)

1. Veja os dados fluindo para o hub de eventos:

    ![Dados do hub de eventos](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>Conectar um hub de eventos ao Azure Data Explorer

Agora, você precisa criar as conexões de dados para os logs de diagnóstico e os logs de atividades.

### <a name="create-the-data-connection-for-diagnostic-logs"></a>Criar a conexão de dados para os logs de diagnóstico

1. No cluster do Azure Data Explorer chamado *kustodocs*, selecione **Bancos de Dados** no menu à esquerda.
1. Na janela **Bancos de Dados**, selecione o banco de dados *TestDatabase*.
1. No menu à esquerda, selecione **Ingestão de dados**.
1. Na janela **Ingestão de dados**, clique em **+ Adicionar Conexão de Dados**.
1. Na janela **Conexão de dados**, insira as seguintes informações:

    ![Conexão de dados do hub de eventos](media/ingest-data-no-code/event-hub-data-connection.png)

    Fonte de dados:

    **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | **Nome da conexão de dados** | *DiagnosticsLogsConnection* | O nome da conexão que você deseja criar no Azure Data Explorer.|
    | **Namespace do Hub de Eventos** | *AzureMonitoringData* | O nome escolhido anteriormente que identifica seu namespace. |
    | **Hub de Eventos** | *diagnosticlogsdata* | O hub de eventos que você criou. |
    | **Grupo de consumidores** | *adxpipeline* | O grupo de consumidores definido no hub de eventos que você criou. |
    | | |

    Tabela de destino:

    Há duas opções de roteamento: *estático* e *dinâmico*. Para este tutorial, você usará o roteamento estático (o padrão), no qual especificará o nome da tabela, o formato de dados e o mapeamento. Não selecione **Meus dados incluem informações de roteamento**.

     **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | **Tabela** | *DiagnosticLogsRawRecords* | A tabela criada no banco de dados *TestDatabase*. |
    | **Formato de dados** | *JSON* | O formato usado na tabela. |
    | **Mapeamento de coluna** | *DiagnosticLogsRecordsMapping* | O mapeamento criado no banco de dados *TestDatabase*, que mapeia os dados JSON de entrada para os nomes de coluna e os tipos de dados da tabela *DiagnosticLogsRecords*.|
    | | |

1. Selecione **Criar**.  

### <a name="create-the-data-connection-for-activity-logs"></a>Criar a conexão de dados para os logs de atividades

Repita as etapas da seção Criar a conexão de dados para os logs de diagnóstico para criar a conexão de dados para os logs de atividades.

1. Use as seguintes configurações na janela **Conexão de Dados**:

    Fonte de dados:

    **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | **Nome da conexão de dados** | *ActivityLogsConnection* | O nome da conexão que você deseja criar no Azure Data Explorer.|
    | **Namespace do Hub de Eventos** | *AzureMonitoringData* | O nome escolhido anteriormente que identifica seu namespace. |
    | **Hub de Eventos** | *insights-operational-logs* | O hub de eventos que você criou. |
    | **Grupo de consumidores** | *$Default* | O grupo de consumidor padrão. Se necessário, é possível criar um grupo de consumidor diferente. |
    | | |

    Tabela de destino:

    Há duas opções de roteamento: *estático* e *dinâmico*. Para este tutorial, você usará o roteamento estático (o padrão), no qual especificará o nome da tabela, o formato de dados e o mapeamento. Não selecione **Meus dados incluem informações de roteamento**.

     **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | **Tabela** | *ActivityLogsRawRecords* | A tabela criada no banco de dados *TestDatabase*. |
    | **Formato de dados** | *JSON* | O formato usado na tabela. |
    | **Mapeamento de coluna** | *ActivityLogsRawRecordsMapping* | O mapeamento criado no banco de dados *TestDatabase*, que mapeia os dados JSON de entrada para os nomes de coluna e os tipos de dados da tabela *ActivityLogsRawRecords*.|
    | | |

1. Selecione **Criar**.  

## <a name="query-the-new-tables"></a>Consultar as novas tabelas

Agora você tem um pipeline com os dados fluindo. A ingestão por meio do cluster leva 5 minutos por padrão; portanto, permita que os dados fluam por alguns minutos antes de iniciar a consulta.

### <a name="an-example-of-querying-the-diagnostic-logs-table"></a>Um exemplo de consulta da tabela dos logs de diagnóstico

A seguinte consulta analisa os dados de duração da consulta dos registros do log de diagnóstico no Azure Data Explorer:

```kusto
DiagnosticLogsRecords
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

Resultados da consulta:

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06.156 |
| | |

### <a name="an-example-of-querying-the-activity-logs-table"></a>Um exemplo de consulta da tabela dos logs de atividades

A seguinte consulta analisa os dados dos registros do log de atividades no Azure Data Explorer:

```kusto
ActivityLogsRecords
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

Resultados da consulta:

|   |   |
| --- | --- |
|   |  avg(DurationMs) |
|   | 768.333 |
| | |

## <a name="next-steps"></a>Próximas etapas

Saiba como escrever muito mais consultas nos dados extraídos do Azure Data Explorer usando o seguinte artigo:

> [!div class="nextstepaction"]
> [Escrever consultas para o Azure Data Explorer](write-queries.md)
