---
title: O que é a API do Detector de anomalias? | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Use algoritmos avançados da API de Detector de anomalias para identificar anomalias em seus dados de série temporal.
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 48dffb8853d3def590e434c1683ecf038e2a194e
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58473126"
---
# <a name="what-is-the-anomaly-detector-api"></a>O que é a API do Detector de anomalias?

A API do Detector de anomalias permite monitorar e detectar anormalidades nos seus dados de série temporal com aprendizado de máquina. A API do Detector de anomalias se adapta automaticamente identificando e aplicando os modelos de melhor ajuste para seus dados, independentemente do setor, o cenário ou o volume de dados. Usando seus dados de série temporal, a API determina os limites para detecção de anomalias, valores esperados, e quais pontos de dados são anomalias.

![Detectar alterações de padrão nas solicitações de serviço](./media/anomaly_detection2.png)

Usar o Detector de anomalias não exige qualquer experiência anterior em aprendizado de máquina e a API RESTful permite que você integre facilmente o serviço em seus aplicativos e processos.

## <a name="features"></a>Recursos

Com o Detector de anomalias, você pode detectar automaticamente anomalias ao longo de seus dados de série temporal, ou conforme elas ocorrem em tempo real. 

|Recurso  |DESCRIÇÃO  |
|---------|---------|
|Detecte anomalias conforme elas ocorrem em tempo real. | Detecte anomalias em seus dados de streaming por meio de pontos de vista anteriormente dados para determinar se seu mais recente é uma anomalia. Essa operação gera um modelo usando os pontos de dados enviados e determina se o ponto de destino é uma anomalia. Chamando a API com cada novo ponto de dados que você gerar, você pode monitorar seus dados conforme eles são criados. |
|Detecte anomalias em todo o conjunto de dados como um lote. | Use sua série de tempo para detectar todas as anomalias que podem existir ao longo de seus dados. Essa operação gera um modelo usando seus dados de série de tempo todo, com cada ponto analisado com o mesmo modelo.         |
| Obter informações adicionais sobre seus dados. | Obtenha detalhes úteis sobre seus dados e quaisquer anomalias observadas, incluindo valores esperados, os limites de anomalias e posições. |
| Ajuste os limites de detecção de anomalias. | A API do Detector de anomalias criará automaticamente limites para a detecção de anomalias. Ajustar esses limites para aumentar ou diminuir a sensibilidade da API para anomalias de dados e ajustar melhor os seus dados. |

## <a name="demo"></a>Demonstração

Para começar rapidamente a usar a API do Detector de anomalias, tente uma [demonstração online](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector) que podem ser executadas em seu navegador. Esta demonstração é executado em um bloco de anotações do Jupyter hospedado na web e mostra como enviar uma solicitação de API e visualizar o resultado.

Para executar a demonstração, conclua as seguintes etapas:

1. Obtenha uma chave de assinatura válida do API do Detector de anomalias e um ponto de extremidade de API. A seção a seguir tem instruções para se inscrever. 
2. Entrar e clique em Clone, no canto superior direito.
3. Clique em **executados em computação gratuitas**
4. Selecione um dos blocos de anotações para este exemplo.
5. Adicione sua chave de assinatura de API do Detector de anomalias válido para o `subscription_key` variável. Alterar o `endpoint` variável ao ponto de extremidade. Por exemplo: `https://westus2.api.cognitive.microsoft.com`
1. Na barra de menus superior, clique em **célula**, em seguida, **executar tudo**.

## <a name="workflow"></a>Fluxo de trabalho

A API do Detector de anomalias é um serviço web RESTful, tornando mais fácil chamar a partir de qualquer linguagem de programação que pode fazer solicitações HTTP e analisar o JSON.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Após a inscrição:

1. Leve seus dados de série de tempo e convertê-la em um formato JSON válido. Use [práticas recomendadas](concepts/anomaly-detection-best-practices.md) ao preparar seus dados para obter os melhores resultados.
1. Envie uma solicitação para a API do Detector de anomalias com seus dados.
1. Processe a resposta da API analisando a mensagem JSON retornada.

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Detectar anomalias em seus dados de série temporal usando a API de REST do Detector de anomalias](quickstarts/detect-data-anomalies-csharp.md)
* A API do Detector de anomalias [demonstração online](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector)
* O Detector de anomalias [referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect)