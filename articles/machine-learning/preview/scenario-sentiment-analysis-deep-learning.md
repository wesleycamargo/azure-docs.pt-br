---
title: "Análise de Sentimento usando Aprendizado profundo com o Azure Machine Learning | Microsoft Docs"
description: "Como realizar a análise de sentimento usando o aprendizado profundo com o Azure ML Workbench."
services: machine-learning
documentationcenter: 
author: miprasad
manager: kristin.tolle
editor: miprasad
ms.assetid: 
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2018
ms.author: miprasad
ms.openlocfilehash: 3b0a5bfc911f3edf91367cbf4fde907cbf98e114
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="sentiment-analysis-using-deep-learning-with-azure-machine-learning"></a>Análise de Sentimento usando Aprendizado profundo com o Azure Machine Learning

A análise de sentimento é uma tarefa conhecida no realm do processamento de linguagem natural. Considerando um conjunto de textos, o objetivo é determinar o sentimento desse texto. O objetivo dessa solução é usar o Aprendizado profundo para prever o sentimento de resenhas do filme.

A solução está localizada em https://github.com/Azure/MachineLearningSamples-SentimentAnalysis

## <a name="link-to-the-gallery-github-repository"></a>Link para o repositório GitHub da galeria

Siga este link para o repositório GitHub público:

[https://github.com/Azure/MachineLearningSamples-SentimentAnalysis](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis)

## <a name="use-case-overview"></a>Visão geral de casos de uso

A explosão de dados e a proliferação de dispositivos móveis criou muitas oportunidades para os clientes expressarem seus sentimentos e atitudes sobre tudo a qualquer momento. Esse "sentimento" ou opinião geralmente é gerado por meio de canais sociais na forma de revisões, bate-papos, compartilhamentos, curtidas, tweets etc. O sentimento pode ser inestimável para empresas que desejam aprimorar produtos e serviços, tomar decisões mais fundamentadas e promover melhor suas marcas.

Para obter o valor da análise de sentimento, as empresas devem ter a capacidade de explorar grandes repositórios de dados sociais não estruturados com relação a ideias acionáveis. Neste exemplo, desenvolveremos modelos de aprendizado profundo para realizar análises de sentimento de revisões de filme usando o AMLWorkbench

## <a name="prerequisites"></a>pré-requisitos

* Uma [conta do Azure](https://azure.microsoft.com/free/) (avaliações gratuitas estão disponíveis).

* Uma cópia instalada do [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) seguindo o [guia de instalação de início rápido](./quickstart-installation.md) para instalar o programa e criar um espaço de trabalho.

* Para a operacionalização, será melhor se você tiver um mecanismo do Docker instalado e em execução localmente. Caso contrário, não será possível usar a opção de cluster. No entanto, a execução de ACS (Serviço de Contêiner do Azure) pode ser cara.

* Esta solução pressupõe que você esteja executando o Azure Machine Learning Workbench no Windows 10 com o mecanismo do Docker instalado localmente. Em um macOS, a instrução é basicamente a mesma.

## <a name="data-description"></a>Descrição dos dados

O conjunto de dados usado para este exemplo é um conjunto de dados artesanal pequeno e está localizado na [pasta de dados](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/tree/master/data).

A primeira coluna contém revisões de filme e a segunda coluna contém o sentimento (0 – negativo e 1 – positivo). O conjunto de dados é usado apenas para fins de demonstração, mas normalmente para obter as pontuações de sentimento robusto, é necessário um grande conjunto de dados. Por exemplo, o [problema de classificação de sentimento de revisões do IMDB Movie](https://keras.io/datasets/#datasets ) do Keras consiste em um conjunto de dados de 25.000 revisões de filmes do IMDB, rotuladas pelo sentimento (positivo ou negativo). A intenção deste laboratório é mostrar como realizar a análise de sentimento usando o aprendizado profundo com o AMLWorkbench.

## <a name="scenario-structure"></a>Estrutura do cenário

A estrutura de pastas é organizada da seguinte maneira:

1. Todo o código relacionado à análise de sentimento usando o AMLWorkbench está na pasta raiz
2. dados: contém o conjunto de dados usado na solução
3. docs: contém todos os laboratórios práticos

A ordem dos Laboratórios práticos para executar a solução é a seguinte:

| Classificar| Nome do Arquivo | Arquivos relacionados |
|--|-----------|------|
| 1 | [`SentimentAnalysisDataPreparation.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisDataPreparation.md) | 'data/sampleReviews.txt' |
| 2 | [`SentimentAnalysisModelingKeras.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisModelingKeras.md) | 'SentimentExtraction.py' |
| 4 | [`SentimentAnalysisOperationalization.md`](https://github.com/Azure/MachineLearningSamples-SentimentAnalysis/blob/master/docs/SentimentAnalysisOperationalization.md) | 'Operaionalization' |

## <a name="conclusion"></a>Conclusão

Portanto, essa solução apresenta o uso do Aprendizado profundo para realizar análise de sentimento com o Azure Machine Learning Workbench. Também é possível operar usando modelos do HDF5.
