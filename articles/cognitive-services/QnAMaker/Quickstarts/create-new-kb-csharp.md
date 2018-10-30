---
title: 'Início Rápido: criar base de dados de conhecimento – REST, C# – QnA Maker'
titlesuffix: Azure Cognitive Services
description: Este início rápido baseado em REST orienta você pela criação programática de um exemplo de base de dados de conhecimento do QnA Maker, que será exibida no Painel do Azure de sua conta da API dos Serviços Cognitivos.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: diberry
ms.openlocfilehash: e1456cb0e7b7662cd460e51af3456fc496502798
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49645007"
---
# <a name="quickstart-create-a-knowledge-base-in-qna-maker-using-c"></a>Início Rápido: criar uma base de dados de conhecimento no QnA Maker usando o C#

Este início rápido ensina a criar uma base de dados de conhecimento do QnA Maker de maneira programática. O QnA Maker extrai automaticamente perguntas e respostas de conteúdos semiestruturados, como perguntas frequentes, de [fontes de dados](../Concepts/data-sources-supported.md). O modelo da base de dados de conhecimento é definido no JSON enviado no corpo da solicitação da API. 

Este início rápido chama as APIs de QnA Maker:
* [Criar uma base de dados de conhecimento](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
* [Obter detalhes da operação](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/operations_getoperationdetails)

## <a name="prerequisites"></a>Pré-requisitos

* [**Edição mais recente do Visual Studio Community**](https://www.visualstudio.com/downloads/).
* Você precisa ter um [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Para recuperar sua chave, selecione **Teclas** em **Gerenciamento de Recursos** no seu painel. 

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-csharp-repo-note.md)]

## <a name="create-a-knowledge-base-project"></a>Criar um projeto da base de dados de conhecimento

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-the-required-dependencies"></a>Adicione as dependências necessárias

[!INCLUDE [Add required constants to code file](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-required-dependencies.md)]  

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias

[!INCLUDE [Add required constants to code file](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-required-constants.md)] 

## <a name="add-the-kb-definition"></a>Adicionar a definição da base de dados de conhecimento

Após as constantes, adicione a seguinte definição de base de dados de conhecimento:

```csharp
static string kb = @"
{
  'name': 'QnA Maker FAQ from quickstart',
  'qnaList': [
    {
      'id': 0,
      'answer': 'You can use our REST APIs to manage your knowledge base. See here for details: https://westus.dev.cognitive.microsoft.com/docs/services/58994a073d9e04097c7ba6fe/operations/58994a073d9e041ad42d9baa',
      'source': 'Custom Editorial',
      'questions': [
        'How do I programmatically update my knowledge base?'
      ],
      'metadata': [
        {
          'name': 'category',
          'value': 'api'
        }
      ]
    }
  ],
  'urls': [
    'https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs',
    'https://docs.microsoft.com/bot-framework/resources-bot-framework-faq'
  ],
  'files': []
}
";
```

## <a name="add-supporting-functions-and-structures"></a>Adicionar funções e estruturas de suporte

[!INCLUDE [Add supporting functions and structures](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-support-functions.md)] 

## <a name="add-a-post-request-to-create-kb"></a>Adicionar uma solicitação POST para criar a base de dados de conhecimento

O código a seguir faz uma solicitação HTTPS para a API de QnA Maker para criar uma base de dados de conhecimento e recebe a resposta:

```csharp
async static Task<Response> PostCreateKB(string kb)
{
    // Builds the HTTP request URI.
    string uri = host + service + method;

    // Writes the HTTP request URI to the console, for display purposes.
    Console.WriteLine("Calling " + uri + ".");

    // Asynchronously invokes the Post(string, string) method, using the
    // HTTP request URI and the specified data source.
    using (var client = new HttpClient())
    using (var request = new HttpRequestMessage())
    {
        request.Method = HttpMethod.Post;
        request.RequestUri = new Uri(uri);
        request.Content = new StringContent(kb, Encoding.UTF8, "application/json");
        request.Headers.Add("Ocp-Apim-Subscription-Key", key);

        var response = await client.SendAsync(request);
        var responseBody = await response.Content.ReadAsStringAsync();
        return new Response(response.Headers, responseBody);
    }
}
```

Essa chamada à API retorna uma resposta JSON que inclui a ID da operação. Use a ID da operação para determinar se a base de dados de conhecimento foi criada com êxito. 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:19:01Z",
  "lastActionTimestamp": "2018-09-26T05:19:01Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "8dfb6a82-ae58-4bcb-95b7-d1239ae25681"
}
```

## <a name="add-get-request-to-determine-creation-status"></a>Adicionar solicitação GET para determinar o status da criação

Verifique o status da operação.

```csharp
async static Task<Response> GetStatus(string operationID)
{
    // Builds the HTTP request URI.
    string uri = host + service + operationID;

    // Writes the HTTP request URI to the console, for display purposes.
    Console.WriteLine("Calling " + uri + ".");

    // Asynchronously invokes the Get(string) method, using the
    // HTTP request URI.
    using (var client = new HttpClient())
    using (var request = new HttpRequestMessage())
    {
        request.Method = HttpMethod.Get;
        request.RequestUri = new Uri(uri);
        request.Headers.Add("Ocp-Apim-Subscription-Key", key);

        var response = await client.SendAsync(request);
        var responseBody = await response.Content.ReadAsStringAsync();
        return new Response(response.Headers, responseBody);
    }
}
```

Essa chamada à API retorna uma resposta JSON que inclui o status da operação: 

```JSON
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:22:53Z",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

