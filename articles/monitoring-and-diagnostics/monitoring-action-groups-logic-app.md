---
title: Como disparar ações complexas com Alertas do Azure Monitor e Grupos de Ações | Microsoft Docs
description: Saiba como criar uma ação do Aplicativo Lógico para processar Alertas do Azure Monitor.
author: dkamstra
manager: chrad
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: dukek
ms.openlocfilehash: 0020f1475d52d01897320062edbd3a66c8acf751
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33887446"
---
# <a name="create-a-logic-app-action"></a>Criar uma ação do Aplicativo Lógico
## <a name="overview"></a>Visão geral ##
Quando um alerta do Azure Monitor dispara, ele chama um [grupo de ações](monitoring-action-groups.md). Os grupos de ações permitem que você dispare uma ou mais ações para notificar as pessoas sobre o alerta e até mesmo corrigi-lo.

Este artigo mostra como configurar e disparar um Aplicativo Lógico para criar uma conversa no Microsoft Teams quando um alerta é acionado.

O processo geral é:

-   Criar o Aplicativo Lógico para o respectivo tipo de alerta

-   Importar o esquema para o respectivo tipo de alerta no Aplicativo Lógico

-   Definir o comportamento do Aplicativo Lógico

-   Copiar o ponto de extremidade HTTP do Aplicativo Lógico em um Grupo de Ações do Azure

O processo será semelhante se você quiser que o Aplicativo Lógico execute uma ação diferente.

## <a name="create-an-activity-log-alert--administrative"></a>Criar um alerta de log de Atividades – Administrativo

1.  Clique no botão **Criar um recurso** localizado no canto superior esquerdo do Portal do Azure.

2.  Pesquise e selecione **Aplicativo Lógico**. Selecione o botão **Criar** .

3.  Forneça um nome ao Aplicativo Lógico, escolha um grupo de Recursos e etc.

    ![Criar caixa de diálogo de Aplicativo Lógico](media/monitoring-action-groups/create-logic-app-dialog.png "Criar caixa de diálogo de aplicativo lógico")

4.  Clique no botão Criar para criar o Aplicativo Lógico. Um pop-up será exibido quando o Aplicativo Lógico for criado. Clique no botão Iniciar Recurso para abrir o Designer de Aplicativos Lógicos.

5.  Selecione o gatilho **Quando uma solicitação HTTP for recebida**.

    ![Gatilhos do Aplicativo Lógico](media/monitoring-action-groups/logic-app-triggers.png "Gatilhos do Aplicativo Lógico")

6.  Selecione **Editar** para alterar o gatilho de solicitação HTTP

    ![Forma de gatilho de solicitação HTTP](media/monitoring-action-groups/http-request-trigger-shape.png "Forma de gatilho de solicitação HTTP")

7.  Selecione **Use o conteúdo de amostra para gerar o esquema**.

    ![Use o botão de carga de exemplo](media/monitoring-action-groups/use-sample-payload-button.png "Use o botão de carga de exemplo")

8.  Copie e cole o esquema de exemplo a seguir na caixa de diálogo.

    ```json
        {
            "schemaId": "Microsoft.Insights/activityLogs",
            "data": {
                "status": "Activated",
                "context": {
                "activityLog": {
                    "authorization": {
                    "action": "microsoft.insights/activityLogAlerts/write",
                    "scope": "/subscriptions/…"
                    },
                    "channels": "Operation",
                    "claims": "…",
                    "caller": "logicappdemo@contoso.com",
                    "correlationId": "91ad2bac-1afa-4932-a2ce-2f8efd6765a3",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-04-03T22:33:11.762469+00:00",
                    "eventDataId": "ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0",
                    "level": "Informational",
                    "operationName": "microsoft.insights/activityLogAlerts/write",
                    "operationId": "61f59fc8-1442-4c74-9f5f-937392a9723c",
                    "resourceId": "/subscriptions/…",
                    "resourceGroupName": "LOGICAPP-DEMO",
                    "resourceProviderName": "microsoft.insights",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:33:36.1068742+00:00",
                    "resourceType": "microsoft.insights/activityLogAlerts"
                }
                },
                "properties": {}
            }
        }
    ```

