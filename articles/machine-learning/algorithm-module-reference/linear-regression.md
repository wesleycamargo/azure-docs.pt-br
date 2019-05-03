---
title: 'Regressão linear: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo de regressão Linear no serviço Azure Machine Learning para criar um modelo de regressão linear para uso em um experimento.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 040f726a703d34a95bae7d5b7cdd766655c62a4e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029709"
---
# <a name="linear-regression-module"></a>Módulo de regressão linear
Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para criar um modelo de regressão linear para uso em um experimento.  Regressão linear tenta estabelecer uma relação linear entre uma ou mais variáveis independentes e um resultado numérico ou a variável dependente. 

Você usa este módulo para definir um método de regressão linear e, em seguida, treinar um modelo usando um conjunto de dados rotulado. O modelo treinado, em seguida, pode ser usado para fazer previsões.

## <a name="about-linear-regression"></a>Sobre a regressão linear

Regressão linear é um método estatístico comum, o qual foi adotado no aprendizado de máquina e aprimorado com vários novos métodos para ajustar a linha e medir o erro. No sentido mais básico, regressão refere-se a previsão de um destino numérico. Regressão linear ainda é uma boa opção quando desejar um modelo simples para uma tarefa de previsão básica. Regressão linear também tende a funcionar bem em conjuntos de dados esparsos, altamente dimensionais sem complexidade.

O Azure Machine Learning dá suporte a uma variedade de modelos de regressão, além de regressão linear. No entanto, o termo "regressão" pode ser interpretado livremente, e não há suporte para alguns tipos de regressão fornecidas em outras ferramentas.

+ O problema de regressão clássico envolve uma única variável independente e uma variável dependente. Isso é chamado *simples de regressão*.  Este módulo oferece suporte a regressão simples.

+ *Regressão linear múltipla* envolve dois ou mais variáveis independentes que contribuem para uma única variável dependente. Problemas em que várias entradas são usadas para prever um resultado numérico único também são chamados *regressão linear multivariada*.

    O **Regressão Linear** módulo pode resolver esses problemas, como a maioria dos módulos de regressão.

+ *Regressão de multi-rótulo* é a tarefa de prever múltiplas variáveis dependentes em um único modelo. Por exemplo, na Regressão logística de multi-rótulo, um exemplo pode ser atribuído para vários rótulos diferentes. (Isso é diferente da tarefa de prever vários níveis dentro de uma variável de classe única.)

    Não há suporte para esse tipo de regressão no Azure Machine Learning. Para prever múltiplas variáveis, crie um aprendiz separado para cada saída que você deseja prever.

Por anos estatísticos têm desenvolvido métodos cada vez mais avançados de regressão. Isso é verdadeiro mesmo para regressão linear. Este módulo oferece suporte a dois métodos para medir o erro e ajustar a linha de regressão: método de quadrados mínimos comuns e descendente do gradiente.

- **Espaço descendente do gradiente** é um método que minimiza a quantidade de erro em cada etapa do processo de treinamento de modelo. Há muitas variações do espaço descendente do gradiente e a otimização para vários problemas de aprendizado tem sido muito estudada. Se você escolher essa opção para **método de solução**, você pode definir uma variedade de parâmetros para controlar o tamanho da etapa, a taxa de aprendizagem e assim por diante. Essa opção também suporta o uso de uma varredura de parâmetro integrado.

- **Quadrados mínimos** é uma das técnicas mais usadas na regressão linear. Por exemplo, quadrados mínimos é o método que é usado em ferramentas de análise para Microsoft Excel.

    Quadrados mínimos refere-se a função de perda, que calcula o erro como a soma do quadrado da distância entre o valor real e a linha prevista e se encaixa o modelo, minimizando o erro ao quadrado. Este método assume uma forte relação linear entre as entradas e a variável dependente.

## <a name="configure-linear-regression"></a>Configurar a regressão Linear

Este módulo oferece suporte a dois métodos para ajustar um modelo de regressão, com opções diferentes:

