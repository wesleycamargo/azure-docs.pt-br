---
title: "Use transformações de dados para a preparação de dados no Azure Machine Learning | Microsoft Docs"
description: "Este documento apresenta uma lista completa de transformações disponíveis para a preparação de dados do Azure Machine Learning."
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 10/09/2017
ms.openlocfilehash: 062143ae34450484cd66cd7364fb8839d697e483
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2017
---
# <a name="use-data-transforms-for-data-preparation-in-azure-machine-learning"></a>Use transformações de dados para a preparação de dados no Azure Machine Learning

Uma *transformação* no Azure Machine Learning consome dados em um determinado formato, executa uma operação nos dados (como alterar o tipo de dados) e, em seguida, gera dados no novo formato. Cada transformação tem sua própria interface e comportamento. Ao encadear várias transformações juntas por meio de etapas no fluxo de dados, você pode executar transformações complexas e repetíveis nos dados. Essa é a base da funcionalidade de preparação de dados.

A seguir estão as transformações disponíveis no Azure Machine Learning. 

## <a name="column-selection"></a>Seleção de coluna 
Muitas das transformações nessa lista funcionam em uma ou em várias colunas. Para selecionar várias colunas, use a tecla Ctrl. Para selecionar um intervalo de colunas, use a tecla Shift.

## <a name="transforms-from-the-main-menu-or-the-grid-header"></a>Transformações do menu principal ou do cabeçalho de grade 
Acesse Transformações na opção Transformações no menu principal. Você também pode selecionar as transformações clicando com o botão direito do mouse no nome da coluna na grade de dados. Se várias colunas forem selecionadas, clicar com o botão direito do mouse em qualquer uma delas apresentará um menu de transformações.

O menu de clique com o botão direito do mouse mostra apenas as transformações válidas para o tipo de dados selecionado. O menu principal mostra todas as transformações, mas desabilita aquelas que não são relevantes para as colunas selecionadas.

Um pequeno subconjunto de transformações contextuais está disponível clicando com o botão direito do mouse em uma célula. Essas transformações são Copiar, Substituir e Filtrar. Elas são sensíveis ao tipo de dados, portanto, as opções para uma coluna de números são diferentes das de uma coluna de cadeia de caracteres.

## <a name="derive-column-by-example"></a>Derivar Colunas por Exemplo
Use essa transformação para criar uma nova coluna como uma derivada de uma ou mais colunas existentes. A transformação consulta as colunas de entrada (selecionadas) e o exemplo apresentado e determina a saída desejada na nova coluna. 

Para usá-la, selecione uma ou mais colunas. Adicione uma nova coluna derivada (em branco) por exemplo. Digite um exemplo de como você deseja ver na coluna derivada (supondo que seja derivada de outras colunas) e a tecnologia “Por exemplo” tentará preencher todas as outras células na coluna. 

Para exemplos complicados, poderá ser necessário fornecer mais de um exemplo. Para fazer isso, selecione outra célula e digite outro exemplo.

A tecnologia de "Por exemplo" usa colunas selecionadas para tentar determinar o significado de um exemplo. Se nenhuma coluna estiver selecionada quando essa transformação for chamada, todas as células da linha atual serão usadas. Selecionar apenas as colunas necessárias leva a resultados mais precisos.

Você pode selecionar colunas antes de invocar a transformação. Após a abertura do editor de transformação, as caixas de seleção na parte superior de cada coluna indicarão quais delas estão selecionadas como entradas. É possível adicionar ou remover colunas da seleção usando as caixas de seleção nos cabeçalhos de coluna.

Para obter uma explicação mais detalhada da transformação **Coluna Derivada por Exemplo**, juntamente com mais exemplos, consulte [Referência de Derivar Colunas por Exemplo](data-prep-derive-column-by-example.md).  

## <a name="split-column-by-example"></a>Dividir Colunas por Exemplo
Essa transformação usa uma coluna existente e, com o mecanismo de “Por exemplo”, tenta dividir a coluna em *n* outras colunas. É possível executar a divisão automática nas colunas geradas subsequentes.

Para obter uma explicação mais detalhada da transformação **Dividir Coluna por Exemplo**, juntamente com mais exemplos, consulte [Referência de Dividir Colunas por Exemplo](data-prep-split-column-by-example.md).

## <a name="expand-json"></a>Expandir JSON

Essa transformação permite que os usuários adicionem várias colunas expandindo uma coluna com texto JSON válido.

