---
title: Início rápido do C# do SDK de Pesquisa Personalizada | Microsoft Docs
titleSuffix: Cognitive Services
description: Configurar aplicativo de console C# do SDK de Pesquisa Personalizada.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/06/2018
ms.author: scottwhi
ms.openlocfilehash: 6c9917e3a63515f36b386e444edcc53de07799fc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46949920"
---
# <a name="c-sdk-quickstart"></a>Início rápido do SDK do C#

O SDK de Pesquisa Personalizada do Bing fornece um modelo de programação mais fácil que a API REST da Pesquisa Personalizada do Bing. Esta seção guia você ao fazer suas primeiras chamadas à Pesquisa Personalizada usando o SDK do C#.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa de:

- Uma instância de pesquisa personalizada pronta para uso. Consulte [Criar a primeira instância da Pesquisa Personalizada do Bing](quick-start.md).  
  
- Uma chave de assinatura. Você pode obter uma chave de assinatura quando ativar sua [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), ou pode usar uma chave de assinatura paga do painel do Azure (veja [Conta de API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).  
  
- Visual Studio 2017 instalado. Caso ainda não o tenha, você pode baixar o [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **gratuito**.  
  
- O pacote [Pesquisa Personalizada do NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) instalado. No Gerenciador de Soluções no Visual Studio, clique com o botão direito do mouse no seu projeto e selecione `Manage NuGet Packages` no menu. Instale o pacote `Microsoft.Azure.CognitiveServices.Search.CustomSearch`.

A instalação do pacote Pesquisa Personalizada do NuGet também instala os assemblies a seguir:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json



## <a name="run-the-code"></a>Executar o código

Para executar esse exemplo, siga estas etapas:

1. Abra o Visual Studio 2017.
  
2. Clique no menu **Arquivo**, clique em **Novo**, **Projeto** e, em seguida, no modelo **Aplicativo de Console do Visual C#**.
  
3. Nomeie a solução CustomSearchSolution e navegue até a pasta na qual a colocará.
  
4. Clique em OK para criar a solução.  
  
4. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto (ele tem o mesmo nome que a solução) e selecione `Manage NuGet Packages`. No Gerenciador de Pacotes do NuGet, clique em **Procurar**. Insira Microsoft.Azure.CognitiveServices.Search.CustomSearch na caixa de pesquisa e pressione Enter. Selecione o pacote e clique em Instalar.  
  
4. Copie o código a seguir no arquivo Program.cs. Substitua **SUA-CHAVE-DE-ASSINATURA** e **SUA-ID-DE-CONFIGURAÇÃO-PERSONALIZADA** pela chave de assinatura e a ID de configuração.  
  
    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

    namespace CustomSrchSDK
    {
        class Program
        {
            static void Main(string[] args)
            {

                var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));

                try
                {
                    // This will look up a single query (Xbox).
                    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
                    Console.WriteLine("Searched for Query# \" Xbox \"");

                    //WebPages
                    if (webData?.WebPages?.Value?.Count > 0)
                    {
                        // find the first web page
                        var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                        if (firstWebPagesResult != null)
                        {
                            Console.WriteLine("Number of webpage results {0}", webData.WebPages.Value.Count);
                            Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
                            Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find web results!");
                        }
                    }
                    else
                    {
                        Console.WriteLine("Didn't see any Web data..");
                    }
                }
                catch (Exception ex)
                {
                    Console.WriteLine("Encountered exception. " + ex.Message);
                }

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

        }
    }
    ```  
  
5. Crie e execute (depure) a solução. 




## <a name="breaking-it-down"></a>Analisando por partes

Depois de instalar o pacote de Pesquisa Personalizada do NuGet, você precisa adicionar uma diretiva using a ele.

```csharp
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
```

Em seguida, crie uma instância para o cliente de pesquisa personalizada, que você usa para fazer solicitações de pesquisa. Substitua `YOUR-SUBSCRIPTION-KEY` pela sua chave.

```csharp
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));
```

Agora você pode usar o cliente para enviar uma solicitação de pesquisa. Certifique-se de substituir sua `YOUR-CUSTOM-CONFIG-ID` pela ID de configuração da sua instância (você pode encontrar a ID no [portal de Pesquisa Personalizada](https://www.customsearch.ai/)). Este exemplo pesquisa por Xbox. Atualize conforme necessário.

```csharp
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
```

O método `SearchAsync` retorna um objeto `WebData`. Use `WebData` para iterar por meio de quaisquer `WebPages` que tenham sido encontrados. Esse código encontra o primeiro resultado da página da Web e imprime `Name` e `URL` da página da Web.

```csharp
//WebPages
if (webData?.WebPages?.Value?.Count > 0)
{
    // find the first web page
    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

    if (firstWebPagesResult != null)
    {
        Console.WriteLine("Webpage Results#{0}", webData.WebPages.Value.Count);
        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
    }
    else
    {
        Console.WriteLine("Couldn't find web results!");
    }
}
else
{
    Console.WriteLine("Didn't see any Web data..");
}

```


## <a name="next-steps"></a>Próximas etapas

Confira os exemplos do SDK. Cada exemplo inclui um arquivo Leiame com detalhes sobre os pré-requisitos e a instalação/execução dos exemplos.

* Introdução aos [exemplos .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7) 
    * [Pacote NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)
    * Consulte também [bibliotecas .NET](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingCustomSearch) para definições e dependências.
* Introdução aos [exemplos NodeJS](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
    * Consulte também [bibliotecas NodeJS](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/customSearch) para definições e dependências.
* Introdução aos [exemplos Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
    * Consulte também as [bibliotecas do Java](https://github.com/Azure/azure-sdk-for-java/tree/master/cognitiveservices/azure-customsearch) para obter definições e dependências.
* Introdução aos [exemplos Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 
    * Consulte também as [bibliotecas do Python](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-customsearch) para obter definições e dependências.

