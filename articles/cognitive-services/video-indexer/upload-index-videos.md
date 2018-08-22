---
title: Carregar e indexar seus vídeos com o Azure Video Indexer | Microsoft Docs
description: Este tópico demonstra como usar APIs para carregar e indexar seus vídeos com o Azure Video Indexer
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 07/25/2018
ms.author: juliako
ms.openlocfilehash: c4a755d0c13516ce3cb0177cea2ea17e4a3abcbb
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390962"
---
# <a name="upload-and-index-your-videos"></a>Carregar e indexar seus vídeos  

Este artigo mostra como usar a API [Carregar vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) para carregar e indexar seus vídeos com o Azure Video Indexer. Ele também aborda alguns parâmetros que você pode definir na API para alterar o processo e a saída da API.

> [!Note]
> Ao criar uma conta do Video Indexer, você pode escolher uma conta de avaliação gratuita (em que você obtém um determinado número de minutos de indexação gratuitos) ou uma opção paga (onde você não está limitado pela cota). <br/>Com o teste gratuito, o Video Indexer fornece até 600 minutos de indexação gratuita para usuários do site e até 2400 minutos de indexação gratuita para usuários da API. <br/>Com a opção paga, você cria uma conta do Indexador de Vídeo que está [conectada à sua assinatura do Azure e a uma conta dos Serviços de Mídia do Azure](connect-to-azure.md). Você paga por minutos indexados, bem como os encargos relacionados à conta de mídia. 

## <a name="configurations-and-params"></a>Configurações e parâmetros

Esta seção descreve alguns parâmetros opcionais e quando você deseja defini-los.

### <a name="externalid"></a>externalID 

Este parâmetro permite que você especifique uma ID que será associada ao vídeo. A ID pode ser aplicada à integração de sistema externa VCM (Gerenciamento de Conteúdo de Vídeo). Os vídeos localizados no portal do Video Indexer podem ser pesquisados usando a ID externa especificada.

### <a name="indexingpreset"></a>indexingPreset

Use esse parâmetro se gravações brutas ou externas contiverem ruídos de fundo. Esse parâmetro é usado para configurar o processo de indexação. É possível especificar os seguintes valores:

- `Default` – Indexar e extrair insights usando áudio e vídeo
- `AudioOnly` – Indexar e extrair insights usando apenas áudio (ignorando vídeo)
- `DefaultWithNoiseReduction` – Indexar e extrair insights de áudio e vídeo durante a aplicação de algoritmos de redução de ruído no fluxo de áudio

O preço depende da opção de indexação selecionada.  

### <a name="callbackurl"></a>callbackUrl

Uma URL DE POSTAGEM para notificar quando a indexação é concluída. O Video Indexer adiciona dois parâmetros de cadeia de consulta a ele: id e state. Por exemplo, se a URL de retorno de chamada for 'https://test.com/notifyme?projectName=MyProject', a notificação será enviada com parâmetros adicionais para 'https://test.com/notifyme?projectName=MyProject&id=1234abcd&state=Processed'.

Também é possível adicionar mais parâmetros à URL antes de postar a chamada no Video Indexer, e esses parâmetros estarão incluídos no retorno de chamada. Posteriormente, em seu código, é possível analisar a cadeia de consulta e obter de volta todos os parâmetros especificados na cadeia de consulta (dados que você tinha originalmente acrescentado à URL mais as informações fornecidas do Video Indexer.) 

### <a name="streamingpereset"></a>streamingPereset

Depois que o vídeo tiver sido carregado, o Video Indexer codificará opcionalmente o vídeo. Em seguida, passe para a indexação e análise do vídeo. Quando o Video Indexer terminar a análise, você receberá uma notificação com a ID do vídeo.  

Ao usar a API [Carregar vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) ou [Reindexar vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?), um dos parâmetros opcionais é `streamingPreset`. Se você definir `streamingPereset` como `Default`, `SingleBitrate` ou `AdaptiveBitrate`, o processo de codificação será disparado. Depois que os trabalhos de indexação e codificação forem concluídos, o vídeo será publicado para que você também possa transmiti-lo. O Ponto de extremidade de streaming do qual você deseja transmitir o vídeo deve estar no estado **Executando**.

Para executar trabalhos de indexação e de codificação, a [conta dos Serviços de Mídia do Azure conectada à sua conta do Video Indexer](connect-to-azure.md), requer unidades reservadas. Para obter mais informações, consulte [Scaling Media Processing](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview) (Colocação em escala do processamento de mídia). Como esses são trabalhos com uso intensivo de computação, o tipo de unidade S3 é altamente recomendável. O número de RUs define o número máximo de trabalhos que podem ser executados em paralelo. A recomendação de linha de base é de 10 RUs de S3. 

