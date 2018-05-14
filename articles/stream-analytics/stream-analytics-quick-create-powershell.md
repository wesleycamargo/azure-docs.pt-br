---
title: Criar um trabalho do Stream Analytics usando o Azure PowerShell
description: Este início rápido demonstra em detalhes como usar o módulo do Azure PowerShell para implantar e executar um trabalho do Azure Stream Analytics.
services: stream-analytics
author: SnehaGunda
ms.author: sngun
ms.date: 03/16/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
manager: kfile
ms.openlocfilehash: 0be8cee9e6c7874282f4e8f43f75fa7f2490c14e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-azure-powershell"></a>Início Rápido: criar um trabalho do Stream Analytics usando o Azure PowerShell

O módulo do Azure PowerShell é usado para criar e gerenciar recursos do Azure usando cmdlets ou scripts do PowerShell. Este início rápido demonstra em detalhes como usar o módulo do Azure PowerShell para implantar e executar um trabalho do Azure Stream Analytics.

## <a name="before-you-begin"></a>Antes de começar

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita.](https://azure.microsoft.com/free/)  

* Este início rápido requer o módulo Azure PowerShell versão 3.6 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para localizar a versão que está instalada em seu computador local. Se você precisar instalá-lo ou atualizá-lo, confira o artigo [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). O cenário deste artigo descreve a leitura de dados do armazenamento de blobs, a transformação dos dados e a gravação em um contêiner diferente no mesmo armazenamento de blobs.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Faça logon na sua assinatura do Azure com o comando `Connect-AzureRmAccount` e insira suas credenciais do Azure no navegador pop-up. Depois de entrar, se você tiver várias assinaturas, selecione a que deseja usar para este início rápido executando os cmdlets a seguir. Substitua <your subscription> pelo nome da sua assinatura:  

```powershell
# Log in to your Azure account
Connect-AzureRmAccount

# List all available subscriptions.
Get-AzureRmSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzureRmSubscription -SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

```powershell
$resourceGroup = "ASAPSRG"
$location = "WestUS2"
New-AzureRMResourceGroup `
  -Name $resourceGroup `
  -Location $location 
```

## <a name="prepare-the-input-data"></a>Preparar os dados de entrada

Antes de definir o trabalho do Stream Analytics, você deve preparar os dados que estão configurados como entrada do trabalho. Execute as seguintes etapas para preparar os dados de entrada exigidos pelo trabalho: 

1. Baixe os [dados de exemplo do sensor](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) do GitHub.  

2. Crie uma conta de armazenamento padrão de uso geral com replicação LRS usando o cmdlet [New-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/New-AzureRmStorageAccount)  

3. Recupere o contexto da conta de armazenamento que define a conta de armazenamento a ser usada. Ao trabalhar com contas de armazenamento, referencie o contexto em vez de fornecer as credenciais repetidamente. Esse exemplo cria uma conta de armazenamento chamada mystorageaccount com LRS (armazenamento com redundância local) e criptografia de blob (habilitada por padrão).  

4. Em seguida, crie um contêiner usando [New-AzureStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer), defina as permissões como ‘blob’ para permitir acesso público dos arquivos e carregue os [dados de exemplo do sensor](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) que você baixou anteriormente. 

Essas etapas são obtidas com a execução do seguinte script do PowerShell:

```powershell
$storageAccountName = "mystorageaccount"
$storageAccount = New-AzureRmStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
$containerName = "myinputcontainer"

New-AzureStorageContainer `
  -Name $containerName `
  -Context $ctx `
  -Permission blob

Set-AzureStorageBlobContent `
  -File "C:\HelloWorldASA-InputStream.json" `
  -Container $containerName `
  -Context $ctx  

$storageAccountKey = (Get-AzureRmStorageAccountKey `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName).Value[0]
```

## <a name="create-a-stream-analytics-job"></a>Criar um trabalho de Stream Analytics

Crie um trabalho do Stream Analytics com o cmdlet [New-AzureRMStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsjob?view=azurermps-5.4.0). Esse cmdlet usa o nome do trabalho, o nome do grupo de recursos e a definição de trabalho como parâmetros. O nome do trabalho pode ser qualquer nome amigável que identifica o trabalho. O nome do trabalho do Stream Analytics pode conter caracteres alfanuméricos, hifens e sublinhados e deve ter entre 3 e 63 caracteres. A definição de trabalho é um arquivo JSON que contém as propriedades necessárias para se criar um trabalho. Em seu computador local, crie um arquivo chamado "JobDefinition.json" e adicione os seguintes dados JSON a ele:

```json
{    
   "location":"Central US",  
   "properties":{    
      "sku":{    
         "name":"standard"  
      },  
      "eventsOutOfOrderPolicy":"drop",  
      "eventsOutOfOrderMaxDelayInSeconds":10,  
      "compatibilityLevel": 1.1
}
}
```

Em seguida, execute o cmdlet New-AzureRMStreamAnalyticsJob. Substitua o valor da variável jobDefinitionFile pelo caminho em que você armazenou o arquivo JSON de definição de trabalho. 

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzureRMStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  –File $jobDefinitionFile `
  –Name $jobName -Force 
```

## <a name="configure-input-to-the-job"></a>Configurar a entrada para o trabalho

Adicione uma entrada para seu trabalho usando o cmdlet [New-AzureRMStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsinput?view=azurermps-5.4.0). Esse cmdlet usa o nome do trabalho, o nome de entrada do trabalho, o nome do grupo de recursos e a definição da entrada do trabalho como parâmetros. A definição da entrada do trabalho é um arquivo JSON que contém as propriedades necessárias para configurar a entrada do trabalho. Neste exemplo, você criará um armazenamento de blobs como entrada. Em seu computador local, crie um arquivo chamado "JobInputDefinition.json" e adicione os dados JSON a seguir. Substitua o valor de **accountKey** pela chave de acesso da sua conta de armazenamento, que é o valor armazenado no valor $storageAccountKey. 

```json
{
    "properties": {
        "type": "Stream",
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                {
                   "accountName": "mystorageaccount",
                   "accountKey":"<Your storage account key>"
                }],
                "container": "myinputcontainer",
                "pathPattern": "",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8"
            }
        }
    },
    "name": "MyBlobInput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/inputs"
}
```

Em seguida, execute o cmdlet New-AzureRMStreamAnalyticsInput. Substitua o valor da variável jobDefinitionFile pelo caminho em que você armazenou o arquivo JSON de definição de entrada do trabalho. 

```powershell
$jobInputName = "MyBlobInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzureRMStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName 
```

## <a name="configure-output-to-the-job"></a>Configurar a saída para o trabalho

Adicione uma saída ao trabalho usando o cmdlet [New-AzureRmStreamAnalyticsOutput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsoutput?view=azurermps-5.4.0). Esse cmdlet usa o nome do trabalho, o nome de saída do trabalho, o nome do grupo de recursos e a definição da saída do trabalho como parâmetros. A definição da saída do trabalho é um arquivo JSON que contém as propriedades necessárias para configurar a saída do trabalho. Neste exemplo, você criará um armazenamento de blobs como saída. Em seu computador local, crie um arquivo chamado "JobOutputDefinition.json" e adicione os dados JSON a seguir. Substitua o valor de **accountKey** pela chave de acesso da sua conta de armazenamento, que é o valor armazenado no valor $storageAccountKey. 

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "mystorageaccount",
                      "accountKey": "<Your storage account key>"
                    }],
                "container": "myoutputcontainer",
                "pathPattern": "",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8",
                "format": "LineSeparated"
            }
        }
    },
    "name": "MyBlobOutput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/outputs"
}
```

Em seguida, execute o cmdlet New-AzureRMStreamAnalyticsOutput. Substitua o valor da variável jobOutputDefinitionFile pelo caminho em que você armazenou o arquivo JSON de definição de saída do trabalho. 

```powershell
$jobOutputName = "MyBlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzureRMStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  –JobName $jobName `
  –File $jobOutputDefinitionFile `
  –Name $jobOutputName -Force 
```

