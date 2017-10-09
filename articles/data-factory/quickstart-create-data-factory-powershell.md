---
title: Criar um Azure Data Factory usando o PowerShell | Microsoft Docs
description: Crie um Azure Data Factory para copiar dados de um local em um Armazenamento de Blobs do Azure para outro local no mesmo Armazenamento de Blobs.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 92f798244db1f69d01f46d0c0bcce9fe139bef05
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---
# <a name="create-a-data-factory-and-pipeline-using-powershell"></a>Criar um data factory e um pipeline usando o PowerShell
O Azure Data Factory é um serviço de integração de dados baseado em nuvem que permite que você crie fluxos de trabalho controlados por dados na nuvem para orquestrar e automatizar a movimentação e a transformação de dados. Usando o Azure Data Factory, você pode criar e agendar fluxos de trabalho controlados por dados (chamados de pipelines) que podem ingerir dados de armazenamentos de dados diferentes, processar/transformar os dados usando serviços de computação como o Hadoop do Azure HDInsight, o Spark, o Azure Data Lake Analytics e o Azure Machine Learning e publicar os dados de saída em armazenamentos de dados como o SQL Data Warehouse do Azure para consumo pelos aplicativos de BI (business intelligence). 

Este guia de início rápido descreve como usar o PowerShell para criar um Azure Data Factory. O pipeline nesse data factory copia dados de uma localização para outra em um Armazenamento de Blobs do Azure.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* **Conta de Armazenamento do Azure**. Você usa o armazenamento de blobs como ambos o armazenamento de dados de **origem** e o de **coletor**. Se você não tiver uma conta de armazenamento do Azure, consulte o artigo [Criar uma conta de armazenamento] sobre como criar uma. artigo (../storage/common/storage-create-storage-account.md#create-a-storage-account) para conhecer as etapas para criar uma.
* Crie um **contêiner de blob** no Armazenamento de Blobs, crie uma **pasta** de entrada no contêiner e carregue alguns arquivos na pasta. 
* **PowerShell do Azure**. Siga as instruções em [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps).
* [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/). Você pode usar essa ferramenta para se conectar ao Armazenamento de Blobs do Azure, criar um contêiner de blob, carregar o arquivo de entrada e verificar o arquivo de saída. 

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Inicie o **PowerShell**. Mantenha o Azure PowerShell aberto até o fim deste guia de início rápido. Se você fechar e reabrir, precisará executar os comandos novamente.

    Execute o comando a seguir e insira o nome de usuário e senha usados para entrar no portal do Azure:
        
    ```powershell
    Login-AzureRmAccount
    ```        
    Execute o comando abaixo para exibir todas as assinaturas dessa conta:

    ```powershell
    Get-AzureRmSubscription
    ```
    Execute o comando a seguir para selecionar a assinatura com a qual deseja trabalhar. Substitua **SubscriptionId** pela ID da assinatura do Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```
2. Execute o cmdlet **Set-AzureRmDataFactoryV2** para criar um data factory. Substitua os espaços reservados por seus próprios valores antes de executar o comando. Os **espaços reservados** foram substituídos por seus próprios valores. 

    ```powershell
    $resourceGroupName = "<your resource group to create the factory>";
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>";
    Set-AzureRmDataFactoryV2 -ResourceGroupName "<your resource group to create the factory>" -Location "East US" -Name "<specify the name of data factory to create. It must be globally unique.>" 
    ```

    Observe os seguintes pontos:

    * O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro a seguir, altere o nome e tente novamente.

        ```
        The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
        ```

    * Para criar instâncias do Data Factory, você precisa ser um colaborador ou administrador da assinatura do Azure.
    * Atualmente, o Data Factory V2 permite que você crie o data factory somente na região Leste dos EUA. Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outras regiões.

## <a name="create-a-linked-service"></a>Criar um serviço vinculado

Crie serviços vinculados em um data factory para vincular seus armazenamentos de dados e serviços de computação ao data factory. Neste guia de início rápido, você só precisa criar um serviço vinculado do Armazenamento do Azure para ser usado tanto como o repositório de origem quanto como o de coletor, denominado "AzureStorageLinkedService" na amostra.

1. Crie um arquivo JSON chamado **AzureStorageLinkedService.json** na pasta **C:\ADFv2QuickStartPSH** com o seguinte conteúdo: (crie a pasta ADFv2QuickStartPSH se ela ainda não existir). Antes de salvar o arquivo, substitua &lt;accountName&gt; e &lt;accountKey&gt; pelo nome e pela chave da sua conta de armazenamento do Azure, respectivamente.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>",
                    "type": "SecureString"
                }
            }
        }
    }
    ```

2. No **Azure PowerShell**, mude para a pasta **ADFv2QuickStartPSH**.

3. Execute o cmdlet **Set-AzureRmDataFactoryV2LinkedService** para criar o serviço vinculado **AzureStorageLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -DefinitionFile ".\AzureStorageLinkedService.json"
    ```

    Veja o exemplo de saída:

    ```
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-a-dataset"></a>Criar um conjunto de dados

Você define um conjunto de dados que representa os dados a copiar de uma origem para um coletor. Neste exemplo, esse conjunto de dados de Blob refere-se ao serviço vinculado do Armazenamento do Azure que você criou na etapa anterior. O conjunto de dados usa um parâmetro cujo valor é definido em uma atividade que consome o conjunto de dados. O parâmetro é usado para construir o "folderPath" que aponta para o local em que os dados residem/estão armazenados.

1. Crie um arquivo JSON denominado **BlobDataset.json** na pasta **C:\ADFv2QuickStartPSH** com o seguinte conteúdo:

    ```json
    {
        "name": "BlobDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": {
                    "value": "@{dataset().path}",
                    "type": "Expression"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "parameters": {
                "path": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. Para criar o conjunto de dados: **BlobDataset**, execute o cmdlet **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "BlobDataset" -DefinitionFile ".\BlobDataset.json"
    ```

    Veja o exemplo de saída:

    ```
    DatasetName       : BlobDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-a-pipeline"></a>Criar uma pipeline

Neste exemplo, este pipeline contém uma atividade e leva dois parâmetros – caminho do blob de entrada e caminho do blob de saída. Os valores para esses parâmetros são definidos quando o pipeline é disparado/executado. A atividade de cópia refere-se ao mesmo conjunto de dados de blob criado na etapa anterior como entrada e saída. Quando o conjunto de dados é usado como um conjunto de dados de entrada, o caminho de entrada é especificado. E quando o conjunto de dados é usado como um conjunto de dados de saída, o caminho de saída é especificado. 

1. Crie um arquivo JSON denominado **Adfv2QuickStartPipeline.json** na pasta **C:\ADFv2QuickStartPSH** com o seguinte conteúdo:

    ```json
    {
        "name": "Adfv2QuickStartPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToBlob",
                    "type": "Copy",
                    "inputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.inputPath"
                            },
                        "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "BlobDataset",
                            "parameters": {
                                "path": "@pipeline().parameters.outputPath"
                            },
                            "type": "DatasetReference"
                        }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    }
                }
            ],
            "parameters": {
                "inputPath": {
                    "type": "String"
                },
                "outputPath": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. Para criar o pipeline **Adfv2QuickStartPipeline**, execute o cmdlet **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "Adfv2QuickStartPipeline" -DefinitionFile ".\Adfv2QuickStartPipeline.json"
    ```

    Veja o exemplo de saída:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

## <a name="create-a-pipeline-run"></a>Criar uma execução de pipeline

Nesta etapa, você define valores para os parâmetros de pipeline **inputPath** e **outputPath** com os valores reais dos caminhos de blob de origem e de coletor. Em seguida, você cria uma execução de pipeline usando esses argumentos. 

1. Crie um arquivo JSON denominado **PipelineParameters.json** na pasta **C:\ADFv2QuickStartPSH** com o seguinte conteúdo:

    Substitua o valor de "inputPath" e "outputPath" pelo caminho de blob de origem e de coletor de/para o qual copiar dados antes de salvar o arquivo.

    ```json
    {
        "inputPath": "<the path to existing blob(s) to copy data from, e.g. containername/foldername>",
        "outputPath": "<the blob path to copy data to, e.g. containername/foldername>"
    }
    ```

2. Execute o cmdlet **Invoke-AzureRmDataFactoryV2Pipeline** para criar uma execução de pipeline e passar os valores de parâmetro. Ele também captura a ID da execução de pipeline para monitoramento futuro.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-a-pipeline-run"></a>Monitorar uma execução de pipeline

1. Execute o script a seguir para verificar continuamente o status da execução de pipeline até que ela termine de copiar os dados.

    ```powershell
    while ($True) {
        $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
        }

        Write-Host  "Pipeline is running...status: " $run.Status -foregroundcolor "Yellow"
        Start-Sleep -Seconds 30
    }
    ```

    Eis aqui a amostra de saída da execução de pipeline:

    ```
    Key                  : 00000000-0000-0000-0000-000000000000
    Timestamp            : 9/7/2017 8:31:26 AM
    RunId                : 000000000-0000-0000-0000-000000000000
    DataFactoryName      : <dataFactoryname>
    PipelineName         : Adfv2QuickStartPipeline
    Parameters           : {inputPath: <inputBlobPath>, outputPath: <outputBlobPath>}
    ParametersCount      : 2
    ParameterNames       : {inputPath, outputPath}
    ParameterNamesCount  : 2
    ParameterValues      : {<inputBlobPath>, <outputBlobPath>}
    ParameterValuesCount : 2
    RunStart             : 9/7/2017 8:30:45 AM
    RunEnd               : 9/7/2017 8:31:26 AM
    DurationInMs         : 41291
    Status               : Succeeded
    Message              :
    ```

2. Execute o script a seguir para recuperar os detalhes de execução da atividade de cópia, por exemplo, o tamanho dos dados lidos/gravados.

    ```powershell
    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```
3. Verifique se você vê uma saída semelhante à amostra de saída do resultado da execução de atividade a seguir:

    ```json
    Activity run details:
    ResourceGroupName : adf
    DataFactoryName   : <dataFactoryname>
    ActivityName      : CopyFromBlobToBlob
    Timestamp         : 9/7/2017 8:24:06 AM
    PipelineRunId     : 9b362a1d-37b5-449f-918c-53a8d819d83f
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, copyDuration, throughput...}
    LinkedServiceName :
    ActivityStart     : 9/7/2017 8:23:30 AM
    ActivityEnd       : 9/7/2017 8:24:06 AM
    Duration          : 36331
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 331452208
    "dataWritten": 331452208
    "copyDuration": 23
    "throughput": 14073.209
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)"
    ```

## <a name="verify-the-output"></a>Verificar a saída
Use ferramentas como o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para verificar se os blobs no inputBlobPath são copiados para outputBlobPath.

## <a name="clean-up-resources"></a>Limpar recursos
Limpe os recursos criados no Guia de início rápido de duas maneiras. Você pode excluir o [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md), que inclui todos os recursos no grupo de recursos. Se desejar manter os outros recursos intactos, exclua apenas o data factory que você criou neste tutorial.

Execute o comando a seguir para excluir o grupo de recursos inteiro: 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

Execute o comando a seguir para excluir somente o data factory: 

```powershell
Remove-AzureRmDataFactoryV2 -Name "<NameOfYourDataFactory>" -ResourceGroupName "<NameOfResourceGroup>"
```

## <a name="next-steps"></a>Próximas etapas
O pipeline nessa amostra copia dados de uma localização para outra em um Armazenamento de Blobs do Azure. Percorra os [tutoriais](tutorial-copy-data-dot-net.md) para saber mais sobre o uso do Data Factory em mais cenários. 
