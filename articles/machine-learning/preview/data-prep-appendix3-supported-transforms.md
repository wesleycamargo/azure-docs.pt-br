---
title: "Transformações com suporte disponíveis com a preparação dos dados do Azure Machine Learning | Microsoft Docs"
description: "Este documento fornece uma lista completa de transformações disponíveis para a preparação de dados do Azure ML"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/07/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: fd3f8157e27847b99e59465063111a6d6c8c713d
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---
# <a name="supported-transforms-for-this-release"></a>Transformações com suporte para esta versão
O artigo a seguir lista e resume as transformações disponíveis para essa versão. 

As transformações com suporte para esta versão são descritas abaixo.

## <a name="column-selection"></a>Seleção de coluna 
Muitas das transformações listadas abaixo funcionam em uma única coluna ou muitas. Para selecionar várias colunas, use Control Clique em ada coluna ou use Shift Clique em um intervalo de colunas.

## <a name="transforms-from-main-menu-andor-grid-header"></a>Transformações do menu principal e/ou cabeçalho de grade 
As transformações podem ser acessadas na opção Transformações no menu principal. As transformações também podem ser selecionadas clicando com o botão direito do mouse no nome da coluna na grade de dados. Se várias colunas forem selecionadas, clicar com o botão direito do mouse em qualquer uma delas fornece um menu de transformações.

Somente transformações válidas para o tipo de dados selecionado são oferecidas no menu de clique com o botão direito do mouse, o menu principal oferece todas as transformações, mas esmaece as transformações que não são relevantes para as colunas selecionadas.

Um pequeno subconjunto de transformações contextuais está disponível clicando com o botão direito em uma célula. Essas transformações são Copiar, Substituir e Filtrar, elas são sensíveis ao tipo de dados, portanto as opções para uma coluna de números são diferentes das de uma coluna de cadeia de caracteres.

## <a name="derive-column-by-example"></a>Derivar Colunas por Exemplo
Essa transformação permite a criação de uma nova coluna como um derivado de uma ou mais colunas existentes. Ela faz isso observando as colunas de entrada (selecionadas) e o exemplo fornecido para determinar a saída desejada na nova coluna. 

Para usá-la, selecione uma ou mais colunas. Adicione uma nova coluna derivada (em branco) por exemplo. Digite um exemplo de como você deseja ver na coluna derivada (supondo que é derivada de outras colunas) e a tecnologia de "Por exemplo" tenta preencher todas as outras células na coluna. 

Para obter exemplos complicados, pode ser necessário fornecer mais de um exemplo, selecionar outra célula e digitar outro exemplo.

A tecnologia de "Por exemplo" usa colunas selecionadas para tentar determinar o significado de um exemplo. Se nenhuma coluna estiver selecionada quando essa transformação for chamada, todas as células da linha atual serão usadas. Selecionar apenas as colunas necessárias leva a resultados mais precisos.

Você pode selecionar colunas antes de invocar a transformação. Depois que o editor de transformação tiver sido iniciado, é possível ver quais colunas são selecionadas como entradas por meio de caixas de seleção na parte superior de cada coluna. A adição e a remoção de colunas da seleção podem ser concluídas usando as caixas de seleção nos cabeçalhos de coluna.

Para obter uma explicação mais detalhada da transformação **Coluna derivada por exemplo**, juntamente com mais exemplos, consulte: [Referência de derivar colunas por exemplo](data-prep-derive-column-by-example.md)  

## <a name="split-column-by-example"></a>Dividir Colunas por Exemplo
Usa uma coluna existente e usando o mecanismo de "Por exemplo" tenta dividir a coluna em n outras colunas. É possível executar a divisão automática nas colunas geradas subsequentes.

Para obter uma explicação mais detalhada da transformação **Dividir coluna por exemplo**, juntamente com mais exemplos, consulte: [Referência de dividir colunas por exemplo](data-prep-split-column-by-example.md)

## <a name="expand-json"></a>Expandir JSON

A transformação **Expandir JSON** permite que os usuários adicionem várias colunas expandindo uma coluna com texto JSON válido.

Para obter uma explicação mais detalhada de **Expandir JSON**, juntamente com mais exemplos, consulte [Referência de expandir JSON](data-prep-expand-json.md)


## <a name="combine-columns-by-example"></a>Combinar colunas por exemplo

