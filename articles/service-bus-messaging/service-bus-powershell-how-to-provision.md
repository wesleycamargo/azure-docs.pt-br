---
title: "Gerenciar o Barramento de Serviço do Azure com o PowerShell | Microsoft Docs"
description: "Gerenciar o Barramento de Serviço com scripts do PowerShell"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: aff8e2ce-bc8b-489f-aca9-a18782be0375
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 61f31c8ad0463776937f366d145595f04cc42d2e
ms.openlocfilehash: 24dd8757942488aa8364cc6cf968cfc17299699f


---
# <a name="manage-service-bus-with-powershell"></a>Gerenciar o Barramento de Serviço com o PowerShell
## <a name="overview"></a>Visão geral
O PowerShell do Microsoft Azure é um ambiente de script que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Neste artigo, você aprenderá como usar o PowerShell para provisionar e gerenciar entidades do Barramento de Serviço como namespaces, filas e Hubs de Eventos usando um console local do PowerShell do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Antes de iniciar este artigo, você deverá ter os seguintes pré-requisitos:

* Uma assinatura do Azure. O Azure é uma plataforma baseada em assinatura. Para saber mais sobre como adquirir uma assinatura, confira [Opções de Compra][Purchase Options], [Ofertas para Membros][Member Offers] ou [Avaliação Gratuita][Free Trial].
* Um computador com o PowerShell do Azure. Para obter instruções, consulte [Instalar e configurar o Azure PowerShell][Install and configure Azure PowerShell].
* Um entendimento geral dos scripts do PowerShell, dos pacotes NuGet e do .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Incluindo uma referência ao assembly .NET para o Barramento de Serviço
Há um número limitado de cmdlets do PowerShell para gerenciar o Barramento de Serviço. Para provisionar entidades que não são expostas pelos cmdlets existentes, você pode usar o cliente .NET para Barramento de Serviço no [pacote NuGet do Barramento de Serviço][Service Bus NuGet package].

Em primeiro lugar, verifique se o script pode localizar o assembly **Microsoft.ServiceBus.dll** , que é instalado com o pacote NuGet. Para que seja flexível, o script executa estas etapas:

1. Determina o caminho no qual ele foi invocado.
2. Percorre o caminho até encontrar uma pasta denominada `packages`. Essa pasta é criada quando você instala pacotes NuGet.
3. Pesquisa recursivamente a pasta `packages` para um assembly denominado **Microsoft.ServiceBus.dll**.
4. Faz referência ao assembly para que os tipos estejam disponíveis para uso posterior.

Aqui está como essas etapas são implementadas em um script do PowerShell:

```powershell
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.ServiceBus.dll
    Write-Host "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Host "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error "Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script."
}
```

## <a name="provision-a-service-bus-namespace"></a>Provisionar um namespace do Barramento de Serviço
Dois cmdlets do PowerShell oferecem suporte a operações de namespace do Barramento de Serviço. Em vez das APIs do SDK do .NET, você pode usar o [Get-AzureSBNamespace][Get-AzureSBNamespace] e [New-AzureSBNamespace][New-AzureSBNamespace].

Este exemplo cria algumas variáveis locais no script; `$Namespace` e `$Location`.

* `$Namespace` é o nome do namespace do Barramento de Serviço com o qual queremos trabalhar.
* `$Location` identifica o datacenter no qual o script provisiona o namespace.
* `$CurrentNamespace` armazena o namespace de referência recuperado (ou criado) pelo script.

Em um script real, `$Namespace` e `$Location` podem ser passados como parâmetros.

Essa parte do script executa as seguintes tarefas:

1. Tenta recuperar um namespace do Barramento de Serviço com o nome fornecido.
2. Se o namespace for encontrado, ele informará o que foi encontrado.
3. Se o namespace não for encontrado, ele vai criar o namespace e, em seguida, recuperar o namespace recentemente criado.
   
    ```powershell
    $Namespace = "MyServiceBusNS"
    $Location = "West US"
   
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace [$Namespace] already exists in the [$($CurrentNamespace.Region)] region."
    }
    else
    {
        Write-Host "The [$Namespace] namespace does not exist."
        Write-Host "Creating the [$Namespace] namespace in the [$Location] region..."
        New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace $false -NamespaceType Messaging
        $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
        Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
    }
    ```

Para provisionar outras entidades do Barramento de Serviço, crie uma instância da classe [NamespaceManager][NamespaceManager] do SDK.
Você pode usar o cmdlet [Get-AzureSBAuthorizationRule][Get-AzureSBAuthorizationRule] para recuperar uma regra de autorização que é usada para fornecer uma cadeia de conexão. Armazenaremos uma referência para a instância `NamespaceManager` na variável `$NamespaceManager`. Usaremos `$NamespaceManager` posteriormente no script para provisionar outras entidades.

```powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the event hub
Write-Host "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Host "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-other-service-bus-entities"></a>Provisionamento de outras entidades do Barramento de Serviço
Para provisionar outras entidades, como filas, tópicos e Hubs de Evento, use a [API do .NET para Barramento de Serviço][API do .NET para Barramento de Serviço]. Este artigo se concentra apenas em Hubs de Eventos, mas as etapas para outras entidades são semelhantes. Além disso, os exemplos mais detalhados, incluindo outras entidades, são referenciados no final deste artigo.

Essa parte do script cria mais quatro variáveis locais. Essas variáveis serão usadas para criar uma instância de um objeto `EventHubDescription` . O script executa as seguintes tarefas:

1. Usando o objeto `NamespaceManager`, verifica se o Hub de Eventos identificou a existência de `$Path`.
2. Se não existir, cria um `EventHubDescription` e o passa para o método `CreateEventHubIfNotExists` da classe `NamespaceManager`.
3. Depois de determinar se o Hub de Eventos está disponível, cria um grupo de consumidores usando `ConsumerGroupDescription` e `NamespaceManager`.
   
    ```powershell
    $Path  = "MyEventHub"
    $PartitionCount = 12
    $MessageRetentionInDays = 7
    $UserMetadata = $null
    $ConsumerGroupName = "MyConsumerGroup"
   
    # Check to see if the Event Hub already exists
    if ($NamespaceManager.EventHubExists($Path))
    {
        Write-Host "The [$Path] event hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Host "Creating the [$Path] event hub in the [$Namespace] namespace: PartitionCount=[$PartitionCount] MessageRetentionInDays=[$MessageRetentionInDays]..."
        $EventHubDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.EventHubDescription -ArgumentList $Path
        $EventHubDescription.PartitionCount = $PartitionCount
        $EventHubDescription.MessageRetentionInDays = $MessageRetentionInDays
        $EventHubDescription.UserMetadata = $UserMetadata
        $EventHubDescription.Path = $Path
        $NamespaceManager.CreateEventHubIfNotExists($EventHubDescription)
        Write-Host "The [$Path] event hub in the [$Namespace] namespace has been successfully created."
    }
   
    # Create the consumer group if it doesn't exist
    Write-Host "Creating the consumer group [$ConsumerGroupName] for the [$Path] event hub..."
    $ConsumerGroupDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.ConsumerGroupDescription -ArgumentList $Path, $ConsumerGroupName
    $ConsumerGroupDescription.UserMetadata = $ConsumerGroupUserMetadata
    $NamespaceManager.CreateConsumerGroupIfNotExists($ConsumerGroupDescription)
    Write-Host "The consumer group [$ConsumerGroupName] for the [$Path] event hub has been successfully created."
    ```

## <a name="migrate-a-namespace-to-another-azure-subscription"></a>Migrar um namespace para outra assinatura do Azure
A sequência de comandos a seguir move um namespace de uma assinatura do Azure para outra. Para executar essa operação, o namespace já deve estar ativo e o usuário que está executando os comandos do PowerShell deve ser um administrador em assinaturas de origem e de destino.

```powershell
# Create a new resource group in target subscription
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Próximas etapas
Este artigo fornece uma estrutura de tópicos para o provisionamento de entidades do Barramento de Serviço usando o PowerShell. Tudo o que você pode fazer usando as bibliotecas de cliente .NET, você também pode fazer em um script do PowerShell.

Os exemplos mais detalhados estão disponíveis nessas postagens de blog:

* [Como criar filas, tópicos e assinaturas do Barramento de Serviço usando um script do PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Como criar um namespace do Barramento de Serviço e um Hub de Eventos usando um script do PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Alguns scripts prontos também estão disponíveis para download:

* [Scripts do PowerShell do Barramento de Serviço](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Link references-->
[Purchase Options]: http://azure.microsoft.com/pricing/purchase-options/
[Member Offers]: http://azure.microsoft.com/pricing/member-offers/
[Free Trial]: http://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Service Bus NuGet package]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://docs.microsoft.com/powershell/servicemanagement/azure.compute/v1.6.1/Get-AzureSBNamespace
[New-AzureSBNamespace]: https://docs.microsoft.com/powershell/servicemanagement/azure.compute/v1.6.1/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: https://docs.microsoft.com/powershell/servicemanagement/azure.compute/v1.6.1/get-azuresbauthorizationrule
[NamespaceManager]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager



<!--HONumber=Jan17_HO2-->


