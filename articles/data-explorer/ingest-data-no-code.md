---
title: 'Tutorial: Ingerir dados de diagnóstico e de log de atividades no Azure Data Explorer sem uma linha de código'
description: Neste tutorial, você aprenderá a ingerir dados no Azure Data Explorer sem uma linha de código e a consultar esses dados.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: tutorial
ms.date: 2/5/2019
ms.openlocfilehash: 145a56bee857debdbf028834a3ed378efd8671c8
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447490"
---
# <a name="tutorial-ingest-data-in-azure-data-explorer-without-one-line-of-code"></a>Tutorial: Ingerir dados no Azure Data Explorer sem uma linha de código

Este tutorial ensinará você a ingerir dados de diagnóstico e de log de atividades para um cluster do Azure Data Explorer sem uma linha de código. Esse método de ingestão simples permite que você comece a consultar o Azure Data Explorer rapidamente para análise de dados.

Neste tutorial, você aprenderá a:
> [!div class="checklist"]
> * Crie tabelas e mapeamento de ingestão em um banco de dados do Azure Data Explorer.
> * Formate os dados ingeridos usando uma política de atualização.
> * Criar um [Hub de Eventos](/azure/event-hubs/event-hubs-about) e conecte-se ao Azure Data Explorer.
> * Transmita dados para um Hub de Eventos dos [Logs de diagnóstico do Azure Monitor](/azure/azure-monitor/platform/diagnostic-logs-overview) e dos [Logs de atividades do Azure Monitor](/azure/azure-monitor/platform/activity-logs-overview).
> * Consulte os dados ingeridos usando o Azure Data Explorer.

> [!NOTE]
> Crie todos os recursos na mesma região/localização do Azure. Esse é um requisito dos logs de diagnóstico do Azure Monitor.

## <a name="prerequisites"></a>Pré-requisitos

* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* [Um cluster e um banco de dados do Azure Data Explorer](create-cluster-database-portal.md). Neste tutorial, o nome do banco de dados é *AzureMonitoring*.

## <a name="azure-monitoring-data-provider---diagnostic-and-activity-logs"></a>Provedor de dados do Monitoramento do Azure – diagnóstico e logs de atividades

Exiba e compreenda os dados fornecidos pelo diagnóstico e pelos logs de atividades do Monitoramento do Azure. Criaremos um pipeline de ingestão com base nesses esquemas de dados.

### <a name="diagnostic-logs-example"></a>Exemplo de logs de diagnóstico

Os logs de diagnóstico do Azure são métricas emitidas por um serviço do Azure que fornecem dados sobre a operação desse serviço. Os dados são agregados com intervalo de agregação de 1 minuto. Cada evento dos logs de diagnóstico contém um registro. A seguir há um exemplo de um esquema de evento de métrica do Azure Data Explorer sobre a duração da consulta:

```json
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
}
```

### <a name="activity-logs-example"></a>Exemplo de logs de atividades

Os logs de atividades do Azure são logs de nível de assinatura que contêm uma coleção de registros. Os logs fornecem informações sobre as operações executadas em recursos de sua assinatura. Ao contrário dos logs de diagnóstico, um evento dos logs de atividades tem uma matriz de registros. Será necessário dividir essa matriz de registros posteriormente no tutorial. A seguir, há um exemplo de um evento de log de atividades para verificar o acesso:

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

## <a name="set-up-ingestion-pipeline-in-azure-data-explorer"></a>Configurar o pipeline de ingestão no Azure Data Explorer 

