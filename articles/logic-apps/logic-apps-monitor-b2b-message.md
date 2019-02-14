---
title: Monitorar mensagens B2B com o Log Analytics – Aplicativos Lógicos do Azure | Microsoft Docs
description: Monitore mensagens de AS2, X12 e EDIFACT para contas de integração e os Aplicativos Lógicos do Azure e configure o log de diagnósticos com o Azure Log Analytics
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/23/2018
ms.openlocfilehash: e3d2b377df6a3ed8312ca8b2563fe466236c2741
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55818287"
---
# <a name="monitor-b2b-messages-with-azure-log-analytics-in-azure-logic-apps"></a>Monitorar mensagens B2B com o Log Analytics do Azure nos Aplicativos Lógicos do Azure

Após você configurar a comunicação B2B entre parceiros comerciais em sua conta de integração, esses parceiros poderão trocar mensagens uns com os outros. Para verificar se a comunicação funciona da forma esperada, você pode monitorar as mensagens de AS2, X12 e EDIFACT e configurar o log de diagnósticos da conta de integração com o [Log Analytics do Azure](../log-analytics/log-analytics-overview.md). Esse serviço monitora seus ambientes locais e na nuvem, ajudando a manter a disponibilidade e o desempenho, além de coletar detalhes e eventos de tempo de execução para uma depuração mais avançada. Também é possível usar esses dados com outros serviços, como o Armazenamento do Azure e os Hubs de Eventos do Azure.

> [!NOTE]
> Esta página ainda pode ter referências ao OMS (Microsoft Operations Management Suite), que será [desativado em janeiro de 2019](../azure-monitor/platform/oms-portal-transition.md), mas substitui essas etapas pelo Log Analytics do Azure sempre que possível. 

## <a name="prerequisites"></a>Pré-requisitos

