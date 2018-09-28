---
title: Comece a usar o Log Analytics no portal do Azure | Microsoft Docs
description: Este artigo fornece um tutorial para usar o Log Analytics no portal do Azure para escrever consultas.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 3bcc5368a99dc7c7c32381ca0226119d81fc2c0a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978192"
---
# <a name="get-started-with-log-analytics-in-the-azure-portal"></a>Comece a usar o Log Analytics no portal do Azure

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Neste tutorial, você aprenderá a usar a página Log Analytics no portal do Azure (atualmente em pré-visualização) para gravar as consultas do Log Analytics. Ele irá ensiná-lo como para:

- Escrever consultas simples
- Compreender o esquema de seus dados
- Filtrar, classificar e agrupar resultados
- Aplicar um intervalo de tempo
- Criar gráficos
- Salvar e carregar consultas
- Exportar e compartilhar consultas


## <a name="meet-the-log-analytics-page"></a>Atender a página do Log Analytics 
Página do Log Analytics é uma ferramenta da web usada para gravar e executar consultas do Log Analytics do Azure. Abra-o, selecionando **Logs (versão prévia)** no menu do Log Analytics. Ele começa com uma nova consulta em branco.

![Página inicial](media/get-started-analytics-portal/homepage.png)



## <a name="basic-queries"></a>Consultas básicas
As consultas podem ser usadas para pesquisar termos, identificar tendências, analisar padrões e fornecer muitas outras informações com base em seus dados. Comece com uma consulta básica:

```Kusto
Event | search "error"
```

Essa consulta pesquisa a tabela _Evento_ em busca de registros que contenham o termo "erro" em qualquer propriedade.

Consultas podem começar com um nome de tabela ou um **pesquisa** comando. O exemplo acima começa com o nome da tabela _Evento_, que define o escopo da consulta. O caractere de barra vertical (|) separa os comandos, portanto, a saída da primeira na entrada do comando a seguir. Você pode adicionar qualquer número de comandos para uma única consulta.

Outra maneira de escrever essa mesma consulta seria:

```Kusto
search in (Event) "error"
```

Neste exemplo, a **pesquisa**  tem escopo para a tabela _Evento_ e todos os registros nessa tabela são pesquisados pelo termo "erro".

## <a name="running-a-query"></a>Executando uma consulta
Executar uma consulta clicando o **executados** botão ou pressionando **Shift + Enter**. Considere os detalhes a seguir que determinam o código que será executado e os dados retornados:

- Quebras de linha: uma única pausa torna sua consulta mais clara. Várias quebras de linha o dividiram em consultas separadas.
- Cursor: Coloque o cursor em algum lugar dentro da consulta para executá-lo. A consulta atual é considerada como sendo o código até que uma linha em branco seja encontrada.
- Intervalo - um intervalo de tempo de tempo _últimas 24 horas_ é definido por padrão. Para usar um intervalo diferente, use o seletor de tempo ou adicione um filtro de intervalo de tempo explícito à sua consulta.


## <a name="understand-the-schema"></a>Entenda o esquema
O esquema é uma coleção de tabelas agrupadas visualmente em uma categoria lógica. Várias das categorias são de soluções de monitoramento. A categoria _LogManagement_ contém dados comuns, como eventos do Windows e do Syslog, dados de desempenho e pulsações do cliente.

![Esquema](media/get-started-analytics-portal/schema.png)

Em cada tabela, os dados são organizados em colunas com diferentes tipos de dados, conforme indicado pelos ícones ao lado do nome da coluna. Por exemplo, a tabela _Evento_ mostrada na captura de tela contém colunas como _Computador_, que é texto, _EventCategory_, que é um número, e _TimeGenerated_ que é data/hora.

## <a name="filter-the-results"></a>Filtrar os resultados
Comece obtendo tudo na tabela _Evento_.

```Kusto
Event
```

Página do Log Analytics tem como escopo automaticamente os resultados por:

- Intervalo de tempo: por padrão, consultas são limitadas ao último 24 horas.
- Número de resultados: os resultados são limitados a um máximo de 10.000 registros.

Essa consulta é muito geral e retorna muitos resultados para ser útil. Você pode filtrar os resultados através dos elementos da tabela ou adicionando explicitamente um filtro à consulta. Os resultados da filtragem por meio dos elementos da tabela se aplicam ao conjunto de resultados existente, enquanto um filtro para a consulta em si retornará um novo conjunto de resultados filtrados e, portanto, poderá produzir resultados mais precisos.

### <a name="add-a-filter-to-the-query"></a>Adicionar um filtro à consulta
Há uma seta à esquerda de cada registro. Clique nessa seta para abrir os detalhes de um registro específico.

Passe o mouse sobre um nome de coluna para os ícones "+" e "-" a serem exibidos. Para adicionar um filtro que retornará somente os registros com o mesmo valor, clique no sinal "+". Clique em "-" para excluir registros com esse valor e, em seguida, clique em **executar** para executar a consulta novamente.

![Adicionar um filtro à consulta](media/get-started-analytics-portal/add-filter.png)

### <a name="filter-through-the-table-elements"></a>Filtrar por meio dos elementos de tabela
Agora vamos nos concentrar em eventos com severidade _erro_. Isso é especificado em uma coluna chamada _EventLevelName_. Você precisará rolar para a direita para ver esta coluna.

Clique no ícone Filtro ao lado do título da coluna e, na janela pop-up, selecione valores que _começa com_ o texto _erro_:

![Filter](media/get-started-analytics-portal/filter.png)