A configuração de pipeline do Azure Data Explorer contém várias etapas que incluem [ingestão de dados e criação de tabela](/azure/data-explorer/ingest-sample-data#ingest-data). Também é possível manipular, mapear e atualizar os dados.

### <a name="connect-to-azure-data-explorer-web-ui"></a>Conectar-se à interface do usuário da Web do Azure Data Explorer

1. No banco de dados *AzureMonitoring* do Azure Data Explorer, selecione **Consultar**, que abrirá a interface do usuário da Web do Azure Data Explorer.

    ![Consultar](media/ingest-data-no-code/query-database.png)

### <a name="create-target-tables"></a>Criar tabelas de destino

Use a interface do usuário da Web do Azure Data Explorer para criar as tabelas de destino no banco de dados do Azure Data Explorer.

#### <a name="diagnostic-logs-table"></a>Tabela de logs de diagnóstico

1. Crie uma tabela *DiagnosticLogsRecords* no banco de dados *AzureMonitoring* que receberá os registros de log de diagnóstico usando o comando de controle `.create table`:

    ```kusto
    .create table DiagnosticLogsRecords (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Selecione **Executar** para criar a tabela.

    ![Executar Consulta](media/ingest-data-no-code/run-query.png)

#### <a name="activity-logs-tables"></a>Tabelas de logs de atividades

Como a estrutura dos logs de atividades não é tabular, será necessário manipular os dados e expandir cada evento para um ou mais registros. Os dados brutos serão ser ingeridos em uma tabela intermediária *ActivityLogsRawRecords*. Nesse momento, os dados serão manipulados e expandidos. Os dados expandidos serão, então, ingeridos na tabela *ActivityLogsRecords* usando uma política de atualização. Portanto, será necessário criar duas tabelas separadas para a ingestão de logs de atividades.

1. Crie uma tabela *ActivityLogsRecords* no banco de dados *AzureMonitoring* que receberá os registros de log de atividades. Execute a seguinte consulta do Azure Data Explorer para criar a tabela:

    ```kusto
    .create table ActivityLogsRecords (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Crie a tabela de dados intermediária *ActivityLogsRawRecords* no banco de dados *AzureMonitoring* para manipulação de dados:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

<!--
     ```kusto
     .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    <[Retention](/azure/kusto/management/retention-policy) for an intermediate data table is set at zero retention policy.
-->

### <a name="create-table-mappings"></a>Criar mapeamentos de tabela

 O formato de dados é `json`; portanto, o mapeamento de dados é necessário. O mapeamento `json` mapeia cada caminho JSON para um nome de coluna de tabela.

#### <a name="diagnostic-logs-table-mapping"></a>Mapeamento de tabela de logs de diagnóstico

Use a consulta a seguir para mapear os dados para a tabela:

```kusto
.create table DiagnosticLogsRecords ingestion json mapping 'DiagnosticLogsRecordsMapping' '[{"column":"Timestamp","path":"$.time"},{"column":"ResourceId","path":"$.resourceId"},{"column":"MetricName","path":"$.metricName"},{"column":"Count","path":"$.count"},{"column":"Total","path":"$.total"},{"column":"Minimum","path":"$.minimum"},{"column":"Maximum","path":"$.maximum"},{"column":"Average","path":"$.average"},{"column":"TimeGrain","path":"$.timeGrain"}]'
```

#### <a name="activity-logs-table-mapping"></a>Mapeamento de tabela de logs de atividades

Use a consulta a seguir para mapear os dados para a tabela:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

### <a name="create-update-policy"></a>Criar política de atualização

1. Crie uma [função](/azure/kusto/management/functions) que expande a coleção de registros de forma que cada valor na coleção recebe uma linha separada. Use o operador [`mvexpand`](/azure/kusto/query/mvexpandoperator):

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mvexpand events = Records
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

2. Adicione uma [política de atualização](/azure/kusto/concepts/updatepolicy) à tabela de destino. Ela executará automaticamente a consulta em quaisquer dados recém-ingeridos na tabela de dados intermediária *ActivityLogsRawRecords* e ingerirá seus resultados na tabela *ActivityLogsRecords*:

    ```kusto
    .alter table ActivityLogsRecords policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

## <a name="create-an-event-hub-namespace"></a>Criar um Namespace do Hub de Eventos

Os logs de diagnóstico do Azure permitem a exportação de métricas para uma conta de armazenamento ou um Hub de Eventos. Neste tutorial, roteamos métricas por meio de um Hub de Eventos. Você criará um Namespace de Hubs de Eventos e um Hub de Eventos para os logs de diagnóstico nas etapas a seguir. O Monitoramento do Azure criará o Hub de Eventos *insights-operational-logs* para logs de atividades.

1. Crie um hub de eventos usando um modelo do Azure Resource Manager no portal do Azure. Use o botão a seguir para iniciar a implantação. Clique com o botão direito do mouse e selecione **Abrir em nova janela** para que você possa acompanhar o restante das etapas neste artigo. O botão **Implantar no Azure** leva você ao portal do Azure.

    [![Implantar no Azure](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Crie um Namespace do Hub de Eventos e um Hub de Eventos para os logs de diagnóstico.

    ![Criação do Hub de Eventos](media/ingest-data-no-code/event-hub.png)

    Preencha o formulário com as seguintes informações. Use padrões para quaisquer configurações não listadas na tabela a seguir.

    **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Assinatura | Sua assinatura | Selecione a assinatura do Azure que você deseja usar para o seu hub de eventos.|
    | Grupo de recursos | *test-resource-group* | Crie um novo grupo de recursos. |
    | Local padrão | Selecione a região que melhor atende às suas necessidades. | Crie o namespace do hub de eventos na mesma localização que outros recursos.
    | Nome do Namespace | *AzureMonitoringData* | Escolha um nome exclusivo que identifique seu namespace.
    | Nome do Hub de Eventos | *DiagnosticLogsData* | O hub de eventos fica sob o namespace, que fornece um contêiner de determinação de escopo exclusivo. |
    | Nome do grupo de consumidor | *adxpipeline* | Crie um nome de grupo de consumidores. Permite que vários aplicativos de consumo tenham, cada um, uma exibição separada do fluxo de eventos. |
    | | |

## <a name="connect-azure-monitoring-logs-to-event-hub"></a>Conectar-se logs de Monitoramento do Azure ao Hub de eventos

### <a name="diagnostic-logs-connection-to-event-hub"></a>Conexão de logs de diagnóstico com o Hub de eventos

Selecione um recurso do qual exportar métricas. Há vários tipos de recursos que permitem a exportação de logs de diagnóstico, incluindo o Namespace do hub de eventos, KeyVault, Hub IoT e cluster do Azure Data Explorer. Neste tutorial, usamos o cluster do Azure Data Explorer como nosso recurso.

1. Selecione o cluster Kusto no portal do Azure.

    ![Configurações de Diagnóstico](media/ingest-data-no-code/diagnostic-settings.png)

1. Selecione **Configurações de diagnóstico** no menu esquerdo.
1. Clique no link **Ativar diagnóstico**. A janela **Configurações de diagnóstico** é aberta.

    ![Janela Configurações de diagnóstico](media/ingest-data-no-code/diagnostic-settings-window.png)

1. No painel **Configurações de diagnóstico**:
    1. Dê um nome aos dados do log de diagnóstico: *ADXExportedData*
    1. Marque a caixa de seleção **AllMetrics** (opcional).
    1. Marque a caixa de seleção **Transmitir para um hub de eventos**.
    1. Clique em **Configurar**

1. No painel **Selecionar hub de eventos** para configurar a exportação para o hub de eventos criado:
    1. **Selecione o namespace do hub de eventos** *AzureMonitoringData* na lista suspensa.
    1. **Selecione o nome do hub de eventos** *diagnosticlogsdata* na lista suspensa.
    1. **Selecione o nome da política de hub de eventos** na lista suspensa.
    1. Clique em **OK**.

1. Clique em **Salvar**. O namespace, o nome e o nome da política do Hub de eventos serão exibidos na janela.

    ![Salvar configurações de diagnóstico](media/ingest-data-no-code/save-diagnostic-settings.png)

### <a name="activity-logs-connection-to-event-hub"></a>Conexão dos logs de atividades com o Hub de eventos

1. No menu esquerdo do portal do Azure, selecione **Log de atividades**
1. A janela **Log de atividades** é aberta. **Clique em Exportar para o Hub de Eventos**

    ![Log de atividades](media/ingest-data-no-code/activity-log.png)

1. Na janela **Exportar log de atividades**:
 
    ![Exportar log de atividades](media/ingest-data-no-code/export-activity-log.png)

    1. Selecione sua assinatura.
    1. Na lista suspensa **Regiões**, escolha **Selecionar tudo**
    1. Marque a caixa de seleção **Exportar para um hub de eventos**.
    1. Clique em **Selecionar um namespace de barramento de serviço** para abrir o painel **Selecionar hub de eventos**.
    1. No painel **Selecionar hub de eventos**, selecione nos menus suspensos: sua assinatura, seu namespace de evento *AzureMonitoringData* e o nome da política do hub de eventos padrão.
    1. Clique em **OK**.
    1. Clique em **Salvar** no lado superior direito da janela. Será criado um hub de eventos com o nome *insights-operational-logs*.

### <a name="see-data-flowing-to-your-event-hubs"></a>Veja os dados fluindo para os Hubs de Eventos

1. Aguarde alguns minutos até que a conexão seja definida e a exportação do log de atividades para o hub de eventos seja concluída. Vá para o namespace do Hub de Eventos para ver os hubs de eventos criados.

    ![Hubs de Eventos criados](media/ingest-data-no-code/event-hubs-created.png)

1. Veja os dados fluindo para o Hub de Eventos:

    ![Dados dos Hubs de Eventos](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-event-hub-to-azure-data-explorer"></a>Conectar o Hub de eventos ao Azure Data Explorer

### <a name="diagnostic-logs-data-connection"></a>Conexão de dados de logs de diagnóstico

1. Em seu cluster do Azure Data Explorer *kustodocs*, selecione **Bancos de Dados** no menu esquerdo.
1. Na janela **Bancos de Dados**, selecione o nome do banco de dados *AzureMonitoring*
1. No menu esquerdo, selecione **Ingestão de dados**
1. Na janela **Ingestão de dados**, clique em **+ Adicionar Conexão de Dados**
1. Na janela **Conexão de dados**, insira as seguintes informações:

    ![Conexão do hub de eventos](media/ingest-data-no-code/event-hub-data-connection.png)

    Fonte de dados:

    **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Nome da conexão de dados | *DiagnosticsLogsConnection* | O nome da conexão que você deseja criar no Azure Data Explorer.|
    | Namespace do Hub de Eventos | *AzureMonitoringData* | O nome escolhido anteriormente que identifica seu namespace. |
    | Hub de Eventos | *diagnosticlogsdata* | O hub de eventos que você criou. |
    | Grupo de consumidores | *adxpipeline* | O grupo de consumidores definido no hub de eventos que você criou. |
    | | |

    Tabela de destino:

    Há duas opções de roteamento: *estático* e *dinâmico*. Para este tutorial, você usará o roteamento estático (o padrão), no qual especificará o nome da tabela, o formato do arquivo e o mapeamento. Portanto, não selecione **Meus dados incluem informações de roteamento**.

     **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Tabela | *DiagnosticLogsRecords* | A tabela criada no banco de dados *AzureMonitoring*. |
    | Formato de dados | *JSON* | Formato na tabela. |
    | Mapeamento de coluna | *DiagnosticLogsRecordsMapping* | O mapeamento criado no banco de dados *AzureMonitoring*, que mapeia os dados JSON de entrada para os nomes de coluna e tipos de dados de *DiagnosticLogsRecords*.|
    | | |

1. Clique em **Criar**  

### <a name="activity-logs-data-connection"></a>Conexão de dados dos logs de atividades

Repita as etapas na seção [Conexão de dados de logs de diagnóstico](#diagnostic-logs-data-connection) para criar a conexão de dados de logs de atividades.

1. Insira as seguintes configurações na janela **Conexão de dados**:

    Fonte de dados:

    **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Nome da conexão de dados | *ActivityLogsConnection* | O nome da conexão que você deseja criar no Azure Data Explorer.|
    | Namespace do Hub de Eventos | *AzureMonitoringData* | O nome escolhido anteriormente que identifica seu namespace. |
    | Hub de Eventos | *insights-operational-logs* | O hub de eventos que você criou. |
    | Grupo de consumidores | *$Default* | O grupo de consumidor padrão. Se necessário, é possível criar um grupo de consumidor diferente. |
    | | |

    Tabela de destino:

    Há duas opções de roteamento: *estático* e *dinâmico*. Para este tutorial, você usará o roteamento estático (o padrão), no qual especificará o nome da tabela, o formato do arquivo e o mapeamento. Portanto, não selecione **Meus dados incluem informações de roteamento**.

     **Configuração** | **Valor sugerido** | **Descrição do campo**
    |---|---|---|
    | Tabela | *ActivityLogsRawRecords* | A tabela criada no banco de dados *AzureMonitoring*. |
    | Formato de dados | *JSON* | Formato na tabela. |
    | Mapeamento de coluna | *ActivityLogsRawRecordsMapping* | O mapeamento criado no banco de dados *AzureMonitoring*, que mapeia os dados JSON de entrada para os nomes de coluna e tipos de dados de *ActivityLogsRawRecords*.|
    | | |

1. Clique em **Criar**  

## <a name="query-the-new-tables"></a>Consultar as novas tabelas

Você tem um pipeline com os dados fluindo. A ingestão por meio do cluster leva 5 minutos, por padrão; dessa forma, espere alguns minutos antes de começar a consultar o fluxo de dados.

### <a name="diagnostic-logs-table-query-example"></a>Exemplo de consulta de tabela de logs de diagnóstico

A consulta a seguir analisa os dados de duração da consulta dos registros do log de diagnóstico do Azure Data Explorer:

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

### <a name="activity-logs-table-query-example"></a>Exemplo de consulta de tabela de logs de atividades

A consulta a seguir analisa dados dos registros do log de atividades do Azure Data Explorer:

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

Aprenda a escrever muitas consultas mais nos dados extraídos do Azure Data Explorer usando o artigo a seguir:

> [!div class="nextstepaction"]
> [Escrever consultas para o Azure Data Explorer](write-queries.md)
