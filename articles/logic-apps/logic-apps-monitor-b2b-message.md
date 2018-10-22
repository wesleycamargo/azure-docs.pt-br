---
title: Monitorar mensagens B2B e configurar o registro em log – Aplicativo Lógico do Azure | Microsoft Docs
description: Monitorar mensagens AS2, X12 e EDIFACT. Configurar o log de diagnósticos da conta de integração no Aplicativo Lógico do Azure.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.date: 07/21/2017
ms.openlocfilehash: fd48dfaf7b863ab026da1758751509d9911ae01e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948002"
---
# <a name="monitor-b2b-messages-and-set-up-logging-for-integration-accounts-in-azure-logic-apps"></a>Monitorar mensagens B2B e configurar o registro em log para contas de integração no Aplicativo Lógico do Azure

Depois de configurar a comunicação B2B entre dois processos ou aplicativos de negócios em execução por meio de sua conta de integração, essas entidades poderão trocar mensagens entre si. Para confirmar se essa comunicação funciona conforme esperado, configure o monitoramento de mensagens AS2, X12 e EDIFACT, juntamente com o log de diagnósticos da conta de integração por meio do serviço [Azure Log Analytics](../log-analytics/log-analytics-overview.md). Esse serviço monitora os ambientes locais e em nuvem, ajudando a manter a disponibilidade e o desempenho, além de coletar detalhes e eventos de tempo de execução para uma depuração mais avançada. Use também [os dados de diagnóstico com outros serviços](#extend-diagnostic-data), como o Armazenamento do Azure e os Hubs de Eventos do Azure.

## <a name="requirements"></a>Requisitos

* Um aplicativo lógico configurado com o log de diagnósticos. Saiba [como configurar o log nesse aplicativo lógico](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

  > [!NOTE]
  > Após atender a esse requisito, será necessário ter um workspace no Log Analytics. Você deve usar o mesmo workspace do Log Analytics ao configurar o log da sua conta de integração. Se você não tiver um workspace do Log Analytics, saiba[como criar um workspace do Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md).

* Uma conta de integração vinculada ao aplicativo lógico. Saiba [como criar uma conta de integração com um vínculo ao aplicativo lógico](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging-for-your-integration-account"></a>Ativar o log de diagnósticos da conta de integração

Ative o log diretamente na conta de integração ou [por meio do serviço Azure Monitor](#azure-monitor-service). O Azure Monitor fornece monitoramento básico com os dados no nível de infraestrutura. Saiba mais sobre o [Azure Monitor](../azure-monitor/overview.md).

### <a name="turn-on-diagnostics-logging-directly-from-your-integration-account"></a>Ativar o log de diagnósticos diretamente na conta de integração

1. No [portal do Azure](https://portal.azure.com), encontre e selecione sua conta de integração. Em **Monitoramento**, escolha **Logs de diagnóstico**, conforme mostrado aqui:

   ![Encontrar e selecionar a conta de integração e escolher “Logs de diagnóstico”](media/logic-apps-monitor-b2b-message/integration-account-diagnostics.png)

2. Depois de selecionar sua conta de integração, os valores a seguir são selecionados automaticamente. Se esses valores estiverem corretos, escolha **Ativar diagnóstico**. Caso contrário, selecione os valores desejados:

   1. Em **Assinatura**, selecione a assinatura do Azure usada com a conta de integração.
   2. Em **Grupo de recursos**, selecione o grupo de recursos usado com a conta de integração.
   3. Em **Tipo de recurso**, selecione **Contas de integração**. 
   4. Em **Recurso**, selecione a conta de integração. 
   5. Escolha **Ativar diagnóstico**.

   ![Configurar o diagnóstico para sua conta de integração](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. Em **Configurações de diagnóstico** e, em seguida, **Status**, escolha **Ativado**.

   ![Ativar o Diagnóstico do Azure](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. Agora, selecione o workspace do Log Analytics e os dados a serem usados para registrar em log, conforme mostrado:

   1. Selecione **Enviar para o Log Analytics**. 
   2. Em **Log Analytics**, escolha **Configurar**. 
   3. Em **Workspaces do OMS**, selecione o workspace do Log Analytics a ser usado para registrar em log.
   4. Em **Log**, selecione a categoria **IntegrationAccountTrackingEvents**.
   5. Escolha **Salvar**.

   ![Configurar o Log Analytics para enviar dados de diagnóstico para um log](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. Agora, [configure o acompanhamento das mensagens B2B no Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-diagnostics-logging-through-azure-monitor"></a>Ativar o log de diagnósticos por meio do Azure Monitor

1. No [portal do Azure](https://portal.azure.com), no menu principal do Azure, escolha **Monitorar**, **Logs de diagnóstico**. Depois, selecione sua conta de integração, conforme mostrado aqui:

   ![Escolher “Monitorar”, “Logs de diagnóstico” e selecionar a conta de integração](media/logic-apps-monitor-b2b-message/monitor-service-diagnostics-logs.png)

2. Depois de selecionar sua conta de integração, os valores a seguir são selecionados automaticamente. Se esses valores estiverem corretos, escolha **Ativar diagnóstico**. Caso contrário, selecione os valores desejados:

   1. Em **Assinatura**, selecione a assinatura do Azure usada com a conta de integração.
   2. Em **Grupo de recursos**, selecione o grupo de recursos usado com a conta de integração.
   3. Em **Tipo de recurso**, selecione **Contas de integração**.
   4. Em **Recurso**, selecione a conta de integração.
   5. Escolha **Ativar diagnóstico**.

   ![Configurar o diagnóstico para sua conta de integração](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. Em **Configurações de diagnóstico**, escolha **Ativado**.

   ![Ativar o Diagnóstico do Azure](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. Agora, selecione o workspace do Log Analytics e a categoria de eventos para registrar em log, conforme mostrado:

   1. Selecione **Enviar para o Log Analytics**. 
   2. Em **Log Analytics**, escolha **Configurar**. 
   3. Em **Workspaces do OMS**, selecione o workspace do Log Analytics a ser usado para registrar em log.
   4. Em **Log**, selecione a categoria **IntegrationAccountTrackingEvents**.
   5. Quando terminar, escolha **Salvar**.

   ![Configurar o Log Analytics para enviar dados de diagnóstico para um log](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. Agora, [configure o acompanhamento das mensagens B2B no Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Estender como e onde usar os dados de diagnóstico com outros serviços

Junto com o Azure Log Analytics, você pode estender a maneira de usar os dados de diagnóstico do aplicativo lógico com outros serviços do Azure, por exemplo: 

* [Arquivar logs do Diagnóstico do Azure no Armazenamento do Azure](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)
* [Transmitir logs do Diagnóstico do Azure para os Hubs de Eventos do Azure](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

Depois, obtenha o monitoramento em tempo real usando a telemetria e a análise de outros serviços, como o [Stream Analytics do Azure](../stream-analytics/stream-analytics-introduction.md) e o [Power BI](../log-analytics/log-analytics-powerbi.md). Por exemplo: 

* [Transmitir dados dos Hubs de Eventos para o Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analisar dados de streaming com o Stream Analytics e criar um painel de análise em tempo real no Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

De acordo com as opções que você deseja configurar, primeiro [crie uma conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md) ou [crie um hub de eventos do Azure](../event-hubs/event-hubs-create.md). Em seguida, selecione as opções do local para o qual deseja enviar os dados de diagnóstico:

![Enviar dados para uma conta de armazenamento do Azure ou um hub de eventos](./media/logic-apps-monitor-b2b-message/storage-account-event-hubs.png)

> [!NOTE]
> Os períodos de retenção se aplicam somente quando você opta por usar uma conta de armazenamento.

## <a name="supported-tracking-schemas"></a>Esquemas de acompanhamento com suporte

O Azure dá suporte a estes tipos de esquema de acompanhamento, que têm esquemas fixos, exceto o tipo Personalizado.

* [Esquema de acompanhamento do AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquema de acompanhamento do X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Esquema de acompanhamento personalizado](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Próximas etapas

* [Acompanhar mensagens B2B no Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "Acompanhar mensagens B2B no OMS")
* [Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")

