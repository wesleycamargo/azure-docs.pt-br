---
title: Início rápido do C# para os Serviços Cognitivos, API de Análise de Texto | Microsoft Docs
description: Obtenha informações e exemplos de código para ajudá-lo a começar a usar rapidamente a API de Análise de Texto nos Serviços Cognitivos da Microsoft no Azure.
services: cognitive-services
documentationcenter: ''
author: luiscabrer
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 08/30/2018
ms.author: ashmaka
ms.openlocfilehash: 42a682898303b742a17b0a6d4d98c2b9fedf9003
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/06/2018
ms.locfileid: "43841687"
---
# <a name="quickstart-for-the-text-analytics-api-with-c"></a>Início Rápido da API de Análise de Texto com C# 
<a name="HOLTop"></a>

Este artigo mostra como detectar a linguagem, analisar o sentimento e extrair frases-chave usando as [APIs de Análise de Texto](//go.microsoft.com/fwlink/?LinkID=759711) com C#. O código foi escrito para funcionar em um aplicativo .NET Core, com referências mínimas a bibliotecas externas; portanto, você também pode executá-lo no Linux ou no MacOS.

Consulte as [definições da API](//go.microsoft.com/fwlink/?LinkID=759346) para obter a documentação técnica das APIs.

## <a name="prerequisites"></a>Pré-requisitos

É necessário ter uma [conta da API de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com a API de Análise de Texto. É possível usar a *camada gratuita para 5.000 transações/mês* para concluir este início rápido.

Também é necessário ter o [ponto de extremidade e a chave de acesso](../How-tos/text-analytics-how-to-access-key.md) que foram gerados para você durante a inscrição. 


## <a name="install-the-nuget-sdk-package"></a>Instalar o pacote do SDK do NuGet
1. Crie uma nova solução de console no Visual Studio.
1. Clique com o botão direito do mouse na solução e selecione **Gerenciar Pacotes NuGet para a solução**.
1. Marque a caixa de seleção **Incluir Pré-lançamento**.
1. Selecione a guia **Procurar** e pesquise **Microsoft.Azure.CognitiveServices.Language**.
1. Selecione o pacote **Microsoft.Azure.CognitiveServices.Language.TextAnalytics** NuGet e instale-o.

> [!Tip]
> Embora você possa chamar os [pontos de extremidade HTTP](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) diretamente do C#, o SDK Microsoft.Azure.CognitiveServices.Language torna mais fácil chamar o serviço sem precisar se preocupar com a serialização e desserialização do JSON.
>
> Eis alguns links úteis:
> - [Página NuGet do SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
> - [Código do SD ](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)


## <a name="call-the-text-analytics-api-by-using-the-sdk"></a>Chame a API de Análise de Texto usando o SDK
1. Substitua Program.cs pelo código a seguir. Este programa demonstra as funcionalidades da API de Análise de Texto em três seções (extração de linguagem, extração de frases-chave e análise de sentimento).
1. Substitua o valor de cabeçalho `Ocp-Apim-Subscription-Key` por uma chave de acesso válida para sua assinatura.
1. Substitua o local em `Endpoint` para o ponto de extremidade em que se inscreveu. É possível localizar o ponto de extremidade no recurso do portal do Azure. O ponto de extremidade normalmente começa com "https://[region].api.cognitive.microsoft.com." Inclua somente o protocolo e o nome do host.
1. Execute o programa.

```csharp
using System;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
using System.Collections.Generic;
using Microsoft.Rest;
using System.Linq;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        /// <summary>
        /// Container for subscription credentials. Make sure to enter your valid key.
        /// </summary>
        class ApiKeyServiceClientCredentials : ServiceClientCredentials
        {
            public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", "ENTER KEY HERE");
                return base.ProcessHttpRequestAsync(request, cancellationToken);
            }
        }

        static void Main(string[] args)
        {

            // Create a client.
            ITextAnalyticsClient client = new TextAnalyticsClient(new ApiKeyServiceClientCredentials())
            {
                Endpoint = "https://westus.api.cognitive.microsoft.com"
            };

            Console.OutputEncoding = System.Text.Encoding.UTF8;
```

## <a name="detect-language"></a>Detectar o idioma

A API de Detecção de Idioma detecta o idioma de um documento de texto, usando o [método Detectar Idioma](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7).

```csharp
            // Extracting language.
            Console.WriteLine("===== LANGUAGE EXTRACTION ======");

            var result =  client.DetectLanguageAsync(new BatchInput(
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
```

## <a name="extract-key-phrases"></a>Extraia frases-chave

A API de Extração de Frases-chave extrai as frases-chave de um documento de texto, usando o [método Frases-chave](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6).

```csharp
            // Getting key phrases.
            Console.WriteLine("\n\n===== KEY-PHRASE EXTRACTION ======");

            KeyPhraseBatchResult result2 = client.KeyPhrasesAsync(new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("ja", "1", "猫は幸せ"),
                          new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                          new MultiLanguageInput("en", "3", "My cat is stiff as a rock."),
                          new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                        })).Result;

            // Printing key phrases.
            foreach (var document in result2.Documents)
            {
                Console.WriteLine("Document ID: {0} ", document.Id);

                Console.WriteLine("\t Key phrases:");

                foreach (string keyphrase in document.KeyPhrases)
                {
                    Console.WriteLine("\t\t" + keyphrase);
                }
            }
```

## <a name="analyze-sentiment"></a>Analisar sentimento

A API de Análise de Sentimento detecta o sentimento de um conjunto de registros de texto, usando o [método Sentiment](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9).

```csharp
            // Analyzing sentiment.
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


            // Printing sentiment results.
            foreach (var document in result3.Documents)
            {
                Console.WriteLine("Document ID: {0} , Sentiment Score: {1:0.00}", document.Id, document.Score);
            }
```

## <a name="identify-linked-entities"></a>Identificar as entidades vinculadas

A API de Vinculação de Entidade identifica as entidades conhecidas em um documento de texto, usando o [método Vinculação de Entidade](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634).

```csharp
            // Linking entities
            Console.WriteLine("\n\n===== ENTITY LINKING ======");

            EntitiesBatchResult result4 = client.EntitiesAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                            new MultiLanguageInput("en", "0", "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."),
                            new MultiLanguageInput("en", "1", "The Seattle Seahawks won the Super Bowl in 2014."),
                        })).Result;

            // Printing entity results.
            foreach (var document in result4.Documents)
            {
                Console.WriteLine("Document ID: {0} , Entities: {1}", document.Id, String.Join(", ", document.Entities.Select(entity => entity.Name)));
            }
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
