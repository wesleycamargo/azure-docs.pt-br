---
title: O que é o serviço de Fala (versão prévia)?
description: 'O serviço de Fala, parte dos Serviços Cognitivos da Microsoft, une vários serviços de fala do Azure que estavam disponíveis anteriormente de forma separada: Fala do Bing (que inclui o reconhecimento de fala e a conversão de texto em fala), Fala Personalizada e Tradução de Fala.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 0cd6d984ac9329112aa388e8d8ee808d4c3e6227
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445627"
---
# <a name="what-is-the-speech-service-preview"></a>O que é o serviço de Fala (versão prévia)?

O serviço de Fala conta com as tecnologias usadas em outros produtos da Microsoft, incluindo o Cortana e o Microsoft Office.  Esse mesmo serviço está disponível para qualquer cliente como um serviço cognitivo. 

> [!NOTE]
> Atualmente, o serviço de Fala está em versão prévia pública. Volte aqui regularmente para conferir se há atualizações de documentação, exemplos de código adicionais e muito mais.

Com uma assinatura, nosso serviço de Fala unificado fornece aos desenvolvedores um jeito fácil de conferir aos seus aplicativos recursos incríveis habilitados para fala. Agora, seus aplicativos podem oferecer comando de voz, transcrição, ditado, sintetização de voz e tradução.

## <a name="speech-service-features"></a>Recursos do serviço de fala

|Função|DESCRIÇÃO|
|-|-|
|[Conversão de fala em texto](speech-to-text.md)| Transcreve transmissões de áudio em texto, o qual seu aplicativo pode aceitar como entrada. Também integra ao [Serviço Inteligente de Reconhecimento Vocal](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) para perceber a intenção nas declarações dos usuários.|
|[Conversão de texto em fala](text-to-speech.md)| Converte o texto sem formatação em uma fala que soa natural, e é entregue ao aplicativo em um arquivo de áudio. Há várias vozes disponíveis, variando em gênero ou sotaque, para vários idiomas com suporte. |
|[Tradução de fala](speech-translation.md)| Pode ser usada para traduzir uma transmissão de áudio quase em tempo real ou para processar uma fala gravada. |
|[SDK de Dispositivos de Fala](speech-devices-sdk.md)| Com a introdução do serviço de Fala unificado, a Microsoft e seus parceiros oferecem uma plataforma integrada de hardware/software otimizada para o desenvolvimento de dispositivos habilitados para fala |

## <a name="using-the-speech-service"></a>Usar o serviço de Fala

O serviço de Fala é disponibilizado de duas maneiras. [O SDK](speech-sdk.md) abstrai os detalhes dos protocolos de rede. A [API REST](rest-apis.md) funciona com qualquer linguagem de programação, mas não oferece todas as funções oferecidas pelo SDK.

|<br>Método|Fala<br>em Texto|Texto em<br>Fala|Fala<br>Tradução|<br>DESCRIÇÃO|
|-|-|-|-|-|
|[SDKs](speech-sdk.md)|SIM|Não |SIM|Bibliotecas para linguagens de programação específicas que simplificam o desenvolvimento.|
|[REST](rest-apis.md)|SIM|sim|Não |Uma API simples baseada em HTTP que facilita a adição de fala ao seu aplicativo.|

## <a name="next-steps"></a>Próximas etapas

Obtenha uma chave de assinatura de avaliação gratuita para o serviço de Fala.

> [!div class="nextstepaction"]
> [Experimente o serviço de Fala gratuitamente](get-started.md)