Permite que o usuário adicione uma nova coluna combinando valores de várias colunas. 

Para obter uma explicação mais detalhada da transformação **Combinar colunas por exemplo**, juntamente com mais exemplos, consulte: [Referência de combinar colunas por exemplo](data-prep-combine-columns-by-example.md)


## <a name="duplicate-column"></a>Duplicar coluna
Essa transformação faz uma cópia exata de uma coluna e fornece a ela um novo nome derivado do nome da coluna original. Essa transformação dá suporte a uma ou mais colunas.

## <a name="text-clustering"></a>Clustering de texto 
Essa transformação é projetada para selecionar valores inconsistentes que devem ser iguais e agrupá-los.  

Ao executar essa transformação, os valores em uma única coluna são analisados quanto à semelhança. Em seguida, eles são agrupados em clusters. Para cada cluster, há um valor canônico, que é o valor que substitui todas as instâncias no cluster e os valores de instância. Os clusters completos podem ser removidos, o valor canônico pode ser editado. As instâncias podem ser removidas de um determinado cluster. Além disso, o filtro de limite de pontuação de similaridade que é usado para agrupar instâncias em um cluster pode ser alterado.

Por padrão essa transformação substitui todos os valores de instância de cluster pelo valor canônico para esse cluster, criando uma nova coluna para conter os novos valores. Também é possível adicionar a pontuação de similaridade, para cada instância, a uma nova coluna (que pode ser nomeada) para permitir seu uso posteriormente no fluxo de dados.

## <a name="replace-values"></a>Substituir valores
Essa transformação permite que uma cadeia de caracteres seja substituída por outra, a cadeia de caracteres de origem pode ser uma cadeia de caracteres parcial ou uma célula inteira. A substituição pode se aplicar a uma única coluna ou a várias. A cadeia de caracteres de pesquisa dá suporte a pesquisas de caracteres especiais, como também aos caracteres normais. 

## <a name="replace-na-values"></a>Substituir valores NA
Permite que as várias formas diferentes de NA (N/A, NA, null, NaN etc.) ou cadeias de caracteres vazias sejam substituídas por um único valor para torná-las consistentes. Essa transformação dá suporte a uma ou mais colunas. Essa transformação é listada apenas quando uma coluna está selecionada e não está presente no menu de transformação principal quando nenhuma coluna está selecionada.

## <a name="replace-missing-values"></a>Substituir valores ausentes
Executa a substituição de dados ausentes por um único valor. Essa transformação dá suporte a uma ou mais colunas. Essa transformação é listada apenas quando uma coluna está selecionada e não está presente no menu de transformação principal quando nenhuma coluna está selecionada.

## <a name="replace-error-values"></a>Substituir valores de erro
Executa a substituição de erros com um único valor. Essa transformação dá suporte a uma ou mais colunas. Essa transformação é listada apenas quando uma coluna está selecionada e não está presente no menu de transformação principal quando nenhuma coluna está selecionada.

## <a name="trim-string"></a>Cortar cadeia de caracteres
Remove caracteres de “espaço em branco” à esquerda e à direita (inclui espaços, tabulações etc.) de uma única coluna ou de várias.

## <a name="adjust-precision"></a>Ajustar precisão
Permite que o número de casas decimais para uma coluna numérica a ser definido

## <a name="rename-column"></a>Renomear coluna
Altera o nome da coluna, essa transformação também pode ser invocada de forma embutida no cabeçalho da coluna clicando no nome da coluna.

## <a name="remove-column"></a>Remover coluna
Remove as colunas selecionadas, essa transformação funciona em uma única coluna ou em várias. 

## <a name="keep-column"></a>Manter coluna
Mantém apenas as colunas selecionadas, essa transformação funciona em uma única coluna ou em várias.

## <a name="handle-path-column"></a>Coluna de caminho do identificador
Durante a importação de um arquivo, uma coluna de caminho é automaticamente adicionada ao conjunto de dados pelo assistente. Ela contém o nome de arquivo totalmente qualificado que compõe o caminho para o conjunto de dados. Essa transformação adiciona ou remove essa coluna adicional do conjunto de dados.

## <a name="convert-field-type-to-numeric"></a>Converter o tipo de campo para numérico
Altere tipo de coluna para numérico. Especifique o separador se houver dados não inteiros. Por padrão essa transformação não solicita o separador, use o item de menu **Editar** para invocar o editor. Essa transformação funciona em uma única coluna ou em várias.

