<properties title="Introdu&ccedil;&atilde;o ao armazenamento do Azure" pageTitle="Introdu&ccedil;&atilde;o ao armazenamento do Azure" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [Introdução][Introdução]
> -   [O que aconteceu][O que aconteceu]

### <span id="whathappened">O que aconteceu com meu projeto?</span>

##### Referências adicionadas

O pacote NuGet do Armazenamento do Azure foi adicionado ao seu projeto do Visual Studio.
Este pacote adiciona as seguintes referências .NET:

-   `Microsoft.Data.Edm`
-   `Microsoft.Data.OData`
-   `Microsoft.Data.Services.Client`
-   `Microsoft.WindowsAzure.Configuration`
-   `Microsoft.WindowsAzure.Storage`
-   `Newtonsoft.Json`
-   `System.Data`
-   `System.Spatial`

Também, o pacote NuGet **Microsoft.Framework.ConfigurationModel.Json** foi adicionado.

##### Cadeia de conexão do Armazenamento do Azure adicionada

No arquivo config.json do seu projeto, foi criado um elemento com a cadeia de conexão e chave da conta de armazenamento selecionada.

Para obter mais informações, consulte [ASP.NET vNext][ASP.NET vNext].

  [Introdução]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs/
  [O que aconteceu]: /documentation/articles/vs-storage-aspnet-vnext-what-happened/
  [ASP.NET vNext]: http://www.asp.net/vnext
