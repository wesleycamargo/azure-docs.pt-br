---
title: 'Partição e exemplo: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo partição e exemplo no serviço Azure Machine Learning para executar amostragem em um conjunto de dados ou criar partições do seu conjunto de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 72f9e11e3582d804eecc7479ea079276564bd12f
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029274"
---
# <a name="partition-and-sample-module"></a>Módulo partição e exemplo

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para executar amostragem em um conjunto de dados ou criar partições do seu conjunto de dados.

A amostragem é uma ferramenta importante no aprendizado de máquina porque permite reduzir o tamanho de um conjunto de dados, mantendo a mesma proporção de valores. Este módulo oferece suporte a várias tarefas relacionadas que são importantes no aprendizado de máquina: 

- Dividir seus dados em várias subseções do mesmo tamanho. 

    Você pode usar as partições para validação cruzada ou atribuir casos a grupos aleatórios.

- Separação dos dados em grupos e, em seguida, trabalhar com dados de um grupo específico. 

    Depois de atribuir aleatoriamente os casos a grupos diferentes, talvez você precise modificar os recursos que estão associados com apenas um grupo.

- Amostragem. 

    Você pode extrair uma porcentagem dos dados, aplicar a amostragem aleatória ou escolher uma coluna para usar para equilibrar o conjunto de dados e realizar a amostragem estratificada em seus valores.

- Criando um conjunto de dados menor para teste. 

    Se você tiver muitos dados, você talvez queira usar somente as primeiras *n* linhas durante a configuração o experimento e, em seguida, alternar para o usando o conjunto de dados completo quando você compila seu modelo. Você também pode usar a amostragem criar s menor conjunto de dados para uso em desenvolvimento.

## <a name="configure-partition-and-sample"></a>Configurar a partição e exemplo

Este módulo oferece suporte a vários métodos para dividir seus dados em partições ou para amostragem. Escolha o método primeiro e, em seguida, definir opções adicionais são necessárias pelo método.

- Principal
- amostragem
- Atribuir a dobras
- Selecionar dobra

### <a name="get-top-n-rows-from-a-dataset"></a>Obter linhas TOP N de um conjunto de dados

Use este modo para obter apenas as primeiras *n* linhas. Essa opção é útil se você quiser testar um experimento em um pequeno número de linhas e não precisa dos dados a ser balanceada ou amostrado de qualquer forma.

1. Adicione a **partição e exemplo** módulo à sua experiência na interface e conecte-se o conjunto de dados.  

2. **Modo de partição ou exemplo**: Defina essa opção como **Head**.

3. **Número de linhas a serem selecionadas**: Digite o número de linhas a serem retornadas.

    O número de linhas que você especificar deve ser um inteiro não negativo. Se o número de linhas selecionadas for maior que o número de linhas no conjunto de dados, o conjunto de dados inteiro será retornado.

4. Execute o experimento.

O módulo produz um único conjunto de dados que contém apenas o número especificado de linhas. As linhas são lidas sempre na parte superior do conjunto de dados.

### <a name="create-a-sample-of-data"></a>Criar uma amostra de dados

Essa opção dá suporte à amostragem aleatória simples ou amostragem aleatória estratificada. Isso é útil se você quiser criar um conjunto de dados de amostra representativa menor para teste.

1. Adicione a **partição e exemplo** módulo ao seu experimento e conecte-se o conjunto de dados.

2. **Modo de partição ou exemplo**: Defina isso como **amostragem**.

3. **Taxa de amostragem**: Digite um valor entre 0 e 1. Esse valor Especifica a porcentagem de linhas do conjunto de dados de origem que devem ser incluídos no conjunto de dados de saída.

    Por exemplo, se você desejar apenas metade do conjunto de dados original, digite `0.5` para indicar que a taxa de amostragem deve ser 50%.

    As linhas do conjunto de dados de entrada são colocadas em ordem aleatória e colocadas seletivamente no conjunto de dados de saída, acordo com a taxa especificada.

4. **Propagação aleatória para amostragem**: Opcionalmente, digite um número inteiro a ser usado como um valor de semente.

    Essa opção é importante se você quiser que as linhas sejam divididas da mesma forma toda vez. O valor padrão é 0, o que significa que a propagação inicial é gerada com base no relógio do sistema. Isso pode levar a resultados um pouco diferentes cada vez que você executar o teste.

5. **Divisão estratificada para amostragem**: Selecione esta opção se for importante que as linhas no conjunto de dados devem ser divididas igualmente por alguma coluna de chave antes de amostragem.

    Para **coluna de chave de Estratificação para amostragem**, selecione uma única *coluna strata* a ser usado ao dividir o conjunto de dados. As linhas no conjunto de dados, em seguida, são divididas da seguinte maneira:

    1. Todas as linhas de entrada são agrupadas (estratificadas) pelos valores na coluna strata especificada.

    2. Linhas são colocadas em ordem aleatória dentro de cada grupo.

    3. Cada grupo é seletivamente adicionado ao conjunto de dados de saída para atender a taxa especificada.