## <a name="sort-and-group-results"></a>Ordenar e agrupar resultados
Os resultados agora são reduzidos para incluir apenas eventos de erro do SQL Server, criados nas últimas 24 horas. No entanto, os resultados não são classificados de forma alguma. Para classificar os resultados por uma coluna específica, como _carimbo de data/hora_, por exemplo, clique no título da coluna. Um clique é classificado em ordem crescente, enquanto um segundo clique será classificado em ordem decrescente.

![Coluna de classificação](media/get-started-analytics-portal/sort-column.png)

Outra maneira de organizar os resultados é por grupos. Para agrupar os resultados por uma coluna específica, basta arrastar o cabeçalho da coluna acima das outras colunas. Para criar subgrupos, arraste outras colunas para a barra superior também.

![Grupos](media/get-started-analytics-portal/groups.png)

## <a name="select-columns-to-display"></a>Selecione colunas para exibir
A tabela de resultados geralmente inclui muitas colunas. Você pode achar que algumas das colunas retornadas não são exibidas por padrão, ou você pode querer remover algumas das colunas que são exibidas. Para selecionar as colunas para mostrar, clique no botão Colunas:

![Selecionar colunas](media/get-started-analytics-portal/select-columns.png)


## <a name="select-a-time-range"></a>Selecione um intervalo de tempo
Por padrão, a página de análise de Log se aplica a _últimas 24 horas_ intervalo de tempo. Para usar um intervalo diferente, selecione outro valor no seletor de tempo e clique em **Executar**. Além dos valores de predefinição, você pode usar o _intervalo de tempo personalizado_ opção para selecionar um intervalo absoluto para a sua consulta.

![Seletor de tempo](media/get-started-analytics-portal/time-picker.png)

Ao selecionar um intervalo de tempo personalizado, os valores selecionados estão em UTC, o que pode ser diferente do fuso horário local.

Se a consulta contiver explicitamente um filtro para _TimeGenerated_, o título do selecionador de hora mostrará _Set in query_. Seleção manual será desabilitada para evitar um conflito.


## <a name="charts"></a>Gráficos
Além de retornar os resultados em uma tabela, os resultados da consulta podem ser apresentados em formatos de visual. Use a seguinte consulta como um exemplo:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Por padrão, os resultados são exibidos em uma tabela. Clique em _Chart_ para ver os resultados em uma visualização gráfica:

![Gráfico de barras](media/get-started-analytics-portal/bar-chart.png)

Os resultados são mostrados em um gráfico de barras empilhadas. Clique em _Stacked Column_ e selecione _Pie_ para mostrar outra visualização dos resultados:

![Gráfico de pizza](media/get-started-analytics-portal/pie-chart.png)

Diferentes propriedades da visualização, como eixos xey, ou preferências de agrupamento e divisão, podem ser alteradas manualmente a partir da barra de controle.

Você também pode definir a visualização preferencial na própria consulta, usando o operador render.

### <a name="smart-diagnostics"></a>Diagnóstico inteligente
Em um gráfico de tempo, se houver uma alteração ou pico repentino em seus dados, você poderá ver um ponto em destaque na linha. Isso indica que _Smart Diagnostics_ identificou uma combinação de propriedades que filtram a mudança repentina. Clique no ponto para obter mais detalhes sobre o filtro e para ver a versão filtrada. Isso pode ajudar você a identificar o que causou a alteração:

![Diagnóstico inteligente](media/get-started-analytics-portal/smart-diagnostics.png)

## <a name="pin-to-dashboard"></a>Fixar no painel
Para fixar um diagrama ou tabela em um dos seus painéis compartilhados do Azure, clique no ícone de alfinete.

![Fixar no painel](media/get-started-analytics-portal/pin-dashboard.png)

Certas simplificações são aplicadas a um gráfico quando você o fixa em um painel:

- Colunas e linhas da tabela: para fixar uma tabela no painel, ela deve ter quatro colunas ou menos. Somente as primeiras sete linhas são exibidas.
- Restrição de tempo: as consultas são automaticamente limitadas aos últimos 14 dias.
- Restrição de contagem do escaninho: Se você exibir um gráfico que tenha muitos escaninhos discretos, os escaninhos menos povoados serão automaticamente agrupados em um único escaninho _outro_.

## <a name="save-queries"></a>Salvar consultas
Depois de criar uma consulta útil, você poderá salvá-lo ou compartilhar com outras pessoas. O **salvar** ícone está na barra superior.

Você pode salvar toda a página de consulta ou uma única consulta como uma função. Funções são consultas que também podem ser referenciadas por outras consultas. Para salvar uma consulta como uma função, você deve fornecer um alias de função, que é o nome usado para chamar essa consulta quando mencionado por outras consultas.

![Salvar função](media/get-started-analytics-portal/save-function.png)

As consultas do Log Analytics são sempre salvas em um espaço de trabalho selecionado e compartilhadas com outros usuários desse espaço de trabalho.

## <a name="load-queries"></a>Carregar consultas
O ícone do Query Explorer está na área superior direita. Isso lista todas as consultas salvas por categoria. Ele também permite que você marque consultas específicas como Favoritos para encontrá-las rapidamente no futuro. Clique duas vezes em uma consulta salva para adicioná-la à janela atual.

![Gerenciador de consultas](media/get-started-analytics-portal/query-explorer.png)

## <a name="export-and-share-as-link"></a>Exportar e compartilhar como link
Página do Log Analytics dá suporte a vários métodos de exportação:

- Excel: salve os resultados como um arquivo CSV.
- Power BI: exporte os resultados para fornecer energia ao BI. Ver [dados de importação do Azure Log Analytics no Power BI](../log-analytics-powerbi.md) para obter detalhes.
- Compartilhar um link: a consulta em si pode ser compartilhada como um link que pode ser enviado e executado por outros usuários que tenham acesso ao mesmo espaço de trabalho.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [como escrever consultas do Log Analytics](get-started-queries.md).
