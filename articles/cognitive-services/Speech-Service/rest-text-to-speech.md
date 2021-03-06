---
title: Referência de API texto em fala (REST) - serviços de fala
titleSuffix: Azure Cognitive Services
description: Saiba como usar a API REST do texto em fala. Neste artigo, você aprenderá sobre opções de autorização, opções de consulta, como estruturar uma solicitação e receber uma resposta.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 71710cd940aad3a56dae6c19d4d52a5b141b3d80
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58660939"
---
# <a name="text-to-speech-rest-api"></a>API REST conversão de texto em fala

Os serviços de fala permitem [converter texto em fala sintetizada](#convert-text-to-speech) e [obter uma lista de vozes com suporte](#get-a-list-of-voices) para uma região usando um conjunto de APIs REST. Cada ponto de extremidade disponível é associado uma região. Para o ponto de extremidade/região que você planeja usar uma chave de assinatura é necessária.

A API REST de conversão de texto em fala é compatível com vozes neurais e padrão de conversão de texto em fala, cada uma delas compatível com um idioma e dialeto específicos, identificados pela localidade.

* Para obter uma lista completa de vozes, consulte [suporte para idioma](language-support.md#text-to-speech).
* Para obter informações sobre a disponibilidade regional, consulte [regiões](regions.md#text-to-speech).

> [!IMPORTANT]
> Os custos variam para vozes padrão, personalizadas e neurais. Para saber mais, consulte [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Antes de usar essa API, compreenda:

* A API REST de conversão de texto em voz requer um cabeçalho de autorização. Isso significa que você precisa concluir uma troca de tokens para acessar o serviço. Para obter mais informações, consulte [Autenticação](#authentication).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>Obter uma lista de vozes

O `voices/list` ponto de extremidade permite que você obtenha uma lista completa de vozes para um região/ponto de extremidade específico.

### <a name="regions-and-endpoints"></a>Regiões e endpoints

| Região | Ponto de extremidade |
|--------|----------|
| Leste da Austrália | https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Sul do Brasil | https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Canadá Central | https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Centro dos EUA | https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Ásia Oriental | https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Leste dos EUA | https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Leste dos EUA 2 | https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list |
| França Central | https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Centro da Índia | https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Leste do Japão | https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Coreia Central | https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Centro-Norte dos EUA | https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Norte da Europa | https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Centro-Sul dos Estados Unidos | https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Sudeste Asiático | https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Sul do Reino Unido | https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Europa Ocidental | https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Oeste dos EUA | https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list |
| Oeste dos EUA 2 | https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list |

### <a name="request-headers"></a>Cabeçalhos da solicitação

Esta tabela lista os cabeçalhos obrigatórios e opcionais para solicitações de texto em fala.

| Cabeçalho | DESCRIÇÃO | Obrigatório/Opcional |
|--------|-------------|---------------------|
| `Authorization` | Um token de autorização precedido pela palavra `Bearer`. Para obter mais informações, consulte [Autenticação](#authentication). | Obrigatório |

### <a name="request-body"></a>Corpo da solicitação

Um corpo não é necessário para `GET` solicitações para esse ponto de extremidade.

### <a name="sample-request"></a>Solicitação de exemplo

Esta solicitação requer apenas um cabeçalho de autorização.

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Authorization: Bearer [Base64 access_token]
```

### <a name="sample-response"></a>Resposta de exemplo

Essa resposta foi truncada para ilustrar a estrutura de uma resposta.

> [!NOTE]
> Disponibilidade de voz varia por região/ponto de extremidade.

```json
[
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
        "ShortName": "ar-EG-Hoda",
        "Gender": "Female",
        "Locale": "ar-EG"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)",
        "ShortName": "ar-SA-Naayf",
        "Gender": "Male",
        "Locale": "ar-SA"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)",
        "ShortName": "bg-BG-Ivan",
        "Gender": "Male",
        "Locale": "bg-BG"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)",
        "ShortName": "ca-ES-HerenaRUS",
        "Gender": "Female",
        "Locale": "ca-ES"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (cs-CZ, Jakub)",
        "ShortName": "cs-CZ-Jakub",
        "Gender": "Male",
        "Locale": "cs-CZ"
    },

    ...

]
```

### <a name="http-status-codes"></a>Códigos de status HTTP

O código de status HTTP para cada resposta indica sucesso ou erros comuns.

| Código de status HTTP | DESCRIÇÃO | Possível motivo |
|------------------|-------------|-----------------|
| 200 | OK | A solicitação foi bem-sucedida. |
| 400 | Solicitação incorreta | Um parâmetro obrigatório está ausente, vazio ou nulo. Ou então, o valor passado como um parâmetro obrigatório ou opcional é inválido. Um problema comum é um cabeçalho que é muito longo. |
| 401 | Não Autorizado | A solicitação não foi autorizada. Verifique se a chave de assinatura ou o token são válidos e se estão na região correta. |
| 429 | Número Excessivo de Solicitações | Você excedeu a cota ou a taxa de solicitações permitidas para a sua assinatura. |
| 502 | Gateway incorreto | Problema de rede ou do servidor. Também pode indicar cabeçalhos inválidos. |


## <a name="convert-text-to-speech"></a>Converter texto em fala

O `v1` ponto de extremidade permite que você converta usando texto em fala [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md).

### <a name="regions-and-endpoints"></a>Regiões e endpoints

Essas regiões são suportadas para text-to-speech usando a API REST. Certifique-se de selecionar o terminal que corresponde à sua região de assinatura.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>Cabeçalhos da solicitação

Esta tabela lista os cabeçalhos obrigatórios e opcionais para solicitações de texto em fala.

| Cabeçalho | DESCRIÇÃO | Obrigatório/Opcional |
|--------|-------------|---------------------|
| `Authorization` | Um token de autorização precedido pela palavra `Bearer`. Para obter mais informações, consulte [Autenticação](#authentication). | Obrigatório |
| `Content-Type` | Especifica o tipo de conteúdo para o texto fornecido. Aceita o valor: `application/ssml+xml`. | Obrigatório |
| `X-Microsoft-OutputFormat` | Especifica o formato de saída de áudio. Para obter uma lista completa dos valores aceitos, consulte [saídas de áudio](#audio-outputs). | Obrigatório |
| `User-Agent` | O nome do aplicativo. O valor fornecido deve ser menor que 255 caracteres. | Obrigatório |

### <a name="audio-outputs"></a>Saídas de áudio

Esta é uma lista de formatos de áudio suportados que são enviados em cada solicitação como o cabeçalho `X-Microsoft-OutputFormat`. Cada um incorpora um tipo de taxa de bits e codificação. Os serviços de fala dá suporte a 24 KHz, KHz 16, e saídas de áudio de 8 KHz.

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

Confira nossos guias de início rápido para obter exemplos específicos de idioma:

* [.NET Core, C#](quickstart-dotnet-text-to-speech.md)
* [Python](quickstart-python-text-to-speech.md)
* [Node.js](quickstart-nodejs-text-to-speech.md)

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
