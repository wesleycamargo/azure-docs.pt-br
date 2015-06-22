<properties
	pageTitle="Gerenciar o Barramento de Serviço com o PowerShell"
	description="Gerenciar o Barramento de Serviço com scripts do PowerShell em vez do .NET"
	services="service-bus"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2015"
	ms.author="sethm"/>

# Gerenciar o Barramento de Serviço com o PowerShell

## Visão geral

O PowerShell do Microsoft Azure é um ambiente de script que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Neste artigo, você aprenderá como usar o PowerShell para provisionar e gerenciar entidades do Barramento de Serviço como namespaces, filas e hubs de eventos usando um console local do PowerShell do Azure.

## Pré-requisitos

Antes de começar este artigo, você deve ter o seguinte:

- Uma assinatura do Azure. O Azure é uma plataforma baseada em assinatura. Para obter mais
informações sobre como adquirir uma assinatura, consulte [Opções de compra][opções de compra do azure],
[Ofertas de membro][ofertas de membro azure] ou [Avaliação gratuita][avaliação gratuita do azure].

- Um computador com o PowerShell do Azure. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][instalar e configurar o Powershell].

- Um entendimento geral de scripts do PowerShell, pacotes NuGet e o .NET Framework.

## Incluindo uma referência ao assembly .NET para o Barramento de Serviço

Há um número limitado de cmdlets do PowerShell para gerenciar o Barramento de Serviço. Para provisionar
entidades que não são expostas através dos cmdlets existentes, usaremos o cliente .NET para
Barramento de serviço usando o [pacote NuGet do Service Bus][].

Primeiro, queremos garantir que nosso script possa localizar o assembly **Microsoft.ServiceBus.dll**, que está instalado com o pacote NuGet. Para que seja flexível, o script irá executar essas etapas:

1. O script determina o caminho onde ele foi chamado.
2. Ele recorre o caminho até encontrar uma pasta chamada `pacotes`. Esta é uma pasta criada durante a instalação de pacotes NuGet.
3. Ele pesquisa recursivamente a pasta `pacotes` por um assembly denominado **Microsoft.ServiceBus.dll**.
4. Depois, faz referência ao assembly para que os tipos estejam disponíveis para uso posterior.

Aqui está como essas etapas são implementadas em um script do PowerShell:

```powershell

try
{
    # WARNING: Make sure to reference the latest version of Microsoft.ServiceBus.dll
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

Ao trabalhar com namespaces do Barramento de Serviço, você pode usar dois cmdlets em vez do SDK do .NET. Especificamente, você pode usar o [Get-AzureSBNamespace][] e [New-AzureSBNamespace][].

Em nosso exemplo, vamos criar algumas variáveis locais no script, `$Namespace`e `$Location`.
- `$Namespace`é o nome do namespace do Barramento de Serviço com o qual queremos trabalhar.
- `$Location`identifica o data center em que iremos provisionar o namespace.
- `$CurrentNamespace`armazena o namespace de referência que recuperamos (ou criamos).

Em um script real, `$Namespace`e `$Location`podem ser passados como parâmetros.

Essa parte do script será

1. Tentativa de recuperar um namespace de Barramento de Serviço com o nome fornecido.
2. Se o namespace for encontrado, ele simplesmente informa o que foi encontrado.
3. Se o namespace não for encontrado, ele cria o namespace e, em seguida, recupera o namespace recém-criado.

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

Para provisionar outras entidades do Barramento de Serviço, podemos criar uma instância do objeto `NamespaceManager` do SDK.
Podemos usar o cmdlet [Get-AzureSBAuthorizationRule][] para recuperar uma regra de autorização é usada para fornecer uma cadeia de conexão. Armazenaremos uma referência para a instância `NamespaceManager` na variável `$NamespaceManager`. Nós usaremos `$NamespaceManager`posteriormente no script para provisionar outras entidades.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the event hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## Provisionamento de outras entidades do Barramento de Serviço

Para provisionar outras entidades, como filas, tópicos e hubs de eventos, podemos usar a API do .NET [ para o Barramento de Serviço][dotnet-servicebus-api]. Vamos nos concentrar apenas em hubs de eventos para este artigo, mas as etapas para outras entidades são semelhantes. Além disso, os exemplos mais detalhados, incluindo outras entidades, são referenciados no final deste artigo.

Nesta parte do script, criamos mais quatro variáveis locais. Essas variáveis serão usadas para criar uma instância `EventHubDescription`. Faremos o seguinte:

1. Usando o objeto `NamespaceManager`, vamos verificar primeiro para ver se o hub de eventos identificado por `$Path`existe.
2. Se não existir, criamos um `EventHubDescription` e passaremos aquele para o método `NamespaceManager` classe `CreateEventHubIfNotExists`.
3. Depois que soubermos se o hub de eventos está disponível, podemos criar um grupo de consumidor usando `ConsumerGroupDescription` e `NamespaceManager`.

``` powershell

$Path  = "MyEventHub"
$PartitionCount = 12
$MessageRetentionInDays = 7
$UserMetadata = $null
$ConsumerGroupName = "MyConsumerGroup"

# Check if the event hub already exists
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

## Próximas etapas

Este artigo fornece uma estrutura de tópicos para o provisionamento de entidades do Barramento de Serviço usando o PowerShell. Tudo o que você pode fazer usando as bibliotecas de cliente .NET, você também pode fazer em um script do PowerShell.

Os exemplos mais detalhados estão disponíveis nessas postagens de blogs:

- [Como criar filas, tópicos e assinaturas usando um script do PowerShell do Barramento de Serviço](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Como criar um Namespace de Barramento de Serviço e um Hub de eventos usando um script do PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Alguns scripts prontos também estão disponíveis para download:
- [Scripts do PowerShell do Barramento de Serviço](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[powershell-install-configure]: ../install-configure-powershell/
[Service Bus NuGet package]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[dotnet-servicebus-api]: https://msdn.microsoft.com/library/microsoft.servicebus.aspx

<!--HONumber=47-->
 