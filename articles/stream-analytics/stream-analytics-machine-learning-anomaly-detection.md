---
title: "Detecção de anomalias no Guia de Uso do Azure (versão prévia) | Microsoft Docs"
description: Use o stream analytics e machine learning para detectar anomalias.
services: stream-analytics
documentationcenter: 
author: dubansal
manager: jhubbard
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/12/2018
ms.author: dubansal
ms.openlocfilehash: d8762ea608afed707d41a3c0a1a8725457a0e4dc
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2018
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Detecção de anomalias no Azure Stream Analytics

> [!IMPORTANT]
> Essa funcionalidade está em versão prévia, não é recomendável usar com cargas de trabalho de produção.

O operador **AnomalyDetection** é usado para detectar diferentes tipos de anomalias em fluxos de eventos. Por exemplo, uma diminuição lenta na memória livre durante um longo tempo pode ser uma indicação de um vazamento de memória, ou o número de solicitações de serviço Web que são estáveis em um intervalo pode aumentar ou diminuir muito.  

O operador AnomalyDetection detecta três tipos de anomalias: 

* **Alteração de nível bidirecional**: um aumento ou diminuição sustentada no nível de valores, tanto ascendente como descendente. Esse valor é diferente de picos e quedas, que são mudanças instantâneas ou de curta duração.  

* **Tendência positiva lenta**: um aumento lento na tendência ao longo do tempo.  

* **Tendência negativa lenta**: uma diminuição lenta da tendência ao longo do tempo.  

Ao usar o operador AnomalyDetection, será necessário especificar a cláusula **Limit Duration**. Essa cláusula especifica o intervalo de tempo (quanto tempo decorrido no histórico do evento atual) que deve ser considerado ao detectar anomalias. Esse operador pode, opcionalmente, ser limitado a apenas eventos que correspondem a uma determinada propriedade ou condição usando a cláusula **When** . Esse operador também pode, opcionalmente, processar grupos de eventos separadamente com base na chave especificada na cláusula  **Partition by** . Treinamento e previsão ocorrem de forma independente para cada partição. 

## <a name="syntax-for-anomalydetection-operator"></a>Sintaxe para operador AnomalyDetection

`ANOMALYDETECTION(<scalar_expression>) OVER ([PARTITION BY <partition key>] LIMIT DURATION(<unit>, <length>) [WHEN boolean_expression])` 

**Exemplo de uso**  

`SELECT id, val, ANOMALYDETECTION(val) OVER(PARTITION BY id LIMIT DURATION(hour, 1) WHEN id > 100) FROM input`

### <a name="arguments"></a>Argumentos

* **scalar_expression** - A expressão escalar sobre a qual a detecção de anomalia é realizada. Os valores permitidos para esse parâmetro incluem tipos de dados Float ou Bigint que retornam um único valor (escalar). A expressão curinga **\*** não é permitida. A expressão escalar não pode conter outras funções analíticas ou funções externas. 

* **partition_by_clause** - A cláusula `PARTITION BY <partition key>` divide a aprendizado e treinamento em partições separadas. Em outras palavras, um modelo separado seria usado por valor de `<partition key>`, e apenas os eventos com esse valor seriam usados para o aprendizado e treinamento nesse modelo. Por exemplo, a consulta a seguir treina e pontua uma leitura em relação a outras leituras do mesmo sensor somente:

  `SELECT sensorId, reading, ANOMALYDETECTION(reading) OVER(PARTITION BY sensorId LIMIT DURATION(hour, 1)) FROM input`

* **limit_duration clause** `DURATION(<unit>, <length>)` - Especifica o intervalo de tempo (quanto tempo decorrido no histórico do evento atual) que deve ser considerado ao detectar anomalias. Consulte [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics) para uma descrição detalhada das unidades com suporte e suas abreviaturas. 

* **when_clause** - Especifica uma condição booliana para os eventos considerados na computação de detecção de anomalia.

### <a name="return-types"></a>Tipos de retorno

