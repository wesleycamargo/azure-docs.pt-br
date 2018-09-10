---
title: Como compilar agendas complexas e recorrência avançada com o Agendador do Microsoft Azure
description: Como compilar agendas complexas e recorrência avançada com o Agendador do Microsoft Azure.
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 5c124986-9f29-4cbc-ad5a-c667b37fbe5a
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: 4293442e13fc4bae871b1f32a3ed4231d9f32632
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
ms.locfileid: "29692327"
---
# <a name="build-complex-schedules-and-advanced-recurrence-with-azure-scheduler"></a>Como compilar agendas complexas e recorrência avançada com o Agendador do Microsoft Azure

O núcleo de um trabalho do Agendador do Microsoft Azure é a agenda. A agenda determina quando e como o Agendador executará o trabalho. 

É possível usar o Agendador para definir vários agendamentos recorrentes e de execução única para um trabalho. Agendas de execução única acionam uma vez em um horário especificado. Efetivamente, as agendas de execução única são horários recorrentes que são executados apenas uma vez. Os agendamentos recorrentes são acionados com uma frequência predeterminada.

Com esta flexibilidade, é possível utilizar o Agendador para uma grande variedade de cenários de negócios:

* **Limpeza periódica de dados**. Por exemplo, todos os dias, exclua todos os tweets com mais de três meses.
* **Arquivamento**. Por exemplo, todos os meses, envie por push o histórico de faturas para um serviço de backup.
* **Solicitações para dados externos**. Por exemplo, a cada 15 minutos, efetue pull de um novo relatório meteorológico de esqui a partir de NOAA.
* **Processamento de imagens**. Por exemplo, todos os dias da semana, fora do horário de pico, use a computação em nuvem para compactar imagens que foram carregadas no dia.

