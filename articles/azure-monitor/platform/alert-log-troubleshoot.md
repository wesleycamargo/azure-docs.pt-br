---
title: Solucionar problemas de alertas de log no Azure Monitor | Microsoft Docs
description: Problemas comuns, erros e resoluções para regras de alerta de log no Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 1c7712fc2ce55a3d22995bb119a9ee485a064903
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683402"
---
# <a name="troubleshoot-log-alerts-in-azure-monitor"></a>Solucionar problemas de alertas de log no Azure Monitor  

Este artigo mostra como resolver problemas comuns que podem ocorrer quando você estiver configurando alertas de log no Azure Monitor. Ele também fornece soluções para problemas comuns com funcionalidade ou configuração de alertas de log. 

O termo *alertas de log* descreve os alertas que incêndio com base em uma consulta de log em um [espaço de trabalho do Log Analytics do Azure](../learn/tutorial-viewdata.md) ou no [Azure Application Insights](../../azure-monitor/app/analytics.md). Saiba mais sobre a funcionalidade, terminologia e tipos no [alertas de Log no Azure Monitor](../platform/alerts-unified-log.md).

> [!NOTE]
> Este artigo não considera os casos em que o portal do Azure mostra uma regra de alerta disparada e uma notificação não é executada por um grupo de ação associada. Para tais casos, consulte os detalhes na [criar e gerenciar grupos de ações no portal do Azure](../platform/action-groups.md).

## <a name="log-alert-didnt-fire"></a>Alerta de log não acionado

Aqui estão algumas razões comuns por que o estado de um configurado [regra de alerta de log no Azure Monitor](../platform/alerts-log.md) não mostra [como *acionado* quando esperado](../platform/alerts-managing-alert-states.md). 

### <a name="data-ingestion-time-for-logs"></a>Tempo de ingestão de dados para logs

Um alerta de log executa sua consulta com base em periodicamente [do Log Analytics](../learn/tutorial-viewdata.md) ou [Application Insights](../../azure-monitor/app/analytics.md). Como Azure Monitor processa muitos terabytes de dados de milhares de clientes de fontes variadas em todo o mundo, o serviço é suscetível a retardos variados. Para obter mais informações, consulte [tempo de ingestão de dados nos logs do Azure Monitor](../platform/data-ingestion-time.md).

Para reduzir os atrasos, o sistema aguarda e repete a consulta do alerta várias vezes se ele encontrar que os dados necessários ainda não são incluídos. O sistema tem um tempo de espera de aumento exponencial. O alerta do log é acionado somente depois que os dados estão disponíveis, portanto, o atraso pode ser devido à lentidão ingestão de dados de log. 

### <a name="incorrect-time-period-configured"></a>Período de tempo incorreto configurado

Conforme descrito no artigo sobre [terminologia para alertas do log](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types), o período de tempo indicado na configuração especifica o intervalo de tempo para a consulta. A consulta retorna somente os registros que foram criados dentro desse intervalo. 

O período de tempo restringe os dados buscados para uma consulta de log evitar o abuso e contorna a qualquer comando de tempo (como **atrás**) usado em uma consulta de log. Por exemplo, se o período de tempo estiver definido como 60 minutos e a consulta for executada às 13h15, somente registros criados entre 12h15 e 13h15 serão usados para a consulta de log. Se a consulta de log usa um comando de tempo como **atrás (1-d)**, a consulta usa dados entre 12H15 e 1:15 PM ainda apenas porque o período de tempo está definido para esse intervalo.

Verifique se o período de tempo na configuração corresponde à sua consulta. No exemplo mostrado anteriormente, se a consulta de log usa **atrás (1-d)** com o marcador de verde, o período de tempo deve ser definido para 1.440 minutos (indicados em vermelho) ou de 24 horas. Essa configuração garante que a consulta é executada conforme o esperado.

