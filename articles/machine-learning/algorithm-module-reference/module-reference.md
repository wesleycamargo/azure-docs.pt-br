---
title: Referência de módulo e algoritmo
titleSuffix: Azure Machine Learning service
description: Saiba mais sobre os módulos disponíveis na interface visual do Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 6602eb4bacdc3b6382c1b6873a465cdfc0632693
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029334"
---
# <a name="algorithm--module-reference-overview"></a>Visão geral de referência de módulo e algoritmos

O conteúdo de referência fornece experiência técnica em cada um dos módulos disponíveis na interface do visual (visualização) do serviço Azure Machine Learning e algoritmos de aprendizado de máquina. 

Cada módulo representa um conjunto de códigos que podem executar de forma independente e executar um machine learning tarefa, considerando as entradas necessárias. Um módulo pode conter um algoritmo específico, ou executar uma tarefa que é importante em aprendizado de máquina, como substituição de valor ausente ou análise estatística. 

> [!TIP]
> Em qualquer experimento na interface do visual, você pode obter informações sobre um módulo específico. Selecione o módulo e selecione o **mais ajuda** link na **ajuda rápida** painel.

Módulos são organizados por funcionalidade:

**Conversões de formato de dados**

  + [Converter para CSV ](convert-to-csv.md)

**Dados de entrada e saída módulos** fazem o trabalho de movimentação de dados de fontes de nuvem em seu teste. Você pode escrever seus resultados ou dados intermediários para armazenamento do Azure, um banco de dados SQL ou o Hive, durante a execução de um experimento ou usar o armazenamento em nuvem para trocar dados entre experimentos.  

  + [Importar Dados](import-data.md)

  + [Exportar Dados](export-data.md)

  + [Inserir os dados manualmente](enter-data-manually.md)


**Módulos de transformação de dados** dão suporte a operações em dados que são exclusivos ao machine learning, como normalização ou compartimentação de dados, a seleção de recursos e redução de dimensionalidade.

  + [Selecionar colunas no conjunto de dados](select-columns-in-dataset.md)

  + [Editar metadados](edit-metadata.md)

  + [Limpar dados ausentes](clean-missing-data.md)

  + [Adicionar colunas](add-columns.md)

  + [Adicionar linhas](add-rows.md)

  + [Remover linhas duplicadas](remove-duplicate-rows.md)

  + [Dividir os dados](split-data.md)

  + [Normalizar dados](normalize-data.md)

  + [Partição e exemplo](partition-and-sample.md)


**Algoritmos de aprendizado de máquina** como clustering, o computador de vetor de suporte ou redes neurais, estão disponíveis dentro de módulos individuais que permitem personalizar a tarefa de aprendizado de máquina com os parâmetros apropriados.  
  + [Modelo de pontuação](score-model.md)

  + [Atribuir dados aos Clusters ](assign-data-to-clusters.md)

  + [Modelo de treinamento](train-model.md)

  + [Treinar o modelo de Clustering](train-clustering-model.md)

  + [Avaliar modelo](evaluate-model.md)

  + [Aplicar transformação](apply-transformation.md)

  + [Regressão linear](linear-regression.md)

  + [Regressão de rede neural](neural-network-regression.md)

  + [Regressão de floresta de decisão](decision-forest-regression.md)

  + [Regressão de árvore de decisão aumentada](boosted-decision-tree-regression.md)

  + [Árvore de decisão aumentada de duas classes](two-class-boosted-decision-tree.md)

  + [Regressão logística de duas classes](two-class-logistic-regression.md)

  + [Regressão logística multiclasse](multiclass-logistic-regression.md)

  + [Rede Neural multiclasse](multiclass-neural-network.md)

  + [Floresta de decisão multiclasse](multiclass-decision-forest.md)

  + [De média Perceptron de duas classes](two-class-averaged-perceptron.md)

  + [Floresta de decisão de duas classes](two-class-decision-forest.md)

  + [Rede Neural de duas classes](two-class-neural-network.md)

  + [Máquina de vetor de suporte de duas classes](two-class-support-vector-machine.md)
  
  + [Clustering de K-Means](k-means-clustering.md)


**Módulo de Python** torna mais fácil executar uma função personalizada. Você escreve o código e inseri-lo em um módulo, a integrar o Python com um serviço de teste.
  + [Executar Script Python](execute-python-script.md)

  + [Criar modelo de Python](create-python-model.md)


