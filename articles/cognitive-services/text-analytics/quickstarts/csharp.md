---
title: 'Início Rápido: usando C# para chamar a API de Análise de Texto'
titleSuffix: Azure Cognitive Services
description: Obtenha informações e exemplos de código para ajudá-lo a começar a usar rapidamente a API de Análise de Texto.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 04/29/2019
ms.author: assafi
ms.openlocfilehash: e7b07472623cc459c31906aeaa6ccfb4388b4b50
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65146096"
---
# <a name="quickstart-using-c-to-call-the-text-analytics-cognitive-service"></a>Início Rápido: usando C# para chamar o Serviço Cognitivo de Análise de Texto
<a name="HOLTop"></a>

Use este guia de início rápido para começar a analisar com o SDK de Análise de Texto para C#. Embora a API REST da [Análise de Texto](//go.microsoft.com/fwlink/?LinkID=759711) seja compatível com a maioria das linguagens de programação, o SDK fornece uma maneira fácil de integrar o serviço aos seus aplicativos. O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics).

Consulte as [definições da API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) para obter a documentação técnica das APIs.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Também é necessário ter o [ponto de extremidade e a chave de acesso](../How-tos/text-analytics-how-to-access-key.md) que foi gerada para você durante a inscrição.

> [!Tip]
>  Embora você possa chamar os [pontos de extremidade HTTP](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) diretamente do C#, o SDK Microsoft.Azure.CognitiveServices.Language torna mais fácil chamar o serviço sem precisar se preocupar com a serialização e desserialização do JSON.
>
> Alguns links úteis:
> - [Página NuGet do SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
> - [Código do SD ](https://github.com/Azure/azure-sdk-for-net/tree/master/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)

## <a name="create-the-visual-studio-solution-and-install-the-sdk"></a>Criar a solução do Visual Studio e instalar o SDK

1. Criar um novo projeto de Aplicativo de console (.NET Core) do [Visual Studio](https://visualstudio.microsoft.com/vs/).
1. Clique com o botão direito do mouse na solução e em **Gerenciar pacotes NuGet para a solução**
1. Selecione a guia **Procurar** e pesquise por **Microsoft.Azure.CognitiveServices.Language.TextAnalytics**

## <a name="authenticate-your-credentials"></a>Autenticar suas credenciais

1. Adicione a seguinte instrução `using` ao arquivo de classe principal (`Program.cs`, por padrão).

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Net.Http;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Rest;
    ```

2. Crie uma nova classe `ApiKeyServiceClientCredentials` para armazenar as credenciais e adicione-as a cada solicitação.

    ```csharp
    /// <summary>
    /// Allows authentication to the API using a basic apiKey mechanism
    /// </summary>
    class ApiKeyServiceClientCredentials : ServiceClientCredentials
    {
        private readonly string subscriptionKey;

        /// <summary>
        /// Creates a new instance of the ApiKeyServiceClientCredentails class
        /// </summary>
        /// <param name="subscriptionKey">The subscription key to authenticate and authorize as</param>
        public ApiKeyServiceClientCredentials(string subscriptionKey)
        {
            this.subscriptionKey = subscriptionKey;
        }

        /// <summary>
        /// Add the Basic Authentication Header to each outgoing request
        /// </summary>
        /// <param name="request">The outgoing request</param>
        /// <param name="cancellationToken">A token to cancel the operation</param>
        public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            if (request == null)
            {
                throw new ArgumentNullException("request");
            }

            request.Headers.Add("Ocp-Apim-Subscription-Key", this.subscriptionKey);
            return base.ProcessHttpRequestAsync(request, cancellationToken);
        }
    }
    ```

3. Atualize a classe `Program` e adicione um membro constante a sua chave de assinatura de Análise de Texto e outro para o ponto de extremidade de serviço. Lembre-se de usar a região do Azure correta para sua assinatura de Análise de Texto.

    ```csharp
    private const string SubscriptionKey = "enter-your-key-here";

    private const string Endpoint = "enter-your-service-endpoint-here"; // For example: "https://westus.api.cognitive.microsoft.com";
    ```
> [!Tip]
> Para a implantação segura de segredos em sistemas de produção é recomendável usar o [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net)
>

## <a name="create-a-text-analytics-client"></a>Criar um cliente de Análise de Texto

Na função `Main` de seu projeto, chame o método de exemplo que você deseja invocar e transmita os parâmetros `Endpoint` e `SubscriptionKey` definidos.

```csharp
    public static void Main(string[] args)
    {
        var credentials = new ApiKeyServiceClientCredentials(SubscriptionKey);
        var client = new TextAnalyticsClient(credentials)
        {
            //Replace 'westus' with the correct region for your Text Analytics subscription
            Endpoint = "https://westus.api.cognitive.microsoft.com"
        };

        // Change console encoding to display non-ASCII characters
        Console.OutputEncoding = System.Text.Encoding.UTF8;
        SentimentAnalysisExample(client).Wait();
        // DetectLanguageExample(client).Wait();
        // RecognizeEntitiesExample(client).Wait();
        // KeyPhraseExtractionExample(client).Wait();
        Console.ReadLine();
    }
```

As seções a seguir descrevem como chamar cada um dos recursos da API.

## <a name="sentiment-analysis"></a>Análise de sentimento

1. Crie uma nova função chamada `SentimentAnalysisExample()` que use o cliente criado anteriormente.
2. Crie um novo objeto `TextAnalyticsClient` com `ApiKeyServiceClientCredentials` como um parâmetro e gere uma lista de objetos `MultiLanguageInput`, contendo os documentos que você deseja analisar.

    ```csharp
    public static async Task SentimentAnalysisExample(string endpoint, string key)
    {
        var credentials = new ApiKeyServiceClientCredentials(key);
        var client = new TextAnalyticsClient(credentials)
        {
            Endpoint = endpoint
        };

        // The documents to be analyzed. Add the language of the document. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "I had the best day of my life."),
                new MultiLanguageInput("en", "2", "This was a waste of my time. The speaker put me to sleep."),
                new MultiLanguageInput("es", "3", "No tengo dinero ni nada que dar..."),
                new MultiLanguageInput("it", "4", "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
            });
        //...
    }
    ```

3. Na mesma função, chame `client.SentimentAsync()` e obtenha o resultado. Em seguida, itere os resultados e imprima a ID de cada documento e a pontuação de sentimento. Uma pontuação mais perto de 0 indica um sentimento negativo, enquanto uma pontuação mais perto de 1 indica um sentimento positivo.

    ```csharp
    var result = await client.SentimentAsync(false, inputDocuments);

    // Printing sentiment results
    foreach (var document in result.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Sentiment Score: {document.Score:0.00}");
    }
    ```

### <a name="output"></a>Saída

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>Detecção de idioma

1. Crie uma nova função chamada `DetectLanguageExample()` que use o cliente criado anteriormente.
2. Crie um novo objeto `TextAnalyticsClient` com `ApiKeyServiceClientCredentials` como um parâmetro e gere uma lista de objetos `LanguageInput`, contendo os seus documentos.

    ```csharp
    public static async Task DetectLanguageExample(string endpoint, string key)
    {
        var credentials = new ApiKeyServiceClientCredentials(key);
        var client = new TextAnalyticsClient(credentials)
        {
            Endpoint = endpoint
        };

        // The documents to be submitted for language detection. The ID can be any value.
        var inputDocuments = new LanguageBatchInput(
                new List<LanguageInput>
                    {
                        new LanguageInput(id: "1", text: "This is a document written in English."),
                        new LanguageInput(id: "2", text: "Este es un document escrito en Español."),
                        new LanguageInput(id: "3", text: "这是一个用中文写的文件")
                    });
        //...
    }
    ```

3. Na mesma função, chame `client.DetectLanguageAsync()` e obtenha o resultado. Em seguida, itere os resultados e imprima a ID de cada documento e o primeiro idioma retornado.

    ```csharp
    var langResults = await client.DetectLanguageAsync(false, inputDocuments);

    // Printing detected languages
    foreach (var document in langResults.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Language: {document.DetectedLanguages[0].Name}");
    }
    ```

### <a name="output"></a>Saída

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Reconhecimento de entidade

1. Crie uma nova função chamada `RecognizeEntitiesExample()` que use o cliente criado anteriormente.
2. Crie um novo objeto `TextAnalyticsClient` com `ApiKeyServiceClientCredentials` como um parâmetro e gere uma lista de objetos `MultiLanguageBatchInput`, contendo os seus documentos.

    ```csharp
    public static async Task RecognizeEntitiesExample(string endpoint, string key)
    {
        var credentials = new ApiKeyServiceClientCredentials(key);
        var client = new TextAnalyticsClient(credentials)
        {
            Endpoint = endpoint
        };

        // The documents to be submitted for entity recognition. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."),
                new MultiLanguageInput("es", "2", "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle.")
            });
        //...
    }
    ```

3. Na mesma função, chame `client.EntitiesAsync()` e obtenha o resultado. Em seguida, itere os resultados e imprima a ID de cada documento. Para cada entidade detectada, imprimi o nome da Wikipédia dela, o tipo e os subtipos (se existir), bem como os locais no texto original.

    ```csharp
    var entitiesResult = await client.EntitiesAsync(false, inputDocuments);

    // Printing recognized entities
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
    ```

### <a name="output"></a>Saída

```console
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