9. O Designer do Aplicativo Lógico exibirá uma anotação lembrando que a solicitação enviada ao Aplicativo Lógico deve definir o cabeçalho Content-Type para aplicativo/json. Prossiga e ignore a caixa de diálogo. O alerta do Azure Monitor fará isso corretamente.

    ![Cabeçalho Content-Type](media/monitoring-action-groups/content-type-header.png "Cabeçalho Content-Type")

10. Selecione **+ Nova Etapa** e, em seguida, **Adicione uma ação**.

    ![Adicionar Ação](media/monitoring-action-groups/add-action.png "Adicionar Ação")

11. Pesquise e selecione o conector do Microsoft Teams. Escolha a ação **Microsoft Teams – Postar Mensagem**.

    ![Ações do Microsoft Teams](media/monitoring-action-groups/microsoft-teams-actions.png "Ações do Microsoft Teams")

12. Configure a ação do Microsoft Teams. O Designer de Aplicativos Lógicos solicitará que você autentique-se na sua conta do Office365. Escolha a **ID da Equipe** e **ID do Canal** para enviar a mensagem.

13. Configure a **Mensagem** usando uma combinação de texto estático e referências aos \<campos\> no conteúdo Dinâmico. Recorte e cole o texto a seguir no campo Mensagem.

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Em seguida, localize e substitua os \<campos\> por marcar de conteúdo Dinâmico com o mesmo nome.

    **[OBSERVAÇÃO!]** Há dois campos dinâmicos nomeados **status**. Adicione ambos os campos de status à mensagem. Use um no recipiente de propriedades activityLog e exclua o outro. Se você passar o mouse sobre o campo **status** verá a referência de campo completa, conforme mostrado na captura de tela

    ![Ação do Teams - Postar Mensagem](media/monitoring-action-groups/teams-action-post-message.png "Ação do Teams - Postar Mensagem")

14. Salve o Aplicativo Lógico clicando no botão Salvar na parte superior do Designer

15. Clique na forma de solicitação HTTP para expandi-la. Copie a URL DE HTTP POST.

    ![URL DE HTTP POST](media/monitoring-action-groups/http-post-url.png "URL DE HTTP POST")

16. Abra o grupo de ações existente e adicione uma ação para referenciar o Aplicativo Lógico. Se você não tiver um grupo de ações existente, consulte <https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-action-groups> para criar um. Não se esqueça de salvar as alterações.

    ![Atualizar grupo de ações](media/monitoring-action-groups/update-action-group.png "Atualizar grupo de ações")

Na próxima vez que um alerta chamar o grupo de ações, o Aplicativo Lógico será chamado.

## <a name="create-a-service-health-alert"></a>Criar um alerta de Integridade do Serviço

As entradas de Integridade do Serviço fazem parte do log de Atividades, portanto, o processo é semelhante com as alterações a seguir

1.  As etapas 1 a 7 são as mesmas.
2.  Use o esquema de exemplo a seguir para o gatilho HTTP na etapa 8.

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "…",
                        "service": "…",
                        "region": "Global",
                        "communication": "…",
                        "incidentType": "Incident",
                        "trackingId": "…",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{"ImpactedRegions"}]",
                        "defaultLanguageTitle": "…",
                        "defaultLanguageContent": "…",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

3.  As etapas 9 a 10 são as mesmas.
4.  A partir da Etapa 11, use o processo abaixo.
5.  Clique no botão **+ Nova Etapa** e escolha **Adicionar uma condição**. Defina as condições a seguir para garantir que o Aplicativo Lógico somente seja executado quando os dados de entrada corresponderem a esses valores
    - schemaId == Microsoft.Insights/activityLogs
    - eventSource == ServiceHealth
    - versão == 0.1.1

    !["Condição de carga da Integridade do Serviço"](media/monitoring-action-groups/service-health-payload-condition.png "Condição de carga da Integridade do Serviço")

