---
title: "Análise de tráfego de pesquisa para o Azure Search | Microsoft Docs"
description: "Habilite a análise de tráfego de pesquisa para a pesquisa do Azure, um serviço de pesquisa hospedado na nuvem no Microsoft Azure, para ter ideias sobre os usuários e seus dados."
services: search
documentationcenter: 
author: bernitorres
manager: pablocas
editor: 
ms.assetid: b31d79cf-5924-4522-9276-a1bb5d527b13
ms.service: search
ms.devlang: multiple
ms.workload: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: betorres
translationtype: Human Translation
ms.sourcegitcommit: ebfed89674dc132bd5d93f34a8b5ed5ab12bd73e
ms.openlocfilehash: cd6c1d094f8601a06642a2a84ef1234a29a8f058

---

# <a name="enabling-and-using-search-traffic-analytics"></a>Habilitação e uso da análise de tráfego de pesquisa
A análise de tráfego de pesquisa é um recurso de pesquisa do Azure que permite que você ganhe visibilidade em seu serviço de pesquisa e tenha ideias sobre os usuários e seus comportamentos. Quando você habilita esse recurso, os dados do serviço de pesquisa são copiados para uma conta de armazenamento de sua escolha. Esses dados incluem seus logs de serviço de pesquisa e as métricas operacionais agregadas que você pode processar e manipular para análise posterior.

## <a name="how-to-enable-search-traffic-analytics"></a>Como habilitar a análise de tráfego de pesquisa
Você precisa de uma conta de armazenamento na mesma região e assinatura do serviço de pesquisa.

> [!IMPORTANT]
> Os encargos padrão se aplicam para essa conta de armazenamento
>
>

Você pode habilitar a análise de tráfego de pesquisa no portal ou por meio do PowerShell. Uma vez habilitados, os dados começam a fluir para sua conta de armazenamento no prazo de 5 a 10 minutos nesses dois contêineres de blobs:

    insights-logs-operationlogs: search traffic logs
    insights-metrics-pt1m: aggregated metrics


