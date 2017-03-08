---
title: "Visão geral de alertas no OMS Log Analytics | Microsoft Docs"
description: "Alertas no Log Analytics identificam informações importantes em seu repositório do OMS e podem notificar proativamente problemas ou invocar ações para tentar corrigi-los.  Este artigo descreve como criar uma regra de alerta e detalha as diferentes ações que elas podem executar."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: ec7167fb36e54219957629699f33dfce950f86d5
ms.openlocfilehash: 48d921650dbbf3f9cc2a8a0e265e0c7deaebdafd
ms.lasthandoff: 03/01/2017


---
# <a name="respond-to-issues-in-log-analytics-using-alerts"></a>Responder a problemas no Log Analytics usando alertas

Alertas no Log Analytics identificam informações importante no repositório de Log Analytics.  Você pode usá-los para notificar sobre problemas de maneira proativa ou executar ações automáticas em resposta ao problema.  Este artigo fornece uma visão geral de como os alertas são criados e usados.  


>[!NOTE]
> Para saber mais sobre regras de alerta de medição de métrica que estão atualmente em visualização pública, consulte [Novo tipo de regra de alerta de medição de métrica em visualização pública!](https://blogs.technet.microsoft.com/msoms/2016/11/22/new-metric-measurement-alert-rule-type-in-public-preview/).

## <a name="alert-rules"></a>Regras de alerta

Os alertas são criados por regras de alerta que executam pesquisas de log automaticamente em intervalos regulares.  Se os resultados da pesquisa de log corresponderem a critérios específicos, um registro de alerta será criado.  A regra também pode executar automaticamente uma ou mais ações para notificar você proativamente do alerta ou invocar outro processo.  

![Alertas do Log Analytics](media/log-analytics-alerts/overview.png)


Regras de Alerta são definidas pelos detalhes a seguir.

- **Pesquisa de log**.  Esta é a consulta que será executada sempre que a regra de alerta for acionada.  Os registros retornados por essa consulta serão usados para determinar se um alerta é criado.
- **Janela de tempo**.  Especifica o intervalo de tempo para a consulta.  A consulta retorna somente os registros que foram criados dentro desse intervalo de tempo atual.  Este pode ser qualquer valor entre 5 minutos e 24 horas. Por exemplo, se a janela de tempo está definida para 60 minutos e a consulta é executada às 13:15h, somente os registros criados entre 12:15h e 13:15h serão retornados.
- **Frequência**.  Especifica a frequência com que a consulta deve ser executada. Pode ser qualquer valor entre 5 minutos e 24 horas. Deve ser igual a ou menor que a janela de tempo.  Se o valor for maior que a janela de tempo, haverá o risco de que registros sejam perdidos.<br>Por exemplo, considere uma janela de tempo de 30 minutos e uma frequência de 60 minutos.  Se a consulta for executada à 1:00, retornará registros entre 12:30 e 1:00.  A próxima vez em que a consulta será executada é às 2:00, quando ela retornará registros entre 1:30 e 2:00.  Todos os registros criados entre 1:00 e 1:30 nunca seriam avaliados.
- **Limite**.  Se o número de registros retornados da pesquisa de log de exceder o limite, um alerta será criado.

## <a name="creating-alert-rules"></a>Criando regras de alerta
Você pode criar e modificar vários métodos de regras de alerta.  Confira os artigos a seguir para obter orientações detalhadas.  

- Criar regras de alerta usando o [portal do OMS](log-analytics-alerts-creating.md).
- Criar regras de alerta usando o [modelo do Resource Manager](log-analytics-template-workspace-configuration.md).
- Criar regras de alerta usando a [API REST](log-analytics-api-alerts.md).

## <a name="alert-actions"></a>Ações de alerta

Além de criar um registro de alerta, uma regra de alerta pode exigir uma ou mais ações quando ele cria um alerta.  Você pode usar ações para enviar um email em resposta ao alerta ou iniciar um processo que tenta realizar uma ação corretiva.  

Você também pode aproveitar as ações para ampliar a funcionalidade de Log Analytics com outros serviços.  Por exemplo, no momento ele não oferece recursos para notificá-lo por SMS ou telefone.  Você pode usar uma ação de webhook em uma regra de alerta para chamar um serviço como [PagerDuty](https://www.pagerduty.com/), que fornece esses recursos.  Você pode ver passo a passo um exemplo de como criar um webhook para enviar uma mensagem usando [Slack](https://slack.com/) em [Criar uma ação de webhook de alerta no Log Analytics do OMS para enviar mensagem ao Slack](log-analytics-alerts-webhooks.md).

A tabela a seguir lista as ações que você pode executar.  Você pode saber mais sobre cada um em [Adicionando ações de regras de alerta no Log Analytics](log-analytics-alerts-actions.md). 

| Ação | Descrição |
|:--|:--|
| Email  |     Envie um email com os detalhes do alerta para um ou mais destinatários. |
| webhook | Invoque um processo externo por meio de uma única solicitação HTTP POST. |
| Runbook | Inicie um runbook na Automação do Azure. |


## <a name="alerting-scenarios"></a>Cenários de alertas

### <a name="events"></a>Eventos
Para alertar quanto a um único evento, crie uma regra de alerta com o número de resultados para **maior que 0** e a frequência e a janela de tempo para **5 minutos**.  Isso vai executar a consulta a cada 5 minutos e verificar a ocorrência de um único evento que foi criado desde a última vez em que a consulta foi executada.  Uma frequência maior pode atrasar o tempo entre o evento que está sendo coletado e o alerta criado.  Você usaria uma consulta semelhante à seguinte para especificar o evento em que está interessado.

    Type=Event Source=MyApplication EventID=7019 

Alguns aplicativos podem registrar um erro ocasional que não necessariamente gerará um alerta.  Por exemplo, o aplicativo pode repetir o processo que criou o evento de erro e depois ter êxito na próxima vez.  Nesse caso, não convém criar o alerta, a menos que vários eventos sejam criados dentro de uma janela de tempo específica.  Para fazer isso, você usaria a mesma consulta, mas definiria o limite como um número maior.  Por exemplo, para alertar sobre cinco eventos em 30 minutos, você define a frequência como **5 minutos**, a janela de tempo como **30 minutos** e o número de resultados como **maior que 4**.    

Em alguns casos, pode ser útil criar um alerta na ausência de um evento.  Por exemplo, um processo pode registrar eventos regulares para indicar que ele está funcionando corretamente.  Se ele não registrar um desses eventos dentro de uma janela de tempo específica, um alerta deverá ser criado.  Nesse caso, você definiria o número de resultados como **menos de 1**.

### <a name="performance-alerts"></a>Alertas de desempenho
[Os dados de desempenho](log-analytics-data-sources-performance-counters.md) são armazenado como registros no repositório de Log Analytics< de maneira semelhante a eventos.  Se você deseja alertar quando um contador de desempenho exceder um limite específico, esse limite deve ser incluído na consulta.

Por exemplo, se você quisesse um alerta quando o processador ultrapassasse 90%, usaria uma consulta como a seguinte, com o número de resultados para a regra de alerta **maior do que 0**.

    Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90

Se você quiser alertar quando o processador ficar acima de 90% durante um determinado período, use uma consulta com o [comando measure](log-analytics-search-reference.md#commands), como no seguinte, com o limite para a regra de alerta **maior do que 0**. 

    Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer | where AggregatedValue>90


## <a name="alert-records"></a>Registros de alerta
Registros de alerta criados por regras de alerta no Log Analytics têm um **Type** que indica **Alert** e um **SourceSystem** que indica **OMS**.  Eles têm as propriedades indicadas na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| Tipo |*Alerta* |
| SourceSystem |*OMS* |
| AlertName |Nome do alerta. |
| AlertSeverity |Nível de severidade do alerta. |
| LinkToSearchResults |Vincular a pesquisa de log do Log Analytics que retorna os registros da consulta que criou o alerta. |
| Consultar |Texto da consulta que foi executada. |
| QueryExecutionEndTime |Término do intervalo de tempo para a consulta. |
| QueryExecutionStartTime |Início do intervalo de tempo para a consulta. |
| ThresholdOperator | Operador que foi usado pela regra de alerta. |
| ThresholdValue | Valor que foi usado pela regra de alerta. |
| TimeGenerated |Data e hora em que o alerta foi criado. |

Há outros tipos de registros de alerta criados pela [solução de Gerenciamento de Alertas](log-analytics-solution-alert-management.md) e por [exportações do Power BI](log-analytics-powerbi.md).  Eles têm um **Type** que indica **Alert**, mas são diferenciados por seus **SourceSystem**.


## <a name="next-steps"></a>Próximas etapas
* Criar uma regra de alerta usando o [portal do OMS](log-analytics-alerts-creating.md).
* Instale a [solução de Gerenciamento de Alertas](log-analytics-solution-alert-management.md) para analisar os alertas criados no Log Analytics junto com aqueles coletados do SCOM (System Center Operations Manager).
* Leia mais sobre [pesquisas de log](log-analytics-log-searches.md) que podem gerar alertas.
* Conclua um passo a passo para [configurar um webhook](log-analytics-alerts-webhooks.md) com uma regra de alerta.  
* Aprenda a criar [runbooks na Automação do Azure](https://azure.microsoft.com/documentation/services/automation) para corrigir os problemas identificados por alertas.


