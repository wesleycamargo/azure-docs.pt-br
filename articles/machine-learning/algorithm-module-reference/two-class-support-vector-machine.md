---
title: 'Máquina de vetor de suporte de duas classes: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o **máquina de vetor de suporte de duas classes** módulo no serviço de Azure Machine Learning para criar um modelo com base no algoritmo de máquina do vetor de suporte.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2f076dd3a5b1ceb9e24548652a71fda5b9aa48b7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027924"
---
# <a name="two-class-support-vector-machine-module"></a>Módulo de máquina de vetor de suporte de duas classes

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para criar um modelo com base no algoritmo de máquina do vetor de suporte. 

Máquinas de vetor de suporte (SVMs) são uma classe bem pesquisada dos métodos de aprendizado supervisionado. Essa implementação particular é adequada para previsão de dois resultados possíveis, com base em variáveis contínuas ou categóricas.

Depois de definir os parâmetros de modelo, treinar o modelo usando os módulos de treinamento e fornecendo um *marcados dataset* que inclui uma coluna de rótulo ou de resultado.

## <a name="about-support-vector-machines"></a>Sobre as máquinas de vetor de suporte

Máquinas de vetor de suporte estão entre os mais recentes dos algoritmos de aprendizado de máquina e modelos SVM foram usados em muitos aplicativos, da recuperação de informações para a classificação de texto e imagem. SVMs podem ser usados para tarefas de classificação e regressão.

Esse modelo SVM é um modelo de aprendizado supervisionado que requer dados rotulados. No processo de treinamento, o algoritmo analisa os dados de entrada e reconhece padrões em um espaço de recurso multidimensional chamada a *hiperplano*.  Todos os exemplos de entrada são representados como pontos neste espaço e são mapeados para as categorias de tal forma que categorias são divididas por da sua largura e desmarque uma lacuna possível de saída.

Para previsão, o algoritmo SVM atribui novos exemplos a uma categoria ou a outra, mapeando-os nesse mesmo espaço. 

## <a name="how-to-configure"></a>Como configurar 

Para esse tipo de modelo, é recomendável que você normalize o conjunto de dados antes de usá-lo para treinar o classificador.
  
1.  Adicione a **máquina de vetor de suporte de duas classes** módulo ao seu experimento.  
  
2.  Especifique como deseja que o modelo ser treinado, definindo o **criar modo de treino** opção.  
  
    -   **Um único parâmetro**: Se você souber como deseja configurar o modelo, você pode fornecer um conjunto específico de valores como argumentos.  

3.  Para **número de iterações**, digite um número que indica o número de iterações usadas ao criar o modelo.  
  
     Esse parâmetro pode ser usado para controlar a compensação entre a precisão e a velocidade de treinamento.  
  
4.  Para **Lambda**, digite um valor a ser usado como o peso para regularização L1.  
  
     Esse coeficiente de regularização pode ser usado para ajustar o modelo. Valores maiores penalizam modelos mais complexos.  
  
5.  Selecione a opção **normalizar recursos**, se você deseja normalizar recursos antes do treinamento.
  
     Se você aplicar a normalização, antes do treinamento, pontos de dados são centralizados na média e dimensionados para ter uma unidade de desvio padrão.
  
6.  Selecione a opção **projeto para a esfera de unidade**para normalizar os coeficientes.
  
     Projetar valores ao espaço da unidade significa que, antes do treinamento, pontos de dados são centralizados em 0 e dimensionados para ter uma unidade de desvio padrão.
  
7.  Na **propagação de número aleatório**, digite um valor inteiro para usar como uma semente se você quiser garantir reprodutibilidade em execuções.  Caso contrário, um valor de relógio do sistema é usado como semente, o que pode resultar em resultados ligeiramente diferentes entre execuções.
  
9. Conectar-se de um conjunto de dados rotulado e uma da [módulos de treinamento](module-reference.md):
  
    -   Se você definir **criar modo de treino** à **único parâmetro**, use o [modelo de treinamento](train-model.md) módulo.
  

10. Execute o experimento.

## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para ver um resumo dos parâmetros do modelo, juntamente com os pesos de recurso que aprendi com treinamento, clique com botão direito a saída de [modelo de treinamento](./train-model.md)e selecione **visualizar**.

+ Para usar os modelos treinados para fazer previsões, conecte-se o modelo treinado para o [modelo de pontuação](score-model.md) módulo.


## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 