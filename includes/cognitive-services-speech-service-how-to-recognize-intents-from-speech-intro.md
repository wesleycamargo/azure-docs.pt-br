---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/27/2018
ms.author: wolfma
ms.openlocfilehash: a4accb0846ec9cf5efc5ca5aa00c731edd41d4d4
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43143113"
---
<!-- N.B. no header, language-agnostic -->

O [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) dos Serviços Cognitivos da Microsoft proporciona uma maneira de reconhecer **intenções de fala** e é compatível com o [LUIS (Serviço Inteligente de Reconhecimento Vocal)](https://www.luis.ai/home) dos Serviços Cognitivos.

1. Criar uma fábrica de fala usando uma chave de assinatura do LUIS e uma [região](~/articles/cognitive-services/speech-service/regions.md#regions-for-intent-recognition) como parâmetros. A chave de assinatura LUIS é chamada de **chave do ponto de extremidade** na documentação do serviço. Não é possível usar a chave de criação do LUIS. (Veja a observação mais adiante nesta seção.)

1. Obter um reconhecedor de intenção da fábrica de fala. Um reconhecedor pode usar o microfone padrão do dispositivo, um fluxo de áudio ou áudio de um arquivo.

1. Obter o modelo de reconhecimento vocal baseado em sua **AppId**. Adicione as intenções de que você precisar. 

1. Associa eventos para operação assíncrona, se você quiser. Depois, o reconhecedor chama seus manipuladores de eventos quando há resultados intermediários e finais (incluindo intenções). Se você não associar os eventos, o aplicativo receberá somente um resultado de transcrição final.

1. Iniciar o reconhecimento de intenção. Para reconhecimento a ser realizado uma única vez, como reconhecimento de comando ou consulta, use o método `RecognizeAsync()`. Esse método retorna a primeira expressão reconhecida. Para o reconhecimento de execução longa, use o método `StartContinuousRecognitionAsync()`. Vincule os eventos para resultados de reconhecimento assíncronos.

Consulte os seguintes trechos de código para cenários de reconhecimento de intenção que usam o SDK de Fala. Substitua os valores no exemplo por sua chave de assinatura (chave de ponto de extremidade) do LUIS, a [região da assinatura](~/articles/cognitive-services/speech-service/regions.md#regions-for-intent-recognition) e a **AppId** do modelo de intenção.

> [!NOTE]
> Em contraste com outros serviços compatíveis com o SDK de Fala, o reconhecimento de intenção requer uma chave de assinatura específica (ponto de extremidade do LUIS). Para obter informações sobre a tecnologia de reconhecimento de intenção, consulte o [site do LUIS](https://www.luis.ai). Para obter informações sobre como adquirir a **chave do ponto de extremidade**, consulte [Criar uma chave de ponto de extremidade do LUIS](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-how-to-azure-subscription#create-luis-endpoint-key).
