---
title: "Alertas de log no Azure Monitor - Alertas (visualização) | Microsoft Docs"
description: "Dispare emails, notificações, chame URLs de sites (webhooks) ou automação quando as condições de consulta complexa especificadas forem atendidas para os Alertas do Azure (visualização)."
author: msvijayn
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: f7457655-ced6-4102-a9dd-7ddf2265c0e2
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: vinagara
ms.openlocfilehash: 99d222102ab0245c7c4dc8603eaedcfc88ae7a66
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2017
---
# <a name="log-alerts-in-azure-monitor---alerts-preview"></a>Alertas de log no Azure Monitor - Alertas (visualização)
Este artigo fornece detalhes de como regras de alerta nas consultas de análise funcionam nos Alertas do Azure (visualização) e descreve as diferenças entre os diversos tipos de regras de alerta de log.
No momento os Alertas do Azure (visualização) só dão suporte aos alertas de log nas consultas do [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) escritas na [nova linguagem de consulta do Log Analytics](../log-analytics/log-analytics-log-search-upgrade.md)

> [!WARNING]
> Os Alertas do Azure (visualização) - Alertas de Log atualmente não dão suporte às consultas entre espaço de trabalho ou entre aplicativos. 

## <a name="log-alert-rules"></a>Regras de alerta de log

Os alertas são criados por Alertas do Azure (visualização) que executam consultas de log automaticamente em intervalos regulares.  Se os resultados da consulta de log corresponderem a critérios específicos, um registro de alerta será criado. A regra poderá, em seguida, executar automaticamente uma ou mais ações para notificá-lo proativamente do alerta ou invocar outro processo como a execução de runbooks, usando [Grupos de ação](monitoring-action-groups.md).  Diferentes tipos de regras de alerta usam lógicas diferentes para realizar essa análise.

Regras de Alerta são definidas pelos detalhes a seguir:

- **Consulta de log**.  A consulta que é executada cada vez que a regra de alerta é acionada.  Os registros retornados por essa consulta serão usados para determinar se um alerta é criado.
- **Janela de tempo**.  Especifica o intervalo de tempo para a consulta.  A consulta retorna somente os registros que foram criados dentro desse intervalo de tempo atual.  A janela de tempo pode ser qualquer valor entre 5 minutos e 1440 minutos ou 24 horas. Por exemplo, se a janela de tempo está definida para 60 minutos e a consulta é executada às 13h15, somente os registros criados entre 12h15 e 13h15 são retornados.
- **Frequência**.  Especifica a frequência com que a consulta deve ser executada. Pode ser qualquer valor entre 5 minutos e 24 horas. Deve ser igual a ou menor que a janela de tempo.  Se o valor for maior que a janela de tempo, haverá o risco de que registros sejam perdidos.<br>Por exemplo, considere uma janela de tempo de 30 minutos e uma frequência de 60 minutos.  Se a consulta for executada à 1:00, retornará registros entre 12:30 e 1:00.  A próxima vez em que a consulta será executada é às 2:00, quando ela retornará registros entre 1:30 e 2:00.  Todos os registros criados entre 1:00 e 1:30 nunca seriam avaliados.
- **Limite**.  Os resultados da pesquisa de logs são avaliados para determinar se um alerta deve ser criado.  O limite é diferente para os diferentes tipos de regras de alerta.

Cada regra de alerta no Log Analytics é de um entre dois tipos.  Cada um desses tipos é descrito detalhadamente nas seções a seguir.

