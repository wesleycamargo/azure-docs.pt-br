<properties
   pageTitle="Pesquisa de dados no Insights Operacionais"
   description="Você pode usar a pesquisa de log no Insights Operacionais do Microsoft Azure para encontrar os dados que está procurando"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="07/21/2015"
   ms.author="banders" />

# Pesquisa de dados no Insights Operacionais

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

No núcleo do Insights Operacionais do Microsoft Azure está o recurso de pesquisa de log que permite combinar e correlacionar quaisquer dados de computador de várias fontes em seu ambiente. As soluções também são alimentadas pela pesquisa de log para fornecer métricas que giram em torno de uma área de problema específica.

Na página de Pesquisa, crie uma consulta e, em então, quando pesquisar, poderá filtrar os resultados por meio de controles da faceta. Você também pode criar consultas avançadas para transformar, filtrar e relatar sobre seus resultados.

As consultas de pesquisa de log comuns aparecem na maioria das páginas de solução. No console do Insights Operacionais, você pode clicar em blocos ou fazer uma busca de outros itens para exibir detalhes sobre o item usando a pesquisa de log.

Neste tutorial, examinaremos exemplos para cobrir todas as noções básicas do uso da pesquisa de log.

Vamos começar com exemplos práticos, simples e desenvolvê-los para que você possa ver casos práticos de como usar a sintaxe para extrair as ideias que desejar dos dados.

Quando você realiza pesquisas de log no Insights Operacionais, deve usar as seguintes técnicas:

- Usar filtros básicos
- Usar filtros adicionais
- Manipular resultados da pesquisa
- Usar o comando medir
- Usar as funções máx e mín com o comando de medida
- Usar a função méd com o comando de medida
- Usar o comando where