* Um aplicativo lógico configurado com o log de diagnósticos. Saiba [como criar um aplicativo lógico](quickstart-create-first-logic-app-workflow.md) e [como configurar o log para esse aplicativo lógico](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Após atender aos requisitos anteriores, você também precisará de um workspace do Log Analytics, que é usado para monitorar e acompanhar a comunicação B2B por meio do Log Analytics. Se você não tiver um workspace do Log Analytics, saiba[como criar um workspace do Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

* Uma conta de integração vinculada ao aplicativo lógico. Saiba [como criar uma conta de integração com um vínculo ao aplicativo lógico](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging"></a>Ativar os logs de diagnósticos

Ative o log diretamente na conta de integração ou [por meio do serviço Azure Monitor](#azure-monitor-service). O Azure Monitor fornece monitoramento básico com os dados no nível de infraestrutura. Saiba mais sobre o [Azure Monitor](../azure-monitor/overview.md).

### <a name="turn-on-logging-from-integration-account"></a>Ativar os logs da conta de integração

1. No [portal do Azure](https://portal.azure.com), encontre e selecione sua conta de integração. Em **Monitoramento**, selecione **Configurações de diagnóstico**.

   ![Encontre e selecione sua conta de integração e selecione “Configurações de diagnóstico”](media/logic-apps-monitor-b2b-message/find-integration-account.png)

1. Agora, encontre e selecione sua conta de integração. Nas listas de filtro, selecione os valores que se aplicam à conta de integração.
Quando terminar, escolha **Adicionar configuração de diagnóstico**.

   | Propriedade | Valor | DESCRIÇÃO | 
   |----------|-------|-------------|
   | **Assinatura** | <*Azure-subscription-name*> | A assinatura do Azure associada à sua conta de integração | 
   | **Grupo de recursos** | <*Azure-resource-group-name*> | O grupo de recursos do Azure para sua conta de integração | 
   | **Tipo de recurso** | **Contas de integração** | O tipo do recurso do Azure no qual você deseja ativar o registro os logs | 
   | **Recurso** | <*integration-account-name*> | O nome do recurso do Azure no qual você deseja ativar o registro os logs | 
   ||||  

   Por exemplo: 

   ![Configurar o diagnóstico para sua conta de integração](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Forneça um nome para sua nova configuração de diagnóstico e selecione o workspace do Log Analytics e os dados que deseja registrar em log.

   1. Selecione **Enviar para o Log Analytics**. 

   1. Em **Log Analytics**, selecione **Configurar**. 

   1. Em **Workspaces do OMS**, selecione o workspace do Log Analytics a ser usado para registro em log. 

      > [!NOTE]
      > Os workspaces do OMS estão sendo substituídos por workspaces do Log Analytics. 

   1. Em **Log**, selecione a categoria **IntegrationAccountTrackingEvents** e escolha **Salvar**.

   Por exemplo:  

   ![Configurar o Log Analytics para enviar dados de diagnóstico para um log](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. Agora, [configure o acompanhamento das mensagens B2B no Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-logging-through-azure-monitor"></a>Ativar os logs no Azure Monitor

1. No [portal do Azure](https://portal.azure.com), no menu principal do Azure, selecione **Monitor**. Em **Configurações**, selecione **Configurações de diagnóstico**. 

   ![Selecione "Monitor" > "Configurações de diagnóstico" > sua conta de integração](media/logic-apps-monitor-b2b-message/monitor-diagnostics-settings.png)

1. Agora, encontre e selecione sua conta de integração. Nas listas de filtro, selecione os valores que se aplicam à conta de integração.
Quando terminar, escolha **Adicionar configuração de diagnóstico**.

   | Propriedade | Valor | DESCRIÇÃO | 
   |----------|-------|-------------|
   | **Assinatura** | <*Azure-subscription-name*> | A assinatura do Azure associada à sua conta de integração | 
   | **Grupo de recursos** | <*Azure-resource-group-name*> | O grupo de recursos do Azure para sua conta de integração | 
   | **Tipo de recurso** | **Contas de integração** | O tipo do recurso do Azure no qual você deseja ativar o registro os logs | 
   | **Recurso** | <*integration-account-name*> | O nome do recurso do Azure no qual você deseja ativar o registro os logs | 
   ||||  

   Por exemplo: 

   ![Configurar o diagnóstico para sua conta de integração](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Forneça um nome para sua nova configuração de diagnóstico e selecione o workspace do Log Analytics e os dados que deseja registrar em log.

   1. Selecione **Enviar para o Log Analytics**. 

   1. Em **Log Analytics**, selecione **Configurar**. 

   1. Em **Workspaces do OMS**, selecione o workspace do Log Analytics a ser usado para registro em log. 

      > [!NOTE]
      > Os workspaces do OMS estão sendo substituídos por workspaces do Log Analytics. 

   1. Em **Log**, selecione a categoria **IntegrationAccountTrackingEvents** e escolha **Salvar**.

   Por exemplo:  

   ![Configurar o Log Analytics para enviar dados de diagnóstico para um log](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. Agora, [configure o acompanhamento das mensagens B2B no Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="use-diagnostic-data-with-other-services"></a>Usar dados de diagnóstico com outros serviços

Junto com o Azure Log Analytics, você pode estender a maneira de usar os dados de diagnóstico do aplicativo lógico com outros serviços do Azure, por exemplo: 

* [Arquivar logs do Diagnóstico do Azure no Armazenamento do Azure](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Transmitir logs do Diagnóstico do Azure para os Hubs de Eventos do Azure](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) 

Depois, obtenha o monitoramento em tempo real usando a telemetria e a análise de outros serviços, como o [Stream Analytics do Azure](../stream-analytics/stream-analytics-introduction.md) e o [Power BI](../azure-monitor/platform/powerbi.md). Por exemplo: 

* [Transmitir dados dos Hubs de Eventos para o Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analisar dados de streaming com o Stream Analytics e criar um painel de análise em tempo real no Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

De acordo com as opções que deseja configurar, primeiro [crie uma conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md) ou [crie um hub de eventos do Azure](../event-hubs/event-hubs-create.md). Em seguida, você pode selecionar os destinos para os quais deseja enviar dados de diagnóstico.
Os períodos de retenção se aplicam somente quando você opta por usar uma conta de armazenamento.

![Enviar dados para uma conta de armazenamento do Azure ou um hub de eventos](./media/logic-apps-monitor-b2b-message/diagnostics-storage-event-hub-log-analytics.png)

## <a name="supported-tracking-schemas"></a>Esquemas de acompanhamento com suporte

O Azure dá suporte a estes tipos de esquema de acompanhamento, que têm esquemas fixos, exceto o tipo Personalizado.

* [Esquema de acompanhamento do AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquema de acompanhamento do X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Esquema de acompanhamento personalizado](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Próximas etapas

* [Rastrear mensagens B2B no Azure Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "Rastrear mensagens B2B no Azure Log Analytics")
* [Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")

