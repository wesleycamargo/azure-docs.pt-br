---
title: 'Script do PowerShell: copiar dados em massa usando o Azure Data Factory | Microsoft Docs'
description: Esse script do PowerShell mostra como usar o Azure Data Factory para copiar dados em massa de um armazenamento de dados de origem para um armazenamento de dados de destino.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2017
ms.author: jingwang
ms.openlocfilehash: d2db5bced78a00c8acabc150752fe65e9515dff1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60480631"
---
# <a name="powershell-script---copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Script do PowerShell – copiar várias tabelas em massa usando o Azure Data Factory

Esse script do PowerShell de exemplo copia dados de diversas tabelas em um banco de dados SQL do Azure para um SQL Data Warehouse do Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

Consulte [tutorial: cópia em massa](../tutorial-bulk-copy.md#prerequisites) para conhecer os pré-requisitos para executar esse exemplo.

## <a name="sample-script"></a>Script de exemplo

> [!IMPORTANT]
> Esse script cria arquivos JSON que definem as entidades do Data Factory (serviço vinculado, conjunto de dados e pipeline) no disco rígido na pasta c:\.

```powershell
# Set variables with your own values
$resourceGroupName = "<resource group name>"
$dataFactoryName = "<data factory name>" # Name of the data factory must be globally unique
$dataFactoryNameLocation = "China East"

$azureSqlServer = "<azure sql server name>"
$azureSqlServerUser = "<azure sql server user>"
$azureSqlServerUserPassword = "<azure sql server password>"
$azureSqlDatabase = "<source azure sql database name>"
$azureSqlDataWarehouse = "<sink azure sql data warehouse name>"

$azureStorageAccount = "<Az.Storage accoutn name>"
$azureStorageAccountKey = "<Az.Storage account key>"

# No need to change values for these variables
$azureSqlDatabaseLinkedService = "AzureSqlDatabaseLinkedService"
$azureSqlDataWarehouseLinkedService = "AzureSqlDataWarehouseLinkedService"
$azureStorageLinkedService = "AzureStorageLinkedService"
$azureSqlDatabaseDataset = "AzureSqlDatabaseDataset"
$azureSqlDataWarehouseDataset = "AzureSqlDataWarehouseDataset"
$IterateAndCopySQLTablesPipeline = "IterateAndCopySQLTables"
$pipelineGetTableListAndTriggerCopyData = "GetTableListAndTriggerCopyData"


# create a resource gorup
New-AzResourceGroup -Name $resourceGroupName -Location $dataFactoryNameLocation

# create a data factory
$df = Set-AzDataFactory -ResourceGroupName $resourceGroupName -Location $dataFactoryNameLocation -Name $dataFactoryName

# create a linked service for Azure SQL Database (source)
$azureSQLDatabaseLinkedServiceDefinition = @"
{
    "name": "$azureSqlDatabaseLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:$azureSqlServer.database.windows.net,1433;Database=$azureSqlDatabase;User ID=$azureSqlServerUser;Password=$azureSqlServerUserPassword;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryLinkedService command. 
$azureSQLDatabaseLinkedServiceDefinition | Out-File c:\$azureSqlDatabaseLinkedService.json

## Creates an Az.Storage linked service
Set-AzDataFactoryLinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $azureSqlDatabaseLinkedService -File c:\$azureSqlDatabaseLinkedService.json


# create a linked service for Azure SQL Datawarehouse (sink)
$azureSQLDataWarehouseLinkedServiceDefinition = @"
{
    "name": "$azureSqlDataWarehouseLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:$azureSqlServer.database.windows.net,1433;Database=$azureSqlDataWarehouse;User ID=$azureSqlServerUser;Password=$azureSqlServerUserPassword;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryLinkedService command. 
$azureSQLDataWarehouseLinkedServiceDefinition | Out-File c:\$azureSqlDataWarehouseLinkedService.json

## Creates an linked service for Az.Storage Account. Interim storage to enable PolyBase
Set-AzDataFactoryLinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $azureSqlDataWarehouseLinkedService -File c:\$azureSqlDataWarehouseLinkedService.json

$storageLinkedServiceDefinition = @"
{
    "name": "$azureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=$azureStorageAccount;AccountKey=$azureStorageAccountKey",
                "type": "SecureString"
            }
        }
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryLinkedService command. 
$storageLinkedServiceDefinition | Out-File c:\$azureStorageLinkedService.json

## Creates an Az.Storage linked service
Set-AzDataFactoryLinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $azureStorageLinkedService -File c:\$azureStorageLinkedService.json


# create the input dataset (Azure SQL Database)
$azureSqlDatabaseDatasetDefiniton = @"
{
    "name": "$azureSqlDatabaseDataset",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "$azureSqlDatabaseLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "dummy"
        }
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryDataset command. 
$azureSqlDatabaseDatasetDefiniton | Out-File c:\$azureSqlDatabaseDataset.json

## Create a dataset in the data factory
Set-AzDataFactoryDataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $azureSqlDatabaseDataset -File "c:\$azureSqlDatabaseDataset.json"


# create the output dataset (Azure SQL Data Warehouse)
$azureSqlDataWarehouseDatasetDefiniton = @"
{
    "name": "$azureSqlDataWarehouseDataset",
    "properties": {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "$azureSqlDataWarehouseLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": {
                "value": "@{dataset().DWTableName}",
                "type": "Expression"
            }
        },
        "parameters":{
            "DWTableName":{
                "type":"String"
            }
        }
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryDataset command. 
$azureSqlDataWarehouseDatasetDefiniton | Out-File c:\$azureSqlDataWarehouseDataset.json

## Create a dataset in the data factory
Set-AzDataFactoryDataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $azureSqlDataWarehouseDataset -File "c:\$azureSqlDataWarehouseDataset.json"

# Create a pipeline in the data factory that copies data from source SQL Database to sink SQL Data Warehouse
$pipelineDefinition = @"
{
    "name": "$IterateAndCopySQLTablesPipeline",
    "properties": {
        "activities": [
            {
                "name": "IterateSQLTables",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "false",
                    "items": {
                        "value": "@pipeline().parameters.tableList",
                        "type": "Expression"
                    },
                    "activities": [
                        {
                            "name": "CopyData",
                            "description": "Copy data from SQL database to SQL DW",
                            "type": "Copy",
                            "inputs": [
                                {
                                    "referenceName": "$azureSqlDatabaseDataset",
                                    "type": "DatasetReference"
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "$azureSqlDataWarehouseDataset",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "DWTableName": "[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                    }
                                }
                            ],
                            "typeProperties": {
                                "source": {
                                    "type": "SqlSource",
                                    "sqlReaderQuery": "SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                },
                                "sink": {
                                    "type": "SqlDWSink",
                                    "preCopyScript": "TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]",
                                    "allowPolyBase": true
                                },
                                "enableStaging": true,
                                "stagingSettings": {
                                    "linkedServiceName": {
                                        "referenceName": "$azureStorageLinkedService",
                                        "type": "LinkedServiceReference"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "tableList": {
                "type": "Object"
            }
        }
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryPipeline command. 
$pipelineDefinition | Out-File c:\$IterateAndCopySQLTablesPipeline.json

## Create a pipeline in the data factory
Set-AzDataFactoryPipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $IterateAndCopySQLTablesPipeline -File "c:\$IterateAndCopySQLTablesPipeline.json"


# Create a pipeline in the data factory that retrieves a list of tables and invokes the above pipeline for each table to be copied
$pipeline2Definition = @"
{
    "name":"$pipelineGetTableListAndTriggerCopyData",
    "properties":{
        "activities":[
            { 
                "name": "LookupTableList",
                "description": "Retrieve the table list from Azure SQL dataabse",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'"
                    },
                    "dataset": {
                        "referenceName": "$azureSqlDatabaseDataset",
                        "type": "DatasetReference"
                    },
                    "firstRowOnly": false
                }
            },
            {
                "name": "TriggerCopy",
                "type": "ExecutePipeline",
                "typeProperties": {
                    "parameters": {
                        "tableList": {
                            "value": "@activity('LookupTableList').output.value",
                            "type": "Expression"
                        }
                    },
                    "pipeline": {
                        "referenceName": "$IterateAndCopySQLTablesPipeline",
                        "type": "PipelineReference"
                    },
                    "waitOnCompletion": true
                },
                "dependsOn": [
                    {
                        "activity": "LookupTableList",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ]
            }
        ]
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryPipeline command. 
$pipeline2Definition | Out-File c:\$pipelineGetTableListAndTriggerCopyData.json

## Create a pipeline in the data factory
Set-AzDataFactoryPipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $pipelineGetTableListAndTriggerCopyData -File "c:\$pipelineGetTableListAndTriggerCopyData.json"



# Create a pipeline run 

## JSON definition for dummy pipeline parameters
$pipelineParameters = @"
{
    "dummy":  "b"
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Invoke-AzDataFactoryPipeline command. 
$pipelineParameters | Out-File c:\PipelineParameters.json

# Create a pipeline run by using parameters
$runId = Invoke-AzDataFactoryPipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineGetTableListAndTriggerCopyData -ParameterFile c:\PipelineParameters.json

# Check the pipeline run status until it finishes the copy operation
Start-Sleep -Seconds 30
while ($True) {
    $result = Get-AzDataFactoryActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

    if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
        Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
        Start-Sleep -Seconds 30
    }
    else {
        Write-Host "Pipeline '$pipelineGetTableListAndTriggerCopyData' run finished. Result:" -foregroundcolor "Yellow"
        $result
        break
    }
}

# Get the activity run details 
    $result = Get-AzDataFactoryActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName `
        -PipelineRunId $runId `
        -RunStartedAfter (Get-Date).AddMinutes(-10) `
        -RunStartedBefore (Get-Date).AddMinutes(10) `
        -ErrorAction Stop

    $result

    if ($result.Status -eq "Succeeded") {`
        $result.Output -join "`r`n"`
    }`
    else {`
        $result.Error -join "`r`n"`
    }

# To remove the data factory from the resource gorup
# Remove-AzDataFactory -Name $dataFactoryName -ResourceGroupName $resourceGroupName
# 
# To remove the whole resource group
# Remove-AzResourceGroup  -Name $resourceGroupName
```

## <a name="clean-up-deployment"></a>Limpar a implantação

Depois de executar o exemplo de script, use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Para remover o data factory do grupo de recursos, execute o seguinte comando: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos: 

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-azdatafactoryv2) | Criar um data factory. |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-azdatafactoryv2linkedservice) | Cria um serviço vinculado no data factory. Um serviço vinculado vincula um armazenamento de dados ou uma computação a um data factory. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/set-azdatafactoryv2dataset) | Cria um conjunto de dados no data factory. Um conjunto de dados representa a entrada/saída para uma atividade em um pipeline. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/set-azdatafactoryv2pipeline) | Cria um pipeline no data factory. Um pipeline contém uma ou mais atividades que executam uma determinada operação. Nesse pipeline, a atividade de cópia copia dados de um local para outro no Armazenamento de Blobs do Azure. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-azdatafactoryv2pipeline) | Cria uma execução do pipeline. Em outras palavras, executa o pipeline. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-azdatafactoryv2activityrun) | Obtém os detalhes sobre a execução da atividade (execução da atividade) no pipeline. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Exemplos adicionais de scripts do Azure Data Factory PowerShell podem ser encontrados nos [Scripts do Azure Data Factory PowerShell](../samples-powershell.md).
