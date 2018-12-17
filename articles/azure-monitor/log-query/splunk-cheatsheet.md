---
title: Splunk para Azure Log Analytics | Microsoft Docs
description: Auxílio para usuários que estão familiarizados com o Splunk no aprendizado da linguagem de consulta do Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: bwren
ms.openlocfilehash: 61f0cff661c79f994a5b3c20646996f617a31b7e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52884420"
---
# <a name="splunk-to-log-analytics"></a>Splunk para Log Analytics

Este artigo se destina a auxiliar os usuários que estão familiarizados com o Splunk no aprendizado da linguagem de consulta do Log Analytics. Comparações diretas são feitas entre os dois para compreender as principais diferenças e também as semelhanças em que você pode aproveitar seu conhecimento existente.

## <a name="structure-and-concepts"></a>Estrutura e conceitos

A tabela a seguir compara os conceitos e estruturas de dados entre o Splunk e o Log Analytics.

 | Conceito  | Splunk | Log Analytics |  Comentário
 | --- | --- | --- | ---
 | Unidade de implantação  | cluster |  cluster |  O Log Analytics permite consultas arbitrárias entre clusters. O Splunk, não. |
 | Caches de dados |  buckets  |  Políticas de retenção e armazenamento em cache |  Controla o período e o nível de armazenamento em cache dos dados. Essa configuração afeta diretamente o desempenho das consultas e o custo da implantação. |
 | Partição lógica dos dados  |  índice  |  Banco de Dados  |  Permite a separação lógica dos dados. Ambas as implementações permitem uniões e junções entre essas partições. |
 | Metadados de eventos estruturados | N/D | tabela |  O Splunk não tem o conceito exposto à linguagem de pesquisa de metadados de evento. O Log Analytics tem o conceito de uma tabela, que tem colunas. Cada instância de evento é mapeada para uma linha. |
 | Registro de dados | evento | linha |  Mudança de terminologia apenas. |
 | Atributo de registro de dados | field |  coluna |  No Log Analytics, isso é predefinido como parte da estrutura de tabela. No Splunk, cada evento tem seu próprio conjunto de campos. |
 | Tipos | tipo de dados |  tipo de dados |  Os tipos de dados do Log Analytics são mais explícitos, visto que são definidos nas colunas. Ambos têm a capacidade de trabalhar dinamicamente com os tipos de dados e o conjunto praticamente equivalente de tipos de dados, incluindo suporte a JSON. |
 | Consulta e pesquisa  | pequisa | query |  Os conceitos são essencialmente os mesmos entre o Log Analytics e o Splunk. |
 | Hora da ingestão de evento | Hora do sistema | ingestion_time() |  No Splunk, cada evento obtém um carimbo de data/hora do sistema do momento em que o evento foi indexado. No Log Analytics, você pode definir uma política chamada ingestion_time que expõe uma coluna do sistema que pode ser referenciada por meio da função ingestion_time(). |

## <a name="functions"></a>Funções

A tabela a seguir especifica as funções no Log Analytics que são equivalentes às funções do Splunk.

|Splunk | Log Analytics |Comentário
|---|---|---
|strcat | strcat()| (1) |
|split  | split() | (1) |
|if     | iff()   | (1) |
|tonumber | todouble()<br>tolong()<br>toint() | (1) |
|upper<br>lower |toupper()<br>tolower()|(1) |
| substituir | replace() | (1)<br> Observe também que, embora `replace()` use três parâmetros em ambos os produtos, os parâmetros são diferentes. |
| substr | substring() | (1)<br>Observe também que Splunk usa índices com base em um. O Log Analytics usa índices com base em zero. |
| tolower |  tolower() | (1) |
| toupper | toupper() | (1) |
| match | matches regex |   (2)  |
| regex | matches regex | No Splunk, `regex` é um operador. No Log Analytics, é um operador relacional. |
| searchmatch | == | No Splunk, `searchmatch` permite pesquisar a cadeia de caracteres exata.
| random | rand()<br>rand(n) | A função do Splunk retorna um número de zero a 2<sup>31</sup>-1. O Log Analytics retorna um número entre 0,0 e 1,0 ou, se um parâmetro for fornecido, entre 0 e n-1.
| now | now() | (1)
| relative_time | totimespan() | (1)<br>No Log Analytics, o equivalente do Splunk de relative_time(datetimeVal, offsetVal) é datetimeVal + totimespan(offsetVal).<br>Por exemplo, <code>search &#124; eval n=relative_time(now(), "-1d@d")</code> torna-se <code>...  &#124; extend myTime = now() - totimespan("1d")</code>.

(1) no Splunk, a função é invocada com o operador `eval`. No Log Analytics, ela é usada como parte de `extend` ou `project`.<br>(2) no Splunk, a função é invocada com o operador `eval`. No Log Analytics, ela pode ser usada com o operador `where`.


## <a name="operators"></a>Operadores

As seções a seguir fornecem exemplos do uso de operadores diferentes entre o Log Analytics e o Splunk.

