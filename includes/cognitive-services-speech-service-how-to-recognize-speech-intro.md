---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: 1103e5a217ca4972cc1c983a7ad0d07b0797e9e9
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43129300"
---
<!-- N.B. no header, no intents here, language-agnostic -->

O [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) dos Serviços Cognitivos fornece a maneira mais simples de usar a **Conversão de Fala em Texto** em seu aplicativo com a funcionalidade completa.

1. Crie um alocador de fala e forneça uma chave de assinatura do serviço de Fala (ou um token de autorização) e uma [região](~/articles/cognitive-services/speech-service/regions.md) como parâmetros. Você também pode fornecer um ponto de extremidade personalizado para especificar um ponto de extremidade de serviço não padrão.

1. Obtenha um reconhecedor de fala da fábrica de fala. Você pode configurar o idioma de entrada e o formato de saída. Um reconhecedor pode usar o microfone padrão do dispositivo, um fluxo de áudio ou áudio de um arquivo.

1. Associa eventos para operação assíncrona, se você quiser. Depois, o reconhecedor chama seus manipuladores de eventos quando houver resultados intermediários e finais. Caso contrário, o aplicativo receberá somente um resultado de transcrição final.

1. Iniciar reconhecimento. Para reconhecimento a ser realizado uma única vez, como reconhecimento de comando ou consulta, use o método `RecognizeAsync()`. Esse método retorna a primeira expressão reconhecida. Para o reconhecimento de execução longa, assim como transcrição, use o método `StartContinuousRecognitionAsync()`. Vincule os eventos para resultados de reconhecimento assíncronos.

Consulte os seguintes trechos de código para cenários de reconhecimento de fala que usam o SDK de Fala.

[!INCLUDE [Get a subscription key](cognitive-services-speech-service-get-subscription-key.md)]
