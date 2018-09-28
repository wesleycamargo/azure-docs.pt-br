---
title: Criar, exibir e gerenciar alertas de log usando o Azure Monitor
description: Use o Azure Monitor para criar, exibir e gerenciar regras de alerta de log no Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 627c0ee5a2fab333b8a120867ab54ea76bc66823
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46976832"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Criar, exibir e gerenciar alertas de log usando o Azure Monitor  

## <a name="overview"></a>Visão geral
Este artigo mostra como configurar alertas usando a interface de alertas no portal do Azure. A definição de uma regra de alerta é realizada em três partes:
- Destino: recurso do Azure específico, que deve ser monitorado
- Critério: condição ou lógica específica que quando aparecer no sinal, deverá disparar uma ação
- Ação: chamada específica enviada a um destinatário de uma notificação – email, SMS, webhook etc.

O termo **Alertas de Log** para descrever alertas em que o sinal é baseado em consulta personalizada no [Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) ou no [Application Insights](../application-insights/app-insights-analytics.md). Saiba mais sobre a funcionalidade, a terminologia e o tipos em [Alertas de log – visão geral](monitor-alerts-unified-log.md).

> [!NOTE]
> Os dados de log populares do [Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) agora também estão disponíveis na plataforma de métricas no Azure Monitor. Para obter uma exibição detalhada, confira [Alerta de métrica para logs](monitoring-metric-alerts-logs.md)

## <a name="managing-log-alerts-from-the-azure-portal"></a>Gerenciando alertas de log no portal do Azure

A seguir há um guia passo a passo detalhado para usar os alertas de log por meio da interface do portal do Azure.

