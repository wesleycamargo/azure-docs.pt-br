---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 0a3f739bae3ec758c8e25d581d3e2b9feb4af5b1
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134164"
---
```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|Propriedade  |Padrão | DESCRIÇÃO |
|---------|---------|---------| 
|maxConcurrentCalls|16|O número máximo de chamadas simultâneas para o retorno de chamada que a bomba de mensagens deve iniciar. Por padrão, o tempo de execução do Functions processa várias mensagens simultaneamente. Para direcionar o tempo de execução para processar uma única fila ou mensagem de tópico de cada vez, defina `maxConcurrentCalls` como 1. | 
|prefetchCount|n/d|O PrefetchCount padrão que será usado pelo MessageReceiver subjacente.| 
|autoRenewTimeout|00:05:00|A duração máxima na qual o bloqueio de mensagem será renovado automaticamente.| 