Para obter uma explicação mais detalhada da transformação **Expandir JSON**, juntamente com mais exemplos, consulte [Referência de Expandir JSON](data-prep-expand-json.md).


## <a name="combine-columns-by-example"></a>Combinar colunas por exemplo

Essa transformação adiciona uma nova coluna combinando valores de várias colunas. 

Para obter uma explicação mais detalhada da transformação **Combinar Colunas por Exemplo**, juntamente com mais exemplos, consulte [Referência de Combinar Colunas por Exemplo](data-prep-combine-columns-by-example.md).


## <a name="duplicate-column"></a>Duplicar coluna
Essa transformação faz uma cópia exata de uma ou mais colunas selecionadas e fornece a cada uma delas um novo nome derivado do nome da coluna original.

## <a name="text-clustering"></a>Clustering de texto 
Essa transformação é projetada para selecionar valores inconsistentes que devem ser iguais e agrupá-los.  

Com essa transformação, os valores em uma única coluna são analisados quanto à similaridade e agrupados em clusters. Para cada cluster, há um valor canônico, que é o valor que substitui todas as instâncias no cluster e todos os valores de instância. Os clusters completos podem ser removidos e o valor canônico pode ser editado. As instâncias podem ser removidas de um determinado cluster. O filtro de limite de pontuação de similaridade usado para agrupar instâncias em um cluster pode ser alterado.

Por padrão, essa transformação substitui todos os valores de instância de cluster pelo valor canônico para esse cluster, criando uma nova coluna para conter os novos valores. Você também pode adicionar a pontuação de similaridade para cada instância a uma nova coluna (que pode ser nomeada) para uso posteriormente no fluxo de dados.

## <a name="replace-values"></a>Substituir valores
Use essa transformação para substituir uma cadeia de caracteres por outra. A cadeia de caracteres de origem pode ser uma cadeia de caracteres parcial ou uma célula completa, e a substituição pode ser aplicada a uma única coluna ou a várias. A cadeia de caracteres de pesquisa é compatível com pesquisas de caracteres especiais, bem como a caracteres normais. 

## <a name="replace-na-values"></a>Substituir valores NA
Use essa transformação para substituir as várias formas de NA (N/A, NA, null, NaN etc.) ou substituir cadeias de caracteres vazias por um único valor para torná-las consistentes. Essa transformação é compatível com uma ou várias colunas e só será listada quando uma coluna for selecionada. Não está presente no menu de transformação principal quando nenhuma coluna está selecionada.

## <a name="replace-missing-values"></a>Substituir valores ausentes
Essa transformação substitui dados ausentes por um único valor e é compatível com uma ou várias colunas. Essa transformação é listada apenas quando uma coluna é selecionada. Não está presente no menu de transformação principal quando nenhuma coluna está selecionada.

## <a name="replace-error-values"></a>Substituir valores de erro
Essa transformação substitui erros por um único valor e é compatível com uma ou várias colunas. Essa transformação é listada apenas quando uma coluna é selecionada. Não está presente no menu de transformação principal quando nenhuma coluna está selecionada.

## <a name="trim-string"></a>Cortar cadeia de caracteres
Essa transformação remove caracteres de “espaço em branco” à esquerda e à direita (incluindo espaços, tabulações etc.) de uma ou de várias colunas.

## <a name="adjust-precision"></a>Ajustar precisão
Essa transformação define o número de casas decimais para uma coluna numérica.

## <a name="rename-column"></a>Renomear coluna
Essa transformação altera o nome da coluna selecionada. Você também pode invocá-la embutida no cabeçalho da coluna clicando no nome da coluna.

## <a name="remove-column"></a>Remover coluna
Essa transformação remove as colunas selecionadas e funciona em uma ou várias colunas. 

## <a name="keep-column"></a>Manter coluna
Essa transformação mantém somente as colunas selecionadas e funciona em uma ou várias colunas.

## <a name="handle-path-column"></a>Coluna de caminho do identificador
Durante a importação de um arquivo, uma coluna de caminho é automaticamente adicionada ao conjunto de dados pelo recurso **Adicionar Fonte de Dados**. Ela contém o nome de arquivo totalmente qualificado que compõe o caminho para o conjunto de dados. Essa transformação adiciona ou remove essa coluna adicional do conjunto de dados.