## <a name="key-phrase-extraction"></a>Extração de frases-chave

1. Crie uma nova função chamada `KeyPhraseExtractionExample()` que use o cliente criado anteriormente.
2. Crie um novo objeto `TextAnalyticsClient` com `ApiKeyServiceClientCredentials` como um parâmetro e gere uma lista de objetos `MultiLanguageBatchInput`, contendo os seus documentos.

    ```csharp
    public static async Task KeyPhraseExtractionExample(string endpoint, string key)
    {
        var credentials = new ApiKeyServiceClientCredentials(key);
        var client = new TextAnalyticsClient(credentials)
        {
            Endpoint = endpoint
        };

        var inputDocuments = new MultiLanguageBatchInput(
                    new List<MultiLanguageInput>
                    {
                        new MultiLanguageInput("ja", "1", "猫は幸せ"),
                        new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                        new MultiLanguageInput("en", "3", "My cat might need to see a veterinarian."),
                        new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                    });
        //...
    }
    ```

3. Na mesma função, chame `client.KeyPhrasesAsync()` e obtenha o resultado. Em seguida, itere os resultados e imprima a ID de cada documento e quaisquer frases-chave detectadas.

    ```csharp
    var kpResults = await client.KeyPhrasesAsync(false, inputDocuments);

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
    ```

### <a name="output"></a>Saída

```console
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
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Análise de Texto com o Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Visão geral da Análise de Texto](../overview.md)
* [Perguntas frequentes (FAQ)](../text-analytics-resource-faq.md)