## <a name="convert-field-type-to-date"></a>Converter o tipo de campo para data
Altere tipo de coluna para data. Um formato de data/hora padrão é usado, mas pode ser substituído usando diretivas `strftime`. Também é possível prefixar os valores temporais com uma data fixa.

Por padrão essa transformação não solicita o formato, use o item de menu **Editar** na etapa resultante para invocar o editor. Essa transformação funciona em uma única coluna ou em várias.

Apenas as datas entre 22-9-1677 e 11-4-2262 podem ser convertidas para o tipo de data.

## <a name="convert-field-type-to-boolean"></a>Converter o tipo de campo para booliano
Altere tipo de coluna para verdadeiro/falso. Essa transformação dá suporte ao mapeamento de vários valores para verdadeiro ou falso, é possível editar esses mapeamentos. Ele também dá suporte a uma constante para mapear valores para os que não existem nas tabelas de mapeamento verdadeiro/falso. Essa transformação funciona em uma única coluna ou em várias.

## <a name="convert-field-type-to-string"></a>Converter o tipo de campo para cadeia de caracteres
Altere tipo de coluna para cadeia de caracteres. Essa transformação funciona em uma única coluna ou em várias.

## <a name="convert-unix-timestamp-to-datetime"></a>Converter o carimbo de data/hora do Unix para DateTime
Essa transformação entende o formato de carimbo de data/hora do Unix se ele for representado como uma cadeia de caracteres nos dados e o converte corretamente para um tipo de data na preparação de dados.

## <a name="filter"></a>Filter
A transformação de filtro dá suporte à filtragem de linhas com base nos valores de uma ou mais colunas, as condições do filtro são dependentes do tipo de dados de cada coluna, portanto, é possível filtrar colunas de cadeia de caracteres por “contém” ou “não contém”, mas não por valores numéricos. No entanto, para colunas numéricas, as condições "maior que" "menor que" se aplicam, mas não para colunas de cadeia de caracteres.

No caso em que a transformação de filtro é invocada no menu principal ou do clique com botão direito do mouse no cabeçalho de uma coluna, a opção para dividir as linhas com falha em outro fluxo de dados está disponível. Assim, o fluxo de dados principal continua com as linhas filtradas para **dentro** e um novo fluxo de dados é criado contendo todas as linhas que foram filtradas para **fora**.

## <a name="use-first-row-as-headers"></a>Usar a primeira linha como cabeçalhos
Essa transformação promove a primeira linha do conjunto de dados para ser os nomes das colunas, caso algumas colunas não tenham dados na primeira linha, o nome é gerado automaticamente. Usar essa transformação significa que a importação de dados começa na linha 2, no mínimo. Dependendo da configuração de Ignorar Linhas, a importação pode ser iniciada ainda mais para baixo no conjunto de dados. Ela também pode ser usada para remover a promoção e ter apenas nomes gerados automaticamente.

## <a name="join"></a>Ingressar
Essa transformação é usada para unir dois fluxos de dados. Ela permite a seleção de qual saída da união deve ser o resultado, as linhas bem-sucedidas da união, as linhas com falha à “esquerda” da união ou as linhas com falha à “direita” da união.

O Assistente de união é iniciado de um fluxo de dados único e seleciona esse fluxo de dados como um lado da união, em seguida, solicita ao usuário outro fluxo de dados para o outro lado da união. Depois que os dois fluxos foram selecionados, o assistente requer que uma única coluna em cada lado da união seja selecionada para unir. Se a união precisar de mais de uma coluna, crie uma coluna derivada antes de iniciar o assistente para criar uma nova coluna (concatenada) a ser usada apenas para a união. O assistente tenta sugerir chaves de união e, se possível, gera a coluna derivada automaticamente.

Depois que a união foi concluída, é apresentada uma exibição de Diagrama de Sankey da união, a largura das linhas de relação umas às outras é representativa do número de linhas percorrendo aquela parte do fluxo de união. O painel à direita permite a seleção de linhas com êxito, à esquerda com falha, à direita com falha exclusivamente ou é possível escolher apenas um branch.

## <a name="append-rows"></a>Acrescentar linhas
Essa transformação acrescenta dados de outro fluxo de dados ao atual, ele mapeia as colunas por posição para adicionar as novas linhas no final.

