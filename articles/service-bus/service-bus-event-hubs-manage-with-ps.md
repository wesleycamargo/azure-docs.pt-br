<properties
   pageTitle="Usar o PowerShell para gerenciar recursos do Barramento de Serviço e dos Hubs de Eventos"
   description="Usando o PowerShell para criar e gerenciar recursos do Barramento de Serviço e dos Hubs de Eventos"
   services="service-bus"
   documentationCenter=".NET"
   authors="sethmanheim"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="04/14/2015"
   ms.author="sethm"/>

# Usar o PowerShell para gerenciar recursos do Barramento de Serviço e dos Hubs de Eventos

O PowerShell do Microsoft Azure é um ambiente de script que você pode usar para controlar e automatizar a implantação e o gerenciamento dos serviços do Azure. Neste artigo, você aprenderá como usar o PowerShell para provisionar e gerenciar entidades do Barramento de Serviço como namespaces, filas e Hubs de Eventos usando um console local do PowerShell do Azure.

## Pré-requisitos

Antes de começar, você precisará do seguinte:

- Uma assinatura do Azure. O Azure é uma plataforma baseada em assinatura. Para obter mais informações sobre como adquirir uma assinatura, consulte [Opções de compra], [Ofertas para membros] ou [Avaliação gratuita].

- Um computador com o PowerShell do Azure. Consulte as instruções de configuração na próxima seção.

- Um entendimento geral dos scripts do PowerShell, dos pacotes NuGet e do .NET Framework.

## Configurar o PowerShell

[AZURE.INCLUDE [powershell-setup](../../includes/powershell-setup.md)]

## Incluir uma referência no assembly .NET para Barramento de Serviço

Há um número limitado de cmdlets do PowerShell para gerenciar o Barramento de Serviço. Para provisionar entidades que não são expostas pelos cmdlets existentes, você pode usar o cliente .NET para Barramento de Serviço no PowerShell fazendo referência ao [pacote NuGet do Barramento de Serviço].

Em primeiro lugar, verifique se o script pode localizar o assembly **Microsoft.ServiceBus.dll**, que é instalado com o pacote NuGet. Para que seja flexível, o script executa estas etapas:

1. Determina o caminho do qual ele foi chamado.
2. Percorre o caminho até encontrar uma pasta denominada `packages`. Essa pasta é criada quando você instala pacotes NuGet.
3. Pesquisa recursivamente a pasta `packages` para um assembly denominado **Microsoft.ServiceBus.dll**.
4. Faz referência ao assembly para que os tipos estejam disponíveis para uso posterior.

Aqui está como essas etapas são implementadas em um script do PowerShell:

	```powershell
	
	try
	{
	    # IMPORTANT: Make sure to reference the latest version of Microsoft.ServiceBus.dll
	    Write-Output "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
	    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
	    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
	    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
	    Add-Type -Path $assembly.FullName
	
	    Write-Output "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
	}
	
	catch [System.Exception]
	{
	    Write-Error("Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
	}
	
	```

## Provisionar um namespace do Barramento de Serviço

Ao trabalhar com namespaces do Barramento de Serviço, há dois cmdlets que podem ser usados no lugar do SDK do .NET: [Get-AzureSBNamespace] e [New-AzureSBNamespace].

Este exemplo cria algumas variáveis locais no script; `$Namespace` e `$Location`.

- `$Namespace` é o nome do namespace do Barramento de Serviço com o qual queremos trabalhar.
- `$Location` identifica o datacenter em que iremos provisionar o namespace.
- `$CurrentNamespace` armazena o namespace de referência que recuperamos (ou criamos).

Em um script real, `$Namespace` e `$Location` podem ser passados como parâmetros.

Esta parte do script tem a seguinte função:

1. Tenta recuperar um namespace do Barramento de Serviço com o nome fornecido.
2. Se o namespace for encontrado, ele informará o que foi encontrado.
3. Se o namespace não for encontrado, ele vai criar o namespace e, em seguida, recuperar o namespace recentemente criado.

		``` powershell
		
		$Namespace = "MyServiceBusNS"
		$Location = "West US"
		
		# Query to see if the namespace currently exists
		$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

		# Check if the namespace already exists or needs to be created
		if ($CurrentNamespace)
		{
		    Write-Output "The namespace [$Namespace] already exists in the [$($CurrentNamespace.Region)] region."
		}
		else
		{
		    Write-Host "The [$Namespace] namespace does not exist."
		    Write-Output "Creating the [$Namespace] namespace in the [$Location] region..."
		    New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace false -NamespaceType Messaging
		    $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
		    Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
		}
		```