### <a name="a-using-the-portal"></a>R. Usando o portal
Abra o serviço Azure Search no [portal do Azure](http://portal.azure.com). Em Configurações, você encontra a opção Análise de tráfego de pesquisa.

![][1]

Altere o Status para **Ativado**, selecione a conta de armazenamento do Azure a ser usada e escolha os dados que você deseja copiar: Logs, Métricas ou ambos. É recomendável copiar os logs e as métricas.
Você tem a opção de definir a política de retenção de dados de 1 a 365 dias. Se você não deseja manter os dados indefinidamente, defina a retenção (dias) para 0.

![][2]

### <a name="b-using-powershell"></a>B. Usando o PowerShell
Primeiro, verifique se você tem a versão mais recente dos [cmdlets do Azure PowerShell](https://github.com/Azure/azure-powershell/releases) instalada.

Em seguida, obtenha as IDs do Recurso para o Serviço de Pesquisa e sua Conta de armazenamento. Você pode encontrá-las no portal navegando até Configurações -> Propriedades -> ResourceId.

![][3]

```PowerShell
Login-AzureRmAccount
$SearchServiceResourceId = "Your Search service resource id"
$StorageAccountResourceId = "Your Storage account resource id"
Set-AzureRmDiagnosticSetting -ResourceId $SearchServiceResourceId StorageAccountId $StorageAccountResourceId -Enabled $true
```

## <a name="understanding-the-data"></a>Compreendendo os dados
Os dados são armazenados em blobs de armazenamento do Azure, formatados como JSON.

Haverá um blob por hora, por contêiner.

Exemplo de caminho: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

### <a name="logs"></a>Logs
Os blobs de logs contêm seus logs de tráfego do serviço de pesquisa.
Cada blob tem um objeto-raiz chamado **registros** que contém uma matriz de objetos do log.
Cada blob tem registros na operação que ocorrem durante a mesma hora.

#### <a name="log-schema"></a>Esquema do log
| Nome | Tipo | Exemplo | Observações |
| --- | --- | --- | --- |
| tempo real |datetime |"2015-12-07T00:00:43.6872559Z" |Carimbo de data/hora da operação |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>  MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |Seu ResourceId |
| operationName |string |"Query.Search" |O nome da operação |
| operationVersion |string |"2016-09-01" |A api-version usada |
| categoria |string |"OperationLogs" |constante |
| resultType |string |"Success" |Valores possíveis: Success ou Failure |
| resultSignature |int |200 |Código do resultado HTTP |
| durationMS |int |50 |Duração da operação em milissegundos |
| propriedades |objeto |confira a seguinte tabela |Objeto que contém os dados específicos da operação |

#### <a name="properties-schema"></a>Esquema de propriedades
| Nome | Tipo | Exemplo | Observações |
| --- | --- | --- | --- |
| Descrição |string |"GET /indexes('content')/docs" |Ponto de extremidade da operação |
| Consultar |string |"?search=AzureSearch&$count=true&api-version=2016-09-01" |Parâmetros da consulta |
| Documentos |int |42 |Número de documentos processados |
| IndexName |string |"testindex" |Nome do índice associado à operação |

### <a name="metrics"></a>Métricas
Os blobs de métricas contêm os valores agregados para o serviço de pesquisa.
Cada arquivo tem um objeto-raiz chamado **registros** que contém uma matriz de objetos de métrica. Esse objeto-raiz contém métricas para cada minuto no qual dados estavam disponíveis.

Métricas disponíveis:

* SearchLatency: o tempo que o serviço de pesquisa precisou para processar consultas de pesquisa, agregadas por minuto.
* SearchQueriesPerSecond: o número de consultas de pesquisa recebidas por segundo, agregadas por minuto.
* ThrottledSearchQueriesPercentage: a porcentagem de consultas de pesquisa que foram limitadas, agregadas por minuto.

> [!IMPORTANT]
> A limitação ocorre quando um número excessivo de consultas são enviadas, esgotando a capacidade de recurso provisionada do serviço. Considere adicionar mais réplicas ao seu serviço.
>
>

#### <a name="metrics-schema"></a>Esquema de métricas
| Nome | Tipo | Exemplo | Observações |
| --- | --- | --- | --- |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |id do recurso |
| metricName |string |"Latency" |o nome da métrica |
| tempo real |datetime |"2015-12-07T00:00:43.6872559Z" |carimbo de data/hora da operação |
| média |int |64 |O valor médio das amostras brutas no intervalo de agregação da métrica |
| mínimo |int |37 |O valor mínimo das amostras brutas no intervalo de agregação da métrica |
| máximo |int |78 |O valor máximo das amostras brutas no intervalo de agregação da métrica |
| total |int |258 |O valor total das amostras brutas no intervalo de agregação da métrica |
| count |int |4 |O número de amostras brutas usadas para gerar a métrica |
| intervalo de tempo |string |"PT1M" |O intervalo de agregação da métrica no ISO 8601 |

Todas as métricas são reportadas em intervalos de um minuto. Cada métrica expõe valores mínimo, máximo e médios por minuto.

Para a métrica SearchQueriesPerSecond, o valor mínimo é o valor mais baixo para consultas de pesquisa por segundo que foram registradas durante esse minuto. O mesmo se aplica ao valor máximo. O valor médio é a agregação durante todo o minuto.
Considere este cenário durante um minuto, um segundo com uma carga muito alta, que é o valor máximo para SearchQueriesPerSecond, seguido de 58 segundos de carga mediana, e, por fim, um segundo com apenas uma consulta, que será o valor mínimo.

Para ThrottledSearchQueriesPercentage, os valores mínimo, máximo, médio e total possuem o mesmo valor: a porcentagem de consultas de pesquisa que foram limitadas, a partir do no número total de consultas de pesquisa durante um minuto.

## <a name="analyzing-your-data"></a>Análise dos dados
Os dados estão em sua própria conta de armazenamento e incentivamos que você explore os dados da maneira que funciona melhor para seu caso.

Como ponto de partida, recomendamos o uso do [Power BI](https://powerbi.microsoft.com) para explorar e visualizar os dados. Você pode conectar facilmente sua conta de armazenamento do Azure e começar rapidamente a analisar os dados.

#### <a name="power-bi-online"></a>Power BI Online
[Pacote de conteúdo do Power BI](https://app.powerbi.com/getdata/services/azure-search): crie um painel e um conjunto de relatórios do Power BI que mostram automaticamente seus dados e fornecem análises visuais sobre o serviço de pesquisa. Confira a [página de ajuda do pacote de conteúdo](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-search/).

![][4]

#### <a name="power-bi-desktop"></a>Power BI Desktop
[Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop): explore seus dados e crie suas próprias visualizações de dados. Veja a consulta inicial na seção a seguir:

1. Abra um novo relatório do Power BI Desktop
2. Selecione Obter Dados -> Mais...

    ![][5]
3. Selecionar o Armazenamento de Blobs do Microsoft Azure e Conectar-se

    ![][6]
4. Inserir o Nome e a Chave da Conta de sua conta de armazenamento
5. Selecione "insight-logs-operationlogs" e "insights-metrics-pt1m" e clique em Editar
6. Quando Editor de Consultas for aberto, verifique se "insight-logs-operationlogs" está selecionado à esquerda. Agora, abra o Editor Avançado selecionando Exibir -> Editor Avançado.

    ![][7]
7. Mantenha as duas primeiras linhas e substitua o restante pela seguinte consulta:

   > # <a name="insights-logs-operationlogs--sourcenameinsights-logs-operationlogsdata"></a>"insights-logs-operationlogs" = Source{[Name="insights-logs-operationlogs"]}[Data],
   > # <a name="sorted-rows--tablesortinsights-logs-operationlogsdate-modified-orderdescending"></a>"Sorted Rows" = Table.Sort(#"insights-logs-operationlogs",{{"Date modified", Order.Descending}}),
   > # <a name="kept-first-rows--tablefirstnsorted-rows744"></a>"Kept First Rows" = Table.FirstN(#"Sorted Rows",744),
   > # <a name="removed-columns--tableremovecolumnskept-first-rowsname-extension-date-accessed-date-modified-date-created-attributes-folder-path"></a>"Removed Columns" = Table.RemoveColumns(#"Kept First Rows",{"Name", "Extension", "Date accessed", "Date modified", "Date created", "Attributes", "Folder Path"}),
   > # <a name="parsed-json--tabletransformcolumnsremoved-columnsjsondocument"></a>"Parsed JSON" = Table.TransformColumns(#"Removed Columns",{},Json.Document),
   > # <a name="expanded-content--tableexpandrecordcolumnparsed-json-content-records-records"></a>"Expanded Content" = Table.ExpandRecordColumn(#"Parsed JSON", "Content", {"records"}, {"records"}),
   > # <a name="expanded-records--tableexpandlistcolumnexpanded-content-records"></a>"Expanded records" = Table.ExpandListColumn(#"Expanded Content", "records"),
   > # <a name="expanded-records1--tableexpandrecordcolumnexpanded-records-records-time-resourceid-operationname-operationversion-category-resulttype-resultsignature-durationms-properties-time-resourceid-operationname-operationversion-category-resulttype-resultsignature-durationms-properties"></a>"Expanded records1" = Table.ExpandRecordColumn(#"Expanded records", "records", {"time", "resourceId", "operationName", "operationVersion", "category", "resultType", "resultSignature", "durationMS", "properties"}, {"time", "resourceId", "operationName", "operationVersion", "category", "resultType", "resultSignature", "durationMS", "properties"}),
   > # <a name="expanded-properties--tableexpandrecordcolumnexpanded-records1-properties-description-query-indexname-documents-description-query-indexname-documents"></a>"Expanded properties" = Table.ExpandRecordColumn(#"Expanded records1", "properties", {"Description", "Query", "IndexName", "Documents"}, {"Description", "Query", "IndexName", "Documents"}),
   > # <a name="renamed-columns--tablerenamecolumnsexpanded-propertiestime-datetime-resourceid-resourceid-operationname-operationname-operationversion-operationversion-category-category-resulttype-resulttype-resultsignature-resultsignature-durationms-duration"></a>"Renamed Columns" = Table.RenameColumns(#"Expanded properties",{{"time", "Datetime"}, {"resourceId", "ResourceId"}, {"operationName", "OperationName"}, {"operationVersion", "OperationVersion"}, {"category", "Category"}, {"resultType", "ResultType"}, {"resultSignature", "ResultSignature"}, {"durationMS", "Duration"}}),
   > # <a name="added-custom2--tableaddcolumnrenamed-columns-queryparameters-each-uripartshttptmp--query"></a>"Added Custom2" = Table.AddColumn(#"Renamed Columns", "QueryParameters", each Uri.Parts("http://tmp" & [Query])),
   > # <a name="expanded-queryparameters--tableexpandrecordcolumnadded-custom2-queryparameters-query-query1"></a>"Expanded QueryParameters" = Table.ExpandRecordColumn(#"Added Custom2", "QueryParameters", {"Query"}, {"Query.1"}),
   > # <a name="expanded-query1--tableexpandrecordcolumnexpanded-queryparameters-query1-search-skip-top-count-api-version-searchmode-filter-search-skip-top-count-api-version-searchmode-filter"></a>"Expanded Query.1" = Table.ExpandRecordColumn(#"Expanded QueryParameters", "Query.1", {"search", "$skip", "$top", "$count", "api-version", "searchMode", "$filter"}, {"search", "$skip", "$top", "$count", "api-version", "searchMode", "$filter"}),
   > # <a name="removed-columns1--tableremovecolumnsexpanded-query1operationversion"></a>"Removed Columns1" = Table.RemoveColumns(#"Expanded Query.1",{"OperationVersion"}),
   > # <a name="changed-type--tabletransformcolumntypesremoved-columns1datetime-type-datetimezone-resourceid-type-text-operationname-type-text-category-type-text-resulttype-type-text-resultsignature-type-text-duration-int64type-description-type-text-query-type-text-indexname-type-text-documents-int64type-search-type-text-skip-int64type-top-int64type-count-type-logical-api-version-type-text-searchmode-type-text-filter-type-text"></a>"Changed Type" = Table.TransformColumnTypes(#"Removed Columns1",{{"Datetime", type datetimezone}, {"ResourceId", type text}, {"OperationName", type text}, {"Category", type text}, {"ResultType", type text}, {"ResultSignature", type text}, {"Duration", Int64.Type}, {"Description", type text}, {"Query", type text}, {"IndexName", type text}, {"Documents", Int64.Type}, {"search", type text}, {"$skip", Int64.Type}, {"$top", Int64.Type}, {"$count", type logical}, {"api-version", type text}, {"searchMode", type text}, {"$filter", type text}}),
   > # <a name="inserted-date--tableaddcolumnchanged-type-date-each-datetimedatedatetime-type-date"></a>"Inserted Date" = Table.AddColumn(#"Changed Type", "Date", each DateTime.Date([Datetime]), type date),
   > # <a name="duplicated-column--tableduplicatecolumninserted-date-resourceid-copy-of-resourceid"></a>"Duplicated Column" = Table.DuplicateColumn(#"Inserted Date", "ResourceId", "Copy of ResourceId"),
   > # <a name="split-column-by-delimiter--tablesplitcolumnduplicated-columncopy-of-resourceidsplittersplittextbyeachdelimiter-null-truecopy-of-resourceid1-copy-of-resourceid2"></a>"Split Column by Delimiter" = Table.SplitColumn(#"Duplicated Column","Copy of ResourceId",Splitter.SplitTextByEachDelimiter({"/"}, null, true),{"Copy of ResourceId.1", "Copy of ResourceId.2"}),
   > # <a name="changed-type1--tabletransformcolumntypessplit-column-by-delimitercopy-of-resourceid1-type-text-copy-of-resourceid2-type-text"></a>"Changed Type1" = Table.TransformColumnTypes(#"Split Column by Delimiter",{{"Copy of ResourceId.1", type text}, {"Copy of ResourceId.2", type text}}),
   > # <a name="removed-columns2--tableremovecolumnschanged-type1copy-of-resourceid1"></a>"Removed Columns2" = Table.RemoveColumns(#"Changed Type1",{"Copy of ResourceId.1"}),
   > # <a name="renamed-columns1--tablerenamecolumnsremoved-columns2copy-of-resourceid2-servicename"></a>"Renamed Columns1" = Table.RenameColumns(#"Removed Columns2",{{"Copy of ResourceId.2", "ServiceName"}}),
   > # <a name="lowercased-text--tabletransformcolumnsrenamed-columns1servicename-textlower"></a>"Lowercased Text" = Table.TransformColumns(#"Renamed Columns1",{{"ServiceName", Text.Lower}}),
   > # <a name="added-custom--tableaddcolumnlowercased-text-daysfromtoday-each-durationdaysdatetimezoneutcnow---datetime"></a>"Added Custom" = Table.AddColumn(#"Lowercased Text", "DaysFromToday", each Duration.Days(DateTimeZone.UtcNow() - [Datetime])),
   > # <a name="changed-type2--tabletransformcolumntypesadded-customdaysfromtoday-int64type"></a>"Changed Type2" = Table.TransformColumnTypes(#"Added Custom",{{"DaysFromToday", Int64.Type}})
   > mergulhar
   >
   > # <a name="changed-type2"></a>"Changed Type2"
   >
8. Clique em Concluído
9. Selecione agora "insights-metrics-pt1m" na última das consultas à esquerda e abra o editor Avançado novamente. Mantenha as duas primeiras linhas e substitua o restante pela seguinte consulta:

   > # <a name="insights-metrics-pt1m1--sourcenameinsights-metrics-pt1mdata"></a>"insights-metrics-pt1m1" = Source{[Name="insights-metrics-pt1m"]}[Data],
   > # <a name="sorted-rows--tablesortinsights-metrics-pt1m1date-modified-orderdescending"></a>"Sorted Rows" = Table.Sort(#"insights-metrics-pt1m1",{{"Date modified", Order.Descending}}),
   > # <a name="kept-first-rows--tablefirstnsorted-rows744"></a>"Kept First Rows" = Table.FirstN(#"Sorted Rows",744),
   > # <a name="removed-columns--tableremovecolumnskept-first-rowsname-extension-date-accessed-date-modified-date-created-attributes-folder-path"></a>"Removed Columns" = Table.RemoveColumns(#"Kept First Rows",{"Name", "Extension", "Date accessed", "Date modified", "Date created", "Attributes", "Folder Path"}),
   > # <a name="parsed-json--tabletransformcolumnsremoved-columnsjsondocument"></a>"Parsed JSON" = Table.TransformColumns(#"Removed Columns",{},Json.Document),
   > # <a name="expanded-content--tableexpandrecordcolumnparsed-json-content-records-records"></a>"Expanded Content" = Table.ExpandRecordColumn(#"Parsed JSON", "Content", {"records"}, {"records"}),
   > # <a name="expanded-records--tableexpandlistcolumnexpanded-content-records"></a>"Expanded records" = Table.ExpandListColumn(#"Expanded Content", "records"),
   > # <a name="expanded-records1--tableexpandrecordcolumnexpanded-records-records-resourceid-metricname-time-average-minimum-maximum-total-count-timegrain-resourceid-metricname-time-average-minimum-maximum-total-count-timegrain"></a>"Expanded records1" = Table.ExpandRecordColumn(#"Expanded records", "records", {"resourceId", "metricName", "time", "average", "minimum", "maximum", "total", "count", "timeGrain"}, {"resourceId", "metricName", "time", "average", "minimum", "maximum", "total", "count", "timeGrain"}),
   > # <a name="filtered-rows--tableselectrowsexpanded-records1-each-metricname--latency"></a>"Filtered Rows" = Table.SelectRows(#"Expanded records1", each ([metricName] = "Latency")),
   > # <a name="removed-columns1--tableremovecolumnsfiltered-rowstimegrain"></a>"Removed Columns1" = Table.RemoveColumns(#"Filtered Rows",{"timeGrain"}),
   > # <a name="renamed-columns--tablerenamecolumnsremoved-columns1time-datetime-resourceid-resourceid-metricname-metricname-average-average-minimum-minimum-maximum-maximum-total-total-count-count"></a>"Renamed Columns" = Table.RenameColumns(#"Removed Columns1",{{"time", "Datetime"}, {"resourceId", "ResourceId"}, {"metricName", "MetricName"}, {"average", "Average"}, {"minimum", "Minimum"}, {"maximum", "Maximum"}, {"total", "Total"}, {"count", "Count"}}),
   > # <a name="changed-type--tabletransformcolumntypesrenamed-columnsresourceid-type-text-metricname-type-text-datetime-type-datetimezone-average-type-number-minimum-int64type-maximum-int64type-total-int64type-count-int64type"></a>"Changed Type" = Table.TransformColumnTypes(#"Renamed Columns",{{"ResourceId", type text}, {"MetricName", type text}, {"Datetime", type datetimezone}, {"Average", type number}, {"Minimum", Int64.Type}, {"Maximum", Int64.Type}, {"Total", Int64.Type}, {"Count", Int64.Type}}),
   > Rounding = Table.TransformColumns(#"Changed Type",{{"Average", each Number.Round(_, 2)}}),
   >
   > # <a name="changed-type1--tabletransformcolumntypesroundingaverage-type-number"></a>"Changed Type1" = Table.TransformColumnTypes(Rounding,{{"Average", type number}}),
   > # <a name="inserted-date--tableaddcolumnchanged-type1-date-each-datetimedatedatetime-type-date"></a>"Inserted Date" = Table.AddColumn(#"Changed Type1", "Date", each DateTime.Date([Datetime]), type date)
   > mergulhar
   >
   > # <a name="inserted-date"></a>"Inserted Date"
   >
10. Clique em Concluído e selecione Fechar e Aplicar na guia Início.

11. Os dados dos últimos 30 dias estão prontos para serem consumidos. Vá em frente e crie algumas [visualizações](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-report-view/).

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre os parâmetros de sintaxe e consulta de pesquisa. Confira [Pesquisar Documentos (API REST da Pesquisa do Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx) para obter detalhes.

Saiba mais sobre como criar relatórios incríveis. Confira [Introdução ao Power BI Desktop](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-getting-started/) para obter detalhes

<!--Image references-->

[1]: ./media/search-traffic-analytics/SettingsBlade.png
[2]: ./media/search-traffic-analytics/DiagnosticsBlade.png
[3]: ./media/search-traffic-analytics/ResourceId.png
[4]: ./media/search-traffic-analytics/Dashboard.png
[5]: ./media/search-traffic-analytics/GetData.png
[6]: ./media/search-traffic-analytics/BlobStorage.png
[7]: ./media/search-traffic-analytics/QueryEditor.png



<!--HONumber=Nov16_HO3-->


