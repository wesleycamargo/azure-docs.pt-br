---
title: Escolher uma tecnologia de aprendizado de máquina na Microsoft
description: Comparar as opções para criar, implantar e gerenciar seus modelos de aprendizado de máquina. Decida quais produtos da Microsoft escolher para sua solução.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
ms.reviewer: jmartens
author: garyericson
ms.author: garye
ms.date: 1/30/2019
ms.openlocfilehash: cd156a10faf396778b76687b3666887a6a60876c
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55505692"
---
# <a name="what-are-the-machine-learning-products-at-microsoft"></a>Quais são os produtos de aprendizado de máquina na Microsoft?

A Microsoft fornece uma variedade de opções de produto para compilar, implantar e gerenciar seu modelos de aprendizado de máquina. Compare esses produtos e escolha o que você precisa para desenvolver com mais eficiência soluções de aprendizado de máquina.


**Opções baseadas em nuvem**

As seguintes opções estão disponíveis para o aprendizado de máquina na nuvem do Azure.

| Opções&nbsp;de nuvem | O que é | O que você pode fazer com ele |
|-|-|-|
| [Serviço do Azure Machine Learning](#azure-machine-learning-service) | O serviço de nuvem gerenciado para ML  | Treinar, implantar e gerenciar modelos no Azure usando o Python e a CLI |
| [Azure Machine Learning Studio](#azure-machine-learning-studio) | Interface visual do tipo arrastar&ndash;e&ndash;soltar para ML | Criar, experimentar e implantar modelos usando algoritmos pré-configurados (Python e R)|
| [Azure Databricks](#azure-databricks) | Plataforma de análise com base no Spark | Crie e implante modelos e fluxos de trabalho de dados |
| [Serviços Cognitivos do Azure](#azure-cognitive-services) | Serviços do Azure com modelos predefinidos de ML e IA | Adicione facilmente recursos inteligentes aos seus aplicativos |
| [Máquina Virtual de Ciência de Dados do Azure](#azure-data-science-virtual-machine) | Máquina virtual com as ferramentas de ciência de dados previamente instaladas | Desenvolver soluções de ML em um ambiente de pré-configurado |

**Opções locais**

As opções a seguir estão disponíveis para aprendizado de máquina local. Os servidores locais também podem ser executados em uma máquina virtual na nuvem.

| Opções&nbsp;locais | O que é | O que você pode fazer com ele |
|-|-|-|
| [Serviços de Machine Learning do SQL Server](#sql-server-machine-learning-services) | Mecanismo de análise inserido em SQL | Compilar e implantar modelos dentro do SQL Server |
| [Microsoft Machine Learning Server](#microsoft-machine-learning-server) | Servidor empresarial autônomo para análise de previsão | Compilar e implantar modelos com R e Python |

**Ferramentas de desenvolvimento**

As ferramentas de desenvolvimento a seguir estão disponíveis para aprendizado de máquina.

| Ferramentas&nbsp;de desenvolvimento | O que é | O que você pode fazer com ele |
|-|-|-|
| [ML.NET](#mlnet) | Código-fonte aberto, SDK de ML multiplataforma | Desenvolver soluções de ML para aplicativos .NET |
| [Windows ML](#windows-ml) | Plataforma do Windows 10 ML | Avaliar modelos treinados em um dispositivo Windows 10 |







## <a name="azure-machine-learning-service"></a>Serviço do Azure Machine Learning

O [serviço do Azure Machine Learning](overview-what-is-azure-ml.md) é um serviço de nuvem totalmente gerenciado usado para treinar, implantar e gerenciar modelos de ML em escala. Ele dá suporte total a tecnologias de software livre, portanto, você pode usar dezenas de milhares de pacotes do Python de software livre, como TensorFlow, PyTorch e scikit-learn. Ferramentas avançadas também estão disponíveis, como [Azure Notebooks](https://notebooks.azure.com/), [Jupyter Notebooks](http://jupyter.org) ou a extensão do [Azure Machine Learning para Visual Studio Code](https://aka.ms/vscodetoolsforai) para facilitar a exploração e a transformação de dados e, posteriormente, o treinamento e a implantação de modelos. Os serviços do Azure Machine Learning incluem recursos que automatizam a geração e o ajuste de modelo com facilidade, eficiência e precisão.

Use os serviços do Azure Machine Learning para treinar, implantar e gerenciar modelos de ML usando Python e CLI em escala de nuvem.

Experimente a [versão gratuita ou paga do Serviço do Azure Machine Learning](http://aka.ms/AMLFree) hoje mesmo.

## <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio

[O Azure Machine Learning Studio](../studio/what-is-ml-studio.md) oferece um workspace visual e interativo que você pode usar para compilar, testar e implantar modelos usando algoritmos de aprendizado de máquina predefinidos com rapidez e facilidade. O Machine Learning Studio publica modelos como serviços Web que podem ser facilmente consumidos por aplicativos personalizados ou ferramentas de BI como o Excel.
Nenhuma programação é necessária: você constrói seu modelo de machine learning conectando conjuntos de dados e módulos de análise em uma tela interativa e, em seguida, implanta-o com alguns cliques.

Use o Machine Learning Studio quando quiser desenvolver e implantar modelos sem exigir nenhum código.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="azure-databricks"></a>Azure Databricks

O [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks) é uma plataforma de análise baseada no Apache Spark otimizada para a plataforma de Serviços de Nuvem do Microsoft Azure. O Databricks é integrado com o Azure para fornecer instalação com um clique, fluxos de trabalho simplificados e um workspace interativo que permite a colaboração entre os cientistas de dados, os engenheiros de dados e os analistas empresariais.
Use código R, Python, Scala e SQL em blocos de anotações baseado na Web para consultar, visualizar e modelar dados.

Use o Databricks quando quiser colaborar na criação de soluções de aprendizado de máquina no Apache Spark.

## <a name="azure-cognitive-services"></a>Serviços Cognitivos do Azure

Os [Serviços Cognitivos do Azure](/azure/cognitive-services/welcome) são um conjunto de APIs que permitem criar aplicativos que usam métodos naturais de comunicação. Essas APIs permitem que seus aplicativos vejam, ouçam, falem, entendam e interpretem as necessidades dos usuários com apenas algumas linhas de código. Adicione facilmente recursos inteligentes para seus aplicativos, tais como: 

- Detecção de emoção e sentimento
- Visão e reconhecimento de fala
- LUIS (Reconhecimento vocal)
- Conhecimento e pesquisa

Use Serviços Cognitivos para desenvolver aplicativos entre dispositivos e plataformas. As APIs estão em constante aperfeiçoamento e são fáceis de configurar.

## <a name="azure-data-science-virtual-machine"></a>Máquina Virtual da Ciência de Dados do Azure

A [Máquina Virtual de Ciência de Dados do Azure](../data-science-virtual-machine/overview.md) é um ambiente de máquina virtual personalizada na nuvem do Microsoft Azure, especificamente criada para ciência de dados. Ela tem muitas ferramentas conhecidas de ciência de dados, entre outras, pré-instaladas e pré-configuradas que ajudam a começar a criar rapidamente aplicativos inteligentes para análise avançada.

Há suporte para a Máquina Virtual de Ciência de Dados como um destino para os serviços do Azure Machine Learning.
Está disponível nas versões para Windows e Linux Ubuntu (não há suporte para os serviços do Azure Machine Learning no Linux CentOS).
Para obter informações de versão específica e uma lista do que foi incluído, veja [Introdução à Máquina Virtual de Ciência de Dados do Azure](../data-science-virtual-machine/overview.md).

Use a VM de Ciência de Dados quando você precisar executar ou hospedar seus trabalhos em um único nó. Ou então, se você precisar expandir remotamente o processamento em um único computador.

## <a name="sql-server-machine-learning-services"></a>Serviços de Machine Learning do SQL Server

O [Serviço do Microsoft Machine Learning para SQL Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-services) adiciona análise estatística, visualização de dados e análise preditiva em R e Python para dados relacionais em bancos de dados do SQL Server. Bibliotecas em R e Python da Microsoft incluem modelagem avançada e algoritmos de ML que podem ser executados em paralelo e em escala no SQL Server.

Use os Serviços de Machine Learning do SQL Server quando precisar de IA integrada e análise preditiva em dados relacionais no SQL Server.

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server

[O Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server) é um servidor empresarial para hospedagem e gerenciamento de cargas de trabalho paralelas e distribuídas de processos do R e do Python. O Microsoft Machine Learning Server é executado em Windows, Linux, Hadoop e Apache Spark e também está disponível no [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/). Ele fornece um mecanismo de execução para soluções criadas usando [pacotes RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) e [MicrosoftML](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package), e estende R e Python de software livre com suporte para análise de alto desempenho, análise estatística, aprendizado de máquina e conjuntos de dados realmente grandes. Essa funcionalidade agregada é fornecida por meio de pacotes de proprietários que são instalados com o servidor. Para desenvolvimento, você pode usar IDEs como [Ferramentas do R para Visual Studio](https://www.visualstudio.com/vs/rtvs/) e [Ferramentas Python para Visual Studio](https://www.visualstudio.com/vs/python/).

Use o Microsoft Machine Learning Server quando precisar compilar e operacionalizar modelos criados com R e Python em um servidor ou distribuir o treinamento de R e Python em grande escala em um cluster Hadoop ou Spark.

## <a name="mlnet"></a>ML.NET

[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/) é uma estrutura de aprendizado de máquina gratuita, de software livre e multiplataforma que permite que você crie soluções de aprendizado de máquina personalizadas e integre-as em seus aplicativos .NET.

Use ML.NET quando quiser integrar soluções de aprendizado de máquina a seus aplicativos .NET.

## <a name="windows-ml"></a>Windows ML

[Windows ML](https://docs.microsoft.com/windows/uwp/machine-learning/) permite que você use modelos de aprendizado de máquina treinados em seus aplicativos, avaliando modelos treinados localmente em dispositivos Windows 10.

Use o Windows ML quando quiser usar modelos de aprendizado de máquina treinados em seus aplicativos do Windows.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre todos os produtos de desenvolvimento de IA (Inteligência Artificial) disponíveis na Microsoft, veja [Plataforma de IA da Microsoft](https://www.microsoft.com/ai)
- Para obter treinamento sobre como desenvolver soluções de IA, veja a [Microsoft AI School](https://aischool.microsoft.com/learning-paths)
