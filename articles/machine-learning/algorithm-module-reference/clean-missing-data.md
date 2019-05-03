---
title: 'Limpe dados ausentes: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo de limpar dados ausentes no serviço de Azure Machine Learning para remover, substituir ou inferir valores ausentes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: de81204219a102734f1820258a3c32e59a64c685
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028779"
---
# <a name="clean-missing-data-module"></a>Módulo de limpar dados ausentes

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para remover, substituir ou inferir valores ausentes. 

Os cientistas de dados geralmente verificam os dados para valores ausentes e, em seguida, executam várias operações para corrigir os dados ou inserir novos valores. O objetivo dessas operações de limpeza é evitar problemas causados por dados ausentes que podem surgir ao treinar um modelo. 

Este módulo oferece suporte a vários tipo das operações de "limpeza" valores ausentes, incluindo:

+ Substituir valores ausentes com um espaço reservado, média ou outro valor
+ Remover completamente a linhas e colunas que têm valores ausentes
+ Inferindo os valores com base em métodos estatísticos


Usar esse módulo não altera seu conjunto de dados de origem. Em vez disso, ele cria um novo conjunto de dados no seu espaço de trabalho que você pode usar no fluxo de trabalho subsequente. Você também pode salvar o conjunto de dados novo e limpo para reutilização.

Este módulo também gera uma definição da transformação usada para limpar os valores ausentes. Você pode reutilizar essa transformação em outros conjuntos de dados que têm o mesmo esquema, usando o [Aplicar transformação](./apply-transformation.md) módulo.  

## <a name="how-to-use-clean-missing-data"></a>Como usar limpar dados ausentes

Esse módulo permite definir uma operação de limpeza. Você também pode salvar a operação de limpeza para que você pode aplicá-lo mais tarde para novos dados. Consulte os links a seguir para obter uma descrição de como criar e salvar um processo de limpeza: 
 
+ Para substituir valores ausentes
  
+ Para aplicar uma transformação de limpeza para novos dados
 
> [!IMPORTANT]
> O método de limpeza que você usa para lidar com valores ausentes drasticamente pode afetar os resultados. É recomendável fazer experiências com métodos diferentes. Considere os dois a justificativa para uso de um método específico e a qualidade dos resultados.

### <a name="replace-missing-values"></a>Substituir valores ausentes  

Cada vez que você aplicar a [limpar dados ausentes](./clean-missing-data.md) módulo a um conjunto de dados, a mesma operação de limpeza é aplicado a todas as colunas que você selecionar. Portanto, se você precisar limpar usando métodos diferentes de colunas diferentes, use instâncias separadas do módulo.

1.  Adicione a [limpar dados ausentes](./clean-missing-data.md) módulo ao seu experimento e conecte-se o conjunto de dados que tem valores ausentes.  
  
2.  Para **colunas a serem limpas**, escolha as colunas que contêm os valores ausentes, você deseja alterar. Você pode escolher várias colunas, mas você deve usar o mesmo método de substituição em todas as colunas selecionadas. Portanto, normalmente você precisará limpar as colunas de cadeia de caracteres e colunas numéricas separadamente.

    Por exemplo, para verificar se há valores ausentes em todas as colunas numéricas:

    1. Abra o seletor de coluna e selecione **com regras**.
    2. Para **começa com**, selecione **sem colunas**.

        Você pode também iniciar com todas as colunas e, em seguida, excluir colunas. Inicialmente, as regras não são mostradas se você primeiro clique **todas as colunas**, mas você pode clicar **sem colunas** e, em seguida, clique em **todas as colunas** novamente para iniciar com todas as colunas e, em seguida, filtrar colunas (excluir) com base no nome do tipo de dados, ou o índice de colunas.

    3. Para **Include**, selecione **tipo de coluna** na lista suspensa e selecione **numérico**, ou um tipo numérico mais específico. 
  
    Qualquer método de limpeza ou de substituição que você escolher deve ser aplicado ao **todos os** colunas na seleção. Se os dados em nenhuma coluna forem incompatíveis com a operação especificada, o módulo retornará um erro e interrompe o experimento.
  
3.  Para **mínimo de taxa de valor ausente**, especifique o número mínimo de valores ausentes necessários para a operação a ser executada.  
  
    Use essa opção em combinação com **máximo de taxa de valor ausente** para definir as condições sob as quais uma operação de limpeza é executada no conjunto de dados. Se houver muitas ou poucas linhas que são valores ausentes, a operação não pode ser executada. 
  
    O número inserido representa o **proporção** de valores ausentes para todos os valores na coluna. Por padrão, o **taxa de valor ausente mínimo** estiver definida como 0. Isso significa que valores ausentes serão limpos mesmo se houver apenas um valor ausente. 

    > [!WARNING]
    > Essa condição deve ser atendida por cada coluna na ordem para a operação especificada a ser aplicado. Por exemplo, suponha que três colunas selecionadas e, em seguida, definir a taxa mínima de valores ausentes para.2 (% 20), mas apenas uma coluna, na verdade, tem valores ausentes de 20%. Nesse caso, a operação de limpeza seria aplicada somente à coluna com mais de 20% de valores ausentes. Portanto, as outras colunas poderia ser inalteradas.
    > 
    > Se você tiver alguma dúvida sobre se os valores ausentes foram alterados, selecione a opção **gerar coluna de indicador de valor ausente**. Uma coluna é acrescentada ao conjunto de dados para indicar se cada coluna atende a critérios especificados para os intervalos mínimos e máximo.  
  
