---
title: Criar um data factory do Azure usando modelo do Resource Manager | Microsoft Docs
description: "Neste tutorial, você criará um pipeline de exemplo do Azure Data Factory usando um modelo do Azure Resource Manager."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/22/2018
ms.author: spelluru
ms.openlocfilehash: 5008d8f0ce36f09ff26bf10fadbf14da6b474ea9
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2018
---
# <a name="tutorial-create-an-azure-data-factory-using-azure-resource-manager-template"></a>Tutorial: Criar um data factory do Azure usando o modelo do Azure Resource Manager
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – já disponível](v1/data-factory-build-your-first-pipeline-using-arm.md)
> * [Versão 2 – Versão prévia](quickstart-create-data-factory-resource-manager-template.md) 

Este início rápido descreve como usar um modelo do Azure Resource Manager para criar um data factory do Azure. O pipeline que você criar nesse data factory **copia** dados de uma pasta para outra em um Armazenamento de Blobs do Azure. Para obter um tutorial sobre como **transformar** dados usando o Azure Data Factory, consulte [Tutorial: transformar dados usando Spark](transform-data-using-spark.md). 

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que já está amplamente disponível (GA), confira [compilar seu primeiro data factory com o Data Factory versão 1](v1/data-factory-build-your-first-pipeline-using-arm.md).
>
> Este artigo não fornece uma introdução detalhada do serviço Data Factory. Para obter uma introdução do serviço do Azure Data Factory, consulte [Introdução ao Azure Data Factory](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="azure-powershell"></a>Azure PowerShell
Instale os módulos mais recentes do Azure PowerShell seguindo as instruções em [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="resource-manager-templates"></a>Modelos do Gerenciador de Recursos
Para saber mais sobre modelos do Azure Resource Manager, confira [Criar modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). 

A seção a seguir fornece o modelo do Resource Manager completo para definir entidades de Data Factory de modo que você possa percorrer o tutorial rapidamente e testar o modelo. Para entender como cada entidade de Data Factory é definida, consulte a seção [Entidades de Data Factory no modelo](#data-factory-entities-in-the-template).

## <a name="data-factory-json"></a>JSON do Data Factory 
Crie um arquivo JSON denominado **ADFTutorialARM.json** na pasta **C:\ADFTutorial** com este conteúdo:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "dataFactoryName": {
            "type": "string",
            "metadata": {
                "description": "Name of the data factory. Must be globally unique."
            }
        },
        "dataFactoryLocation": {
            "type": "string",
            "allowedValues": [
                "East US",
                "East US 2",
                "West Europe"
            ],
            "defaultValue": "East US",
            "metadata": {
                "description": "Location of the data factory. Currently, only East US, East US 2, and West Europe are supported. "
            }
        },
        "storageAccountName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Azure storage account that contains the input/output data."
            }
        },
        "storageAccountKey": {
            "type": "securestring",
            "metadata": {
                "description": "Key for the Azure storage account."
            }
        },
        "blobContainer": {
            "type": "string",
            "metadata": {
                "description": "Name of the blob container in the Azure Storage account."
            }
        },
        "inputBlobFolder": {
            "type": "string",
            "metadata": {
                "description": "The folder in the blob container that has the input file."
            }
        },
        "inputBlobName": {
            "type": "string",
            "metadata": {
                "description": "Name of the input file/blob."
            }
        },
        "outputBlobFolder": {
            "type": "string",
            "metadata": {
                "description": "The folder in the blob container that will hold the transformed data."
            }
        },
        "outputBlobName": {
            "type": "string",
            "metadata": {
                "description": "Name of the output file/blob."
            }
        },
        "triggerStartTime": {
            "type": "string",
            "metadata": {
                "description": "Start time for the trigger."
            }
        },
        "triggerEndTime": {
            "type": "string",
            "metadata": {
                "description": "End time for the trigger."
            }
        }
    },
    "variables": {
        "azureStorageLinkedServiceName": "ArmtemplateStorageLinkedService",
        "inputDatasetName": "ArmtemplateTestDatasetIn",
        "outputDatasetName": "ArmtemplateTestDatasetOut",
        "pipelineName": "ArmtemplateSampleCopyPipeline",
        "triggerName": "ArmTemplateTestTrigger"
    },
    "resources": [{
        "name": "[parameters('dataFactoryName')]",
        "apiVersion": "2017-09-01-preview",
        "type": "Microsoft.DataFactory/factories",
        "location": "[parameters('dataFactoryLocation')]",
        "properties": {
            "loggingStorageAccountName": "[parameters('storageAccountName')]",
            "loggingStorageAccountKey": "[parameters('storageAccountKey')]"
        },
        "resources": [{
                "type": "linkedservices",
                "name": "[variables('azureStorageLinkedServiceName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "AzureStorage",
                    "description": "Azure Storage linked service",
                    "typeProperties": {
                        "connectionString": {
                            "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]",
                            "type": "SecureString"
                        }
                    }
                }
            },
            {
                "type": "datasets",
                "name": "[variables('inputDatasetName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "AzureBlob",
                    "typeProperties": {
                        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'), '/')]",
                        "fileName": "[parameters('inputBlobName')]"
                    },
                    "linkedServiceName": {
                        "referenceName": "[variables('azureStorageLinkedServiceName')]",
                        "type": "LinkedServiceReference"
                    }
                }
            },
            {
                "type": "datasets",
                "name": "[variables('outputDatasetName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "AzureBlob",
                    "typeProperties": {
                        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'), '/')]",
                        "fileName": "[parameters('outputBlobName')]"
                    },
                    "linkedServiceName": {
                        "referenceName": "[variables('azureStorageLinkedServiceName')]",
                        "type": "LinkedServiceReference"
                    }
                }
            },
            {
                "type": "pipelines",
                "name": "[variables('pipelineName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]",
                    "[variables('inputDatasetName')]",
                    "[variables('outputDatasetName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "activities": [{
                        "type": "Copy",
                        "typeProperties": {
                            "source": {
                                "type": "BlobSource"
                            },
                            "sink": {
                                "type": "BlobSink"
                            }
                        },
                        "name": "MyCopyActivity",
                        "inputs": [{
                            "referenceName": "[variables('inputDatasetName')]",
                            "type": "DatasetReference"
                        }],
                        "outputs": [{
                            "referenceName": "[variables('outputDatasetName')]",
                            "type": "DatasetReference"
                        }]
                    }]
                }
            },
            {
                "type": "triggers",
                "name": "[variables('triggerName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]",
                    "[variables('inputDatasetName')]",
                    "[variables('outputDatasetName')]",
                    "[variables('pipelineName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "ScheduleTrigger",
                    "typeProperties": {
                        "recurrence": {
                            "frequency": "Hour",
                            "interval": 1,
                            "startTime": "[parameters('triggerStartTime')]",
                            "endTime": "[parameters('triggerEndTime')]",
                            "timeZone": "UTC"
                        }
                    },
                    "pipelines": [{
                        "pipelineReference": {
                            "type": "PipelineReference",
                            "referenceName": "ArmtemplateSampleCopyPipeline"
                        },
                        "parameters": {}
                    }]
                }
            }
        ]
    }]
}
```

## <a name="parameters-json"></a>Parâmetros JSON
Crie um arquivo JSON chamado **ADFTutorialARM-Parameters.json** que contenha os parâmetros para o modelo do Azure Resource Manager.  

> [!IMPORTANT]
> - Especifique o nome e a chave da conta de armazenamento do Azure para os parâmetros **storageAccountName** e **storageAccountKey** nesse arquivo de parâmetro. Você criou o contêiner adftutorial e carregou o arquivo de exemplo (emp.txt) na pasta de entrada nesse armazenamento de blobs do Azure. 
> - Especifique um nome global exclusivo para o data factory para o parâmetro **dataFactoryName**. Por exemplo: ARMTutorialFactoryJohnDoe11282017. 
> - Para **triggerStartTime**, especifique o dia atual no formato: `2017-11-28T00:00:00`.
> - Para **triggerEndTime**, especifique o próximo dia no formato: `2017-11-29T00:00:00`. Você também pode verificar a hora UTC atual e especificar a próxima um ou duas horas como a hora de término. Por exemplo, se a hora UTC agora for 1h32, especifique `2017-11-29:03:00:00` como a hora de término. Nesse caso, o gatilho executa o pipeline duas vezes (às 2h e às 3h).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dataFactoryName": {
      "value": "<datafactoryname>"
    },    
    "dataFactoryLocation": {
      "value": "East US"
    },
    "storageAccountName": {
      "value": "<yourstroageaccountname>"
    },
    "storageAccountKey": {
      "value": "<yourstorageaccountkey>"
    },
    "blobContainer": {
      "value": "adftutorial"
    },
    "inputBlobFolder": {
      "value": "input"
    },
    "inputBlobName": {
      "value": "emp.txt"
    },
    "outputBlobFolder": {
      "value": "output"
    },
    "outputBlobName": {
      "value": "emp.txt"
    },
    "triggerStartTime": {
        "value": "2017-11-28T00:00:00. Set to today"
    },
    "triggerEndTime": {
        "value": "2017-11-29T00:00:00. Set to tomorrow"
    }
  }
}
```

