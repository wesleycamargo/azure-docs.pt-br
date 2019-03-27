---
title: Práticas recomendadas ao usar a API do Detector de anomalias
description: Saiba mais sobre as práticas recomendadas ao detectar anomalias com a API do Detector de anomalias.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 467ac4e475a1c23e25b62c76cfbc959e7ed49465
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484025"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>Práticas recomendadas para usar a API do Detector de anomalias

A API do Detector de anomalias é um serviço de detecção de anomalias sem monitoração de estado. A precisão e o desempenho de seus resultados podem ser afetados por:

* Como seus dados de série temporal são preparados.
* Os parâmetros de API do Detector de anomalias que foram usados.
* O número de pontos de dados em sua solicitação de API. 

Use este artigo para saber mais sobre práticas recomendadas para usar a API de obtenção dos melhores resultados para seus dados. 

## <a name="data-preparation"></a>Preparação dos dados

A API do Detector de anomalias aceita a série temporal dados formatados em um objeto de solicitação JSON. Uma série de tempo pode ser qualquer dados numéricos registrados ao longo do tempo, em ordem sequencial. Você pode enviar o windows de seus dados de série temporal para o ponto de extremidade de API do Detector de anomalias para melhorar o desempenho da API. O número mínimo de pontos de dados, que você pode enviar é 12, e o máximo é 8640 pontos. 

Pontos de dados enviados para a API do Detector de anomalias devem ter um carimbo de hora válidos Tempo Universal Coordenado (UTC) e valor numérico. 

```json
{
    "granularity": "daily",
    "series": [
      {
        "timestamp": "2018-03-01T00:00:00Z",
        "value": 32858923
      },
      {
        "timestamp": "2018-03-02T00:00:00Z",
        "value": 29615278
      },
    ]
}
```

### <a name="missing-data-points"></a>Pontos de dados ausentes

Pontos de dados ausentes são comuns em conjuntos de dados de série tempo uniformemente distribuído, especialmente aqueles com uma granularidade fina (um intervalo de amostragem pequeno. Por exemplo, os dados amostrados alguns minutos). Tem menos de 10% do número esperado de pontos em seus dados não deve ter um impacto negativo em seus resultados de detecção. Considere preenchendo as lacunas em seus dados com base em suas características, como substituição de pontos de dados de um período anterior, uma interpolação linear ou uma média móvel.

### <a name="aggregate-distributed-data"></a>Agregação de dados distribuídos

A API do Detector de anomalias funciona melhor em uma série de tempo uniformemente distribuído. Se seus dados são distribuídos aleatoriamente, você deve agregá-la por uma unidade de tempo, como por minuto, hora ou diariamente por exemplo.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>Detecção de anomalias em dados com padrões sazonais

Se você souber que seus dados de série temporal tem um padrão sazonal (aquela que ocorre em intervalos regulares), você pode melhorar a precisão e tempo de resposta de API. 

Especificando um `period` ao construir sua solicitação JSON pode reduzir a latência de detecção de anomalias em até 50%. O `period` é um inteiro que especifica a série temporal de pontos de dados aproximadamente quantas necessárias para repetir um padrão. Por exemplo, uma série de tempo com um ponto de dados por dia seria ter uma `period` como `7`, e uma série de tempo com um ponto por hora (com o mesmo padrão semanal) teria um `period` de `7*24`. Se você não tiver certeza dos padrões dos seus dados, você não precisa especificar esse parâmetro.

Para obter melhores resultados, forneça 4 `period`da partir do ponto de dados, mais um adicional. Por exemplo, os dados por hora com um padrão semanal conforme descrito acima devem fornecer 673 pontos de dados no corpo da solicitação (`7 * 24 * 4 + 1`).

### <a name="sampling-data-for-real-time-monitoring"></a>Dados de amostragem para monitoramento em tempo real

Se os dados de streaming são amostrados em um curto intervalo (por exemplo, segundos ou minutos), enviar o número recomendado de pontos de dados pode exceder máximo número permitido (8640 pontos de dados) a API Detector de anomalias. Se seus dados mostram um padrão sazonal estável, considere o envio de uma amostra de seus dados de série temporal em um intervalo de tempo maior, como horas. Amostragem dos dados dessa forma, também visivelmente pode melhorar o tempo de resposta de API. 

## <a name="next-steps"></a>Próximas etapas

* [O que é a API do Detector de anomalias?](../overview.md)
* [Início Rápido: Detectar anomalias em seus dados de série temporal usando a API de REST do Detector de anomalias](../quickstarts/detect-data-anomalies-csharp.md)