## <a name="convert-field-type-to-numeric"></a>Converter o tipo de campo para numérico
Essa transformação altera o tipo de coluna para numérico. Você pode especificar o separador para dados não inteiros. Por padrão, essa transformação não solicita o separador, assim, use o item de menu **Editar** para invocar o editor. Essa transformação funciona em uma única coluna ou em várias.

## <a name="convert-field-type-to-date"></a>Converter o tipo de campo para data
Essa transformação altera o tipo de coluna para data. Um formato de data/hora padrão é usado, mas pode ser substituído usando diretivas `strftime`. Você também pode colocar valores temporais com uma data fixa.

Por padrão, essa transformação não solicita o formato, assim, use o item de menu **Editar** na etapa resultante para invocar o editor. Essa transformação funciona em uma única coluna ou em várias.

Apenas as datas entre 22-9-1677 e 11-4-2262 podem ser convertidas para o tipo de data.

## <a name="convert-field-type-to-boolean"></a>Converter o tipo de campo para booliano
Essa transformação altera o tipo de coluna para verdadeiro/falso. Ela é compatível com mapeamento de vários valores para true ou false e você pode editar esses mapeamentos. Ela também é compatível com uma constante em que é possível mapear valores que não existem nas tabelas de mapeamento true/false. Essa transformação funciona em uma única coluna ou em várias.

## <a name="convert-field-type-to-string"></a>Converter o tipo de campo para cadeia de caracteres
Essa transformação muda o tipo de coluna para cadeia de caracteres e funciona em uma coluna ou várias.

## <a name="convert-unix-timestamp-to-datetime"></a>Converter o carimbo de data/hora do Unix para DateTime
Essa transformação entende o formato do carimbo de data/hora Unix, mesmo que seja representado como uma cadeia de caracteres nos dados. Ela converte o carimbo de data/hora corretamente em um tipo de data na preparação de dados.

## <a name="filter"></a>Filter
Essa transformação dá suporte à filtragem de linhas com base nos valores de uma ou mais colunas. As condições do filtro dependem do tipo de dados de cada coluna, assim, você pode filtrar colunas de cadeia de caracteres por "contém" ou "não contém." Colunas numéricas podem ser filtradas por condições “maior que” ou “menor que”.

Quando a transformação **Filtro** é invocada no menu principal ou pelo clique com o botão direito do mouse no cabeçalho de uma coluna, a opção para dividir as linhas com falha em outro fluxo de dados é disponibilizada. Assim, o fluxo de dados principal continua com as linhas filtradas no lado de **dentro** é criado e um novo fluxo de dados contendo todas as linhas filtradas no lado de **fora**.

## <a name="use-first-row-as-headers"></a>Usar a primeira linha como cabeçalho
Essa transformação promove a primeira linha do conjunto de dados a nomes de coluna. Se algumas colunas não tiverem dados na primeira linha, um nome será gerado automaticamente. Usar essa transformação significa que a importação de dados começa na linha 2, no mínimo. Dependendo da configuração de **Ignorar Linhas**, a importação pode ser iniciada ainda mais adiante no conjunto de dados. Você também pode usar isso para remover a promoção e usar apenas nomes gerados automaticamente.

## <a name="join"></a>Ingressar
Essa transformação é usada para unir dois fluxos de dados. É possível selecionar qual saída da união deve ser o resultado: as linhas bem-sucedidas da união, as linhas com falha à “esquerda” da união ou as linhas com falha à “direita” da união.

A transformação de **União** é iniciada em um fluxo de dados único e seleciona esse fluxo de dados como um lado da união. Em seguida, ela solicita que você selecione outro fluxo de dados para o outro lado da união. Depois de selecionar os dois fluxos, a transformação exige que uma única coluna em cada lado da associação seja selecionada para unir. Se a união precisar de mais de uma coluna, crie uma coluna derivada antes de inicializar a transformação para criar uma nova coluna (concatenada) a ser usada apenas para a união. A transformação tenta sugerir chaves de união e, se possível, gerar a coluna derivada automaticamente.

Após a conclusão, uma exibição do diagrama de Sankey da união é apresentada. A largura de uma linha em relação a outra representa o número de linhas que se movem através dessa parte do fluxo de união. Usando o painel à direita, você pode selecionar as linhas bem-sucedidas, as falha com falha à esquerda ou as linhas com falha à direita exclusivamente. Você também pode escolher uma única ramificação.

## <a name="append-rows"></a>Acrescentar linhas
Essa transformação acrescenta dados de outro fluxo de dados ao fluxo de dados atual. Ela mapeia as colunas por posição para adicionar as novas linhas no final.

