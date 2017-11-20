---
title: "Simulação de transação de alta frequência com o Stream Analytics | Microsoft Docs"
description: "Como executar a pontuação e o treinamento do modelo de regressão linear no mesmo trabalho do Stream Analytics"
keywords: "aprendizado de máquina, análise avançada, regressão linear, simulação, UDA, função definida pelo usuário"
documentationcenter: 
services: stream-analytics
author: zhongc
manager: jhubbard
editor: cgronlun
ms.assetid: 997ccfc1-abaf-4c12-bef2-632481140f05
ms.service: stream-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 11/05/2017
ms.author: zhongc
ms.openlocfilehash: 0a5a1129c5b7fc693ed7c187d928a128650f28b9
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="high-frequency-trading-simulation-with-stream-analytics"></a>Simulação de transação de alta frequência com o Stream Analytics
A combinação de linguagem SQL do Azure Stream Analytics e UDF do JavaScript e UDA é uma combinação poderosa que permite aos usuários executar análises avançadas, incluindo pontuação e treinamento de aprendizado de máquina online, assim como simulação de processo com estado. Este artigo descreve como executar regressão linear em um trabalho do Azure Stream Analytics que faz classificação e pontuação contínuas em um cenário de transação de alta frequência.

## <a name="high-frequency-trading"></a>Transação de alta frequência
O fluxo lógico da transação de alta frequência consiste em obter cotações em tempo real a partir de um câmbio de segurança, criar um modelo preditivo em torno das cotações, para que possamos prever a movimentação de preço e fazer pedidos de compra ou venda pedidos adequadamente para lucrar com uma previsão bem-sucedida das movimentações de preço. Como resultado, é necessário o seguinte
* Feed de cotação em tempo real
* Um modelo preditivo que pode operar com cotações em tempo real
* Uma simulação de negociação que demonstra o lucro/perda do algoritmo de negociação

### <a name="real-time-quote-feed"></a>Feed de cotação em tempo real
O IEX livre oferece cotações de compra e venda em tempo real usando socket.io, https://iextrading.com/developer/docs/#websockets. Um programa de console simples pode ser escrito para receber cotações em tempo real e enviar por push para o Hub de Eventos como uma fonte de dados. O esqueleto do programa é mostrado abaixo. O tratamento de erros foi omitido para fins de brevidade. Você também precisará incluir pacotes NuGet SocketIoClientDotNet e WindowsAzure.ServiceBus no seu projeto.


    using Quobject.SocketIoClientDotNet.Client;
    using Microsoft.ServiceBus.Messaging;

    var symbols = "msft,fb,amzn,goog";
    var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
    var socket = IO.Socket("https://ws-api.iextrading.com/1.0/tops");

    socket.On(Socket.EVENT_MESSAGE, (message) =>
    {
        eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes((string)message)));
    });

    socket.On(Socket.EVENT_CONNECT, () =>
    {
        socket.Emit("subscribe", symbols);
    });

Aqui estão alguns exemplos de evento gerados.

    {"symbol":"MSFT","marketPercent":0.03246,"bidSize":100,"bidPrice":74.8,"askSize":300,"askPrice":74.83,"volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,"lastUpdated":1506953357170,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"GOOG","marketPercent":0.04825,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,"volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,"lastUpdated":1506953357633,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"MSFT","marketPercent":0.03244,"bidSize":100,"bidPrice":74.8,"askSize":100,"askPrice":74.83,"volume":70572,"lastSalePrice":74.825,"lastSaleSize":100,"lastSaleTime":1506953355123,"lastUpdated":1506953359118,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"FB","marketPercent":0.01211,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.67,"volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,"lastUpdated":1506953359641,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"GOOG","marketPercent":0.04795,"bidSize":100,"bidPrice":959.19,"askSize":0,"askPrice":0,"volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,"lastUpdated":1506953360949,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"FB","marketPercent":0.0121,"bidSize":100,"bidPrice":169.9,"askSize":100,"askPrice":170.7,"volume":39042,"lastSalePrice":170.67,"lastSaleSize":100,"lastSaleTime":1506953351912,"lastUpdated":1506953362205,"sector":"softwareservices","securityType":"commonstock"}
    {"symbol":"GOOG","marketPercent":0.04795,"bidSize":114,"bidPrice":870,"askSize":0,"askPrice":0,"volume":11240,"lastSalePrice":959.47,"lastSaleSize":60,"lastSaleTime":1506953317571,"lastUpdated":1506953362629,"sector":"softwareservices","securityType":"commonstock"}

