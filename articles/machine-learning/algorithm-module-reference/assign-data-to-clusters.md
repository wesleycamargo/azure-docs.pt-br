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
ms.openlocfilehash: b370c6ef5be311ed9c8df2737fa1b01144d61011
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028764"
---
# <a name="assign-data-to-clusters"></a>Atribuir dados aos Clusters

Este artigo descreve como usar o [atribuir dados aos Clusters](assign-data-to-clusters.md) módulo na interface visual, para gerar previsões usando um modelo de clustering foi treinado usando o algoritmo de clustering K-Means.

O módulo retorna um conjunto de dados que contém as atribuições prováveis para cada novo ponto de dados. 


## <a name="how-to-use-assign-data-to-clusters"></a>Como usar atribuir dados aos Clusters
  
1.  Na interface visual, localize um modelo de clustering treinado anteriormente. Você pode criar e treinar um modelo de clustering usando qualquer um desses métodos:  
  
    - Configurar o algoritmo K-means usando o [Clustering K-Means](k-means-clustering.md) módulo e, em seguida, treinar o modelo usando um conjunto de dados e o [treinar o modelo de Clustering](train-clustering-model.md) módulo.  
  
  
    Você também pode adicionar um modelo de clustering treinado existente do **modelos salvos** grupo em seu espaço de trabalho.

2. Anexar o modelo treinado para a porta de entrada esquerda [atribuir dados aos Clusters](assign-data-to-clusters.md).  

3. Anexe um novo conjunto de dados como entrada. Esse conjunto de dados, os rótulos são opcionais. Em geral, o clustering é um método de aprendizado sem supervisão para que não é esperado que você saberá categorias com antecedência.

    No entanto, as colunas de entrada devem ser o mesmo que as colunas que foram usadas no treinamento que do modelo de clustering, ou ocorrerá um erro.

    > [!TIP]
    > Para reduzir o número de colunas de saída de previsões de cluster, use [selecionar colunas no conjunto de dados](select-columns-in-dataset.md)e selecionar um subconjunto das colunas. 
    
4. Deixe a opção **verificar se há acréscimo ou desmarque para somente resultado** selecionada se você deseja que os resultados para conter o conjunto completo de entradas, junto com uma coluna que indica os resultados (atribuições de cluster).
  
    Se você desmarcar essa opção, você obter apenas os resultados de volta. Isso pode ser útil durante a criação de previsões como parte de um serviço web.
  
5.  Execute o experimento.  
  
### <a name="results"></a>Resultados

 
+  Para exibir os valores no conjunto de dados, o módulo com o botão direito, selecione **resultar datasets**e clique em **visualizar**.

