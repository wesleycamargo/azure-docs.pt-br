---
title: "Visão geral da Ciência de dados usando Spark no Azure HDInsight | Microsoft Docs"
description: "O kit de ferramentas Spark MLlib traz recursos consideráveis de modelagem de aprendizado de máquina para o ambiente distribuído do HDInsight."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: a4e1de99-a554-4240-9647-2c6d669593c8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: deguhath;bradsev;gokuma
translationtype: Human Translation
ms.sourcegitcommit: 304323601a7fb2c9b46cf0e1eea9429cf099a111
ms.openlocfilehash: da0f910e75fc74821bb08b2fd5b06ed08c386646



---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Visão geral da ciência de dados usando Spark no Azure HDInsight
[!INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

Este conjunto de tópicos mostra como usar o HDInsight Spark para concluir tarefas comuns de ciência de dados, tais como ingestão de dados, engenharia de recursos, modelagem e avaliação de modelo. Os dados usados são uma amostra do conjunto de dados de corridas e tarifas de táxi em Nova York de 2013. Os modelos criados incluem a regressão logística e linear, florestas aleatórias e árvores aumentadas gradientes. Os tópicos também mostram como armazenar esses modelos no Armazenamento de Blobs do Azure (WASB) e como pontuar e avaliar seu desempenho preditivo. Os tópicos mais avançados abordam como os modelos podem ser treinados usando a validação cruzada e a limpeza de hiperparâmetro. Este tópico de visão geral também descreve como configurar um cluster Spark necessário para concluir as etapas nos três passo a passos fornecidos. 

[Spark](http://spark.apache.org/) é uma estrutura de processamento paralelo de software livre que dá suporte ao processamento na memória para melhorar o desempenho de aplicativos analíticos de Big Data. O mecanismo de processamento do Spark foi desenvolvido para velocidade, facilidade de uso e análise sofisticada. As funcionalidades de computação distribuídas na memória do Spark fazem dele uma boa escolha para algoritmos iterativos em cálculos de gráfico e aprendizado de máquina. [MLlib](http://spark.apache.org/mllib/) é a biblioteca de aprendizado de máquina escalonável do Spark que oferece recursos de modelagem para esse ambiente distribuído. 

[HDInsight Spark](../hdinsight/hdinsight-apache-spark-overview.md) é a oferta do Spark de software livre hospedada no Azure. Ele também inclui suporte para **notebooks do Jupyter PySpark** no cluster Spark, que podem executar consultas Spark SQL interativas para transformar, filtrar e visualizar dados armazenados em Blobs do Azure (WASB). PySpark é a API do Python para o Spark. Os trechos de código que fornecem as soluções e mostram as plotagens relevantes para visualizar os dados aqui são executados em notebooks do Jupyter instalados nos clusters Spark. As etapas de modelagem nesses tópicos contêm código que mostra como treinar, avaliar, salvar e consumir cada tipo de modelo. 

As etapas de configuração e o código fornecidos neste passo a passo são referentes ao HDInsight Spark 1.6. Porém, Notebooks Jupyter são fornecidos tanto para o HDInsight Spark 1.6 quanto para os clusters Spark 2.0. No entanto, o código mostrado aqui e nos notebooks vinculados é genérico e funcionarão em qualquer cluster Spark. Se você não estiver usando o HDInsight Spark, as etapas de configuração e gerenciamento do cluster poderão ser ligeiramente diferentes do que é mostrado aqui.

## <a name="prerequisites"></a>Pré-requisitos
1.Você precisa ter uma assinatura do Azure. Se ainda não tiver uma, veja [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2. Você precisa de um cluster Spark 1.6 ou Spark 2.0 para concluir este passo a passo. Para criar um, veja as instruções fornecidas em [Introdução: criar um Apache Spark no Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). O tipo de cluster e a versão são especificados no menu **Selecionar Tipo de Cluster** . 

![Configurar cluster](./media/machine-learning-data-science-spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Para ver um tópico que mostra como usar o Scala, em vez do Python, para concluir as tarefas em um processo completo de ciência de dados, confira [Ciência de dados usando Scala com Spark no Azure](machine-learning-data-science-process-scala-walkthrough.md).
> 
> 

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

## <a name="the-nyc-2013-taxi-data"></a>Os dados de Táxi em NYC em 2013
Os dados de Viagens de Táxi em NYC são cerca de 20 GB de arquivos compactados em valores separados por vírgulas (CSV) (cerca de 48 GB descompactados), que incluem mais de 173 milhões de viagens individuais e a tarifa paga por cada viagem. Cada registro de corrida inclui o local e horário de saída e chegada, número da carteira de motorista de taxista anônima e o número do medalhão (identificador exclusivo do táxi). Os dados abrangem todas as corridas no ano de 2013 e são fornecidos nos dois conjuntos de dados a seguir para cada mês:

1. Os arquivos CSV “trip_data” contêm detalhes da corrida, como o número de passageiros, pontos de saída e chegada, duração e extensão da corrida. Aqui estão alguns exemplos de registros:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. Os arquivos CSV “trip_fare” contêm detalhes sobre as passagens pagas por cada corrida, como tipo de pagamento, valor da tarifa, custos adicionais e impostos, gorjetas e pedágios, e o valor total pago. Aqui estão alguns exemplos de registros:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Pegamos uma amostra de 0,1% desses arquivos CVS trip\_data and trip\_fare e os unimos em um único conjunto de dados que será usado como o conjunto de dados de entrada para este passo a passo. A chave exclusiva para unir trip\_data e trip\_fare é composta pelos campos: medallion, hack\_license e pickup\_datetime. Cada registro do conjunto de dados contém os seguintes atributos que representam uma corrida de táxi em NYC:

| Campo | Breve descrição |
| --- | --- |
| medallion |Licença do táxi em forma anônima (ID exclusiva do táxi) |
| hack_license |Número de licença do carro em forma anônima |
| vendor_id |ID do fornecedor do serviço de táxi |
| rate_code |Tarifa praticada pelos táxis em NYC |
| store_and_fwd_flag |Sinalizador de que os dados foram armazenados no veículo e encaminhados posteriormente |
| pickup_datetime |Data e hora do início da corrida |
| dropoff_datetime |Data e hora do final da corrida |
| pickup_hour |Hora da corrida |
| pickup_week |Semana do ano da corrida |
| weekday |Dia da semana (intervalo de 1 a 7) |
| passenger_count |Número de passageiros em uma corrida de táxi |
| trip_time_in_secs |Tempo da corrida em segundos |
| trip_distance |Distância percorrida na corrida em milhas |
| pickup_longitude |Longitude da corrida |
| pickup_latitude |Latitude da corrida |
| dropoff_longitude |Longitude do final da corrida |
| dropoff_latitude |Latitude do final da corrida |
| direct_distance |Distância direta entre os locais inicial e final da corrida |
| payment_type |Tipo de pagamento (dinheiro, cartão de crédito etc.) |
| fare_amount |Valor da tarifa |
| surcharge |Taxa adicional |
| mta_tax |Imposto da MTA |
| tip_amount |Valor da gorjeta |
| tolls_amount |Valor dos pedágios |
| total_amount |Valor total |
| tipped |Gorjeta (0/1 para não ou sim) |
| tip_class |Classe da gorjeta (0: US$ 0, 1: US$ 0 a 5, 2: US$ 6 a 10, 3: US$ 11 a 20, 4: mais de US$ 20) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Executar código de um bloco de anotações Jupyter no cluster Spark
É possível iniciar o Notebook do Jupyter no portal do Azure. Encontre o cluster Spark no painel e clique nele para entrar na página de gerenciamento de seu cluster. Para abrir o notebook associado ao cluster Spark, clique nos **Painéis do Cluster** -> **Notebook Jupyter**.

![Painéis de cluster](./media/machine-learning-data-science-spark-overview/spark-jupyter-on-portal.png)

Você também pode navegar até ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** para acessar os Notebooks Jupyter. Substitua a parte CLUSTERNAME desta URL pelo nome do seu próprio cluster. Você precisa da senha de sua conta de administrador para acessar os notebooks.

![Procurar Notebooks Jupyter](./media/machine-learning-data-science-spark-overview/spark-jupyter-notebook.png)

Selecione PySpark para ver um diretório que contém alguns exemplos de notebooks predefinidos que usam a API do PySpark. Os notebooks que contêm os exemplos de código para esse conjunto de tópicos do Spark estão disponíveis no [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)

Você pode carregar os blocos de anotações diretamente do Github para o servidor do bloco de anotações Jupyter no seu cluster Spark. Na home page do seu Jupyter, clique no botão **Carregar** na parte direita da tela. Ele abre o explorador de arquivos. Aqui, você pode colar a URL do Github (conteúdo bruto) do Notebook e clicar em **Abrir**. Os notebooks do PySpark estão disponíveis nas seguintes URLs. Para clusters HDInsight pySpark2-Spark1.6:

1. [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb)
2. [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-model-consumption.ipynb)
3. [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb)

Para clusters HDInsight pySpark3-Spark2.0:

[pySpark3-Spark2.0-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/pySpark3-Spark2.0-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb)

Você vê o nome do arquivo em sua lista de arquivos do Jupyter, com um botão **Carregar** novamente. Clique nesse botão **Carregar** . Agora, você importou o notebook. Repita estas etapas deste passo a passo para carregar outros notebooks.

> [!TIP]
> Clique com o botão direito nos links no seu navegador e selecione **Copiar Link** para obter a URL do conteúdo bruto github. Você pode colar essa URL na caixa de diálogo do explorador de arquivos de carregamento Jupyter.
> 
> 

Agora você pode:

* Consulte o código clicando no notebook.
* Executar cada célula pressionando **SHIFT-ENTER**.
* Executar o notebook inteiro clicando em **Célula** -> **Executar**.
* Usar a visualização automática de consultas.

> [!TIP]
> O kernel PySpark visualiza automaticamente a saída das consultas SQL (HiveQL). Você terá a opção de selecionar entre vários tipos diferentes de visualização (Tabela, Pizza, Linha, Área ou Barra) usando os botões de menu **Tipo** no notebook:
> 
> 

![Curva ROC de regressão logística de abordagem genérica](./media/machine-learning-data-science-spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>O que vem a seguir?
Agora que configurou um cluster HDInsight Spark e carregou os notebooks Jupyter, você está pronto para usar os tópicos que correspondem aos três notebooks PySpark. Eles mostram como explorar os dados e como criar e consumir modelos. Onotebook de exploração e modelagem de dados avançadas mostra como incluir validação cruzada, limpeza de hiperparâmetro e avaliação de modelo. 

**Exploração de Dados e modelagem com o Spark:** explore o conjunto de dados, crie, pontue e avalie os modelos de aprendizado de máquina usando o tópico [Create binary classification and regression models for data with the Spark MLlib toolkit](machine-learning-data-science-spark-data-exploration-modeling.md) (Criar modelos de regressão e classificação binária para dados com o kit de ferramentas MLlib do Spark).

**Consumo de modelo:** para saber como pontuar os modelos de classificação e regressão criados neste tópico, confira [Pontuar modelos de aprendizado de máquina criados no Spark](machine-learning-data-science-spark-model-consumption.md).

**Validação cruzada e limpeza de hiperparâmetro**: confira [Modelagem e exploração de dados avançados com o Spark](machine-learning-data-science-spark-advanced-data-exploration-modeling.md) para saber como os modelos podem ser treinados usando a validação cruzada e a limpeza de hiperparâmetro




<!--HONumber=Feb17_HO2-->