Se você apenas deseja indexar seu vídeo, mas não o codificar, defina `streamingPereset` como `NoStreaming`.

## <a name="code-sample"></a>Exemplo de código

O seguinte trecho de código em C# demonstra o uso de todas as APIs do indexador de vídeo juntos.

```csharp
public async Task Sample()
{
    var apiUrl = "https://api.videoindexer.ai";
    var location = "westus2";
    var apiKey = "...";

    System.Net.ServicePointManager.SecurityProtocol =
        System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

    // create the http client
    var handler = new HttpClientHandler();
    handler.AllowAutoRedirect = false;
    var client = new HttpClient(handler);
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);

    // obtain account information and access token
    string queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"generateAccessTokens", "true"},
            {"allowEdit", "true"},
        });
    HttpResponseMessage result = await client.GetAsync($"{apiUrl}/auth/trial/Accounts?{queryParams}");
    var json = await result.Content.ReadAsStringAsync();
    var accounts = JsonConvert.DeserializeObject<AccountContractSlim[]>(json);
    // take the relevant account, here we simply take the first
    var accountInfo = accounts.First();

    // we will use the access token from here on, no need for the apim key
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // upload a video
    var content = new MultipartFormDataContent();
    Debug.WriteLine("Uploading...");
    // get the video from URL
    var videoUrl = "VIDEO_URL"; // replace with the video URL

    // as an alternative to specifying video URL, you can upload a file.
    // remove the videoUrl parameter from the query params below and add the following lines:
    //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
    //byte[] buffer =newbyte[video.Length];
    //video.Read(buffer, 0, buffer.Length);
    //content.Add(newByteArrayContent(buffer));

    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"name", "video_name"},
            {"description", "video_description"},
            {"privacy", "private"},
            {"partition", "partition"},
            {"videoUrl", videoUrl},
        });
    var uploadRequestResult = await client.PostAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos?{queryParams}", content);
    var uploadResult = await uploadRequestResult.Content.ReadAsStringAsync();

    // get the video id from the upload result
    string videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
    Debug.WriteLine("Uploaded");
    Debug.WriteLine("Video ID:");
    Debug.WriteLine(videoId);

    // wait for the video index to finish
    while (true)
    {
        await Task.Delay(10000);

        queryParams = CreateQueryString(
            new Dictionary<string, string>()
            {
                {"accessToken", accountInfo.AccessToken},
                {"language", "English"},
            });

        var videoGetIndexRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/Index?{queryParams}");
        var videoGetIndexResult = await videoGetIndexRequestResult.Content.ReadAsStringAsync();

        string processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

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
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", accountInfo.AccessToken},
            {"id", videoId},
        });

    var searchRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/Search?{queryParams}");
    var searchResult = await searchRequestResult.Content.ReadAsStringAsync();
    Debug.WriteLine("");
    Debug.WriteLine("Search:");
    Debug.WriteLine(searchResult);

    // Generate video access token (used for get widget calls)
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
    var videoTokenRequestResult = await client.GetAsync($"{apiUrl}/auth/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/AccessToken?allowEdit=true");
    var videoAccessToken = (await videoTokenRequestResult.Content.ReadAsStringAsync()).Replace("\"", "");
    client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

    // get insights widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
            {"widgetType", "Keywords"},
            {"allowEdit", "true"},
        });
    var insightsWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/InsightsWidget?{queryParams}");
    var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
    Debug.WriteLine("Insights Widget url:");
    Debug.WriteLine(insightsWidgetLink);

    // get player widget url
    queryParams = CreateQueryString(
        new Dictionary<string, string>()
        {
            {"accessToken", videoAccessToken},
        });
    var playerWidgetRequestResult = await client.GetAsync($"{apiUrl}/{accountInfo.Location}/Accounts/{accountInfo.Id}/Videos/{videoId}/PlayerWidget?{queryParams}");
    var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
    Debug.WriteLine("");
    Debug.WriteLine("Player Widget url:");
    Debug.WriteLine(playerWidgetLink);
}

private string CreateQueryString(IDictionary<string, string> parameters)
{
    var queryParameters = HttpUtility.ParseQueryString(string.Empty);
    foreach (var parameter in parameters)
    {
        queryParameters[parameter.Key] = parameter.Value;
    }

    return queryParameters.ToString();
}

public class AccountContractSlim
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string Location { get; set; }
    public string AccountType { get; set; }
    public string Url { get; set; }
    public string AccessToken { get; set; }
}
```



## <a name="next-steps"></a>Próximas etapas

[Examinar a saída do Azure Video Indexer produzida pela API v2](video-indexer-output-json-v2.md)
