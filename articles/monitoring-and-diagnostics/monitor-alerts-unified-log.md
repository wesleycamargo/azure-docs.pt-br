---
title: Alertas de log no Azure Monitor
description: Acione emails, notificações, chame URLs de sites (webhooks) ou automação quando as condições de consulta analítica especificadas forem atendidas para os Alertas do Azure.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: f36f05789424cfd3213525dd501333f852a0d9c2
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971713"
---
# <a name="log-alerts-in-azure-monitor---alerts"></a>Alertas de log no Azure Monitor - Alertas 
Este artigo fornece detalhes de alertas de Log, que são um dos tipos de alertas de suporte no novo [Alertas do Azure](monitoring-overview-unified-alerts.md) e permitem que os usuários usem a plataforma de análise do Azure como base para alertas. Para obter detalhes de Alertas de Métrica usando Logs, veja [Alertas de métrica quase em tempo real](monitoring-near-real-time-metric-alerts.md)


O Alerta de Log consiste em regras de Pesquisa de Log criadas para o [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) ou o [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events)


## <a name="log-search-alert-rule---definition-and-types"></a>Regra de alerta de pesquisa de log - definição e tipos

As regras de pesquisa de log são criadas pelos Alertas do Azure para executar consultas de log automaticamente em intervalos regulares.  Se os resultados da consulta de log corresponderem a critérios específicos, um registro de alerta será criado. A regra pode executar automaticamente uma ou mais ações usando os [Grupos de Ação](monitoring-action-groups.md). 

