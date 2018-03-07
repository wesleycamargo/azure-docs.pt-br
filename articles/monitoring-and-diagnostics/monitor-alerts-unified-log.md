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
ms.date: 02/02/2018
ms.author: vinagara
ms.openlocfilehash: 438776e7f0885dbdb0d66ccdd18d854e14beb299
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/24/2018
---
# <a name="log-alerts-in-azure-monitor---alerts-preview"></a>Alertas de log no Azure Monitor - Alertas (visualização)
Este artigo fornece detalhes de como regras de alerta nas consultas de análise funcionam nos Alertas do Azure (visualização) e descreve as diferenças entre os diversos tipos de regras de alerta de log.

No momento, Alertas do Azure (Visualização) dá suporte a alertas de log em consultas do [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) e do [Application Insights](../application-insights/app-insights-cloudservices.md#view-azure-diagnostic-events).

> [!WARNING]

> Atualmente, o alerta de log no Alertas do Azure (Visualização) não dá suporte às consultas entre espaço de trabalho ou entre aplicativos.

Além disso, os usuários podem aperfeiçoar suas consultas na plataforma Analytics de sua escolha no Azure e, em seguida, *importá-las para uso em alertas (visualização), salvando a consulta*. Etapas a serem executadas:
- Para o Application Insights: vá para o portal do Analytics, valide a consulta e seus resultados. Em seguida, salve com um nome exclusivo para no *Consultas compartilhadas*.
- Para o Log Analytics: vá para o Log Search, valide a consulta e seus resultados. Em seguida, salve com um nome exclusivo em qualquer categoria.

Ao [criar um alerta de log no Alertas (visualização)](monitor-alerts-unified-usage.md), você vê a consulta salva listada como tipo de sinal **Log (consulta salva)**; conforme ilustrado no exemplo abaixo: ![Consulta Salva, importado para Alertas](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog-new.png)

> [!NOTE]
> Usar o **Log (consulta salva)** resulta em uma importação para Alertas. Portanto, qualquer alteração feita no Analytics não será refletiva em regras de alerta salvas e vice-versa.

## <a name="log-alert-rules"></a>Regras de alerta de log

Os alertas são criados por Alertas do Azure (visualização) que executam consultas de log automaticamente em intervalos regulares.  Se os resultados da consulta de log corresponderem a critérios específicos, um registro de alerta será criado. A regra poderá, em seguida, executar automaticamente uma ou mais ações para notificá-lo proativamente do alerta ou invocar outro processo como enviar dados para aplicação externa usando [json baseado em webhook](monitor-alerts-unified-log-webhook.md), usando [Grupos de ação](monitoring-action-groups.md). Diferentes tipos de regras de alerta usam lógicas diferentes para realizar essa análise.

Regras de Alerta são definidas pelos detalhes a seguir:

- **Consulta de log**.  A consulta que é executada cada vez que a regra de alerta é acionada.  Os registros retornados por essa consulta serão usados para determinar se um alerta é criado.
- **Janela de tempo**.  Especifica o intervalo de tempo para a consulta.  A consulta retorna somente os registros que foram criados dentro desse intervalo de tempo atual.  A janela de tempo pode ser qualquer valor entre 5 minutos e 1440 minutos ou 24 horas. Por exemplo, se a janela de tempo está definida para 60 minutos e a consulta é executada às 13h15, somente os registros criados entre 12h15 e 13h15 são retornados.
- **Frequência**.  Especifica a frequência com que a consulta deve ser executada. Pode ser qualquer valor entre 5 minutos e 24 horas. Deve ser igual a ou menor que a janela de tempo.  Se o valor for maior que a janela de tempo, haverá o risco de que registros sejam perdidos.<br>Por exemplo, considere uma janela de tempo de 30 minutos e uma frequência de 60 minutos.  Se a consulta for executada à 1:00, retornará registros entre 12:30 e 1:00.  A próxima vez em que a consulta será executada é às 2:00, quando ela retornará registros entre 1:30 e 2:00.  Todos os registros criados entre 1:00 e 1:30 nunca seriam avaliados.
- **Limite**.  Os resultados da pesquisa de logs são avaliados para determinar se um alerta deve ser criado.  O limite é diferente para os diferentes tipos de regras de alerta.

Cada regra de alerta no Log Analytics é de um entre dois tipos.  Cada um desses tipos é descrito detalhadamente nas seções a seguir.

- **[Número de resultados](#number-of-results-alert-rules)**. Alerta único criado quando o número de registros retornados pela pesquisa de logs excedeu um número especificado.
- **[Medida métrica](#metric-measurement-alert-rules)**.  Alerta criado para cada objeto nos resultados da pesquisa de logs com valores que excedem o limite especificado.

As diferenças entre tipos de regra de alerta são conforme descrito a seguir.

- **A regra de alerta número de resultados sempre cria um único tempo de alerta, enquanto a regra de alerta **Medição métrica** cria um alerta para cada objeto que exceder o limite.
- Regras de alerta de **Número de resultados** criarão um alerta quando o limite for excedido uma única vez. Regras de alerta de **Medição métrica** podem criar um alerta quando o limite é excedido um determinado número de vezes em um intervalo de tempo específico.

## <a name="number-of-results-alert-rules"></a>Regras de alerta de Número de resultados
Regras de alerta de **Número de resultados** criam um único alerta quando o número de registros retornados pela consulta de pesquisa excede o limite especificado. Esse tipo de regra de alerta é ideal para trabalhar com eventos como logs de eventos do Windows, Syslog, WebApp Response e Logs Personalizados.  Pode ser útil criar um alerta quando um evento de erro específico é criado ou quando vários eventos de erros são criados dentro de uma janela de tempo específica.

**Threshold**: O limite para uma **Regra de alerta de número de resultados é maior ou menor que um valor específico.  Se o número de registros retornados pela pesquisa de logs corresponderem a esses critérios, um alerta será criado.

Para alertar sobre um único evento, defina o número de resultados para maior que 0 e verifique a ocorrência de um único evento que foi criado desde a última vez em que a consulta foi executada. Alguns aplicativos podem registrar um erro ocasional que não necessariamente gerará um alerta.  Por exemplo, o aplicativo pode repetir o processo que criou o evento de erro e depois ter êxito na próxima vez.  Nesse caso, não convém criar o alerta, a menos que vários eventos sejam criados dentro de uma janela de tempo específica.  

Em alguns casos, pode ser útil criar um alerta na ausência de um evento.  Por exemplo, um processo pode registrar eventos regulares para indicar que ele está funcionando corretamente.  Se ele não registrar um desses eventos dentro de uma janela de tempo específica, um alerta deverá ser criado.  Nesse caso você definiria o limite como **menos de 1**.

### <a name="example"></a>Exemplo
Considere um cenário onde você deseja saber quando seu aplicativo baseado na web fornece uma resposta para os usuários com o código de 500 (ou seja) erro interno do servidor. Você criaria uma regra de alerta com os detalhes a seguir:  
**Consulta:** solicitações | onde resultCode = = "500"<br>
**Janela de tempo:** 30 minutos<br>
**Frequência de alerta:** cinco minutos<br>
**Valor de limite:** maior que 0<br>

Alerta deve executar a consulta a cada 5 minutos, com 30 minutos de dados - procurar qualquer registro onde o código de resultado foi 500. Se for encontrado um registro existente, ele aciona o alerta e o gatilho de ação configurada.

## <a name="metric-measurement-alert-rules"></a>Regras de alerta com medição métrica

Regras de alerta de **Medição métrica** criam um alerta para cada objeto em uma consulta com um valor que excede um limite especificado.  Eles têm as diferenças distintas a seguir em relação às regras de alerta de **Número de resultados**.

**Função de agregação**: Determina o cálculo que será realizado e, potencialmente, um campo numérico a agregar.  Por exemplo, **count()** retornará o número de registros na consulta e **avg(CounterValue)** retornará a média do campo CounterValue durante o intervalo.

> [!NOTE]

> Função de agregação na consulta deve ser chamada/nomeada: AggregatedValue e fornecer um valor numérico. 


**Campo de grupo**: Um registro com um valor agregado será criado para cada instância do campo e um alerta pode ser gerado para cada um deles.  Por exemplo, se você quisesse gerar um alerta para cada computador, usaria **by Computer**   

> [!NOTE]

> Para regras de alerta de medição métrica baseadas no Application Insights, você pode especificar o campo para agrupamento dos dados. Para fazer isso, use a opção **Agregação** na definição da regra.   

**Intervalo**: Define o intervalo de tempo durante o qual os dados são agregados.  Por exemplo, se você especificasse **cinco minutos**, um registro seria criado para cada instância do campo de grupo agregada em intervalos de 5 minutos durante a janela de tempo especificada para o alerta.
> [!NOTE]
> Função bin deve ser usada na consulta. Também se os intervalos de tempo diferentes são produzidos para janela de tempo pelo uso da função Bin - Alerta, em vez disso use a função bin_at para garantir que há um ponto fixo

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
* Entender [Ações de Webhook para alertas de log](monitor-alerts-unified-log-webhook.md)
* [Obtenha uma visão geral dos Alertas do Azure (visualização)](monitoring-overview-unified-alerts.md)
* Saiba mais sobre [Como usar os alertas do Azure (visualização)](monitor-alerts-unified-usage.md)
* Saiba mais sobre o [Application Insights](../application-insights/app-insights-analytics.md)
* Saiba mais sobre o [Log Analytics](../log-analytics/log-analytics-overview.md).    
