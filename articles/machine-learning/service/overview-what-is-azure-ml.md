---
title: O que é o Azure Machine Learning? | Microsoft Docs
description: Explica os conceitos básicos do aprendizado de máquina na nuvem, descreve para que você pode usá-lo e define os termos do aprendizado de máquina. Visão geral do Azure Machine Learning, uma solução integrada de ciência de dados de ponta a ponta para cientistas profissionais desenvolverem, experimentarem e implantarem aplicativos de análise avançada em escala de nuvem.
services: machine-learning
author: mwinkle
ms.author: mwinkle
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: overview
ms.date: 09/21/2017
ms.openlocfilehash: 3e744b0e4a7ccebcdedac5a822ff717bed6b1f72
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268409"
---
# <a name="what-is-machine-learning"></a>O que é o Machine Learning?

O Machine Learning é uma técnica da ciência de dados que permite que os computadores usem os dados existentes para prever tendências, resultados e comportamentos futuros. Usando o aprendizado de máquina, os computadores aprendem sem serem explicitamente programados.

As estimativas ou previsões de aprendizado de máquina podem tornar aplicativos e dispositivos mais inteligentes. Quando você faz compras online, o aprendizado de máquina ajuda a recomendar outros produtos que podem lhe agradar com base no que você já comprou. Ao passar seu cartão de crédito, o aprendizado de máquina compara a transação com um banco de dados de transações e ajuda a detectar fraudes. Quando o aspirador de pó robô aspira uma sala, o aprendizado de máquina ajuda a decidir se o trabalho é feito ou não.

## <a name="what-is-azure-machine-learning"></a>O que é o Azure Machine Learning?
O Azure Machine Learning é uma solução integrada de análise avançada e de ciência de dados de ponta a ponta. Ele permite que os cientistas de dados preparem dados, desenvolvam experimentos e implantem modelos em escala de nuvem.

Os principais componentes do Azure Machine Learning são:
- Azure Machine Learning Workbench
- Serviço de Experimentação do Azure Machine Learning
- Serviço de Gerenciamento de Modelos do Azure Machine Learning
- Bibliotecas do Microsoft Machine Learning para o Apache Spark (Biblioteca MMLSpark)
- Ferramentas do Visual Studio Code para IA

Juntos, esses aplicativos e serviços ajudam a acelerar significativamente o desenvolvimento e a implantação de projetos de ciência de dados. 

![Conceitos de Azure Machine Learning](./media/overview-what-is-azure-ml/aml-concepts.png)


## <a name="open-source-compatible"></a>Compatível com software livre

O Azure Machine Learning dá suporte a tecnologias de software livre. Você pode usar dezenas de milhares de pacotes do Python de software livre, como as seguintes estruturas de aprendizado de máquina:

