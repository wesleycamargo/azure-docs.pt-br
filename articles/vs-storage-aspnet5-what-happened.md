<properties 
	pageTitle="Introdução ao Armazenamento do Azure" 
	description="" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Introdução](/documentation/articles/vs-storage-aspnet5-getting-started-blobs/)
> - [O que aconteceu](/documentation/articles/vs-storage-aspnet5-what-happened/)

### <span id="whathappened">O que aconteceu com meu projeto?</span>

##### Referências adicionadas

O pacote do NuGet do armazenamento do Azure foi adicionado ao seu projeto do Visual Studio.  
Esse pacote adiciona as referências de .NET a seguir:

- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.WindowsAzure.Configuration`
- `Microsoft.WindowsAzure.Storage`
- `Newtonsoft.Json`
- `System.Data`
- `System.Spatial`

Além disso, o pacote NuGet **Microsoft.Framework.ConfigurationModel.Json** foi adicionado.

##### Cadeia de conexão do Armazenamento do Azure adicionada 
No arquivo config.json do seu projeto, foi criado um elemento com a cadeia de conexão e chave da conta de armazenamento selecionada.

Para obter mais informações, consulte [ASP.NET 5](http://www.asp.net/vnext).
<!--HONumber=42-->
