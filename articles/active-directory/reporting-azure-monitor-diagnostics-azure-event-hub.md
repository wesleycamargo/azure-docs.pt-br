---
title: Tutorial – Stream de logs do Azure Active Directory para um hub de eventos do Azure (versão prévia) | Microsoft Docs
description: Saiba como configurar o Diagnóstico do Azure para enviar logs do Active Directory do Azure por push para um hub de eventos (versão prévia)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: c4f5bf1b49d7f59b2bb938a7ddc53b96c1d354ef
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240132"
---
# <a name="tutorial-stream-azure-active-directory-logs-to-an-azure-event-hub-preview"></a>Tutorial: Stream de logs do Azure Active Directory para um hub de eventos do Azure (versão prévia)

Neste tutorial, saiba como definir as configurações de diagnóstico do Azure Monitor para transmitir logs do Azure Active Directory para um hub de eventos do Azure. Use esse mecanismo para integrar os logs a ferramentas SIEM de terceiros, como o Splunk e o QRadar.

## <a name="prerequisites"></a>Pré-requisitos 

Você precisa de:

* Uma assinatura do Azure. Se não tiver uma assinatura do Azure, você poderá [assinar uma versão de avaliação gratuita](https://azure.microsoft.com/free/).
* Um locatário do Azure Active Directory
* Um usuário, que é um administrador global ou administrador de segurança para esse locatário
* Um namespace dos Hubs de Eventos e um hub de eventos em sua assinatura do Azure. Saiba como [criar um aqui](https://docs.microsoft.com/azure/event-hubs/event-hubs-create.md).

## <a name="archive-logs-to-event-hub"></a>Arquivar logs em um hub de eventos

1. Entre no [Portal do Azure](https://portal.azure.com). 
2. Clique em **Azure Active Directory** -> **Atividade** -> **Trilhas de auditoria**. 
3. Clique em **Exportar configurações** para abrir a folha Configurações de Diagnóstico. Clique em **Editar configuração**, se você quiser alterar as configurações existentes, ou clique em **Adicionar configuração de diagnóstico** para adicionar uma nova. Você pode ter até três configurações. 
    ![Exportar configurações](./media/reporting-azure-monitor-diagnostics-azure-event-hub/ExportSettings.png "Exportar configurações")

4. Marque a caixa de seleção **Stream para um hub de eventos** e clique em **Hub de Eventos/Configurar**.
5. Selecione uma assinatura do Azure e o namespace dos Hubs de Eventos para onde você deseja encaminhar os logs. A assinatura e o namespace dos Hubs de Eventos devem estar associados ao locatário do Active Directory de onde os logs estão sendo transmitidos. Você também pode especificar um hub de eventos dentro do namespace dos Hubs de Eventos para os quais os logs devem ser enviados. Se nenhum hub de eventos for especificado, um hub de eventos será criado no namespace com o nome padrão **insights-logs-audit**.
6. Clique em **OK** para sair da configuração do hub de eventos.
7. Marque a caixa de seleção **AuditLogs** para enviar as trilhas de auditoria para a conta de armazenamento. 
8. Marque a caixa de seleção **SignInLogs** para enviar os logs de entrada para a conta de armazenamento.
9. Clique em **Salvar** para salvar a configuração.
    ![Configurações de diagnóstico](./media/reporting-azure-monitor-diagnostics-azure-event-hub/DiagnosticSettings.png "Configurações de diagnóstico")

10. Após cerca de 15 minutos, verifique se esses eventos aparecem no hub de eventos. Para fazer isso, navegue até o hub de eventos a partir do portal e verifique se a contagem de **mensagens de entrada** é maior que zero. 
    ![Logs de auditoria](./media/reporting-azure-monitor-diagnostics-azure-event-hub/InsightsLogsAudit.png "Logs de auditoria")


## <a name="access-data-from-event-hubs"></a>Acessar dados dos Hubs de Eventos

Quando os dados aparecerem no hub de eventos, você poderá acessá-lo de duas maneiras.

* **Configurar uma ferramenta de SIEM com suporte para ler os dados**: a maioria das ferramentas exige a cadeia de conexão do hub de eventos e determinadas permissões para que a assinatura do Azure possa ler dados do hub de eventos. Aqui está uma lista parcial das ferramentas com integração ao Azure Monitor:
    1. **Splunk**: para obter mais informações sobre como integrar os logs do Azure AD ao Splunk, consulte [Como integrar os logs do Azure Active Directory ao Splunk usando o Azure Monitor](reporting-azure-monitor-diagnostics-splunk-integration.md).
    
    2. **IBM QRadar**: o Microsoft Azure DSM e protocolo dos Hub de Eventos do Microsoft Azure estão disponíveis para download no [site de suporte da IBM](http://www.ibm.com/support). Você pode [saber mais sobre a integração com o Azure aqui](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0).
    
    3. **SumoLogic**: siga [estas instruções](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub) para configurar o SumoLogic para consumir dados de um hub de eventos. 

* **Configurar ferramentas personalizadas para ler os dados**: se a SIEM atual ainda não tem suporte pelo diagnóstico do Azure Monitor, você pode configurar ferramentas personalizadas usando as APIs do Hub de Eventos. Para obter mais informações, consulte as [APIs do hub de eventos](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph).


## <a name="next-steps"></a>Próximas etapas

* [Integrar logs do Azure Active Directory ao Splunk usando o Azure Monitor (versão prévia)](reporting-azure-monitor-diagnostics-splunk-integration.md)
* [Interpretar o esquema de trilhas de auditoria no diagnóstico do Azure Monitor](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Interpretar o esquema de logs de entrada no diagnóstico do Azure Monitor](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)