As regras de pesquisa de log são definidas pelos detalhes a seguir:
- **Consulta de log**.  A consulta que é executada cada vez que a regra de alerta é acionada.  Os registros retornados por essa consulta serão usados para determinar se um alerta é criado. A consulta do *Application Insights no Azure* também pode incluir [chamadas entre aplicativos](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery), desde que o usuário tenha direitos de acesso aos aplicativos externos. 

    > [!IMPORTANT]
    > O suporte de [consultas entre aplicativos para o Application Insights](https://dev.applicationinsights.io/ai/documentation/2-Using-the-API/CrossResourceQuery) está em versão prévia - a funcionalidade está limitada a dois ou mais aplicativos, e a experiência do usuário está sujeita a alterações. O uso de [consulta entre espaços de trabalho ](https://dev.loganalytics.io/oms/documentation/3-Using-the-API/CrossResourceQuery) e [consulta de recursos cruzados para o Log Analytics](../log-analytics/log-analytics-cross-workspace-search.md) atualmente **não tem suporte** nos alertas do Azure.

- **Período de tempo**.  Especifica o intervalo de tempo para a consulta. A consulta retorna somente os registros que foram criados dentro desse intervalo de tempo atual. O período de tempo restringe os dados buscados para consulta de log para evitar abusos e contorna qualquer comando de tempo (como atrás) usados na consulta de log. <br>*Por exemplo, se o período de tempo está definido para 60 minutos e a consulta é executada às 13h15, somente os registros criados entre 12h15 e 13h15 são retornados para executar a consulta de log. Agora, se a consulta de log usa a hora como o comando atrás (7d), a consulta de log deve ser executada somente para dados entre 12h15 e 13h15 - como se os dados existissem para somente os últimos 60 minutos. E não por sete dias de dados conforme especificado na consulta de log.*
- **Frequência**.  Especifica a frequência com que a consulta deve ser executada. Pode ser qualquer valor entre 5 minutos e 24 horas. Deve ser igual a ou menor que o período de tempo.  Se o valor for maior que o período de tempo, haverá o risco de que registros sejam perdidos.<br>*Por exemplo, considere um período de tempo de 30 minutos e uma frequência de 60 minutos.  Se a consulta for executada à 1:00, retornará registros entre 12:30 e 1:00.  A próxima vez em que a consulta será executada é às 2:00, quando ela retornará registros entre 1:30 e 2:00.  Todos os registros criados entre 13h00 e 13h30 nunca seriam avaliados.*
- **Limite**.  Os resultados da pesquisa de logs são avaliados para determinar se um alerta deve ser criado.  O limite é diferente para os diferentes tipos de regras de alerta da pesquisa de logs.

As regras de pesquisa de logs para o [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) ou o [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events) podem ser de dois tipos. Cada um desses tipos é descrito detalhadamente nas seções a seguir.

- **[Número de resultados](#number-of-results-alert-rules)**. Alerta único criado quando o número de registros retornados pela pesquisa de logs excedeu um número especificado.
- **[Medida métrica](#metric-measurement-alert-rules)**.  Alerta criado para cada objeto nos resultados da pesquisa de logs com valores que excedem o limite especificado.

As diferenças entre tipos de regra de alerta são conforme descrito a seguir.

- A regra de alerta *Número de resultados* sempre cria um único tempo de alerta, enquanto a regra de alerta *Medição métrica* cria um alerta para cada objeto que exceder o limite.
- Regras de alerta de *Número de resultados* criarão um alerta quando o limite for excedido uma única vez. Regras de alerta de *Medição métrica* podem criar um alerta quando o limite é excedido um determinado número de vezes em um intervalo de tempo específico.

### <a name="number-of-results-alert-rules"></a>Regras de alerta de Número de resultados
Regras de alerta de **Número de resultados** criam um único alerta quando o número de registros retornados pela consulta de pesquisa excede o limite especificado. Esse tipo de regra de alerta é ideal para trabalhar com eventos como logs de eventos do Windows, Syslog, WebApp Response e Logs Personalizados.  Pode ser útil criar um alerta quando um evento de erro específico é criado ou quando vários eventos de erros são criados dentro de um período de tempo específico.

**Threshold**: O limite para uma regra de alerta de número de resultados é maior ou menor que um valor específico.  Se o número de registros retornados pela pesquisa de logs corresponderem a esses critérios, um alerta será criado.

Para alertar sobre um único evento, defina o número de resultados para maior que 0 e verifique a ocorrência de um único evento que foi criado desde a última vez em que a consulta foi executada. Alguns aplicativos podem registrar um erro ocasional que não necessariamente gerará um alerta.  Por exemplo, o aplicativo pode repetir o processo que criou o evento de erro e depois ter êxito na próxima vez.  Nesse caso, não convém criar o alerta, a menos que vários eventos sejam criados dentro de um período de tempo específico.  

Em alguns casos, pode ser útil criar um alerta na ausência de um evento.  Por exemplo, um processo pode registrar eventos regulares para indicar que ele está funcionando corretamente.  Se ele não registrar um desses eventos dentro de um período de tempo específico, um alerta deverá ser criado.  Nesse caso você definiria o limite como **menos de 1**.

#### <a name="example"></a>Exemplo
Considere um cenário onde você deseja saber quando seu aplicativo baseado na web fornece uma resposta para os usuários com o código de 500 (ou seja) erro interno do servidor. Você criaria uma regra de alerta com os detalhes a seguir:  
- **Consulta:** solicitações | onde resultCode = = "500"<br>
- **Período de tempo:** 30 minutos<br>
- **Frequência de alerta:** cinco minutos<br>
- **Valor de limite:** maior que 0<br>

Alerta deve executar a consulta a cada 5 minutos, com 30 minutos de dados - procurar qualquer registro onde o código de resultado foi 500. Se mesmo um registro desse tipo é encontrado, ele aciona o alerta e dispara a ação configurada.

### <a name="metric-measurement-alert-rules"></a>Regras de alerta com medição métrica

- Regras de alerta de **Medição métrica** criam um alerta para cada objeto em uma consulta com um valor que excede um limite especificado.  Eles têm as diferenças distintas a seguir em relação às regras de alerta de **Número de resultados**.
- **Função de agregação**: Determina o cálculo que será realizado e, potencialmente, um campo numérico a agregar.  Por exemplo, **count()** retornará o número de registros na consulta e **avg(CounterValue)** retornará a média do campo CounterValue durante o intervalo. Função de agregação na consulta deve ser chamada/nomeada: AggregatedValue e fornecer um valor numérico. 
- **Campo de grupo**: Um registro com um valor agregado será criado para cada instância do campo e um alerta pode ser gerado para cada um deles.  Por exemplo, se você quisesse gerar um alerta para cada computador, usaria **by Computer** 

    > [!NOTE]
    > Para regras de alerta de medição métrica baseadas no Application Insights, você pode especificar o campo para agrupamento dos dados. Para fazer isso, use a opção **Agregação** na definição da regra.   
    
- **Intervalo**: Define o intervalo de tempo durante o qual os dados são agregados.  Por exemplo, se você especificasse **cinco minutos**, um registro seria criado para cada instância do campo de grupo agregada em intervalos de 5 minutos durante o período de tempo especificado para o alerta.

    > [!NOTE]
    > A função bin deve ser usada na consulta para especificar o intervalo. Como bin() pode resultar em intervalos de tempo diferentes, o Alerta converterá automaticamente o comando bin em um comando bin_at com o tempo de execução apropriado, a fim de garantir resultados com um ponto fixo
    
- **Limite**: O limite para regras de alerta de Medição métrica é definido por um valor de agregação e algumas violações.  Se qualquer ponto de dados na pesquisa de logs exceder esse valor, ele será considerado uma violação.  Se o número de violações de qualquer objeto nos resultados exceder o valor especificado, um alerta será criado para esse objeto.

#### <a name="example"></a>Exemplo
Considere um cenário em que você deseje um alerta se qualquer computador exceda 90% de utilização do processador por três vezes em 30 minutos.  Você criaria uma regra de alerta com os detalhes a seguir:  

- **Consulta:** Perf | where ObjectName == "Processor" and CounterName == "% Processor Time" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5 m), Computer<br>
- **Período de tempo:** 30 minutos<br>
- **Frequência de alerta:** cinco minutos<br>
- **Valor de agregação:** maior que 90<br>
- **Disparar alerta com base em:** total de violações maior que dois<br>

A consulta criaria um valor médio para cada computador em intervalos de cinco minutos.  Essa consulta seria executada cada cinco minutos para os dados coletados durante os 30 minutos anteriores.  Abaixo, são mostrados dados de exemplo para três computadores.

![Resultados da consulta de exemplo](./media/monitor-alerts-unified/metrics-measurement-sample-graph.png)

Neste exemplo, alertas separados seriam criados para srv02 e srv03, já que eles violaram o limite de 90% três vezes durante o período de tempo.  Se **Disparar alerta com base em:** fosse alterado para **Consecutivas**, o único alerta criado seria para srv03, já que ele violou o limite em três amostras consecutivas.


## <a name="log-search-alert-rule---creation-and-modification"></a>Regra de alerta de pesquisa de logs - criação e modificação

O alerta de log, bem como sua respectiva regra de alerta de pesquisa de logs de pode ser exibida, criada ou modificada em:
- Portal do Azure
- APIs REST (inclusive por meio do PowerShell)
- Modelos do Azure Resource Manager

### <a name="azure-portal"></a>Portal do Azure
Desde o lançamento dos [novos alertas do Azure](monitoring-overview-unified-alerts.md), agora os usuários podem gerenciar todos os tipos de alertas no portal do Azure de um único local e em etapas semelhantes. Saiba mais sobre como [usar os novos Alertas do Azure](monitor-alerts-unified-usage.md).

Além disso, os usuários podem aperfeiçoar suas consultas na plataforma Analytics de sua escolha no Azure e, em seguida, *importá-las para uso em alertas, salvando a consulta*. Etapas a serem executadas:
- *Para o Application Insights*: vá para o portal do Analytics, valide a consulta e seus resultados. Em seguida, salve com um nome exclusivo para no *Consultas compartilhadas*.
- *Para o Log Analytics*: vá para a Pesquisa de Logs, valide a consulta e seus resultados. Em seguida, salve com um nome exclusivo em qualquer categoria.

Ao [criar um alerta de log no Alertas](monitor-alerts-unified-usage.md), você vê a consulta salva listada como tipo de sinal **Log (consulta salva)**; conforme ilustrado no exemplo abaixo: ![Consulta Salva, importado para Alertas](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog-new.png)

> [!NOTE]
> Usar o **Log (consulta salva)** resulta em uma importação para Alertas. Portanto, qualquer alteração feita no Analytics não será refletida nas regras de alerta da pesquisa de logs e vice-versa.

### <a name="rest-apis"></a>APIs REST
As APIs fornecidas para os alertas de logs são RESTful e podem ser acessadas por meio da API REST do Azure Resource Manager. Portanto, elas podem ser acessadas via PowerShell, bem como por outras opções para aproveitar as APIs.

Para obter mais detalhes, assim como exemplos sobre como usar a API REST, consulte:
- [API REST de alerta do Log Analytics](../log-analytics/log-analytics-api-alerts.md) - para criar e gerenciar regras de alerta de pesquisa de log para o Log Analytics do Azure
- [Azure Monitor agendou API REST de regras de consulta](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) - para criar e gerenciar regras de alerta de pesquisa de log para o Azure Application Insights

### <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager
Os usuários também podem usar a flexibilidade fornecida pelo [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) para criar e atualizar recursos - com a finalidade de criar ou atualizar alertas de Log.

Para obter mais detalhes, assim como exemplos sobre como usar os modelos do Resource Manager, consulte:
- [Gerenciamento de alertas e pesquisa salvo](monitor-alerts-unified-log-template.md#managing-log-alert-on-log-analytics) para alertas de log com base Azure Log Analytics do Azure
- [Agendada regra de consulta](monitor-alerts-unified-log-template.md#managing-log-alert-on-application-insights) para alertas de log com base Application Insights no Azure
 

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre os [alertas de log no Azure](monitor-alerts-unified-log-webhook.md).
* Saiba mais sobre os novos [Alertas do Azure](monitoring-overview-unified-alerts.md).
* Saiba mais sobre o [Application Insights](../application-insights/app-insights-analytics.md).
* Saiba mais sobre o [Log Analytics](../log-analytics/log-analytics-overview.md).    
