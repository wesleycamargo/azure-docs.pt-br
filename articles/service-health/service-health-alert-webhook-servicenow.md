---
title: "Configurar alertas de integridade do serviço do Azure com o ServiceNow | Microsoft Docs"
description: "Obtenha notificações personalizadas sobre eventos de integridade do serviço na instância do ServiceNow."
author: shawntabrizi
services: service-health
documentationcenter: service-health
ms.assetid: 
ms.service: service-health
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: shtabriz
ms.openlocfilehash: 625718ab82443c897d1b15c2eac51dea3d0dfeb4
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="configure-service-health-alerts-with-servicenow"></a>Configurar alertas de integridade do serviço com o ServiceNow

Este artigo mostra como integrar os alertas de integridade do serviço do Azure com o ServiceNow usando um webhook. Depois de configurar a integração do webhook com a instância do ServiceNow, você receberá alertas por meio de sua infraestrutura de notificação existente quando problemas do serviço do Azure o afetarem. Sempre que um alerta de Integridade do Serviço do Azure é disparado, ele chama um webhook por meio da API REST com Script do ServiceNow.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>Criando uma API REST com script no ServiceNow
1.  Verifique se você se inscreveu e entrou em sua conta do [ServiceNow](https://www.servicenow.com/).

2.  Navegue para a seção **Serviços Web do Sistema** no ServiceNow e selecione **APIs REST com Script**.

    ![A seção “Serviço Web com Script” no ServiceNow](./media/webhook-alerts/servicenow-sws-section.png)

3.  Selecione **Novo** para criar um novo serviço REST com Script.
 
    ![O botão “Nova API REST com Script” no ServiceNow](./media/webhook-alerts/servicenow-new-button.png)

4.  Adicione um **Nome** à API REST e defina a **ID de API** como `azureservicehealth`.

5.  Selecione **Enviar**.

    ![As “Configurações de API REST” no ServiceNow](./media/webhook-alerts/servicenow-restapi-settings.png)

6.  Selecione a API REST criada e, na guia **Recursos**, selecione **Novo**.

    ![A “Guia Recursos” no ServiceNow](./media/webhook-alerts/servicenow-resources-tab.png)

7.  **Nomeie** o novo recurso `event` e altere o **método HTTP** para `POST`.

8.  Na seção **Script**, adicione o seguinte código JavaScript:

    >[!NOTE]
    >É necessário atualizar os valores `<secret>`, `<group>` e `<email>` no script abaixo.
    >* `<secret>` deve ser uma cadeia de caracteres aleatória, como um GUID
    >* `<group>` deve ser o grupo do ServiceNow ao qual você deseja atribuir o incidente
    >* `<email>` deve ser a pessoa específica à qual você deseja atribuir o incidente (opcional)
    >

    ```javascript
    (function process( /*RESTAPIRequest*/ request, /*RESTAPIResponse*/ response) {
        var apiKey = request.queryParams['apiKey'];
        var secret = '<secret>';
        if (apiKey == secret) {
            var event = request.body.data;
            var responseBody = {};
            if (event.data.context.activityLog.operationName == 'Microsoft.ServiceHealth/incident/action') {
                var inc = new GlideRecord('incident');
                var incidentExists = false;
                inc.addQuery('number', event.data.context.activityLog.properties.trackingId);
                inc.query();
                if (inc.hasNext()) {
                    incidentExists = true;
                    inc.next();
                } else {
                    inc.initialize();
                }
                var short_description = "Azure Service Health";
                if (event.data.context.activityLog.properties.incidentType == "Incident") {
                    short_description += " - Service Issue - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Maintenance") {
                    short_description += " - Planned Maintenance - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Information" || event.data.context.activityLog.properties.incidentType == "ActionRequired") {
                    short_description += " - Health Advisory - ";
                }
                short_description += event.data.context.activityLog.properties.title;
                inc.short_description = short_description;
                inc.description = event.data.context.activityLog.properties.communication;
                inc.work_notes = "Impacted subscription: " + event.data.context.activityLog.subscriptionId;
                if (incidentExists) {
                    if (event.data.context.activityLog.properties.stage == 'Active') {
                        inc.state = 2;
                    } else if (event.data.context.activityLog.properties.stage == 'Resolved') {
                        inc.state = 6;
                    } else if (event.data.context.activityLog.properties.stage == 'Closed') {
                        inc.state = 7;
                    }
                    inc.update();
                    responseBody.message = "Incident updated.";
                } else {
                    inc.number = event.data.context.activityLog.properties.trackingId;
                    inc.state = 1;
                    inc.impact = 2;
                    inc.urgency = 2;
                    inc.priority = 2;
                    inc.assigned_to = '<email>';
                    inc.assignment_group.setDisplayValue('<group>');
                    var subscriptionId = event.data.context.activityLog.subscriptionId;
                    var comments = "Azure portal Link: https://app.azure.com/h";
                    comments += "/" + event.data.context.activityLog.properties.trackingId;
                    comments += "/" + subscriptionId.substring(0, 3) + subscriptionId.slice(-3);
                    var impactedServices = JSON.parse(event.data.context.activityLog.properties.impactedServices);
                    var impactedServicesFormatted = "";
                    for (var i = 0; i < impactedServices.length; i++) {
                        impactedServicesFormatted += impactedServices[i].ServiceName + ": ";
                        for (var j = 0; j < impactedServices[i].ImpactedRegions.length; j++) {
                            if (j != 0) {
                                impactedServicesFormatted += ", ";
                            }
                            impactedServicesFormatted += impactedServices[i].ImpactedRegions[j].RegionName;
                        }

                        impactedServicesFormatted += "\n";

                    }
                    comments += "\n\nImpacted Services:\n" + impactedServicesFormatted;
                    inc.comments = comments;
                    inc.insert();
                    responseBody.message = "Incident created.";
                }
            } else {
                responseBody.message = "Hello from the other side!";
            }
            response.setBody(responseBody);
        } else {
            var unauthorized = new sn_ws_err.ServiceError();
            unauthorized.setStatus(401);
            unauthorized.setMessage('Invalid apiKey');
            response.setError(unauthorized);
        }
    })(request, response);
    ```

9.  Na guia Segurança, desmarque a opção **Exige autenticação** e selecione **Enviar**. Em vez disso, o `<secret>` definido protege essa API.

    ![A caixa de seleção “Exige autenticação” no ServiceNow](./media/webhook-alerts/servicenow-resource-settings.png)

10.  De volta à seção APIs REST com Script, você deve encontrar o **Caminho Base da API** para a nova API REST:

     ![O “Caminho Base da API” no ServiceNow](./media/webhook-alerts/servicenow-base-api-path.png)

11.  A URL de Integração completa é semelhante a:
        
         https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>


## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>Criar um alerta usando o ServiceNow no portal do Azure
### <a name="for-a-new-action-group"></a>Para um novo grupo de ação:
1. Siga as etapas 1 a 8 [deste artigo](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md) para criar um alerta com um novo grupo de ação.

2. Defina na lista de **Ações**:

    a. **Tipo de Ação:** *Webhook*

    b. **Detalhes:** a **URL de Integração** do ServiceNow salva anteriormente.

    c. **Nome:** o nome, alias ou identificador do webhook.

3. Selecione **Salvar** quando concluir a criação do alerta.

### <a name="for-an-existing-action-group"></a>Para um grupo de ação existente:
1. No [portal do Azure](https://portal.azure.com/), selecione **Monitor**.

2. Na seção **Configurações**, selecione **Grupos de ação**.

3. Encontre e selecione o grupo de ação que você deseja editar.

4. Adicione à lista de **Ações**:

    a. **Tipo de Ação:** *Webhook*

    b. **Detalhes:** a **URL de Integração** do ServiceNow salva anteriormente.

    c. **Nome:** o nome, alias ou identificador do webhook.

5. Selecione **Salvar** quando concluir a atualização do grupo de ação.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testando a integração de webhook por meio de uma solicitação HTTP POST
1. Crie o conteúdo de Integridade do Serviço que você deseja enviar. Encontre um conteúdo de webhook de Integridade do Serviço de exemplo em [Webhooks para alertas do log de atividades do Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Crie uma solicitação HTTP POST, da seguinte maneira:

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <Service Health payload>
    ```
3. Você deverá receber uma resposta `200 OK` com a mensagem “Incidente criado”.

4. Acesse o [ServiceNow](https://www.servicenow.com/) para confirmar se a integração foi configurada com êxito.

## <a name="next-steps"></a>Próximas etapas
- Saiba como [configurar notificações de webhook para sistemas de gerenciamento de problemas existentes](service-health-alert-webhook-guide.md).
- Examine o [esquema do webhook de alertas de log de atividades](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Saiba mais sobre as [notificações de integridade do serviço](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Saiba mais sobre [grupos de ação](../monitoring-and-diagnostics/monitoring-action-groups.md).