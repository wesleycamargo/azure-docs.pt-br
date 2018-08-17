---
title: Use a API do Indexador de Vídeo do Azure | Microsoft Docs
description: Este artigo mostra como começar a usar a API do Video Indexer.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/25/2018
ms.author: juliako
ms.openlocfilehash: 73359955861b88f2bc5ca297c32fa78c2632148c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449454"
---
# <a name="use-azure-video-indexer-api"></a>Usar o indexador de vídeo do Azure API

> [!Note]
> A API do Video Indexer V1 foi descontinuada em 1º de agosto de 2018. Agora, você deve usar a API do indexador de vídeo v2. <br/>Para desenvolver com as APIs do Video Indexer v2, consulte as instruções encontradas [aqui](https://api-portal.videoindexer.ai/). 

O Video Indexer consolida várias tecnologias de inteligência artificial (AI) de áudio e vídeo oferecidas pela Microsoft em um único serviço integrado, tornando o desenvolvimento mais simples. As APIs são projetadas para permitir que os desenvolvedores se concentrem no consumo de tecnologias de AI de mídia sem se preocupar com escala, alcance global, disponibilidade e confiabilidade da plataforma de nuvem. Você pode usar a API para fazer upload de seus arquivos, obter insights de vídeo detalhados, obter URLs de insights e widgets de player para incorporá-los ao seu aplicativo e outras tarefas.

Ao criar uma conta do Video Indexer, você pode escolher uma conta de avaliação gratuita (em que você obtém um determinado número de minutos de indexação gratuitos) ou uma opção paga (onde você não está limitado pela cota). Com o teste gratuito, o Video Indexer fornece até 600 minutos de indexação gratuita para usuários do site e até 2400 minutos de indexação gratuita para usuários da API. Com a opção paga, você cria uma conta do Indexador de Vídeo que está [conectada à sua assinatura do Azure e a uma conta dos Serviços de Mídia do Azure](connect-to-azure.md). Você paga por minutos indexados, bem como os encargos relacionados à conta de mídia. 

Este artigo mostra como os desenvolvedores podem aproveitar a [API do Video Indexer](https://api-portal.videoindexer.ai/). Para ler uma visão mais detalhada do serviço do indexador de vídeo, consulte o artigo [visão geral](video-indexer-overview.md).

## <a name="subscribe-to-the-api"></a>Inscrever-se à API

1. Entrar.

    Para começar a desenvolver com o Video Indexer, você deve primeiro entrar no portal [Video Indexer](https://api-portal.videoindexer.ai/). 
    
    ![Inscrição](./media/video-indexer-use-apis/video-indexer-api01.png)

    > [!Important]
    > * Você deve usar o mesmo provedor usado quando se inscreveu no Video Indexer.
    > * As contas pessoais Google e Microsoft (perspectiva / vida) só podem ser usadas para contas de avaliação. Contas conectadas ao Azure exigem o Azure AD.
    > * Pode haver apenas uma conta ativa por email. Se um usuário tenta entrar com user@gmail.com para o LinkedIn e, depois disso com user@gmail.com para Google o posterior exibirá uma página de erro, informando que o usuário já existe.


2. Inscrever-se.

    Selecione a guia [Produtos](https://api-portal.videoindexer.ai/products). Em seguida, selecione a autorização e inscrever-se. 
    
    ![Inscrição](./media/video-indexer-use-apis/video-indexer-api02.png)

    > [!NOTE]
    > Novos usuários são automaticamente inscritos na Autorização.
    
    Depois de se inscrever, você poderá ver sua assinatura e suas chaves primárias e secundárias. As chaves devem ser protegidas. As chaves só devem ser usadas pelo seu código do servidor. Eles não devem estar disponíveis no lado do cliente (.js, .html, etc.).

    ![Inscrição](./media/video-indexer-use-apis/video-indexer-api03.png)

## <a name="obtain-access-token-using-the-authorization-api"></a>Obter o token de acesso usando a API de autorização

Depois de se inscrever na API de autorização, você poderá obter tokens de acesso. Esses tokens de acesso são usados para autenticar na API de operações. 

Cada chamada para a API de operações deve estar associada a um token de acesso, correspondendo ao escopo de autorização da chamada.

- Nível de usuário - os tokens de acesso no nível do usuário permitem que você execute operações no nível do **usuário**. Por exemplo, obter contas associadas.
- Nível da conta - os tokens de acesso no nível da conta permitem que você execute operações no nível da **conta** ou no nível do **vídeo**. Por exemplo, carregar o vídeo, listar todos os vídeos, obtenha insights de vídeo, etc.
- Nível de vídeo - os tokens de acesso ao nível de vídeo permitem que você realize operações em um vídeo **específico**. Por exemplo, obtenha informações sobre vídeos, faça o download de legendas, obtenha widgets etc. 

Você pode controlar se esses tokens são somente leitura ou se eles permitem editar especificando **allowEdit = true / false**.

Na maioria dos cenários de servidor para servidor, você provavelmente usará o mesmo **conta** de token, pois ele abrange **conta** operações e **vídeo** operações. No entanto, se você estiver planejando fazer chamadas do lado do cliente para o Video Indexer (por exemplo, do javascript), convém usar um token de acesso **video** para impedir que os clientes obtenham acesso a toda a conta. Essa também é a razão pela qual ao incorporar o código do cliente VideoIndexer em seu cliente (por exemplo, usando **Get Insights Widget** ou **Get Player Widget**) você deve fornecer um **vídeo** token de acesso.

Para facilitar, você pode usar a **API de autorização**> **GetAccounts** para obter suas contas sem obter primeiro um token de usuário. Você também pode pedir para obter as contas com tokens válidos, permitindo que você ignore uma chamada adicional para obter um token de conta.

Os tokens de acesso expiram após 1 hora. Certifique-se de que seu token de acesso seja válido antes de usar a API de operações. Se expirar, chame a API de autorização novamente para obter um novo token de acesso.
 
Você está pronto para começar a integrar com a API. Encontre [a descrição detalhada de cada API REST do Indexador de Vídeo](http://api-portal.videoindexer.ai/).

## <a name="location"></a>Local padrão

Todas as APIs de operação requerem um parâmetro Location, que indica a região para a qual a chamada deve ser roteada e na qual a conta foi criada.

Os valores descritos na tabela a seguir se aplicam. O **valor Param** é o valor que você passa ao usar a API.

|**Nome**|**Valor do parâmetro**|**Descrição**|
|---|---|---|
|Avaliação|versão de avaliação|Usado para contas de avaliação.|
|Oeste dos EUA|westus2|Usado para a região do Azure Oeste dos EUA 2.|
|Norte da Europa |northeurope|Usado para a região do Azure Norte da Europa.|
|Ásia Oriental|eastasia|Usado para a região do Azure East Asia.|

## <a name="account-id"></a>ID da Conta 

O parâmetro de ID da conta é obrigatório em todas as chamadas da API operacional. O ID da conta é um GUID que pode ser obtido de uma das seguintes maneiras:

* Use o portal Video Indexer para obter o ID da conta:

    1. Entrar no [videoindexer](https://www.videoindexer.ai/).
    2. Navegue até a **configurações** página.
    3. Copie a ID de conta.

        ![ID da Conta](./media/video-indexer-use-apis/account-id.png)

* Use a API para obter programaticamente o ID da conta.

    Use o [obter contas](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Accounts?) API.
    
    > [!TIP]
    > Você pode gerar tokens de acesso para as contas definindo `generateAccessTokens=true`.
    
* Obtenha o ID da conta a partir do URL de uma página do player em sua conta.

    Quando você assiste a um vídeo, o ID aparece após a `accounts`seção e antes da`videos` seção.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Recomendações

Esta seção lista algumas recomendações ao usar a API do Video Indexer.

- Se você planeja carregar um vídeo, é recomendável colocar o arquivo em algum local de rede pública (por exemplo, OneDrive). Obter o link para vídeo e fornecer a URL como o parâmetro de arquivo de upload. 

    A URL fornecida ao Video Indexer deve apontar para um arquivo de mídia (áudio ou vídeo). Alguns dos links gerados pelo OneDrive são para uma página HTML que contém o arquivo. Uma verificação fácil para o URL seria colá-lo em um navegador. Se o arquivo começar a ser baixado, provavelmente é um bom URL. Se o navegador estiver processando alguma visualização, provavelmente não será um link para um arquivo, mas uma página HTML.
    
- Quando você chama a API que recebe insights de vídeo para o vídeo especificado, você obtém uma saída JSON detalhada como o conteúdo da resposta. [Ver detalhes sobre o JSON retornado neste tópico](video-indexer-output-json.md).

## <a name="code-sample"></a>Exemplo de código

O seguinte trecho de código em C# demonstra o uso de todas as APIs do indexador de vídeo juntos.

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2";
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer =newbyte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(newByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token            
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

  Debug.WriteLine("");
  Debug.WriteLine("State:");
  Debug.WriteLine(processingState);

  // job is finished
  if (processingState != "Uploaded" && processingState != "Processing")
  {
      Debug.WriteLine("");
      Debug.WriteLine("Full JSON:");
      Debug.WriteLine(videoGetIndexResult);
      break;
  }
}

// search for the video
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);

```

## <a name="next-steps"></a>Próximas etapas

[Examinar os detalhes da saída JSON](video-indexer-output-json.md).

## <a name="see-also"></a>Consulte também

[Visão geral do indexador vídeo](video-indexer-overview.md)
