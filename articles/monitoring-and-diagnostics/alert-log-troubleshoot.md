---
title: Solucionar problemas de alertas de log no Azure Monitor
description: Problemas comuns, erros e resoluções para as regras de alerta de log no Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 5572c80879584e7f6df650263ae455a134ee4088
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283590"
---
# <a name="troubleshooting-log-alerts-in-azure-monitor"></a>Solucionar problemas de alertas de log no Azure Monitor  

## <a name="overview"></a>Visão geral
Este artigo mostra a você como lidar com problemas comuns durante a configuração de alertas de log no Azure Monitor. Além disso, fornece soluções para as perguntas frequentes sobre a funcionalidade ou configuração dos alertas de log. O termo **Alertas de Log** para descrever alertas em que o sinal é baseado em consulta personalizada no [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) ou no [Application Insights](../application-insights/app-insights-analytics.md). Saiba mais sobre a funcionalidade, a terminologia e o tipos em [Alertas de log – visão geral](monitor-alerts-unified-log.md).

> [!NOTE]
> Este artigo não considera casos nos quais a regra de alerta aparece como acionada no portal do Azure e a notificação é enviada por meio de Grupos de Ação associados. Para esses casos, consulte os detalhes no artigo sobre [Grupos de Ação](monitoring-action-groups.md).


## <a name="log-alert-didnt-fire"></a>Alerta de log não acionado

Veja a seguir alguns motivos comuns por que uma [regra de alerta de log configurada no Azure Monitor](alert-log.md) não é disparada quando exibida nos [Alertas do Azure](monitoring-alerts-managing-alert-states.md), quando não isso não era esperado. 

### <a name="data-ingestion-time-for-logs"></a>Hora da ingestão de dados para logs
O alerta de log funciona por meio da execução periódica da consulta fornecida pelo cliente com base no [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) ou no [Application Insights](../application-insights/app-insights-analytics.md). Ambos são capacitados pelo Analytics, que processa grandes quantidades de dados de log e fornece a mesma funcionalidade. Como o serviço do Log Analytics envolve o processamento de muitos terabytes de dados de milhares de clientes e de fontes do mundo todo, ele é suscetível a um tempo de espera. Para saber mais, confira [Tempo de ingestão de dados no Log Analytics](../log-analytics/log-analytics-data-ingestion-time.md).

Para superar o atraso da ingestão de dados que pode ocorrer em logs do Log Analytics ou do Application Insights; o alerta do log espera e tenta novamente após algum tempo quando encontra dados ainda não ingeridos durante o período do alerta. Alertas de log tem um tempo de espera cada vez maior, para ter certeza de que esperaremos o tempo necessário para a ingestão dos dados pelo Log Analytics. Portanto, se os logs consultados por sua regra de alerta de log forem afetados por atrasos de ingestão, o alerta de log disparará somente após os dados estarem disponíveis na pós-ingestão do Log Analytics e após o intervalo de tempo exponencial devido ao serviço de alerta de log repetir várias vezes nesse ínterim.

