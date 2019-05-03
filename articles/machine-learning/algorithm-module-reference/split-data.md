---
title: 'Dividir os dados: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo dividir dados no serviço de Azure Machine Learning para dividir um conjunto de dados em dois conjuntos distintos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a7395280ed9a2e9dcb94a081f0b3bf10a28da719
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029394"
---
# <a name="split-data-module"></a>Módulo dividir dados

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para dividir um conjunto de dados em dois conjuntos distintos.

Esse módulo é particularmente útil quando você precisar separar dados em conjuntos de teste e treinamento. Você pode personalizar a maneira que os dados são divididos também. Algumas opções de suportam aleatória dos dados. outros são personalizados para um determinado tipo de dados ou tipo de modelo.

## <a name="how-to-configure"></a>Como configurar

> [!TIP]
> Antes de escolher o modo de divisão, todas as opções para determinar o tipo de divisão de leitura, você precisa.
> Se você alterar o modo de divisão, foi possível redefinir todas as outras opções.

1. Adicione a **dividir dados** módulo à sua experiência na interface. Você pode encontrar esse módulo sob **Data Transformation**, no **exemplo e dividir** categoria.

2. **Modo de divisão**: Escolha um dos modos a seguir, dependendo do tipo de dados que você tem e como você deseja dividir a ele. Cada modo de divisão tem opções diferentes. Clique nos seguintes tópicos para obter instruções detalhadas e exemplos. 

    - **Dividir linhas**: Use esta opção se você apenas deseja dividir os dados em duas partes. Você pode especificar a porcentagem dos dados para colocar em cada divisão, mas por padrão, os dados são divididos 50 e 50.

        Você pode também tornar aleatório a seleção de linhas em cada grupo e usar a amostragem estratificada. No modo de amostragem estratificado, você deve selecionar uma única coluna de dados para o qual você deseja que os valores ser distribuídos igualmente entre dois conjuntos de dados.  

    - **Divisão de expressão regular** Escolha esta opção quando você deseja dividir seu conjunto de dados por uma única coluna para um valor de testes.

        Por exemplo, se você estiver analisando o sentimento, você pode verificar a presença de um nome de produto específico em um campo de texto e, em seguida, dividir o conjunto de dados em linhas com o nome do produto de destino e aqueles sem.

    - **Divisão de expressão relativa**:  Use essa opção sempre que você deseja aplicar uma condição para uma coluna de números. O número pode ser um campo de data/hora, uma idade de recipiente de coluna ou quantias em dinheiro ou até mesmo uma porcentagem. Por exemplo, você talvez queira dividir seu conjunto de dados, dependendo do custo dos itens, grupo de pessoas por faixas etárias ou dados separados por uma data do calendário.

### <a name="split-rows"></a>Linhas de divisão
1.  Adicione a [dividir dados](./split-data.md) módulo à sua experiência na interface e conecte-se o conjunto de dados que você deseja dividir.
  
2.  Para **modo de divisão**, escolha **Dividir linhas**. 

3.  **Fração de linhas no primeiro conjunto de dados de saída**. Use esta opção para determinar quantas linhas ir para a primeira saída (esquerda). Todas as outras linhas entrará na segunda saída (à direita).

    A proporção representa a porcentagem de linhas enviadas para o primeiro conjunto de dados de saída, portanto, você deve digitar um número decimal entre 0 e 1.
     
     Por exemplo, se você digitar 0,75 como o valor, o conjunto de dados ser dividido por meio de uma taxa de 75:25, com 75% das linhas enviadas para o primeiro conjunto de dados de saída e 25% enviadas para o segundo conjunto de dados de saída.
  
4. Selecione o **divisão aleatória** opção se você quiser tornar aleatório a seleção de dados em dois grupos. Isso é a opção preferencial durante a criação de conjuntos de dados de treinamento e teste.

5.  **Propagação aleatória**: Digite um valor inteiro não negativo para inicializar a sequência pseudoaleatória de instâncias a serem usadas. Essa propagação padrão é usada em todos os módulos que geram números aleatórios. 

     Especificar uma semente torna os resultados geralmente reproduzíveis. Se você precisar repetir os resultados de uma operação de divisão, você deve especificar uma semente para o gerador de número aleatório. Caso contrário, a semente aleatória é definida por padrão como 0, o que significa que o valor de semente inicial é obtido do relógio do sistema. Como resultado, a distribuição de dados pode ser ligeiramente diferente cada vez que você executar uma divisão. 

6. **Divisão estratificada**: Defina essa opção como **verdadeira** para garantir que a saída de dois conjuntos de dados contenham uma amostra representativa dos valores na *coluna strata* ou *coluna chave de estratificação*. 

    Com a amostragem estratificada, os dados são divididos, de modo que cada conjunto de dados de saída obtém aproximadamente o mesmo percentual de cada valor de destino. Por exemplo, você pode deseja garantir que seu treinamento e conjuntos de teste são balanceados aproximadamente em relação ao resultado ou com relação ot alguma outra coluna como gênero.

7. Execute o experimento.


## <a name="regular-expression-split"></a>Divisão de expressão regular

1.  Adicione a [dividir dados](./split-data.md) módulo ao seu experimento e conectá-lo como entrada para o conjunto de dados que você deseja dividir.  
  
2.  Para **modo de divisão**, selecione **divisão de expressão Regular**.

3. No **Expressão Regular** , digite uma expressão regular válida. 
  
   A expressão regular deve seguir a sintaxe de expressão regular do Python.


4. Execute o experimento.

    Com base na expressão regular que você fornecer, o conjunto de dados é dividido em dois conjuntos de linhas: linhas com valores que correspondem à expressão e todas as linhas restantes. 

## <a name="relative-expression-split"></a>Divisão de expressão relativa.

1. Adicione a [dividir dados](./split-data.md) módulo ao seu experimento e conectá-lo como entrada para o conjunto de dados que você deseja dividir.
  
2. Para **modo de divisão**, selecione **divisão da expressão relativa**.
  
3. No **expressão relacional** caixa de texto, digite uma expressão que executa uma operação de comparação, em uma única coluna:


 - Coluna numérica:
    - A coluna contém números de qualquer tipo de dados numéricos, incluindo tipos de dados de data/hora.

    - A expressão pode fazer referência a um máximo de um nome de coluna.

    - Use o caractere e comercial (&) para a operação AND e use o caractere de barra vertical (|) para a operação OR.

    - Os operadores a seguir têm suporte: `<`, `>`, `<=`, `>=`, `==`, `!=`

    - Você não pode agrupar operações usando `(` e `)`.

 - Coluna de cadeia de caracteres: 
    - Os operadores a seguir têm suporte: `==`, `!=`



4. Execute o experimento.

    A expressão divide o conjunto de dados em dois conjuntos de linhas: linhas com valores que atendem à condição e todas as linhas restantes.

## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 