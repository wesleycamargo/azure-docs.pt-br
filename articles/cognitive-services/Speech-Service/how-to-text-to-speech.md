---
title: Use o Texto para Fala no Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Saiba como usar a Conversão de Texto em Fala no serviço de Fala.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 09/08/2018
ms.author: erhopf
ms.openlocfilehash: 162b690d4b371cfe76738cd83ce484a3062d139f
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49469904"
---
# <a name="use-text-to-speech-in-speech-service"></a>Usar "Conversão de Texto em Fala" no Serviço de Fala

O serviço de Fala fornece a funcionalidade Conversão de Texto em Fala por meio de uma solicitação HTTP simples. Você `POST` o texto a ser falado ao ponto de extremidade apropriado, e o serviço retorna um arquivo de áudio (`.wav`) que contém a fala sintetizada. Seu aplicativo pode usar este áudio como desejar.

O corpo da solicitação de postagem para a Conversão de Texto em Fala pode ser texto sem formatação (ASCII ou UTF8) ou um documento [SSML](speech-synthesis-markup.md). Solicitações de texto sem formatação são faladas com uma voz padrão. Na maioria dos casos, você deseja usar um corpo SSML. A solicitação HTTP deve incluir um token de [autorização](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#authentication).

Os pontos de extremidade de Conversão de Texto em Fala regionais são mostrados aqui. Use o apropriado para sua assinatura.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

## <a name="specify-a-voice"></a>Especificar uma voz

Para especificar uma voz, use a marca `<voice>` [SSML](speech-synthesis-markup.md). Por exemplo: 

```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
  <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Hello, world!
  </voice>
</speak>
```

Consulte [vozes de Conversão de Texto em Fala](language-support.md#text-to-speech) para uma lista de vozes disponíveis e seus nomes.

## <a name="make-a-request"></a>Fazer uma solicitação

Uma solicitação HTTP de Conversão de Texto em Fala é feita no modo POST com o texto a ser falado no corpo da solicitação. O comprimento máximo do corpo da solicitação HTTP é 1024 caracteres. A solicitação deve ter os seguintes cabeçalhos:

Cabeçalho|Valores|Comentários
-|-|-
|`Content-Type` | `application/ssml+xml` | O formato de texto de entrada.
|`X-Microsoft-OutputFormat`|     `raw-16khz-16bit-mono-pcm`<br>`riff-16khz-16bit-mono-pcm`<br>`raw-8khz-8bit-mono-mulaw`<br>`riff-8khz-8bit-mono-mulaw`<br>`audio-16khz-128kbitrate-mono-mp3`<br>`audio-16khz-64kbitrate-mono-mp3`<br>`audio-16khz-32kbitrate-mono-mp3`<br>`raw-24khz-16bit-mono-pcm`<br>`riff-24khz-16bit-mono-pcm`<br>`audio-24khz-160kbitrate-mono-mp3`<br>`audio-24khz-96kbitrate-mono-mp3`<br>`audio-24khz-48kbitrate-mono-mp3` | O formato de áudio de saída.
|`User-Agent`   |Nome do aplicativo | O nome do aplicativo é necessário e deve ter menos de 255 caracteres.
| `Authorization`   | O token de autorização obtido apresentando sua chave de assinatura para o serviço de token. Cada token é válido por 10 minutos. Consulte [APIs REST: autenticação](rest-apis.md#authentication).

> [!NOTE]
> Se sua voz selecionada e o formato de saída tiverem diferentes taxas de bits, o áudio é aumentado conforme necessário.

Um exemplo de solicitação é mostrado abaixo.

```xml
POST /cognitiveservices/v1
HTTP/1.1
Host: westus.tts.speech.microsoft.com
X-Microsoft-OutputFormat: riff-24khz-16bit-mono-pcm
Content-Type: application/ssml+xml
User-Agent: Test TTS application
Authorization: (authorization token)

<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Hello, world!
</voice> </speak>
```

O corpo da resposta com um status de 200 contém áudio no formato de saída especificado.

```
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

Se ocorrer um erro, os códigos de status abaixo são usados. O corpo da resposta para o erro também contém uma descrição do problema.

|Código|DESCRIÇÃO|Problema|
|-|-|-|
400 |Solicitação incorreta |Um parâmetro obrigatório está ausente, vazio ou nulo. Ou então, o valor passado como um parâmetro obrigatório ou opcional é inválido. Um problema comum é um cabeçalho que é muito longo.
401|Não Autorizado |A solicitação não foi autorizada. Verifique se a chave de assinatura ou o token é válido.
413|Entidade de solicitação muito grande|A entrada SSML é grande demais ou contém mais de 3 elementos `<voice>`.
429|Número Excessivo de Solicitações|Você excedeu a cota ou a taxa de solicitações permitidas para a sua assinatura.
|502|Gateway incorreto    | Problema de rede ou do servidor. Também pode indicar cabeçalhos inválidos.

Para obter mais informações sobre a API REST de Conversão de Texto em Fala, consulte [APIs REST](rest-apis.md#text-to-speech).

## <a name="next-steps"></a>Próximas etapas

- [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
- [Reconhecer fala em C++](quickstart-cpp-windows.md)
- [Reconhecer fala em C#](quickstart-csharp-dotnet-windows.md)
- [Reconhecer fala em Java](quickstart-java-android.md)