O operador AnomalyDetection retorna um registro contendo as três pontuações como sua saída. As propriedades associadas aos diferentes tipos de detectores de anomalia são:

- BiLevelChangeScore
- SlowPosTrendScore
- SlowNegTrendScore

Para extrair os valores individuais do registro, use a função **GetRecordPropertyValue**. Por exemplo: 

`SELECT id, val FROM input WHERE (GetRecordPropertyValue(ANOMALYDETECTION(val) OVER(LIMIT DURATION(hour, 1)), 'BiLevelChangeScore')) > 3.25` 

Uma anomalia de um tipo é detectada quando uma das pontuações de anomalias cruza um limite. O limite pode ser qualquer número de ponto flutuante >= 0. O limite é um equilíbrio entre sensibilidade e confiança. Por exemplo, um limite inferior tornaria a detecção mais sensível a alterações e geraria mais alertas, enquanto um limite superior poderia tornar a detecção menos sensível e mais segura, mas mascararia algumas anomalias. O valor exato do limite a ser usado depende do cenário. Não há limite superior, mas o intervalo recomendado é 3,25-5. 

## <a name="anomaly-detection-algorithm"></a>Algoritmo de detecção de anomalia

* O operador AnomalyDetection usa uma **abordagem de aprendizado não supervisionado**, onde não assume nenhum tipo de distribuição nos eventos. Em geral, dois modelos são mantidos em paralelo em qualquer momento, onde um deles é usado para pontuação e o outro é treinado em segundo plano. Os modelos de detecção de anomalia são treinados usando dados do fluxo atual em vez de usar um mecanismo fora de banda. A quantidade de dados utilizados para treinamento depende do tamanho da janela d especificado pelo usuário dentro do parâmetro Limit Duration. Cada modelo acaba sendo treinado com base no valor de eventos d para 2d. É recomendável ter pelo menos 50 eventos em cada janela para obter melhores resultados. 

* O operador AnomalyDetection usa **semântica de janela deslizante** para treinar modelos e pontuar eventos. O que significa que cada evento é avaliado por anomalia e uma pontuação é retornada. A pontuação é uma indicação do nível de confiança dessa anomalia. 

* O operador AnomalyDetection fornece uma **garantia de repetibilidade** na mesma entrada sempre produzir o mesmo resultado, independentemente da hora de início da saída do trabalho. A hora de início da saída do trabalho representa o momento em que o primeiro evento de saída é produzido pelo trabalho. É definido pelo usuário para a hora atual, um valor personalizado ou a última hora de saída (se o trabalho tivesse produzido uma saída anteriormente). 

### <a name="training-the-models"></a>Treinar os modelos 

À medida que o tempo avança, os modelos são treinados com dados diferentes. Para entender as pontuações, ajuda a entender o mecanismo subjacente pelo qual os modelos são treinados. Aqui, **t<sub>0</sub>** é a **hora de início da saída do trabalho** e **d** é o **tamanho da janela** do parâmetro Limit Duration. Suponha que o tempo seja dividido em **saltos de tamanho d**, iniciando de `01/01/0001 00:00:00`. As etapas a seguir são usadas para treinar o modelo e marcar os eventos:

* Quando um trabalho é iniciado, ele lê os dados começando no tempo t<sub>0</sub> – 2d.  
* Quando o tempo chega ao próximo salto, um novo modelo M1 é criado e começa a ser treinado.  
* Quando o tempo avança por outro salto, um novo modelo M2 é criado e começa a ser treinado.  
* Quando o tempo alcança t<sub>0</sub>, M1 é ativado e sua pontuação começa a ser emitida.  
* No próximo salto, três situações acontecem ao mesmo tempo:  

  * M1 não é mais necessário e é descartado.  
  * M2 foi suficientemente treinado, então é usado para pontuação.  
  * Um novo modelo M3 é criado e começa a ser treinado em segundo plano.  

* Este ciclo se repete para cada salto, onde o modelo ativo é descartado, alterna para o modelo paralelo e inicia o treinamento de um terceiro modelo em segundo plano. 

