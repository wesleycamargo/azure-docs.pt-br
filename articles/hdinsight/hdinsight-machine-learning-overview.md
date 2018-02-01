---
title: "Visão geral do aprendizado de máquina - Azure HDInsight | Microsoft Docs"
description: "Descreve as opções de aprendizado de máquina no HDInsight."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: nitinme
ms.openlocfilehash: ff99a7a60573cad5e6dd30d4ca48903423e9f87f
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2018
---
# <a name="machine-learning-on-hdinsight"></a>Aprendizado de máquina no HDInsight

O HDInsight permite o aprendizado de máquina com Big Data, proporcionando a capacidade de obter informações valiosas de grandes quantidades (petabytes, ou mesmo exabytes) de dados estruturados, não estruturados e em movimento rápido. Há várias opções de aprendizado de máquina no HDInsight: SparkML e MLlib, R, Hive e o Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>SparkML e MLlib

O [HDInsight Spark](spark/apache-spark-overview.md) é uma oferta do [Spark](http://spark.apache.org/) hospedada no Azure, uma estrutura de processamento de dados paralelo unificada, de software livre, que fornece suporte ao processamento em memória para aumentar a análise de Big Data. O mecanismo de processamento do Spark foi desenvolvido para velocidade, facilidade de uso e análise sofisticada. As funcionalidades de computação distribuídas na memória do Spark fazem dele uma boa escolha para algoritmos iterativos usados em cálculos de grafo e aprendizado de máquina. Há três bibliotecas de aprendizado de máquina escalonáveis que oferecem recursos de modelagem de algoritmo para esse ambiente distribuído: MLlib e SparkML. O MLlib contém a API original criada com base em RDDs. O SparkML é um pacote mais recente que fornece uma API de nível superior criada com base em DataFrames para a construção de pipelines do ML. O SparkML ainda não fornece suporte a todos os recursos do MLlib, mas está substituindo o MLlib como biblioteca de aprendizado de máquina padrão do Spark.

A biblioteca do Microsoft Azure Machine Learning para Apache Spark é [MMLSpark](https://github.com/Azure/mmlspark). Essa biblioteca é projetada para tornar os cientistas de dados mais produtivos no Spark, aumentar a taxa de experimentação e alavancar técnicas de aprendizado de máquina avançadas, incluindo aprendizado profundo, em conjuntos de dados muito grandes. O MMLSpark fornece uma camada com base em APIs de baixo nível do SparkML ao construir modelos de ML escalonáveis, como indexação de cadeias de caracteres, forçar dados em um layout esperado por algoritmos de aprendizado de máquina e montagem de vetores de recursos. A biblioteca do MMLSpark simplifica essas e outras tarefas comuns para criação de modelos no PySpark.

## <a name="r"></a>R

[R](https://www.r-project.org/) atualmente é a linguagem de programação estatística mais popular do mundo. É uma ferramenta de visualização de dados de software livre com uma comunidade de mais de 2,5 milhões de usuários e em crescimento. Com sua base de usuários próspera e mais de 8.000 pacotes contribuídos, o R é uma opção provável para muitas empresas que precisam de aprendizado de máquina. Você pode criar um Cluster HDInsight com o Microsoft R Server pronto para ser usado com conjuntos de dados maciços e modelos. Essa capacidade fornece aos cientistas de dados e estatísticos uma interface R familiar que pode der dimensionada sob demanda por meio do HDInsight, sem a sobrecarga de configuração e manutenção do cluster.

![Treinamento para previsão com Microsoft R Server](./media/hdinsight-machine-learning-overview/r-training.png)

O nó de borda de um cluster fornece um local conveniente para se conectar ao cluster e executar os scripts de R.  Você também tem a opção de executar scripts R em todos os nós do cluster, usando o Hadoop Map Reduce do ScaleR ou contextos de computação do Spark.

Com o Microsoft R Server no HDInsight com Spark, é possível paralelizar o treinamento em todos os nós de um cluster, usando um contexto de computação do Spark. Você pode executar scripts R diretamente no nó de borda, usando todos os núcleos disponíveis em paralelo, conforme necessário. Alternativamente, você poderá executar seu código do nó de borda para iniciar o processamento distribuído em todos os nós no cluster. O Microsoft R Server no HDInsight com Spark também permite a paralelização de funções de pacotes R de software livre, se desejado.

## <a name="azure-machine-learning-and-hive"></a>Azure Machine Learning e Hive

O Microsoft Azure Machine Learning fornece ferramentas para modelar análises preditivas, bem como um serviço totalmente gerenciado que você pode usar para implantar seus modelos preditivos como serviços Web prontos para uso. O Microsoft Azure Machine Learning é uma solução de análise preditiva completa na nuvem que você pode usar para criar, testar, operacionalizar e gerenciar modelos preditivos. Selecione de uma grande biblioteca de algoritmos, use um estúdio baseado na Web para a criação de modelos e implante facilmente seu modelo como um serviço Web.

![Criar análises avançadas acessíveis ao Hadoop com Microsoft Azure Machine Learning](./media/hdinsight-machine-learning-overview/hadoop-azure-ml.png)

Criar recursos para dados em um cluster do HDInsight Hadoop usando [consultas do Hive](../machine-learning/team-data-science-process/create-features-hive.md). *A engenharia de recursos* tenta aumentar o poder preditivo dos algoritmos de aprendizado, criando recursos a partir de dados brutos que facilitam o processo de aprendizado. É possível executar consultas HiveQL do Azure ML e acessar os dados processados no Hive e armazenados no Armazenamento de Blobs, usando o [Módulo para Importação de Dados](../machine-learning/studio/import-data.md).

## <a name="microsoft-cognitive-toolkit"></a>Kit de Ferramentas Cognitivas da Microsoft

[Aprendizado profundo](https://www.microsoft.com/en-us/research/group/dltc/) é um branch de aprendizado de máquina que usa redes neurais, inspirado nos processos biológicos do cérebro humano. Muitos pesquisadores veem o aprendizado profundo como uma abordagem promissora para aprimorar a inteligência artificial. Exemplos de aprendizado profundo são os tradutores de idiomas falados, sistemas de reconhecimento de imagens e raciocínio de máquina.

Para ajudar a avançar seu próprio trabalho no aprendizado profundo, a Microsoft desenvolveu o [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/). Este kit de ferramentas está sendo usado por uma grande variedade de produtos da Microsoft, por empresas em todo o mundo com a necessidade de implantar aprendizado profundo em escala e por estudantes interessados nos algoritmos e técnicas mais recentes. 

## <a name="see-also"></a>Consulte também

### <a name="scenarios"></a>Cenários

* [Spark com Machine Learning: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados HVAC](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Gerar recomendações de filmes com Mahout](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Hive e Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Hive e Azure Machine Learning de ponta a ponta](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Aprendizado de máquina com o Spark em HDInsight](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Recursos de aprendizado profundo

* [Kit de ferramentas de aprendizado profundo com Spark](https://blogs.technet.microsoft.com/machinelearning/2017/04/25/using-microsofts-deep-learning-toolkit-with-spark-on-azure-hdinsight-clusters/)
* [Classificação de imagens paralelas de forma anormal com kit de ferramentas Cognitivas + Tensorflow no Spark](https://blogs.technet.microsoft.com/machinelearning/2017/04/12/embarrassingly-parallel-image-classification-using-cognitive-toolkit-tensorflow-on-azure-hdinsight-spark/)
