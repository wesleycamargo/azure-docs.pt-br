---
title: 'Início rápido: SDK de Verificação Ortográfica do Bing, C#'
titlesuffix: Azure Cognitive Services
description: Instalação do aplicativo de console do SDK de Verificação Ortográfica
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 01/30/2018
ms.author: v-gedod
ms.openlocfilehash: 9e7b2bfd25279420272132ef54e4c970333e49c0
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55882039"
---
# <a name="quickstart-bing-spell-check-sdk-with-c"></a>Início Rápido: SDK de Verificação Ortográfica do Bing com C#

O SDK de Verificação Ortográfica do Bing contém a funcionalidade da API REST para verificação ortográfica.

## <a name="application-dependencies"></a>Dependências de aplicativo
Obtenha uma [chave de acesso de Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/) em **Pesquisar**.  Veja também [Cognitive Services Pricing - API de Pesquisa do Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Para configurar um aplicativo de console usando o SDK de Verificação Ortográfica do Bing, navegue até a opção `Manage NuGet Packages` do Gerenciador de Soluções no Visual Studio. Adicione o pacote `Microsoft.Azure.CognitiveServices.Language.SpellCheck`.

Instalar o [pacote SDK de SpellCheck](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.SpellCheck/1.2.0) também instala as dependências, incluindo:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="spell-check-client"></a>Cliente de verificação ortográfica

Para criar uma instância do `SpellCheckClient` cliente, adicione usando diretiva:

```cs
using Microsoft.Azure.CognitiveServices.Language.SpellCheck;
```

Em seguida, instancie o cliente:

```cs
var client = new SpellCheckClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
```

Use o cliente para verificar a ortografia do texto. O parâmetro `acceptLanguage` é opcional:

```cs
var result = client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof", acceptLanguage: "en-US", market: "en-US").Result;
Console.WriteLine("Correction for Query# \"bill gatas\"");
```

Analisar os resultados:

```cs
// SpellCheck Results
if (result?.Body.FlaggedTokens?.Count > 0)
{
    // find the first spellcheck result
    var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();

    if (firstspellCheckResult != null)
    {
        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);

        var suggestions = firstspellCheckResult.Suggestions;
        if (suggestions?.Count > 0)
        {
            var firstSuggestion = suggestions.FirstOrDefault();
            Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
            Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
        }
        }
        else
        {
            Console.WriteLine("Couldn't get any Spell check results!");
        }
    }
    else
    {
        Console.WriteLine("Didn't see any SpellCheck results..");
    }
```

## <a name="complete-console-application"></a>Aplicativo de console completo

O aplicativo de console a seguir executa o código anterior:

```cs
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Language.SpellCheck;

namespace SpellCheckSDK
{
    class Program
    {
        static void Main(string[] args)
        {
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));

            try
            {
                var result = client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof", acceptLanguage: "en-US", market:"en-US").Result;
                Console.WriteLine("Correction for Query# \"bill gatas\"");

                // SpellCheck Results
                if (result?.Body.FlaggedTokens?.Count > 0)
                {
                    // find the first spellcheck result
                    var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();

                    if (firstspellCheckResult != null)
                    {
                        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
                        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
                        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
                        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);

                        var suggestions = firstspellCheckResult.Suggestions;
                        if (suggestions?.Count > 0)
                        {
                            var firstSuggestion = suggestions.FirstOrDefault();
                            Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
                            Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
                        }
                    }
                    else
                    {
                        Console.WriteLine("Couldn't get any Spell check results!");
                    }
                }
                else
                {
                    Console.WriteLine("Didn't see any SpellCheck results..");
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

            SpellCheckError("YOUR-ACCESS-KEY");

            Console.WriteLine("Any key to exit...");
            Console.ReadKey();
        }

        // This will trigger an error response from the API.
        public static void SpellCheckError(string subscriptionKey)
        {
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                var result = client.SpellCheckerAsync(text: "", mode: "proof").Result;
            }
            catch (Exception ex)
            {
                Console.WriteLine("Correction for Query# \"empty text field\"");

                if (ex.GetBaseException().GetType() == typeof(Exception) )
                {
                    Console.WriteLine("Encountered exception. " + ex.Message);
                }
            }
        }
    }
}

```

## <a name="next-steps"></a>Próximas etapas

[Exemplos de SDK do .NET dos Serviços Cognitivos](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