Diagramaticamente, as etapas são semelhantes às seguintes: 

![Modelos de treinamento](media/stream-analytics-machine-learning-anomaly-detection/training_model.png)

|**Modelo** | **Hora de início do treinamento** | **Hora de começar a usar sua pontuação** |
|---------|---------|---------|
|M1     | 11:20   | 11:33   |
|M2     | 11:30   | 11:40   |
|M3     | 11:40   | 11:50   |

* O modelo M1 começa a treinar às 11:20, que é o próximo salto após o trabalho começar a ler às 11:13. O primeiro resultado é produzido a partir de M1 às 11:33 após treinamento com 13 minutos de dados. 

* Um novo modelo de M2 também começa a treinar às 11:30, mas sua pontuação não é usada até as 11:40, após ter sido treinado com 10 minutos de dados. 

* O M3 segue o mesmo padrão que o M2. 

### <a name="scoring-with-the-models"></a>Pontuação com os modelos 

No nível de Machine Learning, o algoritmo de detecção de anomalia calcula um valor de estranheza para cada evento de entrada comparando-o com eventos em uma janela de histórico. A computação de estranheza difere para cada tipo de anomalia.  

Vamos revisar a computação de estranheza em detalhes (assumir um conjunto de janelas de histórico com eventos existentes): 

1. **Alteração de nível bidirecional:** com base na janela de histórico, um intervalo de operação normal é computado como [percentil 10, percentil 90] que é o valor do percentil 10 como o limite inferior e o valor do percentil 90 como limite superior. Um valor de estranheza para o evento atual é computado como:  

   - 0, se event_value estiver no intervalo de operação normal  
   - event_value/90th_percentile, se event_value > 90th_percentile  
   - 10th_percentile/event_value, se event_value for < 10th_percentile  

2. **Tendência positiva lenta:** uma linha de tendência sobre os valores de evento na janela de histórico é calculada e buscamos uma tendência positiva dentro da linha. O valor de estranheza é calculado como:  

   - Inclinação, se a inclinação for positiva  
   - 0, caso contrário 

1. **Tendência negativa lenta:** uma linha de tendência sobre os valores de evento na janela do histórico é calculada e buscamos uma tendência negativa dentro da linha. O valor de estranheza é calculado como: 

   - Inclinação, se a inclinação for negativa  
   - 0, caso contrário  

Quando o valor de estranheza para o evento de entrada é computado, um valor martingale é computado com base no valor de estranheza (consulte o [blog do Machine Learning](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/) para detalhes sobre como o valor martingale é computado). Esse valor martingale é retomado como a pontuação de anomalia. O valor martingale aumenta lentamente em resposta a valores estranhos, o que permite que o detector permaneça robusto para mudanças esporádicas e reduz alertas falsos. Também possui uma propriedade útil: 

Probabilidade [t existe de modo que M<sub>t</sub> > λ ] < 1/λ, onde M<sub>t</sub> é o valor martingale no instante t e λ é um valor real. Por exemplo, se alertarmos quando M M<sub>t</sub>>100, então a probabilidade de falsos positivos será inferior a 1/100.  

## <a name="guidance-for-using-the-bi-directional-level-change-detector"></a>Diretriz para o uso do detector de alteração de nível bidirecional 

O detector de alteração de nível bidirecional pode ser usado em cenários como interrupção de energia e recuperação, ou tráfego de horas de pico etc. No entanto, ele funciona de tal forma que, uma vez que um modelo é treinado com determinados dados, outra alteração de nível será anômala se e somente se o novo valor for maior que o limite superior anterior (caso de alteração de nível ascendente) ou inferior ao limite inferior anterior (caso de alteração de nível descendente). Portanto, um modelo não deverá ver os valores de dados no intervalo do novo nível (ascendente ou descendente) em sua janela de treinamento para que possam ser considerados anômalos. 

Os seguintes pontos devem ser considerados ao usar esse detector: 