> [!IMPORTANT]
> Você pode ter arquivos JSON de parâmetros separados para desenvolvimento, teste e ambientes de produção que pode usar com o mesmo modelo JSON do Data Factory. Usando um script do PowerShell, você pode automatizar a implantação de entidades de Data Factory nesses ambientes. 

## <a name="deploy-data-factory-entities"></a>Implantar entidades de data factory 
No PowerShell, execute o comando a seguir para implantar entidades do Data Factory usando o modelo do Resource Manager criado anteriormente neste início rápido. 

```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFTutorial\ADFTutorialARM.json -TemplateParameterFile C:\ADFTutorial\ADFTutorialARM-Parameters.json
```

Você verá uma saída semelhante ao exemplo a seguir: 

```
DeploymentName          : MyARMDeployment
ResourceGroupName       : ADFTutorialResourceGroup
ProvisioningState       : Succeeded
Timestamp               : 11/29/2017 3:11:13 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                 Type            Value
                          ===============      ============    ==========
                          dataFactoryName      String          <data factory name>
                          dataFactoryLocation  String          East US
                          storageAccountName   String          <storage account name>
                          storageAccountKey    SecureString
                          blobContainer        String          adftutorial
                          inputBlobFolder      String          input
                          inputBlobName        String          emp.txt
                          outputBlobFolder     String          output
                          outputBlobName       String          emp.txt
                          triggerStartTime     String          11/29/2017 12:00:00 AM
                          triggerEndTime       String          11/29/2017 4:00:00 AM

Outputs                 :
DeploymentDebugLogLevel :
```

