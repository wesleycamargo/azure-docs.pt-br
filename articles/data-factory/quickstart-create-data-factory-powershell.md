---
title: Copiar dados no Armazenamento de Blobs com o Azure Data Factory | Microsoft Docs
description: Crie um Azure Data Factory para copiar dados de uma pasta para outra pasta em um Armazenamento de Blobs do Azure para outro local no mesmo Armazenamento de Blobs.
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
ms.date: 11/16/2017
ms.author: jingwang
ms.openlocfilehash: 254dcb6642afc19f434df837c9073d2dd7314313
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2017
---
# <a name="create-an-azure-data-factory-using-powershell"></a>Criar um Azure Data Factory usando o PowerShell 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – já disponível](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão 2 – Versão prévia](quickstart-create-data-factory-powershell.md)

Este guia de início rápido descreve como usar o PowerShell para criar um Azure Data Factory. O pipeline que você criar nesse data factory **copia** dados de uma pasta para outra em um Armazenamento de Blobs do Azure. Para obter um tutorial sobre como **transformar** dados usando o Azure Data Factory, consulte [Tutorial: transformar dados usando Spark](transform-data-using-spark.md). 

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que já está disponível (GA), confira [introdução ao Data Factory versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
>
> Este artigo não fornece uma introdução detalhada do serviço Data Factory. Para obter uma introdução do serviço do Azure Data Factory, consulte [Introdução ao Azure Data Factory](introduction.md).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-subscription"></a>Assinatura do Azure
Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

### <a name="azure-roles"></a>Funções do Azure
Para criar instâncias de Data Factory, a conta de usuário usada para fazer logon no Azure deve ser um membro das funções **colaborador** ou **proprietário**, ou um **administrador** da assinatura do Azure. No Portal do Azure, clique no seu **nome de usuário** no canto superior direito e selecione **Permissões** para exibir as permissões que você tem com a assinatura. Se tiver acesso a várias assinaturas, selecione a que for adequada. Para obter instruções de exemplo sobre como adicionar um usuário a uma função, consulte o artigo [Adicionar funções](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="azure-storage-account"></a>Conta de Armazenamento do Azure
Use uma Conta de Armazenamento do Azure para fins gerais (especificamente o Armazenamento de Blobs) como armazenamento de dados de **fonte** e **destino** neste guia de início rápido. Se você não tiver uma conta de fins gerais de armazenamento do Azure, consulte [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account) para saber sobre como criar uma. 

#### <a name="get-storage-account-name-and-account-key"></a>Obter o nome da conta de armazenamento e a chave da conta
Você usa o nome e a chave da sua conta de armazenamento do Azure neste início rápido. O procedimento a seguir fornece as etapas para obter o nome e a chave da sua conta de armazenamento. 

1. Abra um navegador da Web e navegue até o [portal do Azure](https://portal.azure.com). Faça logon usando seu nome de usuário e senha do Azure. 
2. Clique em **Mais serviços >** no menu à esquerda, filtre pela palavra-chave **Armazenamento** e selecione **Contas de armazenamento**.

    ![Pesquisar conta de armazenamento](media/quickstart-create-data-factory-powershell/search-storage-account.png)
3. Na lista de contas de armazenamento, filtre pela sua conta de armazenamento (se necessário) e, em seguida, selecione a **sua conta de armazenamento**. 
4. Na página da **Conta de armazenamento**, selecione **Chaves de acesso** no menu.

    ![Obter nome e chave da conta de armazenamento](media/quickstart-create-data-factory-powershell/storage-account-name-key.png)
5. Copie os valores dos campos **Nome da conta de armazenamento** e **key1** para a área de transferência. Cole-os em um bloco de notas ou qualquer outro editor e salve-os.  

#### <a name="create-input-folder-and-files"></a>Criar arquivos e pasta de entrada
Nesta seção, você cria um contêiner de blob chamado **adftutorial** no armazenamento de blobs do Azure. Em seguida, você cria uma pasta chamada **entrada** no contêiner e, em seguida, carrega um arquivo de exemplo na pasta de entrada. 

1. Na página **Conta de armazenamento**, alterne para a **Visão geral** e depois clique em **Blobs**. 

    ![Selecione a opção Blobs](media/quickstart-create-data-factory-powershell/select-blobs.png)
2. Na página **Serviço Blob**, clique em **+ Contêiner** na barra de ferramentas. 

    ![Botão Adicionar contêiner](media/quickstart-create-data-factory-powershell/add-container-button.png)    
3. Na caixa de diálogo **Novo contêiner**, insira **adftutorial** como o nome e clique em **OK**. 

    ![Inserir o nome do contêiner](media/quickstart-create-data-factory-powershell/new-container-dialog.png)
4. Clique em **adftutorial** na lista de contêineres. 

    ![Selecione o contêiner](media/quickstart-create-data-factory-powershell/seelct-adftutorial-container.png)
1. Na página **Contêiner**, clique em **Carregar** na barra de ferramentas.  

    ![Botão Carregar](media/quickstart-create-data-factory-powershell/upload-toolbar-button.png)
6. Na página **Carregar blob**, clique em **Avançado**.

    ![Clique no link Avançado](media/quickstart-create-data-factory-powershell/upload-blob-advanced.png)
7. Inicialize o **Bloco de notas** e crie um arquivo chamado **emp.txt** com o seguinte conteúdo: salve-o na pasta **c:\ADFv2QuickStartPSH**. Crie a pasta **ADFv2QuickStartPSH** caso ela ainda não exista.
    
    ```
    John, Doe
    Jane, Doe
    ```    
8. No Portal Azure, na página **Carregar blob**, procure e selecione o arquivo **emp.txt** para o arquivo **Campos**. 
9. Insira **entrada** como um valor do campo **Carregar para a pasta**. 

    ![Carregar configurações de blob](media/quickstart-create-data-factory-powershell/upload-blob-settings.png)    
10. Confirme que a pasta é **entrada** e o arquivo é **emp.txt** e clique em **Carregar**.
11. O arquivo **emp.txt** e o status do carregamento devem estar na lista. 
12. Feche a página **Carregar blob** clicando no **X** no canto superior. 

    ![Fechar a página Carregar blob](media/quickstart-create-data-factory-powershell/close-upload-blob.png)
1. Mantenha a página **Contêiner** aberta. Você a usa para verificar a saída no final do guia de início rápido. 

### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-azure-powershell"></a>Instalar o Azure Powershell
Instale o Azure PowerShell mais recente se você não o tiver em seu computador. 

1. No navegador da Web, navegue até a página [Downloads do SDK do Azure e SDKS](https://azure.microsoft.com/downloads/). 
2. Clique em **Instalar Windows** na seção **Ferramentas de linha de comando** -> **PowerShell**. 
3. Para instalar o Azure PowerShell, execute o arquivo **MSI**. 

Para saber mais, confira [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-azure-powershell"></a>Fazer logon no Azure PowerShell

1. Iniciar o **PowerShell** no seu computador. Mantenha o Azure PowerShell aberto até o fim deste guia de início rápido. Se você fechá-la e reabri-la, precisará executar esses comandos novamente.

    ![Inicializar o PowerShell](media/quickstart-create-data-factory-powershell/search-powershell.png)
1. Execute o comando a seguir e insira o mesmo nome de usuário e senha do Azure que você usa para entrar no Portal do Azure:
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. Se você tiver várias assinaturas do Azure, execute o comando a seguir para exibir todas as assinaturas dessa conta:

    ```powershell
    Get-AzureRmSubscription
    ```
3. Execute o comando a seguir para selecionar a assinatura com a qual deseja trabalhar. Substitua **SubscriptionId** pela ID da assinatura do Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Criar uma data factory
1. Defina uma variável para o nome do grupo de recursos que você usa nos comandos do PowerShell posteriormente. Copie o seguinte texto de comando para o PowerShell, especifique um nome para o [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) entre aspas duplas e, em seguida, execute o comando. Por exemplo: `"adfrg"`.
   
     ```powershell
    $resourceGroupName = "<Specify a name for the Azure resource group>";
    ```
2. Defina uma variável para o nome do data factory. 

    ```powershell
    $dataFactoryName = "<Specify a name for the data factory. It must be globally unique.>";
    ```
1. Defina uma variável para o local do data factory: 

    ```powershell
    $location = "East US"
    ```
4. Para criar o grupo de recursos do Azure, execute o seguinte comando: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 
    Se o grupo de recursos já existir, não convém substituí-lo. Atribua um valor diferente para a variável `$resourceGroupName` e execute o comando novamente. 
5. Para criar o data factory, execute o cmdlet **Set-AzureRmDataFactoryV2** a seguir: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

Observe os seguintes pontos:

* O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro a seguir, altere o nome e tente novamente.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Para criar instâncias de Data Factory, a conta de usuário usada para fazer logon no Azure deve ser um membro das funções **colaborador** ou **proprietário**, ou um **administrador** da assinatura do Azure.
* Atualmente, o Data Factory versão 2 permite que você crie os data factories somente nas regiões Leste dos EUA, Leste dos EUA 2 e Europa Ocidental. Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outras regiões.

## <a name="create-a-linked-service"></a>Criar um serviço vinculado

Crie serviços vinculados em um data factory para vincular seus armazenamentos de dados e serviços de computação ao data factory. Neste guia de início rápido, você cria um serviço vinculado do Armazenamento do Azure que é usado como armazenamento de origem e do coletor. O serviço vinculado tem as informações de conexão que o serviço do Data Factory usa no tempo de execução para se conectar a ele.

1. Crie um arquivo JSON chamado **AzureStorageLinkedService.json** na pasta **C:\ADFv2QuickStartPSH** com o seguinte conteúdo: (crie a pasta ADFv2QuickStartPSH se ela ainda não existir). 

    > [!IMPORTANT]
    > Antes de salvar o arquivo, substitua &lt;accountName&gt; e &lt;accountKey&gt; pelo nome e pela chave da sua conta de armazenamento do Azure, respectivamente.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net",
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
Nesta etapa, você define um conjunto de dados que representa os dados a copiar de uma origem para um coletor. O conjunto de dados é do tipo **AzureBlob**. Ele refere-se ao **serviço vinculado do Armazenamento do Azure** que você criou na etapa anterior. Ele usa um parâmetro para construir a propriedade **folderPath**. Para um conjunto de dados de entrada, a atividade de cópia no pipeline passa o caminho de entrada como um valor para esse parâmetro. De modo semelhante, para um conjunto de dados de saída, a atividade de cópia passa o caminho de saída como um valor para esse parâmetro. 

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
  
Neste guia de início rápido, você cria uma pipeline com uma atividade e usa dois parâmetros – caminho do blob de entrada e caminho do blob de saída. Os valores para esses parâmetros são definidos quando o pipeline é disparado/executado. A atividade de cópia usa o mesmo conjunto de dados de blob criado na etapa anterior como entrada e saída. Quando o conjunto de dados é usado como um conjunto de dados de entrada, o caminho de entrada é especificado. E quando o conjunto de dados é usado como um conjunto de dados de saída, o caminho de saída é especificado. 

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

    ```json
    {
        "inputPath": "adftutorial/input",
        "outputPath": "adftutorial/output"
    }
    ```
2. Execute o cmdlet **Invoke-AzureRmDataFactoryV2Pipeline** para criar uma execução de pipeline e passar os valores de parâmetro. O cmdlet retorna a ID da execução de pipeline para monitoramento futuro.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-the-pipeline-run"></a>Monitorar a execução de pipeline

1. Execute o script do PowerShell a seguir para verificar continuamente o status da execução de pipeline até que ela termine de copiar os dados. Copie/cole o script a seguir na janela do PowerShell e pressione ENTER. 

    ```powershell
    while ($True) {
        $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 10
    }
    ```

    Eis aqui a amostra de saída da execução de pipeline:

    ```
    Pipeline is running...status: InProgress
    Pipeline run finished. The status is:  Succeeded
    
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    RunId             : 0000000000-0000-0000-0000-0000000000000
    PipelineName      : Adfv2QuickStartPipeline
    LastUpdated       : 9/28/2017 8:28:38 PM
    Parameters        : {[inputPath, adftutorial/input], [outputPath, adftutorial/output]}
    RunStart          : 9/28/2017 8:28:14 PM
    RunEnd            : 9/28/2017 8:28:38 PM
    DurationInMs      : 24151
    Status            : Succeeded
    Message           :
    ```

    Se você vir o erro:
    ```
    Activity CopyFromBlobToBlob failed: Failed to detect region of linked service 'AzureStorage' : 'AzureStorageLinkedService' with error '[Region Resolver] Azure Storage failed to get address for DNS. Warning: System.Net.Sockets.SocketException (0x80004005): No such host is known
    ```
    execute as seguintes etapas: 
    1. No AzureStorageLinkedService.json, confirme se o nome e a chave da sua Conta de Armazenamento do Azure estão corretos. 
    2. Verifique se o formato da cadeia de conexão está correto. As propriedades, por exemplo, AccountName e AccountKey, são separadas por caracteres ponto e vírgula (`;`). 
    3. Se houver colchetes angulares ao redor do nome da conta e da chave de conta, remova-os. 
    4. Aqui há um exemplo de cadeia de conexão: 

        ```json
        "connectionString": {
            "value": "DefaultEndpointsProtocol=https;AccountName=mystorageaccountname;AccountKey=mystorageacountkey;EndpointSuffix=core.windows.net",
            "type": "SecureString"
        }
        ```
    5. Recrie o serviço vinculado seguindo as etapas da seção [Criar um serviço vinculado](#create-a-linked-service). 
    6. Execute novamente o pipeline seguindo as etapas da seção [Criar uma execução de pipeline](#create-a-pipeline-run). 
    7. Execute novamente o comando de monitoramento atual para monitorar a nova execução de pipeline. 
1. Execute o script a seguir para recuperar os detalhes de execução da atividade de cópia, por exemplo, o tamanho dos dados lidos/gravados.

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```
3. Verifique se você vê uma saída semelhante à amostra de saída do resultado da execução de atividade a seguir:

    ```json
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    ActivityName      : CopyFromBlobToBlob
    PipelineRunId     : 00000000000-0000-0000-0000-000000000000
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, copyDuration, throughput...}
    LinkedServiceName :
    ActivityRunStart  : 9/28/2017 8:28:18 PM
    ActivityRunEnd    : 9/28/2017 8:28:36 PM
    DurationInMs      : 18095
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 38
    "dataWritten": 38
    "copyDuration": 7
    "throughput": 0.01
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)"
    "usedCloudDataMovementUnits": 2
    "billedDuration": 14
    ```

## <a name="verify-the-output"></a>Verificar a saída
O pipeline cria automaticamente a pasta de saída no contêiner de blob adftutorial. Em seguida, ele copia o arquivo emp.txt da pasta de entrada para a pasta de saída. 

1. No Portal do Azure, na página de contêiner **adftutorial**, clique em **Atualizar** para ver a pasta de saída. 
    
    ![Atualizar](media/quickstart-create-data-factory-powershell/output-refresh.png)
2. Clique em **saída** na lista de pastas. 
2. Confirme que **emp.txt** tenha sido copiado para a pasta de saída. 

    ![Atualizar](media/quickstart-create-data-factory-powershell/output-file.png)

## <a name="clean-up-resources"></a>Limpar recursos
Limpe os recursos criados no Guia de início rápido de duas maneiras. Você pode excluir o [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md), que inclui todos os recursos no grupo de recursos. Se desejar manter os outros recursos intactos, exclua apenas o data factory que você criou neste tutorial.

Ao excluir um grupo de recursos, todos os recursos são excluídos, incluindo os data factories nele. Execute o comando a seguir para excluir o grupo de recursos inteiro: 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

Se deseja excluir apenas o data factory e não o grupo de recursos inteiro, execute o seguinte comando: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Próximas etapas
O pipeline nessa amostra copia dados de uma localização para outra em um Armazenamento de Blobs do Azure. Percorra os [tutoriais](tutorial-copy-data-dot-net.md) para saber mais sobre o uso do Data Factory em mais cenários. 
