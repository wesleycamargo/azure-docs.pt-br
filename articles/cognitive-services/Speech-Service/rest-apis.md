---
title: APIs REST dos Serviços de Fala – Serviços de Fala
titleSuffix: Azure Cognitive Services
description: Aprenda a usar as APIs REST de fala para texto e text-to-speech. Neste artigo, você aprenderá sobre opções de autorização, opções de consulta, como estruturar uma solicitação e receber uma resposta.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: f369ab0ec8c460137f7e2b16a7f2696357d84c50
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247435"
---
# <a name="speech-service-rest-apis"></a>APIs REST de serviço de fala

Como uma alternativa para o [Speech SDK](speech-sdk.md), o Serviço de Fala permite que você converta fala para texto e texto para voz com um conjunto de APIs REST. Cada terminal acessível está associado a uma região. Seu aplicativo requer uma chave de assinatura para o endpoint que você planeja usar.

Antes de usar as APIs REST, entenda:
* As solicitações de fala para texto que usam a API REST podem conter apenas 10 segundos de áudio gravado.
* A API REST de fala para texto só retorna os resultados finais. Resultados parciais não são fornecidos.
* A API REST de conversão de texto em voz requer um cabeçalho de autorização. Isso significa que você precisa concluir uma troca de tokens para acessar o serviço. Para obter mais informações, consulte [Autenticação](#authentication).

## <a name="authentication"></a>Autenticação

Cada solicitação para a API REST de fala para texto ou texto para voz requer um cabeçalho de autorização. Esta tabela ilustra quais cabeçalhos são suportados para cada serviço:

| Cabeçalhos de autorização suportados | Conversão de fala em texto | Conversão de texto em fala |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | SIM | Não  |
| Autorização: Portador | SIM | SIM |

Ao usar o cabeçalho `Ocp-Apim-Subscription-Key`, você só precisa fornecer sua chave de assinatura. Por exemplo: 

```
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

Ao usar o cabeçalho `Authorization: Bearer`, você precisa fazer uma solicitação ao `issueToken` endpoint. Nesta solicitação, você troca sua chave de assinatura por um token de acesso válido por 10 minutos. Nas próximas seções, você aprenderá como obter um token, usar um token e atualizar um token.

### <a name="how-to-get-an-access-token"></a>Como obter um token de acesso

Para obter um token de acesso, você precisará fazer uma solicitação para o endpoint `issueToken` usando a `Ocp-Apim-Subscription-Key` e sua chave de assinatura.

Essas regiões e nós de extremidade são suportados:

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

Use essas amostras para criar sua solicitação de token de acesso.

#### <a name="http-sample"></a>Exemplo de HTTP

Este exemplo é uma solicitação HTTP simples para obter um token. Substitua `YOUR_SUBSCRIPTION_KEY` pela sua chave de assinatura do serviço de Fala. Se sua assinatura não se encontra na região Oeste dos EUA, substitua o cabeçalho `Host` pelo nome de host da sua região.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

O corpo da resposta contém o token de acesso no formato Java Web Token (JWT).

#### <a name="powershell-sample"></a>Exemplo do PowerShell

Este exemplo é um script simples do PowerShell para obter um token de acesso. Substitua `YOUR_SUBSCRIPTION_KEY` pela sua chave de assinatura do serviço de Fala. Certifique-se de usar o endpoint correto para a região que corresponde à sua assinatura. Este exemplo está atualmente definido para o oeste dos EUA.

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

#### <a name="curl-sample"></a>exemplo de cURL

cURL é uma ferramenta de linha de comando no Linux (e no Subsistema do Windows para Linux). Este comando cURL ilustra como obter um token de acesso. Substitua `YOUR_SUBSCRIPTION_KEY` pela sua chave de assinatura do serviço de Fala. Certifique-se de usar o endpoint correto para a região que corresponde à sua assinatura. Este exemplo está atualmente definido para o oeste dos EUA.

```cli
curl -v -X POST
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>Exemplo de C#

Esta classe C# ilustra como obter um token de acesso. Passar sua chave de assinatura do serviço de Fala quando você instanciar a classe. Se a sua assinatura não estiver na região Oeste dos EUA, altere o valor de `FetchTokenUri` para que corresponda à região da sua assinatura.

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

### <a name="how-to-use-an-access-token"></a>Como usar um token de acesso

O token de acesso deve ser enviado para o serviço como o cabeçalho `Authorization: Bearer <TOKEN>`. Cada token de acesso é válido por 10 minutos. Você pode obter um novo token a qualquer momento, no entanto, para minimizar o tráfego de rede e a latência, recomendamos usar o mesmo token por nove minutos.

Aqui está um exemplo de solicitação HTTP para a API REST de conversão de texto em fala:

```http
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

### <a name="how-to-renew-an-access-token-using-c"></a>Como renovar um token de acesso usando C#

Este código C# é um substituto para a classe apresentada anteriormente. A classe `Authentication` recebe automaticamente um novo token de acesso a cada nove minutos usando um timer. Essa abordagem garante que um token válido está sempre disponível enquanto o programa está em execução.

> [!NOTE]
> Em vez de usar um temporizador, você pode armazenar um carimbo de data/hora de quando o último token foi obtido. Dessa forma, você poderá solicitar um novo somente se o atual estiver prestes a expirar. Essa abordagem evita solicitar novos tokens desnecessariamente e pode ser mais adequada para programas que fazem solicitações de Fala ocasionais.

Como antes, certifique-se de que o valor `FetchTokenUri` corresponda com sua região de assinatura. Passe sua chave de assinatura ao instanciar a classe.

```cs
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

## <a name="speech-to-text-api"></a>API de Fala para Texto

A API REST de fala para texto suporta apenas breves declarações. As solicitações podem conter até 10 segundos de áudio com duração total de 14 segundos. A API REST só retorna os resultados finais, não os resultados parciais ou intermediários.

Se o envio de áudio mais longo é um requisito para o seu aplicativo, considere usar o [SDK de Fala](speech-sdk.md) ou [transcrição do lote](batch-transcription.md).

### <a name="regions-and-endpoints"></a>Regiões e endpoints

Essas regiões são suportadas para transcrição de fala para texto usando a API REST. Certifique-se de selecionar o terminal que corresponde à sua região de assinatura.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="query-parameters"></a>Parâmetros de consulta

Esses parâmetros podem ser incluídos na string de consulta da solicitação REST.

| Parâmetro | DESCRIÇÃO | Obrigatório/Opcional |
|-----------|-------------|---------------------|
| `language` | Identifica a linguagem falada que está sendo reconhecida. Confira os [Idiomas compatíveis](language-support.md#speech-to-text). | Obrigatório |
| `format` | Especifica o formato do resultado. Os valores aceitos são `simple` e `detailed`. Resultados simples incluem `RecognitionStatus`, `DisplayText`, `Offset` e `Duration`. As respostas detalhadas incluem vários resultados com valores de confiança e quatro representações diferentes. A configuração padrão é `simple`. | Opcional |
| `profanity` | Especifica como lidar com palavrões em resultados de reconhecimento. Os valores aceitos são `masked`, o que substitui a profanidade por asteriscos, `removed`, que remove todas as profanações do resultado, ou `raw`, o que inclui a profanidade no resultado. A configuração padrão é `masked`. | Opcional |

### <a name="request-headers"></a>Cabeçalhos da solicitação

Esta tabela lista cabeçalhos obrigatórios e opcionais para solicitações de fala para texto.

|Cabeçalho| DESCRIÇÃO | Obrigatório/Opcional |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | Sua chave de assinatura do serviço de Fala. | Esse cabeçalho ou `Authorization` é obrigatório. |
| `Authorization` | Um token de autorização precedido pela palavra `Bearer`. Para obter mais informações, consulte [Autenticação](#authentication). | Esse cabeçalho ou `Ocp-Apim-Subscription-Key` é obrigatório. |
| `Content-type` | Descreve o formato e o codec dos dados de áudio fornecidos. Os valores aceitos são `audio/wav; codecs=audio/pcm; samplerate=16000` e `audio/ogg; codecs=opus`. | Obrigatório |
| `Transfer-Encoding` | Especifica que os dados de áudio em partes estão sendo enviados, em vez de um único arquivo. Use este cabeçalho somente se agrupar dados de áudio. | Opcional |
| `Expect` | Se usar transferência em partes, envie `Expect: 100-continue`. O serviço de Fala reconhece a solicitação inicial e aguarda dados adicionais.| Necessário se enviar dados de áudio em partes. |
| `Accept` | Se fornecido, deve ser `application/json`. O serviço de Fala fornece resultados em JSON. Algumas estruturas de solicitação da Web fornecem um valor padrão incompatível se você não especificar uma, portanto, é uma boa prática incluir sempre `Accept`. | Opcional, mas recomendado. |

### <a name="audio-formats"></a>Formatos de áudio

O áudio é enviado no corpo da solicitação HTTP `POST`. Ele deve estar em um dos formatos nesta tabela:

| Formatar | Codec | Bitrate | Taxa de amostragem |
|--------|-------|---------|-------------|
| WAV | PCM | 16-bit | 16 kHz, mono |
| OGG | OPUS | 16-bit | 16 kHz, mono |

>[!NOTE]
>Os formatos acima têm suporte por meio da API REST e WebSocket no serviço de fala. O [Speech SDK](speech-sdk.md) atualmente dá suporte a apenas o WAV de formato com o codec PCM.

### <a name="sample-request"></a>Solicitação de exemplo

Esta é uma solicitação HTTP típica. O exemplo abaixo inclui o nome do host e os cabeçalhos necessários. É importante observar que o serviço também espera dados de áudio, o que não está incluído nesta amostra. Como mencionado anteriormente, o chunking é recomendado, no entanto, não é obrigatório.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="http-status-codes"></a>Códigos de status HTTP

O código de status HTTP para cada resposta indica sucesso ou erros comuns.

| Código de status HTTP | DESCRIÇÃO | Possível motivo |
|------------------|-------------|-----------------|
| 100 | Continue | A solicitação inicial foi aceita. Continue enviando o restante dos dados. (Usado com transferência em partes.) |
| 200 | OK | A solicitação foi bem-sucedida. O corpo da resposta é um objeto JSON. |
| 400 | Solicitação incorreta | O código de idioma não foi fornecido ou o idioma não tem suporte. Arquivo de áudio inválido. |
| 401 | Não Autorizado | Chave de assinatura ou token de autorização inválido na região especificada, ou ponto de extremidade inválido. |
| 403 | Proibido | Chave de assinatura ou token de autorização ausente. |

### <a name="chunked-transfer"></a>Transferência em partes

A transferência em partes (`Transfer-Encoding: chunked`) pode ajudar a reduzir a latência de reconhecimento, pois permite que o serviço de Fala inicie o processamento do arquivo de áudio enquanto está sendo transmitido. A API REST não fornece resultados parciais ou provisórios. Essa opção destina-se somente a melhorar a capacidade de resposta.

Este exemplo de código mostra como enviar áudio em blocos. Apenas o primeiro bloco deve conter o cabeçalho do arquivo de áudio. `request` é um objeto HTTPWebRequest conectado ao ponto de extremidade REST apropriado. `audioFile` é o caminho para um arquivo de áudio em disco.

```csharp

    HttpWebRequest request = null;
    request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
    request.SendChunked = true;
    request.Accept = @"application/json;text/xml";
    request.Method = "POST";
    request.ProtocolVersion = HttpVersion.Version11;
    request.Host = host;
    request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
    request.Headers["Ocp-Apim-Subscription-Key"] = args[1];
    request.AllowWriteStreamBuffering = false;

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

### <a name="response-parameters"></a>Parâmetros de resposta

Os resultados são fornecidos como JSON. O `simple` formato inclui esses campos de nível superior.

| Parâmetro | DESCRIÇÃO  |
|-----------|--------------|
|`RecognitionStatus`|Status, como `Success` para reconhecimento bem-sucedido. Consulte a próxima tabela.|
|`DisplayText`|O texto reconhecido após o uso de maiúsculas, a pontuação, a normalização de texto inverso (conversão de texto falado em formas mais curtas, como 200 para “duzentos” ou “Dr. Rodrigues” para “doutor Rodrigues”) e o mascaramento de conteúdo ofensivo. Apresentar somente em caso de êxito.|
|`Offset`|O tempo (em unidades de 100 nanossegundos) no qual a fala reconhecida começa no fluxo de áudio.|
|`Duration`|A duração (em unidades de 100 nanossegundos) da fala reconhecida no fluxo de áudio.|

O `RecognitionStatus` campo pode conter estes valores:

| Status | DESCRIÇÃO |
|--------|-------------|
| `Success` | O reconhecimento foi bem-sucedido e o campo `DisplayText` está presente. |
| `NoMatch` | A fala foi detectada no fluxo de áudio, mas nenhuma palavra do idioma de destino foi combinada. Normalmente, isso significa que o idioma do reconhecimento é um idioma diferente daquele que o usuário está falando. |
| `InitialSilenceTimeout` | O início do fluxo de áudio continha apenas silêncio e o serviço atingiu o tempo limite aguardando pela fala. |
| `BabbleTimeout` | O início do fluxo de áudio continha apenas ruído e o serviço atingiu o tempo limite aguardando pela fala. |
| `Error` | O serviço de reconhecimento encontrou um erro interno e não foi possível continuar. Tente novamente, se possível. |

> [!NOTE]
> Se o áudio consistir apenas em conteúdo ofensivo e o parâmetro de consulta `profanity` estiver definido como `remove`, o serviço não retornará um resultado de fala.

O formato `detailed` inclui os mesmos dados que o formato `simple`, juntamente com `NBest`, uma lista de interpretações alternativas do mesmo resultado de reconhecimento de fala. Estes resultados são classificados de mais prováveis para menos prováveis A primeira entrada é a mesma que o resultado principal de reconhecimento.  Ao usar o formato `detailed`, `DisplayText` é fornecido como `Display` para cada resultado na lista `NBest`.

Cada objeto no `NBest` lista inclui:

| Parâmetro | DESCRIÇÃO |
|-----------|-------------|
| `Confidence` | A pontuação de confiança da entrada de 0,0 (nenhuma confiança) a 1,0 (confiança total) |
| `Lexical` | O formato lexical do texto reconhecido: as palavras reais reconhecidas. |
| `ITN` | O formato de texto inverso normalizado (“canônico”) do texto reconhecido, com números de telefone, números, abreviações (“doutor Rodrigues” para “dr Rodrigues”) e demais transformações aplicadas. |
| `MaskedITN` | O formato ITN com mascaramento de conteúdo ofensivo aplicado se solicitado. |
| `Display` | O formato de exibição do texto reconhecido, com a pontuação e uso de maiúsculas adicionados. Este parâmetro é o mesmo que `DisplayText` fornecido quando o formato é definido como `simple`. |

### <a name="sample-responses"></a>Respostas de exemplo

Essa é uma resposta típica para o reconhecimento`simple`.

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Essa é uma resposta típica para o reconhecimento`detailed`.

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

## <a name="text-to-speech-api"></a>API de texto em fala

A API REST de conversão de texto em fala é compatível com vozes neurais e padrão de conversão de texto em fala, cada uma delas compatível com um idioma e dialeto específicos, identificados pela localidade.

* Para obter uma lista completa de vozes, consulte [suporte para idioma](language-support.md#text-to-speech).
* Para obter informações sobre a disponibilidade regional, consulte [regiões](regions.md#text-to-speech).

> [!IMPORTANT]
> Os custos variam para vozes padrão, personalizadas e neurais. Para saber mais, consulte [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="request-headers"></a>Cabeçalhos da solicitação

Esta tabela lista cabeçalhos obrigatórios e opcionais para solicitações de fala para texto.

| Cabeçalho | DESCRIÇÃO | Obrigatório/Opcional |
|--------|-------------|---------------------|
| `Authorization` | Um token de autorização precedido pela palavra `Bearer`. Para obter informações adicionais, consulte [Autenticação](#authentication). | Obrigatório |
| `Content-Type` | Especifica o tipo de conteúdo para o texto fornecido. Aceita o valor: `application/ssml+xml`. | Obrigatório |
| `X-Microsoft-OutputFormat` | Especifica o formato de saída de áudio. Para obter uma lista completa dos valores aceitos, consulte [saídas de áudio](#audio-outputs). | Obrigatório |
| `User-Agent` | O nome do aplicativo. Deve ter menos de 255 caracteres. | Obrigatório |

### <a name="audio-outputs"></a>Saídas de áudio

Esta é uma lista de formatos de áudio suportados que são enviados em cada solicitação como o cabeçalho `X-Microsoft-OutputFormat`. Cada um incorpora um tipo de taxa de bits e codificação. O Serviço de Fala dá suporte a saídas de áudio de 24, 16 e 8 KHz.

|||
|-|-|
| `raw-16khz-16bit-mono-pcm` | `raw-8khz-8bit-mono-mulaw` |
| `riff-8khz-8bit-mono-alaw` | `riff-8khz-8bit-mono-mulaw` |
| `riff-16khz-16bit-mono-pcm` | `audio-16khz-128kbitrate-mono-mp3` |
| `audio-16khz-64kbitrate-mono-mp3` | `audio-16khz-32kbitrate-mono-mp3` |
| `raw-24khz-16bit-mono-pcm` | `riff-24khz-16bit-mono-pcm` |
| `audio-24khz-160kbitrate-mono-mp3` | `audio-24khz-96kbitrate-mono-mp3` |
| `audio-24khz-48kbitrate-mono-mp3` | |

> [!NOTE]
> Se sua voz selecionada e o formato de saída tiverem diferentes taxas de bits, o áudio é aumentado conforme necessário. Contudo, vozes de 24 kHz não são compatíveis com os formatos de saída `audio-16khz-16kbps-mono-siren` e `riff-16khz-16kbps-mono-siren`.

### <a name="request-body"></a>Corpo da solicitação

O corpo de cada solicitação `POST` é enviada como [SSML (Speech Synthesis Markup Language)](speech-synthesis-markup.md). A SSML permite que você escolha o idioma e a voz da fala sintetizada retornada pelo serviço de conversão de texto em fala. Para obter uma lista completa de vozes compatíveis, confira [suporte para idioma](language-support.md#text-to-speech).

> [!NOTE]
> Se usar uma voz personalizada, o corpo de uma solicitação poderá ser enviado como texto sem formatação (ASCII ou UTF-8).

### <a name="sample-request"></a>Solicitação de exemplo

Esta solicitação HTTP usa SSML para especificar a voz e o idioma. O corpo não pode exceder 1.000 caracteres.

```http
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

### <a name="http-status-codes"></a>Códigos de status HTTP

O código de status HTTP para cada resposta indica sucesso ou erros comuns.

| Código de status HTTP | DESCRIÇÃO | Possível motivo |
|------------------|-------------|-----------------|
| 200 | OK | A solicitação foi bem-sucedida. O corpo da resposta é um arquivo de áudio. |
| 400 | Solicitação incorreta | Um parâmetro obrigatório está ausente, vazio ou nulo. Ou então, o valor passado como um parâmetro obrigatório ou opcional é inválido. Um problema comum é um cabeçalho que é muito longo. |
| 401 | Não Autorizado | A solicitação não foi autorizada. Verifique se a chave de assinatura ou o token são válidos e se estão na região correta. |
| 413 | Entidade de solicitação muito grande | A entrada de SSML tem mais de 1024 caracteres. |
| 429 | Número Excessivo de Solicitações | Você excedeu a cota ou a taxa de solicitações permitidas para a sua assinatura. |
| 502 | Gateway incorreto | Problema de rede ou do servidor. Também pode indicar cabeçalhos inválidos. |

Se o status HTTP for `200 OK`, o corpo da resposta conterá um arquivo de áudio no formato solicitado. Este arquivo pode ser reproduzido enquanto é transferido, salvo em um buffer ou salvo em um arquivo.

## <a name="next-steps"></a>Próximas etapas

- [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de linguagem](how-to-customize-language-model.md)
