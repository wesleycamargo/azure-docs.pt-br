---
title: 'Treinar o modelo de Clustering: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo treinar modelo de Clustering no serviço do Azure Machine Learning para treinar modelos de clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 41cdec1d7f1c3932b17da6f9b1de518071f3f542
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028074"
---
# <a name="train-clustering-model"></a>Treinar o modelo de Clustering

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para treinar um modelo de clustering.

O módulo usa um modelo de clustering não treinado que você já tiver configurado usando o [Clustering K-Means](k-means-clustering.md) módulo e treina o modelo usando um conjunto de dados rotulado ou sem rótulo. O módulo cria os dois um modelo treinado que você pode usar para previsão e um conjunto de atribuições de cluster para cada caso nos dados de treinamento.

> [!NOTE]
> Um cluster não foi possível do modelo ser treinado usando o [modelo de treinamento](train-model.md) módulo, que é o módulo genérico de treinamento de modelos de aprendizado de máquina. Isso ocorre porque [modelo de treinamento](train-model.md) funciona apenas com algoritmos de aprendizado supervisionados. K-means e outros algoritmos de clustering permitem aprendizado sem supervisão, o que significa que o algoritmo possa aprender com dados sem rótulo.  
  
## <a name="how-to-use-train-clustering-model"></a>Como usar o modelo de Clustering de treinamento  
  
1.  Adicione a **treinar modelo de Clustering** módulo à sua experiência no Studio. Você pode encontrar o módulo sob **módulos de aprendizado de máquina**, no **Train** categoria.  
  
2. Adicione a [Clustering K-Means](k-means-clustering.md) módulo ou outro módulo personalizado que cria um clustering compatível de modelo e defina os parâmetros do modelo de clustering.  
    
3.  Anexar um conjunto de dados de treinamento a entrada à direita do **treinar modelo de Clustering**.
  
5.  Na **coluna definida**, selecione as colunas do conjunto de dados para usar na criação de clusters. Certifique-se de selecionar colunas que fazem bons recursos: por exemplo, evite usar IDs ou outras colunas que têm valores exclusivos ou colunas que têm os mesmos valores.

    Se um rótulo estiver disponível, você pode usá-lo como um recurso ou deixá-lo.  
  
6. Selecione a opção **verificar se há acréscimo ou desmarque para somente resultado**, se você deseja gerar os dados de treinamento, junto com o novo rótulo de cluster.

    Se você desmarcar essa opção, somente as atribuições de cluster são de saída. 

7. Execute o teste, ou clique no **treinar modelo de Clustering** módulo e selecione **executar selecionado**.  
  
### <a name="results"></a>Resultados

Após o treinamento foi concluída:


+  Para exibir os valores no conjunto de dados, o módulo com o botão direito, selecione **resultar datasets**e clique em **visualizar**.

+ Para salvar o modelo treinado para reutilização posterior, o módulo com o botão direito, selecione **modelo treinados**e clique em **Salvar como modelo treinado**.

+ Para gerar pontuações do modelo, use [atribuir dados aos Clusters](assign-data-to-clusters.md).



## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 