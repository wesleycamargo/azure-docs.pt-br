---
title: Como estender (copiar) alertas do Portal do OMS para o Azure | Microsoft Docs
description: Ferramentas e API pelas quais os alertas estendidos da OMS para o Azure Alerts podem ser feitos por clientes voluntariamente.
author: msvijayn
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: vinagara
ms.openlocfilehash: c2e11d89f35915ef0a0c1e1f544b0be8df0473de
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="how-to-extend-copy-alerts-from-oms-into-azure"></a>Como estender (copiar) alertas do OMS para o Azure
A partir de **23 de abril de 2018**, todos os clientes que usam alertas que estão configurados no [Microsoft Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) serão estendidos para o Azure. Os alertas que são estendidos para o Azure comportam-se da mesma maneira que no OMS. O monitoramento de recursos permanece intacto. Estender os alertas criados no OMS para o Azure fornece muitos benefícios. Para saber mais sobre as vantagens e o processo de estender alertas do OMS para o Azure, veja [Estender alertas do OMS para o Azure](monitoring-alerts-extend.md).

Os clientes que desejam mover seus alertas do OMS para o Azure imediatamente podem fazer isso usando uma das opções indicadas.

## <a name="option-1---using-oms-portal"></a>Opção 1: Uso do portal do OMS
Para iniciar voluntariamente a extensão de alertas do Portal OMS para o Azure, siga as etapas listadas abaixo.

1. Na página Visão geral do Portal OMS, vá até Configurações e, em seguida, na seção Alertas. Clique no botão "Estender no Azure", como destacado na ilustração a seguir.

    ![Página de configurações de alerta do Portal OMS com a opção de extensão](./media/monitor-alerts-extend/ExtendInto.png)

2. Depois que o botão é clicado, um Assistente de 3 etapas será mostrado, com a primeira etapa fornecendo detalhes do processo. Pressione Avançar para continuar.

    ![Estender Alertas do Portal do OMS para o Azure – Etapa 1](./media/monitor-alerts-extend/ExtendStep1.png)

3. Na segunda etapa, o sistema mostrará um resumo da alteração proposta listando os [Grupos de Ação](monitoring-action-groups.md) apropriados para os alertas do Portal do OMS. Se ações semelhantes forem vistas em mais de um alerta, o sistema proporá associar a todos eles um único grupo de ação.  O grupo de ação proposto segue a convenção de nomenclatura: *Espaçodetrabalho_GrupodeAção_Número*. Para continuar, clique em Avançar.
Uma tela de exemplo abaixo.

    ![Estender Alertas do Portal do OMS para o Azure – Etapa 2](./media/monitor-alerts-extend/ExtendStep2.png)


4. Na última etapa do assistente, você pode pedir para o Portal do OMS agendar a extensão de todos os alertas no Azure criando novos grupos de ação e associá-los a alertas, conforme mostrado na imagem anterior. Para prosseguir, clique em Concluir e confirme o aviso para iniciar o processo. Opcionalmente, os clientes também podem fornecer endereços de email para os quais eles gostariam que o Portal do OMS enviasse um relatório para terminar o processamento.

    ![Estender Alertas do Portal do OMS para o Azure – Etapa 3](./media/monitor-alerts-extend/ExtendStep3.png)

5. Depois que o assistente for concluído, o controle retornará para a página de configurações de alerta e a opção "Estender no Azure" será removida. Em segundo plano, o Portal do OMS agendará alertas no Log Analytics para serem estendidos para o Azure. Isso pode levar algum tempo e quando a operação é iniciada por um breve período, os alertas do Portal do OMS não estarão disponíveis para modificação. O status atual será mostrado por meio de faixa e, se os endereços de email tiverem sido fornecidos durante a etapa 4, eles serão informados quando o processo em segundo plano conseguir estender todos os alertas para o Azure. 