- [scikit-learn](http://scikit-learn.org/)
- [TensorFlow](https://www.tensorflow.org/)
- [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)
- [ML do Spark](https://spark.apache.org/docs/2.1.1/ml-pipeline.html)

Você pode executar seus experimentos em ambientes gerenciados como contêineres do Docker e clusters do Spark. Você também pode usar hardware avançado como [máquinas virtuais habilitadas para GPU no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu) para acelerar a execução.

O Azure Machine Learning é criado sobre as seguintes tecnologias de software livre:

- [Jupyter Notebook](http://jupyter.org/)
- [Apache Spark](https://spark.apache.org/)
- [Docker](https://www.docker.com/)
- [Kubernetes](https://kubernetes.io/)
- [Python](https://www.python.org/)
- [Conda](https://conda.io/docs/)

Ele também inclui tecnologias de software livre da Microsoft, como a [Biblioteca de Machine Learning da Microsoft para o Apache Spark](https://github.com/Azure/mmlspark) e o Kit de Ferramentas Cognitivas.

Além disso, você também se beneficiar de algumas das tecnologias de aprendizado de máquina mais avançadas, testadas e aprovadas desenvolvidas pela Microsoft para resolver problemas em larga escala. Elas são testadas em vários produtos da Microsoft, tais como:

- Windows
- Bing
- Xbox
- Office
- SQL Server

A seguir estão algumas das tecnologias de aprendizado de máquina da Microsoft incluídas com o Azure Machine Learning:

- [PROSE](https://microsoft.github.io/prose/) (Program Synthesis using Examples)
- [microsoftml](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)
- [revoscalepy](https://docs.microsoft.com/r-server/python-reference/revoscalepy/revoscalepy-package)

## <a name="azure-machine-learning-workbench"></a>Azure Machine Learning Workbench
O Azure Machine Learning Workbench é um aplicativo da área de trabalho mais ferramentas de linha de comando, com suporte no Windows e no macOS. Ele permite que você gerencie soluções de aprendizado de máquina por todo o ciclo de vida de ciência de dados:

- Preparação e ingestão de dados
- Modelo de desenvolvimento e gerenciamento de experimentos
- Implantação de modelos em ambientes de destino diversos

Aqui estão as funcionalidades principais oferecidas pelo Azure Machine Learning Workbench:

- Ferramenta de preparação de dados que pode aprender a lógica de transformação de dados por exemplo.
- Abstração de fonte de dados acessível por meio de código Python e UX.
- SDK do Python para invocar pacotes de preparação de dados construídos visualmente.
- Serviço de Bloco de anotações do Jupyter interno e UX cliente.
- Monitoramento e gerenciamento de experimentos por meio de exibições de histórico de execuções.
- Controle de acesso baseado em função que permite o compartilhamento e colaboração por meio do Azure Active Directory.
- Instantâneos de projeto automáticos para cada execução e controle de versão explícito habilitado pela integração nativa do Git. 
- Integração com IDEs populares do Python.

Para obter mais informações, faça referência aos seguintes artigos:
- [Guia do Usuário de Preparação de Dados](../desktop-workbench/data-prep-user-guide.md)
- [Usando o Git com o Azure Machine Learning](../desktop-workbench/using-git-ml-project.md)
- [Usando o Bloco de anotações do Jupyter no Azure Machine Learning](../desktop-workbench/how-to-use-jupyter-notebooks.md)
- [Roaming e Compartilhamento](../desktop-workbench/roaming-and-collaboration.md)
- [Lista do Histórico de Execuções](../desktop-workbench/how-to-use-run-history-model-metrics.md)
- [Integração de IDE](../desktop-workbench/how-to-configure-your-ide.md)

## <a name="azure-machine-learning-experimentation-service"></a>Serviço de Experimentação do Azure Machine Learning
O Serviço de Experimentação lida com a execução de experimentos de aprendizado de máquina. Ele também dá suporte ao Workbench, fornecendo gerenciamento de projetos, integração de Git, controle de acesso, roaming e compartilhamento. 

Por meio da configuração fácil, você pode executar suas experiências em uma variedade de opções de ambiente de computação:

- Local nativo
- Contêiner do Docker local
- Contêiner do Docker em uma VM remota
- Expandir o cluster Spark no Azure

O serviço de experimentação constrói ambientes virtuais para garantir que o script possa ser executado em isolamento com resultados reproduzíveis. Ele registra as informações de histórico de execuções e apresenta o histórico para você visualmente. Você pode selecionar facilmente o melhor modelo dentre as suas execuções de experimento. 

Para obter mais informações, confira [Configuração do Serviço de Experimentação](../desktop-workbench/experimentation-service-configuration.md).

## <a name="azure-machine-learning-model-management-service"></a>Serviço de Gerenciamento de Modelos do Azure Machine Learning

O Serviço de Gerenciamento de Modelos permite que cientistas de dados e equipes de operações de desenvolvimento implantem modelos preditivos em uma ampla variedade de ambientes. Versões de modelo e linhagem são acompanhados desde as execuções de treinamento até as implantações. Os modelos são armazenados, registrados e gerenciados na nuvem. 

Usando comandos CLI simples, você pode dividir seu modelo, seus scripts de pontuação e suas dependências em contêineres de imagens do Docker. Essas imagens são registradas no seu próprio registro do Docker hospedado no Azure (Registro de Contêiner do Azure). Eles podem ser implantados com segurança para os destinos a seguir:

- Computadores locais
- Servidores locais
- A nuvem
- Dispositivos de borda IoT

O Kubernetes em execução no ACS (Serviço de Contêiner do Azure) é usado para a implantação de expansão de nuvem. A telemetria de execução do modelo é capturada no AppInsights para análise visual. 

Para obter mais informações sobre o Serviço de Gerenciamento de Modelos, consulte [Visão geral do Gerenciamento de Modelos](../desktop-workbench/model-management-overview.md)


## <a name="microsoft-machine-learning-library-for-apache-spark"></a>Biblioteca do Microsoft Machine Learning para o Apache Spark

O [MMLSpark](https://github.com/Azure/mmlspark) (Biblioteca do Microsoft Machine Learning para o Apache Spark) é um pacote de Spark de software livre que fornece ferramentas de aprendizagem profunda e de ciência de dados para o Apache Spark. Ele integra [Pipelines de Machine Learning do Spark](https://spark.apache.org/docs/2.1.1/ml-pipeline.html) com o [Kit de Ferramentas Cognitivas da Microsoft](https://www.microsoft.com/en-us/cognitive-toolkit/) e a biblioteca [OpenCV](http://opencv.org/). Ele permite que você crie com rapidez modelos analíticos e preditivos altamente escalonáveis e eficientes para grandes conjuntos de dados de texto e de imagens. Alguns dos destaques incluem:

- Ingerir facilmente imagens do HDFS no DataFrame do Spark
- Pré-processar dados de imagem usando transformações do OpenCV
- Personalizar imagens usando redes neurais profundas previamente treinadas usando o Kit de Ferramentas Cognitivas da Microsoft 
- Usar LSTMs bidirecionais previamente treinados do Keras para extração de entidade médica
- Treinar modelos de classificação de imagem baseados em DNN em VMs de GPU série N no Azure
- Personalizar dados de texto de forma livre usando APIs convenientes sobre primitivos em SparkML via um único transformador
- Treinar modelos de classificação e regressão facilmente por meio de personalização implícita de dados
- Computar um conjunto rico de métricas de avaliação, incluindo métricas por instância

Para obter mais informações, consulte [Usando MMLSpark no Azure Machine Learning](../desktop-workbench/how-to-use-mmlspark.md).

## <a name="visual-studio-code-tools-for-ai"></a>Ferramentas do Visual Studio Code para IA
Ferramentas do Visual Studio para IA é uma extensão no Visual Studio Code para criar, testar e implantar soluções de IA e de aprendizagem profunda. Ele apresenta muitos pontos de integração com o Azure Machine Learning, incluindo:
- Uma exibição de histórico de execução mostrando o desempenho das execuções de treinamento e métricas registradas.
- Um modo de exibição de galeria permitindo aos usuários procurar e inicializar novos projetos com o Kit de Ferramentas Cognitivas da Microsoft, o TensorFlow e muitas outras estruturas de aprendizagem profunda. 
- Uma exibição do gerenciador para seleção de destinos de computação para seus scripts executarem.
 

## <a name="what-are-the-machine-learning-options-from-microsoft"></a>Quais são as opções de aprendizado de máquina da Microsoft?
Além do Azure Machine Learning, há uma ampla variedade de opções no Azure para criar, implantar e gerenciar modelos de aprendizado de máquina. [Saiba mais sobre eles aqui.](../desktop-workbench/overview-more-machine-learning.md)

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>Próximas etapas
* [Instalar e criar o Azure Machine Learning](quickstart-installation.md)
