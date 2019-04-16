---
title: Webhooks - serviços de fala
titlesuffix: Azure Cognitive Services
description: Webhooks são ideais para otimizar sua solução ao lidar com longa retornos de chamada do HTTP processos como importações, adaptação, testes de precisão ou transcrições de arquivos de execução longa em execução.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 7b47d4fc3aa4a1a50e441e668a856703c67045ae
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2019
ms.locfileid: "59581001"
---
# <a name="webhooks-for-speech-services"></a>Webhooks para serviços de fala

Webhooks são como retornos de chamada HTTP que permitem que o aplicativo para aceitar dados dos serviços de fala quando ele estiver disponível. Usando webhooks, você pode otimizar o uso de nossas APIs REST, eliminando a necessidade de fazer pesquisas continuamente por uma resposta. As próximas seções, você aprenderá como usar webhooks com os serviços de fala.

## <a name="supported-operations"></a>Operações com suporte

Os serviços de fala oferecem suporte a webhooks para todas as operações de longa execução. Cada uma das operações listadas abaixo pode disparar um retorno de chamada HTTP após a conclusão. 

* DataImportCompletion
* ModelAdaptationCompletion
* AccuracyTestCompletion
* TranscriptionCompletion
* EndpointDeploymentCompletion
* EndpointDataCollectionCompletion

Em seguida, vamos criar um webhook.

## <a name="create-a-webhook"></a>Criar um webhook

Vamos criar um webhook para uma transcrição offline. O cenário: um usuário tem um arquivo de áudio de longa execução que gostaria de transcrição de forma assíncrona com a API de transcrição de lote. 

Parâmetros de configuração para a solicitação são fornecidos como JSON:

```json
{
  "configuration": {
    "url": "https://your.callback.url/goes/here",
    "secret": "<my_secret>"
  },
  "events": [
    "TranscriptionCompletion"
  ],
  "active": true,
  "name": "TranscriptionCompletionWebHook",
  "description": "This is a Webhook created to trigger an HTTP POST request when my audio file transcription is completed.",
  "properties": {
      "Active" : "True"
  }

}
```
Todas as solicitações POST para a API de transcrição de lote exigem um `name`. O `description` e `properties` parâmetros são opcionais.

O `Active` propriedade é usada para alternar a chamada de retorno para a URL de logon e logoff sem precisar excluir e recriar o registro do webhook. Se você apenas precisa retornar a chamada uma vez depois que o processo foi concluído, em seguida, excluir o webhook e o comutador de `Active` propriedade como false.

O tipo de evento `TranscriptionCompletion` é fornecido na matriz de eventos. Ele chamará novamente para seu ponto de extremidade quando uma transcrição entra em um estado terminal (`Succeeded` ou `Failed`). Ao chamar de volta para a URL de registrado, a solicitação conterá um `X-MicrosoftSpeechServices-Event` cabeçalho que contém um dos tipos de evento registrados. Há uma solicitação por tipo de evento registrado. 

Há um tipo de evento que você não pode assinar. É o `Ping` tipo de evento. Uma solicitação com esse tipo é enviada para a URL quando terminar de criar um webhook ao usar a URL de ping (veja abaixo).  

Na configuração, o `url` propriedade é necessária. As solicitações POST são enviadas para esta URL. O `secret` é usado para criar um hash SHA256 da carga, com o segredo como uma chave HMAC. O hash é definido como o `X-MicrosoftSpeechServices-Signature` cabeçalho ao chamar de volta para a URL registrada. Esse cabeçalho é codificado na Base64.

Este exemplo ilustra como validar usando uma carga C#:

```csharp

private const string EventTypeHeaderName = "X-MicrosoftSpeechServices-Event";
private const string SignatureHeaderName = "X-MicrosoftSpeechServices-Signature";

[HttpPost]
public async Task<IActionResult> PostAsync([FromHeader(Name = EventTypeHeaderName)]WebHookEventType eventTypeHeader, [FromHeader(Name = SignatureHeaderName)]string signature)
{
    string body = string.Empty;
    using (var streamReader = new StreamReader(this.Request.Body))
    {
        body = await streamReader.ReadToEndAsync().ConfigureAwait(false);
        var secretBytes = Encoding.UTF8.GetBytes("my_secret");
        using (var hmacsha256 = new HMACSHA256(secretBytes))
        {
            var contentBytes = Encoding.UTF8.GetBytes(body);
            var contentHash = hmacsha256.ComputeHash(contentBytes);
            var storedHash = Convert.FromBase64String(signature);
            var validated = contentHash.SequenceEqual(storedHash);
        }
    }
 
    switch (eventTypeHeader)
    {
        case WebHookEventType.Ping:
            // Do your ping event related stuff here (or ignore this event)
            break;
        case WebHookEventType.TranscriptionCompletion:
            // Do your subscription related stuff here.
            break;
        default:
            break;
    }
 
    return this.Ok();
}

```
Neste trecho de código, o `secret` é decodificada e validado. Você também observará que o tipo de evento do webhook foi alternado. Atualmente, há um evento por transcrição concluída. O código repete cinco vezes para cada evento (com um atraso de segundo um) antes de desistir.

### <a name="other-webhook-operations"></a>Outras operações de webhook

Para obter todos os webhooks: GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks

Para obter um webhook específico: GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

Para remover um webhook específico: DELETE https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

> [!Note] 
> No exemplo acima, a região é 'westus'. Isso deve ser substituído por região em que você criou seu recurso de serviços de fala no portal do Azure.

POST https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/ping corpo: vazio

Envia uma solicitação POST para a URL registrada. A solicitação contém um `X-MicrosoftSpeechServices-Event` cabeçalho com um ping de valor. Se o webhook foi registrado com um segredo, ele conterá uma `X-MicrosoftSpeechServices-Signature` cabeçalho com um hash SHA256 de carga com o segredo como chave HMAC. O hash é codificado na Base64. 

POST https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/test corpo: vazio

Envia uma solicitação POST para a URL registrada, se uma entidade para o tipo de evento assinado (transcrição) estiver presente no sistema e está no estado apropriado. A carga será gerada a partir da última entidade que seria ter invocado o gancho da web. Se nenhuma entidade estiver presente, a POSTAGEM responderá com 204. Se uma solicitação de teste pode ser feita, ele responderá com 200. O corpo da solicitação é da mesma forma como a solicitação GET para uma entidade específica, que assine o gancho da web para (por exemplo, transcrição). A solicitação terá o `X-MicrosoftSpeechServices-Event` e `X-MicrosoftSpeechServices-Signature` cabeçalhos conforme descrito anteriormente.

### <a name="run-a-test"></a>Executar um teste

Um teste rápido pode ser feito usando o site https://bin.webhookrelay.com. A partir daí, você pode obter chamada fazer URLs ser passado como parâmetro para o HTTP POST para criar um webhook descrito anteriormente neste documento.

## <a name="next-steps"></a>Próximos passos

* [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