>[!NOTE]
>O carimbo de data/hora do evento é **lastUpdated**, em época.

### <a name="predictive-model-for-high-frequency-trading"></a>Modelo preditivo para a transação de alta frequência
Para fins de demonstração, usamos um modelo linear descrito por Darryl Shen no seu estudo. http://eprints.maths.ox.ac.uk/1895/1/Darryl%20Shen%20%28for%20archive%29.pdf.

Desequilíbrio de ordem de volume (VOI) é uma função do preço atual bid/peça e volume e bid/peça preço/volume do último tick. O estudo identifica a correlação entre VOI e a movimentação de preço futura e constrói um modelo linear entre os últimos 5 valores de VOI e a alteração de preço nos 10 ticks seguintes. O modelo é treinado usado os dados do dia anterior com regressão linear. O modelo treinado é então usado para fazer previsões de alteração do preço nas cotações no dia da negociação em tempo real. Quando uma alteração de preço grande o suficiente é prevista, uma transação é executada. Dependendo da configuração do limite, milhares de transações podem ser esperadas para uma única ação em um dia de negociação.

![Definição de VOI](./media/stream-analytics-high-frequency-trading/voi-formula.png)

Agora, vamos expressar as operações de treinamento e previsão em um trabalho do Azure Stream Analytics.

Primeiro, as entradas são apagadas. O tempo em época é convertido em datetime usando **DATEADD**. O **TRY_CAST** é usado para forçar os tipos de dados sem falhar a consulta. Sempre é uma boa prática converter os campos de entrada para os tipos de dados esperado, para que não haja nenhum comportamento inesperado na manipulação ou comparação dos campos.

    WITH
    typeconvertedquotes AS (
        /* convert all input fields to proper types */
        SELECT
            System.Timestamp AS lastUpdated,
            symbol,
            DATEADD(millisecond, CAST(lastSaleTime as bigint), '1970-01-01T00:00:00Z') AS lastSaleTime,
            TRY_CAST(bidSize as bigint) AS bidSize,
            TRY_CAST(bidPrice as float) AS bidPrice,
            TRY_CAST(askSize as bigint) AS askSize,
            TRY_CAST(askPrice as float) AS askPrice,
            TRY_CAST(volume as bigint) AS volume,
            TRY_CAST(lastSaleSize as bigint) AS lastSaleSize,
            TRY_CAST(lastSalePrice as float) AS lastSalePrice
        FROM quotes TIMESTAMP BY DATEADD(millisecond, CAST(lastUpdated as bigint), '1970-01-01T00:00:00Z')
    ),
    timefilteredquotes AS (
        /* filter between 7am and 1pm PST, 14:00 to 20:00 UTC */
        /* cleanup invalid data points */
        SELECT * FROM typeconvertedquotes
        WHERE DATEPART(hour, lastUpdated) >= 14 AND DATEPART(hour, lastUpdated) < 20 AND bidSize > 0 AND askSize > 0 AND bidPrice > 0 AND askPrice > 0
    ),

