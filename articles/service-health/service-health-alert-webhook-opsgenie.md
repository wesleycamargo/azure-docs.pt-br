---
title: "Configurar alertas de integridade do serviço do Azure com o OpsGenie | Microsoft Docs"
description: "Obtenha notificações personalizadas sobre eventos de integridade do serviço na instância do OpsGenie."
author: shawntabrizi
manager: scotthit
editor: 
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
ms.openlocfilehash: a2309a050225dd0d7ac8d5b3e4c762bc5bcb25c0
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="configure-service-health-alerts-with-opsgenie"></a>Configurar alertas de integridade do serviço com o OpsGenie

Este artigo mostra como configurar alertas de integridade do serviço do Azure com o OpsGenie usando um webhook. Usando a Integração de Integridade do Serviço do Azure do [OpsGenie](https://www.opsgenie.com/), você pode encaminhar alertas de Integridade do Serviço do Azure para o OpsGenie. O OpsGenie pode determinar as pessoas certas a serem notificadas com base em plantões, usando email, mensagens de texto (SMS), chamadas telefônicas, notificações por push do iOS e Android e escalonando alertas até que o alerta seja confirmado ou fechado.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>Criando uma URL de integração de integridade do serviço no OpsGenie
1.  Verifique se você se inscreveu e entrou em sua conta do [OpsGenie](https://www.opsgenie.com/).

2.  Navegue para a seção **Integrações** no OpsGenie.

    ![A seção “Integração” do OpsGenie](./media/webhook-alerts/opsgenie-integrations-section.png)

3.  Selecione botão de integração **Integridade do Serviço do Azure**.

    ![O “botão Integridade do Serviço do Azure” do OpsGenie](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

4.  **Nomeie** o alerta e especifique o campo **Atribuído à Equipe**.

5.  Preencha os outros campos como **Destinatários**, **Habilitado** e **Suprimir Notificações**.

6.  Copie e salve a **URL de Integração**, que já deve conter a `apiKey` acrescentada ao final.

    ![A “URL de Integração” do OpsGenie](./media/webhook-alerts/opsgenie-integration-url.png)

7.  Selecione **Salvar Integração**

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Criar um alerta usando o OpsGenie no portal do Azure
### <a name="for-a-new-action-group"></a>Para um novo grupo de ação:
1. Siga as etapas 1 a 8 de [Criar um alerta em uma notificação de integridade do serviço para um novo grupo de ação usando o portal do Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md).

2. Defina na lista de **Ações**:

    a. **Tipo de Ação:** *Webhook*

    b. **Detalhes:** a **URL de Integração** do OpsGenie salva anteriormente.

    c. **Nome:** o nome, alias ou identificador do webhook.

3. Selecione **Salvar** quando concluir a criação do alerta.

### <a name="for-an-existing-action-group"></a>Para um grupo de ação existente:
1. No [portal do Azure](https://portal.azure.com/), selecione **Monitor**.

2. Na seção **Configurações**, selecione **Grupos de ação**.

3. Encontre e selecione o grupo de ação que você deseja editar.

4. Adicione à lista de **Ações**:

    a. **Tipo de Ação:** *Webhook*

    b. **Detalhes:** a **URL de Integração** do OpsGenie salva anteriormente.

    c. **Nome:** o nome, alias ou identificador do webhook.

5. Selecione **Salvar** quando concluir a atualização do grupo de ação.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testando a integração de webhook por meio de uma solicitação HTTP POST
1. Crie o conteúdo de Integridade do Serviço que você deseja enviar. Encontre um conteúdo de webhook de Integridade do Serviço de exemplo em [Webhooks para alertas do log de atividades do Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Crie uma solicitação HTTP POST, da seguinte maneira:

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <Service Health payload>
    ```
3. Você deverá receber uma resposta `200 OK` com a mensagem de status “êxito”.

4. Acesse o [OpsGenie](https://www.opsgenie.com/) para confirmar se a integração foi configurada com êxito.

## <a name="next-steps"></a>Próximas etapas
- Saiba como [configurar notificações de webhook para sistemas de gerenciamento de problemas existentes](service-health-alert-webhook-guide.md).
- Examine o [esquema do webhook de alertas de log de atividades](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Saiba mais sobre as [notificações de integridade do serviço](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Saiba mais sobre [grupos de ação](../monitoring-and-diagnostics/monitoring-action-groups.md).