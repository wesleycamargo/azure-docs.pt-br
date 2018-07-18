---
title: Criar um trabalho do Stream Analytics usando o Azure PowerShell
description: Este início rápido demonstra em detalhes como usar o módulo do Azure PowerShell para implantar e executar um trabalho do Azure Stream Analytics.
services: stream-analytics
author: SnehaGunda
ms.author: sngun
ms.date: 05/14/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
manager: kfile
ms.openlocfilehash: 2b5d8bfd6dbe36637a0c6873e941118e7ee71b80
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212425"
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-azure-powershell"></a>Início Rápido: criar um trabalho do Stream Analytics usando o Azure PowerShell

O módulo do Azure PowerShell é usado para criar e gerenciar recursos do Azure usando cmdlets ou scripts do PowerShell. Este início rápido demonstra em detalhes como usar o módulo do Azure PowerShell para implantar e executar um trabalho do Azure Stream Analytics. 

O trabalho de exemplo lê dados de streaming de um blob no armazenamento de blobs do Azure. O arquivo de dados de entrada usado neste guia de início rápido contém dados estáticos apenas para fins ilustrativos. Em um cenário do mundo real, você usa o fluxo de dados de entrada para um trabalho do Stream Analytics. Em seguida, o trabalho transforma os dados usando a linguagem de consulta do Stream Analytics para calcular a média de temperatura quando acima de 100 °. Finalmente, ele grava os eventos de saída resultantes em outro arquivo. 