## <a name="append-columns"></a>Acrescentar colunas
Essa transformação acrescenta novas colunas de outro fluxo de dados ao fluxo de dados atual. Ela adiciona as novas colunas à direita. Ela não tenta alinhar dados em linhas.

## <a name="summarize"></a>Resumir
Essa transformação calcula agregações para a combinação de valores únicos em uma ou mais colunas selecionadas. 

Agregações com suporte: COUNT, SUM, MIN, MAX, MEAN, VARIANCE e STANDARD DEVIATION. A lista de agregações para uma determinada coluna é filtrada para apenas as agregações que se aplicam a esse tipo de dados. As agregações que não se aplicam são desabilitadas. Como exemplo, não é possível calcular a MÉDIA de uma coluna de cadeia de caracteres, mas é possível calcular o MÍNIMO e o MÁXIMO.

Quando o editor estiver disponível, arraste do cabeçalho de coluna até o painel na parte superior esquerda, no qual as colunas a serem agregadas são exibidas. Esse painel é hierárquico, portanto, você pode fazer agregações aninhadas. O painel do editor no canto superior direito é usado para selecionar qual agregação aplicar a uma coluna. Uma única coluna pode ser agregada a uma ou mais vezes. Depois de pelo menos uma agregação ter sido escolhida, a grade na parte inferior direita visualiza os dados em sua forma agregada. 

Essa transformação é análoga a um `ANSI-SQL GROUP BY`.

## <a name="remove-duplicates"></a>Remover duplicatas
Essa transformação remove toda a linha na qual existem valores duplicados em uma ou mais colunas. Se nenhuma coluna for escolhida, as únicas linhas removidas serão aquelas em que os valores de coluna são iguais.

## <a name="sort"></a>Classificar
Essa transformação classifica os dados. A classificação pode ser feita por uma única coluna ou várias e cada coluna pode ser classificada em ordem crescente (o padrão) ou decrescente (isso pode ser alterado no editor).

Essa transformação é análoga a um `ANSI-SQL ORDER BY`.

## <a name="output-transforms"></a>Transformações de saída
As seguintes transformações produzem dados. É possível ter vários blocos de gravação em um único fluxo para gravar os dados em pontos diferentes.

### <a name="write-to-csv"></a>Gravar em CSV
Essa transformação grava os dados em formato CSV do ponto atual no fluxo de dados. Ela controla o local (local ou remoto) e várias configurações em relação ao arquivo.

### <a name="write-to-parquet"></a>Gravar no Parquet
Essa transformação grava os dados em formato Parquet do ponto atual no fluxo de dados. Ela controla o local (local ou remoto) e várias configurações em relação ao arquivo.

## <a name="script-based-transforms"></a>Transformações baseadas em script
As transformações a seguir usam script (Python) para executar a funcionalidade que está ausente no produto principal. 

>[!NOTE]
>Antes de usar qualquer uma dessas transformações, leia [Usando a extensibilidade do Python](data-prep-python-extensibility-overview.md).

### <a name="add-column-script"></a>Adicionar coluna (script)
Essa transformação adiciona uma coluna aos dados usando uma expressão Python.
Para obter mais informações, consulte o [Código Python de exemplo para derivar novas colunas](data-prep-appendix10-sample-custom-column-transforms-python.md).

### <a name="advanced-filter-script"></a>Filtro avançado (script)
Use essa transformação para gravar um filtro de nível de linha do Python.
Para obter mais informações, consulte [Exemplos de expressões de filtro](data-prep-appendix6-sample-filter-expressions-python.md).

### <a name="transform-dataflow-script"></a>Fluxo de dados da transformação (script)
Essa transformação aplica Python a todo o conjunto de dados.
Para obter mais informações, consulte [Exemplo de transformação Transformar fluxo de dados](data-prep-appendix7-sample-transform-data-flow-python.md).

Essa transformação também pode aplicar Python a uma partição de dados inteira.
Para obter mais informações, consulte [Exemplo de transformação Transformar fluxo de dados](data-prep-appendix7-sample-transform-data-flow-python.md).

### <a name="write-dataflow-script"></a>Fluxo de dados de gravação (script)
Essa transformação usa Python para gravar um conjunto de dados inteiro.
Para obter mais informações, consulte o [Python de exemplo para derivar novas colunas](data-prep-appendix9-sample-destination-connections-python.md).