## <a name="start-the-trigger"></a>Iniciar o gatilho

O modelo implanta as seguintes entidades de Data Factory: 

- Serviço vinculado de armazenamento do Azure
- Conjuntos de dados de Blob do Azure (entrada e saída)
- Pipeline com uma atividade de cópia
- Gatilho para disparar o pipeline

O gatilho implantado está no estado interrompido. Uma das maneiras de iniciar o gatilho é usar o cmdlet do PowerShell **Start-AzureRmDataFactoryV2Trigger**. O procedimento a seguir fornece as etapas detalhadas: 

1. Na janela do PowerShell, crie uma variável para conter o nome do grupo de recursos. Copie o seguinte comando na janela do PowerShell e pressione ENTER. Se você tiver especificado um nome de grupo de recursos diferente para o comando New-AzureRmResourceGroupDeployment, atualize o valor aqui. 

    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ``` 
1. Crie uma variável para conter o nome do data factory. Especifique o mesmo nome que você especificou no arquivo ADFTutorialARM-Parameters.json.   

    ```powershell
    $dataFactoryName = "<yourdatafactoryname>"
    ```
3. Defina uma variável para o nome do gatilho. O nome do gatilho está codificada no arquivo de modelo do Resource Manager (ADFTutorialARM.json).

    ```powershell
    $triggerName = "ArmTemplateTestTrigger"
    ```
4. Obtenha o **status do gatilho** executando o seguinte comando do PowerShell depois de especificar o nome de seu data factory e do gatilho:

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $triggerName
    ```

    Veja o exemplo de saída: 

    ```json
    TriggerName       : ArmTemplateTestTrigger
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ARMFactory1128
    Properties        : Microsoft.Azure.Management.DataFactory.Models.ScheduleTrigger
    RuntimeState      : Stopped
    ```
    
    Observe que o estado do tempo de execução do gatilho é **Parado**. 