Repita a chamada até ter um resultado com êxito ou falha: 

```JSON
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-09-26T05:22:53Z",
  "lastActionTimestamp": "2018-09-26T05:23:08Z",
  "resourceLocation": "/knowledgebases/XXX7892b-10cf-47e2-a3ae-e40683adb714",
  "userId": "XXX9549466094e1cb4fd063b646e1ad6",
  "operationId": "177e12ff-5d04-4b73-b594-8575f9787963"
}
```

## <a name="add-createkb-method"></a>Adicionar o método CreateKB

O método a seguir cria a base de dados de conhecimento e repete as verificações do status. Como a criação de KB pode levar algum tempo, você precisará repetir a chamadas para verificar o status até que o status indique êxito ou falha.

```csharp
async static void CreateKB()
{
    try
    {
        // Starts the QnA Maker operation to create the knowledge base.
        var response = await PostCreateKB(kb);

        // Retrieves the operation ID, so the operation's status can be
        // checked periodically.
        var operation = response.headers.GetValues("Location").First();

        // Displays the JSON in the HTTP response returned by the 
        // PostCreateKB(string) method.
        Console.WriteLine(PrettyPrint(response.response));

        // Iteratively gets the state of the operation creating the
        // knowledge base. Once the operation state is set to something other
        // than "Running" or "NotStarted", the loop ends.
        var done = false;
        while (true != done)
        {
            // Gets the status of the operation.
            response = await GetStatus(operation);

            // Displays the JSON in the HTTP response returned by the
            // GetStatus(string) method.
            Console.WriteLine(PrettyPrint(response.response));

            // Deserialize the JSON into key-value pairs, to retrieve the
            // state of the operation.
            var fields = JsonConvert.DeserializeObject<Dictionary<string, string>>(response.response);

            // Gets and checks the state of the operation.
            String state = fields["operationState"];
            if (state.CompareTo("Running") == 0 || state.CompareTo("NotStarted") == 0)
            {
                // QnA Maker is still creating the knowledge base. The thread is 
                // paused for a number of seconds equal to the Retry-After header value,
                // and then the loop continues.
                var wait = response.headers.GetValues("Retry-After").First();
                Console.WriteLine("Waiting " + wait + " seconds...");
                Thread.Sleep(Int32.Parse(wait) * 1000);
            }
            else
            {
                // QnA Maker has completed creating the knowledge base. 
                done = true;
            }
        }
    }
    catch
    {
        // An error occurred while creating the knowledge base. Ensure that
        // you included your QnA Maker subscription key where directed in the sample.
        Console.WriteLine("An error occurred while creating the knowledge base.");
    }
    finally
    {
        Console.WriteLine("Press any key to continue.");
    }

}
``` 

## <a name="add-the-createkb-method-to-main"></a>Adicionar o método CreateKB ao Main

Altere o método Main para chamar o método CreateKB:

```csharp
static void Main(string[] args)
{
    // Call the CreateKB() method to create a knowledge base, periodically 
    // checking the status of the QnA Maker operation until the 
    // knowledge base is created.
    CreateKB();

    // The console waits for a key to be pressed before closing.
    Console.ReadLine();
}
```

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Compile e execute o programa. Ele enviará automaticamente a solicitação para a API de QnA Maker para criar a base de dados de conhecimento e, em seguida, sondará os resultados a cada 30 segundos. Todas as respostas são impressas na janela do console.

Depois que sua base de dados de conhecimento tiver sido criada, você poderá exibi-la em seu Portal do QnA Maker, página [Minhas bases de dados de conhecimento](https://www.qnamaker.ai/Home/MyServices). 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST do QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
