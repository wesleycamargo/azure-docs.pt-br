---
title: Configurar roteamento de mensagens com o Hub IoT do Azure (.NET) | Microsoft Docs
description: Configurar o roteamento de mensagens com o Hub IoT do Azure
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: a52ab4ff65312088e65d56006b6f99a7470b88f6
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43287243"
---
# <a name="tutorial-configure-message-routing-with-iot-hub"></a>Tutorial: Configurar o roteamento de mensagens com o Hub IoT

O roteamento de mensagens permite enviar dados de telemetria de seus dispositivos IoT para pontos de extremidade internos compatíveis com o Hub de Eventos ou pontos de extremidade personalizados, como o Armazenamento de Blob, Fila do Barramento de Serviço, Tópico de Barramento de Serviço e Hubs de Eventos. Ao configurar o roteamento de mensagens, você pode criar regras de roteamento para personalizar a rota que corresponde a um determinado tipo de regra. Uma vez configurado, os dados de entrada são roteados automaticamente para os pontos de extremidade pelo Hub IoT. 

Neste tutorial, você aprenderá como configurar e usar regras de roteamento com o Hub IoT. Você irá rotear mensagens de um dispositivo IoT para um dos vários serviços, incluindo o Armazenamento de Blob e uma fila do Barramento de Serviço. As mensagens na fila do Barramento de Serviço serão escolhidas por um aplicativo lógico e enviadas por e-mail. As mensagens que não tenham roteamento especificamente configurado são enviadas para o ponto de extremidade padrão e exibidas em uma visualização do Power BI.

Neste tutorial, você executa as seguintes tarefas:

