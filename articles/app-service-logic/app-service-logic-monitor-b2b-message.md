---
title: Monitorar mensagens de B2B | Microsoft Docs
description: Como monitorar a conta do Inegration
author: padmavc
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 63ba6e8fec8df1cdbc9928b011130f47c5facf89
ms.openlocfilehash: 8ce3585a557f6b2c9fa4a4c85142cdce9357a90b


---
# <a name="monitor-b2b-messages"></a>Monitorar mensagens B2B
A comunicação B2B envolve trocas de mensagens entre dois processos de negócios ou aplicativos em execução. A relação define um contrato entre os processos de negócios. Quando a comunicação tiver sido estabelecida, precisa haver uma maneira de monitorar se a comunicação funcionará como esperado.  O rastreamento de mensagens foi implementado para os protocolos B2B: AS2, X12 e EDIFACT.  Você pode configurar sua Conta de Integração para usar o diagnóstico e obter detalhes e depuração mais avançados

## <a name="prerequisites"></a>Pré-requisitos
* Uma conta do Azure; você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma Conta de Integração; você pode criar uma [Conta de Integração](app-service-logic-enterprise-integration-create-integration-account.md)
* Um Aplicativo Lógico; você pode criar um [Aplicativo Lógico](app-service-logic-create-a-logic-app.md) e habilitar o registro em log; veja as etapas [aqui](app-service-logic-monitor-your-logic-apps.md)

## <a name="enable-logging-for-an-integration-account"></a>Habilitar o registro em log para uma conta de integração
Você pode habilitar o registro para uma conta de integração com o **portal do Azure** ou com o **Monitor**

### <a name="enable-logging-with-azure-portal"></a>Habilitar registro em log com o Portal do Azure

1. Selecione **conta integração** e selecione **logs de diagnóstico** 
![selecionar conta de integração](./media/app-service-logic-monitor-integration-account/Pic5.png)  

2. Selecione a **Assinatura** e o **Grupo de Recursos**, a **Conta de Integração** no Tipo de Recurso e selecione sua **Conta de Integração** na lista suspensa Recurso para habilitar o diagnóstico.  Clique em **Ativar Diagnóstico** para habilitar o diagnóstico para a Conta de Integração selecionada               
![selecionar Conta de Integração](./media/app-service-logic-monitor-integration-account/Pic2.png) 

3. Selecionar o status **ATIVADA**       
![Ativar diagnóstico](./media/app-service-logic-monitor-integration-account/Pic3.png) 

4. Selecione **Enviar para o Log Analytics** e configure o Log Analytics para a emissão de dados               
![Ativar diagnóstico](./media/app-service-logic-monitor-integration-account/Pic4.png)

### <a name="enable-logging-with-monitor"></a>Habilitar o registro em log com o Monitor

1. Selecione **Monitor** e clique em **Logs de diagnóstico**   
![selecionar o Monitor](./media/app-service-logic-monitor-integration-account/Pic1.png)

2. Selecione a **Assinatura** e o **Grupo de Recursos**, a **Conta de Integração** no Tipo de Recurso e selecione sua **Conta de Integração** na lista suspensa Recurso para habilitar o diagnóstico.  Clique em **Ativar Diagnóstico** para habilitar o diagnóstico para a Conta de Integração selecionada               
![selecionar Conta de Integração](./media/app-service-logic-monitor-integration-account/Pic2.png)

3. Selecionar o status **ATIVADA**       
![Ativar diagnóstico](./media/app-service-logic-monitor-integration-account/Pic3.png) 

4. Selecione **Enviar para o Log Analytics** e configure o Log Analytics para a emissão de dados ![Ativar diagnósticos](./media/app-service-logic-monitor-integration-account/Pic4.png)

## <a name="extending-your-solutions"></a>Estendendo as soluções
Além do **Log Analytics**, você pode configurar sua Conta de Integração e [Aplicativos Lógicos](./app-service-logic-monitor-your-logic-apps.md) para um Hub de Eventos ou a Conta de Armazenamento         
![Configurações de Diagnóstico do Azure](./media/app-service-logic-monitor-your-logic-apps/diagnostics.png)

Você pode usar essa telemetria do Hub de Eventos ou Armazenamento em outros serviços, como o [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) e o [Power BI](https://powerbi.com) para monitorar em tempo real seus fluxos de trabalho de integração.

## <a name="supported-tracking-schema"></a>Esquema de rastreamento com suporte
Damos suporte aos seguintes tipos de esquema de rastreamento.  Todos eles corrigiram esquemas, exceto Tipo personalizado.

* [Esquema de Controle Personalizado](app-service-logic-track-integration-account-custom-tracking-shema.md)   
* [Esquema de Controle do AS2](app-service-logic-track-integration-account-as2-tracking-shemas.md)   
* [Esquema de Controle do X12](app-service-logic-track-integration-account-x12-tracking-shemas.md)  

## <a name="next-steps"></a>Próximas etapas
[Rastreando mensagens B2B no Portal do OMS](app-service-logic-track-b2b-messages-omsportal.md "Tracking B2B messages")   
[Saiba mais sobre o Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack") 




<!--HONumber=Nov16_HO3-->


