---
title: Serviço de Fala do Bing da Microsoft | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Use o API de Fala da Microsoft para adicionar ações controladas por voz para seus aplicativos, incluindo interação em tempo real com os usuários.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ms.openlocfilehash: 9b462ba594122b86580b9d136a844db070eea92d
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217638"
---
# <a name="what-is-bing-speech"></a>O que é a Fala do Bing?

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

A API de Fala do Bing da Microsoft baseada em nuvem fornece aos desenvolvedores uma forma fácil de criar recursos avançados habilitados para fala em seus aplicativos, como controle de comando de voz, diálogo do usuário usando conversa de fala natura e transcrição e ditado de fala. A API de Fala da Microsoft dá suporte a *Conversão de fala em texto* e *Texto em fala*.

- **A API de conversão de fala em texto** converte a fala humana em texto que pode ser usado como entrada ou comandos para controlar o seu aplicativo.
- **A API de Conversão de Texto em Fala**I converte o texto em fluxos de áudio que podem ser reproduzidos para o usuário do seu aplicativo.

## <a name="speech-to-text-speech-recognition"></a>Conversão de fala em texto (reconhecimento de fala)

A API de reconhecimento de fala da Microsoft *transcreve* transmissões por streaming em texto que o seu aplicativo pode exibir para o usuário ou atuar como entrada de comando. Ela fornece duas maneiras para os desenvolvedores adicionarem Fala aos seus aplicativos: APIs REST **ou** bibliotecas de clientes baseadas no WebSocket.

- [APIs REST](GetStarted/GetStartedREST.md): Os desenvolvedores podem usar chamadas HTTP em seus aplicativos para o serviço de reconhecimento de fala.
- [Bibliotecas de cliente](GetStarted/GetStartedClientLibraries.md): Para recursos avançados, os desenvolvedores podem baixar as bibliotecas de clientes de Fala da Microsoft e vinculá-las a seus aplicativos.  As bibliotecas de cliente estão disponíveis em várias plataformas (Windows, Android, iOS) usando diferentes linguagens (C#, Java, JavaScript, ObjectiveC). Diferentemente das APIs REST, as bibliotecas de cliente utilizam procotolo com base em Websocket.

| Casos de uso | [APIs REST](GetStarted/GetStartedREST.md) | [Bibliotecas de cliente](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Converter um áudio curto falado, por exemplo, comandos (tamanho do áudio < 15 s) sem resultados intermediários | SIM | SIM |
| Converter um áudio longo (> 15 s) | Não  | SIM |
| Fluxo de áudio desejado com resultados intermediários | Não  | SIM |
| Entender o texto convertido de áudio usando LUIS | Não  | SIM |

Qualquer que seja a abordagem que os desenvolvedores escolham (APIs REST ou bibliotecas de cliente), o serviço de fala Microsoft suporta o seguinte:

- Tecnologias avançadas de reconhecimento de fala da Microsoft que são usadas pelo Cortana Office Dictation, Office Translator e outros produtos da Microsoft.
- Reconhecimento contínuo em tempo real. A API de reconhecimento de fala permite aos usuários transcrever áudio em texto em tempo real e oferece suporte para receber os resultados intermediários de palavras que foram reconhecidas até o momento. O serviço de fala também oferece suporte à detecção final de fala. Além disso, os usuários também podem escolher recursos de formatação adicionais, como capitalização e pontuação, mascaramento de profanidade e normalização do texto.
- Suporta resultados de reconhecimento de fala otimizados para cenários *interativos*, *de conversa*, e *ditado*. Para cenários de usuário que requerem modelos personalizados e modelos acústicos, o [Serviço de Fala Personalizado](../custom-speech-service/cognitive-services-custom-speech-home.md) permite que você crie modelos de fala personalizado para seu aplicativo e seus usuários.
- Suporte para muitos idiomas falados em múltiplos dialetos. Para obter uma lista completa dos idiomas com suporte em cada modo de reconhecimento, consulte [Idiomas com reconhecimento](api-reference-rest/supportedlanguages.md).
- Integração com a compreensão de idioma. Além de converter o áudio de entrada em texto, a *conversão de fala em texto* fornece aos aplicativos uma capacidade adicional para entender o que o texto significa. Ele usa o [Serviço Inteligente de Reconhecimento Vocal (LUIS)](../LUIS/what-is-luis.md) para extrair as entidades e intenções do texto reconhecido.

### <a name="next-steps"></a>Próximas etapas

- Começar a usar o serviço de reconhecimento de fala da Microsoft com [APIs REST](GetStarted/GetStartedREST.md) ou [bibliotecas de cliente](GetStarted/GetStartedClientLibraries.md).
- Fazr check-out de [aplicativos de exemplo](samples.md) na linguagem de programação de sua preferência.
- Vá para a seção de referência para localizar os detalhes do [Protocolo de Fala da Microsoft](API-Reference-REST/websocketprotocol.md) e as referências de API.

## <a name="text-to-speech-speech-synthesis"></a>Conversão de texto em fala (síntese de fala)

As APIs*Conversão de Texto em Fala* usam REST para converter texto estruturado em um streaming de áudio. As APIs fornecem a conversão de texto em fala rápida em várias vozes e idiomas. Além disso, os usuários também têm a capacidade de alterar as características de áudio como pronúncia, volume, tom etc. usando marcas SSML.

### <a name="next-steps"></a>Próximas etapas

- Introdução ao uso do serviço de Conversão de Texto em Fala da Microsoft: [Referência da API de Conversão de Texto em Fala](api-reference-rest/bingvoiceoutput.md). Para obter a lista completa de idiomas e vozes suportadas pela Conversão de Texto em Fala, consulte [Locais compatíveis e fonte de voz](api-reference-rest/bingvoiceoutput.md#SupLocales).