## <a name="define-the-transformation-query"></a>Definir a consulta de transformação

Adicione uma transformação ao trabalho usando o cmdlet [New-AzureRmStreamAnalyticsTransformation](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticstransformation?view=azurermps-5.4.0). Esse cmdlet usa o nome do trabalho, o nome da transformação do trabalho, o nome do grupo de recursos e a definição da transformação do trabalho como parâmetros. Em seu computador local, crie um arquivo chamado "JobTransformationDefinition.json" e adicione os dados JSON a seguir a ele. O arquivo JSON contém um parâmetro de consulta que define a consulta de transformação:

```json
{     
   "name":"MyTransformation",  
   "type":"Microsoft.StreamAnalytics/streamingjobs/transformations",  
   "properties":{    
      "streamingUnits":1,  
      "script":null,  
      "query":" SELECT System.Timestamp AS OutputTime, dspl AS SensorName, Avg(temp) AS AvgTemperature INTO MyBlobOutput FROM MyBlobInput TIMESTAMP BY time GROUP BY TumblingWindow(second,30),dspl HAVING Avg(temp)>100"  
   }  
}
```

Em seguida, execute o cmdlet New-AzureRMStreamAnalyticsTransformation. Substitua o valor da variável jobTransformationDefinitionFile pelo caminho em que você armazenou o arquivo JSON de definição da transformação do trabalho. 

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzureRMStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  –JobName $jobName `
  –File $jobTransformationDefinitionFile `
  –Name $jobTransformationName -Force
```

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Iniciar o trabalho do Stream Analytics e verificar a saída

Inicie o trabalho usando o cmdlet [Start-AzureRMStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0). Esse cmdlet usa o nome do trabalho, o nome do grupo de recursos, o modo inicial de saída e a hora de início como parâmetros. OUtpputStartMode aceita dois valores: JobStartTime, CustomTime ou LastOutputEventTime. Para saber mais sobre ao que se refere cada um desses valores, confira a seção [parâmetros](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) na documentação do PowerShell. Neste exemplo, você pode especificar o modo como CustomTime e fornecer um valor para OutputStartTime. 

Para o valor temporal, selecione um dia antes de quando você carregou o arquivo de armazenamento de blobs porque a hora em que o arquivo foi carregado é anterior à hora atual. Depois de executar o cmdlet a seguir, ele retornará "True" como saída se o trabalho for iniciado. Um contêiner denominado "myoutputcontainer" é criado na conta de armazenamento com os dados transformados. 

```powershell
Start-AzureRMStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode CustomTime `
  -OutputStartTime 2018-03-11T14:45:12Z 
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, exclua o grupo de recursos, o trabalho de streaming e todos os recursos relacionados. A exclusão do trabalho evita a cobrança das unidades de streaming consumidas por ele. Se você está planejando usar o trabalho no futuro, pode interrompê-lo e reiniciar mais tarde, quando necessário. Se você não for mais usar o trabalho, exclua todos os recursos criados neste início rápido executando o cmdlet a seguir:

```powershell
Remove-AzureRmResourceGroup `
  -Name $resourceGroup 
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou um trabalho do Stream Analytics simples. Para saber mais sobre como configurar outras fontes de entrada e executar detecção em tempo real, avance para o artigo a seguir:

> [!div class="nextstepaction"]
> [Detecção de fraude em tempo real usando o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
