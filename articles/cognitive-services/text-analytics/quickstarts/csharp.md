---
title: 'Início Rápido: usando C# para chamar a API de Análise de Texto'
titleSuffix: Azure Cognitive Services
description: Obtenha informações e exemplos de código para ajudá-lo a começar a usar rapidamente a API de Análise de Texto.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: quickstart
ms.date: 10/01/2018
ms.author: ashmaka
ms.openlocfilehash: a5223b026705cef5abbcd0be6f64cf0c98fd0930
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2018
ms.locfileid: "49309018"
---
# <a name="quickstart-using-c-to-call-the-text-analytics-cognitive-service"></a>Início Rápido: usando C# para chamar o Serviço Cognitivo de Análise de Texto
<a name="HOLTop"></a>

Este artigo mostra como detectar a linguagem, analisar o sentimento e extrair frases-chave usando as [APIs de Análise de Texto](//go.microsoft.com/fwlink/?LinkID=759711) com C#. O código foi escrito para funcionar em um aplicativo .Net Core, com referências mínimas a bibliotecas externas; portanto, você também pode executá-lo no Linux ou no macOS.

Consulte as [definições da API](//go.microsoft.com/fwlink/?LinkID=759346) para obter a documentação técnica das APIs.

## <a name="prerequisites"></a>Pré-requisitos

É necessário ter uma [conta da API de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com a **API de Análise de Texto**. É possível usar a **camada gratuita para 5.000 transações/mês** para concluir este início rápido.

Também é necessário ter o [ponto de extremidade e a chave de acesso](../How-tos/text-analytics-how-to-access-key.md) que foi gerada para você durante a inscrição. 


## <a name="install-the-nuget-sdk-package"></a>Instalar o pacote do SDK do NuGet
1. Crie uma nova solução de console no Visual Studio.
1. Clique com o botão direito do mouse na solução e em **Gerenciar pacotes NuGet para a solução**
1. Marque a caixa de seleção **Incluir pré-lançamento**.
1. Selecione a guia **Procurar** e pesquise por **Microsoft.Azure.CognitiveServices.Language.TextAnalytics**
1. Selecione o pacote NuGet e instale-o.

> [!Tip]
>  Embora você possa chamar os [pontos de extremidade HTTP](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) diretamente do C#, o SDK Microsoft.Azure.CognitiveServices.Language torna mais fácil chamar o serviço sem precisar se preocupar com a serialização e desserialização do JSON.
>
> Alguns links úteis:
> - [Página NuGet do SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
> - [Código do SDK ](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)


## <a name="call-the-text-analytics-api-using-the-sdk"></a>Chame a API de Análise de Texto usando o SDK
1. Substitua Program.cs pelo código fornecido abaixo. Este programa demonstra as funcionalidades da API de Análise de Texto em três seções (extração de linguagem, extração de frases-chave e análise de sentimento).
1. Substitua o valor de cabeçalho `Ocp-Apim-Subscription-Key` por uma chave de acesso válida para sua assinatura.
1. Substitua o local em `Endpoint` para o ponto de extremidade em que inscreveu-se. É possível localizar o ponto de extremidade no recurso do portal do Azure. O ponto de extremidade normalmente começa com "https://[region].api.cognitive.microsoft.com" e aqui, somente inclua protocolo e nome do host.
1. Execute o programa.

```csharp
using System;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
using System.Collections.Generic;
using Microsoft.Rest;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        /// <summary>
        /// Container for subscription credentials. Make sure to enter your valid key.
        string subscriptionKey = ""; //Insert your Text Anaytics subscription key
        /// </summary>
        class ApiKeyServiceClientCredentials : ServiceClientCredentials
        {
            public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                return base.ProcessHttpRequestAsync(request, cancellationToken);
            }
        }

        static void Main(string[] args)
        {

            // Create a client.
            ITextAnalyticsClient client = new TextAnalyticsClient(new ApiKeyServiceClientCredentials())
            {
                Endpoint = "https://westus.api.cognitive.microsoft.com"
            }; //Replace 'westus' with the correct region for your Text Analytics subscription

            Console.OutputEncoding = System.Text.Encoding.UTF8;

            // Extracting language
            Console.WriteLine("===== LANGUAGE EXTRACTION ======");

            var result = client.DetectLanguageAsync(new BatchInput(
                    new List<Input>()
                        {
                          new Input("1", "This is a document written in English."),
                          new Input("2", "Este es un document escrito en Español."),
                          new Input("3", "这是一个用中文写的文件")
                    })).Result;

            // Printing language results.
            foreach (var document in result.Documents)
            {
                Console.WriteLine("Document ID: {0} , Language: {1}", document.Id, document.DetectedLanguages[0].Name);
            }

            // Getting key-phrases
            Console.WriteLine("\n\n===== KEY-PHRASE EXTRACTION ======");

            KeyPhraseBatchResult result2 = client.KeyPhrasesAsync(new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("ja", "1", "猫は幸せ"),
                          new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                          new MultiLanguageInput("en", "3", "My cat is stiff as a rock."),
                          new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                        })).Result;

            // Printing keyphrases
            foreach (var document in result2.Documents)
            {
                Console.WriteLine("Document ID: {0} ", document.Id);

                Console.WriteLine("\t Key phrases:");

                foreach (string keyphrase in document.KeyPhrases)
                {
                    Console.WriteLine("\t\t" + keyphrase);
                }
            }

            // Extracting sentiment
            Console.WriteLine("\n\n===== SENTIMENT ANALYSIS ======");

            SentimentBatchResult result3 = client.SentimentAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("en", "0", "I had the best day of my life."),
                          new MultiLanguageInput("en", "1", "This was a waste of my time. The speaker put me to sleep."),
                          new MultiLanguageInput("es", "2", "No tengo dinero ni nada que dar..."),
                          new MultiLanguageInput("it", "3", "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
                        })).Result;


            // Printing sentiment results
            foreach (var document in result3.Documents)
            {
                Console.WriteLine("Document ID: {0} , Sentiment Score: {1:0.00}", document.Id, document.Score);
            }


            // Identify entities
            Console.WriteLine("\n\n===== ENTITIES ======");

            EntitiesBatchResult result4 = client.EntitiesAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("en", "0", "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%.")
                        })).Result;

            // Printing entities results
            foreach (var document in result4.Documents)
            {
                Console.WriteLine("Document ID: {0} ", document.Id);

                Console.WriteLine("\t Entities:");

                foreach (EntityRecord entity in document.Entities)
                {
                    Console.WriteLine("\t\t" + entity.Name);
                }
            }

            Console.ReadLine();
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Análise de Texto com o Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Consulte também 

 [Visão geral da Análise de Texto](../overview.md)  
 [Perguntas frequentes (FAQ)](../text-analytics-resource-faq.md)