Em seguida, usamos a função **LAG** para obter valores do último tick. Uma hora do valor de **LIMIT DURATION** valor é escolhida arbitrariamente. Dada a frequência da cotação, é seguro supor que você pode encontrar o tick procurando na hora anterior.  

    shiftedquotes AS (
        /* get previous bid/ask price and size in order to calculate VOI */
        SELECT
            symbol,
            (bidPrice + askPrice)/2 AS midPrice,
            bidPrice,
            bidSize,
            askPrice,
            askSize,
            LAG(bidPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidPricePrev,
            LAG(bidSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS bidSizePrev,
            LAG(askPrice) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askPricePrev,
            LAG(askSize) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS askSizePrev
        FROM timefilteredquotes
    ),

Em seguida podemos calcular o valor de VOI. Observe que estamos filtrando os valores nulos se o tick anterior não existir, só por precaução.

    currentPriceAndVOI AS (
        /* calculate VOI */
        SELECT
            symbol,
            midPrice,
            (CASE WHEN (bidPrice < bidPricePrev) THEN 0
                ELSE (CASE WHEN (bidPrice = bidPricePrev) THEN (bidSize - bidSizePrev) ELSE bidSize END)
             END) -
            (CASE WHEN (askPrice < askPricePrev) THEN askSize
                ELSE (CASE WHEN (askPrice = askPricePrev) THEN (askSize - askSizePrev) ELSE 0 END)
             END) AS VOI
        FROM shiftedquotes
        WHERE
            bidPrice IS NOT NULL AND
            bidSize IS NOT NULL AND
            askPrice IS NOT NULL AND
            askSize IS NOT NULL AND
            bidPricePrev IS NOT NULL AND
            bidSizePrev IS NOT NULL AND
            askPricePrev IS NOT NULL AND
            askSizePrev IS NOT NULL
    ),

Agora, podemos usar **LAG** novamente para criar uma sequência com 2 valores de VOI consecutivos, seguido de 10 valores de preços médio consecutivos.

    shiftedPriceAndShiftedVOI AS (
        /* get 10 future prices and 2 previous VOIs */
        SELECT
            symbol,
            midPrice AS midPrice10,
            LAG(midPrice, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice9,
            LAG(midPrice, 2) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice8,
            LAG(midPrice, 3) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice7,
            LAG(midPrice, 4) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice6,
            LAG(midPrice, 5) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice5,
            LAG(midPrice, 6) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice4,
            LAG(midPrice, 7) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice3,
            LAG(midPrice, 8) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice2,
            LAG(midPrice, 9) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice1,
            LAG(midPrice, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS midPrice,
            LAG(VOI, 10) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI1,
            LAG(VOI, 11) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
        FROM currentPriceAndVOI
    ),

Em seguida reformatamos os dados em entradas para um modelo linear de duas variáveis. Filtramos novamente os eventos onde não temos todos os dados.

    modelInput AS (
        /* create feature vector, x being VOI, y being delta price */
        SELECT
            symbol,
            (midPrice1 + midPrice2 + midPrice3 + midPrice4 + midPrice5 + midPrice6 + midPrice7 + midPrice8 + midPrice9 + midPrice10)/10.0 - midPrice AS y,
            VOI1 AS x1,
            VOI2 AS x2
        FROM shiftedPriceAndShiftedVOI
        WHERE
            midPrice1 IS NOT NULL AND
            midPrice2 IS NOT NULL AND
            midPrice3 IS NOT NULL AND
            midPrice4 IS NOT NULL AND
            midPrice5 IS NOT NULL AND
            midPrice6 IS NOT NULL AND
            midPrice7 IS NOT NULL AND
            midPrice8 IS NOT NULL AND
            midPrice9 IS NOT NULL AND
            midPrice10 IS NOT NULL AND
            midPrice IS NOT NULL AND
            VOI1 IS NOT NULL AND
            VOI2 IS NOT NULL
    ),

Como o Azure Stream Analytics não tem uma função de regressão linear interna, usamos as funções de agregação **SUM** e **AVG** para computar os coeficientes para o modelo linear.

![Fórmula da regressão linear](./media/stream-analytics-high-frequency-trading/linear-regression-formula.png)

    modelagg AS (
        /* get aggregates for linear regression calculation,
         http://faculty.cas.usf.edu/mbrannick/regression/Reg2IV.html */
        SELECT
            symbol,
            SUM(x1 * x1) AS x1x1,
            SUM(x2 * x2) AS x2x2,
            SUM(x1 * y) AS x1y,
            SUM(x2 * y) AS x2y,
            SUM(x1 * x2) AS x1x2,
            AVG(y) AS avgy,
            AVG(x1) AS avgx1,
            AVG(x2) AS avgx2
        FROM modelInput
        GROUP BY symbol, TumblingWindow(hour, 24, -4)
    ),
    modelparambs AS (
        /* calculate b1 and b2 for the linear model */
        SELECT
            symbol,
            (x2x2 * x1y - x1x2 * x2y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b1,
            (x1x1 * x2y - x1x2 * x1y)/(x1x1 * x2x2 - x1x2 * x1x2) AS b2,
            avgy,
            avgx1,
            avgx2
        FROM modelagg
    ),
    model AS (
        /* calculate a for the linear model */
        SELECT
            symbol,
            avgy - b1 * avgx1 - b2 * avgx2 AS a,
            b1,
            b2
        FROM modelparambs
    ),

Para usar o modelo do dia anterior para a pontuação do evento atual, queremos unir as cotações com o modelo. No entanto, aqui, em vez de usar **JOIN**, usamos **UNION** nos eventos do modelo e nos eventos da cotação e, em seguida, usamos **LAG** para emparelhar os eventos com o modelo do dia anterior, para que possamos obter uma correspondência exata. Por causa do fim de semana precisamos examinar os três últimos dias. Se usarmos um **JOIN** simples, obteremos três modelos para cada evento de cotação.

    shiftedVOI AS (
        /* get two consecutive VOIs */
        SELECT
            symbol,
            midPrice,
            VOI AS VOI1,        
            LAG(VOI, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 1)) AS VOI2
        FROM currentPriceAndVOI
    ),
    VOIAndModel AS (
        /* combine VOIs and models */
        SELECT
            'voi' AS type,
            symbol,
            midPrice,
            VOI1,
            VOI2,
            0.0 AS a,
            0.0 AS b1,
            0.0 AS b2
        FROM shiftedVOI
        UNION
        SELECT
            'model' AS type,
            symbol,
            0.0 AS midPrice,
            0 AS VOI1,
            0 AS VOI2,
            a,
            b1,
            b2
        FROM model
    ),
    VOIANDModelJoined AS (
        /* match VOIs with the latest model within 3 days (72 hours, to take weekend into account) */
        SELECT
            symbol,
            midPrice,
            VOI1 as x1,
            VOI2 as x2,
            LAG(a, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS a,
            LAG(b1, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b1,
            LAG(b2, 1) OVER (PARTITION BY symbol LIMIT DURATION(hour, 72) WHEN type = 'model') AS b2
        FROM VOIAndModel
        WHERE type = 'voi'
    ),

Agora, podemos fazer previsões e gerar sinais de compra/venda com base no modelo, com um valor limite de 0.02. O valor da transação de 10 é uma compra; o valor da transação de -10 é uma venda.

    prediction AS (
        /* make prediction if there is a model */
        SELECT
            symbol,
            midPrice,
            a + b1 * x1 + b2 * x2 AS efpc
        FROM VOIANDModelJoined
        WHERE
            a IS NOT NULL AND
            b1 IS NOT NULL AND
            b2 IS NOT NULL AND
            x1 IS NOT NULL AND
            x2 IS NOT NULL
    ),
    tradeSignal AS (
        /* generate buy/sell signals */
        SELECT
            DateAdd(hour, -7, System.Timestamp) AS time,
            symbol,     
            midPrice,
            efpc,
            CASE WHEN (efpc > 0.02) THEN 10 ELSE (CASE WHEN (efpc < -0.02) THEN -10 ELSE 0 END) END AS trade,
            DATETIMEFROMPARTS(DATEPART(year, System.Timestamp), DATEPART(month, System.Timestamp), DATEPART(day, System.Timestamp), 0, 0, 0, 0) as date
        FROM prediction
    ),

### <a name="trading-simulation"></a>Simulação da transação
Após termos os sinais da transação, desejamos testar a eficiência da estratégia comercial, sem realmente fazer a transação. Isso é feito com um agregado definido pelo usuário (UDA), com janelas de salto, saltando a cada minuto. O agrupamento adicional na data e a cláusula having permitem que a janela considere somente eventos pertencentes ao mesmo dia. Para uma janela de salto que inclua dois dias, **GROUP BY** por data, separa o agrupamento em dia anterior e atual. A cláusula **HAVING** filtra as janelas que terminam no dia atual, mas o agrupamento no dia anterior.

    simulation AS
    (
        /* perform trade simulation for the past 7 hours to cover an entire trading day, generate output every minute */
        SELECT
            DateAdd(hour, -7, System.Timestamp) AS time,
            symbol,
            date,
            uda.TradeSimulation(tradeSignal) AS s
        FROM tradeSignal
        GROUP BY HoppingWindow(minute, 420, 1), symbol, date
        Having DateDiff(day, date, time) < 1 AND DATEPART(hour, time) < 13
    )

O UDA do JavaScript inicializa todos os acumuladores na função init, calcula a transição de estado com todos os eventos adicionados à janela e retorna os resultados da simulação no final da janela. O processo geral de transação é comprar ações quando um sinal de compra é recebido e não há nenhum investimento em ações; vender ações quando um sinal de venda é recebido há investimento em ações ou ação vendida se não houver nenhum investimento em ações. Se estiver na posição vendida e um sinal de compra é recebido, compre para cobrir. Nunca mantemos ou vendemos 10 frações de qualquer ação nesta simulação e as transações possuem um custo fixo de US$8.


    function main() {
        var TRADE_COST = 8.0;
        var SHARES = 10;

        this.init = function () {
            this.own = false;
            this.pos = 0;
            this.pnl = 0.0;
            this.tradeCosts = 0.0;
            this.buyPrice = 0.0;
            this.sellPrice = 0.0;
            this.buySize = 0;
            this.sellSize = 0;
            this.buyTotal = 0.0;
            this.sellTotal = 0.0;
        }

        this.accumulate = function (tradeSignal, timestamp) {

            if(!this.own && tradeSignal.trade == 10) {
              // Buy to open
              this.own = true;
              this.pos = 1;
              this.buyPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.buySize += SHARES;
              this.buyTotal += SHARES * tradeSignal.midprice;
            } else if(!this.own && tradeSignal.trade == -10) {
              // Sell to open
              this.own = true;
              this.pos = -1
              this.sellPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.sellSize += SHARES;
              this.sellTotal += SHARES * tradeSignal.midprice;
            } else if(this.own && this.pos == 1 && tradeSignal.trade == -10) {
              // Sell to close
              this.own = false;
              this.pos = 0;
              this.sellPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
              this.sellSize += SHARES;
              this.sellTotal += SHARES * tradeSignal.midprice;

              // Sell to open
              this.own = true;
              this.pos = -1;
              this.sellPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.sellSize += SHARES;        
              this.sellTotal += SHARES * tradeSignal.midprice;
            } else if(this.own && this.pos == -1 && tradeSignal.trade == 10) {
              // Buy to close
              this.own = false;
              this.pos = 0;
              this.buyPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.pnl += (this.sellPrice - this.buyPrice)*SHARES - 2*TRADE_COST;
              this.buySize += SHARES;
              this.buyTotal += SHARES * tradeSignal.midprice;

              // Buy to open
              this.own = true;
              this.pos = 1;
              this.buyPrice = tradeSignal.midprice;
              this.tradeCosts += TRADE_COST;
              this.buySize += SHARES;         
              this.buyTotal += SHARES * tradeSignal.midprice;
            }
        }

        this.computeResult = function () {
            var result = {
                "pnl": this.pnl,
                "buySize": this.buySize,
                "sellSize": this.sellSize,
                "buyTotal": this.buyTotal,
                "sellTotal": this.sellTotal,
                "tradeCost": this.tradeCost
                };
            return result;
        }
    }

Por fim, enviamos o resultado para o painel do Power BI para visualização.

    SELECT * INTO tradeSignalDashboard FROM tradeSignal /* output tradeSignal to PBI */
    SELECT
        symbol,
        time,
        date,
        TRY_CAST(s.pnl as float) AS pnl,
        TRY_CAST(s.buySize as bigint) AS buySize,
        TRY_CAST(s.sellSize as bigint) AS sellSize,
        TRY_CAST(s.buyTotal as float) AS buyTotal,
        TRY_CAST(s.sellTotal as float) AS sellTotal
        INTO pnlDashboard
    FROM simulation /* output trade simulation to PBI */

![Transações](./media/stream-analytics-high-frequency-trading/trades.png)

![P&L](./media/stream-analytics-high-frequency-trading/pnl.png)


## <a name="summary"></a>Resumo
Como você pode ver, um modelo de transação de alta frequência realista pode ser implementado com uma consulta moderadamente complexa no Azure Stream Analytics. Temos que simplificar o modelo de cinco variáveis de entrada para duas, devido à falta de uma função de regressão linear interna. No entanto, para um determinado usuário, os algoritmos com mais dimensões e sofisticação podem possivelmente ser implementados também como UDA do JavaScript. Vale a pena observar que a maioria das consultas, que não seja UDA do JavaScript, pode ser testada e depurada dentro do Visual Studio com a [Ferramenta do Azure Stream Analytics para Visual Studio](stream-analytics-tools-for-visual-studio.md). Depois que a consulta inicial foi escrita, o autor gasta menos de 30 minutos testando e depurando a consulta no Visual Studio. No momento, o UDA não pode ser depurado no Visual Studio. Estamos trabalhando para habilitar essa funcionalidade com a capacidade de percorrer o código JavaScript. Além disso, observe que os campos que alcançar a UDA possuem nomes de campo inferior todos em letras minúsculas. Isso não era um comportamento óbvio durante o teste de consulta. No entanto, com o nível de compatibilidade do Azure Stream Analytics 1.1, permitimos que as maiúsculas e minúsculas do nome do campo sejam preservadas, para que o comportamento seja mais natural.

Espero que este artigo sirva como inspiração para todos os usuários do Azure Stream Analytics, que podem usar o nosso serviço para executar análises avançadas quase em tempo real, de forma contínua. Os seus comentários são importantes para que possamos facilitar a implementação de consultas para cenários de consulta avançada.