Para provisionar outras entidades do Barramento de Serviço, crie uma instância do objeto `NamespaceManager` do SDK. Você pode usar o cmdlet [Get-AzureSBAuthorizationRule] para recuperar uma regra de autorização que é usada para fornecer uma cadeia de conexão. Este exemplo armazena uma referência à instância `NamespaceManager` na variável `$NamespaceManager`. Posteriormente, o script usará `$NamespaceManager` para provisionar outras entidades.

		``` powershell
		$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
		# Create the NamespaceManager object to create the Event Hub
		Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
		$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
		Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
		```
## Provisionamento de outras entidades do Barramento de Serviço

Para provisionar outras entidades, como filas, tópicos e Hubs de Eventos, é possível usar a [API do .NET para Barramento de Serviço]. Há referências de exemplos mais detalhados, incluindo outras entidades, no fim deste artigo.

### Criar um Hub de Evento

Essa parte do script cria mais quatro variáveis locais. Essas variáveis serão usadas para criar uma instância de um objeto `EventHubDescription`. O script tem a seguinte função:

1. Usando o objeto `NamespaceManager`, verifica se o Hub de Eventos identificou a existência de `$Path`.
2. Se não existir, cria um `EventHubDescription` e o passa para o método `CreateEventHubIfNotExists` da classe `NamespaceManager`.
3. Depois de determinar se o Hub de Eventos está disponível, cria um grupo de consumidores usando `ConsumerGroupDescription` e `NamespaceManager`.

		``` powershell
		
		$Path  = "MyEventHub"
		$PartitionCount = 12
		$MessageRetentionInDays = 7
		$UserMetadata = $null
		$ConsumerGroupName = "MyConsumerGroup"
		
		# Check if the Event Hub already exists
		if ($NamespaceManager.EventHubExists($Path))
		{
		    Write-Output "The [$Path] event hub already exists in the [$Namespace] namespace."  
		}
		else
		{
		    Write-Output "Creating the [$Path] event hub in the [$Namespace] namespace: PartitionCount=[$PartitionCount] MessageRetentionInDays=[$MessageRetentionInDays]..."
		    $EventHubDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.EventHubDescription -ArgumentList $Path
		    $EventHubDescription.PartitionCount = $PartitionCount
		    $EventHubDescription.MessageRetentionInDays = $MessageRetentionInDays
		    $EventHubDescription.UserMetadata = $UserMetadata
		    $EventHubDescription.Path = $Path
		    $NamespaceManager.CreateEventHubIfNotExists($EventHubDescription);
		    Write-Output "The [$Path] event hub in the [$Namespace] namespace has been successfully created."
		}
		
		# Create the consumer group if it doesn't exist
		Write-Output "Creating the consumer group [$ConsumerGroupName] for the [$Path] event hub..."
		$ConsumerGroupDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.ConsumerGroupDescription -ArgumentList $Path, $ConsumerGroupName
		$ConsumerGroupDescription.UserMetadata = $ConsumerGroupUserMetadata
		$NamespaceManager.CreateConsumerGroupIfNotExists($ConsumerGroupDescription);
		Write-Output "The consumer group [$ConsumerGroupName] for the [$Path] event hub has been successfully created."
		```

### Criar uma fila

Para criar uma fila ou um tópico, execute uma verificação de namespace., como na seção anterior.

	if ($NamespaceManager.QueueExists($Path))
	{
	    Write-Output "The [$Path] queue already exists in the [$Namespace] namespace." 
	}
	else
	{
	    Write-Output "Creating the [$Path] queue in the [$Namespace] namespace..."
	    $QueueDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.QueueDescription -ArgumentList $Path
	    if ($AutoDeleteOnIdle -ge 5)
	    {
	        $QueueDescription.AutoDeleteOnIdle = [System.TimeSpan]::FromMinutes($AutoDeleteOnIdle)
	    }
	    if ($DefaultMessageTimeToLive -gt 0)
	    {
	        $QueueDescription.DefaultMessageTimeToLive = [System.TimeSpan]::FromMinutes($DefaultMessageTimeToLive)
	    }
	    if ($DuplicateDetectionHistoryTimeWindow -gt 0)
	    {
	        $QueueDescription.DuplicateDetectionHistoryTimeWindow = [System.TimeSpan]::FromMinutes($DuplicateDetectionHistoryTimeWindow)
	    }
	    $QueueDescription.EnableBatchedOperations = $EnableBatchedOperations
	    $QueueDescription.EnableDeadLetteringOnMessageExpiration = $EnableDeadLetteringOnMessageExpiration
	    $QueueDescription.EnableExpress = $EnableExpress
	    $QueueDescription.EnablePartitioning = $EnablePartitioning
	    $QueueDescription.ForwardDeadLetteredMessagesTo = $ForwardDeadLetteredMessagesTo
	    $QueueDescription.ForwardTo = $ForwardTo
	    $QueueDescription.IsAnonymousAccessible = $IsAnonymousAccessible
	    if ($LockDuration -gt 0)
	    {
	        $QueueDescription.LockDuration = [System.TimeSpan]::FromSeconds($LockDuration)
	    }
	    $QueueDescription.MaxDeliveryCount = $MaxDeliveryCount
	    $QueueDescription.MaxSizeInMegabytes = $MaxSizeInMegabytes
	    $QueueDescription.RequiresDuplicateDetection = $RequiresDuplicateDetection
	    $QueueDescription.RequiresSession = $RequiresSession
	    if ($EnablePartitioning)
	    {
	        $QueueDescription.SupportOrdering = $False
	    }
	    else
	    {
	        $QueueDescription.SupportOrdering = $SupportOrdering
	    }
	    $QueueDescription.UserMetadata = $UserMetadata
	    $NamespaceManager.CreateQueue($QueueDescription);
	}

