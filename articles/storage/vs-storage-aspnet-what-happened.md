<properties 
	pageTitle="Introdução ao Armazenamento do Azure" 
	description="Descreve o que aconteceu durante a criação de um armazenamento do Azure em um projeto de Visual Studio ASP.NET" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Introdução](vs-storage-aspnet-getting-started-blobs.md)
> - [O que aconteceu](vs-storage-aspnet-what-happened.md)

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

##### Cadeia de conexão do Armazenamento do Azure adicionada 
No arquivo web.config do seu projeto, foi criado um elemento com a cadeia de conexão e chave da conta de armazenamento selecionada.

Para obter mais informações, consulte [ASP.NET](http://www.asp.net).
<!--HONumber=47-->
 