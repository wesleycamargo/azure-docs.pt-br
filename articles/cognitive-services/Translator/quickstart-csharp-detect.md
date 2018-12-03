---
title: 'Início Rápido: Identificar o idioma com base no texto, C# - API de Tradução de Texto'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você aprenderá a detectar o idioma do texto fornecido usando o .NET Core e a API REST de Tradução de Texto.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 11/26/2018
ms.author: erhopf
ms.openlocfilehash: 8f98c4cbca87fd77e3c09c1028bfcb3181907412
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52335689"
---
# <a name="quickstart-detect-text-language-with-the-translator-text-rest-api-c"></a>Início Rápido: Detectar o idioma do texto com a API REST de Tradução de Texto (C#)

Neste início rápido, você aprenderá a detectar o idioma do texto fornecido usando o .NET Core e a API REST de Tradução de Texto.

Este início rápido requer uma [Conta dos Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso de Tradução de Texto. Se não tiver uma conta, você poderá usar a [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/) para obter uma chave de assinatura.

## <a name="prerequisites"></a>Pré-requisitos

* [SDK .NET](https://www.microsoft.com/net/learn/dotnet/hello-world-tutorial)
* [Pacote NuGet do Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) ou seu editor de texto favorito
* Uma chave de assinatura do Azure para o Serviço de Fala

## <a name="create-a-net-core-project"></a>Criar projeto do .NET Core

Abra um novo prompt de comando (ou a sessão de terminal) e execute estes comandos:

```console
dotnet new console -o detect-sample
cd detect-sample
```

O primeiro comando faz duas coisas. Ele cria um novo aplicativo de console .NET e cria um diretório chamado `detect-sample`. O segundo comando faz a mudança para o diretório do seu projeto.

Em seguida, você precisará instalar o Json.Net. No diretório do projeto, execute:

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="add-required-namespaces-to-your-project"></a>Adicionar namespaces obrigatórios ao projeto

O comando `dotnet new console` que você executou anteriormente criou um projeto, incluindo `Program.cs`. Esse arquivo é onde você colocará o código do aplicativo. Abra `Program.cs` e substitua as instruções using existentes. Essas instruções garantem que você tenha acesso a todos os tipos necessários para compilar e executar o aplicativo de exemplo.

```csharp
using System;
using System.Net.Http;
using System.Text;
using Newtonsoft.Json;
```

## <a name="create-a-function-to-detect-the-source-texts-language"></a>Criar uma função para detectar o idioma do texto de origem

Na classe `Program`, crie uma função chamada `Detect`. Essa classe encapsula o código usado para chamar o recurso Detect e imprime o resultado no console.

```csharp
static void Detect()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="set-the-subscription-key-host-name-and-path"></a>Definir a chave de assinatura, o nome do host e o caminho

Adicione essas linhas à função `Detect`.

```csharp
string host = "https://api.cognitive.microsofttranslator.com";
string route = "/detect?api-version=3.0";
string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
```

Em seguida, precisamos criar e serializar o objeto JSON que inclui o texto do qual o idioma será detectado.

```csharp
System.Object[] body = new System.Object[] { new { Text = @"Salve mondo!" } };
var requestBody = JsonConvert.SerializeObject(body);
```

## <a name="instantiate-the-client-and-make-a-request"></a>Instanciar o cliente e fazer uma solicitação

Essas linhas instanciam `HttpClient` e `HttpRequestMessage`:

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>Construir a solicitação e imprimir a resposta

Dentro de `HttpRequestMessage`, você vai:

* Declarar o método HTTP
* Construir o URI de solicitação
* Inserir o corpo da solicitação (objeto JSON serializado)
* Adicionar os cabeçalhos obrigatórios
* Fazer uma solicitação assíncrona
* Imprima a resposta

Adicione este código a `HttpRequestMessage`:

```csharp
// Set the method to POST
request.Method = HttpMethod.Post;

// Construct the full URI
request.RequestUri = new Uri(host + route);

// Add the serialized JSON object to your request
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");

// Add the authorization header
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send request, get response
var response = client.SendAsync(request).Result;
var jsonResponse = response.Content.ReadAsStringAsync().Result;

// Print the response
Console.WriteLine(jsonResponse);
Console.WriteLine("Press any key to continue.");
```

## <a name="put-it-all-together"></a>Colocar tudo isso junto

A última etapa é chamar `Detect()` na função `Main`. Localize `static void Main(string[] args)` e adicione estas linhas:

```csharp
Detect();
Console.ReadLine();
```

## <a name="run-the-sample-app"></a>Executar o aplicativo de exemplo

E, pronto, você já pode executar seu aplicativo de exemplo. Na linha de comando (ou sessão de terminal), navegue até o diretório do seu projeto e execute:

```console
dotnet run
```

## <a name="sample-response"></a>Resposta de exemplo

```json
[
  {
    "language": "it",
    "score": 1.0,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "en",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="clean-up-resources"></a>Limpar recursos

Remova todas as informações confidenciais do código-fonte do seu aplicativo de exemplo, como as chaves de assinatura.

## <a name="next-steps"></a>Próximas etapas

Explore o exemplo de código para este início rápido e outros, incluindo tradução e transliteração, bem como outros projetos de Tradução de Texto de exemplo no GitHub.

> [!div class="nextstepaction"]
> [Explore exemplos de C# no GitHub](https://aka.ms/TranslatorGitHub?type=&language=c%23)

## <a name="see-also"></a>Consulte também

* [Traduzir o texto](quickstart-csharp-translate.md)
* [Transliteração de texto](quickstart-csharp-transliterate.md)
* [Obter traduções alternativas](quickstart-csharp-dictionary.md)
* [Obter uma lista de idiomas com suporte](quickstart-csharp-languages.md)
* [Determinar o comprimento da frase em uma entrada](quickstart-csharp-sentences.md)
