---
title: "Configurar alertas de integridade do serviço do Azure com o PagerDuty | Microsoft Docs"
description: "Obtenha notificações personalizadas sobre eventos de integridade do serviço na instância do PagerDuty."
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
ms.openlocfilehash: 9edcb727b9f0af348cacd5533523c4f2e8214703
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="configure-service-health-alerts-with-pagerduty"></a>Configurar alertas de integridade do serviço com o PagerDuty

Este artigo mostra como configurar notificações de integridade do serviço do Azure por meio do PagerDuty usando um webhook. Usando o tipo de integração personalizada do Microsoft Azure [PagerDuty](https://www.pagerduty.com/), você pode adicionar alertas de Integridade do Serviço com facilidade a serviços do PagerDuty novos ou existentes.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Criando uma URL de integração de integridade do serviço no PagerDuty
1.  Verifique se você se inscreveu e entrou em sua conta do [PagerDuty](https://www.pagerduty.com/).

2.  Navegue para a seção **Serviços** do PagerDuty.

    ![A seção “Serviços” do PagerDuty](./media/webhook-alerts/pagerduty-services-section.png)

3.  Selecione **Adicionar Novo Serviço** ou abra um serviço existente configurado.

4.  Nas **Configurações de Integração**, selecione o seguinte:

    a. **Tipo de Integração**: Microsoft Azure

    b. **Nome da Integração**: \<Nome\>

    ![As “Configurações de Integração” do PagerDuty](./media/webhook-alerts/pagerduty-integration-settings.png)

5.  Preencha todos os outros campos obrigatórios e selecione **Adicionar**.

6.  Abra essa nova integração e copie e salve a **URL de Integração**.

    ![A “URL de Integração” do PagerDuty](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Criar um alerta usando o PagerDuty no portal do Azure
### <a name="for-a-new-action-group"></a>Para um novo grupo de ação:
1. Siga as etapas 1 a 8 de [Criar um alerta em uma notificação de integridade do serviço para um novo grupo de ação usando o portal do Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md).

2. Defina na lista de **Ações**:

    a. **Tipo de Ação:** *Webhook*

    b. **Detalhes:** a **URL de Integração** do PagerDuty salva anteriormente.

    c. **Nome:** o nome, alias ou identificador do webhook.

3. Selecione **Salvar** quando concluir a criação do alerta.

### <a name="for-an-existing-action-group"></a>Para um grupo de ação existente:
1. No [portal do Azure](https://portal.azure.com/), selecione **Monitor**.

2. Na seção **Configurações**, selecione **Grupos de ação**.

3. Encontre e selecione o grupo de ação que você deseja editar.

4. Adicione à lista de **Ações**:

    a. **Tipo de Ação:** *Webhook*

    b. **Detalhes:** a **URL de Integração** do PagerDuty salva anteriormente.

    c. **Nome:** o nome, alias ou identificador do webhook.

5. Selecione **Salvar** quando concluir a atualização do grupo de ação.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Testando a integração de webhook por meio de uma solicitação HTTP POST
1. Crie o conteúdo de Integridade do Serviço que você deseja enviar. Encontre um conteúdo de webhook de Integridade do Serviço de exemplo em [Webhooks para alertas do log de atividades do Azure](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md).

2. Crie uma solicitação HTTP POST, da seguinte maneira:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <Service Health payload>
    ```
3. Você deverá receber um `202 Accepted` com uma mensagem que contém a “ID do evento”.

4. Acesse o [PagerDuty](https://www.pagerduty.com/) para confirmar se a integração foi configurada com êxito.

## <a name="next-steps"></a>Próximas etapas
- Saiba como [configurar notificações de webhook para sistemas de gerenciamento de problemas existentes](service-health-alert-webhook-guide.md).
- Examine o [esquema do webhook de alertas de log de atividades](../monitoring-and-diagnostics/monitoring-activity-log-alerts-webhook.md). 
- Saiba mais sobre as [notificações de integridade do serviço](../monitoring-and-diagnostics/monitoring-service-notifications.md).
- Saiba mais sobre [grupos de ação](../monitoring-and-diagnostics/monitoring-action-groups.md).