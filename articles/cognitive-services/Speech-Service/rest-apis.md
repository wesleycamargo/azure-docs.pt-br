---
title: APIs REST do Serviço de Fala
description: Referência para API REST para o Serviço de Fala.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/09/2018
ms.author: v-jerkin
ms.openlocfilehash: 9991e0a1fc54e6aa4a99b8bfbd93abdfe974b01b
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39283062"
---
# <a name="speech-service-rest-apis"></a>APIs REST do Serviço de Fala

As APIs REST do serviço de Fala unificado são semelhantes às APIs fornecidas pela [API de Fala](https://docs.microsoft.com/azure/cognitive-services/Speech) (anteriormente conhecida como o Serviço de Fala do Bing). Os pontos de extremidade diferenciam-se dos pontos de extremidade usados pelo serviço de Fala anterior.

## <a name="speech-to-text"></a>Conversão de Fala em Texto

Na API de Conversão de fala em texto, somente os pontos de extremidade usados diferenciam-se da API De Reconhecimento de Fala do Serviço de Fala anterior. Os novos pontos de extremidade são mostrados na tabela a seguir. Use um que corresponda com sua região de assinatura.

[!include[](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

A API de Conversão de fala em texto é, pelo contrário, semelhante à [API REST](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest) para a API de Fala anterior.

A API REST de Conversão de Fala em Texto oferece suporte somente para expressões curtas. As solicitações podem conter até 10 segundos de áudio e durar no máximo 14 segundos no total. A API REST somente retorna resultados finais, não resultados interinos ou parciais.

> [!NOTE]
> Se você personalizou o modelo acústico ou o modelo de linguagem ou pronúncia, use seu ponto de extremidade personalizado no lugar disso.

## <a name="text-to-speech"></a>Texto em fala

A nova API de Conversão de Texto em Fala oferece suporte para saída de áudio de 24 Khz. O cabeçalho `X-Microsoft-OutputFormat` pode conter agora os valores a seguir.

|||
|-|-|
`raw-16khz-16bit-mono-pcm`         | `audio-16khz-16kbps-mono-siren`
`riff-16khz-16kbps-mono-siren`     | `riff-16khz-16bit-mono-pcm`
`audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3`
`audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm`
`riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3`
`audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3`

O serviço de Fala agora oferece duas vozes de 24 KHz:

Local | Linguagem   | Gênero | Mapeamento do nome do serviço
-------|------------|--------|------------
pt-BR  | Inglês (EUA) | Feminino | "Conversão de Texto em Fala do Microsoft Server (en-US, Jessa24kRUS)" 
pt-BR  | Inglês (EUA) | Masculino   | "Conversão de Texto em Fala do Microsoft Server (en-US, Guy24kRUS)"

A seguir estão os pontos de extremidades de REST para a API de Conversão de Texto em Fala do Serviço de Fala. Use o ponto de extremidade que corresponda com sua região de assinatura.

[!include[](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

Mantenha essas diferenças em mente, ao consultar a [documentação da API REST](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/bingvoiceoutput) para a API de Fala anterior.

## <a name="authentication"></a>Autenticação

Enviar uma solicitação à API REST do Serviço de Fala requer um token de acesso. Você obtém um token fornecendo a chave de assinatura para um ponto de extremidade `issueToken` de serviço de Fala regional, mostrado na tabela a seguir. Use o ponto de extremidade que corresponda com sua região de assinatura.

[!include[](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

Cada token de acesso é válido por 10 minutos. Você pode obter um novo token a qualquer momento — incluindo, se quiser, exatamente antes de cada solicitação da API REST de Fala. Para minimizar a latência e o tráfego de rede, no entanto, é recomendável usar o mesmo token por nove minutos.

As seções a seguir mostram como obter um token e como usá-lo em uma solicitação.

### <a name="getting-a-token-http"></a>Obter um token: HTTP

Abaixo está um exemplo de solicitação HTTP para obter um token. Substitua `YOUR_SUBSCRIPTION_KEY` por sua chave de assinatura do Serviço de Fala. Se sua assinatura não estiver na região Oeste dos EUA, substitua o cabeçalho `Host` pelo nome de host da sua região.

```
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

O corpo da resposta para esta solicitação é o token de acesso no formato de Java Web Token (JWT).

### <a name="getting-a-token-powershell"></a>Obter um token: PowerShell

O script Windows PowerShell abaixo ilustra como obter um token de acesso. Substitua `YOUR_SUBSCRIPTION_KEY` por sua chave de assinatura do Serviço de Fala. Se sua assinatura não estiver na região Oeste dos EUA, altere o nome do host do URI especificado adequadamente.

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

### <a name="getting-a-token-curl"></a>Obter um token: cURL

cURL é uma ferramenta de linha de comando no Linux (e no Subsistema do Windows para Linux). O comando cURL abaixo ilustra como obter um token de acesso. Substitua `YOUR_SUBSCRIPTION_KEY` por sua chave de assinatura do Serviço de Fala. Se sua assinatura não estiver na região Oeste dos EUA, altere o nome do host do URI especificado adequadamente.

> [!NOTE]
> O comando é mostrado em várias linhas para legibilidade, mas deve entrar em uma única linha em uma shell do prompt.

```
curl -v -X POST 
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" 
 -H "Content-type: application/x-www-form-urlencoded" 
 -H "Content-Length: 0" 
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

### <a name="getting-a-token-c"></a>Obter um token: C#

A classe C# abaixo ilustra como obter um token de acesso. Passe sua chave de assinatura do Serviço de Fala ao instanciar a classe. Se sua assinatura não estiver na região Oeste dos EUA, altere o nome do host de `FetchTokenUri` adequadamente.

```cs
    /*
     * This class demonstrates how to get a valid access token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri =
            "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
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

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```

### <a name="using-a-token"></a>Usar um token

Para usar um token em uma solicitação de API REST, forneça-o no cabeçalho `Authorization`, após a palavra `Bearer`. Aqui está, por exemplo, uma solicitação REST de Conversão de Texto em Fala de exemplo que contém um token. Substitua seu token real por `YOUR_ACCESS_TOKEN` e use o nome de host correto no cabeçalho `Host`.

```xml
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.tts.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Hello, world!
</voice></speak>
```

### <a name="renewing-authorization"></a>Renovar autorização

O token de autorização expira após 10 minutos. Renove sua autorização obtendo um novo token antes de expirar — por exemplo, depois de nove minutos. 

O código C# a seguir é uma substituição imediata para a classe apresentada antes. A classe `Authentication` obtém automaticamente um novo token de acesso a cada minutos nove usando um timer. Essa abordagem garante que um token válido está sempre disponível enquanto o programa está em execução.

> [!NOTE]
> Em lugar de usar um timer, você pode armazenar um carimbo de data/hora de quando o token atual foi obtido, e solicitar um novo somente se o token atual estiver prestes a expirar. Este método evita solicitar novos tokens desnecessariamente e pode ser mais adequado para programas que fazem solicitações de Fala ocasionais.

Como antes, certifique-se de que o valor `FetchTokenUri` corresponda com sua região de assinatura. Passe sua chave de assinatura ao instanciar a classe.

```cs
    /*
     * This class demonstrates how to maintain a valid access token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = 
            "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
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

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```

## <a name="next-steps"></a>Próximas etapas

- [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de linguagem](how-to-customize-language-model.md)

