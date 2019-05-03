---
title: 'Inserir os dados manualmente: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo inserir dados manualmente no serviço Azure Machine Learning para criar um conjunto de dados pequeno digitando valores. O conjunto de dados pode ter várias colunas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ee15b6fb7160ece907d55e790b0ae38ee458ab96
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028599"
---
# <a name="enter-data-manually-module"></a>Insira o módulo de dados manualmente

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para criar um conjunto de dados pequeno digitando valores. O conjunto de dados pode ter várias colunas.
  
Esse módulo pode ser útil em cenários como estes:  
  
- Gerando um pequeno conjunto de valores para teste  
  
- Criando uma lista curta de rótulos
  
- Digitar uma lista de nomes de coluna a ser inserido em um conjunto de dados

## <a name="enter-data-manually"></a>Inserir os dados manualmente 
  
1.  Adicione a [inserir manualmente dados](./enter-data-manually.md) módulo ao seu experimento. Você pode encontrar esse módulo na **dados de entrada e saída** categoria no Azure Machine Learning. 
  
2.  Para **DataFormat**, selecione uma das opções a seguir. Essas opções determinam como os dados que você fornecer devem ser analisados. Os requisitos para cada formato diferem muito, portanto, certifique-se de ler os tópicos relacionados.  
  
    -   **ARFF**. Relação de atributo formato de arquivo, usado pelo Weka.   
  
    -   **CSV**. Formato de valores separados por vírgulas. Para obter mais informações, consulte [converter para CSV](./convert-to-csv.md).  
  
    -   **SVMLight**. Um formato usado pelo Vowpal Wabbit e outra estruturas de aprendizado de máquina.  
  
    -   **TSV**. Formato de valores separados por tabulações.

     Se você escolher um formato e não fornece dados que atenda às especificações de formato, ocorrerá um erro de tempo de execução.
  
3.  Clique dentro de **dados** caixa de texto para começar a inserir dados. Os formatos a seguir requerem atenção especial:  
  
    - **CSV**:  Para criar várias colunas, colar texto separada por vírgulas ou várias colunas usando vírgulas entre os campos de tipo.
  
        Se você selecionar o **HasHeader** opção, você pode usar a primeira linha de valores como o título de coluna.  
  
        Se você desmarcar essa opção, os nomes de colunas, Col1, Col2 e assim por diante, são usados. Você pode adicionar ou alterar colunas de nomes mais tarde usando [editar metadados](./edit-metadata.md).  
  
    - **TSV**: Para criar várias colunas, colar texto separado por tabulações ou várias colunas usando guias entre os campos de tipo.  
  
        Se você selecionar o **HasHeader** opção, você pode usar a primeira linha de valores como o título de coluna.  
  
        Se você desmarcar essa opção, os nomes de colunas, Col1, Col2 e assim por diante, são usados. Você pode adicionar ou alterar colunas de nomes mais tarde usando [editar metadados](./edit-metadata.md).  
  
    -   **ARFF**:  Cole em um arquivo de formato ARFF existente. Se você estiver digitando valores diretamente, certifique-se de adicionar os campos de atributo necessário e o cabeçalho opcional no início dos dados. 
    
        Por exemplo, as seguintes linhas de cabeçalho e o atributo poderia ser adicionadas a uma lista simple. O título de coluna seria `SampleText`.
    
        ```text
        % Title: SampleText.ARFF  
        % Source: Enter Data module  
        @ATTRIBUTE SampleText STRING  
        @DATA  
        \<type first data row here>  
        ```

    -   **SVMLight**: Digite ou cole os valores usando o formato SVMLight.  
  
        Por exemplo, o exemplo a seguir representa as primeiras linhas do conjunto de dados doação de sangue, no formato SVMight:  
  
        ```text  
        # features are [Recency], [Frequency], [Monetary], [Time]  
        1 1:2 2:50 3:12500 4:98   
        1 1:0 2:13 3:3250 4:28   
        ```  
  
        Quando você executa o [inserir manualmente dados](./enter-data-manually.md) módulo, essas linhas são convertidas em um conjunto de dados de colunas e indexar valores da seguinte maneira:  
  
        |Col1|Col2|Col3|Col4|Rótulos|  
        |-|-|-|-|-|  
        |0.00016|0.004|0.999961|0.00784|1|  
        |0|0.004|0.999955|0.008615|1|  
  
4.  Pressione ENTER após cada linha, para iniciar uma nova linha.  
  
     **Certifique-se de pressionar ENTER após a linha final.** 
     
     Se você pressionar ENTER várias vezes para adicionar vários vazio à direita de linhas, a linha final de vazia é removida cortado, mas outras linhas vazias são tratadas como valores ausentes.  
  
     Se você criar linhas com valores ausentes, você sempre pode filtrá-los mais tarde.  
  
5.  O módulo com o botão direito e selecione **executar selecionado** para analisar os dados e carregá-lo em seu espaço de trabalho como um conjunto de dados.  
  
     Para exibir o conjunto de dados, clique na porta de saída e selecione **visualizar**.  
## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 