### <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Criar uma regra de alerta de log com o portal do Azure
1. No [portal](https://portal.azure.com/), selecione **Monitor** e na seção MONITOR, escolha **Alertas**.  
    ![Monitoramento](./media/monitor-alerts-unified/AlertsPreviewMenu.png)

1. Selecione o botão **Nova Regra de Alerta** para criar um novo alerta no Azure.
    ![Adicionar Alerta](./media/monitor-alerts-unified/AlertsPreviewOption.png)

1. A seção Criar Alerta é mostrada com três partes que consistem em: *Definir condição de alerta*, *Definir detalhes do alerta* e *Definir grupo de ação*.

    ![Criar regra](./media/monitor-alerts-unified/AlertsPreviewAdd.png)

1.  Defina a condição de alerta usando o link **Selecionar Recurso** e especificando o destino ao selecionar um recurso. Filtre escolhendo a _Assinatura_, o _Tipo de Recurso_ e o _Recurso_ necessário. 

    >[!NOTE]

    > Para a criação de um log de alerta, verifique se o sinal do **log** está disponível para o recurso selecionado antes de continuar.
    ![Selecionar recurso](./media/monitor-alerts-unified/Alert-SelectResourceLog.png)

 
1. *Alertas de Log*: verifique se **Tipo de Recurso** é uma fonte de análise como *Log Analytics* ou *Application Insights* e se o tipo de sinal é **Log**; em seguida, depois de escolher o **recurso** apropriado, clique em *Concluído*. Em seguida, use o botão **Adicionar critérios** para exibir uma lista de opções de sinais disponíveis para o recurso e na opção **Pesquisa de logs personalizada**para o serviço de monitoramento de log escolhido como *Log Analytics* ou *Application Insights*.

   ![Selecione um recurso – pesquisa de logs personalizada](./media/monitor-alerts-unified/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]

   > Listas de alertas podem importar consulta analítica como tipo de sinal - **Log (Consulta Salva)**, como mostrado na ilustração acima. Dessa forma, os usuários podem aperfeiçoar sua consulta no Analytics e, em seguida, salvá-la para uso futuro em alertas - mais detalhes sobre o uso de consulta salvas disponível em [usando a pesquisa de log no Log Analytics](../log-analytics/log-analytics-log-searches.md) ou [consulta compartilhada na análise do Application Insights](../log-analytics/log-analytics-overview.md). 

1.  *Alertas de Log*: depois de selecionado, a consulta de alerta poderá ser declarada no campo **Consulta de Pesquisa**. Se a sintaxe de consulta estiver incorreta, o campo exibirá o erro em vermelho. Se a sintaxe de consulta estiver correta – para referência, os dados históricos da consulta indicada serão mostrados como um gráfico com a opção de ajustar a janela de tempo das últimas seis horas até a última semana.

 ![Configurar regra de alerta](./media/monitor-alerts-unified/AlertsPreviewAlertLog.png)

 > [!NOTE]

    > A visualização de dados históricos somente poderá ser mostrada se os resultados da consulta tiverem detalhes de tempo. Se a consulta resultar em dados resumidos ou em valores de coluna específicos, isso será mostrado como um único gráfico.

    >  Para o tipo de medida da métrica de alertas de log usando o Application insights, você pode especificar qual variável específica para agrupar os dados usando a opção **Agregar**; conforme ilustrado abaixo:

    ![opção de agregação](./media/monitor-alerts-unified/aggregate-on.png)

1.  *Alertas de Log*: com a visualização exibida, a **Lógica de Alerta** poderá ser selecionada nas opções de condição, agregação e, finalmente, limite mostradas. Finalmente, especifique na lógica, o tempo para avaliar a condição especificada, usando a opção **Período**. Juntamente com frequência em que o Alerta deve ser executado, selecionando **Frequência**.
Para **Alertas de Log**, os alertas podem ser baseados em:
   - *Número de Registros*: um alerta será criado se a contagem de registros retornada pela consulta for maior ou menor que o valor fornecido.
   - *Medição de Métrica*: um alerta será criado se cada *valor agregado* nos resultados exceder o valor limite fornecido e estiver *agrupado por* valor escolhido. O número de violações de um alerta é o número de vezes que o limite é excedido no período escolhido. Especifique o Total de violações para qualquer combinação de violações no conjunto de resultados ou as Violações consecutivas para exigir que as violações ocorram em amostras consecutivas. Saiba mais sobre [Alertas de Log e seus tipos](monitor-alerts-unified-log.md).


1. Na segunda etapa, defina um nome para o alerta no campo **Nome da regra de alerta** juntamente com uma **Descrição**, detalhando as especificações do alerta, e o valor de **Gravidade** nas opções fornecidas. Esses detalhes são reutilizados em todos os emails de alerta, notificações ou pushs realizados pelo Azure Monitor. Além disso, o usuário pode optar por ativar a regra de alerta imediatamente na criação, ativando adequadamente a opção **Habilitar regra após a criação**.

    Para **Alertas de Log** apenas, algumas funcionalidades adicionais estão disponíveis em detalhes do alerta:

    - **Suprimir Alertas**: quando você ativa a supressão da regra de alerta, as ações da regra são desabilitadas durante um período definido após a criação de um novo alerta. A regra ainda é executada e cria registros de alerta quando os critérios são atendidos. Permitindo que você tenha tempo para corrigir o problema sem executar ações duplicadas.

        ![Suprimir Alertas para Alertas de Log](./media/monitor-alerts-unified/AlertsPreviewSuppress.png)

        > [!TIP]
        > Especifique um valor de supressão de alerta maior do que a frequência do alerta para garantir que as notificações sejam interrompidas sem sobreposição

1. Na terceira e última etapa, especifique se algum **Grupo de Ação** precisa ser acionado para a regra de alerta quando a condição de alerta é atendida. Você pode escolher qualquer Grupo de Ação existente com alerta ou criar um novo Grupo de Ação. De acordo com o Grupo de Ação selecionado, quando o alerta for disparado, o Azure vai: enviar email(s), enviar SMS(s), chamar Webhook(s), corrigir o uso de Runbooks do Azure, enviar por push para a ferramenta ITSM, etc. Saiba mais sobre [Grupos de Ação](monitoring-action-groups.md).

    > [!NOTE]
    > Veja os [Limites do serviço de assinatura do Azure](../azure-subscription-service-limits.md) para saber quais são os limites de conteúdo de Runbook disparados para alertas de log por meio de grupos de ações do Azure

    Para **Alertas de Log**, algumas funcionalidades adicionais estão disponíveis para substituir as ações padrão:

    - **Notificação por Email**: substitui o *assunto do email* no email, enviado pelo Grupo de Ação, se há uma ou mais ações de email no Grupo de Ação. Não é possível modificar o corpo do email e esse campo **não** se destina ao endereço de email.
    - **Incluir conteúdo personalizado do JSON**: substitui o webhook JSON usado pelos Grupos de Ação se há uma ou mais ações de webhook no Grupo de Ação. O usuário pode especificar o formato do JSON a ser usado para todos os webhooks configurados no Grupo de Ação associado; para obter mais informações sobre formatos de webhook, consulte [Ação de webhook para Alertas de Log](monitor-alerts-unified-log-webhook.md). A opção Exibir Webhook é fornecida para verificar o formato usando dados JSON de exemplo.

        ![Substituições de ação para alertas de Log](./media/monitor-alerts-unified/AlertsPreviewOverrideLog.png)


1. Se todos os campos forem válidos e tiverem um tique verde, o botão **criar regra de alerta** poderá ser clicado e o alerta será criado no Azure Monitor – Alertas. Todos os alertas podem ser exibidos no painel do Alertas.

    ![Criação de regra](./media/monitor-alerts-unified/AlertsPreviewCreate.png)

    Em alguns minutos, o alerta estará ativo e disparará conforme descrito anteriormente.

Os usuários também podem finalizar a consulta de análise na [página do Log Analytics no portal do Azure](../log-analytics/log-analytics-log-search-portals.md#log-analytics-page
) e, em seguida, enviá-la por push para criar um alerta por meio do botão 'Definir Alerta' e, em seguida, seguir as instruções da Etapa 6 em diante, no tutorial acima.

 ![Log Analytics – definir um alerta](./media/monitor-alerts-unified/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>Exibir e gerenciar alertas de log no portal do Azure

1. No [portal](https://portal.azure.com/), selecione **Monitor** e na seção MONITOR, escolha **Alertas**.  

1. O **Painel de Alertas** é exibido, no qual todos os alertas do Azure (incluindo os alertas de log) são exibidos em um único painel, incluindo todas as instâncias de quando a regra de alerta de log foi disparada. Para saber mais, confira [Gerenciamento de Alertas](https://aka.ms/managealertinstances).
    > [!NOTE]
    > As regras de alerta do log consistem em uma lógica personalizada com base em consulta, fornecida pelos usuários e, portanto, sem um estado resolvido. Devido a ela, toda vez que as condições especificadas na regra de alerta de log são atendidas, a regra é acionada. 


1. Selecione o botão **Gerenciar regras** na barra superior, para navegar até a seção de gerenciamento de regra, na qual todas as regras de alerta criadas são listadas, incluindo os alertas que foram desabilitados.
    ![ gerenciar regras de alerta](./media/monitoring-activity-log-alerts-new-experience/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>Gerenciando alertas de log usando o modelo de recurso do Azure
No momento, os alertas de log podem ser criados usando dois modelos de recursos diferentes, com base em qual plataforma de análise o alerta deve se basear, ou seja, no Log Analytics ou no Application Insights.

Portanto, a seção a seguir fornece detalhes de como usar o modelo de recurso de alertas de log para cada plataforma de análise.

### <a name="azure-resource-template-for-log-analytics"></a>Modelo de recursos do Azure para o Log Analytics
Os alertas para o Log Analytics são criados por regras de alerta que executam uma pesquisa salva em um intervalo regular. Se os resultados da consulta correspondência aos critérios especificados, será criado um registro de alerta e uma ou mais ações são executadas. 

Modelos de recursos para pesquisa salva e para alertas do Log Analytics estão disponíveis na seção Log Analytics da documentação. Para saber mais, confira [Adicionando pesquisas salvas e alertas do Log Analytics](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md), que inclui exemplos ilustrativos e detalhes do esquema.

### <a name="azure-resource-template-for-application-insights"></a>Modelo de recurso do Azure para o Application Insights
O alerta de log para os recursos do Application Insights tem um tipo de `Microsoft.Insights/scheduledQueryRules/`. Para obter mais informações sobre esse tipo de recurso, consulte [Azure Monitor - referência da API de regras de consulta agendada](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

Esta é a estrutura para [criação de Regras de Consulta Agendada](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) com base em modelo de recursos, com o conjunto de dados de exemplo como variáveis.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0", 
    "parameters": {      
    },   
    "variables": {
    "alertLocation": "southcentralus",
    "alertName": "samplelogalert",
    "alertTag": "hidden-link:/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
    "alertDesription": "Sample log search alert",
    "alertStatus": "true",
    "alertSource":{
        "Query":"requests",
        "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
        "Type":"ResultCount"
         },
     "alertSchedule":{
         "Frequency": 15,
         "Time": 60
         },
     "alertActions":{
         "SeverityLevel": "4"
         },
      "alertTrigger":{
        "Operator":"GreaterThan",
        "Threshold":"1"
         },
       "actionGrp":{
        "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
        "Subject": "Customized Email Header",
        "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"           
         }
  },
  "resources":[ {
    "name":"[variables('alertName')]",
    "type":"Microsoft.Insights/scheduledQueryRules",
    "apiVersion": "2018-04-16",
    "location": "[variables('alertLocation')]",
    "tags":{"[variables('alertTag')]": "Resource"},
    "properties":{
       "description": "[variables('alertDesription')]",
       "enabled": "[variables('alertStatus')]",
       "source": {
           "query": "[variables('alertSource').Query]",
           "dataSourceId": "[variables('alertSource').SourceId]",
           "queryType":"[variables('alertSource').Type]"
       },
      "schedule":{
           "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
           "timeWindowInMinutes": "[variables('alertSchedule').Time]"    
       },
      "action":{
           "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
           "severity":"[variables('alertActions').SeverityLevel]",
           "aznsAction":{
               "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
               "emailSubject":"[variables('actionGrp').Subject]",
               "customWebhookPayload":"[variables('actionGrp').Webhook]"
           },
       "trigger":{
               "thresholdOperator":"[variables('alertTrigger').Operator]",
               "threshold":"[variables('alertTrigger').Threshold]"
           }
       }
     }
   }
 ]
}
```
> [!IMPORTANT]
> Campo de marca com link oculto para o recurso de destino é obrigatório no uso de chamada API para [Regras de Consulta Agendada ](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) ou recurso de modelo. 

O json de exemplo acima podem ser salvo como (digamos) sampleScheduledQueryRule.json para fins deste passo a passo e pode ser implantado usando o [Azure Resource Manager no portal do Azure](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).


## <a name="managing-log-alerts-using-powershell-cli-or-api"></a>Gerenciando alertas de log usando o PowerShell, a CLI ou a API
No momento, os alertas de log podem ser criados usando duas APIs compatíveis com o Resource Manager diferentes, com base em qual plataforma de análise o alerta deve se basear, ou seja, no Log Analytics ou no Application Insights.

Portanto, a seção a seguir fornece detalhes de como usar a API pelo Powershell ou pela CLI para alertas de log para cada plataforma de análise.

### <a name="powershell-cli-or-api-for-log-analytics"></a>PowerShell, CLI ou API para o Log Analytics
A API REST Alerta do Log Analytics é RESTful e pode ser acessada por meio da API REST do Azure Resource Manager. A API pode então ser acessada da uma linha de comando do PowerShell e produzirá resultados da pesquisa para você no formato JSON, permitindo que você use os resultados de diferentes maneiras por meio de programação.

Saiba mais sobre [criar e gerenciar regras de alerta no Log Analytics com API REST](../log-analytics/log-analytics-api-alerts.md), inclusive exemplos de como acessar a API do Powershell.

### <a name="powershell-cli-or-api-for-application-insights"></a>PowerShell, CLI ou API para o Application Insights
[Azure Monitor – API de Regras de Consulta Agendada](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) é uma API REST totalmente compatível com a API REST do Azure Resource Manager. Portanto, pode ser usado por meio do Powershell usando o cmdlet do Gerenciador de Recursos, bem como a CLI do Azure.

Está ilustrado abaixo o uso por meio do cmdlet do PowerShell do Azure Resource Manager para o modelo de recurso de exemplo mostrado anteriormente (sampleScheduledQueryRule.json) na [seção de modelo de recurso](#azure-resource-template-for-application-insights):
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile "D:\Azure\Templates\sampleScheduledQueryRule.json"
```
Está ilustrado abaixo o uso por meio do comando do Azure Resource Manager na CLI do Azure para o modelo de recurso de exemplo mostrado anteriormente (sampleScheduledQueryRule.json) na [seção de modelo de recurso](#azure-resource-template-for-application-insights):

```azurecli
az group deployment create --resource-group myRG --template-file sampleScheduledQueryRule.json
```
Operação bem-sucedida, 201 será retornado para a criação da regra de alerta de novo estado ou 200 será retornado se uma regra de alerta existente for modificada.


  
## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre os [Alertas de log nos alertas do Azure](monitor-alerts-unified-log.md)
* Entender [Ações de Webhook para alertas de log](monitor-alerts-unified-log-webhook.md)
* Saiba mais sobre o [Application Insights](../application-insights/app-insights-analytics.md)
* Saiba mais sobre o [Log Analytics](../log-analytics/log-analytics-overview.md). 

