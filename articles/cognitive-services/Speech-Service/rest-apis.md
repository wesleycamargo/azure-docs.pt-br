---
title: APIs REST de serviço de fala
description: Referência de APIs REST para o serviço de fala.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: erhopf
ms.openlocfilehash: be2f6c49a260477e907f1f8f29f64b9eb08e6926
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51038596"
---
# <a name="speech-service-rest-apis"></a>APIs REST de serviço de fala

As APIs REST do serviço de Fala dos Serviços Cognitivos do Azure são semelhantes às APIs fornecidas pela [Bing Speech API](https://docs.microsoft.com/azure/cognitive-services/Speech). Os pontos de extremidade diferenciam-se daqueles usados pelo serviço de Fala do Bing. Pontos de extremidade regionais estão disponíveis, e será preciso usar uma chave de assinatura correspondente ao ponto de extremidade que você está usando.

## <a name="speech-to-text"></a>Conversão de Fala em Texto

Os pontos de extremidade para a API REST de Conversão de Fala em Texto são mostrados na tabela a seguir. Use um que corresponda com sua região de assinatura.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

> [!NOTE]
> Se você personalizou o modelo acústico ou o modelo de linguagem ou pronúncia, use seu ponto de extremidade personalizado no lugar disso.

Essa API é compatível apenas com declarações curtas. As solicitações podem conter até 10 segundos de áudio e durar no máximo 14 segundos no total. A API REST retorna apenas resultados finais, não resultados parciais ou provisórios. O serviço de Fala também conta com uma API de [transcrição em lote](batch-transcription.md) que pode transcrever áudios mais longos.


### <a name="query-parameters"></a>Parâmetros de consulta

Os seguintes parâmetros podem ser incluídos na cadeia de caracteres de consulta da solicitação REST.

|Nome do parâmetro|Obrigatório/opcional|Significado|
|-|-|-|
|`language`|Obrigatório|O identificador do idioma a ser reconhecido. Confira os [Idiomas compatíveis](language-support.md#speech-to-text).|
|`format`|Opcional<br>padrão: `simple`|Formato do resultado, `simple` ou `detailed`. Os resultados simples incluem `RecognitionStatus`, `DisplayText`, `Offset` e a duração. Os resultados detalhados incluem vários candidatos com valores de confiança e quatro representações diferentes.|
|`profanity`|Opcional<br>padrão: `masked`|Como tratar conteúdo ofensivo nos resultados de reconhecimento. Pode ser `masked` (substitui o conteúdo ofensivo por asteriscos), `removed` (remove todo o conteúdo ofensivo) ou `raw` (inclui o conteúdo ofensivo).

### <a name="request-headers"></a>Cabeçalhos da solicitação

Os campos a seguir são enviados no cabeçalho da solicitação HTTP.

|Cabeçalho|Significado|
|------|-------|
|`Ocp-Apim-Subscription-Key`|Sua chave de assinatura do serviço de Fala. É preciso fornecer esse cabeçalho ou `Authorization`.|
|`Authorization`|Um token de autorização precedido pela palavra `Bearer`. É preciso fornecer esse cabeçalho ou `Ocp-Apim-Subscription-Key`. Consulte [Autenticação](#authentication).|
|`Content-type`|Descreve o formato e o codec dos dados de áudio. Atualmente, esse valor precisa ser `audio/wav; codec=audio/pcm; samplerate=16000`.|
|`Transfer-Encoding`|Opcional. Se fornecido, precisa ser `chunked` para permitir que os dados de áudio sejam enviados em várias partes pequenas, em vez de um único arquivo.|
|`Expect`|Se usar transferência em partes, envie `Expect: 100-continue`. O serviço de Fala reconhece a solicitação inicial e aguarda os dados adicionais.|
|`Accept`|Opcional. Se fornecido, é preciso incluir `application/json`, já que o serviço de Fala fornece resultados no formato JSON. (Algumas estruturas de solicitação da Web fornecerão um valor padrão incompatível se você não especificar um, portanto, é recomendável sempre incluir `Accept`.)|

### <a name="audio-format"></a>Formato de áudio

O áudio é enviado no corpo da solicitação HTTP `POST`. Ele deve estar em um dos formatos nesta tabela:

| Formatar | Codec | Bitrate | Taxa de amostragem |
|--------|-------|---------|-------------|
| WAV | PCM | 16-bit | 16 kHz, mono |
| OGG | OPUS | 16-bit | 16 kHz, mono |

>[!NOTE]
>Os formatos acima têm suporte por meio da API REST e WebSocket no serviço de fala. O [Speech SDK](/index.yml) atualmente dá suporte a apenas o WAV de formato com o codec PCM.

### <a name="chunked-transfer"></a>Transferência em partes

A transferência em partes (`Transfer-Encoding: chunked`) pode ajudar a reduzir a latência de reconhecimento porque ela permite que o serviço de Fala comece a processar o arquivo de áudio enquanto ele está sendo transmitido. A API REST não fornece resultados parciais ou provisórios. Essa opção destina-se somente a melhorar a capacidade de resposta.

O código a seguir ilustra como enviar áudio em partes. Apenas o primeiro bloco deve conter o cabeçalho do arquivo de áudio. `request` é um objeto HTTPWebRequest conectado ao ponto de extremidade REST apropriado. `audioFile` é o caminho para um arquivo de áudio em disco.

```csharp
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

### <a name="example-request"></a>Solicitação de exemplo

Veja a seguir uma solicitação típica.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec="audio/pcm"; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="http-status"></a>Status HTTP

O status HTTP da resposta indica o êxito ou as condições de erro comuns.

Código HTTP|Significado|Possível motivo
-|-|-|
100|Continue|A solicitação inicial foi aceita. Continue enviando o restante dos dados. (Usado com transferência em partes.)
200|OK|A solicitação foi bem-sucedida. O corpo da resposta é um objeto JSON.
400|Solicitação incorreta|O código de idioma não foi fornecido ou o idioma não tem suporte. Arquivo de áudio inválido.
401|Não Autorizado|Chave de assinatura ou token de autorização inválido na região especificada, ou ponto de extremidade inválido.
403|Proibido|Chave de assinatura ou token de autorização ausente.

### <a name="json-response"></a>Resposta JSON

Os resultados são retornados no formato JSON. O formato `simple` inclui somente os seguintes campos superiores.

|Nome do campo|Conteúdo|
|-|-|
|`RecognitionStatus`|Status, como `Success` para reconhecimento bem-sucedido. Consulte a próxima tabela.|
|`DisplayText`|O texto reconhecido após o uso de maiúsculas, a pontuação, a normalização de texto inverso (conversão de texto falado em formas mais curtas, como 200 para “duzentos” ou “Dr. Rodrigues” para “doutor Rodrigues”) e o mascaramento de conteúdo ofensivo. Apresentar somente em caso de êxito.|
|`Offset`|O tempo (em unidades de 100 nanossegundos) no qual a fala reconhecida começa no fluxo de áudio.|
|`Duration`|A duração (em unidades de 100 nanossegundos) da fala reconhecida no fluxo de áudio.|

O campo `RecognitionStatus` pode conter os valores a seguir.

|Valor de status|DESCRIÇÃO
|-|-|
| `Success` | O reconhecimento foi bem-sucedido e o campo DisplayText está presente. |
| `NoMatch` | A fala foi detectada no fluxo de áudio, mas nenhuma palavra do idioma de destino foi combinada. Normalmente, isso significa que o idioma do reconhecimento é um idioma diferente daquele que o usuário está falando. |
| `InitialSilenceTimeout` | O início do fluxo de áudio continha apenas silêncio e o serviço atingiu o tempo limite aguardando pela fala. |
| `BabbleTimeout` | O início do fluxo de áudio continha apenas ruído e o serviço atingiu o tempo limite aguardando pela fala. |
| `Error` | O serviço de reconhecimento encontrou um erro interno e não foi possível continuar. Tente novamente, se possível. |

> [!NOTE]
> Se o áudio consistir apenas em conteúdo ofensivo e o parâmetro de consulta `profanity` estiver definido como `remove`, o serviço não retornará um resultado de fala.


O formato `detailed` inclui os mesmos campos que o formato `simple`, juntamente com um campo `NBest`. O campo `NBest` é uma lista de interpretações alternativas de reconhecimento da mesma fala, classificadas da mais provável até a menos provável. A primeira entrada é o mesmo que o resultado do reconhecimento principal. Cada entrada contém os seguintes campos:

|Nome do campo|Conteúdo|
|-|-|
|`Confidence`|A pontuação de confiança da entrada de 0,0 (nenhuma confiança) a 1,0 (confiança total)
|`Lexical`|O formato lexical do texto reconhecido: as palavras reais reconhecidas.
|`ITN`|O formato de texto inverso normalizado (“canônico”) do texto reconhecido, com números de telefone, números, abreviações (“doutor Rodrigues” para “dr Rodrigues”) e demais transformações aplicadas.
|`MaskedITN`| O formato ITN com mascaramento de conteúdo ofensivo aplicado se solicitado.
|`Display`| O formato de exibição do texto reconhecido, com a pontuação e uso de maiúsculas adicionados. Mesmo que `DisplayText` no resultado superior.

### <a name="sample-responses"></a>Respostas de exemplo

Veja a seguir uma resposta típica para reconhecimento de `simple`.

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Veja a seguir uma resposta típica para reconhecimento de `detailed`.

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="text-to-speech"></a>Texto em fala

Veja a seguir os pontos de extremidades de REST para a API de Conversão de Texto em Fala do serviço de Fala. Use o ponto de extremidade que corresponda com sua região de assinatura.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

O serviço de Fala é compatível com saída de áudio de 24 kHz, além da saída de 16 kHz compatível com a Fala do Bing. Quatro formatos de saída 24 KHz estão disponíveis para uso no cabeçalho HTTP `X-Microsoft-OutputFormat`, bem como duas vozes de 24 KHz, `Jessa24kRUS` e `Guy24kRUS`.

Local | Linguagem   | Gênero | Mapeamento do nome do serviço
-------|------------|--------|------------
pt-BR  | Inglês (EUA) | Feminino | "Conversão de Texto em Fala do Microsoft Server (en-US, Jessa24kRUS)"
pt-BR  | Inglês (EUA) | Masculino   | "Conversão de Texto em Fala do Microsoft Server (en-US, Guy24kRUS)"

Uma lista completa das vozes disponíveis está disponível em [Idiomas compatíveis](language-support.md#text-to-speech).

### <a name="request-headers"></a>Cabeçalhos da solicitação

Os campos a seguir são enviados no cabeçalho da solicitação HTTP.

|Cabeçalho|Significado|
|------|-------|
|`Authorization`|Um token de autorização precedido pela palavra `Bearer`. Obrigatório. Consulte [Autenticação](#authentication).|
|`Content-Type`|O tipo do conteúdo de entrada: `application/ssml+xml`.|
|`X-Microsoft-OutputFormat`|O formato de áudio de saída. Consulte a próxima tabela.|
|`User-Agent`|Nome do aplicativo. Obrigatório. Precisa conter menos de 255 caracteres.|

Os formatos de saída de áudio disponíveis (`X-Microsoft-OutputFormat`) incorporam uma taxa de bits e uma codificação.

|||
|-|-|
`raw-16khz-16bit-mono-pcm`         | `raw-8khz-8bit-mono-mulaw`
`riff-8khz-8bit-mono-mulaw`     | `riff-16khz-16bit-mono-pcm`
`audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3`
`audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm`
`riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3`
`audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3`

> [!NOTE]
> Se sua voz selecionada e o formato de saída tiverem diferentes taxas de bits, o áudio é aumentado conforme necessário. Contudo, vozes de 24 kHz não são compatíveis com os formatos de saída `audio-16khz-16kbps-mono-siren` e `riff-16khz-16kbps-mono-siren`.

### <a name="request-body"></a>Corpo da solicitação

O texto a ser convertido em fala será enviado como o corpo de uma solicitação `POST` HTTP em texto sem formatação (ASCII ou UTF-8) ou no formato SSML [Speech Synthesis Markup Language](speech-synthesis-markup.md) (UTF-8). Solicitações de texto sem formatação usam a voz e o idioma padrão do serviço. Envie no formato SSML para usar uma voz diferente.

### <a name="sample-request"></a>Solicitação de exemplo

A solicitação HTTP a seguir usa um corpo SSML para escolher a voz. O corpo precisa ter menos de 1.000 caracteres.

```xml
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

### <a name="http-response"></a>Resposta HTTP

O status HTTP da resposta indica o êxito ou as condições de erro comuns.

Código HTTP|Significado|Possível motivo
-|-|-|
200|OK|A solicitação foi bem-sucedida. O corpo da resposta é um arquivo de áudio.
400 |Solicitação incorreta |Um parâmetro obrigatório está ausente, vazio ou nulo. Ou então, o valor passado como um parâmetro obrigatório ou opcional é inválido. Um problema comum é um cabeçalho que é muito longo.
401|Não Autorizado |A solicitação não foi autorizada. Verifique se a chave de assinatura ou o token são válidos e se estão na região correta.
413|Entidade de solicitação muito grande|A entrada de SSML tem mais de 1024 caracteres.
429|Número Excessivo de Solicitações|Você excedeu a cota ou a taxa de solicitações permitidas para a sua assinatura.
502|Gateway incorreto | Problema de rede ou do servidor. Também pode indicar cabeçalhos inválidos.

Se o status HTTP for `200 OK`, o corpo da resposta conterá um arquivo de áudio no formato solicitado. Esse arquivo pode ser reproduzido enquanto é transferido ou salvo em um buffer ou arquivo para reprodução posterior ou outros usos.

## <a name="authentication"></a>Autenticação

Enviar uma solicitação à API REST do serviço de Fala requer uma chave de assinatura ou um token de acesso. Em geral, é mais fácil enviar a chave de assinatura diretamente. O serviço de Fala obtém o token de acesso para você. Para minimizar o tempo de resposta, pode ser útil usar um token de acesso.

Para obter um token, apresente sua chave de assinatura para um ponto de extremidade `issueToken` de serviço de Fala regional, conforme mostrado na tabela a seguir. Use o ponto de extremidade que corresponda com sua região de assinatura.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

Cada token de acesso é válido por 10 minutos. É possível obter um novo token a qualquer momento. Se desejar, você poderá obter um token antes de cada solicitação de API REST de Fala. Para minimizar a latência e o tráfego de rede, é recomendável usar o mesmo token por nove minutos.

As seções a seguir mostram como obter um token e como usá-lo em uma solicitação.

### <a name="get-a-token-http"></a>Obter um token: HTTP

A seguir temos um exemplo de solicitação HTTP para obter um token. Substitua `YOUR_SUBSCRIPTION_KEY` por sua chave de assinatura do Serviço de Fala. Se sua assinatura não se encontra na região Oeste dos EUA, substitua o cabeçalho `Host` pelo nome de host da sua região.

```
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

O corpo da resposta para esta solicitação é o token de acesso no formato de Java Web Token (JWT).

### <a name="get-a-token-powershell"></a>Obter um token: PowerShell

O script do Windows PowerShell a seguir ilustra como obter um token de acesso. Substitua `YOUR_SUBSCRIPTION_KEY` por sua chave de assinatura do Serviço de Fala. Se sua assinatura não se encontra na região Oeste dos EUA, altere o nome do host do URI especificado adequadamente.

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

### <a name="get-a-token-curl"></a>Obter um token: cURL

cURL é uma ferramenta de linha de comando no Linux (e no Subsistema do Windows para Linux). O comando cURL a seguir ilustra como obter um token de acesso. Substitua `YOUR_SUBSCRIPTION_KEY` por sua chave de assinatura do Serviço de Fala. Se sua assinatura não se encontra na região Oeste dos EUA, altere o nome do host do URI especificado adequadamente.

> [!NOTE]
> O comando é mostrado em várias linhas para melhor legibilidade, mas é preciso inseri-lo em uma única linha em um prompt de shell.

```
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
 -H "Content-type: application/x-www-form-urlencoded"
 -H "Content-Length: 0"
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

### <a name="get-a-token-c"></a>Obter um token: C#

A classe C# a seguir ilustra como obter um token de acesso. Passe sua chave de assinatura do serviço de Fala ao instanciar a classe. Se sua assinatura não se encontra na região Oeste dos EUA, altere o nome do host de `FetchTokenUri` adequadamente.

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

### <a name="use-a-token"></a>Usar um token

Para usar um token em uma solicitação de API REST, forneça-o no cabeçalho `Authorization`, após a palavra `Bearer`. Este é um exemplo de solicitação REST de Conversão de Texto em Fala que contém um token. Substitua `YOUR_ACCESS_TOKEN` pelo seu token real. Use o nome de host correto no cabeçalho `Host`.

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

### <a name="renew-authorization"></a>Renovar autorização

O token de autorização expira após 10 minutos. Renove sua autorização obtendo um novo token antes que ele expire. Por exemplo, você pode obter um novo token após nove minutos.

O código C# a seguir é uma substituição imediata para a classe apresentada antes. A classe `Authentication` obtém automaticamente um novo token de acesso a cada minutos nove usando um temporizador. Essa abordagem garante que um token válido está sempre disponível enquanto o programa está em execução.

> [!NOTE]
> Em vez de usar um temporizador, você pode armazenar um carimbo de data/hora de quando o último token foi obtido. Dessa forma, você poderá solicitar um novo somente se o atual estiver prestes a expirar. Essa abordagem evita solicitar novos tokens desnecessariamente e pode ser mais adequada para programas que fazem solicitações de Fala ocasionais.

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