Depois que você aprende as técnicas de pesquisa, pode examinar o [Referência de sintaxe de pesquisa](#search-syntax-reference) e a [Referência de campo e de faceta](#search-field-and-facet-reference).

## Usar filtros básicos

A primeira coisa a saber é que a primeira parte de uma consulta de pesquisa, antes de um caractere de barra vertical "|", é sempre um *filtro*. Você pode pensar nela como uma cláusula WHERE em TSQL: ela determina *que* subconjunto desses dados ela deve retirar do repositório de dados do Insights Operacionais. A pesquisa de um repositório de dados é basicamente a especificação das características dos dados que você deseja extrair; portanto, é natural que uma consulta deva começar com a cláusula WHERE.

Os filtros mais básicos que você pode usar são *palavras-chave*, como 'erro', 'tempo limite' ou um nome de computador. Esses tipos de consultas simples normalmente retornam diversas formas de dados dentro do mesmo conjunto de resultados. Isso acontece porque o Insights Operacionais tem diferentes *tipos* de dados no sistema.


### Para realizar uma pesquisa simples
1. No portal de informações operacionais, clique **Pesquisar no Explorador de Dados**. ![pesquisar bloco](./media/operational-insights-search/overview-search.png)
2. No campo de consulta, digite `error` e clique em **Pesquisar**. ![pesquisar erro](./media/operational-insights-search/search-error.png) Por exemplo, a consulta de `error` na imagem a seguir retornou 100.000 registros de **Event** (coletados pelo Gerenciamento de Logs), 18 registros de **ConfigurationAlert** (gerados pela Avaliação de Configuração) e 12 registros **ConfigurationChange** (capturados pelo Controle de Alterações). ![pesquisar resultados](./media/operational-insights-search/results01.png)

Esses filtros não são realmente tipos/classes de objeto. *Type* é uma marca, uma propriedade ou uma cadeia de caracteres/nome/categoria, que é anexada a uma porção dos dados. Alguns documentos no sistema são marcados como **Type:ConfigurationAlert**, alguns são marcados como **Type:PerfHourly** ou **Type:Event** e assim por diante. Cada resultado da pesquisa, documento, registro ou entrada exibe todas as propriedades brutas e seus valores para cada porção de dados, e você pode usar esses nomes de campo para especificar o filtro quando desejar recuperar somente os registros onde o campo tiver aquele valor.

*Type* é realmente apenas um campo que existe em todos os registros; ele não é diferente de qualquer outro campo. Isso foi estabelecido com base no valor do campo Type. Esse registro terá uma forma diferente. Por acaso, **Type=PerfHourly** ou **Type=Event** também é a sintaxe que você precisa saber para consultar eventos ou agregações de dados de desempenho por hora.

Você pode usar dois pontos (:) ou um sinal de igual (=) a seguir ao nome do campo e antes do valor. **Type:Event** e **Type=Event** são equivalentes em significado; você pode escolher o estilo desejado.

Por isso, se os registros Type=PerfHourly tiverem um campo chamado 'CounterName', então você pode escrever uma consulta semelhante a `Type=PerfHourly CounterName="% Processor Time"`.

Isso lhe dará apenas os dados de desempenho onde o nome do contador de desempenho é "% de tempo do processador".

### Para procurar dados de desempenho de tempo do processador
- No campo pesquisar consulta, digite `Type=PerfHourly CounterName="% Processor Time"`

Você também pode ser mais específico e usar **InstanceName=_'Total'** na consulta, que é um contador de desempenho do Windows. Você também pode selecionar uma faceta e outro **field:value**. O filtro é adicionado automaticamente ao seu filtro na barra de consulta. Você pode ver isso na imagem a seguir. Ela mostra onde clicar para adicionar **InstanceName:’_Total’** à consulta sem digitar nada.

![pesquisar faceta](./media/operational-insights-search/search-facet.png)

A sua consulta agora se torna `Type=PerfHourly CounterName=”% Processor Time” InstanceName=”_Total”`

Nesse exemplo, você não precisa especificar **Type=PerfHourly** para obter esse resultado. Como os campos CounterName e InstanceName existem somente para registros de Type=PerfHourly, a consulta é específica o suficiente para retornar os mesmos resultados da anterior, mais longa: ```
CounterName=”% Processor Time” InstanceName=”_Total”
```

Isso ocorre porque todos os filtros na consulta são avaliados como estando em *AND* uns com os outros. Efetivamente, quanto mais campos você adicionar ao critério, mais específicos e refinados são os resultados.

Por exemplo, a consulta `Type=Event EventLog="Windows PowerShell"` é idêntica à `Type=Event AND EventLog="Windows PowerShell"`. Ela retorna todos os eventos que foram registrados no e coletados do log de eventos do Windows PowerShell. Se você adicionar um filtro várias vezes selecionando repetidamente a mesma faceta, o problema é apenas superficial: ele pode sobrecarregar a barra de pesquisa, mas ainda retorna os mesmos resultados porque o operador AND implícito sempre está lá.

Você pode facilmente reverter o operador AND implícito usando um operador NOT explicitamente. Por exemplo:

`Type:Event NOT(EventLog:"Windows PowerShell")` ou seu equivalente `Type=Event EventLog!="Windows PowerShell"` retorna todos os eventos de todos os outros logs que NÃO sejam o log do Windows PowerShell.

Você pode usar outro operador booliano, como 'OR'. A consulta a seguir retorna registros para os quais o log de eventos é um sistema OU aplicativo.

```
EventLog=Application OR EventLog=System
```

Usando a consulta anterior, você obterá as entradas para ambos os logs no mesmo conjunto de resultados.

No entanto, se você remover o OR deixando o AND implícito funcionando, a consulta a seguir não produzirá resultados porque não existe uma entrada de log de eventos que pertença a AMBOS os logs. Cada entrada de log de eventos foi escrita em apenas um dos dois logs.

```
EventLog=Application EventLog=System
```


## Usar filtros adicionais

A consulta a seguir retorna entradas de dois logs de eventos para todos os computadores que enviaram dados.

```
EventLog=Application OR EventLog=System
```

![pesquisar resultados](./media/operational-insights-search/search-results03.png)

A seleção de um dos campos ou filtros restringirá a consulta a um computador específico, excluindo todos os outros. A consulta resultante seria semelhante à seguinte:

```
EventLog=Application OR EventLog=System Computer=SERVER1.contoso.com
```

Que é equivalente à seguinte, por causa do AND implícito.

```
EventLog=Application OR EventLog=System AND Computer=SERVER1.contoso.com
```

Cada consulta é avaliada na ordem explícita abaixo. Observe o parêntese.

```
(EventLog=Application OR EventLog=System) AND Computer=SERVER1.contoso.com
```

Assim como no campo de log de eventos, você pode recuperar dados apenas para um conjunto de computadores específicos adicionando OR. Por exemplo:

```
(EventLog=Application OR EventLog=System) AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com OR Computer=SERVER3.contoso.com)
```

Da mesma forma, a consulta a seguir retorna **% CPU Time** somente para os dois computadores selecionados.

```
CounterName=”% Processor Time”  AND InstanceName=”_Total” AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com)
```


### Operadores boolianos
Com campos numéricos e de datetime, você pode procurar por valores usando *maior que*, *menor que* e *menor ou igual*. Você pode usar operadores simples como >, <, =, < =,! = na barra de pesquisa de consulta.


Você pode consultar um log de eventos específico para um período de tempo específico. Por exemplo, as últimas 24 horas são indicadas com a seguinte expressão mnemônica:

```
EventLog=System TimeGenerated>NOW-24HOURS
```


#### Para pesquisar usando um operador booliano
- No campo de consulta de pesquisa, digite `EventLog=System TimeGenerated>NOW-24HOURS"` ![pesquisar com booliano](./media/operational-insights-search/search-boolean.png)

Embora você pode controlar o intervalo de tempo graficamente, e talvez queira fazer isso na maioria das vezes, há vantagens em incluir um filtro de tempo diretamente na consulta. Por exemplo, isso funciona muito bem com painéis em você pode substituir o tempo para cada bloco, independentemente do seletor de tempo *global* na página do painel. Para saber mais, confira [Assuntos de tempo no Painel](http://cloudadministrator.wordpress.com/2014/10/19/system-center-advisor-restarted-time-matters-in-dashboard-part-6/).

Ao filtrar por tempo, tenha em mente que você obterá resultados para a *interseção* dos dois períodos de tempo: o especificado no portal do Insights Operacionais (S1) e o especificado na consulta (S2).

![interseção](./media/operational-insights-search/intersection.png)

Isso significa que, se os períodos de tempo não se cruzarem, por exemplo, no portal do Insights Operacionais onde você escolhe **Esta semana** e na consulta onde você define **última semana**, não haverá nenhuma interseção e você não receberá nenhum resultado.

Os operadores de comparação usados para o campo TimeGenerated também são úteis em outras situações. Por exemplo, com campos numéricos.

Seria o caso de, já que os alertas da análise de configuração têm os valores de severidade abaixo:

- 0 = Informações
- 1= Aviso
- 2 = Crítico

Você pode fazer a consulta de alertas de aviso e críticos e também excluir os informativos com a seguinte consulta:

```
Type=ConfigurationAlert  Severity>=1
```


Você também pode usar consultas de intervalo. Isso significa que você pode fornecer o intervalo de início e de fim dos valores em uma sequência. Por exemplo, se desejar que os eventos do log de eventos do Operations Manager em que o EventID é maior ou igual a 2100, mas abaixo de 2199, a consulta a seguir retornaria esses resultados.

```
Type=Event EventLog="Operations Manager" EventID:[2100..2199]
```


>[AZURE.NOTE]A sintaxe de intervalo que você deve usar é o separador de valor de campo de dois pontos (:) valor de dois-pontos (:) e *não* o sinal de igual (=). Coloque as extremidades inferior e superior do intervalo entre colchetes e separe-as com dois pontos finais (..).

## Manipular resultados da pesquisa

Quando você estiver procurando por dados, vai querer refinar sua consulta de pesquisa e ter um bom nível de controle sobre os resultados. Quando os resultados são recuperados, você pode aplicar comandos para transformá-los.

Os comandos em pesquisas do Insights Operacionais *devem* vir após o caractere de barra vertical (|). Um filtro deve ser sempre a primeira parte de uma cadeia de caracteres de consulta. Ele define o conjunto de dados em que você está trabalhando e "canaliza" esses resultados para um comando. Você pode usar a barra vertical para adicionar comandos adicionais. Isso é vagamente semelhante ao pipeline do Windows PowerShell.

Em geral, o idioma da busca do Insights Operacionais tenta seguir o estilo e as diretrizes do PowerShell para torná-lo semelhante aos profissionais de TI e para facilitar a curva de aprendizado.

Comandos têm nomes de verbos para que você possa perceber facilmente o que eles fazem.

### Classificar

O comando classificar permite definir a ordem de classificação por um ou vários campos. Mesmo se você não usá-lo, por padrão, a ordem imposta é decrescente por tempo. Os resultados mais recentes ficam sempre na parte superior dos resultados da pesquisa. Isso significa que quando você executa uma pesquisa com `Type=Event EventID=1234`, o que realmente é executado para você é:

```
Type=Event EventID=1234 **| Sort TimeGenerated desc**
```

Isso ocorre porque é o tipo de experiência com que você está familiarizado nos logs. Por exemplo, no Visualizador de Eventos do Windows.

Você pode usar Classificar para alterar o modo como os resultados são retornados. Os exemplos a seguir mostram como isso funciona.

```
Type=Event EventID=1234 | Sort TimeGenerated asc
```

```
Type=Event EventID=1234 | Sort Computer asc
```

```
Type=Event EventID=1234 | Sort Computer asc,TimeGenerated desc
```


Os exemplos simples acima mostram como os comandos funcionam: eles alteram a forma dos resultados que o filtro retornou.

### Limite e superior
Outro comando menos conhecido é LIMITE. O limite é um verbo do tipo do PowerShell. O Limite é funcionalmente idêntico ao comando SUPERIOR. As consultas a seguir retornam os mesmos resultados.

```
Type=Event EventID=2110 | Limit 1
```

```
Type=Event EventID=2110 | Top 1
```


#### Para pesquisar usando superior
- No campo de consulta de pesquisa, digite `Type=Event EventID=2110 | Top 1` ![pesquisar superior](./media/operational-insights-search/search-top.png)

Na imagem acima, existem 988 registros com EventID = 2110. Os campos, facetas e filtros à esquerda sempre mostram informações sobre os resultados retornados *pela parte de filtro* da consulta, que é a parte antes de qualquer caractere de barra vertical. O painel **Resultados** somente retorna 1 resultado mais recente, pois o comando de exemplo transformou os resultados.

### Selecionar

O comando SELECIONAR se comporta como Select-Object no PowerShell. Ele retorna resultados filtrados que não têm todas as suas propriedades originais. Em vez disso, ele seleciona somente as propriedades que você especifica.

#### Para executar uma pesquisa usando o comando selecionar

1. Na pesquisa, digite `Type=Event` e clique em **Pesquisar**.
2. Clique em **+ mostrar mais** em um dos resultados para exibir todas as propriedades que eles têm.
3. Selecione alguns deles explicitamente e a consulta muda para `Type=Event | Select Computer,EventID,RenderedDescription`. ![pesquisar selecionar](./media/operational-insights-search/search-select.png)

Esse comando é particularmente útil quando você deseja controlar a saída de pesquisa e escolher apenas as partes de dados que realmente importam para exploração, o que geralmente não é o registro completo. Isso também é útil quando os registros de tipos diferentes têm *algumas* propriedades em comum, mas não *todas*. Você pode gerar uma saída mais naturalmente parecida com uma tabela ou que funciona bem quando exportada para um arquivo CSV e processada no Excel.

[AZURE.INCLUDE [operational-insights-export](../../includes/operational-insights-export.md)]

## Usar o comando medir

MEDIDA é um dos comandos mais versáteis em pesquisas do Insights Operacionais. Ele permite que você aplique *funções* estatísticas aos seus dados e agregue os resultados agrupados por um determinado campo. Há várias funções estatísticas que têm suporte de Medida.

### Contagem de medida()

A primeira função estatística com a qual trabalhar e uma das mais simples de entender é a função *count()*.

Os resultados de qualquer consulta de pesquisa, como `Type=Event`, mostram filtros também chamados de facetas à esquerda dos resultados da pesquisa. Os filtros mostram uma distribuição de valores por um determinado campo para os resultados da pesquisa executada.

![pesquisar contagem de medida](./media/operational-insights-search/search-measure-count01.png)

Por exemplo, na imagem acima, você verá o campo **Computer**, e ele mostra que em quase 3 milhões de eventos nos resultados, há 20 valores exclusivos e distintos para o campo **Computer** nesses registros. O bloco mostra somente os 5 principais, que são os 5 valores mais comuns gravados nos campos **Computer**, classificados pelo número de documentos que contêm esse valor específico nesse campo. Na imagem, você pode ver que, dentre os quase três milhões de eventos, 880 mil vêm do computador DM, 602 mil vêm do computador DE e assim por diante.


E se você quiser ver todos os valores, já que o bloco mostra somente os primeiros cinco?

Isso é que o comando de medida pode fazer com a função contagem(). Essa função não usa nenhum parâmetro. Você simplesmente especifica o campo pelo qual deseja agrupar — o campo **Computer** nesse caso:

`Type=Event | Measure count() by Computer`

![pesquisar contagem de medida](./media/operational-insights-search/search-measure-count-computer.png)

No entanto, **Computer** é somente um campo usado *em* cada porção de dados — nenhum banco de dados relacional está envolvido e não há nenhum objeto **Computer** separado em lugar algum. Apenas os valores *nos* dados podem descrever qual entidade os gerou, além de várias outras características e aspectos dos dados; por isso, o termo *faceta*. No entanto, você pode também agrupar por outros campos. Como os resultados originais de quase 3 milhões de eventos que foram canalizados para o comando de medidas também têm um campo chamado **EventID**, você pode aplicar a mesma técnica para agrupar por esse campo e obter uma contagem de eventos por EventID:

```
Type=Event | Measure count() by EventID
```

Se não estiver interessado na contagem de registro real que contém um valor específico, mas apenas em uma lista dos próprios valores, você poderá adicionar um comando *Select* no final dela e selecionar somente a primeira coluna:

```
Type=Event | Measure count() by EventID | Select EventID
```

Você pode obter resultados mais complexos e classificar previamente os resultados da consulta, ou pode simplesmente clicar nas colunas na grade.

```
Type=Event | Measure count() by EventID | Select EventID | Sort EventID asc
```

#### Para pesquisar usando contagem de medida

- No campo de consulta da pesquisa, digite `Type=Event | Measure count() by EventID`
- Acrescente `| Select EventID` ao fim da consulta.
- Por fim, acrescente `| Sort EventID asc` ao fim da consulta.


Há alguns pontos importantes a observar e a enfatizar:

Primeiro, os resultados que você vê não são mais os resultados brutos originais. Em vez disso, eles são resultados agregados: essencialmente, grupos de resultados. Isso não é um problema, mas você deve entender que você está interagindo com uma forma muito diferente de dados que não é igual à forma bruta original que é criada espontaneamente como resultado da função de agregação/estatística.

Segundo, atualmente, a **Contagem de medidas** retorna somente os 100 resultados distintos principais. Esse limite não se aplica às outras funções estatísticas. Portanto, você geralmente precisará usar um filtro mais preciso primeiro para procurar itens específicos antes de aplicar contagem de medida().

## Usar as funções máx e mín com o comando de medida

Há várias situações em que **Measure Max()** e **Measure Min()** são úteis. No entanto, já que elas são opostas, vamos exemplificar Máx() e você pode testar Mín() por conta própria.

Se você consultar alertas de Avaliação de Configuração, eles terão uma propriedade **Severity** que pode ser 0, 1 ou 2, representando informações, aviso e crítico. Por exemplo:

```
Type=ConfigurationAlert
```

![pesquisar início da contagem de medida](./media/operational-insights-search/search-measure-max01.png)

Se você deseja exibir o valor mais alto para todos os alertas de um mesmo computador, o agrupar por campo, pode usar

```
Type=ConfigurationAlert | Measure Max(Severity) by Computer
```

![pesquisar medir máxima computador](./media/operational-insights-search/search-measure-max02.png)

Ela exibirá isso para os computadores que têm registros de **Alerta**; a maioria tem pelo menos um alerta crítico e o computador Bacc tem um aviso como sua pior severidade.

```
Type=ConfigurationAlert | Measure Max(Severity) by Computer
```

![pesquisar tempo máximo gerado computador](./media/operational-insights-search/search-measure-max03.png)

Essa função funciona bem com números, mas também funciona com campos de data e hora. Ela é útil para verificar o último ou mais recente carimbo de hora de qualquer porção de dados indexados para cada computador. Por exemplo, quando a alteração de configuração mais recente foi relatada pela solução de controle de alterações para cada máquina?

```
Type=ConfigurationChange | Measure Max(TimeGenerated) by Computer
```

## Usar a função méd com o comando de medida

A função estatística Méd() usada com medidas permite que você calcule o valor médio para alguns campos e agrupar os resultados por tal campo ou por outro. Isso é útil em muitos casos, como dados de desempenho.

Vamos começar com os dados de desempenho. No entanto, observe que o Insights Operacionais atualmente coleta somente determinados contadores de desempenho relacionados à malha para hosts do Virtual Machine Manager e Hyper-V como parte da solução de gerenciamento de capacidade.

Para pesquisar *todos* os dados de desempenho, a consulta mais básica é:

```
Type=PerfHourly
```

![pesquisar média início](./media/operational-insights-search/search-avg01.png)

A primeira coisa que você pode notar é que o Insights Operacionais mostra gráficos dos contadores de desempenho. Na parte inferior dos resultados, você pode ver os registros reais por trás dos gráficos.

![pesquisar média início](./media/operational-insights-search/search-avg02.png)

Na imagem acima, há dois conjuntos de campos marcados que indicam o seguinte:

- o primeiro conjunto identifica o nome de contador, o nome do objeto e o nome da instância do Windows Performance no filtro da consulta. Esses são os campos que você provavelmente usará com mais frequência como facetas/filtros
- **SampleValue** é o valor real do contador
- na consulta, **Type=PerfHourly** é uma agregação por hora
- **TimeGenerated** é 21:00, no formato de 24 horas. É a agregação para aquele período por hora das 20:00 às 21:00.
- **SampleCount** é a agregação, calculada usando 12 amostras (uma a cada 5 minutos)
- **min**, **max** e **Percentile95** para o período de hora em hora foi, nesse exemplo de memória em uma máquina virtual, 6144 (megabytes)
- **SampleValue** é uma agregação por hora e é populada com a *média* para o período de hora em hora; isso é usado para plotar os gráficos de desempenho

Depois de ler sobre a forma de registro PerfHourly e de ler sobre outras técnicas de pesquisa, você pode usar medir Méd() para agregar este tipo de dados numéricos.

Eis um exemplo simples:

```
Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" | Measure Avg(SampleValue) by Computer
```

![pesquisar média valordeexemplo](./media/operational-insights-search/search-avg03.png)

Neste exemplo, você deve selecionar o contador de desempenho de Tempo Total de CPU e média por Computador Como **SampleValue** já é uma média, você, na verdade, consulta uma média de uma média. Esse é o caso com Type=PerfHourly neste momento. Você sempre deve usar um filtro em TimeGenerated para restringir a operação a um dataset pequeno ou recente, como as últimas 4 horas, e não 7 dias.

Assim, a consulta acima se torna:

```
Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-4HOURS | Measure Avg(SampleValue) by Computer
```

### Para pesquisar usando a função méd com o comando de medida
1. Na caixa de consulta Pesquisar, digite `Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-4HOURS | Measure Avg(SampleValue) by Computer`.
2. Observe que a média recente geralmente será maior.
3. Calcule a média dos valores Máximos de hora revisando a consulta de pesquisa com `Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-4HOURS | Measure Avg(Max) by Computer`

Você pode agregar e correlacionar dados *entre* computadores. Por exemplo, imagine que você tenha um conjunto de hosts em algum tipo de farm onde os nós são uns iguais aos outros, fazem o mesmo tipo de trabalho e a carga deve ser balanceada por aproximação. Você pode fazer com que seus contadores todos sigam a consulta abaixo e obtenham as médias para todo o farm. Você pode começar escolhendo os computadores com o exemplo a seguir:

```
Type=PerfHourly AND (Computer=”SERVER1.contoso.com” OR Computer=”SERVER2.contoso.com” OR Computer=”SERVER3.contoso.com”)
```

Agora que você tem os computadores, só deve querer selecionar dois KPIs (indicadores chave de desempenho): % de uso de CPU e % de espaço livre em disco. Portanto, essa parte da consulta se torna:

```
Type=PerfHourly  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS
```

Agora você pode adicionar computadores e contadores com o exemplo abaixo:

```
Type=PerfHourly  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer=”SERVER1.contoso.com” OR Computer=”SERVER2.contoso.com” OR Computer=”SERVER3.contoso.com”)
```

Como você tem uma seleção muito específica, o comando **measure Avg()** pode retornar a média não por computador, mas pelo farm, simplesmente agrupando por CounterName. Por exemplo:

```
Type=PerfHourly  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer=”SERVER1.contoso.com” OR Computer=”SERVER2.contoso.com” OR Computer=”SERVER3.contoso.com”) | Measure Avg(SampleValue) by CounterName
```

Isso dá a você uma visualização compacta útil de alguns dos KPIs do seu ambiente.

![pesquisar média agrupamento](./media/operational-insights-search/search-avg04.png)


Você pode usar isso facilmente em um painel. Para saber mais sobre como usar os painéis, consulte [Painéis do Insights Operacionais](operational-insights-use-dashboards).

![pesquisar média painel](./media/operational-insights-search/search-avg05.png)

### Usar a função soma com o comando de medida

A função soma é semelhante a outras funções do comando de medida. Você pode ver um exemplo de como usar a função de soma em [Pesquisa de Logs IIS W3C no Insights Operacionais do Microsoft Azure](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx).

Você pode usar Máx() e Mín() com cadeias de caracteres numéricos, datetimes e texto. Com cadeias de caracteres de texto, eles são classificados em ordem alfabética e você obtém a primeira e a última.

No entanto, você não pode usar Soma() com algo diferente de campos numéricos. Isso também se aplica a Méd().

## Usar o comando where

O comando where funciona como um filtro, mas ele pode ser aplicado ao pipeline para filtrar os resultados agregados que foram produzidos pelo comando Medir, em vez de resultados brutos que são filtrados no início de uma consulta.

Por exemplo:

```
Type=PerfHourly  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(SampleValue) as AVGCPU by Computer
```

Você pode adicionar outro caractere de barra vertical "|" e o comando Where para obter somente computadores cuja média de CPU estiver acima de 80%, como no exemplo abaixo:

```
Type=PerfHourly  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(SampleValue) as AVGCPU by Computer | Where AVGCPU>80
```

Se você estiver familiarizado com o Microsoft System Center - Operations Manager, pode pensar no comando where em termos de pacote de gerenciamento de comando. Se o exemplo fosse uma regra, a primeira parte da consulta seria a fonte de dados e o comando where seria a detecção de condição.

Você pode usar a consulta como um bloco em **Meu Painel**, como um monitor de classificações, para ver quando as CPUs do computador são utilizadas em excesso. Para saber mais sobre painéis, consulte [Painéis do Insights Operacionais](operational-insights-use-dashboards). Você também pode criar e usar painéis utilizando o aplicativo móvel. Para obter mais informações, consulte [Aplicativo móvel do Insights Operacionais do Azure ](http://www.windowsphone.com/en-us/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865). Nos dois blocos inferiores da imagem a seguir, você pode ver o monitor exibindo uma lista e como um número. No fundo, convém sempre ter o número como zero e a lista em branco. Caso contrário, isso indica uma condição de alerta. Se necessário, você pode usá-lo para dar uma olhada em quais computadores estão sob pressão.

![painel móvel](./media/operational-insights-search/search-mobile.png)

## Referência de sintaxe de pesquisa

A seção de referência sobre a linguagem de pesquisa a seguir descreve as opções de sintaxe de consulta geral que você pode usar ao pesquisar expressões de dados e de filtragem para ajudar a restringir a sua pesquisa. Ele também descreve os comandos que você pode usar para atuar sobre os dados recuperados.

Você pode ler sobre os campos retornados nas pesquisas e as facetas que o ajudarão a analisar detalhadamente categorias semelhantes de dados em [Campo de pesquisa e referência de faceta](#Search-field-and-facet-reference).

### Sintaxe de consulta geral

Sintaxe:

filterExpression | command1 | command2 …

A expressão de filtro (**filterExpression**) define a condição "where" para a consulta. Os comandos se aplicam aos resultados retornados pela consulta. Vários comandos devem ser separados pelo caractere de barra vertical (|).

#### Exemplos de sintaxe geral

Exemplos:

	system

Essa consulta retorna resultados que contêm a palavra "sistema" em qualquer campo que tenha sido indexado por pesquisa de texto completo ou de termos.

>[AZURE.NOTE]Nem todos os campos são indexados dessa maneira, mas os campos de texto mais comuns (como nomes e descrições) normalmente são.

	system error

Essa consulta retorna resultados que contêm as palavras "sistema" e "erro".

	system error | sort ManagementGroupName, TimeGenerated desc | top 10

Essa consulta retorna resultados que contêm as palavras "sistema" e "erro". Em seguida, ela classifica os resultados pelo campo **ManagementGroupName** (ordem crescente) e pelo campo **TimeGenerated** (em ordem decrescente). Ela usa apenas os 10 primeiros resultados.

>[AZURE.IMPORTANT]Todos os nomes de campo e os valores para os campos de cadeia de caracteres e de texto diferenciam maiúsculas de minúsculas.

### Expressão do filtro...

As subseções a seguir explicam as expressões de filtro.

#### Literais de cadeia de caracteres

Um literal de cadeia de caracteres é qualquer cadeia de caracteres que não seja reconhecida pelo analisador como uma palavra-chave ou um tipo de dados predefinido (por exemplo, um número ou data).

Exemplos:

	These all are string literals


Essa consulta pesquisa resultados que contenham ocorrências de todas as cinco palavras. Para executar uma pesquisa de cadeia de caracteres complexa, coloque o literal da cadeia de caracteres entre aspas, por exemplo:

	" Windows Server"

Isso retorna somente os resultados com correspondências exatas para "Windows Server"

#### Números

O analisador dá suporte a inteiros decimais e à sintaxe de número de ponto flutuante para campos numéricos.

Exemplos:

	Type:PerfHourly 0.5


	HTTP 500

#### Data/hora

Cada porção dos dados do sistema tem uma propriedade **TimeGenerated** que representa a data e hora original do registro. Alguns tipos de dados podem ter mais campos de data/hora (por exemplo, **LastModified**).

O seletor Gráfico/Tempo da linha do tempo no Insights Operacionais mostra uma distribuição de resultados ao longo do tempo (de acordo com a consulta atual que está sendo executada) baseada no campo **TimeGenerated**. Os campos de data/hora têm um formato de cadeia de caracteres específico que pode ser usado em consultas para restringi-la a um determinado período. Você também pode usar a sintaxe para se referir a intervalos de tempo relativo (por exemplo, "entre 3 dias atrás e 2 horas atrás").

Sintaxe:

	yyyy-mm-ddThh:mm:ss.dddZ


	yyyy-mm-ddThh:mm:ss.ddd


	yyyy-mm-ddThh:mm:ss


	yyyy-mm-ddThh:mm:ss


	yyyy-mm-ddThh:mm


	yyyy-mm-dd



Exemplo:

	TimeGenerated:2013-10-01T12:20

O comando anterior retorna somente os registros com um valor **TimeGenerated** de exatamente 12:20 em 1º de outubro de 2013. É improvável que ele venha a fornecer qualquer resultado, mas você pegou a ideia.

O analisador também dá suporte ao valor de data/hora mnemônico AGORA.

Exemplo:


Novamente, é improvável que isso gere um resultado porque os dados não passam pelo sistema tão rápido.

Esses exemplos são blocos de construção a serem usados para datas relativas e absolutas. Nas próximas três subseções, explicaremos como usá-los em filtros mais avançados, com exemplos que usam intervalos de datas relativas.

#### Cálculos de data/hora

Use os operadores de cálculo de data/hora para deslocar ou arredondar o valor de data/hora usando cálculos simples de data/hora.

Sintaxe:

	datetime/unit


	datetime[+|-]count unit




<table border="1" cellspacing="4" cellpadding="4">
	<tr>
		<th>Operador</th>
		<th>Descrição</th>
	</tr>
	<tr>
		<td>
		<p>/</p>
		</td>
		<td>
		<p>Arredonda data/hora para a unidade especificada. </p>
		<p>Exemplo: AGORA/DIA Arredonda a data/hora atual para a meia-noite do dia atual. </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>+ ou -</p>
		</td>
		<td>
		<p>Desloca Data/Hora no número especificado de unidades</p>
		<p>Exemplos:&#160; </p>
		<ul>
			<li class="unordered">AGORA+1HORA desloca a data/hora atual uma hora à frente.<br><br></li>
			<li class="unordered">2013-10-01T12:00-10DAYS desloca o valor de data em 10 dias.</li>
		</ul>
		</td>
	</tr>
</table>





Você pode encadear os operadores de cálculo de data/hora, por exemplo:

	NOW+1HOUR-10MONTHS/MINUTE

A tabela a seguir lista as unidades com suporte de data/hora.

<table border="1" cellspacing="4" cellpadding="4"><table> <tr> <th>Unidade de data/hora </th> <th>Descrição </th> </tr> <tr> <td> <p>YEAR, YEARS</p> </td> <td> <p>Arredonda para o ano atual ou desloca pelo número especificado de anos.</p> </td> </tr> <tr> <td> <p>MONTH, MONTHS</p> </td> <td> <p>Arredonda para o mês atual ou desloca pelo número especificado de meses.</p> </td> </tr> <tr> <td> <p>DAY, DAYS, DATE</p> </td> <td> <p>Arredonda para o dia do mês atual ou desloca pelo número especificado de dias.</p> </td> </tr> <tr> <td> <p>HOUR, HOURS</p> </td> <td> <p>Arredonda para a hora atual ou desloca pelo número especificado de horas.</p> </td> </tr> <tr> <td> <p>MINUTE, MINUTES</p> </td> <td> <p>Arredonda para o minuto atual ou desloca pelo número especificado de minutos.</p> </td> </tr> <tr> <td> <p>SECOND, SECONDS</p> </td> <td> <p>Arredonda para o segundo atual ou desloca pelo número especificado de segundos.</p> </td> </tr> <tr> <td> <p>MILLISECOND, MILLISECONDS, MILLI, MILLIS</p> </td> <td> <p>Arredonda para o milissegundo atual ou desloca pelo número especificado de milissegundos.</p> </td> </tr> </table>


#### Facetas de campo

Usando as facetas de campo, você pode especificar o critério de pesquisa para campos específicos e seus valores exatos, em vez de escrever consultas com "texto livre" para vários termos em todo o índice. Já usamos essa sintaxe em vários exemplos nos parágrafos anteriores. Aqui, fornecemos exemplos mais complexos.

**Sintaxe**

*campo:valor*

*campo=valor*

**Descrição**

Pesquisa o valor específico no campo. O valor pode ser um literal de cadeia de caracteres, número ou data/hora.

Exemplo:


	TimeGenerated:NOW


	ObjectDisplayName:"server01.contoso.com"


	SampleValue:0.3

**Sintaxe**

*campo>valor*

*campo<valor*

*campo>=valor*

*campo<=valor*

*campo!=valor*

**Descrição**

Fornece comparações.

Exemplo:

	TimeGenerated>NOW+2HOURS


**Sintaxe**

*campo:[de... para]*

**Descrição**

Fornece facetamento de intervalo.

Exemplo:

	TimeGenerated:[NOW..NOW+1DAY]


	SampleValue:[0..2]
#### Operadores lógicos

As linguagens de consulta dão suporte aos operadores lógicos (AND, OR e NOT) e seus alias de operadores em C (& &, | |, e !), respectivamente. Você pode usar parênteses para agrupar esses operadores.

Exemplos:

	system OR error


	Type:Alert AND NOT(Severity:1 OR ObjectId:"8066bbc0-9ec8-ca83-1edc-6f30d4779bcb8066bbc0-9ec8-ca83-1edc-6f30d4779bcb")
Você pode omitir o operador lógico para os argumentos de filtro de nível superior. Nesse caso, o operador AND será assumido.


<table border="1" cellspacing="4" cellpadding="4"><table> <tr> <th>Expressão de filtro</th> <th>Equivalente a</th> </tr> <tr> <td> <p>erro de sistema</p> </td> <td> <p>erro AND sistema</p> </td> </tr> <tr> <td> <p>sistema "; Windows Server"; OR Severity:1</p> </td> <td> <p>sistema AND (";Windows Server"; OR Severity:1)</p> </td> </tr> </table>



### Comandos

Os comandos se aplicam aos resultados retornados pela consulta. Use o caractere de barra vertical (|) para aplicar um comando aos resultados obtidos. Vários comandos devem ser separados pelo caractere de barra vertical.

>[AZURE.NOTE]Os nomes de comando podem ser escritos em letras maiúsculas ou minúsculas, ao contrário de nomes de campos e dados.

#### Classificar

Sintaxe:

	sort field1 asc|desc, field2 asc|desc, …

Classifica os resultados por campos específicos. O prefixo crescente/decrescente é opcional. Se eles forem omitidos, presume-se a ordem de classificação “crescente”. Se uma consulta não usar o comando **Sort** explicitamente, Sort **TimeGenerated** desc será o comportamento padrão e sempre retornará os resultados mais recentes primeiro.

#### Superior/limite

Sintaxe:

	top number


	limit number
Limita a resposta aos N principais resultados.

Exemplo:

	Type:Alert errors detected | top 10

Retorna os 10 primeiros resultados de correspondência.

#### Skip

Sintaxe:

	skip number

Ignora o número de resultados listados.

Exemplo:

	Type:Alert errors detected | top 10 | skip 200

Retorna os 10 primeiros resultados de correspondência, começando no resultado 200.

#### Selecionar

Sintaxe:

	select field1, field2, ...

Limita os resultados aos campos escolhidos.

Exemplo:

	Type:Alert errors detected | select Name, Severity

Limita os campos de resultados retornados a **Name** e **Severity**.

#### Medida

O comando **measure** é usado para aplicar funções estatísticas aos resultados brutos da pesquisa. Isso é muito útil para obter as exibições *agrupar por* dos dados. Quando você usa o comando **measure**, o Insights Operacionais exibe uma tabela com resultados agregados.

Sintaxe:

	measure aggregateFunction([aggregatedField]) [as fieldAlias] by groupField [interval interval]


	measure aggregateFunction([aggregatedField])  interval interval

Agrega os resultados por **groupField** e calcula os valores agregados de medida usando **aggregatedField**.


<table border="1" cellspacing="4" cellpadding="4"><table> <tr> <th>Função estatística de measure</th> <th>Descrição </th> </tr> <tr> <td> <p><em>aggregateFunction</em> </p> <p></p> </td> <td> <p>O nome da função de agregação (não diferencia maiúsculas de minúsculas). Há suporte para as seguintes funções de agregação:</p> <ul> <li class="unordered">COUNT<br><br></li> <li class="unordered">MAX<br><br></li> <li class="unordered">MIN<br><br></li> <li class="unordered">SUM<br><br></li> <li class="unordered">AVG<br><br></li> <li class="unordered">STDDEV<br><br></li> </ul> </td> </tr> <tr> <td> <p><em>aggregatedField</em> </p> </td> <td> <p>O campo que está sendo agregado. Esse campo é opcional para a função de agregação COUNT, mas deve ser um campo numérico existente para SUM, MAX, MIN, AVG ou STDDEV.</p> </td> </tr> <tr> <td> <p><em>fieldAlias</em> </p> </td> <td> <p>O alias (opcional) para o valor agregado calculado. Se não for especificado, o nome do campo será <em>AggregatedValue.</em></p> </td> </tr> <tr> <td> <p><em>groupField</em> </p> </td> <td> <p>O nome do campo pelo qual o conjunto de resultados é agrupado. </p> </td> </tr> <tr> <td> <p><em>Interval</em> </p> </td> <td> <p>O intervalo de tempo no formato: </p> <p><em>nnnNAME</em> </p> <p></p> <p>Em que: </p> <p>nnn é o número inteiro positivo</p> <p><em>NAME</em> é o nome do intervalo</p> <p>Os nomes de intervalo com suporte incluem (com distinção entre maiúsculas e minúsculas): </p> <ul> <li class="unordered">MILLISECOND[S]<br><br></li> <li class="unordered">SECOND[S]<br><br></li> <li class="unordered">MINUTE[S]<br><br></li> <li class="unordered">HOUR[S]<br><br></li> <li class="unordered">DAY[S]<br><br></li> <li class="unordered">MONTH[S]<br><br></li> <li class="unordered">YEAR[S]<br></li> </ul> </td> </tr> </table>



A opção de intervalo só pode ser usada em campos de grupo de data/hora (como **TimeGenerated** e **TimeCreated**). Atualmente, isso não é imposto pelo serviço, mas um campo sem data/hora que é passado para o back-end causará um erro de tempo de execução. Quando a validação do esquema é implementada, a API do serviço rejeita as consultas que usam campos sem data/hora para agregação de intervalo. A implementação atual de **Measure** oferece suporte somente ao agrupamento de intervalo para a função de agregação **Count**.

Se a cláusula BY for omitida, mas um intervalo for especificado (como uma segunda sintaxe), o campo **TimeGenerated** será considerado por padrão.

Exemplos:

**Exemplo 1**

	Type:Alert | measure count() as Count by ObjectId

*Explicação*

Agrupa os alertas por **ObjectID** e calcula o número de alertas para cada grupo. O valor agregado é retornado como o campo **Count** (alias).

**Exemplo 2**

	Type:Alert | measure count() interval 1HOUR

*Explicação*

Agrupa os alertas por intervalos de 1 hora usando o campo **TimeGenerated** e retorna o número de alertas em cada intervalo.

**Exemplo 3**

	Type:Alert | measure count() as AlertsPerHour interval 1HOUR

*Explicação*

Mesma coisa que o exemplo anterior, mas com um alias de campo agregado (**AlertsPerHour**).

**Exemplo 4**

	* | measure count() by TimeCreated interval 5DAYS

*Explicação*

Agrupa os resultados por intervalos de 5 dias usando o campo **TimeCreated** e retorna o número de resultados em cada intervalo.

**Exemplo 5**

	Type:Alert | measure max(Severity) by WorkflowName

*Explicação*

Agrupa os alertas por cargas de trabalho e retorna o valor máximo de severidade de alerta para cada fluxo de trabalho.

**Exemplo 6**

	Type:Alert | measure min(Severity) by WorkflowName

*Explicação*

Igual ao exemplo anterior, mas com a função de agregação **Min**.

**Exemplo 7**

	Type:PerfHourly | measure avg(SampleValue) by ObjectId

*Explicação*

Agrupa PerfHourly por ObjectId e calcula a média (méd).

**Exemplo 8**

	Type:PerfHourly | measure sum(SampleValue) by ObjectId

*Explicação*

Igual ao exemplo anterior, mas usa **Sum**.

**Exemplo 9**

	Type:PerfHourly | measure stddev(SampleValue) by ObjectId

*Explicação*

Igual ao exemplo anterior, mas usa **STDDEV**.

**Exemplo 10**

	Type:Alert | measure count() as Count by WorkflowName | sort Count desc | top 5

*Explicação*

Obtém os cinco principais fluxos de trabalho com o número máximo de alertas.

#### Where

Sintaxe:

**where** AggregatedValue>20

Pode ser usado somente depois de um comando **Measure** para filtrar ainda mais os resultados agregados que a função de agregação **Measure** produziu.

Exemplos:

	Type:PerfHourly CounterName:"% Total Run Time" | Measure max(Max) as MAXCPU by

	Type:PerfHourly CounterName:"% Total Run Time" | Measure max(Max) by RootObjectName | where (AggregatedValue>50 and AggregatedValue<90)




## Campo de pesquisa e referência da faceta

Quando você usar Pesquisar para localizar dados, os resultados exibem vários campos e facetas. No entanto, algumas informações que você vê talvez não pareçam muito descritivas. Você pode usar as informações a seguir para ajudá-lo a entender os resultados.

<table border="1" cellspacing="4" cellpadding="4">
	<tr>
		<th><b>Campo</b></th>
		<th><b>Tipo de pesquisa<b></th>
		<th><b>Descrição</b></th>
	</tr>
	<tr>
		<td>
		<p>TenantId</p>
		</td>
		<td>
		<p>Todos</p>
		</td>
		<td>
		<p>Usado para particionar dados</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>TimeGenerated</p>
		</td>
		<td>
		<p>Todos</p>
		</td>
		<td>
		<p>Usado para orientar a linha do tempo, timeselectors (na pesquisa e em outras telas). Representa quando a porção de dados foi gerada (normalmente no agente). O tempo é expresso no formato ISO e é sempre UTC. No caso de ‘tipos’ baseados em instrumentação existente (ou seja, eventos em um log), isso é normalmente o tempo real em que a entrada de log/linha/registro foi registrada em log; para alguns dos outros tipos que são produzidos através de pacotes de gerenciamento ou na nuvem, ou seja, recomendações/alertas/updateagent/etc., isso é o tempo em que essa nova porção dos dados com um instantâneo de alguma configuração foi coletada ou uma recomendação/alerta foi gerada com base nela</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventID</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>EventID no log de eventos do Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventLog</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>Log de eventos onde o evento foi registrado pelo Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventLevelName</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>Crítico/aviso/informação/êxito</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventLevel</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>Valor numérico de crítico/aviso/informação/êxito (use EventLevelName em vez disso para consultas mais legíveis/mais fáceis)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SourceSystem</p>
		</td>
		<td>
		<p>Todos</p>
		</td>
		<td>
		<p>De onde os dados vêm (no sentido de modo 'anexar' para o serviço, ou seja, o Operations Manager, o Insights Operacionais (= os dados são gerados na nuvem), o Armazenamento do Azure (dados provenientes do WAD) e assim por diante</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ObjectName</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Nome de objeto de desempenho do Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>InstanceName</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Nomes da instância do contador de desempenho do Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>CounteName</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Nome do contador de desempenho do Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ObjectDisplayName</p>
		</td>
		<td>
		<p>PerfHourly, ConfigurationAlert, ConfigurationObject, ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>Nome de exibição do objeto alvo de uma regra de coleta de desempenho no Operations Manager ou do objeto descoberto pelo Insights Operacionais, ou pela qual o alerta foi gerado</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RootObjectName</p>
		</td>
		<td>
		<p>PerfHourly, ConfigurationAlert, ConfigurationObject, ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>Nome de exibição do pai do pai (em uma relação de hospedagem dupla: por exemplo, SqlDatabase hospedado pelo SqlInstance hospedado pelo computador do Windows) do objeto alvo de uma regra de coleta de desempenho no Operations Manager ou do objeto descoberto pelo Insights Operacionais, ou pela qual o alerta foi gerado</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Computador</p>
		</td>
		<td>
		<p>Maioria dos tipos </p>
		</td>
		<td>
		<p>Nome do computador a que pertencem os dados</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DeviceName</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>O nome do computador a que os dados pertencem (mesmo que 'Computador')</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DetectionId</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ThreatStatusRank</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>A classificação de status de ameaça é uma representação numérica do status de ameaça e, de forma semelhante a códigos de resposta HTTP, deixamos lacunas entre os números (motivo pelo qual nenhuma ameaça é 150, e não 100 ou 0) para que tenhamos um espaço para adicionar novos estados. Quando fazemos um acúmulo de estados de ameaça e status de proteção, queremos mostrar o pior estado em que o computador esteve durante o período de tempo selecionado. Usamos os números para classificar os diferentes estados e nos permitir procurar o registro com o número mais alto.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ThreatStatus</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>Descrição do ThreatStatus, mapeia 1:1 com ThreatStatusRank</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>TypeofProtection</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>Produto antimalware detectado no computador: nenhum, ferramenta de remoção de Malware da Microsoft, Forefront, etc.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ScanDate</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SourceHealthServiceId</p>
		</td>
		<td>
		<p>ProtectionStatus, RequiredUpdate</p>
		</td>
		<td>
		<p>ID de serviço de integridade para o agente desse computador</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>HealthServiceId</p>
		</td>
		<td>
		<p>Maioria dos tipos </p>
		</td>
		<td>
		<p>ID de serviço de integridade para o agente desse computador</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ManagementGroupName</p>
		</td>
		<td>
		<p>Maioria dos tipos </p>
		</td>
		<td>
		<p>Nome do grupo de gerenciamento para agentes anexados ao Operations Manager; caso contrário, será nulo/espaço em branco</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ObjectType</p>
		</td>
		<td>
		<p>ConfigurationObject</p>
		</td>
		<td>
		<p>Type (como em 'type'/class do pacote de gerenciamento do Operations Manager) para esse objeto descoberto pela avaliação de configuração do Insights Operacionais</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>UpdateTitle</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>Nome da atualização que foi encontrada não instalada</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>PublishDate</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>Quando a atualização foi publicada no Microsoft Update?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Servidor</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>O nome do computador a que os dados pertencem (mesmo que 'Computador')</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Produto</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>Produto ao qual a atualização se aplica</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>UpdateClassification</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>Tipo de atualização (pacote cumulativo de atualizações, service pack, etc.)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>KBID</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>ID do artigo KB que melhor descreve essa prática recomendada ou atualização</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>WorkflowName</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Nome da regra ou monitor que produziu o alerta</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Severidade</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Severidade do alerta</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Prioridade</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Prioridade do alerta</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>IsMonitorAlert</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Esse alerta foi gerado por um monitor (true) ou por uma regra (false)?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AlertParameters</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>XML com os parâmetros do alerta do Insights Operacionais</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Contexto</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>XML com 'contexto' do alerta do Insights Operacionais</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Carga de trabalho</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Tecnologia ou 'carga de trabalho' a que o alerta se refere </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AdvisorWorkload</p>
		</td>
		<td>
		<p>Recomendações</p>
		</td>
		<td>
		<p>Tecnologia ou 'carga de trabalho' a que se refere a recomendação</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Descrição</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Descrição do alerta (curta)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DaysSinceLastUpdate</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>Quantos dias atrás (relativo ao 'TimeGenerated' desse registro) esse agente instalou qualquer atualização do WSUS/Microsoft Update?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DaysSinceLastUpdateBucket</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>Com base em DaysSinceLastUpdate, uma categorização em 'buckets de tempo' de quanto tempo atrás um computador teve instalada pela última vez qualquer atualização do WSUS/Microsoft Update</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AutomaticUpdateEnabled</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>A verificação de atualização automática está habilitada ou desabilitada nesse agente?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AutomaticUpdateValue</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>A verificação de atualização automática está definida para baixar automaticamente e instalar, somente baixar ou somente instalar?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>WindowsUpdateAgentVersion</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>Número da versão do agente do Microsoft Update</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>WSUSServer</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>A qual servidor WSUS se destina esse agente de atualização?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>OSVersion</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>Versão do sistema operacional sendo executada nesse agente de atualização</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Nome</p>
		</td>
		<td>
		<p>Recomendação, ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>Nome/título da recomendação ou nome da propriedade da avaliação de configuração do Insights Operacionais</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Valor</p>
		</td>
		<td>
		<p>ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>Valor de uma propriedade da avaliação de configuração do Insights Operacionais</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>KBLink</p>
		</td>
		<td>
		<p>Recomendações</p>
		</td>
		<td>
		<p>URL para o artigo KB que melhor descreve essa prática recomendada ou atualização</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RecommendationStatus</p>
		</td>
		<td>
		<p>Recomendações</p>
		</td>
		<td>
		<p>As recomendações estão entre os poucos tipos cujos registros são 'atualizados', não apenas adicionados ao índice de pesquisa. Esse status se altera quando a recomendação está ativa/aberta ou quando o Insights Operacionais detecta que foi resolvido</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RenderedDescription</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>Descrição renderizada (texto reutilizado com parâmetros populados) de um evento do Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ParameterXml</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>XML com os parâmetros na seção 'dados' de um evento do Windows (como visto no visualizador de eventos)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventData</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>XML com a seção inteira de 'dados' de um evento do Windows (como visto no visualizador de eventos)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Fonte</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>Origem do log de eventos que gerou o evento</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventCategory</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>Categoria do evento, diretamente do log de eventos do Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>UserName</p>
		</td>
		<td>
		<p>Evento</p>
		</td>
		<td>
		<p>Nome de usuário de evento do Windows (normalmente, NT AUTHORITY\LOCALSYSTEM)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SampleValue</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Valor médio para a agregação por hora de um contador de desempenho </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Mín</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Valor mínimo no intervalo por hora de uma agregação por hora do contador de desempenho</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Máx</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Valor máximo no intervalo por hora de uma agregação por hora do contador de desempenho</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Percentile95</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>O 95º valor de percentil para o intervalo de hora de uma agregação por hora do contador de desempenho</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SampleCount</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Quantas amostras de contador de desempenho 'brutas' foram usadas para produzir esse registro de agregação por hora</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Ameaça</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>Nome de malware encontrado</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>StorageAccount</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>A conta de armazenamento do Azure de onde o log foi lido</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AzureDeploymentID</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>ID de implantação do Azure do serviço de nuvem a que o log pertence</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Função</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>A função do serviço de nuvem do Azure a que o log pertence</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RoleInstance</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>RoleInstance da função do Azure a que pertence o log</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sSiteName</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Site do IIS a que pertence o log (notação de metabase); o campo s-sitename no log original</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sComputerName</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>O campo s-computername no log original</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sIP</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Endereço IP do servidor ao qual a solicitação HTTP foi endereçada. O campo s-ip no log original</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csMethod</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Método HTTP (GET/POST/etc.) usado pelo cliente na solicitação HTTP. O cs-method no log original</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>cIP</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Endereço IP do cliente de onde veio a solicitação HTTP. O campo c-ip no log original</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUserAgent</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Agente-usuário HTTP declarado pelo cliente (navegador ou outros). O cs-user-agent no log original</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scStatus</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Código de status HTTP (200/403/500/etc.) retornado pelo servidor para o cliente. O cs-status no log original</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>TimeTaken</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Quanto tempo (em milissegundos) a solicitação levou para ser concluída. O campo timetaken no log original</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUriStem</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>O Uri relativo (sem hospedar o endereço, ou seja, '/search ') que foi solicitado. O campo cs-uristem no log original</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUriQuery</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Consulta URI. As consultas URI somente são necessárias para páginas dinâmicas, como as páginas ASP; portanto, esse campo geralmente contém um hífen para páginas estáticas.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sPort</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Porta do servidor que recebeu a solicitação HTTP (e é ouvida pelo IIS, já que ele a percebeu)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUserName</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>nome de usuário autenticado, se a solicitação for autenticada e não anônima</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csVersion</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Versão do protocolo HTTP usada na solicitação (ou seja, HTTP/1.1 OK</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csCookie</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Informações de cookie</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csReferer</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Site que o usuário visitou por último. Esse site fornece um link para o site atual. </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csHost</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Cabeçalho de host (por exemplo, 'www.mysite.com') que foi solicitado</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scSubStatus</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Código de erro de substatus</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scWin32Status</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Código de Status do Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csBytes</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Bytes enviados na solicitação do cliente para o servidor</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scBytes</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Bytes retornados em resposta do servidor para o cliente</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ConfigChangeType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Tipo de alteração (WindowsServices/Software /etc.)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ChangeCategory </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Categoria da alteração (modificado/adicionado/removido)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SoftwareType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Tipo de software (atualização/aplicativo)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SoftwareName </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Nome do software (aplicável somente às alterações de software)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Editor </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Fornecedor que publica o software (aplicável somente às alterações de software)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcChangeType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Tipo de alteração que foi aplicada a um serviço do Windows (State / StartupType / Path / ServiceAccount): aplicável somente às alterações de serviço Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcDisplayName </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Nome de exibição do serviço que foi alterado</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcName </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Nome do serviço que foi alterado</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcState </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Novo (atual) estado do serviço</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousState</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Estado anterior conhecido do serviço (aplicável somente se o estado do serviço for alterado)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcStartupType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Tipo de inicialização do serviço</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousStartupType</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Tipo anterior de inicialização do serviço (aplicável somente se o tipo de inicialização de serviço for alterado)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcAccount </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Conta de serviço</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousAccount</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Conta de serviço anterior (aplicável somente se a conta de serviço for alterada)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPath</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Caminho do executável do serviço Windows</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousPath</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Caminho anterior do executável para o serviço do Windows (aplicável somente se ele for alterado)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcDescription</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Descrição do serviço</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Voltar </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Estado anterior do software (versão instalada/não instalada/atual)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Atual</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Estado mais recente do software (versão instalada/não instalada/atual)</p>
		</td>
	</tr>
</table>

## Postagens no blog - Casos de uso de pesquisa
- [Pesquisa de Logs IIS W3C no Insights Operacionais do Microsoft Azure](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx)
- [Monitoramento de falhas de backup do SQL com Pesquisa e Painéis do Insights Operacionais do Azure](http://blogs.msdn.com/b/dmuscett/archive/2015/02/21/monitoring-sql-backup-failures-with-azure-operational-insights-search-and-dashboards.aspx)
- [Equivalentes de pesquisas do OpInsights das regras de alerta de evento do pacote de gerenciamento do IIS](http://blogs.msdn.com/b/dmuscett/archive/2014/11/05/iis-mp-event-alerting-rules-s-opinsights-searches-equivalents.aspx)
- [Coleção de consultas de pesquisa úteis do Insights Operacionais](http://blogs.msdn.com/b/dmuscett/archive/2014/10/19/advisor-searches-collection.aspx)

## Outros recursos
Stefan Roth criou uma cola muito prática. Configura seu [blog](http://stefanroth.net/2014/11/05/microsoft-azure-operational-insights-search-data-explorer-cheat-sheet/) para saber mais e baixar seu roteiro.

<!---HONumber=July15_HO4-->