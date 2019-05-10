---
title: 'Floresta de decisão de duas classes: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo de floresta de decisão de duas classes no serviço de Azure Machine Learning para criar um modelo com base no algoritmo de florestas de decisão de aprendizado de máquina.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 73b7822c56e2b07eeefdedce1bce6d410d110ebc
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411498"
---
# <a name="two-class-decision-forest-module"></a>Módulo de floresta de decisão de duas classes

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para criar um modelo com base no algoritmo de florestas de decisão de aprendizado de máquina.  

Florestas de decisão são modelos de ensemble rápidos e supervisionados. Esse módulo é uma boa opção se você quiser prever um destino com um máximo de dois resultados. 

## <a name="understanding-decision-forests"></a>Noções básicas sobre florestas de decisão

Esse algoritmo de floresta de decisão é um método que se destina para tarefas de classificação de aprendizado de ensemble. Métodos de Ensemble baseiam-se no princípio geral que, em vez de contar com um único modelo, você pode obter resultados melhores e um modelo mais generalizado criando vários modelos relacionados e combiná-los de alguma forma. Em geral, modelos de ensemble fornecem melhor cobertura e precisão que árvores de decisão única. 

Há muitas maneiras de criar modelos individuais e combiná-los em um ensemble. Nesta implementação específica de uma floresta de decisão funciona criando várias árvores de decisão e, em seguida **votação** na classe de saída mais popular. Votação é um dos métodos para gerar os resultados em um modelo de ensemble conhecidos. 

+ Várias árvores de classificação individuais são criados, usando o conjunto de dados inteiro, mas diferentes (normalmente executada de forma aleatória) pontos de partida. Isso é diferente da abordagem de floresta aleatória, em que as árvores de decisão individual podem usar apenas uma parte aleatória dos dados ou recursos.
+ Cada árvore na árvore de floresta de decisão produz um histograma de frequência não normalizado de rótulos. 
+ O processo de agregação resume essas histogramas e normaliza o resultado para obter as probabilidades de"" para cada rótulo. 
+ As árvores que têm confiança alta previsão terá um peso maior na decisão final do ensemble.

Em geral, as árvores de decisão têm muitas vantagens para tarefas de classificação:
  
- Eles podem capturar os limites de decisão não lineares.
- Você pode treinar e prever em grandes quantidades de dados, conforme eles são eficientes no uso de memória e computação.
- Seleção de recursos é integrada nos processos de classificação e treinamento.  
- Árvores podem acomodar dados com ruídos e muitos recursos.  
- Eles são modelos não paramétricos, que significa que eles possam lidar com dados com distribuições variadas. 

No entanto, as árvores de decisão simples podem sobreajustar nos dados e são menos generalizável que conjuntos de árvore.

Para obter mais informações, consulte [florestas de decisão](https://go.microsoft.com/fwlink/?LinkId=403677).  

## <a name="how-to-configure"></a>Como configurar
  
1.  Adicione a **floresta de decisão de duas classes** módulo à sua experiência no Azure Machine Learning e abra o **propriedades** painel do módulo. 

    Você pode encontrar o módulo sob **Machine Learning**. Expandir **inicializar**e então **classificação**.  
  
2.  Para **método de reamostragem**, escolha o método usado para criar as árvores individuais.  Você pode escolher entre **Bagging** ou **replicar**.  
  
    -   **Bagging**: Também é chamado de bagging *agregando bootstrap*. Nesse método, cada árvore crescer em uma nova amostra, criada por um conjunto de dados original com a substituição de amostragem aleatoriamente até que você tenha um conjunto de dados, o tamanho do original.  
  
         As saídas dos modelos são combinadas por *votação*, que é uma forma de agregação. Cada árvore em uma floresta de decisão de classificação produz um histograma de frequência não normalizado de rótulos. A agregação é a soma desses histogramas e normalizar para obter as probabilidades de"" para cada rótulo. Dessa forma, as árvores que têm confiança alta previsão terá um peso maior na decisão final do ensemble.  
  
         Para obter mais informações, consulte a entrada da Wikipedia para agregar Bootstrap.  
  
    -   **Replicar**: Na replicação, cada árvore é treinado em exatamente os mesmos dados de entrada. A determinação de qual dividir o predicado é usado para cada nó de árvore permanece aleatória e árvores será variadas.   
  
3.  Especifique como deseja que o modelo ser treinado, definindo o **criar modo de treino** opção.  
  
    -   **Um único parâmetro**: Se você souber como deseja configurar o modelo, você pode fornecer um conjunto específico de valores como argumentos.
  
4.  Para **número de árvores de decisão**, digite o número máximo de árvores de decisão que podem ser criados no ensemble. Criando mais árvores de decisão, você pode potencialmente obter melhor cobertura, mas aumenta o tempo de treinamento.  
  
    > [!NOTE]
    >  Esse valor também controla o número de árvores exibidas ao visualizar o modelo treinado. Se você quiser ver ou imprimir uma única árvore, você pode definir o valor como 1. No entanto, somente uma única árvore pode ser produzidos (a árvore com o conjunto inicial de parâmetros) e nenhuma iteração adicional é executadas.
  
5.  Para **profundidade máxima das árvores de decisão**, digite um número para limitar a profundidade máxima de qualquer árvore de decisão. Aumentar a profundidade da árvore pode aumentar a precisão, com o risco de algum tempo de treinamento overfitting e maior.
  
6.  Para **número de divisões aleatórias por nó**, digite o número de divisões a ser usado ao criar cada nó da árvore. Um *dividir* significa que recursos em cada nível da árvore (nó) é divididas aleatoriamente.
  
7.  Para **o número mínimo de amostras por nó folha**, indicam o número mínimo de casos que são necessárias para criar qualquer nó terminal (folha) em uma árvore.
  
     Ao aumentar esse valor, você aumenta o limite para a criação de novas regras. Por exemplo, com o valor padrão de 1, até mesmo um único caso pode causar uma nova regra a ser criado. Se você aumentar o valor para 5, os dados de treinamento precisam conter pelo menos cinco casos que atendem as mesmas condições.  
  
8.  Selecione o **permitir valores desconhecidos para recursos categóricos** opção para criar um grupo de valores desconhecidos em conjuntos de treinamento ou de validação. O modelo poderá ser menos preciso para valores conhecidos, mas ele pode fornecer melhores previsões para novos valores (desconhecidos). 

     Se você desmarcar essa opção, o modelo pode aceitar apenas os valores que estão contidos nos dados de treinamento.
  
9. Anexar um conjunto de dados rotulado e uma da [módulos de treinamento](module-reference.md):  
  
    -   Se você definir **criar modo de treino** à **único parâmetro**, use o [modelo de treinamento](./train-model.md) módulo.  
  
    
## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para ver a árvore que foi criada em cada iteração, clique com botão direito a saída a [modelo de treinamento](./train-model.md) módulo e selecione **visualizar**.
  
    Clique em cada árvore para detalhar as divisões e ver as regras para cada nó.

+ Para salvar um instantâneo do modelo, clique com botão direito do **treinado** de saída e, em seguida, selecione **Salvar modelo**. O modelo salvo não é atualizado em execuções sucessivas do experimento.

+ Para usar o modelo de pontuação, adicione a **modelo de pontuação** módulo para um experimento.


## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 