### Criar um tópico

	if ($NamespaceManager.TopicExists($Path))
	{
	    Write-Output "The [$Path] topic already exists in the [$Namespace] namespace." 
	}
	else
	{
	    Write-Output "Creating the [$Path] topic in the [$Namespace] namespace..."
	    $TopicDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.TopicDescription -ArgumentList $Path
	    if ($AutoDeleteOnIdle -ge 5)
	    {
	        $TopicDescription.AutoDeleteOnIdle = [System.TimeSpan]::FromMinutes($AutoDeleteOnIdle)
	    }
	    if ($DefaultMessageTimeToLive -gt 0)
	    {
	        $TopicDescription.DefaultMessageTimeToLive = [System.TimeSpan]::FromMinutes($DefaultMessageTimeToLive)
	    }
	    if ($DuplicateDetectionHistoryTimeWindow -gt 0)
	    {
	        $TopicDescription.DuplicateDetectionHistoryTimeWindow = [System.TimeSpan]::FromMinutes($DuplicateDetectionHistoryTimeWindow)
	    }
	    $TopicDescription.EnableBatchedOperations = $EnableBatchedOperations
	    $TopicDescription.EnableExpress = $EnableExpress
	    $TopicDescription.EnableFilteringMessagesBeforePublishing = $EnableFilteringMessagesBeforePublishing
	    $TopicDescription.EnablePartitioning = $EnablePartitioning
	    $TopicDescription.IsAnonymousAccessible = $IsAnonymousAccessible
	    $TopicDescription.MaxSizeInMegabytes = $MaxSizeInMegabytes
	    $TopicDescription.RequiresDuplicateDetection = $RequiresDuplicateDetection
	    if ($EnablePartitioning)
	    {
	        $TopicDescription.SupportOrdering = $False
	    }
	    else
	    {
	        $TopicDescription.SupportOrdering = $SupportOrdering
	    }
	    $TopicDescription.UserMetadata = $UserMetadata
	    $NamespaceManager.CreateTopic($TopicDescription);
	}

## Próximas etapas

Este artigo fornece uma estrutura de tópicos para o provisionamento de entidades do Barramento de Serviço usando o PowerShell. Embora haja um número limitado de cmdlets do PowerShell disponíveis para gerenciar entidades de mensagem do Barramento de Serviço, ao fazer referência ao assembly Microsoft.ServiceBus.dll, praticamente qualquer operação que você pode executar usando as bibliotecas do cliente .NET, você também poderá executar em um script do PowerShell.

Os exemplos mais detalhados estão disponíveis nessas postagens de blogs:

- [Como criar filas, tópicos e assinaturas do Barramento de Serviço usando um script do PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Como criar um namespace do Barramento de Serviço e um Hub de Eventos usando um script do PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Alguns scripts prontos também estão disponíveis para download : - [Scripts do PowerShell do Barramento de Serviço](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[Opções de compra]: http://azure.microsoft.com/pricing/purchase-options/
[Ofertas para membros]: http://azure.microsoft.com/pricing/member-offers/
[Avaliação gratuita]: http://azure.microsoft.com/pricing/free-trial/
[pacote NuGet do Barramento de Serviço]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[API do .NET para Barramento de Serviço]: https://msdn.microsoft.com/library/microsoft.servicebus.aspx

<!---HONumber=58--> 