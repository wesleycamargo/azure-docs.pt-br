---
title: 'Aplicativo do Aprendizado de Máquina: Serviço de Detecção de Anomalias | Microsoft Docs'
description: A API de detecção de anomalias é um exemplo criado com o Aprendizado de Máquina do Microsoft Azure que detecta anomalias nos dados de série temporal com valores numéricos que são espaçados uniformemente no tempo.
services: machine-learning
documentationcenter: ''
author: alokkirpal
manager: jhubbard
editor: cgronlun

ms.service: machine-learning
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 10/11/2016
ms.author: alokkirpal

---
# <a name="machine-learning-anomaly-detection-service#"></a>Serviço de Detecção de Anomalias do Aprendizado de Máquina
## <a name="overview"></a>Visão geral
A [API de detecção de anomalias](https://datamarket.azure.com/dataset/aml_labs/anomalydetection) é um exemplo criado com o Azure Machine Learning que detecta anomalias nos dados de série temporal com valores numéricos que são espaçados uniformemente no tempo. 

Esta API pode detectar os seguintes tipos de padrão anômalo nos dados de série temporal:

* **Tendências positivas e negativas**: por exemplo, ao monitorar o uso da memória na computação, uma tendência de aumento pode ser interessante, pois ela pode indicar um vazamento de memória,
* **Alterações no intervalo dinâmico dos valores**: por exemplo, ao monitorar as exceções geradas por um serviço de nuvem, as alterações no intervalo dinâmico dos valores pode indicar instabilidade na integridade do serviço e
* **Picos e quedas**: por exemplo, ao monitorar o número de falhas de logon em um serviço ou o número de check-outs em um site de comércio eletrônico, os picos ou as quedas podem indicar um comportamento anormal.

Esses detectores de aprendizado da máquina rastreiam as alterações nos valores ao longo do tempo e informam as mudanças contínuas em seus valores como pontuações de anomalia. Eles não precisam de ajuste de limite ad hoc e suas pontuações podem ser usadas para controlar a taxa de falsos positivos. A API de detecção de anomalias é útil em vários cenários, como o monitoramento do serviço ao controlar KPIs ao longo do tempo, monitoramento do uso por meio de métricas, como o número de pesquisas, número de cliques, monitoramento do desempenho por meio de contadores, como memória, CPU, leituras de arquivos etc. ao longo do tempo.

A oferta da Detecção de Anomalias vem com ferramentas úteis para você começar. 

* O [aplicativo Web](http://anomalydetection-aml.azurewebsites.net/) ajuda a avaliar e visualizar os resultados das APIs de detecção de anomalias em seus dados. 
* O [código de exemplo](http://adresultparser.codeplex.com/) mostra como acessar a API e analisar os resultados no C# programaticamente.

> [!NOTE]
> Tente a **solução IT Anomaly Insights** fornecida por [essa API](https://datamarket.azure.com/dataset/aml_labs/anomalydetection)
> 
> Para obter essa solução de ponta a ponta implantada em sua assinatura do Azure <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank"> **Comece por aqui >**</a>
> 
> 

## <a name="api-definition"></a>Definição da API
O serviço fornece uma API baseada em REST sobre HTTPS que pode ser consumida de maneiras diferentes, incluindo um aplicativo Web ou móvel, R, Python, Excel etc.  Você envia seus dados de série temporal para esse serviço por meio de uma chamada da API REST e eles executarão uma combinação dos três tipos de anomalias descritos acima. O serviço é executado na plataforma de Aprendizado de Máquina do Azure, que se dimensiona continuamente conforme as necessidades de negócio e fornece SLAs.

### <a name="headers"></a>Cabeçalhos
Inclua os cabeçalhos corretos em sua solicitação, que devem da seguinte maneira:

    Authorization: Basic <creds>
    Accept: application/json

    Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

Você pode encontrar a chave da conta em sua conta no [Azure Data Market](https://datamarket.azure.com/account/keys). 

### <a name="score-api"></a>API de Pontuação
A API de Pontuação é usada para executar a detecção de anomalias nos dados de série temporal não sazonais. A API executa vários detectores de anomalias nos dados e retorna suas pontuações de anomalias. A figura abaixo mostra um exemplo de anomalias que a API de Pontuação pode detectar. A série temporal tem duas alterações distintas no nível e três picos. Os pontos vermelhos mostram a hora em que a alteração no nível é detectada, enquanto as setas pretas mostram os picos detectados.

![API de Pontuação][1]

**URL**

    https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/Score 

**Exemplo do corpo de solicitação**

Na solicitação abaixo, enviamos uma série temporal (mostrada truncada) com pontos de dados de 1/3/2016 até 10/3/2016 e parâmetros dos detectores de picos para a API para detectar se qualquer um desses pontos é anômalo. 

    {
      "data": [
        [ "9/21/2014 11:05:00 AM", "3" ],
        [ "9/21/2014 11:10:00 AM", "9.09" ],
        [ "9/21/2014 11:15:00 AM", "0" ]
      ],
      "params": {
        "tspikedetector.sensitivity": "3",
        "zspikedetector.sensitivity": "3",
        "trenddetector.sensitivity": "3.25",
        "bileveldetector.sensitivity": "3.25",
        "postprocess.tailRows": "2"
      }
    }

A resposta para isso será: 

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
      "ADOutput":"{
        "ColumnNames":["Time","Data","TSpike","ZSpike","rpscore","rpalert","tscore","talert"],
        "ColumnTypes":["DateTime","Double","Double","Double","Double","Int32","Double","Int32"],
        "Values":[
          ["9/21/2014 11:10:00 AM","9.09","0","0","-1.07030497733224","0","-0.884548154298423","0"],
          ["9/21/2014 11:15:00 AM","0","0","0","-1.05186237440962","0","-1.173800281031","0"]
        ]
      }"
    }

### <a name="scorewithseasonality-api"></a>API ScoreWithSeasonality
A API ScoreWithSeasonality é usada para executar a detecção de anomalias na série temporal que têm padrões sazonais. Essa API é útil para detectar desvios nos padrões sazonais.  

A figura a seguir mostra um exemplo de anomalias detectadas em uma série temporal sazonal. A série temporal tem um pico (o 1º ponto preto), duas queda (o 2º ponto preto e um no final) e uma alteração no nível (ponto vermelho). Observe que a queda no meio da série temporal e a alteração no nível só ficam visíveis depois dos componentes sazonais serem removidos da série.

![API de Sazonalidade][2]

**URL**

    https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/ScoreWithSeasonality 

**Exemplo do corpo de solicitação**

Na solicitação abaixo, enviamos uma série temporal (mostrada truncada) com pontos de dados de 1/3/2016 até 10/3/2016 e parâmetros para a API para detectar se qualquer um desses pontos é anômalo. 

    {
      "data": [
        [ "9/21/2014 11:10:00 AM", "9" ],
        [ "9/21/2014 11:15:00 AM", "12" ],
        [ "9/21/2014 11:20:00 AM", "10" ]
      ],
      "params": {
        "preprocess.aggregationInterval": "0",
        "preprocess.aggregationFunc": "mean",
        "preprocess.replaceMissing": "lkv",
        "postprocess.tailRows": "1",
        "zspikedetector.sensitivity": "3",
        "tspikedetector.sensitivity": "3",
        "upleveldetector.sensitivity": "3.25",
        "bileveldetector.sensitivity": "3.25",
        "trenddetector.sensitivity": "3.25",
        "detectors.historywindow": "500",
        "seasonality.enable": "true",
        "seasonality.transform": "deseason",
        "seasonality.numSeasonality": "1"
      }
    }

A resposta para isso será: 

    {
        "odata.metadata": "https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
        "ADOutput": "{
            "ColumnNames":["Time","OriginalData","ProcessedData","TSpike","ZSpike","PScore","PAlert","RPScore","RPAlert","TScore","TAlert"],
            "ColumnTypes":["DateTime","Double","Double","Double","Double","Double","Int32","Double","Int32","Double","Int32"],
            "Values":[
                ["9/21/201411: 20: 00AM","10","10","0","0","-1.30229513613974","0","-1.30229513613974","0","-1.173800281031","0"]
            ]
        }"
    }

### <a name="detectors"></a>Detectores
A API de detecção de anomalias suporta detectores em três categorias amplas. Os detalhes sobre determinados parâmetros de entrada e saídas para cada detector podem ser encontrados na tabela a seguir.

| Categoria do Detector | Detector | Descrição | Parâmetros de Entrada | Saídas |
| --- | --- | --- | --- | --- |
| Detectores de Pico |Detector TSpike |Detectar picos e quedas com base na distância dos valores em relação ao primeiro e terceiro quartis |*tspikedetector.sensitivity:* usa o valor inteiro no intervalo de 1 a 10, padrão: 3; valores mais altos capturam valores mais extremos, tornando-o menos sensível |TSpike: valores binários – '1' se um pico/queda for detectado, '0' do contrário |
| Detector ZSpike |Detecta picos e quedas com base na distância dos pontos de dados em relação à média |*zspikedetector.sensitivity:* usa o valor inteiro no intervalo de 1 a 10, padrão: 3; valores mais altos capturam valores mais extremos, tornando-o menos sensível |ZSpike: valores binários – '1' se um pico/queda for detectado, '0' do contrário | |
| Detector de Tendências Lentas |Detector de Tendências Lentas |Detectar uma tendência positiva lenta de acordo com a sensibilidade definida |*trenddetector.sensitivity:* limite na pontuação do detector (padrão: 3,25, 3,25 – 5 é um intervalo razoável para selecionar. Quanto maior, menos sensível) |TScore: número flutuante que representa a pontuação de anomalias na tendência |
| Detectores de Alteração no Nível |Detector de Alteração no Nível Unidirecional |Detectar uma alteração de aumento no nível de acordo com a sensibilidade definida |*upleveldetector.sensitivity:* limite na pontuação do detector (padrão: 3,25, 3,25 – 5 é um intervalo razoável para selecionar. Quanto maior, menos sensível) |PScore: número flutuante que representa a pontuação de anomalias na alteração de aumento no nível |
| Detector de Alteração no Nível Bidirecional |Detectar uma alteração de aumento e diminuição no nível de acordo com a sensibilidade definida |*bileveldetector.sensitivity:* limite na pontuação do detector (padrão: 3.25, 3.25 – 5 é um intervalo razoável para selecionar. Quanto maior, menos sensível) |RPScore: número flutuante que representa a pontuação de anomalias na alteração de aumento e diminuição no nível | |

### <a name="parameters"></a>Parâmetros
Informações mais detalhadas sobre esses parâmetros de entrada são listadas na tabela a seguir:

| Parâmetros de Entrada | Descrição | Configuração Padrão | Tipo | Intervalo Válido | Intervalo Sugerido |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |Intervalo de agregação em segundos para agregar a série temporal de entrada |0 (nenhuma agregação é realizada) |inteiro |0: ignorar a agregação, > 0 do contrário |5 minutos para 1 dia, dependente da série temporal |
| preprocess.aggregationFunc |Função usada para agregar dados para o AggregationInterval especificado |média |enumeração |média, soma, comprimento |N/D |
| preprocess.replaceMissing |Valores usados para atribuir os dados ausentes |lkv (último valor conhecido) |enumeração |zero, lkv, média |N/D |
| detectors.historyWindow |Histórico (no número de pontos de dados) usado para o cálculo da pontuação de anomalias |500 |inteiro |10-2.000 |Dependente da série temporal |
| upleveldetector.sensitivity |Sensibilidade para o detector da alteração de aumento no nível. |3.25 |double |Nenhum |3.25-5 (Os valores mais baixos significam mais sensível) |
| bileveldetector.sensitivity |Sensibilidade para o detector de alteração no nível bidirecional. |3.25 |double |Nenhum |3.25-5 (Os valores mais baixos significam mais sensível) |
| trenddetector.sensitivity |Sensibilidade para o detector de tendência positiva. |3.25 |double |Nenhum |3.25-5 (Os valores mais baixos significam mais sensível) |
| tspikedetector.sensitivity |Sensibilidade para o Detector TSpike |3 |inteiro |1-10 |3-5 (Os valores mais baixos significam mais sensível) |
| zspikedetector.sensitivity |Sensibilidade para o Detector ZSpike |3 |inteiro |1-10 |3-5 (Os valores mais baixos significam mais sensível) |
| seasonality.enable |Caso a análise da sazonalidade precise ser executada |verdadeiro |Booliano |verdadeiro, falso |Dependente da série temporal |
| seasonality.numSeasonality |Número máximo de ciclos periódicos a serem detectados |1 |inteiro |1, 2 |1-2 |
| seasonality.transform |Caso os componentes sazonais (e) de tendência devam ser removidos antes de aplicar a detecção de anomalias |sem sazonalidade |enumeração |nenhum, sem sazonalidade, sem sazonalidade e tendência |N/D |
| postprocess.tailRows |Número de pontos de dados mais recentes a serem mantidos nos resultados da saída |0 |inteiro |0 (manter todos os pontos de dados) ou especificar o número de pontos de nos resultados |N/D |

### <a name="output"></a>Saída
A API executa esses detectores em seus dados da série temporal e retorna as pontuações de anomalias e os indicadores de picos binários para cada ponto no tempo. A tabela abaixo lista as saídas da API. 

| Saídas | Descrição |
| --- | --- |
| Hora |Carimbos de data/hora dos dados brutos ou dos dados atribuídos (e/ou) agregados se a atribuição dos dados ausentes (e/ou) de agregação for aplicada |
| OriginalData |Valores dos dados brutos ou dos dados atribuídos (e/ou) agregados se a atribuição dos dados ausentes (e/ou) de agregação for aplicada |
| ProcessedData |Um dos seguintes:  <ul><li>Série temporal ajustada periodicamente caso uma sazonalidade significativa tenha sido detectada e opção sem sazonalidade foi selecionada;</li><li>séries temporais e sem tendência e ajustadas periodicamente caso uma sazonalidade significativa tenha sido detectada e a opção sem sazonalidade e tendência foi selecionada</li><li>caso contrário, isso é o mesmo que OriginalData</li> |
| TSpike |Indicador binário para indicar se um pico é detectado pelo Detector TSpike |
| ZSpike |Indicador binário para indicar se um pico é detectado pelo Detector ZSpike |
| Pscore |Um número flutuante que representa a pontuação de anomalias na alteração de aumento no nível |
| Palert |Valor 1/0 indicando que há uma anomalia na alteração de aumento no nível baseada na sensibilidade de entrada |
| RPScore |Um número flutuante que representa a pontuação de anomalias na alteração bidirecional no nível |
| RPAlert |Valor 1/0 indicando que há uma anomalia na alteração bidirecional no nível baseada na sensibilidade de entrada |
| TScore |um número flutuante que representa a pontuação de anomalias na tendência positiva |
| TAlert |Valor 1/0 indicando que há uma anomalia de tendência positiva baseada na sensibilidade de entrada |

Esta saída pode ser analisada usando um [Analisador simples](https://adresultparser.codeplex.com/) – tem um código de exemplo que mostra como conectar a API e analisar a saída. As anomalias detectadas podem ser visualizadas em um painel e/ou passadas para especialistas humanos para terem ações corretivas ou serem integradas nos sistemas de emissão de tíquetes.

[1]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-score.png
[2]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-seasonal.png







<!--HONumber=Oct16_HO2-->