+ [Criar um modelo de regressão usando descendente do gradiente online](#bkmk_GradientDescent)

    Espaço descendente do gradiente é uma função de perda melhor para modelos que são mais complexos, ou que têm muito poucos dados de treinamento, dado o número de variáveis.



+ [Ajustar um modelo de regressão usando quadrados mínimos](#bkmk_OrdinaryLeastSquares)

    Para pequenos conjuntos de dados, é melhor selecionar os quadrados mínimos. Isso deve dar resultados semelhantes para o Excel.

## <a name="bkmk_OrdinaryLeastSquares"></a> Criar um modelo de regressão usando quadrados mínimos

1. Adicione a **modelo de regressão Linear** módulo à sua experiência na interface.

    Você pode encontrar esse módulo na **Machine Learning** categoria. Expandir **inicializar modelo**, expanda **regressão**e, em seguida, arraste o **modelo de regressão Linear** módulo ao seu experimento.

2. No **propriedades** painel, no **método de solução** lista suspensa, selecione **quadrados mínimos**. Essa opção especifica o método de computação usado para localizar a linha de regressão.

3. Na **ponderação da regularização L2**, digite o valor a ser usado como o peso para regularização L2. É recomendável que você use um valor diferente de zero para evitar o superajuste.

     Para saber mais sobre como a regularização afeta o ajuste do modelo, consulte este artigo: [Regularização L1 e L2 para Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)

4. Selecione a opção **termo de interceptação de inclusão**, se você deseja exibir o termo de interceptação.

    Desmarque essa opção se você não precisar revisar a fórmula de regressão.

5. Para **propagação de número aleatório**, opcionalmente, você pode digitar um valor para propagar o gerador de número aleatório usado pelo modelo.

    Usar um valor de semente é útil se você quiser manter os mesmos resultados entre execuções diferentes do mesmo experimento. Caso contrário, o padrão é usar um valor do relógio do sistema.


7. Adicione a [treinar modelo](./train-model.md) módulo ao seu experimento e conecte-se um conjunto de dados rotulado.

8. Execute o experimento.

## <a name="results-for-ordinary-least-squares-model"></a>Resultados para o modelo de quadrados mínimos comuns

Após a conclusão do treinamento:

+ Para exibir os parâmetros do modelo, a saída do instrutor com o botão direito e selecione **visualizar**.

+ Para fazer previsões, conecte-se o modelo treinado para o [modelo de pontuação](./score-model.md) módulo, juntamente com um conjunto de dados de novos valores. 


## <a name="bkmk_GradientDescent"></a> Criar um modelo de regressão usando descendente do gradiente online

1. Adicione a **modelo de regressão Linear** módulo à sua experiência na interface.

    Você pode encontrar esse módulo na **Machine Learning** categoria. Expandir **inicializar modelo**, expanda **regressão**e arraste o **modelo de regressão Linear** módulo à sua experiência

2. No **propriedades** painel, no **método de solução** lista suspensa, escolha **descendente do gradiente Online** como o método de cálculo usado para localizar a linha de regressão.

3. Para **criar modo de treino**, indicar se você deseja treinar o modelo com um conjunto predefinido de parâmetros, ou se você deseja otimizar o modelo usando uma varredura de parâmetro.

    + **Um único parâmetro**: Se você souber como deseja configurar a rede de regressão linear, você pode fornecer um conjunto específico de valores como argumentos.

   
4. Para **taxa de aprendizagem**, especifique a taxa de aprendizagem inicial para o otimizador descendente do gradiente estocástico.

5. Para **número de épocas de treinamento**, digite um valor que indica quantas vezes o algoritmo deve iterar por meio de exemplos. Para conjuntos de dados com um pequeno número de exemplos, esse número deve ser grande para alcançar convergência.

6. **Normalizar recursos**: Se você já tiverem se normalizado os dados numéricos usados para treinar o modelo, você pode desmarcar essa opção. Por padrão, o módulo normaliza todas as entradas numéricas para um intervalo entre 0 e 1.

    > [!NOTE]
    > 
    > Lembre-se de aplicar o mesmo método de normalização aos novos dados usados para pontuação.

7. Na **ponderação da regularização L2**, digite o valor a ser usado como o peso para regularização L2. É recomendável que você use um valor diferente de zero para evitar o superajuste.

    Para saber mais sobre como a regularização afeta o ajuste do modelo, consulte este artigo: [Regularização L1 e L2 para Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)


9. Selecione a opção **taxa de aprendizagem de diminuição**, se você quiser que a taxa de aprendizagem para diminuir à medida que as iterações progridem.  

10. Para **propagação de número aleatório**, opcionalmente, você pode digitar um valor para propagar o gerador de número aleatório usado pelo modelo. Usar um valor de semente é útil se você quiser manter os mesmos resultados entre execuções diferentes do mesmo experimento.


12. Adicione um conjunto de dados rotulado e um dos módulos de treinamento.

    Se você não estiver usando uma varredura de parâmetro, use o [modelo de treinamento](train-model.md) módulo.

13. Execute o experimento.

## <a name="results-for-online-gradient-descent"></a>Resultados para o espaço descendente do gradiente online

Após a conclusão do treinamento:

+ Para fazer previsões, conecte-se o modelo treinado para o [modelo de pontuação](./score-model.md) módulo, junto com novos dados de entrada.


## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 