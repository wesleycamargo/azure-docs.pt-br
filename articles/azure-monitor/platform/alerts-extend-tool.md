---
title: Estender alertas do Log Analytics para Nuvem do Azure Governamental
description: Este artigo descreve as ferramentas e a API pelas quais você pode estender alertas do Log Analytics para os Alertas do Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 9d734f74c4e12b369e46c15dcb9d01a8185dddd6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60431018"
---
# <a name="extend-alerts-from-log-analytics-into-azure-alerts"></a>Estender alertas do Log Analytics para os Alertas do Azure
O recurso de alertas no portal do OMS está sendo substituído pelos Alertas do Azure na nuvem do Azure Governamental. Como parte dessa transição, os alertas que você configurou originalmente no Log Analytics serão estendidos para o Azure. Caso não queira aguardar até que os alertas sejam movidos automaticamente para o Azure, você poderá iniciar o processo:

- Manualmente do portal do Microsoft Operations Management Suite. 
- Programaticamente usando a API AlertsVersion.  

> [!NOTE]
> A Microsoft estenderá automaticamente os alertas criados nas instâncias do portal do OMS do Azure Governamental do Log Analytics para Alertas do Azure, a partir de 1º de março de 2019, de forma sistemática. Se você tiver problemas ao criar [grupos de ação](../../azure-monitor/platform/action-groups.md), use [essas etapas de correção](alerts-extend-tool.md#troubleshooting) para obter grupos de ação criados automaticamente. Será possível usar essas etapas até 15 de março de 2019 no portal do OMS do Azure Governamental.

## <a name="option-1-initiate-from-the-operations-management-suite-portal"></a>Opção 1: iniciar do conjunto do portal do Operations Management Suite
As etapas a seguir descrevem como estender alertas para o espaço de trabalho do portal do Operations Management Suite para a nuvem do Azure Governamental.  

1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Log Analytics**.
2. No painel de assinaturas do Log Analytics, selecione um workspace e, em seguida, selecione o bloco **Portal do OMS**.
![Captura de tela do painel de assinaturas do Log Analytics, com o bloco do portal do OMS realçado](media/alerts-extend-tool/azure-portal-01.png) 
3. Depois que for redirecionado para o portal do Operations Management Suite, selecione o ícone **Configurações**.
![Captura de tela do portal do Operations Management Suite, com o ícone Configurações realçado](media/alerts-extend-tool/oms-portal-settings-option.png) 
4. Na página **Configurações**, selecione **Alertas**.  
5. Selecione **Estender para o Azure**.
![Captura de tela da página Configurações de Alerta do portal do Operations Management Suite, com Estender para o Azure realçado](media/alerts-extend-tool/ExtendInto.png)
6. Um assistente de três etapas é exibido no painel **Alertas**. Leia a visão geral e selecione **Avançar**.
![Captura de tela da etapa 1 do assistente](media/alerts-extend-tool/ExtendStep1.png)  
7. Na segunda etapa, você verá um resumo das alterações propostas, listando [grupos de ações](../../azure-monitor/platform/action-groups.md) para os alertas. Se ações semelhantes forem vistas em mais de um alerta, o assistente propõe associar um único grupo de ação a todos eles.  A convenção de nomenclatura é a seguinte: *WorkspaceName_AG_#Number*. Para continuar, selecione **Avançar**.
![Captura de tela da etapa 2 do assistente](media/alerts-extend-tool/ExtendStep2.png)  
8. Na última etapa do assistente, selecione **Concluir** e, quando solicitado, confirme para iniciar o processo. Opcionalmente, é possível fornecer um endereço de email para ser notificado quando o processo for concluído e todos os alertas tiverem sido movidos com êxito para os Alertas do Azure.
![Captura de tela da etapa 3 do assistente](media/alerts-extend-tool/ExtendStep3.png)

Quando o assistente for concluído, na página **Configurações de Alerta**, a opção de estender alertas para o Azure será removida. Em segundo plano, seus alertas serão movidos para o Azure e isso poderá demorar algum tempo. Durante a operação, não será possível fazer alterações nos alertas do portal do Operations Management Suite. Você poderá ver o status atual da faixa na parte superior do portal. Se você forneceu um endereço de email anteriormente, receberá um email quando o processo for concluído com êxito.  


Os alertas continuarão sendo listados no portal do Operations Management Suite, mesmo após terem sido movidos com êxito para o Azure.
![Captura de tela da página Configurações de Alerta do portal do Operations Management Suite](media/alerts-extend-tool/PostExtendList.png)


## <a name="option-2-use-the-alertsversion-api"></a>Opção 2: usar a API AlertsVersion
É possível usar a API do Log Analytics AlertsVersion para estender alertas do Log Analytics para os Alertas do Azure de qualquer cliente que possa chamar a API REST. Você pode acessar a API do PowerShell usando [ARMClient](https://github.com/projectkudu/ARMClient), uma ferramenta de linha de comando de software livre. É possível gerar os resultados em JSON.  

Para usar a API, primeiro você cria uma solicitação GET. Isso avalia e retorna ao resumo das alterações propostas, antes de tentar realmente estender para o Azure usando uma solicitação POST. Os resultados listam os alertas e uma lista proposta dos [grupos de ações](../../azure-monitor/platform/action-groups.md), no formato JSON. Se ações semelhantes forem vistas em mais de um alerta, o serviço proporá associá-las a um único grupo de ações. A convenção de nomenclatura é a seguinte: *WorkspaceName_AG_#Number*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Se a solicitação GET tiver êxito, um código de status HTTP 200 será retornado, junto com uma lista de alertas e grupos de ações propostos nos dados JSON. A seguir, um exemplo de resposta:

```json
{
    "version": 1,
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "https://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}

```
Se o workspace especificado não tiver nenhuma regra de alerta definida, os dados JSON retornarão o seguinte:

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

Se todas as regras de alerta no workspace especificado já tiverem sido estendidas ao Azure, a resposta à solicitação GET será:

```json
{
    "version": 2
}
```

Para iniciar a migração dos alertas para o Azure, inicie a resposta POST. A resposta do POST confirma a intenção, bem como a aceitação, de ter alertas estendidos do Log Analytics para os Alertas do Azure. A atividade é agendada e os alertas são processados conforme indicado, com base nos resultados quando você executou a resposta GET anteriormente. Opcionalmente, é possível fornecer uma lista de endereços de email para os quais o Log Analytics enviará um relatório quando o processo agendado de migração dos alertas for concluído com êxito. Você pode usar o exemplo de solicitação a seguir:

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> O resultado da migração de alertas para os Alertas do Azure pode variar com base no resumo fornecido pela resposta GET. Quando agendados, os alertas no Log Analytics ficam temporariamente indisponíveis para modificação no portal do Operations Management Suite. No entanto, é possível criar novos alertas. 

Se a solicitação POST tiver êxito, ela retornará um status HTTP 200 OK, junto com a resposta a seguir:

```json
{
    "version": 2
}
```

Essa resposta indica que os alertas foram estendidos com êxito para os Alertas do Azure. A propriedade de versão serve apenas para verificar se os alertas foram estendidos para o Azure e não têm relação com a [API de Pesquisa do Log Analytics](../../azure-monitor/platform/api-alerts.md). Quando os alertas são estendidos ao Azure com êxito, todos os endereços de email fornecidos com a solicitação POST serão enviados para o relatório. Se todos os alertas no workspace especificado já estiverem agendados para serem estendidos, a resposta à solicitação POST será de que a tentativa foi proibida (para o código de status 403). Para exibir qualquer mensagem de erro ou reconhecer se o processo está paralisado, você poderá enviar para a solicitação GET. Se houver uma mensagem de erro, ela será retornada junto com as informações de resumo.

```json
{
    "version": 1,
    "message": "OMS was unable to extend your alerts into Azure, Error: The subscription is not registered to use the namespace 'microsoft.insights'. OMS will schedule extending your alerts, once remediation steps illustrated in the troubleshooting guide are done.",
    "recipients": [
       "john.doe@email.com",
       "jane.doe@email.com"
     ],
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "https://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}              

```

## <a name="troubleshooting"></a>solução de problemas 
Durante o processo de extensão de alertas, os problemas podem impedir que o sistema crie os [grupos de ações](../../azure-monitor/platform/action-groups.md). Nesses casos, você vê uma mensagem de erro em uma faixa na seção **Alerta** do portal do Operations Management Suite ou na chamada GET feita para a API.

> [!IMPORTANT]
> Se os usuários do portal do OMS baseados em nuvem do Azure Governamental não executarem as etapas de correção a seguir antes de 15 de março de 2019, os alertas serão executados no Azure, mas não irão disparar nenhuma ação ou notificação. Para receber notificações de alertas, será necessário editar manualmente as regras de alerta no Azure e adicionar [grupos de ações](../../azure-monitor/platform/action-groups.md)

A seguir são apresentadas as etapas de correção para cada erro:
- **Erro: Bloqueio de Escopo está presente no nível do grupo de recursos/assinatura para operações de gravação**:   ![Captura de tela da página Configurações do Alerta do portal do Operations Management Suite com a mensagem de erro de bloqueio de escopo realçada](media/alerts-extend-tool/ErrorScopeLock.png)

    Quando o Bloqueio de Escopo estiver habilitado, o recurso restringirá qualquer alteração na assinatura ou no grupo de recursos que contém o workspace Log Analytics (Operations Management Suite). O sistema não pode estender alertas para o Azure e criar grupos de ações necessários.
    
    Para resolver, exclua o bloqueio *ReadOnly* na assinatura ou grupo de recursos que contém o workspace. É possível fazer isso usando o portal do Azure, o PowerShell, a CLI do Azure ou a API. Para saber mais, consulte [uso de bloqueio de recurso](../../azure-resource-manager/resource-group-lock-resources.md). 
    
    Quando você resolve o erro usando as etapas ilustradas no artigo, o Operations Management Suite estenderá os alertas para o Azure na execução agendada do dia seguinte. Não será necessário tomar mais nenhuma ação nem iniciar nada.

- **Erro: a política está presente no nível do grupo de recursos/assinatura**:   ![Captura de tela da página Configurações do Alerta do portal do Operations Management Suite com a mensagem de erro de Política realçada](media/alerts-extend-tool/ErrorPolicy.png)

    Quando o [Azure Policy](../../governance/policy/overview.md) for aplicado restringirá qualquer novo recurso em um grupo de recursos ou assinatura que contenha o workspace do Log Analytics (Operations Management Suite). O sistema não pode estender alertas para o Azure e criar grupos de ações necessários.
    
    Para resolver, edite a política que está causando o erro *[RequestDisallowedByPolicy](../../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)*, o que impede a criação de novos recursos no grupo de recursos ou assinatura que contém o workspace. É possível fazer isso usando o portal do Azure, o PowerShell, a CLI do Azure ou a API. Você pode auditar ações para localizar a política apropriada que está causando falhas. Para saber mais, consulte [visualizando logs de atividades para auditar ações](../../azure-resource-manager/resource-group-audit.md). 
    
    Quando você resolve o erro usando as etapas ilustradas no artigo, o Operations Management Suite estenderá os alertas para o Azure na execução agendada do dia seguinte. Não será necessário tomar mais nenhuma ação nem iniciar nada.


## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre a nova [experiência de Alertas do Azure](../../azure-monitor/platform/alerts-overview.md).
* Saiba mais sobre os [Alertas de log nos Alertas do Azure](alerts-unified-log.md).

