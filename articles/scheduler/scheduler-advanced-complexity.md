<properties 
 pageTitle="Como criar agendamentos complexos e recorrência avançada com o Agendador do Azure" 
 description="" 
 services="scheduler" 
 documentationCenter=".NET" 
 authors="krisragh" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="scheduler" 
 ms.workload="infrastructure-services" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="08/04/2015" 
 ms.author="krisragh"/>

# Como criar agendamentos complexos e recorrência avançada com o Agendador do Azure  

## Visão geral

O centro de um trabalho do Agendador do Azure é o *agendamento*. O agendamento determina quando e como o Agendador executa o trabalho.

O Agendador do Azure permite que você especifique diferentes agendamentos únicos e recorrentes para um trabalho. Agendamentos *Únicos* são acionados uma vez em um determinado momento: na verdade, eles são agendamentos *recorrentes* que são executados somente uma vez. Os agendamentos recorrentes são acionados com uma frequência predeterminada.

Com essa flexibilidade, o Agendador do Azure permite que você dê suporte a uma ampla variedade de cenários de negócios:

-	Limpeza periódica de dados: por exemplo, todos os dias, excluir todos os tweets com mais de 3 meses
-	Arquivamento: por exemplo, a cada mês, enviar o histórico da fatura para o serviço de backup
-	Solicitações de dados externos: por exemplo, a cada 15 minutos, receber um novo relatório de previsão do tempo de Esqui da NOAA
-	Processamento: por exemplo, a cada dia da semana, fora do horário de pico, usar computação em nuvem para compactar as imagens carregadas no dia