## <a name="before-you-begin"></a>Antes de começar

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita.](https://azure.microsoft.com/free/)  

* Este início rápido requer o módulo Azure PowerShell versão 3.6 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para localizar a versão que está instalada em seu computador local. Se você precisar instalá-lo ou atualizá-lo, confira o artigo [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). 


## <a name="sign-in-to-azure"></a>Entrar no Azure

Faça logon na sua assinatura do Azure com o comando `Connect-AzureRmAccount` e insira suas credenciais do Azure no navegador pop-up:

```powershell
# Log in to your Azure account
Connect-AzureRmAccount
```

Depois de entrar, se você tiver várias assinaturas, selecione a que deseja usar para este início rápido executando os cmdlets a seguir. Substitua <your subscription name> pelo nome da sua assinatura:  

```powershell
# List all available subscriptions.
Get-AzureRmSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzureRmSubscription -SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

```powershell
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzureRmResourceGroup `
   -Name $resourceGroup `
   -Location $location 
```

## <a name="prepare-the-input-data"></a>Preparar os dados de entrada

Antes de definir o trabalho do Stream Analytics, prepare os dados que estão configurados como entrada do trabalho.

1. Baixe os [dados de exemplo do sensor](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) do GitHub. Clique no link com o botão direito do mouse para **Salvar Link Como...** ou **Salvar destino como**.

2. O bloco de código do PowerShell a seguir usa vários comandos para preparar os dados de entrada exigidos pelo trabalho. Examine as seções para entender o código. 

   1. Crie uma conta de armazenamento padrão de uso geral usando o cmdlet [New-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/New-AzureRmStorageAccount).  Esse exemplo cria uma conta de armazenamento chamada mystorageaccount com LRS (armazenamento com redundância local) e criptografia de blob (habilitada por padrão).  

   2. Recupere o contexto da conta de armazenamento `$storageAccount.Context` que define a conta de armazenamento a ser usada. Ao trabalhar com contas de armazenamento, referencie o contexto em vez de fornecer as credenciais repetidamente. 

   3. Crie um contêiner de armazenamento usando [New-AzureStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer)e carregue os [dados de exemplo do sensor](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) baixados anteriormente. 

   4. Copie a chave de armazenamento que é enviada pelo código e cole-a nos arquivos JSON para criar a entrada e as saídas do trabalho de streaming posteriormente.

   ```powershell
   $storageAccountName = "mystorageaccount"
   $storageAccount = New-AzureRmStorageAccount `
     -ResourceGroupName $resourceGroup `
     -Name $storageAccountName `
     -Location $location `
     -SkuName Standard_LRS `
     -Kind Storage
   
   $ctx = $storageAccount.Context
   $containerName = "streamanalytics"
   
   New-AzureStorageContainer `
     -Name $containerName `
     -Context $ctx
   
   Set-AzureStorageBlobContent `
     -File "c:\HelloWorldASA-InputStream.json" `
     -Blob "input/HelloWorldASA-InputStream.json" `
     -Container $containerName `
     -Context $ctx  
   
   $storageAccountKey = (Get-AzureRmStorageAccountKey `
     -ResourceGroupName $resourceGroup `
     -Name $storageAccountName).Value[0]
   
   Write-Host "The <storage account key> placeholder needs to be replaced in your input and output json files with this key value:" 
   Write-Host $storageAccountKey -ForegroundColor Cyan
   ```

## <a name="create-a-stream-analytics-job"></a>Criar um trabalho de Stream Analytics

Crie um trabalho do Stream Analytics com o cmdlet [New-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsjob?view=azurermps-5.4.0). Esse cmdlet usa o nome do trabalho, o nome do grupo de recursos e a definição de trabalho como parâmetros. O nome do trabalho pode ser qualquer nome amigável que identifique o trabalho. Ele só pode conter caracteres alfanuméricos, hifens e sublinhados e deve ter entre 3 e 63 caracteres. A definição de trabalho é um arquivo JSON que contém as propriedades necessárias para se criar um trabalho. Em seu computador local, crie um arquivo chamado `JobDefinition.json` e adicione os seguintes dados JSON a ele:

```json
{    
   "location":"WestUS2",  
   "properties":{    
      "sku":{    
         "name":"standard"  
      },  
      "eventsOutOfOrderPolicy":"adjust",  
      "eventsOutOfOrderMaxDelayInSeconds":10,  
      "compatibilityLevel": 1.1
   }
}
```

Em seguida, execute o cmdlet `New-AzureRmStreamAnalyticsJob`. Substitua o valor da variável `jobDefinitionFile` pelo caminho em que você armazenou o arquivo JSON. 

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzureRmStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -File $jobDefinitionFile `
  -Name $jobName `
  -Force 
```

## <a name="configure-input-to-the-job"></a>Configurar a entrada para o trabalho

Adicione uma entrada para seu trabalho usando o cmdlet [New-AzureRmStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsinput?view=azurermps-5.4.0). Esse cmdlet usa o nome do trabalho, o nome de entrada do trabalho, o nome do grupo de recursos e a definição da entrada do trabalho como parâmetros. A definição de entrada do trabalho é um arquivo JSON que contém as propriedades necessárias para configurar a entrada do trabalho. Neste exemplo, você criará um armazenamento de blobs como uma entrada. 

Em seu computador local, crie um arquivo chamado `JobInputDefinition.json` e adicione os seguintes dados JSON a ele. Substitua o valor de `accountKey` pela chave de acesso da sua conta de armazenamento, que é o valor armazenado em $storageAccountKey. 

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
                   "accountKey":"<storage account key>"
                }],
                "container": "streamanalytics",
                "pathPattern": "input/",
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

Em seguida, execute o cmdlet `New-AzureRmStreamAnalyticsInput`; substitua o valor da variável `jobDefinitionFile` pelo caminho em que você armazenou o arquivo JSON de definição de entrada do trabalho. 

```powershell
$jobInputName = "MyBlobInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzureRmStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName 
```

## <a name="configure-output-to-the-job"></a>Configurar a saída para o trabalho

Adicione uma saída ao trabalho usando o cmdlet [New-AzureRmStreamAnalyticsOutput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsoutput?view=azurermps-5.4.0). Esse cmdlet usa o nome do trabalho, o nome de saída do trabalho, o nome do grupo de recursos e a definição da saída do trabalho como parâmetros. A definição de saída do trabalho é um arquivo JSON que contém as propriedades necessárias para configurar a saída do trabalho. Este exemplo usa o armazenamento de blobs como saída. 

Em seu computador local, crie um arquivo chamado `JobOutputDefinition.json` e adicione os seguintes dados JSON a ele. Substitua o valor de `accountKey` pela chave de acesso da sua conta de armazenamento, que é o valor armazenado em $storageAccountKey. 

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "mystorageaccount",
                      "accountKey": "<storage account key>"
                    }],
                "container": "streamanalytics",
                "pathPattern": "output/",
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

Em seguida, execute o cmdlet `New-AzureRmStreamAnalyticsOutput`. Substitua o valor da variável `jobOutputDefinitionFile` pelo caminho em que você armazenou o arquivo JSON de definição da saída do trabalho. 

```powershell
$jobOutputName = "MyBlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzureRmStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobOutputDefinitionFile `
  -Name $jobOutputName -Force 
```

## <a name="define-the-transformation-query"></a>Definir a consulta de transformação

Adicione uma transformação ao trabalho usando o cmdlet [New-AzureRmStreamAnalyticsTransformation](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticstransformation?view=azurermps-5.4.0). Esse cmdlet usa o nome do trabalho, o nome da transformação do trabalho, o nome do grupo de recursos e a definição da transformação do trabalho como parâmetros. Em seu computador local, crie um arquivo chamado `JobTransformationDefinition.json` e adicione os seguintes dados JSON a ele. O arquivo JSON contém um parâmetro de consulta que define a consulta de transformação:

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

Em seguida, execute o cmdlet `New-AzureRmStreamAnalyticsTransformation`. Substitua o valor da variável `jobTransformationDefinitionFile` pelo caminho em que você armazenou o arquivo JSON de definição da transformação do trabalho. 

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzureRmStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobTransformationDefinitionFile `
  -Name $jobTransformationName -Force
```

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Iniciar o trabalho do Stream Analytics e verificar a saída

Inicie o trabalho usando o cmdlet [Start-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0). Esse cmdlet usa o nome do trabalho, o nome do grupo de recursos, o modo inicial de saída e a hora de início como parâmetros. `OutputStartMode` aceita valores de `JobStartTime`, `CustomTime` ou `LastOutputEventTime`. Para saber mais sobre o que cada um desses valores referencia, confira a seção [Parâmetros](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) na documentação do PowerShell. Neste exemplo, especifique o modo como `CustomTime` e forneça um valor para `OutputStartTime`. 

Para o valor de tempo, selecione `2018-01-01`. A data de início é escolhida porque ela precede o carimbo de hora do evento dos dados de exemplo. Depois de executar o cmdlet a seguir, ele retornará `True` como saída se o trabalho for iniciado. No contêiner de armazenamento, uma pasta de saída é criada com os dados transformados. 

```powershell
Start-AzureRmStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode CustomTime `
  -OutputStartTime 2018-01-01T00:00:00Z 
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, exclua o grupo de recursos, o trabalho de streaming e todos os recursos relacionados. A exclusão do trabalho evita a cobrança das unidades de streaming consumidas por ele. Se você estiver planejando usar o trabalho no futuro, ignore a exclusão e pare o trabalho agora. Se você não for mais usar o trabalho, exclua todos os recursos criados neste início rápido executando o cmdlet a seguir:

```powershell
Remove-AzureRmResourceGroup `
  -Name $resourceGroup 
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você implantou um trabalho simples do Stream Analytics. Para saber mais sobre como configurar outras fontes de entrada e executar detecção em tempo real, avance para o artigo a seguir:

> [!div class="nextstepaction"]
> [Detecção de fraude em tempo real usando o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
