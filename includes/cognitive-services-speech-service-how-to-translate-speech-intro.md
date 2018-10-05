---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: ab4795325f733d15403b53f027daa4fb8c824cc6
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47185682"
---
<!-- N.B. no header, language-agnostic -->

O [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) dos Serviços Cognitivos da Microsoft fornece a maneira mais simples de usar a **tradução de fala** em seu aplicativo.
O SDK fornece a funcionalidade completa do serviço. O processo básico para executar a tradução de fala inclui as seguintes etapas:

1. Crie uma configuração de tradução de fala e forneça uma chave de assinatura do serviço de Fala (ou um token de autorização) e uma [região](~/articles/cognitive-services/speech-service/regions.md) como parâmetros. Altere a configuração conforme necessário. Por exemplo, você também pode configurar os idiomas de tradução de origem e de destino, bem como especificar se deseja uma saída de fala ou de texto.

1. Criar um reconhecedor de tradução da configuração de tradução de fala. Se você quiser que reconhece a partir de uma fonte diferente de seu microfone padrão (por exemplo, o fluxo de áudio ou o arquivo de áudio), forneça uma configuração de áudio.

1. Associa eventos para operação assíncrona, se você quiser. O reconhecedor chama então seus manipuladores de eventos quando ele tem resultados intermediários e finais, bem como um evento de síntese para a saída de áudio opcional. Caso contrário, o aplicativo receberá somente um resultado de transcrição final.

1. Iniciar reconhecimento. Para tradução única, use o método `RecognizeOnceAsync()`, que retorna a primeira expressão reconhecida. Para traduções de execução longa, use o método `StartContinuousRecognitionAsync()` e vincule os eventos para resultados de reconhecimento assíncrono.

Consulte os seguintes snippets de código para cenários de tradução de fala que usam o SDK de Fala.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
