---
title: Data Factory - Log de alterações de API do .NET | Microsoft Docs
description: Descreve alterações significativas, novos recursos e correções de bugs etc... em uma versão específica de API do .NET para o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: spelluru
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2016
ms.author: spelluru

---
# Azure Data Factory - Log de alterações da API .NET
Este artigo fornece informações sobre alterações no SDK do Azure Data Factory em uma versão específica. Você pode encontrar o pacote NuGet mais recente do Azure Data Factory [aqui](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## Versão 4.11.0
Adições de Recursos:

* Os seguintes tipos de serviço vinculados foram adicionados:
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* Os seguintes tipos de conjunto de dados foram adicionados:
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* Os seguintes tipos de fonte de cópia a seguir foram adicionados:
  * [MongoDbSource](https://msdn.microsoft.com/pt-BR/library/mt765123.aspx)

## Versão 4.10.0
* As propriedades opcionais a seguir foram adicionadas a TextFormat:
  * [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* Os seguintes tipos de serviço vinculados foram adicionados:
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* Os seguintes tipos de conjunto de dados foram adicionados:
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* Os seguintes tipos de fonte de cópia a seguir foram adicionados:
  * [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* Adicionar a propriedade [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) a AzureMLBatchExecutionActivity
  * Habilitar passando várias entradas do serviço Web para uma experiência do Azure Machine Learning

## Versão 4.9.1
### Correção de bug
* Substitua a autenticação baseada na WebApi pelo [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## Versão 4.9.0
### Adições de recursos
* Adicione as propriedades [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) e [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) a CopyActivity. Consulte [Cópia em etapas](data-factory-copy-activity-performance.md#staged-copy) para obter detalhes sobre o recurso.

### Correção de bug
* Introduza uma sobrecarga do método [ActivityWindowOperationExtensions.List](https://msdn.microsoft.com/library/mt767915.aspx) que usa uma instância [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx).
* Marque [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) e [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) como opcionais em CopySink.

## Versão 4.8.0
### Adições de recursos
* As propriedades opcionais a seguir foram adicionadas ao tipo Atividade de cópia para permitir o ajuste de desempenho de cópia:
  * [ParallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## Versão 4.7.0
### Adições de recursos
* Adição do novo tipo StorageFormat [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) para copiar os arquivos no formato ORC (colunas com linhas otimizadas).
* Adicione as propriedades [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) e PolyBaseSettings a SqlDWSink.
  * Habilita o uso do PolyBase para copiar dados para o SQL Data Warehouse.

## Versão 4.6.1
### Correções de bug
* Corrige a solicitação HTTP para listar janelas de atividade.
  * Remove o nome do grupo de recursos e o nome do data factory da carga da solicitação.

## Versão 4.6.0
### Adições de recursos
* As propriedades a seguir foram adicionadas a [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx):
  * [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Conjunto de dados](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* As propriedades a seguir foram adicionadas a [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx):
  * [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* Adição de um novo tipo [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) do tipo [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) para definir os conjuntos de dados cujos dados estão no formato JSON.

## Versão 4.5.0
### Adições de recursos
* Adição da [lista de operações à janela de atividade](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
  * Métodos adicionados para recuperar as janelas de atividade com filtros baseados nos tipos de entidade (ou seja, fábricas de dados, conjuntos de dados, pipelines e atividades).
* Os seguintes tipos de serviço vinculados foram adicionados:
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Os seguintes tipos de conjunto de dados foram adicionados:
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Os seguintes tipos de fonte de cópia a seguir foram adicionados:
  * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## Versão 4.4.0
### Adições de recursos
* O seguinte tipo de serviço vinculado foi adicionado como fontes de dados e coletores para atividades de cópia:
  * [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Consulte [Serviço Vinculado de SAS de Armazenamento do Azure](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) para obter informações conceituais e exemplos.

## Versão 4.3.0
### Adições de recursos
* Os seguintes tipos de serviço vinculados foram adicionados como fontes de dados para atividades de cópia:
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Consulte [Mover dados do HDFS usando o Data Factory](data-factory-hdfs-connector.md) para obter informações conceituais e exemplos.
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Consulte [Mover dados dos armazenamentos de dados ODBC usando o Azure Data Factory](data-factory-odbc-connector.md) para obter informações conceituais e exemplos.

## Versão 4.2.0
### Adições de recursos
* O novo tipo de atividade a seguir foi adicionado: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Para obter detalhes sobre a atividade, consulte [Atualização dos modelos do ML do Azure usando a Atividade de Recurso de Atualização](data-factory-azure-ml-batch-execution-activity.md#updating-azure-ml-models-using-the-update-resource-activity).
* Uma nova propriedade opcional [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) foi adicionada ao [AzureMLLinkedService classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx).
* [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) e [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) propriedades foram adicionadas para a classe [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx).
* Permitir a configuração de tempos de limite para chamadas de cliente para o serviço de dados de fábrica.

## Versão 4.1.0
### Adições de recursos
* Os seguintes tipos de serviço vinculados foram adicionados:
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Os seguintes tipos de atividades foram adicionados:
  * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Os seguintes tipos de conjunto de dados foram adicionados:
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Os seguintes tipos de fonte e coletor de atividade para Atividade de Cópia foram adicionados:
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## Versão 4.0.1
### Alterações de última hora
As seguintes classes foram renomeadas. Os novos nomes eram os nomes das classes originais antes da versão 4.0.0.

| Nome no 4.0.0 | Nome no 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx) |
| AzureSqlDataset |[AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx) |
| AzureDataset |[AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx) |
| OracleDataset |[OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx) |
| RelationalDataset |[RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx) |
| SqlServerDataset |[SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx) |

## Versão 4.0.0
### Alterações de última hora
* As seguintes classes/interfaces foram renomeadas.

| Nome antigo | Novo nome |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |
| Tabela |[Conjunto de dados](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| TableProperties |[DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TableTypeProprerties |[DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| TableGetResponse |[DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| TableListResponse |[DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

* Os métodos **List** agora retornam resultados paginados. Se a resposta contiver uma propriedade não vazia **NextLink**, o aplicativo cliente precisa continuar buscando a próxima página até que todas as páginas sejam retornadas. Aqui está um exemplo:
  
        PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
        var pipelines = new List<Pipeline>(response.Pipelines);
  
        string nextLink = response.NextLink;
        while (string.IsNullOrEmpty(response.NextLink))
        {
            PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
            pipelines.AddRange(nextResponse.Pipelines);
  
            nextLink = nextResponse.NextLink;
        }
* O API da **Lista** retorna apenas o resumo de um pipeline, em vez de todos os detalhes. Por exemplo, as atividades em um resumo de pipeline contém apenas nome e tipo.

### Adições de recursos
* A classe [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) dá suporte a duas novas propriedades, **SliceIdentifierColumnName** e **SqlWriterCleanupScript**, para dar suporte à cópia idempotente no SQL Data Warehouse do Azure. Confira o artigo [SQL Data Warehouse do Azure](data-factory-azure-sql-data-warehouse-connector.md), especificamente, as seções [Mecanismo 1](data-factory-azure-sql-data-warehouse-connector.md#mechanism-1) e [Mecanismo 2](data-factory-azure-sql-data-warehouse-connector.md#mechanism-2) para saber mais sobre essas propriedades.
* Agora há suporte para executar o procedimento armazenado nas fontes do Banco de Dados SQL do Azure e Azure SQL Data Warehouse como parte da Atividade de Cópia. As classes [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) e [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) têm as seguintes propriedades: **SqlReaderStoredProcedureName** e **StoredProcedureParameters**. Confira os artigos [Banco de Dados SQL do Azure](data-factory-azure-sql-connector.md#sqlsource) e [SQL Data Warehouse do Azure](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) em Azure.com para saber mais sobre essas propriedades.

<!---HONumber=AcomDC_0921_2016-->