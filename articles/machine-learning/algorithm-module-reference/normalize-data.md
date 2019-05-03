---
title: 'Normalize dados: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo normalizar dados no serviço de Azure Machine Learning para transformar um conjunto de dados por meio *normalização*...
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 95069bafa94770511c7ee40e82068960298fd6c5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029439"
---
# <a name="normalize-data-module"></a>Normalizar o módulo de dados

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para transformar um conjunto de dados por meio *normalização*.

A normalização é uma técnica geralmente aplicada como parte da preparação de dados para o aprendizado de máquina. O objetivo de normalização é alterar os valores de colunas numéricas no conjunto de dados para usar uma escala comum, sem distorção diferenças nos intervalos de valores ou perder informações. Normalização também é necessária para alguns algoritmos modelar os dados corretamente.

Por exemplo, suponha que seu conjunto de dados de entrada contém uma coluna com valores que variam de 0 a 1 e outra coluna com valores que variam de 10.000 a 100.000. As grandes diferenças entre o *escala* dos números pode causar problemas quando você tenta combinar os valores como recursos durante a modelagem.

*Normalização* evita esses problemas com a criação de novos valores que mantêm a distribuição geral e as taxas na fonte de dados, mantendo os valores dentro de uma escala aplicadas em todas as colunas numéricas usadas no modelo.

Este módulo oferece várias opções para transformar os dados numéricos:

- Você pode alterar todos os valores para um valor de 0 a 1 escalar ou transformar os valores que representam-los como percentil classifica, em vez de valores absolutos.
- Você pode aplicar a normalização para uma única coluna ou em várias colunas em um mesmo conjunto de dados.
- Se você precisar repetir o teste, ou aplicar as mesmas etapas de normalização com outros dados, você pode salvar as etapas como uma transformação de normalização e aplicá-la a outros conjuntos de dados que têm o mesmo esquema.

> [!WARNING]
> Alguns algoritmos exigem que os dados sejam normalizados antes de treinar um modelo. Outros algoritmos executam seus próprios dados de dimensionamento ou normalização. Portanto, quando você escolhe um algoritmo a ser usado na criação de um modelo de previsão de aprendizado de máquina, certifique-se de examinar os requisitos de dados do algoritmo antes de aplicar a normalização para os dados de treinamento.

##  <a name="configure-normalize-data"></a>Configurar normalizar dados

Você pode aplicar somente um método de normalização em um tempo de uso deste módulo. Portanto, o mesmo método de normalização é aplicado a todas as colunas que você selecionar. Para usar métodos diferentes de normalização, use uma segunda instância do **normalizar dados**.

1. Adicione a **normalizar dados** módulo ao seu experimento. Você pode encontrar o módulo no Azure Machine Learning, sob **Data Transformation**, no **dimensionar e reduzir** categoria.

2. Conecte-se um conjunto de dados que contém pelo menos uma coluna de todos os números.

3. Use o seletor de coluna para escolher as colunas numéricas para normalizar. Se você não escolher colunas individuais, por padrão **todos os** colunas de tipo numérico na entrada são incluídas, e o mesmo processo de normalização é aplicado a todas as colunas selecionadas. 

    Isso pode levar a resultados estranhos, se você incluir colunas numéricas que não devem ser normalizadas! Sempre verifique as colunas com cuidado.

    Se não há colunas numéricas forem detectadas, verifique os metadados da coluna para verificar se o tipo de dados da coluna é um tipo numérico com suporte.

    > [!TIP]
    > Para garantir que as colunas de um tipo específico são fornecidas como entrada, tente usar o [selecionar colunas no conjunto de dados](./select-columns-in-dataset.md) módulo antes **normalizar dados**.

4. **Use 0 para colunas constante quando marcado**:  Selecione esta opção quando qualquer coluna numérica contém um único valor inalterável. Isso garante que essas colunas não são usadas em operações de normalização.

5. Dos **método de transformação** lista suspensa, escolha uma única função matemática para aplicar a todas as colunas selecionadas. 
  
    - **Zscore**: Converte todos os valores em uma pontuação z.
    
      Os valores na coluna são transformados usando a seguinte fórmula:  
  
      ![normalização usando z&#45;pontuações](media/module/aml-normalization-z-score.png)
  
      Desvio médio e padrão são calculados para cada coluna separadamente. Desvio padrão da população é usado.
  
    - **MinMax**: O normalizador de Mín-Máx redimensiona linearmente cada recurso no intervalo [0,1].
    
      O redimensionamento no intervalo [0,1] é feito mudando os valores de cada recurso para que o valor mínimo é 0, e, em seguida, dividindo pelo novo valor máximo (que é a diferença entre os valores máximos e mínimos originais).
      
      Os valores na coluna são transformados usando a seguinte fórmula:  
  
      ![normalização usando o mínimo de&#45;máximo de função](media/module/aml-normalization-minmax.png "AML_normalization minmax")  
  
    - **Logística**: Os valores na coluna são transformados usando a seguinte fórmula:

      ![fórmula de normalização pela função logística](media/module/aml-normalization-logistic.png "AML_normalization logística")  
  
    - **LogNormal**: Essa opção converte todos os valores em uma escala de log-normal.
  
      Os valores na coluna são transformados usando a seguinte fórmula:
  
      ![log de fórmulas&#45;distribuição normal](media/module/aml-normalization-lognormal.png "AML_normalization lognormal")
    
      Aqui μ e σ são os parâmetros da distribuição, calculados empiricamente a partir dos dados como estimativas de probabilidade máxima, para cada coluna separadamente.  
  
    - **TanH**: Todos os valores são convertidos em uma tangente hiperbólica.
    
      Os valores na coluna são transformados usando a seguinte fórmula:
    
      ![normalização usando a função tanh](media/module/aml-normalization-tanh.png "AML_normalization tanh")

6. Execute o teste, ou clique duas vezes o **normalizar dados** módulo e selecione **executar selecionado**. 

## <a name="results"></a>Resultados

O **normalizar dados** módulo gera duas saídas:

- Para exibir os valores transformados, o módulo com o botão direito, selecione **transformado dataset**e clique em **visualizar**.

    Por padrão, os valores são transformados em vigor. Se você quiser comparar os valores transformados com os valores originais, use o [adicionar colunas](./add-columns.md) módulo para recombinar os conjuntos de dados e exiba as colunas lado a lado.

- Para salvar a transformação para que você possa aplicar o mesmo método de normalização para outro conjunto de dados semelhante, o módulo com o botão direito, selecione **função de transformação**e clique em **Salvar como transformação**.

    Em seguida, você pode carregar as transformações salvas do **transforma** grupo do painel de navegação à esquerda e aplicá-lo a um conjunto de dados com o mesmo esquema, usando [. / Aplicar transformação](apply-transformation.md).  


## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 