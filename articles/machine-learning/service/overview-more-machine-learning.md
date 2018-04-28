---
title: Outros produtos de aprendizado de máquina da Microsoft — Azure Machine Learning | Microsoft Docs
description: Além do Azure Machine Learning, há uma variedade de opções na Microsoft para criar, implantar e gerenciar modelos de aprendizado de máquina.
services: machine-learning
author: haining
ms.author: haining
manager: cgronlun
ms.reviewer: garyericson, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/11/2018
ms.openlocfilehash: bbb8deeab8368d9c0e6d29c8d7e1e2e0a8805d60
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="other-machine-learning-products-and-services-from-microsoft"></a>Outros produtos de aprendizado de máquina e serviços da Microsoft

Além do [Azure Machine Learning](overview-what-is-azure-ml.md), há uma variedade de opções na Microsoft para criar, implantar e gerenciar modelos de aprendizado de máquina. 
* Serviços de Machine Learning do SQL Server
* Microsoft Machine Learning Server
* Máquina Virtual da Ciência de Dados do Azure
* MLLib Spark no HDInsight
* Serviço de Treinamento de IA do Lote
* CNTK (Microsoft Cognitive Toolkit)
* Serviços Cognitivos do Azure


## <a name="sql-server-machine-learning-services"></a>Serviços de Machine Learning do SQL Server
Os [Serviços de Machine Learning do SQL Server da Microsoft](https://docs.microsoft.com/sql/advanced-analytics/r/r-services) permitem que você execute, treine e implante modelos de aprendizado de máquina usando o R ou Python. Você pode usar dados situados localmente e em Bancos de Dados do SQL Server. 

Use os Serviços de Machine Learning da Microsoft quando você precisar treinar ou implantar modelos localmente ou dentro do Microsoft SQL Server. Modelos criados com os Serviços de Machine Learning podem ser implantados usando o Gerenciamento de Modelos do Azure Machine Learning. 

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server 
[O Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone) é um servidor empresarial para hospedagem e gerenciamento de cargas de trabalho paralelas e distribuídas de processos do R e do Python. O Microsoft Machine Learning Server é executado no Windows, Linux, Hadoop e Apache Spark. Ele também está disponível em [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/). Ele fornece um mecanismo de execução para soluções criadas usando [pacotes do Microsoft Machine Learning](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package) e estende o R e o Python de software livre com suporte para os seguintes cenários:

- Análise de alto desempenho
- Análise estatística
- Aprendizado de máquina
- Conjuntos de dados realmente grandes

A funcionalidade agregada é fornecida por meio de pacotes de proprietários que são instalados com o servidor. Para desenvolvimento, você pode usar IDEs como [Ferramentas do R para Visual Studio](https://www.visualstudio.com/vs/rtvs/) e [Ferramentas Python para Visual Studio](https://www.visualstudio.com/vs/python/).

Use o Microsoft Machine Learning Server quando você precisar:

- Compilar e implantar modelos criados com R e Python em um servidor
- Distribuir o treinamento de R e Python em grande escala em um cluster Spark ou Hadoop

## <a name="azure-data-science-virtual-machine"></a>Máquina Virtual da Ciência de Dados do Azure
O [Máquina Virtual de Ciência de Dados](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) é um ambiente de máquina virtual personalizada na nuvem do Microsoft Azure, especificamente criada para ciência de dados. Ela tem muitas ferramentas conhecidas de ciência de dados, entre outras, pré-instaladas e pré-configuradas que ajudam a começar a criar rapidamente aplicativos inteligentes para análise avançada. A VM de Ciência de Dados está disponível em um versões do Windows Server 2012 e 2016, em uma VM Linux Ubuntu 16.04 LTS e em distribuições de OpenLogic CentOS 7.4. 

Use a VM de Ciência de Dados quando você precisar executar ou hospedar seus trabalhos em um único nó. Ou então, se você precisar expandir remotamente o processamento em um único computador. A Máquina Virtual de Ciência de Dados tem suporte como um destino para Experimentação do Azure Machine Learning e Gerenciamento de Modelos do Azure Machine Learning. 

## <a name="spark-mllib-in-hdinsight"></a>MLLib Spark no HDInsight
O [MLLib Spark no HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-ipython-notebook-machine-learning) permite que você crie modelos como parte de trabalhos Spark que estão em execução em Big Data. O Spark permite que você transforme e prepare dados com facilidade e, em seguida, expanda a criação de modelos em um único trabalho. Modelos criados por meio do MLLib Spark podem ser implantados, gerenciados e monitorados por meio do Gerenciamento de Modelos do Azure Machine Learning. Execuções de treinamento podem ser distribuídas e gerenciadas com a Experimentação do Azure Machine Learning. O Spark também pode ser usado para expandir trabalhos de preparação de dados criados no Machine Learning Workbench. 

Use o Spark quando você precisar expandir o processamento de dados e criar modelos como parte de um pipeline de dados. Você pode criar trabalhos Spark no Scala, Java, Python ou R. 

## <a name="batch-ai-training"></a>Treinamento de IA do Lote 
O [Treinamento de IA do Lote do Azure](https://aka.ms/batchaitraining) ajuda a fazer experiências em paralelo com seus modelos de IA usando qualquer estrutura, então treina-os em grande escala em GPUs clusterizadas. Descreva sua configuração e requisitos de trabalho para a execução e nós cuidaremos do restante. 

O Treinamento de IA do Lote permite que você expanda trabalhos de aprendizagem profunda entre GPUs clusterizadas usando estruturas como:

- Kit de Ferramentas Cognitivas
- Caffe
- Encadeador
- TensorFlow

O Gerenciamento de Modelos do Azure Machine Learning pode ser usado para implantar, gerenciar e monitorar modelos tomados do Treinamento de IA do Lote.  O Treinamento de IA do Lote será integrado à Experimentação do Azure Machine Learning no futuro. 

## <a name="microsoft-cognitive-toolkit-cntk"></a>CNTK (Microsoft Cognitive Toolkit)
O [Kit de Ferramentas Cognitivas da Microsoft](https://www.microsoft.com/en-us/cognitive-toolkit/) é um kit de ferramentas de aprendizagem profunda unificado que descreve as redes neurais como etapas computacionais em um grafo direcionado. Nesse grafo direcionado, nós de folha representam valores de entrada ou parâmetros de rede, enquanto outros nós representam as operações de matriz sobre as respectivas entradas. O Kit de Ferramentas Cognitivas permite facilmente perceber e combinar tipos de modelo populares como DNNs de encaminhamento do feed, CNNs (redes convolucionais) e RNNs/LSTMs (redes recorrentes). Ele implementa o SGD (gradiente descendente estocástico, erro backpropagation) com diferenciação automática e paralelização entre vários GPUs e servidores.

Use o Kit de Ferramentas Cognitivas quando você desejar criar um modelo usando aprendizagem profunda.  O Kit de Ferramentas Cognitivas pode ser usado em qualquer um dos serviços acima.

## <a name="azure-cognitive-services"></a>Serviços Cognitivos do Azure
Os [Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/#pivot=products&panel=ai) são um conjunto de cerca de 30 APIs que permitem que você crie aplicativos que usam métodos naturais de comunicação. Essas APIs permitem que seus aplicativos vejam, ouçam, falem, entendam e interpretem as necessidades dos usuários com apenas algumas linhas de código. Adicione facilmente recursos inteligentes para seus aplicativos, tais como: 

- Detecção de emoção e sentimento
- Visão e reconhecimento de fala
- LUIS (Reconhecimento vocal)
- Conhecimento e pesquisa

Os Serviços Cognitivos podem ser usados para desenvolver aplicativos entre dispositivos e plataformas. As APIs estão em constante aperfeiçoamento e são fáceis de configurar. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>Próximas etapas

Leia a visão geral do [Azure Machine Learning](overview-what-is-azure-ml.md).
