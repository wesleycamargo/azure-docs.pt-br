---
title: 'Selecione as colunas no conjunto de dados: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar a selecionar colunas no módulo de conjunto de dados no serviço de Azure Machine Learning para escolher um subconjunto de colunas para usar nas operações downstream.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: e7407f62bd3401411d56076b298bd8cd134ece62
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028089"
---
# <a name="select-columns-in-dataset-module"></a>Selecionar colunas no módulo de conjunto de dados

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para escolher um subconjunto de colunas para usar nas operações downstream. O módulo não remove fisicamente as colunas do conjunto de dados de origem; em vez disso, ele cria um subconjunto de colunas, como um banco de dados *modo de exibição* ou *projeção*.

Esse módulo é útil quando você precisar limitar as colunas disponíveis para uma operação de downstream, ou se desejar reduzir o tamanho do conjunto de dados, removendo colunas desnecessárias.

As colunas no conjunto de dados são a saída na mesma ordem como os dados originais, mesmo se você especificá-los em uma ordem diferente.

## <a name="how-to-use"></a>Como usar

Esse módulo não tem parâmetros. Você pode usar o seletor de coluna para escolher as colunas a serem incluídos ou excluídos.

### <a name="choose-columns-by-name"></a>Escolha colunas por nome

Há várias opções no módulo para escolher colunas por nome: 

+ Filtrar e pesquisar

    Clique o **pelo nome** opção.

    Se você se conectou a um conjunto de dados que já está preenchido, deve aparecer uma lista de colunas disponíveis. Se nenhuma coluna for exibido, você talvez precise executar módulos upstream para exibir a lista de colunas.

    Para filtrar a lista, digite na caixa de pesquisa. Por exemplo, se você digitar a letra `w` na caixa de pesquisa, a lista é filtrada para mostrar os nomes de coluna que contêm a letra `w`.

    Selecione colunas e clique no botão de seta para a direita para mover as colunas selecionadas à lista no painel à direita.

    + Para selecionar um intervalo contínuo de nomes de coluna, pressione **Shift + clique**.
    + Para adicionar colunas individuais para a seleção, pressione **Ctrl + clique**.

    Clique no botão de marca de seleção para salvar e fechar.

+ Use nomes em combinação com outras regras

    Clique o **com regras** opção.
    
    Escolha uma regra, como Mostrar colunas de um tipo de dados específico.

    Em seguida, clique em colunas individuais desse tipo por nome, para adicioná-los à lista de seleção.

+ Digite ou cole uma lista separada por vírgulas de nomes de coluna

    Se seu conjunto de dados for grande, ele pode ser mais fácil de usar índices ou gerado listas de nomes, em vez de colunas selecionando individualmente. Supondo que você preparou a lista de antemão:

    1. Clique o **com regras** opção. 
    2. Selecione **não há colunas**, selecione **Include**e, em seguida, clique dentro da caixa de texto com o ponto de exclamação vermelho. 
    3. Cole ou digite uma lista separada por vírgulas de nomes de coluna validado anteriormente. Você não pode salvar o módulo se nenhuma coluna tiver um nome inválido, então, ser Verifique os nomes com antecedência.
    
    Você também pode usar esse método para especificar uma lista de colunas usando seus valores de índice. 

### <a name="choose-by-type"></a>Escolha por tipo

Se você usar o **com regras** opção, você pode aplicar várias condições em colunas selecionadas. Por exemplo, você talvez precise obter somente as colunas de recursos de um tipo de dados numéricos.

O **começa com** opção determina o ponto de partida e é importante para compreender os resultados. 

+ Se você selecionar o **todas as colunas** opção, todas as colunas são adicionadas à lista. Em seguida, você deve usar o **exclua** opção *remover* colunas que atendem a determinadas condições. 

    Por exemplo, você pode iniciar com todas as colunas e, em seguida, remover colunas por nome ou por tipo.

+ Se você selecionar o **colunas não** opção, a lista de colunas começa vazio. Você, em seguida, especificar condições para *adicionar* colunas à lista. 

    Se você aplicar várias regras, cada condição for **aditivas**. Por exemplo, digamos que você comece com nenhuma coluna e, em seguida, adicione uma regra para obter todas as colunas numéricas. O conjunto de dados de preço de automóvel, que resulta em 16 colunas. Em seguida, clique no **+** entrar para adicionar uma nova condição e, em seguida, selecione **incluir todos os recursos**. O conjunto de dados resultante inclui todas as colunas numéricas, além de todas as colunas de recurso, incluindo algumas colunas de recurso de cadeia de caracteres.

### <a name="choose-by-column-index"></a>Escolha pelo índice de coluna

O índice da coluna refere-se a ordem de coluna no conjunto de dados original.

+ As colunas são numeradas em sequência, começando em 1.  
+ Para obter um intervalo de colunas, use um hífen. 
+ As especificações em aberto, como `1-` ou `-3` não são permitidas.
+ Valores duplicados de índice (ou nomes de coluna) não são permitidos e podem resultar em um erro.

Por exemplo, supondo que seu conjunto de dados tem pelo menos oito colunas, você pode colar em qualquer um dos exemplos a seguir para retornar várias colunas não contíguas: 

+ `8,1-4,6`
+ `1,3-8`
+ `1,3-6,4` 

o exemplo final não resulta em erro; No entanto, ela retorna uma única instância da coluna `4`.



### <a name="change-order-of-columns"></a>Alterar a ordem das colunas

A opção **Permitir duplicatas e preservar a ordem das colunas na seleção** começa com uma lista vazia e adiciona as colunas que você especifica por nome ou índice. Ao contrário de outras opções, que sempre retornam colunas na "ordem natural", essa opção gera as colunas na ordem em que você nomeará ou listá-los. 

Por exemplo, em um conjunto de dados com as colunas Col1, Col2, Col3 e Col4, você pode inverter a ordem das colunas e deixar a coluna 2, especificando as listas a seguir:

+ `Col4, Col3, Col1`
+ `4,3,1`


## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 