---
title: Moldagem de eventos da Versão Prévia do Azure Time Series Insights – formatar eventos com a Versão Prévia do Azure Time Series Insights | Microsoft Docs
description: Entenda como formatar eventos na Versão Prévia do Azure Time Series Insights.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: b0ce3b5bbd58c594147f64de9f06f248f4a87fc3
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269066"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Formatar eventos com Versão Prévia do Azure Time Series Insights

Este artigo ajuda você a formatar o arquivo JSON para maximizar a eficiência de suas consultas de Versão Prévia do Azure Time Series Insights.

## <a name="best-practices"></a>Práticas recomendadas

É importante considerar como enviar eventos para a Versão Prévia do Time Series Insights. Ou seja, você sempre deve:

* Envie dados pela rede de maneira mais eficiente possível.
* Armazene seus dados de uma maneira que o ajude a agregá-lo mais adequadamente para seu cenário.

Para o melhor desempenho de consulta possível, faça o seguinte:

* Não envie propriedades desnecessárias. A Versão Prévia do Time Series Insights cobra pelo seu uso. É melhor armazenar e processar os dados que você vai consultar.
* Use os campos de instância para dados estáticos. Essa prática ajuda a evitar o envio de dados estáticos pela rede. Campos de instância, um componente do modelo de série temporal, funcionam como dados de referência no serviço de GA do Time Series Insights. Para saber mais sobre campos de instância, confira [Modelos de Série Temporal](./time-series-insights-update-tsm.md).
* Compartilhe propriedades de dimensão entre dois ou mais eventos. Essa prática ajuda você a enviar dados pela rede com mais eficiência.
* Não use o aninhamento profundo de matriz. A Versão Prévia do Time Series Insights dá suporte a até dois níveis de matrizes aninhadas contendo objetos. A Versão Prévia do Time Series Insights nivela matrizes em mensagens em vários eventos com pares de valor da propriedade.
* Se apenas algumas medidas existirem para a maioria ou todos os eventos, será melhor enviar essas medidas como propriedades separadas dentro do mesmo objeto. Enviá-las separadamente reduz o número de eventos e, porque menos eventos precisam ser processados, a prática pode aumentar o desempenho das consultas.

## <a name="example"></a>Exemplo

O exemplo a seguir é baseado em um cenário em que dois ou mais dispositivos enviam sinais ou medidas. As medidas ou os sinais podem ser *Vazão*, *Pressão de Óleo do Motor*, *Temperatura* e *Umidade*.

No exemplo a seguir, há uma única mensagem do Hub IoT, em que a matriz externa contém uma seção compartilhada comuns dos valores de dimensão. A matriz externa usa dados da instância de série temporal para aumentar a eficiência da mensagem. A Instância da Série Temporal contém metadados de dispositivo, que não mudam a cada evento, mas fornece propriedades úteis para análise de dados. Para salvar em bytes enviados pela rede e tornar a mensagem mais eficiente, considere criar um lote de valores de dimensão comuns e utilizar metadados da Instância de Série Temporal.

### <a name="example-json-payload"></a>Conteúdo JSON de exemplo

```JSON
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 1.0172575712203979,
                "Engine Oil Pressure psi ": 34.7
            },
            {
                "Flow Rate ft3/s": 2.445906400680542,
                "Engine Oil Pressure psi ": 49.2
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "Flow Rate psi": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

### <a name="time-series-instance"></a>Instância de Série Temporal 
> [!NOTE]
> A ID de Série Temporal é *deviceId*.

```JSON
{
    "timeSeriesId": [
      "FXXX"
    ],
    "typeId": "17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds": [
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description": null,
    "instanceFields": {
      "L1": "REVOLT SIMULATOR",
      "L2": "Battery System",
    }
  },
  {
    "timeSeriesId": [
      "FYYY"
    ],
    "typeId": "17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds": [
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description": null,
    "instanceFields": {
      "L1": "COMMON SIMULATOR",
      "L2": "Battery System",
    }
  },
```

A Versão Prévia do Time Series Insights une uma tabela (após o nivelamento) durante o tempo de consulta. A tabela inclui colunas adicionais, como **Tipo**. O exemplo a seguir demonstra como você pode [formatar](./time-series-insights-send-events.md#json) os dados de telemetria:

| deviceId  | Tipo | L1 | L2 |  timestamp | series.Flow Rate ft3/s | series.Engine Oil Pressure psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | REVOLT SIMULATOR | Sistema de Bateria | 2018-01-17T01:17:00Z |    1,0172575712203979 |    34,7 |
| `FXXX` | LINE_DATA    REVOLT | SIMULADOR |    Sistema de Bateria |    2018-01-17T01:17:00Z | 2,445906400680542 |  49,2 |
| `FYYY` | LINE_DATA    COMMON | SIMULADOR |    Sistema de Bateria |    2018-01-17T01:18:00Z | 0,58015072345733643 |    22,2 |

No exemplo anterior, observe os seguintes pontos:

* Propriedades estáticas são armazenadas na Versão Prévia do Time Series Insights para otimizar os dados enviados pela rede.
* Dados da Versão Prévia do Time Series Insights são unidos no momento da consulta usando a *timeSeriesId* definida na instância.
* Duas camadas de aninhamento são usadas, que é o máximo compatível com a Versão Prévia do Time Series Insights. É essencial para evitar matrizes profundamente aninhadas.
* Como há algumas medidas, elas são enviadas como propriedades separadas dentro do mesmo objeto. No exemplo, **series.Flow Rate psi**, **series.Engine Oil Pressure psi** e **series.Flow Rate ft3/s** são colunas exclusivas.

>[!IMPORTANT]
> Campos de instância não são armazenados com telemetria. São armazenados com metadados no **Modelo de Série Temporal**.
> A tabela anterior representa a visualização da consulta.

## <a name="next-steps"></a>Próximas etapas

Para colocar essas diretrizes em prática, confira [Sintaxe de consulta da Versão Prévia do Azure Time Series Insights](./time-series-insights-query-data-csharp.md). Você aprenderá mais sobre a sintaxe de consulta para a API REST de acesso a dados da Versão Prévia do Time Series Insights.

Para saber mais sobre formas JSON com suporte, confira [Formas JSON com suporte](./time-series-insights-send-events.md#json).