6. Na condição **se verdadeiro**, adicione a ação do Microsoft Teams usando as etapas de 11 a 13 do exemplo anterior.

7. Defina a mensagem usando uma combinação de HTML e [conteúdo dinâmico]. Copie e cole o conteúdo abaixo no campo da mensagem. Substitua os campos [incidentType], [trackingID], [title] e [communication] por marcas de conteúdo dinâmico com o mesmo nome

    ```html
    <p>
    <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
    <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
    <strong>Title:</strong>&nbsp;[title]</p>
    <p>
    <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details log into the Azure Service Health dashboard</a>
    </p>
    <p>[communication]</p>
    ```

    !["Ação posterior da condição verdadeira da Integridade do Serviço"](media/monitoring-action-groups/service-health-true-condition-post-action.png "Ação posterior da condição verdadeira da Integridade do Serviço")

8. Para a condição **Se falso** forneça uma Mensagem útil

    ```html
    <p><strong>Service Health Alert</strong></p>
    <p><b>Unrecognized alert schema</b></p>
    <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details log into the Azure Service Health dashboard\</a></p>
    ```

    !["Ação posterior da condição falsa da Integridade do Serviço"](media/monitoring-action-groups/service-health-false-condition-post-action.png "Ação posterior da condição falsa da Integridade do Serviço")

9.  Siga as etapas de 15 a 16 do exemplo anterior para salvar o Aplicativo Lógico e atualizar grupo de ações

## <a name="metric-alert"></a>Alerta de Métrica

1.  As etapas de 1 a 7 são as mesmas do primeiro exemplo
2.  Use o esquema de exemplo a seguir para o gatilho HTTP na etapa 8.

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "…",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "…",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "…",
        "portalLink": "…"
        },
        "properties": {}
    }
    }
    ```

3.  As etapas 9 a 10 são as mesmas.
4.  A partir da Etapa 11, use o processo abaixo.
5.  Clique no botão **+ Nova Etapa** e escolha **Adicionar uma condição**. Defina as condições a seguir para garantir que o Aplicativo Lógico somente seja executado quando os dados de entrada corresponderem a esses valores

    - schemaId == AzureMonitorMetricAlert
    - versão == 2.0

    !["Condição de carga de alerta de métrica"](media/monitoring-action-groups/metric-alert-payload-condition.png "Condição de carga de alerta de métrica")

6.  Na condição **se verdadeiro** adicionaremos uma forma **Para cada** e a ação do Microsoft Teams e definiremos a Mensagem usando uma combinação de HTML e [conteúdo dinâmico]

    !["Ação posterior da condição verdadeira de alerta de métrica"](media/monitoring-action-groups/metric-alert-true-condition-post-action.png "Ação posterior da condição verdadeira de alerta de métrica")

7.  Na forma **Se falso**, defina uma ação do Microsoft Teams que comunica que o Alerta de Métrica não corresponde às expectativas do Aplicativo lógico e inclua a carga JSON. Observe como referenciamos o conteúdo dinâmico triggerBody na expressão json().

    !["Ação posterior da condição falsa de alerta de métrica"](media/monitoring-action-groups/metric-alert-false-condition-post-action.png "Ação posterior da condição falsa de alerta de métrica")

8.  Siga as etapas de 15 a 16 do primeiro exemplo para salvar o Aplicativo Lógico e atualizar grupo de ações

## <a name="next-steps"></a>Próximas etapas ##
* Obtenha uma [visão geral dos alertas do log de atividades](monitoring-overview-alerts.md) e saiba como receber alertas.  
* Saiba como [configurar alertas sempre que uma notificação de integridade do serviço é postada](monitoring-activity-log-alerts-on-service-notifications.md).
* Saiba mais sobre [Grupos de Ação](monitoring-action-groups.md)