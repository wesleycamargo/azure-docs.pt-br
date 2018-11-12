---
title: Estender alertas do Log Analytics para o Azure
description: Este artigo descreve as ferramentas e a API pelas quais você pode estender alertas do Log Analytics para os Alertas do Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: a470299df86f6b8f7fd61279af0334d01ef94f8d
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50957414"
---
# <a name="extend-alerts-from-log-analytics-into-azure-alerts"></a>Estender alertas do Log Analytics para os Alertas do Azure
O recurso de alertas no Azure Log Analytics está sendo substituído por Alertas do Azure. Como parte dessa transição, os alertas que você configurou originalmente no Log Analytics serão estendidos para o Azure. Caso não queira aguardar até que os alertas sejam movidos automaticamente para o Azure, você poderá iniciar o processo:

- Manualmente do portal do Microsoft Operations Management Suite. 
- Programaticamente usando a API AlertsVersion.  

> [!NOTE]
> A Microsoft aumenta automaticamente os alertas criados em instâncias de nuvem pública do Log Analytics para os Alertas do Azure, a partir de 14 de maio de 2018, em uma série recorrente até que seja concluída. Se você tiver problemas ao criar [grupos de ação](monitoring-action-groups.md), use [essas etapas de correção](monitoring-alerts-extend-tool.md#troubleshooting) para obter grupos de ação criados automaticamente. É possível usar essas etapas até 5 de julho de 2018. *Não aplicável para usuários do Google Azure Government e do Sovereign Cloud do Log Analytics*. 

## <a name="option-1-initiate-from-the-operations-management-suite-portal"></a>Opção 1: iniciar a partir do conjunto do portal do Operations Management Suite
As etapas a seguir descrevem como estender alertas para o workspace do portal do Operations Management Suite.  

1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Log Analytics**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Log Analytics**.
2. No painel de assinaturas do Log Analytics, selecione um workspace e, em seguida, selecione o bloco **Portal do OMS**.
![Captura de tela do painel de assinaturas do Log Analytics, com o bloco do portal do OMS realçado](media/monitoring-alerts-extend-tool/azure-portal-01.png) 
3. Depois que for redirecionado para o portal do Operations Management Suite, selecione o ícone **Configurações**.
![Captura de tela do portal do Operations Management Suite, com o ícone Configurações realçado](media/monitoring-alerts-extend-tool/oms-portal-settings-option.png) 
4. Na página **Configurações**, selecione **Alertas**.  
5. Selecione **Estender para o Azure**.
![Captura de tela da página Configurações de Alerta do portal do Operations Management Suite, com Estender para o Azure realçado](media/monitoring-alerts-extend-tool/ExtendInto.png)
6. Um assistente de três etapas é exibido no painel **Alertas**. Leia a visão geral e selecione **Avançar**.
![Captura de tela da etapa 1 do assistente](media/monitoring-alerts-extend-tool/ExtendStep1.png)  
7. Na segunda etapa, você verá um resumo das alterações propostas, listando [grupos de ações](monitoring-action-groups.md) para os alertas. Se ações semelhantes forem vistas em mais de um alerta, o assistente propõe associar um único grupo de ação a todos eles.  A convenção de nomenclatura é a seguinte: *WorkspaceName_AG_#Number*. Para continuar, selecione **Avançar**.
![Captura de tela da etapa 2 do assistente](media/monitoring-alerts-extend-tool/ExtendStep2.png)  
8. Na última etapa do assistente, selecione **Concluir** e, quando solicitado, confirme para iniciar o processo. Opcionalmente, é possível fornecer um endereço de email para ser notificado quando o processo for concluído e todos os alertas tiverem sido movidos com êxito para os Alertas do Azure.
![Captura de tela da etapa 3 do assistente](media/monitoring-alerts-extend-tool/ExtendStep3.png)

Quando o assistente for concluído, na página **Configurações de Alerta**, a opção de estender alertas para o Azure será removida. Em segundo plano, seus alertas serão movidos para o Azure e isso poderá demorar algum tempo. Durante a operação, não será possível fazer alterações nos alertas do portal do Operations Management Suite. Você poderá ver o status atual da faixa na parte superior do portal. Se você forneceu um endereço de email anteriormente, receberá um email quando o processo for concluído com êxito.  


Os alertas continuarão sendo listados no portal do Operations Management Suite, mesmo após terem sido movidos com êxito para o Azure.
![Captura de tela da página Configurações de Alerta do portal do Operations Management Suite](media/monitoring-alerts-extend-tool/PostExtendList.png)


## <a name="option-2-use-the-alertsversion-api"></a>Opção 2: usar a API AlertsVersion
É possível usar a API do Log Analytics AlertsVersion para estender alertas do Log Analytics para os Alertas do Azure de qualquer cliente que possa chamar a API REST. Você pode acessar a API do PowerShell usando [ARMClient](https://github.com/projectkudu/ARMClient), uma ferramenta de linha de comando de software livre. É possível gerar os resultados em JSON.  

Para usar a API, primeiro você cria uma solicitação GET. Isso avalia e retorna ao resumo das alterações propostas, antes de tentar realmente estender para o Azure usando uma solicitação POST. Os resultados listam os alertas e uma lista proposta dos [grupos de ações](monitoring-action-groups.md), no formato JSON. Se ações semelhantes forem vistas em mais de um alerta, o serviço proporá associá-las a um único grupo de ações. A convenção de nomenclatura é a seguinte: *WorkspaceName_AG_#Number*.

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
                            "serviceUri": "http://test.com"
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

Essa resposta indica que os alertas foram estendidos com êxito para os Alertas do Azure. A propriedade de versão serve apenas para verificar se os alertas foram estendidos para o Azure e não têm relação com a [API de Pesquisa do Log Analytics](../log-analytics/log-analytics-api-alerts.md). Quando os alertas são estendidos ao Azure com êxito, todos os endereços de email fornecidos com a solicitação POST serão enviados para o relatório. Se todos os alertas no workspace especificado já estiverem agendados para serem estendidos, a resposta à solicitação POST será de que a tentativa foi proibida (para o código de status 403). Para exibir qualquer mensagem de erro ou reconhecer se o processo está paralisado, você poderá enviar para a solicitação GET. Se houver uma mensagem de erro, ela será retornada junto com as informações de resumo.

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
                            "serviceUri": "http://test.com"
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


## <a name="option-3-use-a-custom-powershell-script"></a>Opção 3: usar um script do PowerShell personalizado
 Se a Microsoft não tiver estendido com êxito os alertas do portal do Operations Management Suite para o Azure, você poderá fazê-lo manualmente até 5 de julho de 2018. As duas opções de extensão manual são abordadas nas duas seções anteriores.

Após 5 de julho de 2018, todos os alertas do portal do Operations Management Suite serão estendidos para o Azure. Os usuários que não realizarem as [etapas de correção necessárias sugeridas ](#troubleshooting) terão seus alertas em execução sem ações de acionamento ou notificações devido à falta de [grupos de ações](monitoring-action-groups.md) associados. 

Para criar manualmente [grupos de ações](monitoring-action-groups.md) para alertas no Log Analytics, use o script de exemplo a seguir:
```PowerShell
########## Input Parameters Begin ###########


$subscriptionId = ""
$resourceGroup = ""
$workspaceName = "" 


########## Input Parameters End ###########

armclient login

try
{
    $workspace = armclient get /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/"$workspaceName"?api-version=2015-03-20 | ConvertFrom-Json
    $workspaceId = $workspace.properties.customerId
    $resourceLocation = $workspace.location
}
catch
{
    "Please enter valid input parameters i.e. Subscription Id, Resource Group and Workspace Name !!"
    exit
}

# Get Extend Summary of the Alerts
"`nGetting Extend Summary of Alerts for the workspace...`n"
try
{

    $value = armclient get /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/alertsversion?api-version=2017-04-26-preview

    "Extend preview summary"
    "=========================`n"

    $value

    $result = $value | ConvertFrom-Json
}
catch
{

    $ErrorMessage = $_.Exception.Message
    "Error occured while fetching/parsing Extend summary: $ErrorMessage"
    exit 
}

if ($result.version -eq 2)
{
    "`nThe alerts in this workspace have already been extended to Azure."
    exit
}

$in = Read-Host -Prompt "`nDo you want to continue extending the alerts to Azure? (Y/N)"

if ($in.ToLower() -ne "y")
{
    exit
} 


# Check for resource provider registration
try
{
    $val = armclient get subscriptions/$subscriptionId/providers/microsoft.insights/?api-version=2017-05-10 | ConvertFrom-Json
    if ($val.registrationState -eq "NotRegistered")
    {
        $val = armclient post subscriptions/$subscriptionId/providers/microsoft.insights/register/?api-version=2017-05-10
    }
}
catch
{
    "`nThe user does not have required access to register the resource provider. Please try with user having Contributor/Owner role in the subscription"
    exit
}

$actionGroupsMap = @{}
try
{
    "`nCreating new action groups for alerts extension...`n"
    foreach ($actionGroup in $result.migrationSummary.actionGroups)
    {
        $actionGroupName = $actionGroup.actionGroupName
        $actions = $actionGroup.actions
        if ($actionGroupsMap.ContainsKey($actionGroupName))
        {
            continue
        } 
        
        # Create action group payload
        $shortName = $actionGroupName.Substring($actionGroupName.LastIndexOf("AG_"))
        $properties = @{"groupShortName"= $shortName; "enabled" = $true}
        $emailReceivers = New-Object Object[] $actions.emailIds.Count
        $webhookReceivers = New-Object Object[] $actions.webhookActions.Count
        
        $count = 0
        foreach ($email in $actions.emailIds)
        {
            $emailReceivers[$count] = @{"name" = "Email$($count+1)"; "emailAddress" = "$email"}
            $count++
        }

        $count = 0
        foreach ($webhook in $actions.webhookActions)
        {
            $webhookReceivers[$count] = @{"name" = "$($webhook.name)"; "serviceUri" = "$($webhook.serviceUri)"}
            $count++
        }

        $itsmAction = $actions.itsmAction
        if ($itsmAction.connectionId -ne $null)
        {
            $val = @{
            "name" = "ITSM"
            "workspaceId" = "$subscriptionId|$workspaceId"
            "connectionId" = "$($itsmAction.connectionId)"
            "ticketConfiguration" = $itsmAction.templateInfo
            "region" = "$resourceLocation"
            }
            $properties["itsmReceivers"] = @($val)  
        }

        $properties["emailReceivers"] = @($emailReceivers)
        $properties["webhookReceivers"] = @($webhookReceivers)
        $armPayload = @{"properties" = $properties; "location" = "Global"} | ConvertTo-Json -Compress -Depth 4

    
        # Azure Resource Manager call to create action group
        $response = $armPayload | armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroupName/?api-version=2017-04-01

        "Created Action Group with name $actionGroupName" 
        $actionGroupsMap[$actionGroupName] = $actionGroup.actionGroupResourceId.ToLower()
        $index++
    }

    "`nSuccessfully created all action groups!!"
}
catch
{
    $ErrorMessage = $_.Exception.Message

    #Delete all action groups in case of failure
    "`nDeleting newly created action groups if any as some error happened..."
    
    foreach ($actionGroup in $actionGroupsMap.Keys)
    {
        $response = armclient delete /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroup/?api-version=2017-04-01      
    }

    "`nError: $ErrorMessage"
    "`nExiting..."
    exit
}

# Update all alerts configuration to the new version
"`nExtending OMS alerts to Azure...`n"

try
{
    $index = 1
    foreach ($alert in $result.migrationSummary.alerts)
    {
        $uri = $alert.alertId + "?api-version=2015-03-20"
        $config = armclient get $uri | ConvertFrom-Json
        $aznsNotification = @{
            "GroupIds" = @($actionGroupsMap[$alert.actionGroupName])
        }
        if ($alert.customWebhookPayload)
        {
            $aznsNotification.Add("CustomWebhookPayload", $alert.customWebhookPayload)
        }
        if ($alert.customEmailSubject)
        {
            $aznsNotification.Add("CustomEmailSubject", $alert.customEmailSubject)
        }      

        # Update alert version
        $config.properties.Version = 2

        $config.properties | Add-Member -MemberType NoteProperty -Name "AzNsNotification" -Value $aznsNotification
        $payload = $config | ConvertTo-Json -Depth 4
        $response = $payload | armclient put $uri
    
        "Extended alert with name $($alert.alertName)"
        $index++
    }
}
catch
{
    $ErrorMessage = $_.Exception.Message   
    if ($index -eq 1)
    {
        "`nDeleting all newly created action groups as no alerts got extended..."
        foreach ($actionGroup in $actionGroupsMap.Keys)
        {
            $response = armclient delete /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroup/?api-version=2017-04-01      
        }
        "`nDeleted all action groups."  
    }
    
    "`nError: $ErrorMessage"
    "`nPlease resolve the issue and try extending again!!"
    "`nExiting..."
    exit
}

"`nSuccessfully extended all OMS alerts to Azure!!" 

# Update version of workspace to indicate extension
"`nUpdating alert version information in OMS workspace..." 

$response = armclient post "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/alertsversion?api-version=2017-04-26-preview&iversion=2"

"`nExtension complete!!"
```


### <a name="about-the-custom-powershell-script"></a>Sobre o script do PowerShell personalizado 
A seguir, informações importantes sobre o uso do script:
- Um pré-requisito é a instalação do [ARMclient](https://github.com/projectkudu/ARMClient), uma ferramenta de linha de comando de software livre que simplifica a invocação da API do Azure Resource Manager.
- Para executar o script, é necessário ter uma função de colaborador ou proprietário na assinatura do Azure.
- Você deverá fornecer os parâmetros a seguir:
    - $subscriptionId: a ID de Assinatura do Azure associada ao workspace do Log Analytics do Operations Management Suite.
    - $resourceGroup: o workspace do Grupo de Recursos do Azure para o workspace do Log Analytics do Operations Management Suite.
    - $workspaceName: o nome do workspace do Log Analytics do Operations Management Suite.

### <a name="output-of-the-custom-powershell-script"></a>Saída do script do PowerShell personalizado
O script é detalhado e emite as etapas conforme é executado: 
- Também exibe o resumo, contendo informações sobre os alertas existentes do Log Analytics do Operations Management Suite no workspace. O resumo também contém informações sobre os grupos de ações do Azure a serem criados para as ações associadas a eles. 
- Você será solicitado a prosseguir com a extensão ou sair após visualizar o resumo.
- Se você prosseguir com a extensão, novos grupos de ações do Azure serão criados e todos os alertas existentes serão associados a eles. 
- O script sai exibindo a mensagem "Extensão concluída!" Em caso de falhas intermediárias, o script exibirá erros subsequentes.

## <a name="troubleshooting"></a>solução de problemas 
Durante o processo de extensão de alertas, os problemas podem impedir que o sistema crie os [grupos de ações](monitoring-action-groups.md). Nesses casos, você vê uma mensagem de erro em uma faixa na seção **Alerta** do portal do Operations Management Suite ou na chamada GET feita para a API.

> [!IMPORTANT]
> Se os usuários do Log Analytics com base na nuvem pública do Azure não executarem as etapas de correção a seguir antes de 5 de julho de 2018, os alertas serão executados no Azure, mas não acionarão nenhuma ação ou notificação. Para receber notificações de alertas, será necessário editar manualmente e adicionar [grupos de ações](monitoring-action-groups.md) ou usar o script do [ PowerShell personalizado](#option-3---using-custom-powershell-script).

A seguir são apresentadas as etapas de correção para cada erro:
- **Erro: o bloqueio de escopo está presente no nível de grupo de recursos/assinatura para operações de gravação**:   ![Captura de tela da página Configurações de Alerta do portal do Operations Management Suite, com a mensagem de erro Bloqueio de Escopo realçada](media/monitoring-alerts-extend-tool/ErrorScopeLock.png)

    Quando o Bloqueio de Escopo estiver habilitado, o recurso restringirá qualquer alteração na assinatura ou no grupo de recursos que contém o workspace Log Analytics (Operations Management Suite). O sistema não pode estender alertas para o Azure e criar grupos de ações necessários.
    
    Para resolver, exclua o bloqueio *ReadOnly* na assinatura ou grupo de recursos que contém o workspace. É possível fazer isso usando o portal do Azure, o PowerShell, a CLI do Azure ou a API. Para saber mais, consulte [uso de bloqueio de recurso](../azure-resource-manager/resource-group-lock-resources.md). 
    
    Quando você resolve o erro usando as etapas ilustradas no artigo, o Operations Management Suite estenderá os alertas para o Azure na execução agendada do dia seguinte. Não será necessário tomar mais nenhuma ação nem iniciar nada.

- **Erro: Policy está presente no nível de grupo de recursos/assinatura**:   ![Captura de tela da página Configurações de Alerta do portal do Operations Management Suite, com mensagem de erro de Policy realçada](media/monitoring-alerts-extend-tool/ErrorPolicy.png)

    Quando o [Azure Policy](../governance/policy/overview.md) for aplicado restringirá qualquer novo recurso em um grupo de recursos ou assinatura que contenha o workspace do Log Analytics (Operations Management Suite). O sistema não pode estender alertas para o Azure e criar grupos de ações necessários.
    
    Para resolver, edite a política que está causando o erro *[RequestDisallowedByPolicy](../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)*, o que impede a criação de novos recursos no grupo de recursos ou assinatura que contém o workspace. É possível fazer isso usando o portal do Azure, o PowerShell, a CLI do Azure ou a API. Você pode auditar ações para localizar a política apropriada que está causando falhas. Para saber mais, consulte [visualizando logs de atividades para auditar ações](../azure-resource-manager/resource-group-audit.md). 
    
    Quando você resolve o erro usando as etapas ilustradas no artigo, o Operations Management Suite estenderá os alertas para o Azure na execução agendada do dia seguinte. Não será necessário tomar mais nenhuma ação nem iniciar nada.


## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a nova [experiência de Alertas do Azure](monitoring-overview-unified-alerts.md).
* Saiba mais sobre os [Alertas de log nos Alertas do Azure](monitor-alerts-unified-log.md).