5. **Iniciar o gatilho**. O gatilho executa o pipeline definido no modelo na hora. Ou seja, se você executou esse comando às 14h25, o gatilho executará o pipeline às 15h pela primeira vez. Em seguida, ele executa o pipeline a cada hora até a hora de término especificada para o gatilho. 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -TriggerName $triggerName
    ```
    
    Veja o exemplo de saída: 
    
    ```
    Confirm
    Are you sure you want to start trigger 'ArmTemplateTestTrigger' in data factory 'ARMFactory1128'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
    True
    ```
6. Confirme que o disparador foi iniciado executando novamente o comando Get-AzureRmDataFactoryV2Trigger.  

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -TriggerName $triggerName
    ```
    
    Veja o exemplo de saída:
    
    ```
    TriggerName       : ArmTemplateTestTrigger
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ARMFactory1128
    Properties        : Microsoft.Azure.Management.DataFactory.Models.ScheduleTrigger
    RuntimeState      : Started
    ```

## <a name="monitor-the-pipeline"></a>Monitorar o Pipeline
1. Após o logon no [portal do Azure](https://portal.azure.com/), clique em **Mais serviços**, pesquise com uma palavra-chave como **data fa** e selecione **Data factories**.

    ![Procurar no menu de data factories](media/quickstart-create-data-factory-resource-manager-template/browse-data-factories-menu.png)
2. Na página **Data Factories**, clique no data factory que você criou. Se for necessário, filtre a lista com o nome de seu data factory.  

    ![Selecionar o data factory](media/quickstart-create-data-factory-resource-manager-template/select-data-factory.png)
3. Na página Data factory, clique no bloco **Monitorar e Gerenciar**. 

    ![Bloco Monitorar e Gerenciar](media/quickstart-create-data-factory-resource-manager-template/monitor-manage-tile.png)
4. O **Aplicativo de Integração de Dados** deve abrir em uma guia separada do navegador da Web. Se a guia do monitor não estiver ativa, alterne para a **guia do monitor**. Observe que o pipeline de execução foi disparado por um **gatilho agendador**. 

    ![Monitorar execução do pipeline](media/quickstart-create-data-factory-resource-manager-template/monitor-pipeline-run.png)    

    > [!IMPORTANT]
    > Você verá as execuções de pipeline somente por hora (por exemplo: 4h, 5h, 6h etc.). Clique em **Atualizar** na barra de ferramentas para atualizar a lista quando o tempo atingir a próxima hora. 
5. Clique no link nas colunas **Ações**. 

    ![Link de ações do pipeline](media/quickstart-create-data-factory-resource-manager-template/pipeline-actions-link.png)
6. Você vê as execuções de atividade associadas à execução do pipeline. Neste guia de início rápido, o pipeline tem apenas uma atividade do tipo: Cópia. Portanto, você vê uma execução para essa atividade. 

    ![Execuções de atividade](media/quickstart-create-data-factory-resource-manager-template/activity-runs.png)
1. Clique no link sob a coluna **Saída**. Veja a saída da operação de cópia em uma janela **Saída**. Clique no botão maximizar para ver a saída completa. Você pode minimizar a janela de saída ou fechá-la. 

    ![Janela de Saída](media/quickstart-create-data-factory-resource-manager-template/output-window.png)
7. Interrompa o gatilho quando vir uma execução bem-sucedida/com falha. O gatilho executa o pipeline uma vez por hora. O pipeline copia o mesmo arquivo da pasta de entrada para a pasta de saída para cada execução. Para interromper o gatilho, execute o seguinte comando na janela do PowerShell. 
    
    ```powershell
    Stop-AzureRmDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $triggerName
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)] 

## <a name="json-definitions-for-entities"></a>Definições de JSON para entidades
As seguintes entidades de Data Factory são definidas no modelo JSON: 

- [Serviço vinculado de armazenamento do Azure](#azure-storage-linked-service)
- [Conjunto de dados de entrada de Blob do Azure](#azure-blob-input-dataset)
- [Conjunto de dados de saída de Blob do Azure](#azure-blob-output-dataset)
- [Pipeline com a Atividade de cópia](#data-pipeline)
- [Gatilho](#trigger)

#### <a name="azure-storage-linked-service"></a>Serviço vinculado de armazenamento do Azure
O AzureStorageLinkedService vincula sua conta do armazenamento do Azure ao data factory. Você criou um contêiner e carregou dados nessa conta de armazenamento como parte dos pré-requisitos. Especifique o nome e a chave da sua conta de armazenamento do Azure nesta seção. Consulte [Serviço vinculado de Armazenamento do Azure](connector-azure-blob-storage.md#linked-service-properties) para obter detalhes sobre os propriedades JSON usadas para definir um serviço vinculado de armazenamento do Azure. 

```json
{
    "type": "linkedservices",
    "name": "[variables('azureStorageLinkedServiceName')]",
    "dependsOn": [
        "[parameters('dataFactoryName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
        "type": "AzureStorage",
        "description": "Azure Storage linked service",
        "typeProperties": {
            "connectionString": {
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]",
                "type": "SecureString"
            }
        }
    }
}
```

A connectionString usa os parâmetros storageAccountName e storageAccountKey. Os valores para esses parâmetros são passados pelo uso de um arquivo de configuração. A definição também usa variáveis: azureStroageLinkedService e dataFactoryName definidos no modelo. 

#### <a name="azure-blob-input-dataset"></a>Conjunto de dados de entrada de Blob do Azure
O serviço vinculado do Armazenamento do Azure especifica a cadeia de conexão que o serviço Data Factory usa no tempo de execução para se conectar à sua conta do Armazenamento do Azure. Na definição de conjunto de dados do blob do Azure, especifique os nomes do contêiner de blob, da pasta e do arquivo que contém os dados de entrada. Confira [Propriedades de conjunto de dados de Blob do Azure](connector-azure-blob-storage.md#dataset-properties) para obter detalhes sobre os propriedades JSON usadas para definir um conjunto de dados de Blob do Azure. 

```json
{
    "type": "datasets",
    "name": "[variables('inputDatasetName')]",
    "dependsOn": [
    "[parameters('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'), '/')]",
        "fileName": "[parameters('inputBlobName')]"
    },
    "linkedServiceName": {
        "referenceName": "[variables('azureStorageLinkedServiceName')]",
        "type": "LinkedServiceReference"
    }
    }
},