4. Para **máximo de taxa de valor ausente**, especifique o número máximo de valores ausentes que podem estar presentes para a operação a ser executada.   
  
    Por exemplo, você talvez queira executar a substituição de valor ausente somente se 30% ou menos das linhas contêm valores ausentes, mas deixe os valores como-se mais de 30% de linhas tiver valores ausentes.  
  
    Defina o número como a proporção de valores ausentes para todos os valores na coluna. Por padrão, o **máximo de taxa de valor ausente** é definido como 1. Isso significa que valores ausentes serão limpos mesmo que 100% dos valores na coluna estejam ausentes.  
  
   
  
5. Para **modo de limpeza**, selecione uma das opções a seguir para substituir ou remover faltando valores:  
  
  
    + **Valor de substituição personalizado**: Use esta opção para especificar um valor de espaço reservado (por exemplo, 0 ou NA) que se aplica a todos os valores ausentes. O valor que você especificar como uma substituição deve ser compatível com o tipo de dados da coluna.
  
    + **Substituir por média**: Calcula a média da coluna e usa a média como o valor de substituição para cada valor ausente na coluna.  
  
        Aplica-se apenas a colunas que têm o número inteiro, duplo ou tipos de dados booliano.  
  
    + **Substituir por mediana**: Calcula o valor mediano da coluna e usa o valor mediano como a substituição de qualquer valor ausente na coluna.  
  
        Aplica-se somente às colunas que têm tipos de dados inteiro ou duplo. 
  
    + **Substituir por modo**: Calcula o modo da coluna e usa o modo como o valor de substituição para cada valor ausente na coluna.  
  
        Aplica-se às colunas que têm tipos de dados inteiro, duplo, booleano ou categórico. 
  
    + **Remover linha inteira**: Remove completamente qualquer linha no conjunto de dados que tem um ou mais valores ausentes. Isso é útil se o valor ausente pode ser considerado como ausente aleatoriamente.  
  
    + **Remover coluna inteira**: Remove completamente qualquer coluna do conjunto de dados que tem um ou mais valores ausentes.  
  
    
  
6. A opção **valor de substituição** estará disponível se você tiver selecionado a opção **valor de substituição personalizado**. Digite um novo valor a ser usado como o valor de substituição para todos os valores ausentes na coluna.  
  
    Observe que você pode usar essa opção somente em colunas que têm os tipos de dados inteiro, duplo, booleano ou data. Para colunas de data, o valor de substituição também poderá ser inserido como o número de tiques de 100 nanossegundos desde 1/1/0001 12:00:00  
  
7. **Gerar coluna de indicador de valor ausente**: Selecione esta opção se você quiser alguma indicação se os valores na coluna atendidos os critérios para a limpeza de valor ausente de saída. Essa opção é particularmente útil quando você estiver configurando uma nova operação de limpeza e deseja verificar se que ele funciona conforme o projetado.
  
8. Execute o experimento.

### <a name="results"></a>Resultados

O módulo retorna duas saídas:  

-   **Conjunto de dados limpo**: Um conjunto de dados composto por colunas selecionadas, com valores ausentes são tratados conforme especificado, juntamente com uma coluna de indicador, se você tiver selecionado essa opção.  

    Colunas não selecionadas para a limpeza são também "passadas".  
  
-  **Transformação de limpeza**: Transformação de dados usada para limpeza, que pode ser salvo no espaço de trabalho e aplicada aos novos dados mais tarde.

### <a name="apply-a-saved-cleaning-operation-to-new-data"></a>Aplicar uma operação de limpeza salva aos novos dados  

Se você precisar repetir operações de limpeza com frequência, é recomendável que você salve sua receita para limpeza de dados como uma *transformar*, reutilizar o mesmo conjunto de dados. Salvar uma transformação de limpeza é particularmente útil se você deve com frequência importar novamente e, em seguida, limpar os dados que tem o mesmo esquema.  
      
1.  Adicione a [Aplicar transformação](./apply-transformation.md) módulo ao seu experimento.  
  
2.  Adicione o conjunto de dados que você deseja limpar e conecte o conjunto de dados para a porta de entrada à direita.  
  
3.  Expanda o **transforma** grupo no painel esquerdo da interface. Localize a transformação salva e arraste-o para o teste.  
  
4.  Conectar-se a transformação salva para a porta de entrada esquerda [Aplicar transformação](./apply-transformation.md). 

    Quando você aplica uma transformação salva, você não pode selecionar as colunas aos quais a transformação são aplicadas. Isso ocorre porque a transformação já foi definida e aplica-se automaticamente às colunas especificadas na operação original.

    No entanto, suponha que você criou uma transformação em um subconjunto de colunas numéricas. Você pode aplicar essa transformação para um conjunto de dados de tipos de coluna misto sem gerar um erro, porque os valores ausentes são alterados somente nas colunas numéricas correspondentes.

6.  Execute o experimento.  

## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 