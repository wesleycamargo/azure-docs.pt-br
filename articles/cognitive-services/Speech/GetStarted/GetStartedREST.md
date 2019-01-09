---
title: Introdução à API de reconhecimento de Fala do Bing usando REST | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Use REST para acessar a API de reconhecimento de Fala nos Serviços Cognitivos da Microsoft para converter o áudio falado em texto.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: 5cbdad82e25baa95c0342eb514f39c7026f1618b
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2018
ms.locfileid: "53753063"
---
# <a name="quickstart-use-the-bing-speech-recognition-rest-api"></a>Início Rápido: Usar a API REST de reconhecimento de Fala do Bing

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Com o serviço de Fala do Bing baseado em nuvem, você pode desenvolver aplicativos usando a API REST para converter áudio falado em texto.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Inscreva-se na API de Fala e receba uma chave de assinatura de avaliação gratuita

O Speech API é parte dos Serviços Cognitivos (anteriormente Projeto Oxford). É possível obter chaves de assinatura de avaliação gratuita na página de [assinatura dos Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/). Após selecionar o Speech API, selecione **Obter chave de API** para obter a chave. Isso retorna uma chave primária e secundária. Ambas as chaves estão vinculadas à mesma cota, portanto, é possível usar uma das chaves.

> [!IMPORTANT]
>* Obtenha uma chave de assinatura. Antes de poder acessar a API REST, você deve ter uma [chave de assinatura](https://azure.microsoft.com/try/cognitive-services/).
>
>* Use sua chave de assinatura. Nos exemplos de REST a seguir, substitua YOUR_SUBSCRIPTION_KEY pela sua própria chave de assinatura. 
>
>* Consulte a página [autenticação](../how-to/how-to-authentication.md) para saber como obter uma chave de assinatura.

### <a name="prerecorded-audio-file"></a>Arquivo de áudio pré-gravado

Neste exemplo, usamos um arquivo de áudio gravado para ilustrar como usar a API REST. Grave um arquivo de áudio de si mesmo dizendo uma frase curta. Por exemplo, diga "Como está o clima hoje?" ou "Localize filmes engraçados para assistir". A API de reconhecimento de fala também oferece suporte a entrada de microfone externo.

> [!NOTE]
> O exemplo requer que áudio seja gravado como um arquivo WAV com canal **PCM único (mono), 16 KHz**.

## <a name="build-a-recognition-request-and-send-it-to-the-speech-recognition-service"></a>Criar uma solicitação de reconhecimento e enviá-la para o serviço de reconhecimento de fala

A próxima etapa para o reconhecimento de fala é enviar uma solicitação POST para os pontos de extremidade de HTTP de fala com o cabeçalho de solicitação apropriada e corpo.

### <a name="service-uri"></a>URI de serviço

O serviço de reconhecimento de fala URI é definido com base em [modos de reconhecimento](../concepts.md#recognition-modes) e [idiomas de reconhecimento](../concepts.md#recognition-languages):

```HTTP
https://speech.platform.bing.com/speech/recognition/<RECOGNITION_MODE>/cognitiveservices/v1?language=<LANGUAGE_TAG>&format=<OUTPUT_FORMAT>
```

`<RECOGNITION_MODE>` especifica o modo de reconhecimento e deve ser um dos seguintes valores: `interactive`, `conversation`, ou `dictation`. É um caminho de recurso exigido no URI. Para saber obter mais informações, consulte [Modos de Reconhecimento](../concepts.md#recognition-modes).

`<LANGUAGE_TAG>` é um parâmetro obrigatório na cadeia de caracteres de consulta. Define o idioma de destino para a conversão de áudio: por exemplo, `en-US` para inglês (Estados Unidos). Para obter mais informações, consulte [Idiomas de Reconhecimento](../concepts.md#recognition-languages).

`<OUTPUT_FORMAT>` é um parâmetro opcional na cadeia de caracteres de consulta Seus valores permitidos são `simple` e `detailed`. Por padrão, o serviço retorna resultados `simple` no formato. Para obter mais informações, consulte o [Formato de saída](../concepts.md#output-format).

Alguns exemplos de URIs de serviço estão listados na tabela a seguir.

| Modo de reconhecimento  | Linguagem | Formato da saída | URI de serviço |
|---|---|---|---|
| `interactive` | pt-BR | Padrão | https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| `conversation` | en-US | Detalhado |https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed |
| `dictation` | fr-FR | Simples | https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR&format=simple |

> [!NOTE]
> O URI do serviço é necessário somente quando seu aplicativo usa APIs REST para chamar o serviço de reconhecimento de fala. Se você usar uma das [bibliotecas de cliente](GetStartedClientLibraries.md),  normalmente não precisa saber qual URI é usado. As bibliotecas de cliente podem usar outro serviço URIs, que são aplicáveis apenas para uma biblioteca de cliente específico. Para obter mais informações, consulte a biblioteca de cliente de sua escolha.

### <a name="request-headers"></a>Cabeçalhos da solicitação

Os campos a seguir devem ser definidos no cabeçalho da solicitação:

- `Ocp-Apim-Subscription-Key`: Toda vez que você chama o serviço, você deve passar sua chave de assinatura no `Ocp-Apim-Subscription-Key`cabeçalho. Serviço de Fala também dá suporte a autorização passando tokens em vez de chaves de assinatura. Para obter mais informações, consulte [Autenticação](../How-to/how-to-authentication.md).
- `Content-type`: O `Content-type` campo descreve o formato e o codec do fluxo de áudio. No momento, somente arquivo WAV e codificação de PCM Mono 16000 têm suporte. O valor de tipo de conteúdo para esse formato é `audio/wav; codec=audio/pcm; samplerate=16000`.

O campo `Transfer-Encoding` é opcional. Se você definir esse campo como `chunked`, você pode cortar o áudio em pequenas partes. Para obter mais informações, consulte [em transferência por parts](../How-to/how-to-chunked-transfer.md).

Veja a seguir um exemplo de solicitação de cabeçalho:

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="send-a-request-to-the-service"></a>Enviar uma solicitação para o serviço

O exemplo a seguir mostra como enviar uma solicitação de reconhecimento de fala para pontos de extremidade REST de fala. Usa o `interactive`modo de reconhecimento

> [!NOTE]
> Substitua `YOUR_AUDIO_FILE` pelo caminho para o arquivo de áudio pré-gravado. Substitua `YOUR_SUBSCRIPTION_KEY` pela sua própria chave de assinatura.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authorization token returned by the token service.
$RecoRequestHeader = @{
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY';
  'Transfer-Encoding' = 'chunked';
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

O exemplo usa curl no Linux com bash. Se não estiver disponível na plataforma, talvez seja necessário instalar o curl. O exemplo também funciona no Cygwin no Windows, Git Bash, zsh e em outros shells.

> [!NOTE]
> Manter o `@` antes do nome do arquivo de áudio ao substituir `YOUR_AUDIO_FILE` com o caminho para o arquivo de áudio pré-gravado, uma vez que isso indica que o valor de `--data-binary` é um nome de arquivo em vez de dados.

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

// Send an audio file by 1024 byte chunks
using (FileStream fs = new FileStream(YOUR_AUDIO_FILE, FileMode.Open, FileAccess.Read))
{

    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```

---

## <a name="process-the-speech-recognition-response"></a>Processar a resposta de reconhecimento de fala

Depois de processar a solicitação, o serviço de fala retorna os resultados em uma resposta como formato JSON.

> [!NOTE]
> Se o código anterior retorna um erro, consulte [solução de problemas](../troubleshooting.md) para localizar a possível causa.

O snippet de código a seguir mostra um exemplo de como você pode ler a resposta do fluxo.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
# show the response in JSON format
ConvertTo-Json $RecoResponse
```

# <a name="curltabcurl"></a>[curl](#tab/curl)

Neste exemplo, rotação diretamente retorna a mensagem de resposta em uma cadeia de caracteres. Se você deseja mostrá-la no formato JSON, você pode usar ferramentas adicionais, por exemplo, jq.

```
curl -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE | jq
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
/*
* Get the response from the service.
*/
Console.WriteLine("Response:");
using (WebResponse response = request.GetResponse())
{
    Console.WriteLine(((HttpWebResponse)response).StatusCode);

    using (StreamReader sr = new StreamReader(response.GetResponseStream()))
    {
        responseString = sr.ReadToEnd();
    }

    Console.WriteLine(responseString);
    Console.ReadLine();
}
```

---

O exemplo a seguir é uma resposta JSON:

```json
OK
{
  "RecognitionStatus": "Success",
  "Offset": 22500000,
  "Duration": 21000000,
  "NBest": [{
    "Confidence": 0.941552162,
    "Lexical": "find a funny movie to watch",
    "ITN": "find a funny movie to watch",
    "MaskedITN": "find a funny movie to watch",
    "Display": "Find a funny movie to watch."
  }]
}
```

## <a name="limitations"></a>Limitações

A API REST tem  algumas limitações.

- Ela dá suporte ao fluxo de áudio somente até 15 segundos.
- Não oferece suporte a resultados intermediários durante o reconhecimento. Os usuários receberão apenas o resultado final de reconhecimento.

Para remover essas limitações, use [bibliotecas de cliente](GetStartedClientLibraries.md) de Fala. Ou você pode trabalhar diretamente com o [protocolo WebSocket de fala](../API-Reference-REST/websocketprotocol.md).

## <a name="whats-next"></a>O que vem a seguir

- Para ver como usar a API REST no C#, Java, etc., consulte estes [aplicativos de exemplo](../samples.md).
- Se você encontrar erros, confira a [Solução de problemas](../troubleshooting.md).
- Para usar os recursos mais avançados, consulte a introdução usando [bibliotecas de cliente de fala](GetStartedClientLibraries.md).

### <a name="license"></a>Licença

Todos os SDKs dos Serviços Cognitivos da Microsoft e exemplos são licenciados com a licença do MIT. Para obter mais informações, consulte [Licença](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