```

#### <a name="azure-blob-output-dataset"></a>Conjunto de dados de saída de blob do Azure
Você pode especificar o nome da pasta no Armazenamento de Blobs do Azure que contém os dados copiados da pasta de entrada. Confira [Propriedades de conjunto de dados de Blob do Azure](connector-azure-blob-storage.md#dataset-properties) para obter detalhes sobre os propriedades JSON usadas para definir um conjunto de dados de Blob do Azure. 

```json
{
    "type": "datasets",
    "name": "[variables('outputDatasetName')]",
    "dependsOn": [
    "[parameters('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'), '/')]",
        "fileName": "[parameters('outputBlobName')]"
    },
    "linkedServiceName": {
        "referenceName": "[variables('azureStorageLinkedServiceName')]",
        "type": "LinkedServiceReference"
    }
    }
}
```

#### <a name="data-pipeline"></a>Pipeline de dados
Defina um pipeline que copia dados de um conjunto de dados de blobs do Azure para outro conjunto de dados de blobs do Azure. Confira [JSON de Pipeline](concepts-pipelines-activities.md#pipeline-json) para obter descrições dos elementos JSON usados para definir um pipeline neste exemplo. 

```json
{
    "type": "pipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
    "[parameters('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]",
    "[variables('inputDatasetName')]",
    "[variables('outputDatasetName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
    "activities": [
        {
        "type": "Copy",
        "typeProperties": {
            "source": {
            "type": "BlobSource"
            },
            "sink": {
            "type": "BlobSink"
            }
        },
        "name": "MyCopyActivity",
        "inputs": [
            {
            "referenceName": "[variables('inputDatasetName')]",
            "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
            "referenceName": "[variables('outputDatasetName')]",
            "type": "DatasetReference"
            }
        ]
        }
    ]
    }
}
```

#### <a name="trigger"></a>Gatilho
Defina um disparador que executa o pipeline uma vez por hora. O gatilho implantado está no estado interrompido. Inicie o gatilho usando o cmdlet **Start-AzureRmDataFactoryV2Trigger**. Para obter mais informações sobre gatilhos, consulte o artigo [Gatilhos e execução de pipeline](concepts-pipeline-execution-triggers.md#triggers). 

```json
{
    "type": "triggers",
    "name": "[variables('triggerName')]",
    "dependsOn": [
        "[parameters('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]",
        "[variables('inputDatasetName')]",
        "[variables('outputDatasetName')]",
        "[variables('pipelineName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Hour",
                "interval": 1,
                "startTime": "2017-11-28T00:00:00",
                "endTime": "2017-11-29T00:00:00",
                "timeZone": "UTC"               
            }
        },
        "pipelines": [{
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "ArmtemplateSampleCopyPipeline"
            },
            "parameters": {}
        }]
    }
}
```

## <a name="reuse-the-template"></a>Reutilizar o modelo
No tutorial, você criou um modelo para definir entidades de Data Factory e um modelo para passar valores para parâmetros. Para usar o mesmo modelo para implantar as entidades de Data Factory em ambientes diferentes, você cria um arquivo de parâmetro para cada ambiente e usa-o ao implantar esse ambiente.     

Exemplo:  

```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Dev.json

New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Test.json

New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Production.json
```
Observe que o primeiro comando usa o arquivo de parâmetro para o ambiente de desenvolvimento, outro para o ambiente de teste e um terceiro para o ambiente de produção.  

Também é possível reutilizar o modelo para executar tarefas repetidas. Por exemplo, crie vários data factories com um ou mais pipelines que implementem a mesma lógica, mas cada data factory usa contas de armazenamento do Azure diferentes. Nesse cenário, você usa o mesmo modelo no mesmo ambiente (desenvolvimento, teste ou produção) com arquivos de parâmetros diferentes para criar data factories. 


## <a name="next-steps"></a>Próximas etapas
O pipeline nessa amostra copia dados de uma localização para outra em um Armazenamento de Blobs do Azure. Percorra os [tutoriais](tutorial-copy-data-dot-net.md) para saber mais sobre o uso do Data Factory em mais cenários. 
