---
title: 'Início Rápido: usando C# para chamar a API de Análise de Texto'
titleSuffix: Azure Cognitive Services
description: Obtenha informações e exemplos de código para ajudá-lo a começar a usar rapidamente a API de Análise de Texto.
services: cognitive-services
author: ashmaka
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 04/12/2019
ms.author: assafi
ms.openlocfilehash: 7051f1c1ce43be7dce5d88a06fccee9d876a70f4
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60010170"
---
# <a name="quickstart-using-c-to-call-the-text-analytics-cognitive-service"></a>Início Rápido: usando C# para chamar o Serviço Cognitivo de Análise de Texto
<a name="HOLTop"></a>

Este artigo mostra como detectar a linguagem, analisar o sentimento e extrair frases-chave usando as  [APIs de Análise de Texto](//go.microsoft.com/fwlink/?LinkID=759711)  com C#. O código foi gravado para funcionar em um aplicativo .NET Core, com referências mínimas a bibliotecas externas, para que você também possa executá-lo em Linux ou MacOS. O código-fonte deste Início Rápido pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics).

Consulte as [definições da API](//go.microsoft.com/fwlink/?LinkID=759346) para obter a documentação técnica das APIs.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Também é necessário ter o [ponto de extremidade e a chave de acesso](../How-tos/text-analytics-how-to-access-key.md) que foi gerada para você durante a inscrição.

## <a name="install-the-nuget-sdk-package"></a>Instalar o pacote do SDK do NuGet
1. Crie uma solução de Console usando o `.netcoreapp2.0` e posterior no Visual Studio.
1. Clique com o botão direito do mouse na solução e em **Gerenciar pacotes NuGet para a solução**
1. Selecione a guia **Procurar** e pesquise por **Microsoft.Azure.CognitiveServices.Language.TextAnalytics**

> [!Tip]
>  Embora você possa chamar os [pontos de extremidade HTTP](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6) diretamente do C#, o SDK Microsoft.Azure.CognitiveServices.Language torna mais fácil chamar o serviço sem precisar se preocupar com a serialização e desserialização do JSON.
>
> Alguns links úteis:
> - [Página NuGet do SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
> - [Código do SD ](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)

## <a name="call-the-text-analytics-api-using-the-sdk"></a>Chame a API de Análise de Texto usando o SDK

1. Substitua Program.cs pelo código fornecido abaixo. Este programa demonstra as funcionalidades da API de Análise de Texto em três seções (extração de linguagem, extração de frases-chave e análise de sentimento).
1. Substitua o valor de cabeçalho `Ocp-Apim-Subscription-Key` por uma chave de acesso válida para sua assinatura.
1. Substitua a região em `Endpoint`. É possível localizar o ponto de extremidade na seção de visão geral do recurso de Análise de Texto no [portal do Azure](<https://ms.portal.azure.com>). Inclua apenas esta parte do ponto de extremidade: "https://[region].api.cognitive.microsoft.com".
1. Execute o programa.

```csharp
using System;
using System.Collections.Generic;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;

using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
using Microsoft.Rest;

namespace ConsoleApp1
{
    class Program
    {
        private const string SubscriptionKey = ""; //Insert your Text Anaytics subscription key

        private class ApiKeyServiceClientCredentials : ServiceClientCredentials
        {
            public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", SubscriptionKey);
                return base.ProcessHttpRequestAsync(request, cancellationToken);
            }
        }

        static async Task Main(string[] args)
        {

            // Create a client.
            ITextAnalyticsClient client = new TextAnalyticsClient(new ApiKeyServiceClientCredentials())
            {
                Endpoint = "https://westus.api.cognitive.microsoft.com"
            }; //Replace 'westus' with the correct region for your Text Analytics subscription

            Console.OutputEncoding = System.Text.Encoding.UTF8;

            // Extracting language
            Console.WriteLine("===== LANGUAGE EXTRACTION ======");

            var langResults = await client.DetectLanguageAsync(
                false,
                new LanguageBatchInput(
                    new List<LanguageInput>
                        {
                          new LanguageInput(id: "1", text: "This is a document written in English."),
                          new LanguageInput(id: "2", text: "Este es un document escrito en Español."),
                          new LanguageInput(id: "3", text: "这是一个用中文写的文件")
                        }));

            // Printing language results.
            foreach (var document in langResults.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} , Language: {document.DetectedLanguages[0].Name}");
            }

            // Getting key-phrases
            Console.WriteLine("\n\n===== KEY-PHRASE EXTRACTION ======");

            var kpResults = await client.KeyPhrasesAsync(
                false,
                new MultiLanguageBatchInput(
                    new List<MultiLanguageInput>
                    {
                        new MultiLanguageInput("ja", "1", "猫は幸せ"),
                        new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                        new MultiLanguageInput("en", "3", "My cat is stiff as a rock."),
                        new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                    }));

            // Printing keyphrases
            foreach (var document in kpResults.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} ");

                Console.WriteLine("\t Key phrases:");

                foreach (string keyphrase in document.KeyPhrases)
                {
                    Console.WriteLine($"\t\t{keyphrase}");
                }
            }

            // Extracting sentiment
            Console.WriteLine("\n\n===== SENTIMENT ANALYSIS ======");

            var sentimentResults = await client.SentimentAsync(
                false,
                new MultiLanguageBatchInput(
                    new List<MultiLanguageInput>
                    {
                        new MultiLanguageInput("en", "1", "I had the best day of my life."),
                        new MultiLanguageInput("en", "2", "This was a waste of my time. The speaker put me to sleep."),
                        new MultiLanguageInput("es", "3", "No tengo dinero ni nada que dar..."),
                        new MultiLanguageInput("it", "4", "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
                    }));


            // Printing sentiment results
            foreach (var document in sentimentResults.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} , Sentiment Score: {document.Score:0.00}");
            }


            // Identify entities
            Console.WriteLine("\n\n===== ENTITIES ======");

            var entitiesResult = await client.EntitiesAsync(
                false,
                new MultiLanguageBatchInput(
                    new List<MultiLanguageInput>()
                    {
                        new MultiLanguageInput("en", "1", "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."),
                        new MultiLanguageInput("es", "2", "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle.")
                    }));

            // Printing entities results
            foreach (var document in entitiesResult.Documents)
            {
                Console.WriteLine($"Document ID: {document.Id} ");

                Console.WriteLine("\t Entities:");

                foreach (var entity in document.Entities)
                {
                    Console.WriteLine($"\t\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
                    foreach (var match in entity.Matches)
                    {
                        Console.WriteLine($"\t\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
                    }
                }
            }

            Console.ReadLine();
        }
    }
}
```

## <a name="application-output"></a>Saída do aplicativo

O aplicativo exibe seguintes informações:

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified


===== KEY-PHRASE EXTRACTION ======
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                rock
Document ID: 4
         Key phrases:
                fútbol


===== SENTIMENT ANALYSIS ======
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00


===== ENTITIES ======
Document ID: 1
         Entities:
                Name: Microsoft,        Type: Organization,     Sub-Type: N/A
                        Offset: 0,      Length: 9,      Score: 1.000
                Name: Bill Gates,       Type: Person,   Sub-Type: N/A
                        Offset: 25,     Length: 10,     Score: 1.000
                Name: Paul Allen,       Type: Person,   Sub-Type: N/A
                        Offset: 40,     Length: 10,     Score: 0.999
                Name: April 4,  Type: Other,    Sub-Type: N/A
                        Offset: 54,     Length: 7,      Score: 0.800
                Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
                        Offset: 54,     Length: 13,     Score: 0.800
                Name: BASIC,    Type: Other,    Sub-Type: N/A
                        Offset: 89,     Length: 5,      Score: 0.800
                Name: Altair 8800,      Type: Other,    Sub-Type: N/A
                        Offset: 116,    Length: 11,     Score: 0.800
Document ID: 2
         Entities:
                Name: Microsoft,        Type: Organization,     Sub-Type: N/A
                        Offset: 21,     Length: 9,      Score: 1.000
                Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
                        Offset: 60,     Length: 7,      Score: 0.991
                Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
                        Offset: 71,     Length: 13,     Score: 0.800
                Name: Seattle,  Type: Location, Sub-Type: N/A
                        Offset: 88,     Length: 7,      Score: 1.000
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Análise de Texto com o Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Consulte também

 [Visão geral da Análise de Texto](../overview.md) [Perguntas frequentes](../text-analytics-resource-faq.md)

