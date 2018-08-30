---
title: Início Rápido do Azure – Criar um hub de eventos usando o PowerShell | Microsoft Docs
description: Este início rápido descreve como criar um hub de eventos usando o Azure PowerShell e, em seguida, enviar e receber eventos usando o SDK do .NET Standard.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: b1f0c0d06f6c5f99a843a384e1dda667b967a02b
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/23/2018
ms.locfileid: "42743900"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>Início Rápido: criar um hub de eventos usando o Azure PowerShell

Os Hubs de Eventos do Azure são uma plataforma de fluxo de dados altamente escalonável e um serviço de ingestão capaz de receber e processar milhões de eventos por segundo. Este início rápido mostra como criar um hub de eventos usando o Azure PowerShell e, em seguida, enviar e receber de um hub de eventos usando o SDK do .NET Standard.

Para concluir este início rápido, você precisa de uma assinatura do Azure. Se você não tiver [uma conta gratuita][], crie uma antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, verifique se você tem:

- [Atualização 3 do Visual Studio 2017 (versão 15.3, 26730.01)](http://www.visualstudio.com/vs) ou posterior.
- [SDK do .NET Standard](https://www.microsoft.com/net/download/windows), versão 2.0 ou posterior.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você estiver usando o PowerShell localmente, você deve executar a versão mais recente do PowerShell para concluir este início rápido. Se precisar instalar ou atualizar, confira [Instalar e configurar o Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

## <a name="provision-resources"></a>Provisionar recursos

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é uma coleção lógica de recursos do Azure e você precisa de um grupo de recursos para criar um hub de eventos. 

O exemplo a seguir cria um grupo de recursos na região Leste dos EUA. Substitua `myResourceGroup` pelo nome do grupo de recursos e local que você deseja usar:

```azurepowershell-interactive
New-AzureRmResourceGroup –Name myResourceGroup –Location eastus
```

### <a name="create-an-event-hubs-namespace"></a>Criar um namespace de Hubs de Eventos

Após o seu grupo de recursos ser criado, crie um namespace de Hubs de Eventos dentro deste mesmo grupo de recursos. Um namespace de Hubs de Eventos fornece um nome de domínio exclusivo totalmente qualificado no qual você pode criar seu hub de eventos. Substitua `namespace_name` por um nome exclusivo para o seu namespace:

```azurepowershell-interactive
New-AzureRmEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

### <a name="create-an-event-hub"></a>Criar um Hub de Evento

Agora que você possui um namespace de Hubs de Eventos, crie um hub de eventos dentro desse namespace:

```azurepowershell-interactive
New-AzureRmEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name
```

### <a name="create-a-storage-account-for-event-processor-host"></a>Criar uma conta de armazenamento para o Host do Processador de Eventos

O Host do Processador de Eventos simplifica o recebimento de eventos de Hubs de Eventos ao gerenciar pontos de verificação e recebimentos paralelos. Para o ponto de verificação, o Host do Processador de Eventos requer uma conta de armazenamento. Para criar uma conta de armazenamento e obter suas respectivas chaves, execute os seguintes comandos:

```azurepowershell-interactive
# Create a standard general purpose storage account 
New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name storage_account_name -Location eastus -SkuName Standard_LRS 
e
# Retrieve the storage account key for accessing it
Get-AzureRmStorageAccountKey -ResourceGroupName myResourceGroup -Name storage_account_name
```

### <a name="get-the-connection-string"></a>Obtenha a cadeia de conexão

Uma cadeia de conexão é necessária para se conectar ao seu hub de eventos e processar os eventos. Para obter sua cadeia de conexão, execute:

```azurepowershell-interactive
Get-AzureRmEventHubKey -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Name RootManageSharedAccessKey
```

## <a name="stream-into-event-hubs"></a>Transmitir para os Hubs de Eventos

Agora você pode iniciar o streaming em seus Hubs de Eventos. Os exemplos podem ser baixados ou clonados do Git no [repositório de Hubs de Eventos](https://github.com/Azure/azure-event-hubs)

### <a name="ingest-events"></a>Ingerir eventos

Para iniciar o streaming de eventos, baixe o [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) do GitHub ou clone o [repositório GitHub de Hubs de evento](https://github.com/Azure/azure-event-hubs) emitindo o comando a seguir:

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

Navegue até a pasta \azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleSender e carregue o arquivo SampleSender.sln no Visual Studio.

Em seguida, adicione o pacote Nuget [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) ao projeto.

No arquivo Program.cs, substitua os seguintes espaços reservados pelo nome do seu hub de eventos e cadeia de conexão:

```C#
private const string EhConnectionString = "Event Hubs connection string";
private const string EhEntityPath = "Event Hub name";

```

Agora, crie e execute o exemplo. Você pode ver os eventos que estão sendo ingeridos no hub de eventos:

![][3]

### <a name="receive-and-process-events"></a>Receber e processar eventos

Agora baixe o exemplo de receptor de Host do Processador de Eventos, que recebe as mensagens que você acabou de enviar. Baixe o [SampleEphReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) do GitHub ou clone o [repositório GitHub de Hubs de Eventos](https://github.com/Azure/azure-event-hubs) emitindo o comando a seguir:

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

Navegue até a pasta \azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleEphReceiver e carregue o arquivo da solução SampleEphReceiver.sln no Visual Studio.

Em seguida, adicione os pacotes NuGet [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) e [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) ao seu projeto.

No arquivo Program.cs, substitua as constantes a seguir pelos seus valores correspondentes:

```C#
private const string EventHubConnectionString = "Event Hubs connection string";
private const string EventHubName = "Event Hub name";
private const string StorageContainerName = "Storage account container name";
private const string StorageAccountName = "Storage account name";
private const string StorageAccountKey = "Storage account key";
```

Agora, crie e execute o exemplo. Você pode ver os eventos que estão sendo recebidos em seu aplicativo de exemplo:

![][4]

No portal do Azure, você pode exibir a taxa na qual os eventos estão sendo processados para um determinado namespace de Hubs de Eventos conforme mostrado:

![][5]

## <a name="clean-up-resources"></a>Limpar recursos

Depois de concluir este início rápido, você pode excluir seu grupo de recursos e o namespace, a conta de armazenamento e o hub de eventos dentro dele. Substitua `myResourceGroup` pelo nome do grupo de recursos que você criou. 

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou um namespace de Hubs de Eventos e outros recursos necessários para enviar e receber eventos do seu hub de eventos. Para saber mais, continue com o tutorial a seguir:

> [!div class="nextstepaction"]
> [Visualizar anomalias de dados nos fluxos de dados dos Hubs de Eventos](event-hubs-tutorial-visualize-anomalies.md)

[uma conta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[New-AzureRmResourceGroup]: https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