> [!div class="checklist"]
> * Usando a CLI do Azure ou o PowerShell, configure os recursos básicos – um Hub IoT, uma conta de armazenamento, uma fila do Barramento de Serviço e um dispositivo simulado.
> * Configure pontos de extremidade e as rotas no Hub IoT para a conta de armazenamento e a fila do Barramento de Serviço.
> * Crie um aplicativo lógico que é disparado e envia e-mail quando uma mensagem é adicionada à fila de Barramento de Serviço.
> * Baixe e execute um aplicativo que simula um dispositivo IoT enviando mensagens para o hub para as diferentes opções de roteamentos.
> * Crie uma visualização do Power BI para os dados enviados para o ponto de extremidade padrão.
> * Exiba os resultados...
> * ... na fila do Barramento de Serviço e emails.
> * ... na conta de armazenamento.
> * ...na visualização do Power BI.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Instalar o [Visual Studio para Windows](https://www.visualstudio.com/). 

- Uma conta do Power BI para analisar a análise de fluxo do ponto de extremidade padrão. ([Experimente o Power BI gratuitamente](https://app.powerbi.com/signupredirect?pbi_source=web).)

- Uma conta do Office 365 para enviar e-mails de notificação. 

Você precisará da CLI do Azure ou do Azure PowerShell para executar as etapas de instalação para este tutorial. 

Para usar a CLI do Azure, embora você possa instalar a CLI do Azure localmente, é recomendável que você use o Azure Cloud Shell. O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar os scripts da CLI do Azure. As ferramentas comuns do Azure estão pré-instaladas e configuradas no Cloud Shell para uso com sua conta, portanto você não precisará instalá-las localmente. 

Para usar o PowerShell, instale-o localmente usando as instruções a seguir. 

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

Há algumas maneiras de abrir o Cloud Shell:

|  |   |
|-----------------------------------------------|---|
| Selecione **Experimente** no canto superior direito de um bloco de código. | ![Cloud Shell neste artigo](./media/tutorial-routing/cli-try-it.png) |
| Abra o Cloud Shell em seu navegador. | [![https://shell.azure.com/bash](./media/tutorial-routing/launchcloudshell.png)](https://shell.azure.com) |
| Selecione o botão **Cloud Shell** no menu no canto superior direito do [Portal do Azure](https://portal.azure.com). |    ![Cloud Shell no portal](./media/tutorial-routing/cloud-shell-menu.png) |
|  |  |

### <a name="using-azure-cli-locally"></a>Usando a CLI do Azure localmente

Se você preferir usar a CLI localmente em lugar de usar o Cloud Shell, você deve ter a CLI do Azure versão do módulo 2.0.30.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). 

### <a name="using-powershell-locally"></a>Usando o PowerShell localmente

Este tutorial requer o módulo do Azure PowerShell, versão 5.7 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisar instalá-lo ou atualizá-lo, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="set-up-resources"></a>Configurar recursos

Para este tutorial, você precisa de um Hub IoT, uma conta de armazenamento e uma fila do Barramento de Serviço. Esses recursos podem todos ser criados usando a CLI do Azure ou o Azure PowerShell. Use o mesmo grupo de recursos e o local para todos os recursos. Em seguida, no final, você pode remover tudo em uma única etapa, excluindo o grupo de recursos.

As seções a seguir descrevem como executar essas etapas necessárias. Siga as instruções da CLI *ou* do PowerShell.

1. Crie um [grupo de recursos](../azure-resource-manager/resource-group-overview.md). 

    <!-- When they add the Basic tier, change this to use Basic instead of Standard. -->

1. Criar um Hub IoT na camada S1. Adicionar um grupo de consumidores ao Hub IoT. O grupo de consumidores é usado pelo Azure Stream Analytics ao recuperar dados.

1. Crie uma conta de armazenamento padrão V1 com replicação Standard_LRS.

1. Criar um namespace do Barramento de Serviço e da fila. 

1. Crie uma identidade de dispositivo para o dispositivo simulado que envia mensagens para o hub. Salve a chave para a fase de teste.

### <a name="azure-cli-instructions"></a>Instruções da CLI do Azure

A maneira mais fácil de usar esse script é copiá-lo e colá-lo no Cloud Shell. Supondo que você já está conectado, ele executará o script de uma linha por vez. 

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-cli-iot-ext

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotHubConsumerGroup=ContosoConsumers
containerName=contosoresults
iotDeviceName=Contoso-Test-Device 

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$RANDOM
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# Add a consumer group to the IoT hub.
az iot hub consumer-group create --hub-name $iotHubName \
    --name $iotHubConsumerGroup

# The storage account name must be globally unique, so add a random number to the end.
storageAccountName=contosostorage$RANDOM
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key. 
#    You need this to create the container.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"') 

# See the value of the storage account key.
echo "$storageAccountKey"

# Create the container in the storage account. 
az storage container create --name $containerName \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off 

# The Service Bus namespace must be globally unique, so add a random number to the end.
sbNameSpace=ContosoSBNamespace$RANDOM
echo "Service Bus namespace = " $sbNameSpace

# Create the Service Bus namespace.
az servicebus namespace create --resource-group $resourceGroup \
    --name $sbNameSpace \
    --location $location
    
# The Service Bus queue name must be globally unique, so add a random number to the end.
sbQueueName=ContosoSBQueue$RANDOM
echo "Service Bus queue name = " $sbQueueName

# Create the Service Bus queue to be used as a routing destination.
az servicebus queue create --name $sbQueueName \
    --namespace-name $sbNameSpace \
    --resource-group $resourceGroup

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

```

### <a name="powershell-instructions"></a>Instruções do PowerShell

A maneira mais fácil de usar esse script é abrir o [PowerShell ISE](https://docs.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise?view=powershell-6), copiar o script na área de transferência e, em seguida, colar o script inteiro na janela de script. Você pode alterar os valores para os nomes dos recursos (se desejar) e executar o script inteiro. 

```azurepowershell-interactive
# Log into Azure account.
Login-AzureRMAccount

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"
$iotDeviceName = "Contoso-Test-Device"

# Create the resource group to be used 
#   for all resources for this tutorial.
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, so add a random number to the end.
$iotHubName = "ContosoTestHub$(Get-Random)"
Write-Host "IoT hub name is " $iotHubName

# Create the IoT hub.
New-AzureRmIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzureRmIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# The storage account name must be globally unique, so add a random number to the end.
$storageAccountName = "contosostorage$(Get-Random)"
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
$storageContext = $storageAccount.Context 

# Create the container in the storage account.
New-AzureStorageContainer -Name $containerName `
    -Context $storageContext

# The Service Bus namespace must be globally unique,
#   so add a random number to the end.
$serviceBusNamespace = "ContosoSBNamespace$(Get-Random)"
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzureRmServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random number to the end.
$serviceBusQueueName  = "ContosoSBQueue$(Get-Random)"
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzureRmServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName 

```

Em seguida, crie uma identidade de dispositivo e salve sua chave para uso posterior. Essa identidade de dispositivo é usada pelo aplicativo de simulação para enviar mensagens para o Hub IoT. Esse recurso não está disponível no PowerShell, mas você pode criar o dispositivo no [portal do Azure](https://portal.azure.com).

1. Abra o [portal do Azure](https://portal.azure.com) e faça logon na sua conta do Azure.

1. Clique em **grupos de recursos** e selecione o grupo de recursos. Este tutorial usa **ContosoResources**.

1. Na lista de recursos, clique em seu Hub IoT. Este tutorial usa **ContosoTestHub**. Selecione **dispositivos IoT** do painel de Hub.

1. Clique em **+ Adicionar**. No painel Adicionar dispositivo, preencha a ID do dispositivo. Este tutorial usa **Contoso-Test-Device**. Deixe em branco as chaves e verifique **Gerar chaves automaticamente**. Certifique-se de que **Conectar o dispositivo ao Hub IoT** está habilitado. Clique em **Salvar**.

   ![Captura de tela mostrando a tela adicionar dispositivo.](./media/tutorial-routing/add-device.png)

1. Agora que ela é criada, clique no dispositivo para ver as chaves geradas. Clique no ícone Copiar na chave primária e salve-o em algum lugar, como o bloco de notas para a fase de teste deste tutorial.

   ![Captura de tela mostrando os detalhes do dispositivo, incluindo as chaves.](./media/tutorial-routing/device-details.png)

## <a name="set-up-message-routing"></a>Configurar roteamento de mensagens

Você vai para rotear mensagens para recursos diferentes com base nas propriedades anexadas à mensagem pelo dispositivo simulado. As mensagens que não são roteadas de forma personalizada são enviadas ao ponto de extremidade padrão (mensagens/eventos). 

|value |Result|
|------|------|
|level="storage" |Grave no Armazenamento do Azure.|
|level="critical" |Grave em uma fila do Barramento de Serviço. Um aplicativo lógico recupera a mensagem da fila e usa o Office 365 para enviar a mensagem por e-mail.|
|padrão |Exiba esses dados usando o Power BI.|

### <a name="routing-to-a-storage-account"></a>Rotear para uma conta de armazenamento 

Agora, configure o roteamento para a conta de armazenamento. Definir um ponto de extremidade e, em seguida, configure uma rota para esse ponto de extremidade. As mensagens em que a propriedade **nível** está definida como **armazenamento** são gravados em uma conta de armazenamento automaticamente.

1. No [portal do Azure](https://portal.azure.com), clique em **Grupos de recursos**, selecione o grupo de recursos. Este tutorial usa **ContosoResources**. Clique no Hub IoT na lista de recursos. Este tutorial usa **ContosoTestHub**. Clique em **Pontos de Extremidade**. No painel **pontos de extremidade**, clique em **+ Adicionar**. Insira as seguintes informações:

   **Nome**: Insira um nome para o ponto de extremidade. Este tutorial usa **StorageContainer**.
   
   **Tipo de ponto de extremidade**: Selecione **Contêiner de Armazenamento do Azure** na lista suspensa.

   Clique em **Escolher um contêiner** para ver a lista de contas de armazenamento. Selecione sua conta de armazenamento. Este tutorial usa **contosostorage**. Em seguida, selecione o contêiner. Este tutorial usa **contosoresults**. Clique em **Selecionar**, que retorna para o painel **Adicionar ponto de extremidade**. 
   
   ![Captura de tela mostrando a adição de um ponto de extremidade.](./media/tutorial-routing/add-endpoint-storage-account.png)
   
   Clique em **OK** para terminar de adicionar o ponto de extremidade.
   
1. Clique em **Rotas** no seu Hub IoT. Você vai criar uma regra de roteamento que roteia mensagens para o contêiner de armazenamento que você acabou de adicionar como um ponto de extremidade. Clique em **+Adicionar**, na parte superior do painel de Rotas. Preencha os campos na tela. 

   **Nome**: insira um nome para a regra de roteamento. Este tutorial usa o **StorageRule**.

   **Fonte de dados**: selecione **Mensagens de dispositivo** na lista suspensa.

   **Ponto de extremidade**: selecione o ponto de extremidade que você configurou. Este tutorial usa **StorageContainer**. 
   
   **Cadeia de caracteres de consulta**: Insira `level="storage"` como a cadeia de caracteres de consulta. 

   ![Captura de tela mostrando a criação de uma regra de roteamento para a conta de armazenamento.](./media/tutorial-routing/create-a-new-routing-rule-storage.png)
   
   Clique em **Salvar**. Quando terminar, ele retorna para o painel de Rotas, onde você pode ver a nova regra de roteamento para o armazenamento. Feche o painel de Rotas, que retorna para a página de Grupo de Recursos.

### <a name="routing-to-a-service-bus-queue"></a>Roteamento para uma fila do Barramento de Serviço 

Agora, configure o roteamento para a fila do Barramento de Serviço. Definir um ponto de extremidade e, em seguida, configure uma rota para esse ponto de extremidade. Mensagens onde a propriedade **nível** está definida como **crítica** são gravadas na fila do Barramento de Serviço, que dispara um aplicativo lógico, que envia um e-mail com as informações. 

1. Na página do Grupo de Recursos, clique em seu Hub IoT e, em seguida, clique em **pontos de extremidade**. No painel **pontos de extremidade**, clique em **+ Adicionar**. Insira as seguintes informações.

   **Nome**: Insira um nome para o ponto de extremidade. Este tutorial usa **CriticalQueue**. 

   **Tipo de ponto de extremidade**: Selecione **Fila do Barramento de Serviço** na lista suspensa.

   **Namespace do Barramento de Serviço**: selecione o namespace do Barramento de Serviço para este tutorial na lista suspensa. Este tutorial usa **ContosoSBNamespace**.

   **Fila do Barramento de Serviço**: Selecione fila do Barramento de Serviço na lista suspensa. Este tutorial usa **contososbqueue**.

   ![Captura de tela mostrando a adição de um ponto de extremidade para a fila do Barramento de Serviço.](./media/tutorial-routing/add-endpoint-sb-queue.png)

   Clique em **OK** para salvar o ponto de extremidade. Depois que ele for concluído, feche o painel de pontos de extremidade. 
    
1. Clique em **Rotas** no seu Hub IoT. Você vai criar uma regra de roteamento que roteia mensagens para a fila do Barramento de Serviço que você acabou de adicionar como um ponto de extremidade. Clique em **+Adicionar**, na parte superior do painel de Rotas. Preencha os campos na tela. 

   **Nome**: insira um nome para a regra de roteamento. Este tutorial usa o **SBQueueRule**. 

   **Fonte de dados**: selecione **Mensagens de dispositivo** na lista suspensa.

   **Ponto de extremidade**: selecione o ponto de extremidade que você configurou, **CriticalQueue**.

   **Cadeia de caracteres de consulta**: Insira `level="critical"` como a cadeia de caracteres de consulta. 

   ![Captura de tela mostrando a criação de uma regra de roteamento para a fila do Barramento de Serviço.](./media/tutorial-routing/create-a-new-routing-rule-sbqueue.png)
   
   Clique em **Salvar**. Ao retornar ao painel de Rotas, verá as suas novas regras de roteamentos, conforme exibido aqui.

   ![Captura de tela mostrando as rotas que você acabou de configurar.](./media/tutorial-routing/show-routing-rules-for-hub.png)

   Feche o painel de Rotas, que retorna para a página de Grupo de Recursos.

## <a name="create-a-logic-app"></a>Criar um aplicativo lógico  

É a fila do Barramento de Serviço a ser usado para receber mensagens designadas como críticas. Configure um aplicativo lógico para monitorar a fila do Barramento de Serviço e enviar um e-mail quando uma mensagem é adicionada à fila. 

1. No [portal do Azure](https://portal.azure.com), clique em **+ Criar um recurso**. Coloque o **aplicativo lógico** na caixa de pesquisa e clique em Enter. Nos resultados da pesquisa exibidos, selecione o aplicativo lógico e clique em **Criar** para continuar para o painel **Criar aplicativo lógico**. Preencha os campos. 

   **Nome**: este campo é o nome do aplicativo lógico. Este tutorial usa **ContosoLogicApp**. 

   **Assinatura**: selecione sua assinatura do Azure.

   **Grupo de Recursos**: clique em **Usar existente** e selecione seu grupo de recursos. Este tutorial usa **ContosoResources**. 

   **Local**: use o seu local. Este tutorial usa **Oeste dos EUA**. 

   **Log Analytics**: essa alternância deve ser desativada. 

   ![Captura de tela mostrando a tela Criar aplicativo lógico.](./media/tutorial-routing/create-logic-app.png)

   Clique em **Criar**.

1. Agora vá para o aplicativo lógico. A maneira mais fácil de obter o aplicativo lógico é clicar em **Grupos de recursos**, selecionar o grupo de recursos (este tutorial usa **ContosoResources**), em seguida, selecionar o aplicativo lógico da lista de recursos. É exibida a página do Designer de Aplicativos Lógicos (talvez seja necessário rolar para a direita para ver a página inteira). Na página do Designer de Aplicativos Lógicos, role para baixo até ver o bloco que diz **Aplicativo lógico em branco +** e clique nele. 

1. Uma lista de conectores é exibida. Selecione **Barramento de Serviço**. 

   ![Captura de tela mostrando a lista de conectores.](./media/tutorial-routing/logic-app-connectors.png)

1. Uma lista de gatilhos é exibida. Selecione **Barramento de Serviço - quando uma mensagem é recebida em uma fila (preenchimento automático)**. 

   ![Captura de tela mostrando a lista dos gatilhos para o Barramento de Serviço.](./media/tutorial-routing/logic-app-triggers.png)

1. Na próxima tela, preencha o nome de Conexão. Este tutorial usa **ContosoConnection**. 

   ![Captura de tela mostrando a configuração de conexão para a fila do Barramento de Serviço.](./media/tutorial-routing/logic-app-define-connection.png)

   Clique no namespace do Barramento de Serviço. Este tutorial usa **ContosoSBNamespace**. Quando você seleciona o namespace, o portal consulta o namespace do Barramento de Serviço para recuperar as chaves. Selecione **RootManageSharedAccessKey** e clique em **Criar**. 
   
   ![Captura de tela mostrando a configuração concluir a conexão.](./media/tutorial-routing/logic-app-finish-connection.png)

1. Na próxima tela, selecione o nome da fila (este tutorial usa **contososbqueue**) na lista suspensa. Você pode usar os padrões para o restante dos campos. 

   ![Captura de tela mostrando as opções de fila.](./media/tutorial-routing/logic-app-queue-options.png)

1. Configure agora a ação para enviar um e-mail quando uma mensagem é recebida na fila. No Designer de Aplicativos Lógicos, clique em **+ Nova etapa** para adicionar uma etapa, em seguida, clique em **Adicionar uma ação**. No painel **Escolher uma ação**, localize e clique em **Outlook do Office 365**. Na tela de gatilhos, selecione **Outlook do Office 365 - enviar um email**.  

   ![Captura de tela mostrando as opções do Office 365.](./media/tutorial-routing/logic-app-select-outlook.png)

1. Em seguida, faça logon em sua conta do Office 365 para configurar a conexão. Especifique os endereços de e-mail para os destinatários dos e-mails. Também especifique o assunto e digite a mensagem de que você gostaria que o destinatário visse no corpo da mensagem. Para testar, preencha seu próprio endereço de e-mail como o destinatário.

   Clique em **Adicionar conteúdo dinâmico** para mostrar o conteúdo da mensagem que você pode incluir. Selecione **Conteúdo** – ele incluirá a mensagem no e-mail. 

   ![Captura de tela mostrando as opções de e-mail para o aplicativo lógico.](./media/tutorial-routing/logic-app-send-email.png)

1. Clique em **Salvar**. Em seguida, feche o Designer de Aplicativo Lógico.

## <a name="set-up-azure-stream-analytics"></a>Configurar o Azure Stream Analytics

Para ver os dados em uma visualização do Power BI, primeiro configure um trabalho do Stream Analytics para recuperar os dados. Lembre-se de que apenas as mensagens onde o **nível** é **normal** são enviadas ao ponto de extremidade padrão e serão recuperadas pelo trabalho do Stream Analytics para a visualização do Power BI.

### <a name="create-the-stream-analytics-job"></a>Criar o trabalho do Stream Analytics

1. No [Portal do Azure](https://portal.azure.com), clique em **Criar um recurso** > **Internet das Coisas** > **Trabalho do Stream Analytics**.

1. Insira as seguintes informações para o trabalho.

   **Nome do trabalho**: o nome do trabalho. O nome deve ser globalmente exclusivo. Este tutorial usa **contosoJob**.

   **Grupo de recursos**: use o mesmo grupo de recursos usado pelo seu Hub IoT. Este tutorial usa **ContosoResources**. 

   **Local**: use o mesmo local usado no script de instalação. Este tutorial usa **Oeste dos EUA**. 

   ![Captura de tela mostrando como criar o trabalho do stream analytics.](./media/tutorial-routing/stream-analytics-create-job.png)

1. Clique em **Criar** para criar o trabalho. Para voltar ao trabalho, clique em **Grupos de recursos**. Este tutorial usa **ContosoResources**. Selecione o grupo de recursos, depois clique no trabalho do Stream Analytics na lista de recursos. 

### <a name="add-an-input-to-the-stream-analytics-job"></a>Adicionar uma entrada ao trabalho do Stream Analytics

1. Em **Topologia do Trabalho**, clique em **Entradas**.

1. No painel de **Entradas**, clique em **Adicionar entrada de fluxo** e selecione o Hub IoT. Na tela que aparece, preencha os seguintes campos:

   **Alias de entrada**: este tutorial usa **contosoinputs**.

   **Assinatura**: selecione sua assinatura.

   **Hub IoT**: selecione o Hub IoT. Este tutorial usa **ContosoTestHub**.

   **Ponto de extremidade**: selecione **Mensagens**. (Se você selecionar Monitoramento de Operações, você obtém dados de telemetria sobre o Hub IoT em lugar dos dados que você está enviando.) 

   **Nome da política de acesso compartilhado**: selecione **iothubowner**. O portal preenche a chave de política de acesso compartilhado para você.

   **Grupo de consumidores**: selecione o grupo de consumidores criado anteriormente. Este tutorial usa **contosoconsumers**.
   
   Para o restante dos campos, aceite os padrões. 

   ![Captura de tela mostrando como configurar as entradas para o trabalho do stream analytics.](./media/tutorial-routing/stream-analytics-job-inputs.png)

1. Clique em **Salvar**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Adicionar uma saída ao trabalho do Stream Analytics

1. Em **Topologia do Trabalho**, clique em **Saídas**.

1. No painel **Saídas**, clique em **Adicionar** e, em seguida, selecione **Power BI**. Na tela que aparece, preencha os seguintes campos:

   **Alias de saída**: o alias exclusivo para a saída. Este tutorial usa **contosooutputs**. 

   **Nome do conjunto de dados**: nome do conjunto de dados a ser usado no Power BI. Este tutorial usa **contosodataset**. 

   **Nome da tabela**: nome da tabela a ser usada no Power BI. Este tutorial usa **contosotable**.

   Aceite os padrões para o restante dos campos.

1. Clique em **Autorizar** e entre na sua conta do Power BI.

   ![Captura de tela mostrando como configurar as saídas para o trabalho do stream analytics.](./media/tutorial-routing/stream-analytics-job-outputs.png)

1. Clique em **Salvar**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configurar a consulta do trabalho do Stream Analytics

1. Em **Topologia do Trabalho**, clique em **Consulta**.

1. Substitua `[YourInputAlias]` pelo alias de entrada do trabalho. Este tutorial usa **contosoinputs**.

1. Substitua `[YourOutputAlias]` pelo alias de saída do trabalho. Este tutorial usa **contosooutputs**.

   ![Captura de tela mostrando como configurar a consulta para o trabalho do stream analytics.](./media/tutorial-routing/stream-analytics-job-query.png)

1. Clique em **Salvar**.

1. Feche o painel de consulta. Isso faz você voltar para a exibição dos recursos no Grupo de recursos. Clique no trabalho do Stream Analytics. Este tutorial o chama de **contosoJob**.

### <a name="run-the-stream-analytics-job"></a>Executar o trabalho do Stream Analytics

No trabalho do Stream Analytics, clique em **Iniciar** > **Agora** > **Iniciar**. Depois que o trabalho é iniciado com êxito, o status do trabalho muda de **parado** para **executando**.

Para configurar o relatório do Power BI, você precisa de dados, portanto você configurará o Power BI depois de criar o dispositivo e executar o aplicativo de simulação do dispositivo.

## <a name="run-simulated-device-app"></a>Executar um aplicativo de dispositivo simulado

Anteriormente na seção de configuração do script, você configurou um dispositivo para simular usando um dispositivo de IoT. Nesta seção, você baixa um aplicativo do console do .NET que simula um dispositivo que envia mensagens do dispositivo para a nuvem para um hub IoT. Este aplicativo envia mensagens para cada um dos métodos de roteamentos diferentes. 

Baixe a solução para a [Simulação de dispositivo IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Isso baixa um repositório com vários aplicativos nele; a solução que você está procurando está em iot-hub/Tutorials/Routing/SimulatedDevice/.

Clique duas vezes no arquivo de solução (SimulatedDevice.sln) para abrir o código no Visual Studio, em seguida, abra o Program.cs. Substitua `{iot hub hostname}` pelo nome de host do Hub IoT. O formato de nome de host do hub IoT é **{iot-hub-name}.azure-devices.net**. Para este tutorial, o nome de host do hub é **ContosoTestHub.azure-devices.net**. Em seguida, substitua `{device key}` com a chave do dispositivo salvo anteriormente durante a configuração do dispositivo simulado. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Executar e testar 

Execute o aplicativo de console. Aguarde alguns minutos. Você pode ver as mensagens sendo enviadas na tela do aplicativo de console.

Esse aplicativo envia uma nova mensagem de dispositivo para a nuvem ao Hub IoT a cada segundo. A mensagem contém um objeto serializado para JSON com a ID do dispositivo, temperatura, umidade e nível de mensagem, cujo padrão é `normal`. Atribui aleatoriamente um nível de `critical` ou `storage`, fazendo com que a mensagem deve ser roteada para a conta de armazenamento ou para a fila do Barramento de Serviço (que dispara seu Aplicativo Lógico para enviar um e-mail). As leituras (`normal`) padrão serão exibidas no relatório de BI que você configurar a seguir.

Se tudo estiver configurado corretamente, agora você verá os seguintes resultados:

1. Você começa a obter e-mails sobre mensagens críticas. 

   ![Captura de tela mostrando os e-mails resultantes.](./media/tutorial-routing/results-in-email.png)

   Isso significa o seguinte:

   * O roteamento para a fila do Barramento de Serviço está funcionando corretamente.
   * O Aplicativo Lógico recuperar a mensagem da fila de Barramento de Serviço está funcionando corretamente.
   * O conector de Aplicativo Lógico para o Outlook está funcionando corretamente. 

1. No [portal do Azure](https://portal.azure.com), clique em **Grupos de recursos**, selecione o Grupo de Recursos. Este tutorial usa **ContosoResources**. Selecione a conta de armazenamento, clique em **Blobs**, em seguida, selecione o contêiner. Este tutorial usa **contosoresults**. Você deve ver uma pasta, e você pode fazer drill down em diretórios até que você veja um ou mais arquivos. Abrir um desses arquivos; elas contêm as entradas roteadas para a conta de armazenamento. 

   ![Captura de tela mostrando os arquivos de resultado no armazenamento.](./media/tutorial-routing/results-in-storage.png)

Isso significa o seguinte:

   * O roteamento para a conta de armazenamento está funcionando corretamente.

Agora, com o aplicativo ainda em execução, configure a visualização do Power BI para ver as mensagens recebidas por meio de roteamento padrão. 

## <a name="set-up-the-power-bi-visualizations"></a>Configurar as visualizações do Power BI

1. Entre na sua conta do [Power BI](https://powerbi.microsoft.com/).

1. Vá para os **Espaços de trabalho** e selecione o espaço de trabalho que você definiu quando criou a saída para o trabalho do Stream Analytics. Este tutorial usa **Meu Espaço de trabalho**. 

1. Clique em **Conjuntos de dados**.

   Você deve ver o conjunto de dados relacionado que você especificou quando criou a saída para o trabalho do Stream Analytics. Este tutorial usa **contosodataset**. (Ele pode levar de 5 a 10 minutos para aparecer o conjunto de dados pela primeira vez.)

1. Em **AÇÕES**, clique no primeiro ícone para criar um relatório.

   ![Captura de tela mostrando o espaço de trabalho do Power BI com ícone de relatório e ações realçado.](./media/tutorial-routing/power-bi-actions.png)

1. Crie um gráfico de linhas para mostrar a temperatura em tempo real ao longo do tempo.

   a. Na página de criação de relatório, adicione um gráfico de linha, clicando no ícone de gráfico de linha.

   ![Captura de tela mostrando as visualizações e campos.](./media/tutorial-routing/power-bi-visualizations-and-fields.png)

   b. Sobre o **campos** painel, expanda a tabela que você especificou quando criou a saída para o trabalho do Stream Analytics. Este tutorial usa **contosotable**.

   c. Arraste **EventEnqueuedUtcTime** para **eixo** sobre o **visualizações** painel.

   d. Arraste **temperatura** para **Valores**.

   Um gráfico de linhas é criado. O eixo x exibe a data e a hora no fuso horário UTC. O eixo y mostra a temperatura do sensor.

1. Crie outro gráfico de linhas para mostrar umidade em tempo real ao longo do tempo. Para configurar o segundo gráfico, siga as mesmas etapas acima e coloque **EventEnqueuedUtcTime** no eixo x e **umidade** no eixo y.

   ![Captura de tela mostrando o relatório do Power BI final com os dois gráficos.](./media/tutorial-routing/power-bi-report.png)

1. Clique em **Salvar** para salvar o relatório.

Você deve ser capaz de visualizar dados em ambos os gráficos. Isso significa o seguinte:

   * O roteamento para o ponto de extremidade padrão está funcionando corretamente.
   * O trabalho do Azure Stream Analytics está transmitindo corretamente.
   * A visualização do Power BI está configurada corretamente.

Você pode atualizar os gráficos para ver os dados mais recentes, clique no botão Atualizar na parte superior da janela do Power BI. 

## <a name="clean-up-resources"></a>Limpar recursos 

Se você quiser remover todos os recursos que você criou, exclua o grupo de recursos. Essa ação também exclui todos os recursos contidos no grupo. Nesse caso, ele remove o Hub IoT, a fila e namespace do Barramento de Serviço, o aplicativo lógico, a conta de armazenamento e o próprio grupo de recursos. 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Limpar os recursos na visualização do Power BI

Faça logon na sua conta do [Power BI](https://powerbi.microsoft.com/). Vá até seu espaço de trabalho. Este tutorial usa **Meu Espaço de trabalho**. Para remover a visualização do Power BI, vá para conjuntos de dados e clique no ícone de Lixeira para excluir o conjunto de dados. Este tutorial usa **contosodataset**. Quando você remove o conjunto de dados, o relatório também será removido.

### <a name="clean-up-resources-using-azure-cli"></a>Limpar recursos usando a CLI do Azure

Para remover o grupo de recursos, use o comando [excluir grupo az](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive
az group delete --name $resourceGroup
```
### <a name="clean-up-resources-using-powershell"></a>Limpar recursos usando o PowerShell

Para remover o grupo de recursos, use o comando [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/remove-azurermresourcegroup). $resourceGroup foi definido como **ContosoIoTRG1** novamente no início deste tutorial.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $resourceGroup
```


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar o roteamento de mensagens para rotear mensagens do Hub IoT para diferentes destinos executando as seguintes tarefas.  

> [!div class="checklist"]
> * Usando a CLI do Azure ou o PowerShell, configure os recursos básicos – um Hub IoT, uma conta de armazenamento, uma fila do Barramento de Serviço e um dispositivo simulado.
> * Configure pontos de extremidade e as rotas no Hub IoT para a conta de armazenamento e a fila do Barramento de Serviço.
> * Crie um aplicativo lógico que é disparado e envia e-mail quando uma mensagem é adicionada à fila de Barramento de Serviço.
> * Baixe e execute um aplicativo que simula um dispositivo IoT enviando mensagens para o hub para as diferentes opções de roteamentos.
> * Crie uma visualização do Power BI para os dados enviados para o ponto de extremidade padrão.
> * Exiba os resultados...
> * ... na fila do Barramento de Serviço e emails.
> * ... na conta de armazenamento.
> * ...na visualização do Power BI.

Avance para o próximo tutorial para aprender a gerenciar o estado de um dispositivo IoT. 

> [!div class="nextstepaction"]
[Configurar seus dispositivos de um serviço de back-end](tutorial-device-twins.md)

 <!--  [Manage the state of a device](./tutorial-manage-state.md) -->
