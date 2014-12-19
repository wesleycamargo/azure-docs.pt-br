       <properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="vs-what-happened" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Introdução](/documentation/articles/vs-mobile-services-dotnet-getting-started/)
> - [O que aconteceu](/documentation/articles/vs-mobile-services-dotnet-what-happened/)

###<span id="whathappened">O que aconteceu com meu projeto?</span>

#####Referências adicionadas

O pacote NuGet de Serviços Móveis do Azure foi adicionado ao seu projeto. Por isso, as referências .NET a seguir foram adicionadas ao projeto:

- `Microsoft.WindowsAzure.Mobile`
- `Microsoft.WindowsAzure.Mobile.Ext`
- `Newtonsoft.Json`
- `System.Net.Http.Extensions`
- `System.Net.Http.Primitives` 

#####Valores de cadeia de conexão para Serviços Móveis

No seu arquivo App.xaml.cs, um objeto **MobileServiceClient** foi criado com o URL do aplicativo do serviço móvel selecionado e a chave do aplicativo. 

[Saiba mais sobre serviços móveis](http://azure.microsoft.com/documentation/services/mobile-services/)
