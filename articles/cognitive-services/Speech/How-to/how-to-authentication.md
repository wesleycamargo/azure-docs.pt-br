---
title: Autenticar na Fala do Bing | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Solicitar autenticação para usar a API de Fala do Bing
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: 205eff6c79ba4699516a8898c5b1268eb3dfe644
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2018
ms.locfileid: "53754009"
---
# <a name="authenticate-to-the-speech-api"></a>Autenticar para a API de fala

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

A Fala do Bing dá suporte à autenticação usando:

- Uma chave de assinatura.
- Um token de autorização.

## <a name="use-a-subscription-key"></a>Use uma chave de assinatura

Para usar o Serviço de Fala, primeiro você deve assinar a API de fala que faz parte dos serviços cognitivos (anteriormente Projeto Oxford). É possível obter chaves de assinatura de avaliação gratuita na página de [assinatura dos Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/). Após selecionar o Speech API, selecione **Obter chave de API** para obter a chave. Isso retorna uma chave primária e secundária. Ambas as chaves estão vinculadas à mesma cota, portanto, é possível usar uma das chaves.

Para uso de longo prazo ou cota maior, inscreva-se para uma [conta gratuita do Azure](https://azure.microsoft.com/free/).

Para usar a API de REST de fala, você precisa passar a chave de assinatura no `Ocp-Apim-Subscription-Key` campo no cabeçalho da solicitação.

NOME| Formatar| DESCRIÇÃO
----|-------|------------
Ocp-Apim-Subscription-Key | ASCII | YOUR_SUBSCRIPTION_KEY

Segue um exemplo de um cabeçalho de solicitação:

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

> [!IMPORTANT]
> Se você usar [bibliotecas de cliente](../GetStarted/GetStartedClientLibraries.md) em seu aplicativo, verifique se você pode obter o token de autorização com a sua chave de assinatura, conforme descrito na seção a seguir. As bibliotecas de cliente usam a chave de assinatura para obter um token de autorização e, em seguida, usam o token de autenticação.

## <a name="use-an-authorization-token"></a>Use um token de autorização

Como alternativa, você pode usar um token de autorização para autenticação, como prova de autenticação/autorização. Para obter esse token, você deve primeiro obter uma chave de assinatura da API de fala, conforme descrito na [seção anterior](#use-a-subscription-key).

### <a name="get-an-authorization-token"></a>Use um token de autorização

Depois que você tiver uma chave de assinatura válida, envie uma solicitação POST para o serviço de token de Serviços Cognitivos. Na resposta, você receberá na resposta o token de autorização como um JWT (Token Web JSON).

> [!NOTE]
> O token tem um prazo de 10 minutos. Para renovar o token, consulte a seção a seguir.

O URI do serviço de token está localizado aqui:

```
https://api.cognitive.microsoft.com/sts/v1.0/issueToken
```

O exemplo de código mostra como obter um token de acesso. Substitua `YOUR_SUBSCRIPTION_KEY` pela sua própria chave de assinatura:

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

O exemplo usa curl no Linux com bash. Se não estiver disponível na plataforma, talvez seja necessário instalar o curl. O exemplo também funciona no Cygwin no Windows, Git Bash, zsh e em outros shells.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
    /*
     * This class demonstrates how to get a valid access token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = "https://api.cognitive.microsoft.com/sts/v1.0";
        private string subscriptionKey;
        private string token;

        public Authentication(string subscriptionKey)
        {
            this.subscriptionKey = subscriptionKey;
            this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
        }

        public string GetAccessToken()
        {
            return this.token;
        }

        private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
        {
            using (var client = new HttpClient())
            {
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                UriBuilder uriBuilder = new UriBuilder(fetchUri);
                uriBuilder.Path += "/issueToken";

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```

---

Veja a seguir um exemplo de solicitação de POST:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
Connection: Keep-Alive
```

Se você não puder obter um token de autorização do serviço de token, verifique se a sua chave de assinatura ainda é válida. Se você estiver usando uma chave de avaliação gratuita, vá para a página [Assinatura serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/), clique em "Login" para fazer logon usando a conta que é usada para aplicar a chave de avaliação gratuita e verifique se a chave de assinatura está expirada ou excede a cota.

### <a name="use-an-authorization-token-in-a-request"></a>Use um token de autorização em uma solicitação

Cada vez que você chamar a API de fala, você precisa passar o token de autorização no `Authorization` cabeçalho. O `Authorization` cabeçalho deve conter um token de acesso JWT.

O exemplo a seguir mostra como usar um token de autorização, quando você chamar a API de REST de fala.

> [!NOTE]
> Substituir `YOUR_AUDIO_FILE` pelo caminho para o arquivo de áudio pré-gravado. Substituir `YOUR_ACCESS_TOKEN` com o token de autorização que você obteve na etapa anterior [Obter um token de autorização](#get-an-authorization-token).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authrization token returned by the token service.
$RecoRequestHeader = @{
  'Authorization' = 'Bearer '+ $OAuthToken;
  'Transfer-Encoding' = 'chunked'
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```cs
HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Authorization"] = "Bearer " + token;

// Send an audio file by 1024 byte chunks
using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
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

### <a name="renew-an-authorization-token"></a>Reove um token de autorização

O token de autorização expira após um determinado período de tempo (atualmente em 10 minutos). Você deve renovar o token de autorização antes de expirar.

O código a seguir é uma implementação de exemplo em C# como renovar o token de autorização:

```cs
    /*
     * This class demonstrates how to get a valid O-auth token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = "https://api.cognitive.microsoft.com/sts/v1.0";
        private string subscriptionKey;
        private string token;
        private Timer accessTokenRenewer;

        //Access token expires every 10 minutes. Renew it every 9 minutes.
        private const int RefreshTokenDuration = 9;

        public Authentication(string subscriptionKey)
        {
            this.subscriptionKey = subscriptionKey;
            this.token = FetchToken(FetchTokenUri, subscriptionKey).Result;

            // renew the token on set duration.
            accessTokenRenewer = new Timer(new TimerCallback(OnTokenExpiredCallback),
                                           this,
                                           TimeSpan.FromMinutes(RefreshTokenDuration),
                                           TimeSpan.FromMilliseconds(-1));
        }

        public string GetAccessToken()
        {
            return this.token;
        }

        private void RenewAccessToken()
        {
            this.token = FetchToken(FetchTokenUri, this.subscriptionKey).Result;
            Console.WriteLine("Renewed token.");
        }

        private void OnTokenExpiredCallback(object stateInfo)
        {
            try
            {
                RenewAccessToken();
            }
            catch (Exception ex)
            {
                Console.WriteLine(string.Format("Failed renewing access token. Details: {0}", ex.Message));
            }
            finally
            {
                try
                {
                    accessTokenRenewer.Change(TimeSpan.FromMinutes(RefreshTokenDuration), TimeSpan.FromMilliseconds(-1));
                }
                catch (Exception ex)
                {
                    Console.WriteLine(string.Format("Failed to reschedule the timer to renew access token. Details: {0}", ex.Message));
                }
            }
        }

        private async Task<string> FetchToken(string fetchUri, string subscriptionKey)
        {
            using (var client = new HttpClient())
            {
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                UriBuilder uriBuilder = new UriBuilder(fetchUri);
                uriBuilder.Path += "/issueToken";

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```