- **[Número de resultados](#number-of-results-alert-rules)**. Alerta único criado quando o número de registros retornados pela pesquisa de logs excedeu um número especificado.
- **[Medida métrica](#metric-measurement-alert-rules)**.  Alerta criado para cada objeto nos resultados da pesquisa de logs com valores que excedem o limite especificado.

As diferenças entre tipos de regra de alerta são conforme descrito a seguir.

- A regra de alerta **Número de resultados** sempre criará um único tempo de alerta, enquanto a regra de alerta **Medição métrica** criará um alerta para cada objeto que exceder o limite.
- Regras de alerta de **Número de resultados** criarão um alerta quando o limite for excedido uma única vez. Regras de alerta de **Medição métrica** podem criar um alerta quando o limite é excedido um determinado número de vezes em um intervalo de tempo específico.

## <a name="number-of-results-alert-rules"></a>Regras de alerta de Número de resultados
Regras de alerta de **Número de resultados** criam um único alerta quando o número de registros retornados pela consulta de pesquisa excede o limite especificado.

**Threshold**: O limite para uma regra de alerta de **Número de resultados** é maior ou menor que um valor específico.  Se o número de registros retornados pela pesquisa de logs corresponderem a esses critérios, um alerta será criado.

### <a name="scenarios"></a>Cenários

#### <a name="events"></a>Eventos
Esse tipo de regra de alerta é ideal para trabalhar com eventos como logs de eventos do Windows, Syslog e logs Personalizados.  Pode ser útil criar um alerta quando um evento de erro específico é criado ou quando vários eventos de erros são criados dentro de uma janela de tempo específica.

Para alertar quanto a um único evento, defina o número de resultados para maior que 0 e tanto a frequência quanto a janela de tempo para cinco minutos.  Isso executa a consulta a cada cinco minutos e verifica a ocorrência de um único evento que foi criado desde a última vez em que a consulta foi executada.  Uma frequência maior pode atrasar o tempo entre o evento que está sendo coletado e o alerta criado.

Alguns aplicativos podem registrar um erro ocasional que não necessariamente gerará um alerta.  Por exemplo, o aplicativo pode repetir o processo que criou o evento de erro e depois ter êxito na próxima vez.  Nesse caso, não convém criar o alerta, a menos que vários eventos sejam criados dentro de uma janela de tempo específica.  

Em alguns casos, pode ser útil criar um alerta na ausência de um evento.  Por exemplo, um processo pode registrar eventos regulares para indicar que ele está funcionando corretamente.  Se ele não registrar um desses eventos dentro de uma janela de tempo específica, um alerta deverá ser criado.  Nesse caso você definiria o limite como **menos de 1**.

## <a name="metric-measurement-alert-rules"></a>Regras de alerta com medição métrica

Regras de alerta de **Medição métrica** criam um alerta para cada objeto em uma consulta com um valor que excede um limite especificado.  Eles têm as diferenças distintas a seguir em relação às regras de alerta de **Número de resultados**.

**Função de agregação**: Determina o cálculo que será realizado e, potencialmente, um campo numérico a agregar.  Por exemplo, **count()** retornará o número de registros na consulta e **avg(CounterValue)** retornará a média do campo CounterValue durante o intervalo.

**Campo de grupo**: Um registro com um valor agregado será criado para cada instância do campo e um alerta pode ser gerado para cada um deles.  Por exemplo, se você quisesse gerar um alerta para cada computador, usaria **by Computer**   

**Intervalo**: Define o intervalo de tempo durante o qual os dados são agregados.  Por exemplo, se você especificasse **cinco minutos**, um registro seria criado para cada instância do campo de grupo agregada em intervalos de 5 minutos durante a janela de tempo especificada para o alerta.

**Limite**: O limite para regras de alerta de Medição métrica é definido por um valor de agregação e algumas violações.  Se qualquer ponto de dados na pesquisa de logs exceder esse valor, ele será considerado uma violação.  Se o número de violações de qualquer objeto nos resultados exceder o valor especificado, um alerta será criado para esse objeto.

#### <a name="example"></a>Exemplo
Considere um cenário em que você deseje um alerta se qualquer computador exceda 90% de utilização do processador por três vezes em 30 minutos.  Você criaria uma regra de alerta com os detalhes a seguir:  

**Consulta:** Perf | where ObjectName == "Processor" and CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5 m), Computer<br>
**Janela de tempo:** 30 minutos<br>
**Frequência de alerta:** cinco minutos<br>
**Valor de agregação:** maior que 90<br>
**Disparar alerta com base em:** total de violações maior que cinco<br>

A consulta criaria um valor médio para cada computador em intervalos de cinco minutos.  Essa consulta seria executada cada cinco minutos para os dados coletados durante os 30 minutos anteriores.  Abaixo, são mostrados dados de exemplo para três computadores.

![Resultados da consulta de exemplo](../log-analytics/media/log-analytics-alerts/metrics-measurement-sample-graph.png)

Neste exemplo, alertas separados seriam criados para srv02 e srv03, já que eles violaram o limite de 90% três vezes durante a janela de tempo.  Se **Disparar alerta com base em:** fosse alterado para **Consecutivas**, o único alerta criado seria para srv03, já que ele violou o limite em três amostras consecutivas.


## <a name="next-steps"></a>Próximas etapas
* [Obtenha uma visão geral dos Alertas do Azure (visualização)](monitoring-overview-unified-alerts.md) 
* Saiba mais sobre [Como usar os alertas do Azure (visualização)](monitor-alerts-unified-usage.md)
* Saiba mais sobre o [Log Analytics](../log-analytics/log-analytics-overview.md).    