1. Quando as séries temporais repentinamente aumentam ou diminuem o valor, o operador AnomalyDetection detecta. Mas detectar o retorno ao normal requer mais planejamento. Se uma série temporal estava em estado contínuo antes da anomalia, o que pode ser alcançado configurando a janela de detecção do operador AnomalyDetection para no máximo metade do comprimento da anomalia. Esse cenário pressupõe que a duração mínima da anomalia pode ser estimada antes do tempo, e há eventos suficientes nesse período de tempo para treinar o modelo o suficiente (pelo menos 50 eventos). 

   Isso é mostrado nas figuras 1 e 2 abaixo, usando uma alteração de limite superior (a mesma lógica aplica-se a uma mudança de limite inferior). Em ambas as figuras, as formas de onda são uma alteração de nível anômala. As linhas verticais alaranjadas indicam limites de salto e tamanho de salto é o mesmo que a janela de detecção especificada no operador AnomalyDetection. As linhas verdes indicam o tamanho da janela de treinamento. Na Figura 1, o tamanho de salto é o mesmo que o tempo de duração da anomalia. Na Figura 2, o tamanho de salto é metade do tempo de duração da anomalia. Em todos os casos, uma alteração ascendente é detectada porque o modelo utilizado para pontuação foi treinado em dados normais. Mas com base em como o detector de alteração de nível bidirecional funciona, devemos excluir os valores normais da janela de treinamento usada para o modelo que marca o retorno ao normal. Na Figura 1, o treinamento do modelo de pontuação inclui alguns eventos normais, desse modo, retornar ao normal não pode ser detectado. Mas, na Figura 2, o treinamento inclui apenas a parte anômala, que garante que o retorno ao normal seja detectado. Qualquer coisa menor que a metade também funciona pela mesma razão, enquanto que qualquer coisa maior acabará incluindo um pouco dos eventos normais. 

   ![AD com tamanho da janela igual comprimento de anomalia](media/stream-analytics-machine-learning-anomaly-detection/windowsize_equal_anomaly_length.png)

   ![AD com tamanho da janela é igual à metade do comprimento da anomalia](media/stream-analytics-machine-learning-anomaly-detection/windowsize_equal_half_anomaly_length.png)

2. Nos casos em que o comprimento da anomalia não pode ser previsto, esse detector opera com o melhor esforço. No entanto, escolher uma janela de tempo mais estreita limita os dados de treinamento, o que aumentaria a probabilidade de detectar o retorno ao normal. 

3. No cenário a seguir, a anomalia mais longa não é detectada, pois a janela de treinamento já inclui uma anomalia do mesmo valor alto. 

   ![Anomalias com o mesmo tamanho](media/stream-analytics-machine-learning-anomaly-detection/anomalies_with_same_length.png)

## <a name="example-query-to-detect-anomalies"></a>Exemplo de consulta para detectar anomalias 

A consulta a seguir pode ser usada para gerar um alerta se uma anomalia for detectada.
Quando o fluxo de entrada não é uniforme, a etapa de agregação pode ajudar a transformá-lo em uma série temporal uniforme. O exemplo usa AVG, mas o tipo específico de agregação depende do cenário do usuário. Além disso, quando uma série temporal tem lacunas maiores do que a janela de agregação, não há nenhum evento na série temporal para disparar a detecção de anomalias (de acordo com a semântica de janela deslizante). Como resultado, a suposição de uniformidade é interrompida quando o próximo evento chega. Em tais situações, as lacunas na série temporal devem ser preenchidas. Uma abordagem possível é colocar o último evento em cada janelas de salto, conforme mostrado abaixo.

