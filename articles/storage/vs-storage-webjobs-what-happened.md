<properties 
	pageTitle="Introdução ao Armazenamento do Azure" 
	description="Descreve o que aconteceu durante a criação de um armazenamento do Azure em um projeto Azure WebJob do Visual Studio" 
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
	ms.date="07/13/2015" 
	ms.author="patshea123"/>

# O que aconteceu com meu projeto?

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-webjobs-getting-started-blobs.md)
> - [What Happened](vs-storage-webjobs-what-happened.md)

###<span id="whathappened">O que aconteceu com meu projeto?</span>

##### Referências adicionadas

O pacote do NuGet do Armazenamento do Azure foi adicionado ao seu projeto do Visual Studio. Esse pacote adiciona as referências de .NET a seguir:

- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.WindowsAzure.ConfigurationManager`
- `Microsoft.WindowsAzure.Storage`
- `Newtonsoft.Json`
- `System.Data`
- `System.Spatial`

#####Cadeia de conexão do Armazenamento do Azure adicionada 
No arquivo App.config do seu projeto, o `AzureWebJobsStorage` e o `AzureWebJobsDashboard` foram atualizados com a cadeia de conexão e chave da conta de armazenamento e chave selecionada.

Para obter mais informações, consulte [Recursos Recomendados para Trabalhos Web do Azure](http://go.microsoft.com/fwlink/?linkid=390226).

<!---HONumber=August15_HO6-->