6. Execute o experimento.

    Com essa opção, o módulo produz um único conjunto de dados que contém uma amostra representativa dos dados. A parte restante e não provada do conjunto de dados não é de saída. 

## <a name="split-data-into-partitions"></a>Dividir os dados em partições

Use essa opção quando você deseja dividir o conjunto de dados em subconjuntos de dados. Essa opção também é útil quando você deseja criar um número de dobras para validação cruzada ou Dividir linhas em vários grupos.

1. Adicione a **partição e exemplo** módulo ao seu experimento e conecte-se o conjunto de dados.

2. Para **partição ou no modo de amostragem**, selecione **atribuir a dobras**.

3. **Usar substituição no particionamento**: Selecione esta opção se desejar que a linha de amostra para ser colocado de volta no pool de linhas para reutilização potencial. Como resultado, a mesma linha pode ser atribuída a vários dobras.

    Se você não usar substituição (a opção padrão), a linha de amostra não é colocada de volta no pool de linhas para reutilização potencial. Como resultado, cada linha podem ser atribuída a apenas uma partição.

4. **Divisão aleatória**:  Selecione esta opção se desejar que as linhas a ser atribuído aleatoriamente para dobras.

    Se você não selecionar essa opção, as linhas são atribuídas a dobras usando o método round robin.

5. **Propagação aleatória**: Opcionalmente, digite um número inteiro a ser usado como o valor de semente. Essa opção é importante se você quiser que as linhas sejam divididas da mesma forma toda vez. Caso contrário, o valor padrão de 0 significa que um aleatório semente de início será usado.

6. **Especifique o método particionador**: Indique como deseja dados a ser particionadas para cada partição, usando as seguintes opções:

    - **Particionar uniformemente**: Use esta opção para colocar um número igual de linhas em cada partição. Para especificar o número de partições de saída, digite um número inteiro na **especificar o número de dobras para dividir uniformemente em** caixa de texto.

    - **Partição com proporções personalizadas**: Use esta opção para especificar o tamanho de cada partição como uma lista separada por vírgulas.

        Por exemplo, se você deseja criar três partições, com a primeira partição que contém 50% dos dados e as duas partições restantes contendo 25% dos dados, clique o **lista de proporções separadas por vírgula** caixa de texto, e Digite esses números: `.5, .25, .25`

        A soma de todos os tamanhos de partição deve adicionar até exatamente 1.

        - Se você inserir números adicionados a **menor que 1**, uma partição adicional é criada para manter as linhas restantes. Por exemplo, se você digitar a partição de valores.2 e.3, um terceiro é criada que contém o restantes 50 por cento de todas as linhas.

        - Se você inserir números adicionados a **mais de 1**, ocorrerá um erro quando você executar o teste.

7. **Divisão estratificada**: Selecione esta opção se você quiser que as linhas a ser estratificada quando divididas e, em seguida, escolha o _coluna strata_.

8. Execute o experimento.

    Com essa opção, o módulo gera vários conjuntos de dados, particionados usando as regras especificadas por você.

### <a name="use-data-from-a-predefined-partition"></a>Usar dados de uma partição predefinido  

Essa opção é usada quando você tiver dividido a um conjunto de dados em várias partições e agora deseja carregar cada partição por sua vez para análise posterior ou processamento.

1. Adicione a **partição e exemplo** módulo para o experimento.

2. Conectar-se à saída de uma instância anterior do **partição e exemplo**. Essa instância deve ter usado o **atribuir a dobras** opção para gerar um número de partições.

3. **Modo de partição ou exemplo**: Selecione **selecionar dobra**.

4. **Especifique qual dobra a ser exemplificada**: Selecione uma partição para usar, digitando seu índice. Índices de partição são baseados em 1. Por exemplo, se o conjunto de dados é dividido em três partes, as partições teria os índices de 1, 2 e 3.

    Se você digitar um valor de índice inválido, será gerado um erro de tempo de design: "Erro 0018: Conjunto de dados contém dados inválidos."

    Além de agrupar o conjunto de dados por dobras, você pode separar o conjunto de dados em dois grupos: uma partição de destino e todo o resto. Para fazer isso, digite o índice de uma única partição e, em seguida, selecione a opção **escolher complemento da dobra selecionada**, para obter tudo, exceto os dados na partição especificada.

5. Se você estiver trabalhando com várias partições, você deve adicionar instâncias adicionais do **partição e exemplo** módulo para tratar cada partição.

    Por exemplo, digamos que anteriormente particionados pacientes em quatro partições usando idade. Para trabalhar com cada partição individual, você precisa de quatro cópias do **partição e exemplo** módulo, e em cada um, selecione uma partição diferente, conforme mostrado abaixo. Ele não está correto usar o **atribuir a dobras** diretamente de saída.  

    [![Partição e exemplo](./media/partition-and-sample/partition-and-sample.png)](./media/partition-and-sample/partition-and-sample-lg.png#lightbox)

5. Execute o experimento.

    Com essa opção, o módulo produz um único conjunto de dados que contém apenas as linhas atribuídas a essa partição.

> [!NOTE]
>  Não é possível exibir as designações de dobra diretamente; eles estão presentes somente nos metadados.

## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 