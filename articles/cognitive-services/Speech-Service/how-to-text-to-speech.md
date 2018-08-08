---
title: Usar Conversão de Texto em Fala usando serviços de Fala
description: Saiba como usar a Conversão de Texto em Fala no serviço de Fala.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 21157af9ad6361234ffa9b927da64baab505f624
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39281507"
---
# <a name="use-text-to-speech-in-speech-service"></a>Usar "Conversão de Texto em Fala" no serviço de Fala

O serviço de Fala fornece a funcionalidade Conversão de Texto em Fala por meio de uma solicitação HTTP simples. Você escreve o texto a ser falado ao ponto de extremidade apropriado, e o serviço retorna um arquivo de áudio (`.wav`) que contém a fala sintetizada. Seu aplicativo pode usar este áudio como desejar.

O corpo da solicitação de postagem para a Conversão de Texto em Fala pode ser texto sem formatação (ASCII ou UTF8) ou um documento [SSML](speech-synthesis-markup.md). Solicitações de texto sem formatação são faladas com uma voz padrão. Na maioria dos casos, você deseja usar um corpo SSML. A solicitação HTTP deve incluir um token de [autorização](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#authentication). 

Os pontos de extremidade de Conversão de Texto em Fala regionais são mostrados aqui. Use o apropriado para sua assinatura.

[!include[](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

## <a name="specify-a-voice"></a>Especificar uma voz

Para especificar uma voz, use a marca `<voice>` [SSML](speech-synthesis-markup.md). Por exemplo: 

```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
  <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Hello, world!
  </voice>
</speak>
```

Consulte [vozes de Conversão de Texto em Fala](supported-languages.md#text-to-speech) para uma lista de vozes disponíveis e seus nomes.

## <a name="make-a-request"></a>Fazer uma solicitação

Uma solicitação HTTP de Conversão de Texto em Fala é feita no modo POST com o texto a ser falado no corpo da solicitação. O comprimento máximo do corpo da solicitação HTTP é 1024 caracteres. A solicitação deve ter os seguintes cabeçalhos: 

Cabeçalho|Valores|Comentários
-|-|-
|`Content-Type` | `application/ssml+xml` | O formato de texto de entrada.
|`X-Microsoft-OutputFormat`|     `raw-16khz-16bit-mono-pcm`<br>`audio-16khz-16kbps-mono-siren`<br>`riff-16khz-16kbps-mono-siren`<br>`riff-16khz-16bit-mono-pcm`<br>`audio-16khz-128kbitrate-mono-mp3`<br>`audio-16khz-64kbitrate-mono-mp3`<br>`audio-16khz-32kbitrate-mono-mp3`<br>`raw-24khz-16bit-mono-pcm`<br>`riff-24khz-16bit-mono-pcm`<br>`audio-24khz-160kbitrate-mono-mp3`<br>`audio-24khz-96kbitrate-mono-mp3`<br>`audio-24khz-48kbitrate-mono-mp3` | O formato de áudio de saída.
|`User-Agent`   |Nome do aplicativo | O nome do aplicativo é necessário e deve ter menos de 255 caracteres.
| `Authorization`   | O token de autorização obtido apresentando sua chave de assinatura para o serviço de token. Cada token é válido por 10 minutos. Consulte [APIs REST: autenticação](rest-apis.md#authentication).

> [!NOTE]
> Se sua voz selecionada e o formato de saída tiverem diferentes taxas de bits, o áudio é aumentado conforme necessário. Vozes 24khz não oferecem suporte aos formatos de saída `audio-16khz-16kbps-mono-siren` e `riff-16khz-16kbps-mono-siren`. 

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
413|Entidade de solicitação muito grande|A entrada de SSML tem mais de 1024 caracteres.
|502|Gateway incorreto    | Problema de rede ou do servidor. Também pode indicar cabeçalhos inválidos.

Para obter mais informações sobre a API REST de Conversão de Texto em Fala, consulte [APIs REST](rest-apis.md#text-to-speech).

## <a name="next-steps"></a>Próximas etapas

- [Obtenha sua assinatura de avaliação de Fala](https://azure.microsoft.com/try/cognitive-services/)
- [Reconhecer fala em C++](quickstart-cpp-windows.md)
- [Reconhecer fala em C#](quickstart-csharp-dotnet-windows.md)
- [Reconhecer fala em Java](quickstart-java-android.md)
