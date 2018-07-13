---
title: Início rápido do C# do SDK de Pesquisa Personalizada | Microsoft Docs
titleSuffix: Cognitive Services
description: Configurar aplicativo de console C# do SDK de Pesquisa Personalizada.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 01/31/2018
ms.author: rosh
ms.openlocfilehash: 59b208b53bec974433c50c0e2304dc96bd9bd09e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363365"
---
# <a name="custom-search-sdk-c-quickstart"></a>Início rápido do C# do SDK de Pesquisa Personalizada

O SDK de Pesquisa Personalizada do Bing contém a funcionalidade da API REST para pesquisa de entidade e análise dos resultados.

## <a name="application-dependencies"></a>Dependências de aplicativo

Para configurar uma aplicação de console usando o SDK de Pesquisa Personalizada do Bing, navegue até a opção `Manage NuGet Packages` do Gerenciador de Soluções no Visual Studio. Adicione o pacote `Microsoft.Azure.CognitiveServices.Search.CustomSearch`.

A instalação do pacote [Pesquisa Personalizada do NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) também instala dependências, incluindo os conjuntos a seguir:
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="entity-search-client"></a>Cliente de Pesquisa de Entidade

Para criar uma instância do cliente CustomSearchAPI, adicione usando diretivas:
```
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

```

Instanciar o cliente de pesquisa personalizada: substitua `YOUR-CUSTOM-SEARCH-KEY` e `YOUR-CUSTOM-CONFIG-ID` pela chave de acesso e pela ID de configuração do ponto de extremidade de API atribuído em [Minhas instâncias](https://www.customsearch.ai/).
```
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));

```
Use o cliente para pesquisar com um texto de consulta:
```
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;

```
## <a name="parse-the-results"></a>Analise os resultados

O método `SearchAsync` retornará um objeto `WebData` que contém `WebPages`, se algum for localizado para a consulta. Este código localiza o primeiro resultado e obtém seus `Name` e `URL`.
```
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
 
Console.WriteLine("Searched for Query# \" Xbox \"");

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
## <a name="complete-console-application"></a>Aplicativo do console completo

O código a seguir pesquisa a consulta "Xbox" e imprime `Name` e `URL` para o primeiro resultado da Web.
```
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

namespace CustomSrchSDK
{
    class Program
    {
        static void Main(string[] args)
        {

            var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));

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
            }
            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

            Console.WriteLine("Any key to exit...");
            Console.ReadKey();
        }

    }
}


```

## <a name="next-steps"></a>Próximas etapas

[Exemplos de SDK do .NET dos serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