Neste artigo, percorreremos trabalhos de exemplo que você pode criar com o Agendador do Azure. Nós fornecemos os dados JSON que descrevem cada agendamento. Se você usar a [API REST do Agendador](https://msdn.microsoft.com/library/azure/dn528946.aspx), pode usar esse mesmo JSON para [Criar um trabalho do Agendador do Azure](https://msdn.microsoft.com/library/azure/dn528937.aspx).

## Cenários com suporte

Muitos exemplos neste tópico ilustram a variedade de cenários com suporte do Agendador do Azure. No geral, esses exemplos ilustram como criar agendamentos para vários padrões de comportamento, incluindo estes abaixo:

-	Executar uma vez em uma determinada data e hora
-	Executar e repetir um número de vezes específico
-	Executar imediatamente e repetir 
-	Executar e repetir a cada *n* minutos, horas, dias, semanas ou meses, começando em um momento específico
-	Executar e repetir a frequência semanal ou mensal, mas somente em determinados dias, dias específicos da semana ou dias específicos do mês
-	Executar e repetir várias vezes em um período: por exemplo, na última sexta-feira e segunda-feira de cada mês ou às 5h15 e 17h15 todos os dias

## Datas e DateTimes

As datas em trabalhos do Agendador do Azure seguem a [especificação ISO 8601](http://en.wikipedia.org/wiki/ISO_8601) e incluem apenas a data.

As referências de data e hora em trabalhos do Agendador do Azure seguem a [especificação ISO 8601](http://en.wikipedia.org/wiki/ISO_8601) e incluem partes de data e hora. Uma data e hora que não especifique um deslocamento UTC é considerada como UTC.

## Como usar JSON e API REST para criar agendamentos

Para criar um agendamento simples usando os exemplos JSON neste artigo e a API REST do Agendador do Azure, [primeiro crie um serviço de nuvem](https://msdn.microsoft.com/library/azure/dn528943.aspx), [crie uma coleção de trabalhos](https://msdn.microsoft.com/library/azure/dn528940.aspx) e, [finalmente, crie um trabalho](https://msdn.microsoft.com/library/azure/dn528937.aspx). Quando você cria um trabalho, pode especificar o agendamento e recorrência usando JSON, como representado abaixo:

	{
	    "startTime": "2012-08-04T00:00Z", // optional
	     …
	    "recurrence":                     // optional
	    {
	        "frequency": "week",     // can be "year" "month" "day" "week" "hour" "minute"
	        "interval": 1,                // optional, how often to fire (default to 1)
	        "schedule":                   // optional (advanced scheduling specifics)
	        {
	            "weekDays": ["monday", "wednesday", "friday"],
	            "hours": [10, 22]                      
	        },
	        "count": 10,                  // optional (default to recur infinitely)
	        "endTime": "2012-11-04",      // optional (default to recur infinitely)
	    },
	    …
	}
	
## Visão geral: Noções básicas sobre esquemas de trabalho

A tabela a seguir fornece uma visão geral de alto nível dos principais elementos relacionados a recorrência e planejamento em um trabalho:

|**Nome JSON**|**Descrição**|
|:--|:--|
|**_startTime_**|_startTime_ é uma data e hora. Para agendamentos simples, _startTime_ é a primeira ocorrência e, para agendamentos complexos, o trabalho será iniciado somente na _startTime_.|
|**_recorrência_**|O objeto _Recorrência_ especifica regras de recorrência para o trabalho e a recorrência da sua execução. O objeto de recorrência dá suporte aos elementos _frequência, intervalo, endTime, contagem_ e _agendamento_. Se a _recorrência_ estiver definida, a _frequência_ é necessária; os outros elementos de _recorrência_ são opcionais.|
|**_frequência_**|A cadeia de caracteres _frequência_ que representa a unidade de frequência com que o trabalho se repete. Valores com suporte são _"minuto", "hora", "dia", "semana"_ ou _"mês"._|
|**_intervalo_**|O _intervalo_ é um inteiro positivo e denota o intervalo para a _frequência_ que determina com que assiduidade o trabalho será executado. Por exemplo, se o _intervalo_ for três e a _frequência_ for "semana", o trabalho se repete a cada três semanas. O Agendador do Azure dá suporte a um _intervalo_ máximo de 18 meses para frequência mensal, 78 semanas para frequência semanal ou 548 dias para frequência diária. Para frequência de hora e minuto, o intervalo com suporte é 1 < = _intervalo_ < = 1000.|
|**_endTime_**|A cadeia de caracteres _endTime_ especifica a data e hora depois da qual o trabalho não deve ser executado. Não é válido ter um _endTime_ no passado. Se nenhum _endTime_ ou contagem for especificado, o trabalho é executado indefinidamente. _endTime_ e _contagem_ não podem ser incluídos em um mesmo trabalho.|
|**_contagem_**|<p>A _contagem_ é um inteiro positivo (maior que zero) que especifica o número de vezes que esse trabalho deve ser executado antes da conclusão.</p><p>A _contagem_ representa o número de vezes que o trabalho é executado antes de ser definido como concluído. Por exemplo, para um trabalho que é executado diariamente com _contagem_ cinco e a data inicial na segunda-feira, o trabalho é concluído depois da execução na sexta-feira. Se a data de início estiver no passado, a primeira execução é calculada do momento da criação.</p><p>Se nenhum _endTime_ ou _contagem_ for especificado, o trabalho será executado indefinidamente. _endTime_ e _contagem_ não podem ser incluídos em um mesmo trabalho.</p>|
|**_agendamento_**|Um trabalho com uma frequência especificada altera sua recorrência com base em um agendamento de recorrência. Um _agendamento_ contém modificações com base em minutos, horas, dias da semana, dias do mês e número da semana.|

## Visão geral: padrões de esquema, limites e exemplos de trabalho

Após essa visão geral, vamos examinar cada um desses elementos em detalhes.

|**Nome JSON**|**Tipo de valor**|**Obrigatório?**|**Valor padrão**|**Valores válidos**|**Exemplo**|
|:---|:---|:---|:---|:---|:---|
|**_startTime_**|Cadeia de caracteres|Não|Nenhum|Data e hora ISO 8601|<code>"startTime" : "2013-01-09T09:30:00-08:00"</code>|
|**_recorrência_**|Objeto|Não|Nenhum|Objeto de recorrência|<code>"recorrência": {"frequência": "mensalmente", "intervalo" : 1}</code>|
|**_frequência_**|Cadeia de caracteres|Sim|Nenhum|"minuto", "hora", "dia", "semana", "mês"|<code>"frequência": "hora"</code> |
|**_intervalo_**|Número|Não|1|1 a 1000.|<code>"intervalo": 10</code>|
|**_endTime_**|Cadeia de caracteres|Não|Nenhum|Valor de data e hora que representa um momento no futuro|<code>"endTime" : "2013-02-09T09:30:00-08:00"</code> |
|**_contagem_**|Número|Não|Nenhum|>= 1|<code>"contagem": 5</code>|
|**_agendamento_**|Objeto|Não|Nenhum|Objeto Agendamento|<code>"agendamento" : { "minuto" : [30], "hora" : [8,17] }</code>|

## Análise aprofundada: _startTime_

A tabela a seguir mostra como _startTime_ controla a maneira em que um trabalho é executado.

|**valor startTime**|**Sem recorrência**|**Recorrência. Sem agendamento**|**Recorrência com agendamento**|
|:--|:--|:--|:--|
|**Sem hora de início**|Executar uma vez imediatamente|Executar uma vez imediatamente. Fazer as execuções subsequentes com base no cálculo do tempo da última execução|<p>Executar uma vez imediatamente</p><p>Fazer as execuções subsequentes com base no agendamento de recorrência</p>|
|**Hora de início no passado**|Executar uma vez imediatamente|<p>Calcular o primeiro tempo de execução futuro após a hora de início e executar nesse horário</p><p>Fazer as execuções subsequentes com base no tempo da última execução</p><p>Confira o exemplo depois desta tabela para explicações adicionais</p>|<p>O trabalho inicia_somente depois_ da hora de início especificada. A primeira ocorrência baseia-se no agendamento calculado a partir da hora de início</p><p>Executar execuções subsequentes com base no agendamento de recorrência</p>|
|**Hora de início no futuro ou no momento**|Executar uma vez na hora de início especificada|<p>Executar uma vez na hora de início especificada</p><p>Fazer as execuções subsequentes com base no cálculo do tempo da última execução</p>|<p>O trabalho inicia_somente depois_ da hora de início especificada. A primeira ocorrência baseia-se no agendamento calculado a partir da hora de início</p><p>Executar execuções subsequentes com base no agendamento de recorrência</p>|

Vejamos um exemplo do que acontece quando _startTime_ está no passado, com _recorrência_, mas sem _agendamento_. Suponha que a hora atual seja 08-04-2015 13:00, a _startTime_ seja 07-04-2015 14:00 e a _recorrência_ seja a cada dois dias (definida com _frequência_: dia e _intervalo_: 2.) Observe que a _startTime_ está no passado e ocorre antes da hora atual

Sob essas condições, a _primeira execução_ será em 09-04-2015 às 14:00. O mecanismo do Agendador calcula as ocorrências de execução desde a hora de início. As instâncias no passado serão descartadas. O mecanismo usa a próxima instância que ocorrer no futuro. Nesse caso, a _startTime_ é 07-04-2015 às 14:00; portanto, a próxima instância é dois dias a partir desse momento, que é 09-04-2015 às 14:00.

Observe que a primeira execução deve ser a mesma que a hora de início de 05-04-2015 14:00 ou 01-04-2015 14:00. Após a primeira execução, as execuções subsequentes são calculadas usando a agendada; portanto, seriam em 11-04-2015 às 14:00, 13-04-2015 às 14:00, 15-04-2015 às 14:00, etc.

Finalmente, quando um trabalho tiver um agendamento, se as horas e/ou minutos não estiverem definidos no agendamento, serão adotadas as horas e/ou minutos da primeira execução, respectivamente.

## Análise aprofundada: _agendamento_

Por um lado, um _agendamento_ pode _limitar_ o número de execuções do trabalho. Por exemplo, se um trabalho com uma frequência de "mês" tem um _agendamento_ que é executado somente no dia 31, o trabalho é executado apenas nos meses que têm o dia 31<sup></sup>.

Por outro lado, um _agendamento_ também pode _expandir_ o número de execuções do trabalho. Por exemplo, se um trabalho com uma frequência de "mês" tiver um _agendamento_ que é executado nos dias do mês 1 e 2, o trabalho é executado no 1<sup>º</sup> e 2<sup>º</sup> dias do mês em vez de apenas uma vez por mês.

Se forem especificados vários elementos de agendamento, a ordem de avaliação é do maior para o menor: número da semana, dia do mês, dia da semana, hora e minuto.

A tabela a seguir descreve elementos de _agendamento_ em detalhes.

|**Nome JSON**|**Descrição**|**Valores Válidos**|
|:---|:---|:---|
|**minutos**|Minutos da hora em que o trabalho será executado|<ul><li>Número inteiro ou</li><li>Matriz de números inteiros</li></ul>|
|**horas**|Horas do dia em que o trabalho será executado|<ul><li>Número inteiro ou</li><li>Matriz de números inteiros</li></ul>|
|**Dias da semana**|Dias da semana em que o trabalho será executado. Só pode ser especificado com uma frequência semanal.|<ul><li>"Segunda-feira", "Terça-feira", "Quarta-feira", "Quinta-feira", "Sexta-feira", "Sábado" ou "Domingo"</li><li>Matriz de qualquer um dos valores acima (tamanho máximo da matriz 7)</li></ul>\_Não\_diferencia\_maiúsculas\_de\_minúsculas|
|**monthlyOccurences**|Determina em quais dias do mês o trabalho será executado. Só pode ser especificado com uma frequência mensal.|<ul><li>Matriz de objetos monthlyOccurence:</li></ul> <pre>{"dia": _dia_,<br /> "ocorrência": _ocorrência_<br />}</pre><p> _dia_ é o dia da semana em que o trabalho será executado, por exemplo, {Domingo} é todo domingo do mês. Obrigatório.</p><p>Ocorrência é a _ocorrência_ do dia durante o mês, por exemplo, {Domingo, -1} é o último domingo do mês. Opcional.</p>|
|**Dias do mês**|Dia do mês em que o trabalho será executado. Só pode ser especificado com uma frequência mensal.|<ul><li>Qualquer valor < = -1 e > = -31.</li><li>Qualquer valor > = 1 e < = 31.</li><li>Uma matriz dos valores acima</li></ul>|

## Exemplos: agendamentos de recorrência

Seguem diversos exemplos de agendamentos de recorrência voltados para o objeto de agendamento e seus subelementos.

Todos os agendamentos abaixo pressupõem que o _intervalo_ é definido como 1. Além disso, deve assumir a frequência correta de acordo com o que está no _agendamento_, por exemplo, não é possível usar a frequência "dia" e ter uma modificação de "dias do mês" no agendamento. As restrições estão descritas acima.

|**Exemplo**|**Descrição**|
|:---|:---|
|<code>{"horas":[5]}</code>|Executar às 5h da manhã todos os dias. O Agendador do Azure corresponde a cada valor em "horas" com cada valor em "minutos", um a um, para criar uma lista de todos os horários em que o trabalho deve ser executado.|
|<code>{"minutos":[15], "horas":[5]}</code>|Executar às 5:15 todos os dias|
|<code>{"minutos":[15], "horas":[5,17]}</code>|Executar às 5:15 e 17:15 todos os dias|
|<code>{"minutos":[15,45], "horas":[5,17]}</code>|Executar às 5:15, 5:45, 17:15 e 17:45 todos os dias|
|<code>{"minutos": [0,15,30,45]}</code>|Executar a cada 15 minutos|
|<code>{horas": [0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23]}</code>|Executar a cada hora. Esse trabalho é executado a cada hora. O minuto é controlado pela _startTime_, se for especificado ou não no momento da criação. Por exemplo, se a hora de início ou a hora de criação (o que for aplicável) for 00:25, o trabalho será executado às 00:25, 01:25, 02:25, …, 23:25. O agendamento é equivalente a ter um trabalho com _frequência_ de "hora", _intervalo_ de 1 e sem _agendamento_. A diferença é que esse agendamento pode ser usado com diferentes _frequências_ e _intervalos_ para também criar outros trabalhos. Por exemplo, se a _frequência_ fosse "mês", o agendamento seria executado somente uma vez por mês, em vez de todos os dias com a _frequência_ sendo "dia"|
|<code>{minutos: [0]}</code>|Execute a cada hora em hora exata. Esse trabalho também é executado a cada hora, mas na hora exata (por exemplo, 00:00, 1:00, 2:00, etc.) Isso equivale a um trabalho com frequência de "hora", startTime com zero minutos e sem agendamento se a frequência fosse "dia", mas, sendo a frequência "semana" ou "mês", o agendamento deve executar apenas uma vez por semana ou por mês, respectivamente.|
|<code>{"minutos": [15]}</code>|Executar 15 minutos após a hora exata a cada hora. É executado a cada hora, começando em 00:15, 1:15, 2:15, etc. e terminando às 22:15 e 23:15.|
|<code>{"horas":[17], "dias da semana": ["sábado"]}</code>|Executar às 17h aos sábados toda semana|
|<code>{horas":[17],"dias da semana": ["segunda-feira","quarta-feira","sexta-feira"]}</code>|Executar às 17h às segundas-feiras, quartas-feiras e sextas-feiras toda semana|
|<code>{"minutos":[15,45], "horas":[17], "dias da semana": ["segunda-feira", "quarta-feira", "sexta-feira"]}</code>|Executar às 17:15 e 17:45 às segundas-feiras, quartas-feiras e sextas-feiras toda semana|
|<code>{"horas":[5,17], "dias da semana": ["segunda-feira", "quarta-feira", "sexta-feira"]}</code>|Executar às 5h e às 17h toda semana às segundas-feiras, quartas-feiras e sextas-feiras|
|<code>{"minutos":[15,45], "horas":[5,17], "dias da semana": ["segunda-feira", "quarta-feira", "sexta-feira"]}</code>|Executar às 5:15, 5:45, 17:15 e 17:45 às segundas-feiras, quartas-feiras e sextas-feiras toda semana|
|<code>{"minutos":[0,15,30,45], "dias da semana": ["segunda-feira", "terça-feira", "quarta-feira", "quinta-feira", "sexta-feira"]}</code>|Executar a cada 15 minutos em dias da semana|
|<code>{"minutos":[0,15,30,45], "horas": [9, 10, 11, 12, 13, 14, 15, 16] "dias da semana": ["segunda-feira", "terça-feira", "quarta-feira", "quinta-feira", "sexta-feira"]}</code>|Executar a cada 15 minutos nos dias úteis entre 9:00 e 16:45|
|<code>{"dias da semana":["domingo"]}</code>|Executar aos domingos na hora de início|
|<code>{"dias da semana":["terça-feira", "quinta-feira"]}</code>|Executar às terças e quintas-feiras na hora de início|
|<code>{"minutos":[0], "horas":[6], "dias mês":[28]}</code>|Executar às 6h no 28º dia de cada mês (supondo a frequência do mês)|
|<code>{"minutos":[0], "horas":[6], "dias mês":[-1]}</code>|Executar às 6h no último dia do mês. Se você quiser executar um trabalho no último dia de um mês, use -1 em vez de dia 28, 29, 30 ou 31.|
|<code>{"minutos":[0], "horas":[6], "dias mês": [1, -1]}</code>|Executar às 6h no primeiro e no último dia de cada mês|
|<code>{dias mês":[1, -1]}</code>|Executar no primeiro e no último dia de cada mês na hora de início|
|<code>{dias mês": [1,14]}</code>|Executar no primeiro e no décimo quarto dia de cada mês na hora de início|
|<code>{dias mês": [2]}</code>|Executar no segundo dia do mês na hora de início|
|<code>{"minutos": [0], "horas": [5], "monthlyOccurrences": [{"dia": "sexta-feira", "ocorrência": 1}]}</code>|Executar na primeira sexta-feira de cada mês às 5h|
|<code>{"monthlyOccurrences": [{"dia": "sexta-feira", "ocorrência": 1}]}</code>|: Executar na primeira sexta-feira de cada mês na hora de início|
|<code>{"monthlyOccurrences": [{"dia": "sexta-feira", "ocorrência": -3}]}</code>|Executar na terceira sexta-feira do final do mês, todo mês, na hora de início|
|<code>{"minutos": 15, "horas": [5], "monthlyOccurrences": [{"dia": "sexta-feira", "ocorrência": 1}, {"dia": "sexta-feira", "ocorrência": -1}]}</code>|Executar na primeira e na última sexta-feira de cada mês às 5:15|
|<code>{"monthlyOccurrences": [{"dia": "sexta-feira", "ocorrência": 1}, {"dia": "sexta-feira", "ocorrência": -1}]}</code>|Executar na primeira e na última sexta-feira de cada mês na hora de início|
|<code>{"monthlyOccurrences": [{"dia": "sexta-feira", "ocorrência": 5}]}</code>|Executar na quinta sexta-feira de cada mês na hora de início. Se não houver nenhuma quinta sexta-feira em um mês, ele não é executado, uma vez que está agendado para executar apenas nas quintas sextas-feiras. Considere o uso de -1 em vez de 5 na ocorrência se você quiser executar o trabalho na última sexta-feira do mês.|
|<code>{"minutos": [0,15,30,45] "monthlyOccurrences": [{"dia": "sexta-feira", "ocorrência": -1}]}</code>|Executar a cada 15 minutos na última sexta-feira do mês|
|<code>{"minutos": [15,45] "horas": [5,17] "monthlyOccurrences": [{"dia": "quarta-feira", "ocorrência": 3}]}</code>|Executar às 5:15, 5:45, 17:15 e 17:45 na terceira quarta-feira de cada mês|

## Consulte também
 
 [O que é o Agendador?](scheduler-intro.md)
 
 [Conceitos, terminologia e hierarquia de entidades do Agendador](scheduler-concepts-terms.md)
 
 [Introdução à utilização do Agendador no Portal de Gerenciamento](scheduler-get-started-portal.md)
 
 [Planos e cobrança no Agendador do Azure](scheduler-plans-billing.md)
 
 [Referência da API REST do Agendador](https://msdn.microsoft.com/library/dn528946)
 
 [Referência de cmdlets do PowerShell do Agendador](scheduler-powershell-reference.md)
 
 [Alta disponibilidade e confiabilidade do Agendador](scheduler-high-availability-reliability.md)
 
 [Limites, padrões e códigos de erro do Agendador](scheduler-limits-defaults-errors.md)
 
 [Autenticação de saída do Agendador](scheduler-outbound-authentication.md)
 
  

<!---HONumber=Oct15_HO3-->