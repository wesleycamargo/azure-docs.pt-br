---
title: 'Aplica transformação: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo Aplicar transformação no serviço do Azure Machine Learning para modificar um conjunto de dados de entrada com base em uma transformação calculada.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 810f375642af49814049589cb83ad17fea578b13
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028704"
---
# <a name="apply-transformation-module"></a>Aplicar o módulo de transformação

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para modificar um conjunto de dados de entrada com base em uma transformação calculada.  
  
Por exemplo, se você tiver usado o z-scores para normalizar os dados de treinamento usando o **normalizar dados** módulo, você iria querer usar o valor da pontuação z que foi calculado para treinamento durante a fase de pontuação também. No Azure Machine Learning, você pode salvar o método de normalização como uma transformação e, em seguida, usando **Aplicar transformação** para aplicar a pontuação de z para os dados de entrada antes de pontuação.
  
O Azure Machine Learning fornece suporte para criar e, em seguida, aplicar muitos tipos diferentes de transformações personalizadas. Por exemplo, você talvez queira salvar e, em seguida, reutilizar as transformações para:  
  
- Remover ou substituir valores ausentes, usando **limpar dados ausentes**
- Normalizar dados, usando **normalizar dados**
  

## <a name="how-to-use-apply-transformation"></a>Como usar Aplicar transformação  
  
1. Adicione a **Aplicar transformação** módulo ao seu experimento. Você pode encontrar esse módulo sob **Machine Learning**, no **pontuação** categoria. 
  
2. Localize uma transformação existente para usar como entrada.  Transformações salvas anteriormente podem ser encontradas na **transforma** grupo no painel de navegação à esquerda.  
  
   
  
3. Conecte-se o conjunto de dados que você deseja transformar. O conjunto de dados deve ter exatamente o mesmo esquema (número de colunas, nomes de coluna, tipos de dados) que o conjunto de dados para o qual a transformação foi criada pela primeira vez.  
  
4. Nenhum outro parâmetro precisa ser definida, pois toda a personalização é feita ao definir a transformação.  
  
5. Para aplicar uma transformação ao novo conjunto de dados, execute o experimento.  

## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 