## <a name="append-columns"></a>Acrescentar colunas
Essa transformação acrescenta novas colunas de outro fluxo de dados ao atual, ela adiciona as novas colunas à direita, não tenta alinhar os dados em linhas.

## <a name="summarize"></a>Resumir
Selecione uma coluna e compute os agregados para essa combinação de valores únicos.

Agregações com suporte:  COUNT, SUM, MIN, MAX, MEAN, VARIANCE, STANDARD DEVIATION. A lista de agregações para uma determinada coluna é filtrada para apenas as que se aplicam a esse tipo de dados. As agregações que não se aplicam estão esmaecidas. Como exemplo, não é possível calcular a média de uma coluna de cadeia de caracteres, mas é possível calcular o mínimo e o máximo.

Semelhante a um ANSI-SQL GROUP BY

Depois que o editor está disponível, arraste do cabeçalho de coluna até o painel na parte superior esquerda, as colunas a serem agregadas são exibidas aqui. Este painel é hierárquico, portanto, é possível fazer agregações aninhadas. O painel do editor na parte superior direita é usada para selecionar qual agregação aplicar a uma coluna. Uma única coluna pode ser agregada a uma ou mais vezes. Depois de pelo menos uma agregação ter sido escolhida, a grade na parte inferior direita visualiza os dados em sua forma agregada. 

## <a name="remove-duplicates"></a>Remover duplicatas
Essa transformação remove toda a linha em que existem valores duplicados. Os valores duplicados são definidos por uma ou mais colunas selecionadas na invocação de transformação ou acionando o editor. Se nenhuma coluna for escolhida, as únicas linhas que serão removidas são as em que os valores de coluna são iguais.

## <a name="sort"></a>Classificar
Essa transformação classifica os dados, isso pode ser feito por uma única coluna ou muitas, cada coluna pode ser classificada em ordem crescente (o padrão) ou decrescente (pode ser alterado no editor).

Semelhante a um ANSI-SQL ORDER BY

## <a name="output-transforms"></a>Transformações de saída
As transformações que ocorrem após a saída de dados. É possível ter vários blocos de gravação em um único fluxo para ser capaz de gravar os dados em pontos diferentes.

### <a name="write-to-csv"></a>Gravar em CSV
Essa transformação grava os dados em formato CSV do ponto atual no fluxo de dados.  Ela permite o controle de local (local ou remoto) e várias configurações em relação ao arquivo.

### <a name="write-to-parquet"></a>Gravar no Parquet
Essa transformação grava os dados em formato Parquet do ponto atual no fluxo de dados. Ela permite o controle de local (local ou remoto) e várias configurações em relação ao arquivo.

## <a name="script-based-transforms"></a>Transformações baseadas em script
As transformações que ocorrem após todo uso do script (Python) para executar a funcionalidade que está ausente no produto principal. 
[Antes de usar qualquer uma dessas transformações, leia a extensibilidade em doc aqui](data-prep-python-extensibility-overview.md):

### <a name="add-column-script"></a>Adicionar coluna (Script)
Permite que uma coluna seja adicionada aos dados usando uma expressão do Python [Consulte o Apêndice 10 para obter mais informações](data-prep-appendix10-sample-custom-column-transforms-python.md)

### <a name="advanced-filter-script"></a>Filtro avançado (Script)
Permite que um filtro de nível de linha do Python seja gravado [Consulte o Apêndice 6 para obter mais informações](data-prep-appendix6-sample-filter-expressions-python.md)

### <a name="transform-dataflow-script"></a>Transformar fluxo de dados (Script)
Permite que o Python seja aplicado a todo o conjunto de dados

[Consulte o Apêndice 7 para obter mais informações](data-prep-appendix7-sample-transform-data-flow-python.md)

### <a name="transform-dataflow-script"></a>Transformar fluxo de dados (Script)
Permite que o Python seja aplicado a uma partição de dados inteira

[Consulte o Apêndice 7 para obter mais informações](data-prep-appendix7-sample-transform-data-flow-python.md)

### <a name="write-dataflow-script"></a>Gravar fluxo de dados (Script)
Permite que o Python seja aplicado à gravação e a todo o conjunto de dados [Consulte o Apêndice 10 para obter mais informações](data-prep-appendix9-sample-destination-connections-python.md)




