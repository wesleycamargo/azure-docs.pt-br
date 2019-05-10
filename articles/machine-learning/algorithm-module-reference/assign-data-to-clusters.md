---
title: 'Atribua dados ao Cluster: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar os dados de atribuir ao módulo de Cluster no serviço do Azure Machine Learning para pontuar o modelo de clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 1c2d2a02ecfb617551dd9174b87f363d57b151a8
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467209"
---
# <a name="module-assign-data-to-clusters"></a>Módulo: Atribuir dados aos Clusters

Este artigo descreve como usar o *atribuir dados aos Clusters* módulo na interface visual do Azure Machine Learning. O módulo gera previsões por meio de um modelo de clustering foi treinado com o *clustering K-means* algoritmo.

Os dados de atribuir ao módulo de Clusters retorna um conjunto de dados que contém as atribuições prováveis para cada novo ponto de dados. 


## <a name="how-to-use-assign-data-to-clusters"></a>Como usar atribuir dados aos Clusters
  
1. Na interface visual do Azure Machine Learning, localize um modelo de clustering treinado anteriormente. Você pode criar e treinar um modelo de clustering usando qualquer um dos seguintes métodos:  
  
    - Configurar o algoritmo de clustering K-means, usando o [Clustering K-Means](k-means-clustering.md) módulo e treinar o modelo usando um conjunto de dados e o módulo treinar modelo de Clustering (Este artigo).  
  
    - Você também pode adicionar um modelo de clustering treinado existente do **modelos salvos** grupo em seu espaço de trabalho.

2. Anexar o modelo treinado para a porta de entrada esquerda **atribuir dados aos Clusters**.  

3. Anexe um novo conjunto de dados como entrada. 

   Esse conjunto de dados, os rótulos são opcionais. Em geral, o clustering é um método de aprendizado sem supervisão. Você não deve saber com antecedência as categorias. No entanto, as colunas de entrada devem ser o mesmo que as colunas que foram usadas no treinamento que do modelo de clustering, ou ocorrerá um erro.

    > [!TIP]
    > Para reduzir o número de colunas que são gravados para a interface das previsões de cluster, use [selecionar colunas no conjunto de dados](select-columns-in-dataset.md)e selecionar um subconjunto das colunas. 
    
4. Deixe o **verificar se há acréscimo ou desmarque para somente resultado** caixa de seleção se você deseja que os resultados para conter o conjunto completo de entradas, incluindo uma coluna que exibe os resultados (atribuições de cluster).
  
    Se você desmarcar essa caixa de seleção, apenas os resultados são retornados. Essa opção pode ser útil quando você cria previsões como parte de um serviço web.
  
5.  Execute o experimento.  
  
### <a name="results"></a>Resultados

+  Para exibir os valores no conjunto de dados, o módulo com o botão direito, selecione **resultar datasets**e, em seguida, selecione **visualizar**.