Neste artigo, percorremos trabalhos de exemplos que podem ser criados utilizando o Agendador. Nós fornecemos os dados JSON que descrevem cada agendamento. Se você utilizar a [API REST do Agendador](https://msdn.microsoft.com/library/mt629143.aspx), poderá usar esse mesmo JSON para [criar um trabalho do Agendador](https://msdn.microsoft.com/library/mt629145.aspx).

## <a name="supported-scenarios"></a>Cenários com suporte
Os exemplos neste artigo ilustram a amplitude dos cenários que o Agendador fornece suporte. Os exemplos ilustram amplamente como criar agendas para muitos padrões de comportamento, incluindo:

* Executar uma vez em uma data e hora específicas.
* Executar e repetir um número específico de vezes.
* Executar imediatamente e repetir.
* Executar e repetir a cada *n* minutos, horas, dias, semanas ou meses, iniciando em um horário específico.
* Executar e repetir em uma frequência semanal ou mensal, mas apenas em dias específicos da semana ou em dias específicos do mês.
* Executar e repetir várias vezes em um período. Por exemplo, na última sexta-feira e última segunda-feira de cada mês, ou às 5:15 e às 17:15 todos os dias.

## <a name="date-and-date-time"></a>Data e data/hora
As referências de data nos trabalhos do Agendador seguem as [especificações ISO 8601](http://en.wikipedia.org/wiki/ISO_8601) e incluem apenas a data.

As referências de data/hora nos trabalhos do Agendador seguem as [especificações ISO 8601](http://en.wikipedia.org/wiki/ISO_8601) e incluem a data e a hora. Uma data/hora que não especifica um deslocamento de UTC é assumida como UTC.  

## <a name="use-json-and-the-rest-api-to-create-a-schedule"></a>Usar JSON e API REST para criar uma agenda
Para criar uma agenda básica usando a [API REST do Agendador](https://msdn.microsoft.com/library/mt629143), primeiro [registre sua assinatura com um provedor de recursos](https://msdn.microsoft.com/library/azure/dn790548.aspx). O nome do provedor do Agendador é **Microsoft.Scheduler**. Em seguida, [crie uma coleção de trabalhos](https://msdn.microsoft.com/library/mt629159.aspx). Por fim, [crie um trabalho](https://msdn.microsoft.com/library/mt629145.aspx). 

Ao criar um trabalho, é possível especificar o agendamento e a recorrência utilizando o JSON, como neste extrato:

    {
        "startTime": "2012-08-04T00:00Z", // Optional
         …
        "recurrence":                     // Optional
        {
            "frequency": "week",     // Can be "year", "month", "day", "week", "hour", or "minute"
            "interval": 1,                // How often to fire
            "schedule":                   // Optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]                      
            },
            "count": 10,                  // Optional (default to recur infinitely)
            "endTime": "2012-11-04",      // Optional (default to recur infinitely)
        },
        …
    }

## <a name="job-schema-basics"></a>Noções básicas sobre esquemas de trabalho
A tabela a seguir fornece uma visão geral de alto nível dos principais elementos utilizados para definir a recorrência e o agendamento em um trabalho:

| Nome JSON | DESCRIÇÃO |
|:--- |:--- |
| **startTime** |Um valor de data/hora. Para agendas básicas, **startTime** é a primeira ocorrência. Para agendas complexas, o trabalho não inicia antes do **startTime**. |
| **recurrence** |Especifica as regras de recorrência para o trabalho e a recorrência na qual o trabalho executa. O objeto de recorrência tem suporte para os elementos **frequency**, **interval**, **endTime**, **count** e **schedule**. Se a**recorrência** estiver definida, a **frequência** será obrigatória. Outros elementos da **recorrência** são opcionais. |
| **frequency** |Uma cadeia de caracteres que representa a unidade de frequência na qual o trabalho repete. Os valores com suporte são "minutos", "hora", "dia", "semana" e "mês". |
| **interval** |Um número inteiro positivo. **Interval** indica o intervalo para o valor da **frequência** que determina a frequência com que o trabalho é executado. Por exemplo, se **interval** for 3 e **frequency** for "week", o trabalho se repetirá a cada três semanas.<br /><br />O Agendador tem suporte para um **intervalo** máximo de 18 para frequência mensal, 78 para frequência semanal e 548 para frequência diária. Para a frequência em hora e em minuto, o intervalo com suporte é 1 <= **interval** <= 1.000. |
| **endTime** |Uma cadeia de caracteres que especifica a data/hora além da qual o trabalho não é executado. Você pode definir um valor para **endTime** que está no passado. Se **endTime** e **count** não forem especificados, o trabalho será executado infinitamente. Você não pode incluir **endTime** e **count** no mesmo trabalho. |
| **count** |Um número inteiro positivo (maior que zero) que especifica o número de vezes que o trabalho é executado antes de ser concluído.<br /><br />**count** representa o número de vezes que o trabalho é executado antes de ser determinado concluído. Por exemplo, para um trabalho que é executado diariamente com um **count** de 5 e uma data de início de segunda-feira, o trabalho será concluído após a execução na sexta-feira. Se a data de início estiver no passado, a primeira execução é calculada do momento da criação.<br /><br />Se nem **endTime** nem **count** forem especificados, o trabalho será executado de maneira infinita. Você não pode incluir **endTime** e **count** no mesmo trabalho. |
| **schedule** |Um trabalho com uma frequência especificada altera sua recorrência com base em um agendamento de recorrência. Um valor **schedule** contém as modificações com base em minutos, horas, dias da semana, dias do mês e em número da semana. |

## <a name="job-schema-defaults-limits-and-examples"></a>Padrões, limites e exemplos de esquema de trabalhos
Posteriormente, discutiremos detalhadamente no artigo cada um dos elementos a seguir:

| Nome JSON | Tipo de valor | Obrigatório? | Valor padrão | Valores válidos | Exemplo |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** |string |Não  |Nenhum |Data/hora ISO 8601 |`"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** |objeto |Não  |Nenhum |Objeto de recorrência |`"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **frequency** |string |sim |Nenhum |"minuto", "hora", "dia", "semana", "mês" |`"frequency" : "hour"` |
| **interval** |número |sim |Nenhum |1 a 1000 |`"interval":10` |
| **endTime** |string |Não  |Nenhum |Valor de data/hora que representa uma hora no futuro |`"endTime" : "2013-02-09T09:30:00-08:00"` |
| **count** |número |Não  |Nenhum |>= 1 |`"count": 5` |
| **schedule** |objeto |Não  |Nenhum |Objeto de agendamento |`"schedule" : { "minute" : [30], "hour" : [8,17] }` |

## <a name="deep-dive-starttime"></a>Análise aprofundada: startTime
A tabela a seguir descreve como **startTime** controla a maneira que um trabalho é executado:

| Valor startTime | Sem recorrência | Recorrência, sem agendamento | Recorrência com agendamento |
|:--- |:--- |:--- |:--- |
| **Sem hora de início** |Executar uma vez imediatamente. |Executar uma vez imediatamente. Executar as execuções subsequentes calculadas a partir do último tempo de execução. |Executar uma vez imediatamente.<br /><br />Realizar as execuções subsequentes com base no agendamento de recorrência. |
| **Hora de início no passado** |Executar uma vez imediatamente. |Calcular a primeira hora de execução futura após a hora de início e executar naquela hora.<br /><br />Executar as execuções subsequentes calculadas a partir do último tempo de execução. <br /><br />Para obter mais informações, consulte o exemplo que segue esta tabela. |O trabalho é iniciado *somente na* hora de início especificada. A primeira ocorrência baseia-se no elemento schedule calculado com base na hora de início.<br /><br />Realizar as execuções subsequentes com base no agendamento de recorrência. |
| **Hora de início no futuro ou a hora atual** |Executar uma vez na hora de início especificada. |Executar uma vez na hora de início especificada.<br /><br />Executar as execuções subsequentes calculadas a partir do último tempo de execução.|O trabalho é iniciado *somente na* hora de início especificada. A primeira ocorrência baseia-se no elemento schedule calculado com base na hora de início.<br /><br />Realizar as execuções subsequentes com base no agendamento de recorrência. |

Vejamos um exemplo do que acontece quando **startTime** está no passado, com recorrência, mas sem agenda.  Suponha que a hora atual seja 2015-04-08 13:00, que **startTime** seja 2015-04-07 14:00 e que **recurrence** seja a cada dois dias (definida com **frequency**: day e **interval**: 2.) Observe que **startTime** está no passado e ocorre antes da hora atual.

Nessas condições, a primeira execução será em 09-04-2015 às 14:00\. O mecanismo do Agendador calcula as ocorrências de execução desde a hora de início. As instâncias no passado serão descartadas. O mecanismo usa a próxima instância que ocorrer no futuro. Nesse caso, **startTime** é 07/04/2015 às 14:00, portanto, a próxima instância será dois dias a partir dessa hora, que será em 09/04/2015 às 14:00.

Observe que a primeira execução seria a mesma se **startTime** fosse 05/04/2015 14:00 ou 01/04/2015 14:00\. Após a primeira execução, as execuções seguintes são calculadas com o uso do agendamento. Elas serão em 11/04/2015 às 14:00, depois em 13/04/2015 às 14:00, em seguida, 15/04/2015 às 14:00, e assim por diante.

Por fim, quando um trabalho tiver uma agenda, se as horas e os minutos não estiverem definidos na agenda, serão adotados os valores padrão para as horas e os minutos da primeira execução, respectivamente.

## <a name="deep-dive-schedule"></a>Análise aprofundada: agenda
Você pode usar **schedule** para *limitar* o número de execuções de trabalho. Por exemplo, se um trabalho com uma **frequência** de "month" tiver uma agenda que executa somente no dia 31, o trabalho será executado apenas em meses que tenham um trigésimo primeiro dia.

Também é possível usar **schedule** para *expandir* o número de execuções de trabalho. Por exemplo, se um trabalho com uma **frequência** de "month" tiver uma agenda para ser executado nos dias 1 e 2 do mês, o trabalho será executado no primeiro e segundo dias do mês, em vez de apenas uma vez por mês.

Se você especificar vários elementos de agenda, a ordem de avaliação será do maior para o menor: número da semana, dia do mês, dia da semana, hora e minuto.

A tabela a seguir descreve elementos schedule em detalhes:

| Nome JSON | DESCRIÇÃO | Valores válidos |
|:--- |:--- |:--- |
| **minutos** |Minutos da hora em que o trabalho executa. |Uma matriz de inteiros. |
| **horas** |Horas do dia em que o trabalho executa. |Uma matriz de inteiros. |
| **Dias da semana** |Dias da semana em que o trabalho executa. Pode ser especificado apenas com uma frequência semanal. |Uma matriz de qualquer um dos valores a seguir (o tamanho máximo da matriz é 7):<br />- "Monday"<br />- "Tuesday"<br />- "Wednesday"<br />- "Thursday"<br />- "Friday"<br />- "Saturday"<br />- "Sunday"<br /><br />Não diferencia maiúsculas de minúsculas. |
| **monthlyOccurences** |Determina em quais dias do mês o trabalho executa. Pode ser especificado apenas com uma frequência mensal. |Uma matriz de objetos **monthlyOccurrences**:<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **day** é o dia da semana em que o trabalho executa. Por exemplo, *{Sunday}* são todos os domingos do mês. Obrigatório.<br /><br />**occurrence** é a ocorrência do dia durante o mês. Por exemplo,  *{Sunday, -1}* é o último domingo do mês. Opcional. |
| **Dias do mês** |Dia do mês em que o trabalho executa. Pode ser especificado apenas com uma frequência mensal. |Uma matriz dos valores a seguir:<br />- Qualquer valor <= -1 e >= -31<br />- Qualquer valor >= 1 e <= 31|

## <a name="examples-recurrence-schedules"></a>Exemplos: agendamentos de recorrência
Os exemplos a seguir mostram vários agendamentos de recorrência. Os exemplos concentram-se no objeto de agendamento e seus subelementos.

Esses agendamentos assumem que **intervalo** está definido para 1\. Os exemplos também assumem os valores de **frequência** corretos para os valores na **agenda**. Por exemplo, não é possível usar uma **frequência** de "day" e ter uma modificação **monthDays** na **agenda**. Nós descrevemos essas restrições no início do artigo.

| Exemplo | DESCRIÇÃO |
|:--- |:--- |
| `{"hours":[5]}` |Executar às 5:00 todos os dias.<br /><br />O Agendador corresponde a cada valor em "hours" com cada valor em "minutes", um a um, para criar uma lista de todos os horários em que o trabalho é executado. |
| `{"minutes":[15], "hours":[5]}` |Executar às 5h15 todos os dias. |
| `{"minutes":[15], "hours":[5,17]}` |Executar às 5h15 e 17h15 todos os dias. |
| `{"minutes":[15,45], "hours":[5,17]}` |Executar às 5h15, 5h45, 17h15 e 17h45 todos os dias. |
| `{"minutes":[0,15,30,45]}` |Executar a cada 15 minutos. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |Executar a cada hora.<br /><br />Esse trabalho é executado a cada hora. O minuto é controlado pelo valor de **startTime**, se especificado. Se nenhuma valor **startTime** for especificado, o minuto será controlado pelo tempo de criação. Por exemplo, se a hora de início ou a hora de criação (o que for aplicável) for 00:25, o trabalho é executado às 00:25, 01:25, 02:25, …, 23:25.<br /><br />O agendamento é equivalente a um trabalho com **frequency** de "hour", **interval** de 1 e sem valor **schedule**. A diferença é que você pode usar essa agenda com diferentes **frequency** e **interval** para criar outros trabalhos. Por exemplo, se **frequency** for "month", a agenda executa somente uma vez por mês em vez de todos os dias (se **frequency** fosse "day"). |
| `{minutes:[0]}` |Executar a cada hora na hora.<br /><br />Esse trabalho também executa a cada hora, mas na hora exata (00:00, 1:00, 2:00, e assim por diante). Isso equivale a um trabalho com uma **frequência** de "hour", um valor **startTime** se zero minutos e sem **agenda**, se a frequência for "day". No entanto, se valor de **frequência** for "week" ou "month", a agenda executa somente um dia da semana ou um dia do mês, respectivamente. |
| `{"minutes":[15]}` |Executar 15 minutos após a hora exata a cada hora.<br /><br />Executa a cada hora, iniciando em 00:15, 1:15, 2:15, e assim por diante. Termina às 23:15. |
| `{"hours":[17], "weekDays":["saturday"]}` |Executar às 17:00 aos sábados todas as semanas. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Executar às 17:00 nas segundas-feiras, quartas-feiras e sextas-feiras todas as semanas. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Executar às 17:15 e às 17:45 às segundas-feiras, quartas-feiras e sextas-feiras todas as semanas. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Executar às 17:00 e às 17:00 às segundas-feiras, quartas-feiras e sextas-feiras todas as semanas. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Executar às 5:15, 5:45, 17:15 e 17:45 às segundas-feiras, quartas-feiras e sextas-feiras todas as semanas. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Executar a cada 15 minutos em dias da semana. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Executar a cada 15 minutos nos dias da semana, entre às 9:00 e às 16:45. |
| `{"weekDays":["sunday"]}` |Executar aos domingos na hora de início. |
| `{"weekDays":["tuesday", "thursday"]}` |Executar às terças-feiras e quintas-feiras na hora de início. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Executar às 6:00 no vigésimo oitavo dia de cada mês (assumindo uma **frequência** de "month"). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |Executar às 6:00 no último dia do mês.<br /><br />Se você quiser executar um trabalho no último dia de um mês, use -1 em vez de dia 28, 29, 30 ou 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |Executar às 6:00 no primeiro e no último dia de cada mês. |
| `{monthDays":[1,-1]}` |Executar no primeiro e no último dia de cada mês na hora de início. |
| `{monthDays":[1,14]}` |Executar no primeiro e no décimo quarto dia de cada mês na hora de início. |
| `{monthDays":[2]}` |Executar no segundo dia do mês na hora de início. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Executar na primeira sexta-feira de cada mês às 5:00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Executar na primeira sexta-feira de cada mês na hora de início. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |Executar na terceira sexta-feira do final do mês, todos os meses, na hora de início. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Executar na primeira e última sexta-feira de cada mês às 5:15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Executar na primeira e última sexta-feira de cada mês na hora de início. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |Executar na quinta sexta-feira de cada mês na hora de início.<br /><br />Se não houver quinta sexta-feira em um mês, o trabalho não será executado. Considere o uso de -1 em vez de 5 na ocorrência se você quiser executar o trabalho na última sexta-feira do mês. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |Executar a cada 15 minutos na última sexta-feira do mês. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |Executar às 5h15, 5h45, 17h15 e 17h45 na terceira quarta-feira de cada mês. |

## <a name="see-also"></a>Consulte também

- [O que é o Agendador?](scheduler-intro.md)
- [Conceitos, terminologia e hierarquia de entidades do Agendador do Azure](scheduler-concepts-terms.md)
- [Introdução à utilização do Agendador no Portal do Azure](scheduler-get-started-portal.md)
- [Planos e Cobrança no Agendador do Azure](scheduler-plans-billing.md)
- [Referência da API REST do Agendador do Azure](https://msdn.microsoft.com/library/mt629143)
- [Referência de cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md)
- [Alta disponibilidade e confiabilidade do Agendador do Microsoft Azure](scheduler-high-availability-reliability.md)
- [Limites, padrões e códigos de erro do Agendador do Azure](scheduler-limits-defaults-errors.md)
- [Autenticação de saída do Agendador do Azure](scheduler-outbound-authentication.md)

