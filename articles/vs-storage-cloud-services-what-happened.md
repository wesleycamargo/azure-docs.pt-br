<properties title="Getting Started with Azure Storage" pageTitle="Introdução ao Armazenamento do Azure" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="vs-what-happened" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Introdução](/documentation/articles/vs-storage-cloud-services-getting-started-blobs/)
> - [O que aconteceu](/documentation/articles/vs-storage-cloud-services-what-happened/)

###<span id="whathappened">O que aconteceu com meu projeto?</span>

###### Referências adicionadas

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

######Cadeia de conexão do Armazenamento do Azure adicionada 
Elementos foram criados com a cadeia de conexão e chave da conta de armazenamento selecionada. Foram realizadas modificações nos seguintes arquivos:

- `ServiceDefinition.csdef`
- `ServiceConfiguration.Cloud.cscfg`
- `ServiceConfiguration.Local.cscfg`

