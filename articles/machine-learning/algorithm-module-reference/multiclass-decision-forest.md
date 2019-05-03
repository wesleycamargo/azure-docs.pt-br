---
title: 'Floresta de decisão multiclasse: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo de floresta de decisão Multiclasse no serviço de Azure Machine Learning para criar um modelo de machine learning com base nas *floresta de decisão* algoritmo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: d5ff1809886198f7f1e4f4b9c36f877ce61d512e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029424"
---
# <a name="multiclass-decision-forest-module"></a>Módulo de floresta de decisão multiclasse

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para criar um modelo de machine learning com base nas *floresta de decisão* algoritmo. Uma floresta de decisão é um modelo de ensemble que cria rapidamente uma série de árvores de decisão, ao mesmo tempo aprendendo com dados marcados.

## <a name="more-about-decision-forests"></a>Mais informações sobre as florestas de decisão

O algoritmo de floresta de decisão é um método de classificação de aprendizado de ensemble. O algoritmo funciona criando várias árvores de decisão e, em seguida *votação* na classe de saída mais popular. Votação é uma forma de agregação, no qual cada árvore em uma decisão de classificação floresta produz um histograma de frequência não normalizado de rótulos. O processo de agregação resume essas histogramas e normaliza o resultado para obter as probabilidades de"" para cada rótulo. As árvores que têm confiança alta previsão tem um peso maior na decisão final do ensemble.

Em geral, as árvores de decisão são modelos não paramétricos, significando que dão suporte a dados com distribuições variadas. Em cada árvore, uma sequência de testes simples é executada para cada classe, aumentando os níveis de uma estrutura de árvore até que um nó folha (decisão) seja atingido.

Árvores de decisão têm muitas vantagens:

+ Eles podem representar limites de decisão não lineares.
+ São eficientes no uso de memória e computação durante o treinamento e previsão.
+ Eles executam a seleção de recursos integrados e classificação.
+ Elas são flexíveis na presença de recursos com ruídos.

O classificador de floresta de decisão no Azure Machine Learning consiste em um ensemble de árvores de decisão. Em geral, modelos de ensemble fornecem melhor cobertura e precisão que árvores de decisão única. Para obter mais informações, consulte [árvores de decisão](http://go.microsoft.com/fwlink/?LinkId=403677).

## <a name="how-to-configure-multiclass-decision-forest"></a>Como configurar a floresta de decisão Multiclasse



1. Adicione a **floresta de decisão Multiclasse** módulo à sua experiência na interface. Você pode encontrar esse módulo sob **Machine Learning**, **inicializar modelo**, e **classificação**.

2. Clique duas vezes no módulo para abrir o **propriedades** painel.

3. Para **método de reamostragem**, escolha o método usado para criar as árvores individuais.  Você pode escolher entre bagging ou replicação.

    + **Bagging**: Também é chamado de bagging *agregando bootstrap*. Nesse método, cada árvore crescer em uma nova amostra, criada por um conjunto de dados original com a substituição de amostragem aleatoriamente até que você tenha um conjunto de dados, o tamanho do original. As saídas dos modelos são combinadas por *votação*, que é uma forma de agregação. Para obter mais informações, consulte a entrada da Wikipedia para agregar Bootstrap.

    + **Replicar**: Na replicação, cada árvore é treinado em exatamente os mesmos dados de entrada. A determinação de qual dividir o predicado é usado para cada nó de árvore permanece aleatória, criando árvores diferentes.

   

4. Especifique como deseja que o modelo ser treinado, definindo o **criar modo de treino** opção.

    + **Um único parâmetro**: Selecione esta opção se você souber como deseja configurar o modelo e fornecer um conjunto de valores como argumentos.


5. **Número de árvores de decisão**: Digite o número máximo de árvores de decisão que podem ser criados no ensemble. Criando mais árvores de decisão, você pode potencialmente obter melhor cobertura, mas pode aumentar o tempo de treinamento.

    Esse valor também controla o número de árvores exibida nos resultados, ao visualizar o modelo treinado. Para ver ou imprimir uma única árvore, você pode definir o valor como 1; No entanto, isso significa que somente uma única árvore pode ser produzidos (a árvore com o conjunto inicial de parâmetros), e nenhuma iteração adicional é executadas.

6. **A profundidade máxima das árvores de decisão**: Digite um número para limitar a profundidade máxima de qualquer árvore de decisão. Aumentar a profundidade da árvore pode aumentar a precisão, com o risco de algum tempo de treinamento overfitting e maior.

7. **Número de divisões aleatórias por nó**: Digite o número de divisões a ser usado ao criar cada nó da árvore. Um *dividir* significa que recursos em cada nível da árvore (nó) é divididas aleatoriamente.

8. **Número mínimo de amostras por nó folha**: Indique o número mínimo de casos que são necessárias para criar qualquer nó terminal (folha) em uma árvore. Ao aumentar esse valor, você aumenta o limite para a criação de novas regras.

    Por exemplo, com o valor padrão de 1, até mesmo um único caso pode causar uma nova regra a ser criado. Se você aumentar o valor para 5, os dados de treinamento precisam conter pelo menos cinco casos que atendem as mesmas condições.



10. Conecte-se de um conjunto de dados rotulado e um dos módulos de treinamento:

    + Se você definir **criar modo de treino** à **único parâmetro**, use o [modelo de treinamento](./train-model.md) módulo.

11. Execute o experimento.

## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para ver a árvore que foi criada em cada iteração, clique com botão direito a saída a [modelo de treinamento](./train-model.md) módulo e selecione **visualizar**.
+ Para ver as regras para cada nó, clique em cada árvore para detalhar as divisões.


## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 