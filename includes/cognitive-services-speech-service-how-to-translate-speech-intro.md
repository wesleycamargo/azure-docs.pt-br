---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: ee50a104a75d3cd5ff958bd49a1ff7010c5d5083
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43144388"
---
<!-- N.B. no header, language-agnostic -->

O [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) dos Serviços Cognitivos da Microsoft fornece a maneira mais simples de usar a **tradução de fala** em seu aplicativo.
O SDK fornece a funcionalidade completa do serviço. O processo básico para executar a tradução de fala inclui as seguintes etapas:

1. Crie um alocador de fala e forneça uma chave de assinatura do serviço de Fala ou um token de autorização e uma [região](~/articles/cognitive-services/speech-service/regions.md) como parâmetros.
   
1. Criar um reconhecedor de tradução da fábrica de fala. Você também configurar os idiomas de tradução de origem e de destino, bem como especificar se deseja uma saída de fala ou de texto. Há várias versões do reconhecedor de tradução com base na fonte de áudio que você está usa.

1. Associa eventos para operação assíncrona, se você quiser. O reconhecedor chama então seus manipuladores de eventos quando ele tem resultados intermediários e finais, bem como um evento de síntese para a saída de áudio opcional. Caso contrário, o aplicativo receberá somente um resultado de transcrição final.

1. Iniciar reconhecimento. Para tradução única, use o método `RecognizeAsync()`, que retorna a primeira expressão reconhecida. Para traduções de execução longa, use o método `StartContinuousRecognitionAsync()` e vincule os eventos para resultados de reconhecimento assíncrono.

Consulte os seguintes trechos de código para cenários de tradução de fala que usam o SDK de Fala.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
