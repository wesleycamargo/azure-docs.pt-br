---
title: O que é Localizador de Anomalias? - Serviços Cognitivos da Microsoft | Microsoft Docs
description: Use algoritmos avançados no Localizador de Anomalias para ajudá-lo a identificar anomalias nos dados de série temporal e retornar informações nos Serviços Cognitivos da Microsoft.
services: cognitive-services
author: tonyxing
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 04/19/2018
ms.author: tonyxing
ms.openlocfilehash: 41d20cfd9821fbd2a34a66754e5c2da3d6439fd2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60499050"
---
# <a name="what-is-anomaly-finder"></a>O que é Localizador de Anomalias?

[!INCLUDE [PrivatePreviewNote](../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

O Localizador de Anomalias permite monitorar dados ao longo do tempo e detectar anomalias com o aprendizado de máquina que se adapta a seus dados exclusivos aplicando automaticamente o modelo estatístico correto, independentemente do setor, cenário ou volume de dados. Usando uma série temporal como entrada, a API do Localizador de Anomalias retorna se um ponto de dados é uma anomalia ou não, determina o valor esperado e os limites superior e inferior da visualização. Como um serviço de IA predefinido, o Localizador de Anomalias não requer nenhum conhecimento do aprendizado de máquina além de saber como usar uma API RESTful. Isso torna o desenvolvimento simples e versátil porque ele trabalha com os dados da série temporal e pode também ser baseado nos sistemas de dados de streaming. O Localizador de Anomalias abrange muitos casos de uso, por exemplo, ferramentas financeiras para gerenciar fraudes, roubo, mercados em evolução e possíveis incidentes de negócios ou monitoramento do tráfego de dispositivos IoT preservando o anonimato. Essa solução também pode ser monetizada como parte de um serviço de clientes finais para entender as alterações nos dados, gastos, retorno sobre investimento ou atividade do usuário.
Experimente a API do Localizador de Anomalias e entenda melhor seus dados. 

Veja o que você pode compilar com essa API:

* Aprenda a prever os valores esperados com base nos dados históricos na série temporal
* Informe se um ponto de dados é uma anomalia fora do padrão histórico
* Gere uma faixa para visualizar o intervalo de valor "normal"

![Anomaly_Finder](./media/anomaly_detection1.png) 

Fig. 1: Detectar anomalias nas receitas de vendas

![Anomaly_Finder](./media/anomaly_detection2.png)

Fig. 2: Detectar alterações de padrão nas solicitações de serviço

## <a name="requirements"></a>Requisitos

- Dados mínimos para série temporal de entrada: Mínimo de 13 pontos de dados para a série temporal sem uma periodicidade clara, mínimo de 4 ciclos de pontos de dados para a série temporal com periodicidade conhecida. 
- Integridade dos dados: os pontos de dados da série temporal são separados no mesmo intervalo e sem pontos ausentes. 

## <a name="identify-anomalies"></a>Identificar anomalias

A API de detecção de anomalias retorna resultados sobre se quaisquer pontos de dados especificados estão anomalias ou não e fornece informações extras da seguinte maneira
* Period: a periodicidade que a API usou para detectar os pontos de anomalias.
* WarningText: as possíveis informações de aviso.
* ExpectedValue: o valor previsto pelo modelo baseado em aprendizado
* IsAnomaly: o resultado sobre se os pontos de dados são anomalias ou não
* IsAnomaly_Neg: o resultado sobre se os pontos de dados são anomalias na direção negativa (quedas)
* IsAnomaly_Pos: o resultado sobre se os pontos de dados são anomalias na direção positiva (picos)
* UpperMargin: a soma de ExpectedValue e UpperMargin determina o limite superior para que o ponto de dados ainda seja considerado normal
* LowerMargin: (ExpectedValue - LowerMargin) determina o limite inferior para que o ponto de dados ainda seja considerado normal

> [!Note]
> UpperMargin e LowerMargin podem ser usados para gerar uma faixa em torno da série temporal real para visualizar o intervalo de valores normais. 

## <a name="adjusting-lower-and-upper-bounds-in-post-processing-on-the-response"></a>Ajustando os limites inferior e superior após o processamento na resposta

A API de detecção de anomalias retorna o resultado padrão sobre se um ponto de dados é uma anomalia ou não e se os limites superior e inferior podem ser calculados a partir de ExpectedValue e UpperMargin/LowerMargin. Esses valores padrão funcionam bem na maioria dos casos. No entanto, alguns cenários exigem limites diferentes das definições padrão. A prática recomendada é aplicar uma coeficiência em UpperMargin ou LowerMargin para ajustar os limites dinâmicos.

### <a name="examples-with-1152-as-coefficiency"></a>Exemplos com 1/1.5/2 como coeficiência

![Sensibilidade Padrão](./media/sensitivity_1.png)

![Sensibilidade 1.5](./media/sensitivity_1.5.png)

![Sensibilidade 2](./media/sensitivity_2.png)

Solicitar com dados de exemplo

[!INCLUDE [Request](./includes/request.md)]

Resposta JSON de exemplo

[!INCLUDE [Response](./includes/response.md)]
