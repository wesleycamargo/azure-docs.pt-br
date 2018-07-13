---
title: Solução de problemas do SDK de Fala dos Serviços Cognitivos | Microsoft Docs
description: Solução de problemas do SDK de Fala dos Serviços Cognitivos
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: 16eaebcf9494ab57521068a9418ccf2ac7f5a8fe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364407"
---
# <a name="troubleshooting-speech-services-sdk"></a>Solução de problemas do SDK de Serviços de Fala

Este artigo fornece informações para ajudá-lo a resolver problemas que você pode ter ao usar o SDK de Fala.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Erro `WebSocket Upgrade failed with an authentication error (403).`

Você pode ter o ponto de extremidade incorreto para a região ou o serviço. Verifique o URI para garantir que ele está correto. Confira também a próxima seção, pois isso também pode ser um problema com a chave de assinatura ou o token de autorização.

## <a name="error-http-403-forbidden-or-error-http-401-unauthorized"></a>Erro `HTTP 403 Forbidden` ou erro `HTTP 401 Unauthorized`

Esse erro geralmente é causado por problemas de autenticação. As solicitações de conexão sem um cabeçalho `Ocp-Apim-Subscription-Key` ou `Authorization` válido são rejeitadas com o status 401 ou 403.

* Se estiver usando uma chave de assinatura para autenticação, a causa poderá ser:

    - a chave de assinatura está ausente ou é inválida
    - você excedeu a cota de uso da assinatura

* Se estiver usando um token de autorização para autenticação, a causa poderá ser:

    - o token de autorização é inválido
    - o token de autorização expirou

### <a name="validate-your-subscription-key"></a>Validar a chave de assinatura

Verifique para ter certeza de que você tem uma chave de assinatura válida executando um dos comandos abaixo.

> [!NOTE]
> Substitua `YOUR_SUBSCRIPTION_KEY` e `YOUR_REGION` por sua própria chave de assinatura e a região associada, respectivamente.

* PowerShell

    ```Powershell
    $FetchTokenHeader = @{
      'Content-type'='application/x-www-form-urlencoded'
      'Content-Length'= '0'
      'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
    }
    $OAuthToken = Invoke-RestMethod -Method POST -Uri https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader
    $OAuthToken
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0"
    ```

### <a name="validate-an-authorization-token"></a>Validar um token de autorização

Se você usar um token de autorização para autenticação, execute um dos comandos a seguir para verificar se o token de autorização ainda é válido. Os tokens são válidos por 10 minutos.

> [!NOTE]
> Substitua `YOUR_AUDIO_FILE` pelo caminho para o arquivo de áudio pré-gravado, `YOUR_ACCESS_TOKEN` pelo token de autorização retornado na etapa anterior e `YOUR_REGION` pela região correta.

* PowerShell

    ```Powershell
    $SpeechServiceURI =
    'https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US'
    
    # $OAuthToken is the authorization token returned by the token service.
    $RecoRequestHeader = @{
      'Authorization' = 'Bearer '+ $OAuthToken
      'Transfer-Encoding' = 'chunked'
      'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
    }
    
    # Read audio into byte array
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")
    
    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes
    
    # Show the result
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

---

## <a name="error-http-400-bad-request"></a>Erro `HTTP 400 Bad Request`

Esse erro normalmente ocorre quando o corpo da solicitação contém dados de áudio inválidos. Há suporte apenas para o formato `WAV`. Verifique também os cabeçalhos da solicitação para garantir que você está especificando um `Content-Type` e um `Content-Length` apropriados.

## <a name="error-http-408-request-timeout"></a>Erro `HTTP 408 Request Timeout`

O erro provavelmente ocorre porque nenhum dado de áudio está sendo enviado para o serviço. Esse erro também pode ser causado por problemas de rede.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>O `RecognitionStatus` na resposta é `InitialSilenceTimeout`

Os dados de áudio geralmente são a causa do problema. Por exemplo: 

* Há um longo trecho de silêncio no início do áudio. O serviço interromperá o reconhecimento após alguns segundos e retornará `InitialSilenceTimeout`.
* O áudio usa um formato de codec sem suporte, fazendo com que os dados de áudio sejam tratados como silêncio.

## <a name="next-steps"></a>Próximas etapas

* [Notas de versão](releasenotes.md)