![Período de tempo](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>A opção Suprimir Alertas está definida

Conforme descrito na etapa 8, o artigo sobre [criar uma regra de alerta de log no portal do Azure](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal), os alertas de log fornecem um **Suprimir alertas** opção para suprimir a notificação e disparar ações para um período configurado de tempo. Como resultado, você pode pensar que um alerta não foi acionado. Na verdade, ele acionado, mas foi suprimido.  

![Suprimir alertas](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>A regra de alerta com medição métrica está incorreta

*Alertas do log de medição métrica* são um subtipo de alertas de log que têm recursos especiais e uma sintaxe de consulta do alerta restrito. Uma regra para um alerta do log de medição métrica exige que a saída seja uma série de métrica de tempo de consulta. Ou seja, a saída é uma tabela com períodos de tempo distintos, tamanho, juntamente com os valores agregados correspondentes. 

Você pode optar por ter variáveis adicionais na tabela junto com **AggregatedValue**. Essas variáveis podem ser usadas para classificar a tabela. 

Por exemplo, suponha que uma regra para um alerta do log de medição métrica foi configurada como:

- Consulta de `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- período de tempo de 6 horas
- limite de 50
- lógica de alerta de três violações consecutivas
- **Agregação após** escolhido como **$table**

Como o comando inclui **resumir... por** e fornece duas variáveis (**timestamp** e **$table**), o sistema escolherá **$table** para **agregação após** . O sistema classifica a tabela de resultados, o **$table** campo, conforme mostrado na seguinte captura de tela. Em seguida, ele examina o múltiplo **AggregatedValue** instâncias para cada tipo de tabela (como **availabilityResults**) para ver se houve três ou mais violações consecutivas.

![Execução de consulta de métrica de medição com vários valores](media/alert-log-troubleshoot/LogMMQuery.png)

Porque **agregação após** é definida no **$table**, os dados são classificados em um **$table** coluna (indicada em vermelho). Em seguida, podemos agrupar e procurar por tipos dos **agregação após** campo. 

Por exemplo, para **$table**, os valores para **availabilityResults** serão considerados como uma plotagem/entidade (indicada em laranja). Nesta entidade/plotagem em valor, o serviço de alerta verifica três violações consecutivas (indicadas em verde). As violações disparam um alerta para o valor de tabela **availabilityResults**. 

Da mesma forma, se três violações consecutivas acontecem por qualquer outro valor de **$table**, outra notificação de alerta é disparada para a mesma coisa. O serviço de alerta automaticamente classifica os valores em uma plotagem/entidade (indicado em laranja) por vez.

Agora, suponha que a regra de alerta do log de medição métrica foi modificada e a consulta foi `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`. O restante da configuração permanece o mesmo que antes, incluindo a lógica de alerta para três violações consecutivas. O **agregação após** nesse caso é uma opção **timestamp** por padrão. Apenas um valor é fornecido na consulta para **resumir... por** (ou seja, **timestamp**). Como no exemplo anterior, a saída no final da execução seria conforme ilustrado a seguir.

   ![Execução de consulta de métrica de medição com o valor singular](media/alert-log-troubleshoot/LogMMtimestamp.png)

Porque **agregação após** é definida no **timestamp**, os dados são classificados no **timestamp** coluna (indicada em vermelho). Em seguida, podemos Agrupar por **carimbo de hora**. Por exemplo, os valores para `2018-10-17T06:00:00Z` serão considerados como uma plotagem/entidade (indicada em laranja). Essa plotagem de valor/entidade, o serviço de alerta não encontrará nenhum violações consecutivas (porque cada **carimbo de hora** valor tem apenas uma entrada). Para o alerta nunca seja acionado. Nesse caso, o usuário deve:

- Adicionar uma variável existente ou uma variável fictícia (como **$table**) para classificar corretamente usando o **agregação após** campo.
- Reconfigure a regra de alerta para usar com base em lógica de alerta **total de violação** em vez disso.

## <a name="log-alert-fired-unnecessarily"></a>Alerta de log disparado desnecessariamente

Um configurado [regra de alerta de log no Azure Monitor](../platform/alerts-log.md) podem ser disparados inesperadamente quando você exibi-lo no [alertas do Azure](../platform/alerts-managing-alert-states.md). As seções a seguir descrevem algumas razões comuns.

### <a name="alert-triggered-by-partial-data"></a>Alerta disparado por dados parciais

O log Analytics e Application Insights estão sujeitos a atrasos de ingestão e processamento. Quando você executar uma consulta de alerta do log, você pode achar que não há dados disponíveis ou apenas alguns dados estiverem disponíveis. Para obter mais informações, consulte [tempo de ingestão de dados de Log no Azure Monitor](../platform/data-ingestion-time.md).

Dependendo de como você configurou a regra de alerta, misfiring pode acontecer se não houver nenhum dado ou dados parciais nos logs, no momento da execução de alerta. Nesses casos, aconselhamos que você altere a consulta do alerta ou a configuração. 

Por exemplo, se você configurar a regra de alerta de log para ser disparado quando o número de resultados de uma consulta do analytics é menor que 5, o alerta é disparado quando não há nenhum dado (zero registro) ou os resultados parciais (um registro). Mas, após o atraso de ingestão de dados, a mesma consulta usando dados completos pode fornecer um resultado de 10 registros.

### <a name="alert-query-output-is-misunderstood"></a>Saída de consulta do alerta é mal compreendida

Você fornece a lógica para alertas de log em uma consulta de análise. A consulta do analytics pode usar várias funções matemáticas e big data. O serviço de alerta executa sua consulta em intervalos especificados com os dados por um período de tempo especificado. O serviço de alerta faz alterações sutis para a consulta com base no tipo de alerta. Você pode exibir essa alteração na **consulta a ser executada** seção o **configurar lógica de sinal** tela:

![Consulta a ser executada](media/alert-log-troubleshoot/LogAlertPreview.png)

O **consulta a ser executada** caixa é o que o serviço de alerta de log é executado. Se você quiser entender o que a consulta do alerta de saída pode ser antes de criar o alerta, você pode executar a consulta indicada e o período de tempo por meio de [portal do Analytics](../log-query/portals.md) ou o [API de análise de](https://docs.microsoft.com/rest/api/loganalytics/).

## <a name="log-alert-was-disabled"></a>Alerta de log foi desabilitado

As seções a seguir listam alguns dos motivos por que o Azure Monitor pode desabilitar a [regra de alerta de log](../platform/alerts-log.md).

### <a name="resource-where-the-alert-was-created-no-longer-exists"></a>Recurso em que o alerta foi criado não existe

Regras de alerta do log criadas no Azure Monitor um recurso específico, como um espaço de trabalho do Log Analytics do Azure, um aplicativo do Azure Application Insights e um recurso do Azure de destino. O serviço de alerta de log, em seguida, executará uma consulta do analytics fornecida na regra para o destino especificado. Mas, após a criação de regra, os usuários geralmente vá para excluir do Azure – ou mover dentro do Azure – o destino da regra de alerta de log. Como o destino da regra de alerta não é mais válido, a execução da regra falhará.

Nesses casos, o Azure Monitor desabilita o alerta do log e garante que você não esteja cobrado desnecessariamente quando a regra não pode ser executada continuamente por um período significativo (como uma semana). Você pode descobrir o tempo exato quando o Azure Monitor desabilitado o alerta do log por meio [Log de atividades do Azure](../../azure-resource-manager/resource-group-audit.md). No Log de atividades do Azure, um evento é adicionado ao Azure Monitor desabilita a regra de alerta do log.

O seguinte exemplo de evento no Log de atividades do Azure é para uma regra de alerta foi desabilitada devido a uma falha contínua.

```json
{
    "caller": "Microsoft.Insights/ScheduledQueryRules",
    "channels": "Operation",
    "claims": {
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/ScheduledQueryRules"
    },
    "correlationId": "abcdefg-4d12-1234-4256-21233554aff",
    "description": "Alert: test-bad-alerts is disabled by the System due to : Alert has been failing consistently with the same exception for the past week",
    "eventDataId": "f123e07-bf45-1234-4565-123a123455b",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2019-03-22T04:18:22.8569543Z",
    "id": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "localizedValue": "Microsoft.Insights/ScheduledQueryRules/disable/action"
    },
    "resourceGroupName": "<Resource Group>",
    "resourceProviderName": {
        "value": "MICROSOFT.INSIGHTS",
        "localizedValue": "Microsoft Insights"
    },
    "resourceType": {
        "value": "MICROSOFT.INSIGHTS/scheduledqueryrules",
        "localizedValue": "MICROSOFT.INSIGHTS/scheduledqueryrules"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-03-22T04:18:22.8569543Z",
    "subscriptionId": "<SubscriptionId>",
    "properties": {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
        "subscriptionId": "<SubscriptionId>",
        "resourceGroup": "<ResourceGroup>",
        "eventDataId": "12e12345-12dd-1234-8e3e-12345b7a1234",
        "eventTimeStamp": "03/22/2019 04:18:22",
        "issueStartTime": "03/22/2019 04:18:22",
        "operationName": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "status": "Succeeded",
        "reason": "Alert has been failing consistently with the same exception for the past week"
    },
    "relatedEvents": []
}
```

### <a name="query-used-in-a-log-alert-is-not-valid"></a>Usado em um alerta do log de consulta não é válida

Cada regra de alerta do log criada no Azure Monitor como parte de sua configuração deve especificar uma consulta de análise que o serviço de alerta será executado periodicamente. A consulta do analytics pode ter a sintaxe correta no momento da criação da regra ou atualização. Mas, às vezes, durante um período de tempo, a consulta fornecida na regra de alerta de log pode desenvolver problemas de sintaxe e fazer com que a execução da regra falhe. Algumas razões comuns por que uma consulta do analytics fornecida em uma regra de alerta de log pode desenvolver os erros são:

- A consulta é gravada [executado em vários recursos](../log-query/cross-workspace-query.md). E um ou mais dos recursos especificados não existem mais.
- Não houve nenhum fluxo de dados para a plataforma de análise. O [execução de consulta apresentará um erro](https://dev.loganalytics.io/documentation/Using-the-API/Errors) porque não há nenhum dado para a consulta fornecida.
- As alterações no [linguagem de consulta](https://docs.microsoft.com/azure/kusto/query/) incluem um formato revisado para comandos e funções. Portanto, a consulta fornecida anteriormente em uma regra de alerta não é válida.

[O Azure Advisor](../../advisor/advisor-overview.md) avisa sobre esse comportamento. Uma recomendação é adicionada para a regra de alerta de log específico no Assistente do Azure, sob a categoria de alta disponibilidade com impacto médio e uma descrição de "Reparar sua regra de alerta de log para garantir que o monitoramento". Se uma consulta de alerta na regra de alerta de log não é corrigida após o Assistente do Azure forneceu uma recomendação por sete dias, do Azure Monitor desabilitará o alerta do log e certifique-se de que você não esteja cobrado desnecessariamente quando a regra não pode ser executada continuamente para um (período dimensionável como uma semana).

Você pode encontrar o tempo exato quando o Azure Monitor desabilitado a regra de alerta de log procurando por um evento no [Log de atividades do Azure](../../azure-resource-manager/resource-group-audit.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [alertas de log no Azure](../platform/alerts-unified-log.md).
- Saiba mais sobre o [Application Insights](../../azure-monitor/app/analytics.md).
- Saiba mais sobre [registrar consultas](../log-query/log-query-overview.md).