6. Os alertas continuarão a ser listados no Portal do OMS, mesmo depois de serem estendidos para o Azure.

    ![Depois de estender alertas do Portal do OMS para o Azure](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2---using-api"></a>Opção 2: Uso da API
Para os clientes que quiserem controlar ou automatizar programaticamente o processo de estender alertas do Portal do OMS para o Azure, a Microsoft forneceu uma nova API AlertsVersion no Log Analytics.

A API AlertsVersion do Log Analytics é RESTful e pode ser acessada por meio da API REST do Azure Resource Manager. Neste documento, você encontrará exemplos em que a API é acessada por meio de uma linha de comando do PowerShell usando o [ARMClient](https://github.com/projectkudu/ARMClient), uma ferramenta de linha de comando de software livre que simplifica a invocação da API do Azure Resource Manager. O uso do ARMClient e do PowerShell é uma das muitas opções para acessar a API. A API produzirá resultados no formato JSON, permitindo o uso dos resultados da pesquisa de diferentes maneiras por meio de programação.

Ao usar GET na API, será possível obter nos resultados o resumo da alteração proposta, como lista dos [Grupos de Ação](monitoring-action-groups.md) apropriados para os alertas no Portal do OMS, em formato JSON. Se ações semelhantes forem vistas em mais de um alerta, o sistema proporá criar associação de todos eles a um único grupo de ação.  O grupo de ação proposto segue a convenção de nomenclatura: *Espaçodetrabalho_GrupodeAção_Número*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

> [!NOTE]
> A chamada de GET à API não fará os alertas do Portal do OMS serem estendidos para o Azure. Ele apenas fornecerá como resposta o resumo das alterações propostas. Para confirmar que essas alterações serão feitas para estender os alertas no Azure, uma chamada POST precisa ser feita para a API.

Se a chamada GET para a API for bem-sucedida, junto com 200 respostas de OK, será fornecida uma lista JSON de alertas com os grupos de ação propostos. Veja abaixo uma resposta de exemplo:

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
No caso, não há alertas no espaço de trabalho especificado, junto com 200 respostas OK para a operação GET, o JSON seria:

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

Se todos os alertas no espaço de trabalho especificado já tiverem sido estendidos para o Azure, a resposta para a chamada GET seria:
```json
{
    "version": 2
}
```

Para iniciar o agendamento da extensão dos alertas no Portal do OMS para o Azure, inicie um POST para a API. Fazer essa chamada/comando confirma a intenção do usuário, bem como a aceitação para que seus alertas no Portal do OMS sejam estendidos para o Azure e que as alterações sejam executadas conforme o indicado na resposta da chamada GET para a API. Opcionalmente, o usuário pode fornecer uma lista de endereços de email para o qual o Portal do OMS enviará um relatório, quando o processo em segundo plano agendado de estender os alertas no OMS para o Azure for concluído com êxito.

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> O resultado de estender alertas do Portal do OMS para o Azure pode variar do resumo fornecido por GET devido a qualquer alteração feita no sistema. Depois do agendamento, os alertas do Portal do OMS estarão temporariamente indisponíveis para edição/modificação embora novos alertas possam ser criados. 

Se o POST for bem-sucedido, ele deverá retornar 200 respostas OK juntamente com:
```json
{
    "version": 2
}
```
Indicação de que os alertas foram estendidos para o Azure, conforme indicado pela versão 2. Esta versão é apenas para verificar se os alertas foram estendidos para o Azure e não tem nenhuma diferença de uso com a [API de pesquisa do Log Analytics](../log-analytics/log-analytics-api-alerts.md). Depois que os alertas são estendidos para o Azure com êxito, todos os endereços de email fornecido durante o GET receberão um relatório com os detalhes das alterações feitas.


E, finalmente, se todos os alertas no espaço de trabalho especificado já tiverem sido programados para serem estendidos para o Azure, a resposta para POST será 403 Proibido.


## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a nova [experiência de alertas do Azure](monitoring-overview-unified-alerts.md).
* Saiba mais sobre os [Alertas de log nos Alertas do Azure](monitor-alerts-unified-log.md).