> [!NOTE]
> Para fins do exemplo a seguir, o campo do Splunk _rule_ mapeia para uma tabela no Azure Log Analytics, e o carimbo de data/hora padrão do Splunk mapeia para a coluna _ingestion_time()_ do Log Analytics.

### <a name="search"></a>Search
No Splunk, você pode omitir a palavra-chave `search` e especificar uma cadeia de caracteres sem aspas. No Azure Log Analytics, você deve iniciar cada pesquisa com `find`, uma cadeia de caracteres sem aspas é um nome de coluna, e o valor de pesquisa deve ser uma cadeia de caracteres entre aspas. 

| |  | |
|:---|:---|:---|
| Splunk | **search** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Log Analytics | **find** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |
| | |

### <a name="filter"></a>Filter
As consultas do Azure Log Analytics iniciam em um conjunto de resultados tabulares de onde filtrar. No Splunk, a filtragem é a operação padrão no índice atual. Você também pode usar o operador `where` no Splunk, mas isso não é recomendado.

| |  | |
|:---|:---|:---|
| Splunk | **search** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Log Analytics | **where** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |
| | |


### <a name="getting-n-eventsrows-for-inspection"></a>Obtendo n eventos/linhas para inspeção 
O Azure Log Analytics também dá suporte a `take` como um alias para `limit`. No Splunk, se os resultados forem ordenados, `head` retornará os primeiros n resultados. No Azure Log Analytics, o limite não é ordenado, mas retorna as primeiras n linhas encontradas.

| |  | |
|:---|:---|:---|
| Splunk | **head** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Log Analytics | **limit** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |
| | |



### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Obtendo os primeiros n eventos/linhas ordenados por um campo/coluna
Para obter os resultados finais, use `tail` no Splunk. No Azure Log Analytics, você pode especificar a direção de classificação com `asc`.

| |  | |
|:---|:---|:---|
| Splunk | **head** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Log Analytics | **início** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |
| | |




### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Estendendo o conjunto de resultados com novos campos/colunas
O Splunk também tem uma função `eval`, que não é comparável ao operador `eval`. O operador `eval` no Splunk e o operador `extend` no Azure Log Analytics dá suporte apenas a funções de valor escalar e a operadores aritméticos.

| |  | |
|:---|:---|:---|
| Splunk | **eval** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Log Analytics | **extend** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |
| | |


### <a name="rename"></a>Renomear 
O Azure Log Analytics usa o mesmo operador para renomear e criar um novo campo. O Splunk tem dois operadores separados, `eval` e `rename`.

| |  | |
|:---|:---|:---|
| Splunk | **rename** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Log Analytics | **extend** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend exception = Date_Exception</code> |
| | |




### <a name="format-resultsprojection"></a>Formatar os resultados/projeção
O Splunk não parece ter um operador semelhante a `project-away`. Você pode usar a interface do usuário para filtrar os campos ausentes.

| |  | |
|:---|:---|:---|
| Splunk | **tabela** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Log Analytics | **project**<br>**project-away** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |
| | |



### <a name="aggregation"></a>Agregação
Consulte as [Agregações em consultas do Log Analytics](aggregations.md) para as diferentes funções de agregação.

| |  | |
|:---|:---|:---|
| Splunk | **stats** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Log Analytics | **summarize** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |
| | |



### <a name="join"></a>Ingressar
Unir-se ao Splunk tem limitações significativas. A subconsulta tem um limite de 10000 resultados (definido no arquivo de configuração de implantação), e há um número limitado de tipos de junção.

| |  | |
|:---|:---|:---|
| Splunk | **join** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias | join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| Log Analytics | **join** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |
| | |



### <a name="sort"></a>Classificar
No Splunk, para classificar em ordem crescente, você deve usar o operador `reverse`. O Azure Log Analytics também dá suporte à definição de onde colocar os nulos, no início ou no final.

| |  | |
|:---|:---|:---|
| Splunk | **sort** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Log Analytics | **order by** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |
| | |



### <a name="multivalue-expand"></a>Expansão de múltiplos valores
Este é um operador semelhante no Splunk e no Log Analytics.

| |  | |
|:---|:---|:---|
| Splunk | **mvexpand** |  `mvexpand foo` |
| Log Analytics | **mvexpand** | `mvexpand foo` |
| | |




### <a name="results-facets-interesting-fields"></a>Facetas de resultados, campos interessantes
No portal do Log Analytics, apenas a primeira coluna é exposta. Todas as colunas estão disponíveis por meio da API.

| |  | |
|:---|:---|:---|
| Splunk | **fields** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Log Analytics | **facets** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |
| | |




### <a name="de-duplicate"></a>Eliminar duplicação
Você pode usar `summarize arg_min()` para inverter a ordem da qual o registro é escolhido.

| |  | |
|:---|:---|:---|
| Splunk | **dedup** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Log Analytics | **summarize arg_max()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |
| | |




## <a name="next-steps"></a>Próximas etapas

- Percorra uma lição em [escrevendo consultas no Log Analytics](get-started-queries.md).