### <a name="incorrect-time-period-configured"></a>Período de tempo incorreto configurado
Conforme descrito no artigo sobre [terminologia para alertas de log](monitor-alerts-unified-log.md#log-search-alert-rule---definition-and-types), o período indicado na configuração especifica o intervalo de tempo para a consulta. A consulta retorna somente os registros que foram criados dentro desse intervalo de tempo. O período de tempo restringe os dados buscados para consulta de log para evitar abusos e contorna qualquer comando de tempo (como atrás) usados na consulta de log. 
*Por exemplo, se o período de tempo está definido para 60 minutos e a consulta é executada às 13h15, somente os registros criados entre 12h15 e 13h15 são retornados para executar a consulta de log. Agora, se a consulta de log usa a hora como o comando de hora como ago (1d), a consulta de log seria executada somente para dados entre 12h15 e 13h15 - como se os dados existissem somente nos últimos 60 minutos. E não por sete dias de dados conforme especificado na consulta de log.*

Com base em sua lógica de consulta, verifique se o período de tempo apropriado foi fornecido na configuração. No exemplo mencionado anteriormente, se a consulta de log usa ago (1d), conforme mostrado com o marcador Verde, o período de tempo deverá ser definido como 24 horas ou 1.440 minutos (conforme indicado em Vermelho), para garantir que a consulta fornecida seja executada corretamente como o previsto.
    ![Período de tempo](./media/monitor-alerts-unified/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>A opção Suprimir Alertas está definida
Conforme descrito na etapa 8 do artigo sobre [Criar uma regra de alerta de log no portal do Azure](alert-log.md#managing-log-alerts-from-the-azure-portal), o alerta de log fornece uma opção de configuração da supressão automática da regra de alerta e para impedir a notificação/gatilho por um período determinado. A opção Suprimir Alertas fará com que o alerta de log execute sem disparar o grupo de ação durante o tempo especificado na opção **Suprimir Alertas** e, portanto, o usuário pode sentir que esse alerta não foi acionado, porém, na realidade, ele foi suprimido conforme configurado.
    ![Suprimir alertas](./media/monitor-alerts-unified/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>A regra de alerta com medição métrica está incorreta
O tipo de medição métrica da regra de alerta de log é o subtipo dos alertas de log, que tem recursos especiais mas, por sua vez, emprega restrição sobre a sintaxe de consulta do alerta. A regra de alerta do log de medição métrica exige que a saída da consulta do alerta forneça uma série de tempo de métrica - uma tabela com períodos distintos de tamanhos iguais, juntamente com os valores correspondentes de AggregatedValue computados. Além disso, os usuários podem escolher ter variáveis adicionais na tabela, junto com AggregatedValue, como Computador, Nó etc. usando os dados na tabela que podem ser classificados.

Por exemplo, vamos supor que a regra de alerta de log de medição métrica tenha sido configurada como:
- a consulta era: `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- período de 6 horas
- limite de 50
- lógica de alerta de três violações consecutivas
- Aggregate Upon escolhido como $table

Como o comando inclui summarize … by e forneceu duas variáveis: timestamp e $table; o serviço de alerta escolherá $table para "Aggregate Upon" – Ele classifica a tabela resultante pelo campo $table, conforme mostrado abaixo, e analisa os vários AggregatedValue em busca do tipo de tabela (como availabilityResults) para ver se houve violações consecutivas de 3 ou mais.

   ![Execução de consulta de medição métrica com vários valores](./media/monitor-alerts-unified/LogMMQuery.png)

Como "Aggregate Upon" é $table – os dados são classificados na coluna $table (como em VERMELHO); em seguida, agrupamos e procuramos por tipos de campo de "Aggregate Upon" (ou seja) $table – por exemplo: valores para availabilityResults serão considerados como uma plotagem/entidade (conforme realçado em Laranja). Nessa plotagem/entidade de valor – o serviço de alerta procura três violações consecutivas (como mostrado em Verde), para as quais o alerta será disparado para o valor de tabela 'availabilityResults'. Da mesma forma, se para qualquer outro valor de $table três violações consecutivas forem vistas - outra notificação de alerta será disparada para o mesmo; com o serviço de alerta classificando automaticamente os valores em uma plotagem/entidade (como em Laranja) por vez.

Agora suponha que a regra de alerta de log de medição métrica tenha sido modificada e a consulta fosse `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)` com o restante da configuração igual à anterior, incluindo a lógica de alerta para três violações consecutivas. A opção "Aggregate Upon" nesse caso será por padrão: carimbo de hora. Já que apenas um valor é fornecido na consulta para summarize... by (ou seja) timestamp; semelhante ao exemplo anterior, no final da execução, a saída seria igual à ilustrada abaixo. 

   ![Execução de consulta de medição métrica com valor singular](./media/monitor-alerts-unified/LogMMtimestamp.png)

Como "Aggregate Upon" é timestamp – os dados são classificados na coluna de carimbo de hora (como em VERMELHO); em seguida, agrupamos por data/hora – por exemplo: os valores para `2018-10-17T06:00:00Z` serão considerados como uma plotagem/entidade (conforme realçado em Laranja). Nessa plotagem/entidade de valor – o serviço de alerta não encontrará violações consecutivas (pois cada valor de carimbo de data e hora tem apenas uma entrada) e, portanto, nunca será disparado. Portanto, nesse caso, o usuário precisa -
- Adicionar uma variável fictícia ou uma variável existente (como $table) para classificar corretamente usando o campo "Aggregate Upon" configurado
- (Ou) reconfigurar a regra de alerta para usar a lógica de alerta com base no *total de violações*, conforme adequado
 
## <a name="log-alert-fired-unnecessarily"></a>Alerta de log disparado desnecessariamente
Veja a seguir alguns motivos comuns por que uma [regra de alerta de log configurada no Azure Monitor](alert-log.md) pode disparar quando exibida nos [Alertas do Azure](monitoring-alerts-managing-alert-states.md), quando não era essa a expectativa.

### <a name="alert-triggered-by-partial-data"></a>Alerta disparado por dados parciais
As análises que capacitam o Log Analytics e o Application Insights estão sujeitas a atrasos de ingestão e processamento; por isso, no momento da execução da consulta de alerta de log especificada, talvez nenhum dado esteja disponível ou apenas alguns dados estejam disponíveis. Para saber mais, confira [Tempo de ingestão de dados no Log Analytics](../log-analytics/log-analytics-data-ingestion-time.md).

Dependendo de como a regra de alerta foi configurada, pode haver acionamentos incorretos caso não haja dados, ou só dados parciais, nos Logs no momento da execução do alerta. Nesses casos, recomendamos a alteração da consulta do alerta ou da configuração. *Por exemplo, se a regra de alerta do log estiver configurada para disparar quando o número de resultados da consulta de análise for menor do que (por exemplo) 5; quando não houver dados (zero registro) ou resultados parciais (um registro) a regra de alerta será disparada. Considerando que, após o atraso de ingestão, quando a mesma consulta é executada no Analytics, a consulta com dados completos poderá fornecer resultados como 10 registros.*

### <a name="alert-query-output-misunderstood"></a>Saída de consulta do alerta incompreendida
Para os alertas de log, a lógica para o alerta é fornecida pelo usuário por meio da consulta de análise. A consulta de análise fornecida pode empregar várias funções Matemáticas e de Big Data para criar construções específicas. O serviço de alerta executará a consulta fornecida pelo cliente em intervalos especificados, com os dados para o período de tempo especificado; o serviço de alerta faz alterações sutis na consulta fornecida com base no tipo de alerta escolhido; e o mesmo pode ser visto na seção "Consulta a ser executada" na tela Configuração de lógica de sinal, conforme ilustrado abaixo: ![Consulta a ser executada](./media/monitor-alerts-unified/LogAlertPreview.png)
 
O que aparece na seção **Consulta a ser executada** é o serviço de alerta de log que será executado; o usuário pode executar a consulta indicada, bem como o intervalo de tempo, por meio do [Portal do Analytics](../log-analytics/log-analytics-log-search-portals.md) ou da [API do Analytics](https://docs.microsoft.com/rest/api/loganalytics/), se o usuário quiser entender, antes da criação do alerta, qual pode ser a saída da consulta do alerta.
 
## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre os [Alertas de log nos alertas do Azure](monitor-alerts-unified-log.md)
* Saiba mais sobre o [Application Insights](../application-insights/app-insights-analytics.md)
* Saiba mais sobre o [Log Analytics](../log-analytics/log-analytics-queries.md). 