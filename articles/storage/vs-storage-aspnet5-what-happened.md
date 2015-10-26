<properties
	pageTitle="O que aconteceu com meu projeto do ASP.NET 5 (serviços conectados do Visual Studio) | Armazenamento do Microsoft Azure"
	description="Descreve o que acontece após a conexão a uma conta de armazenamento do Azure em um projeto do ASP.NET 5 do Visual Studio usando os serviços conectados do Visual Studio"
	services="storage"
	documentationCenter=""
	authors="patshea123"
	manager="douge"
	editor="tglee"/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-what-happened"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/03/2015"
	ms.author="patshea"/>

# O que aconteceu com meu projeto do ASP.NET 5 (serviços conectados do Armazenamento do Azure do Visual Studio)?

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet5-getting-started-blobs.md)
> - [What Happened](vs-storage-aspnet5-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet5-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet5-getting-started-queues.md)
> - [Tables](vs-storage-aspnet5-getting-started-tables.md)

## Referências adicionadas

O pacote do NuGet do armazenamento do Azure foi adicionado ao seu projeto do Visual Studio. Esse pacote adiciona as referências de .NET a seguir:

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.Configuration**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

Além disso, o pacote NuGet **Microsoft.Framework.Configuration.Json** foi adicionado.

## Cadeia de conexão do Armazenamento do Azure adicionada
No arquivo config.json do seu projeto, foi criado um elemento com a cadeia de conexão e chave da conta de armazenamento selecionada.

Para obter mais informações, veja [ASP.NET 5](http://www.asp.net/vnext).

<!---HONumber=Oct15_HO3-->