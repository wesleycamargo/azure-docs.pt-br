---
title: Solução de problemas da API de Fala do Bing | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Como resolver problemas ao usar API de Fala do Bing.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX
ms.openlocfilehash: 532916106f62e0236b8dd53cf7988a648355aef4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991854"
---
# <a name="troubleshooting-bing-speech"></a>Solução de problemas da API de Fala do Bing

## <a name="error-http-403-forbidden"></a>Erro `HTTP 403 Forbidden`

Ao usar a API de reconhecimento de fala, o serviço retorna um erro `HTTP 403 Forbidden`.

### <a name="cause"></a>Causa

Esse erro geralmente é causado por problemas de autenticação. As solicitações de conexão sem o cabeçalho `Ocp-Apim-Subscription-Key` ou `Authorization` válido são rejeitadas pelo serviço com uma resposta `HTTP 403 Forbidden`.

Se estiver usando a chave de assinatura para autenticação, o motivo pode ser

- a chave de inscrição está ausente ou é inválida
- a cota de uso da chave de assinatura está excedida
- o campo `Ocp-Apim-Subscription-Key` não está configurado no cabeçalho da solicitação, quando a API REST é chamada

Se estiver usando o token de autorização para autenticação, os motivos a seguir poderão causar o erro.

- o cabeçalho `Authorization` está ausente na solicitação ao usar REST
- o token de autorização especificado no cabeçalho de autorização é inválido
- o token de autorização expirou. O token de acesso tem um prazo de 10 minutos

Para obter mais informações sobre autenticação, consulte a página [Autenticação](How-to/how-to-authentication.md).

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

#### <a name="verify-that-your-subscription-key-is-valid"></a>Verifique se a chave de assinatura é válida

É possível executar o comando a seguir para verificação. Observe a substituição de *YOUR_SUBSCRIPTION_KEY* pela sua própria chave de assinatura. Se a chave de assinatura estiver válida, você receberá na resposta o token de autorização como um JWT (Token Web JSON). Caso contrário, você receberá um erro em resposta.

> [!NOTE]
> Substitua `YOUR_SUBSCRIPTION_KEY` pela sua própria chave de assinatura.

# <a name="powershelltabpowershell"></a>[Powershell](#tab/Powershell)

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

O exemplo usa curl no Linux com bash. Se não estiver disponível na plataforma, talvez seja necessário instalar o curl. O exemplo deve funcionar também no Cygwin no Windows, Git Bash, zsh e em outros shells.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```
---

Certifique-se de usar a mesma chave de assinatura no aplicativo ou na solicitação REST utilizada acima.

#### <a name="verify-the-authorization-token"></a>Verificar o token de autorização

Esta etapa será necessária apenas se você usar o token de autorização para autenticação. Execute o comando a seguir para verificar se o token de autorização ainda é válido. O comando faz uma solicitação POST para o serviço e espera uma mensagem de resposta do serviço. Se você ainda receber um erro HTTP `403 Forbidden` verifique se o token de acesso está configurado corretamente e não expirou.

> [!IMPORTANT]
> O token tem um prazo de 10 minutos.
> [!NOTE]
> Substitua `YOUR_AUDIO_FILE` pelo caminho do arquivo de áudio gravado previamente e `YOUR_ACCESS_TOKEN` pelo token de autorização retornado na etapa anterior.

# <a name="powershelltabpowershell"></a>[Powershell](#tab/Powershell)

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

---

## <a name="error-http-400-bad-request"></a>Erro `HTTP 400 Bad Request`

Geralmente, isso ocorre porque o corpo da solicitação contém dados de áudio inválidos. Atualmente, há suporte apenas para arquivos WAV.

## <a name="error-http-408-request-timeout"></a>Erro `HTTP 408 Request Timeout`

O erro é mais provável porque nenhum dado de áudio é enviado para o serviço e o serviço retorna esse erro após o tempo limite. Para a API REST, os dados de áudio devem ser colocados no corpo da solicitação.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>O `RecognitionStatus` na resposta é `InitialSilenceTimeout`

Os dados de áudio geralmente são a causa do problema. Por exemplo,

- o áudio tem um tempo de silêncio longo no começo. O serviço interromperá o reconhecimento após alguns segundos e retornará `InitialSilenceTimeout`.
- o áudio usa o formato de codec sem suporte, fazendo com que os dados de áudio sejam tratados como silêncio.