```sql
    WITH AggregationStep AS 
    (
         SELECT
               System.Timestamp as tumblingWindowEnd,

               AVG(value) as avgValue

         FROM input
         GROUP BY TumblingWindow(second, 5)
    ),

    FillInMissingValuesStep AS
    (
          SELECT
                System.Timestamp AS hoppingWindowEnd,

                TopOne() OVER (ORDER BY tumblingWindowEnd DESC) AS lastEvent

         FROM AggregationStep
         GROUP BY HOPPINGWINDOW(second, 300, 5)

    ),

    AnomalyDetectionStep AS
    (

          SELECT
                hoppingWindowEnd,
                lastEvent.tumblingWindowEnd as lastTumblingWindowEnd,
                lastEvent.avgValue as lastEventAvgValue,
                system.timestamp as anomalyDetectionStepTimestamp,

                ANOMALYDETECTION(lastEvent.avgValue) OVER (LIMIT DURATION(hour, 1)) as
                scores

          FROM FillInMissingValuesStep
    )

    SELECT
          alert = 1,
          hoppingWindowEnd,
          lastTumblingWindowEnd,
          lastEventAvgValue,
          anomalyDetectionStepTimestamp,
          scores

    INTO output

    FROM AnomalyDetectionStep

    WHERE

        CAST(GetRecordPropertyValue(scores, 'BiLevelChangeScore') as float) >= 3.25

        OR CAST(GetRecordPropertyValue(scores, 'SlowPosTrendScore') as float) >=
        3.25

       OR CAST(GetRecordPropertyValue(scores, 'SlowNegTrendScore') as float) >=
       3.25
```

## <a name="performance-guidance"></a>Diretriz de desempenho

* Use seis unidades de streaming para trabalhos. 
* Envie eventos pelo menos um segundo de distância.
* Uma consulta não particionada que está usando o operador AnomalyDetection pode produzir resultados com uma latência computacional de aproximadamente 25 ms em média.
* A latência experimentada por uma consulta particionada varia um pouco com o número de partições, à medida que o número de cálculos for maior. No entanto, a latência é quase a mesma que o caso não particionado para um número total comparável de eventos em todas as partições.
* Durante a leitura de dados não em tempo real, uma grande quantidade de dados é incluída rapidamente. O processamento desses dados é mais lento. A latência em tais cenários foi encontrada para aumentar linearmente com o número de pontos de dados na janela, em vez do tamanho da janela ou intervalo de eventos. Para reduzir a latência em casos de não em tempo real, considere o uso de um tamanho menor de janela. Como alternativa, considere começar seu trabalho na hora atual. Alguns exemplos de latências em uma consulta não particionada: 
    - 60 pontos de dados na janela de detecção podem resultar em uma latência de 10 segundos, com uma taxa de transferência de 3 Mbps. 
    - Em 600 pontos de dados, a latência pode atingir aproximadamente 80 segundos com uma taxa de transferência de 0,4 MBps.

## <a name="limitations-of-the-anomalydetection-operator"></a>Limitações do operador AnomalyDetection 

* Atualmente, esse operador não fornece suporte a detecção de pico e queda. Picos e quedas são alterações espontâneas ou de curta duração na série temporal. 

* Atualmente, esse operador não lida com padrões de sazonalidade. Padrões de sazonalidade são padrões repetidos nos dados, por exemplo, um comportamento de tráfego da Web diferente durante fins de semana ou tendências de compras diferentes durante o Black Friday, que não são anomalias, mas um padrão de comportamento esperado. 

* Esse operador espera que as séries temporais de entrada sejam uniformes. Um fluxo de eventos pode ficar uniforme pela agregação sobre uma janela em cascata ou de salto. Em cenários onde a lacuna entre os eventos sempre é menor do que a janela de agregação, uma janela em cascata é suficiente para tornar a série temporal uniforme. Quando o intervalo puder ser maior, os intervalos poderão ser preenchidos repetindo o último valor usando uma janela de salto. 

## <a name="references"></a>Referências

* [Detecção de anomalias – Usar Machine Learning para detectar anormalidades nos dados da série temporal](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/)
* [API de detecção de anomalias do Machine Learning](https://docs.microsoft.com/en-gb/azure/machine-learning/machine-learning-apps-anomaly-detection-api)
* [Detecção de anomalias de série temporal](https://msdn.microsoft.com/library/azure/mt775197.aspx)

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

