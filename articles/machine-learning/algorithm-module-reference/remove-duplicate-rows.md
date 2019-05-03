---
title: 'Remova linhas duplicadas: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo de remover linhas duplicadas no serviço Azure Machine Learning para remover duplicatas potenciais de um conjunto de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: dce90d911085c1f7330a2e0952bb9576c1d765fa
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029289"
---
# <a name="remove-duplicate-rows-module"></a>Remover linhas duplicadas de módulo

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para remover duplicatas potenciais de um conjunto de dados.

Por exemplo, suponha que seus dados é semelhante ao seguinte e representa vários registros de pacientes. 

| PatientID | Initials| Gênero|Idade|Admitido|
|----|----|----|----|----|
|1|F.M.| M| 53| Jan|
|2| F.A.M.| M| 53| Jan|
|3| F.A.M.| M| 24| Jan|
|3| F.M.| M| 24| Feb|
|4| F.M.| M| 23| Feb|
| | F.M.| M| 23| |
|5| F.A.M.| M| 53| |
|6| F.A.M.| M| NaN| |
|7| F.A.M.| M| NaN| |

Claramente, este exemplo tem várias colunas com dados possivelmente duplicados. Se eles são, na verdade, as duplicatas depende de seu conhecimento dos dados. 

+ Por exemplo, você pode saber que muitos pacientes têm o mesmo nome. Você não seria eliminar duplicatas usando qualquer coluna de nome, somente o **ID** coluna. Dessa forma, somente as linhas com valores de ID duplicados são filtradas, independentemente dos pacientes tem ou não o mesmo nome.

+ Como alternativa, você pode decidir permitir duplicatas no campo ID e usar outras combinações de arquivos para localizar registros exclusivos, como nome, último nome, idade e sexo.  

Para definir os critérios para uma linha seja duplicada ou não, você especifica uma única coluna ou um conjunto de colunas a serem usadas como **chaves**. Duas linhas serão consideradas duplicatas somente quando os valores em **todos os** colunas de chave são iguais. Se qualquer linha tem o valor ausente para **chaves**, eles não serão considerados linhas duplicadas. Por exemplo, se o sexo e idade são definidos como chaves em acima da tabela, a linha 6 e 7 não são linhas duplicadas dadas ter valor ausente na idade.

Quando você executa o módulo, ele cria um conjunto de dados do candidato e retorna um conjunto de linhas que têm sem duplicatas em um conjunto de colunas que você especificou.

> [!IMPORTANT]
> O conjunto de dados de origem não é alterado; Esse módulo cria um novo conjunto de dados que é filtrado para excluir duplicatas, com base em critérios especificados por você.

## <a name="how-to-use-remove-duplicate-rows"></a>Como usar remover linhas duplicadas

1. Adicione o módulo à sua experiência. Você pode encontrar o **remover linhas duplicadas** módulo sob **transformação de dados**, **manipulação**.  

2. Conecte-se o conjunto de dados que você deseja verificar linhas duplicadas.

3. No **propriedades** painel, em **expressão de filtro de seleção de coluna de chave**, clique em **iniciar seletor de coluna**, para escolher colunas a serem usadas na identificação de duplicatas.

    Nesse contexto, **chave** não significa um identificador exclusivo. Todas as colunas que você selecionar usando o seletor de coluna são designadas como **colunas de chave**. Todas as colunas não selecionadas são consideradas colunas não chave. A combinação de colunas selecionadas como chaves determina a exclusividade dos registros. (Considere isso como uma instrução SQL que usa várias junções igualdades.)

    Exemplos:

    + "Eu quero garantir que as IDs são exclusivas": Escolha somente a coluna de ID.
    + "Eu quero garantir que a combinação de nome, sobrenome e ID seja exclusiva": Selecione todas as três colunas.

4. Use o **reter primeira linha duplicada** caixa de seleção para indicar qual linha a ser retornado quando forem encontradas duplicatas:

    + Se selecionado, a primeira linha é retornada e descartados por outras pessoas. 
    + Se você desmarcar essa opção, a última linha duplicada é mantida nos resultados e outras são descartadas. 

5. Execute o experimento.

6. Para examinar os resultados, clique com botão direito do módulo, selecione **conjunto de dados de resultados**e clique em **visualizar**. 

> [!TIP]
> Se os resultados são difíceis de entender, ou se você quiser excluir algumas colunas da consideração, você pode remover colunas usando o [selecionar colunas no conjunto de dados](./select-columns-in-dataset.md) módulo.

## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 