---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 10/19/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 7d8773cc12b50382f6f300987ec6ce504cd238af
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50251254"
---
Controla o [recurso de amostragem no Application Insights](../articles/azure-functions/functions-monitoring.md#configure-sampling).

```json
{
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

|Propriedade  |Padrão | DESCRIÇÃO |
|---------|---------|---------| 
|isEnabled|verdadeiro|Habilita ou desabilita a amostragem.| 
|maxTelemetryItemsPerSecond|5|O limite em que a amostragem começa.| 