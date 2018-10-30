---
title: 'Início Rápido: Publicar uma base de dados de conhecimento - REST, C# – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Este início rápido com base em REST orienta você na publicação de sua base de dados de conhecimento, que envia por push a última versão da base de dados de conhecimento testada para um índice do Azure Search dedicado que representa a base de dados de conhecimento publicada. Isso também cria um ponto de extremidade que pode ser chamado no aplicativo ou chat bot.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 10/19/2018
ms.author: diberry
ms.openlocfilehash: ce027abb75423d0174a7175c3bbafe5c0fb3e157
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646255"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-c"></a>Início Rápido: publicar uma base de dados de conhecimento no QnA Maker usando C#

Este início rápido orienta você durante a publicação programática de sua KB (base de dados de conhecimento). A publicação envia por push a versão mais recente da base de dados de conhecimento para um índice dedicado do Azure Search e cria um ponto de extremidade que pode ser chamado em seu aplicativo ou chat bot.

Este início rápido chama as APIs de QnA Maker:
* [Publicar](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75fe) – esta API não requer nenhuma informação no corpo da solicitação.

## <a name="prerequisites"></a>Pré-requisitos

* [**Edição mais recente do Visual Studio Community**](https://www.visualstudio.com/downloads/).
* Você precisa ter um [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Para recuperar sua chave, selecione **Teclas** em **Gerenciamento de Recursos** no seu painel. 
* A ID da KB (base de dados de conhecimento) do QnA Maker encontrada na URL no parâmetro de cadeia de caracteres de consulta kbid, conforme mostrada abaixo.

    ![ID da base de dados de conhecimento do QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

Se você não tiver uma base de dados de conhecimento, pode criar uma de amostra para usar para este início rápido: [Criar uma nova base de dados de conhecimento](create-new-kb-csharp.md).

[!INCLUDE [Code is available in Azure-Samples Github repo](../../../../includes/cognitive-services-qnamaker-csharp-repo-note.md)]

## <a name="create-knowledge-base-project"></a>Criar projeto da base de dados de conhecimento

[!INCLUDE [Create Visual Studio Project](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-create-project.md)] 

## <a name="add-required-dependencies"></a>Adicionar as dependências necessárias

[!INCLUDE [Add required dependencies to code file](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-required-dependencies.md)] 

## <a name="add-required-constants"></a>Adicionar constantes necessárias

[!INCLUDE [Add required constants to code file](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-required-constants.md)]  

## <a name="add-knowledge-base-id"></a>Adicionar ID da base de dados de conhecimento

[!INCLUDE [Add knowledge base ID as constant](../../../../includes/cognitive-services-qnamaker-quickstart-csharp-kb-id.md)] 

## <a name="add-supporting-functions-and-structures"></a>Adicionar funções e estruturas de suporte

Adicione o seguinte bloco de código dentro da classe Program:

```csharp
static string PrettyPrint(string s)
{
    return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
}
```

## <a name="add-post-request-to-publish-kb"></a>Adicionar solicitação POST para publicar base de dados de conhecimento

O código a seguir faz uma solicitação HTTPS para a API de QnA Maker para publicar uma base de dados de conhecimento e recebe a resposta:

```csharp
async static void PublishKB()
{
    string responseText;

    var uri = host + service + method + kb;
    Console.WriteLine("Calling " + uri + ".");
    using (var client = new HttpClient())
    using (var request = new HttpRequestMessage())
    {
        request.Method = HttpMethod.Post;
        request.RequestUri = new Uri(uri);
        request.Headers.Add("Ocp-Apim-Subscription-Key", key);

        var response = await client.SendAsync(request);

        // successful status doesn't return an JSON so create one
        if (response.IsSuccessStatusCode)
        {
            responseText = "{'result' : 'Success.'}";
        }
        else
        {
            responseText =  await response.Content.ReadAsStringAsync();
        }
    }
    Console.WriteLine(PrettyPrint(responseText));
    Console.WriteLine("Press any key to continue.");
}
```

A chamada à API retorna um status 204 de publicação bem-sucedida sem nenhum conteúdo no corpo da resposta. O código adiciona conteúdo para respostas 204.

Para qualquer outra resposta, essa resposta é retornada inalterada.
 
## <a name="add-the-publishkb-method-to-main"></a>Adicione o método PublishKB a Main

Altere o método Main para chamar o método CreateKB:

```csharp
static void Main(string[] args)
{

    // Call the PublishKB() method to publish a knowledge base.
    PublishKB();

    // The console waits for a key to be pressed before closing.
    Console.ReadLine();
}
```

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Compile e execute o programa. Ela enviará automaticamente a solicitação para a API de QnA Maker para publicar a base de dados de conhecimento e, em seguida, a resposta será impressa na janela do console.

Após sua base de dados de conhecimento ser publicada, você poderá consultá-la do ponto de extremidade com um aplicativo cliente ou chat bot. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST do QnA Maker (V4)](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
