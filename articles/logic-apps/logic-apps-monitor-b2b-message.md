---
title: "Monitorar mensagens em transações B2B – Alicativos lógicos do Azure | Microsoft Docs"
description: "Monitore e controle mensagens durante a comunicação B2B entre processos e aplicativos usando Aplicativos Lógicos na sua Conta de Integração."
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5913c81088724ef946ae147f4f3154fa6aefd22e
ms.openlocfilehash: dc760b4c08d0e1afff3bc1276f6ed2367d67629e
ms.lasthandoff: 03/01/2017


---
# <a name="start-or-enable-logging-of-as2-x12-and-edifact-messages-to-monitor-success-errors-and-message-properties"></a>Iniciar ou habilitar o registro em log de mensagens AS2, X12 e EDIFACT para monitorar o êxito, os erros e as propriedades da mensagem

A comunicação B2B envolve trocas de mensagens entre dois processos de negócios ou aplicativos em execução. A relação define um contrato entre os processos de negócios. Depois de a comunicação ser estabelecida, você poderá configurar o monitoramento de mensagens para verificar se a comunicação está funcionando conforme o esperado. Você pode configurar sua conta de integração para usar o diagnóstico e obter detalhes e depuração mais avançados.

O rastreamento de mensagens está disponível para os protocolos B2B: AS2, X12 e EDIFACT. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure; você pode criar uma [conta gratuita](https://azure.microsoft.com/free).
* Uma Conta de Integração; você pode criar uma [Conta de Integração](logic-apps-enterprise-integration-create-integration-account.md).
* Um Aplicativo Lógico; você pode criar um [Aplicativo Lógico](logic-apps-create-a-logic-app.md) e [habilitar o registro em log](logic-apps-monitor-your-logic-apps.md).

## <a name="enable-logging-for-an-integration-account"></a>Habilitar o registro em log para uma conta de integração

Você pode habilitar o registro em log para uma conta de integração com o **portal do Azure** ou com o **Monitor**.

### <a name="enable-logging-with-azure-portal"></a>Habilitar registro em log com o Portal do Azure

1. Selecione sua conta de integração, em seguida, selecione **Logs de diagnóstico**.

    ![Selecione sua conta de integração](media/logic-apps-monitor-b2b-message/pic5.png)

2. Selecione sua **Assinatura** e **Grupo de Recursos**. Em **Tipo de Recurso**, selecione **Contas de Integração**. Em **Tipo de Recurso**, selecione sua conta de integração. Clique em **Ativar Diagnósticos** para habilitar o diagnóstico para a conta de integração selecionada.

    ![Configurar o diagnóstico para sua conta de integração](media/logic-apps-monitor-b2b-message/pic2.png)

3. Selecione o status **ATIVADA**.

    ![Ativar o status de diagnóstico](media/logic-apps-monitor-b2b-message/pic3.png)

4. Selecione **Enviar para o Log Analytics** e configure o Log Analytics para a emissão de dados.

    ![Enviar dados de diagnóstico para um registro](media/logic-apps-monitor-b2b-message/pic4.png)

### <a name="enable-logging-with-monitor"></a>Habilitar o registro em log com o Monitor

0. Selecione **Monitorar** e clique em **Logs de diagnóstico**.

    ![Selecione Monitorar e clique em Logs de diagnóstico](media/logic-apps-monitor-b2b-message/pic1.png)

0. Selecione sua **Assinatura** e **Grupo de Recursos**. Em **Tipo de Recurso**, selecione **Contas de Integração**. Em **Tipo de Recurso**, selecione sua conta de integração. Clique em **Ativar Diagnósticos** para habilitar o diagnóstico para a conta de integração selecionada.

    ![Configurar o diagnóstico para sua conta de integração](media/logic-apps-monitor-b2b-message/pic2.png)

0. Selecione o status **ATIVADA**.

    ![Ativar o status de diagnóstico](media/logic-apps-monitor-b2b-message/pic3.png) 

0. Selecione **Enviar para o Log Analytics** e configure o **Log Analytics** para a emissão de dados.

    ![Enviar dados de diagnóstico para um registro](media/logic-apps-monitor-b2b-message/pic4.png)

## <a name="extend-your-solutions"></a>Estender suas soluções

Além do **Log Analytics**, você pode configurar sua conta de integração e [Aplicativos Lógicos](./logic-apps-monitor-your-logic-apps.md) para um Hub de Eventos ou a Conta de Armazenamento.

![Configurações de Diagnóstico do Azure](./media/logic-apps-monitor-your-logic-apps/diagnostics.png)

Você pode usar essa telemetria do Hub de Eventos ou Armazenamento em outros serviços, como o [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) e o [Power BI](https://powerbi.com) para monitorar em tempo real seus fluxos de trabalho de integração.

## <a name="supported-tracking-schema"></a>Esquema de rastreamento com suporte

Há suporte para esses tipos de esquema de rastreamento que têm esquemas fixos, exceto o tipo Personalizado.

* [Esquema de Controle Personalizado](logic-apps-track-integration-account-custom-tracking-schema.md)
* [Esquema de Controle do AS2](logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquema de Controle do X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Próximas etapas

[Rastreando mensagens B2B no Portal do OMS](logic-apps-track-b2b-messages-omsportal.md "Rastreando mensagens B2B")

[Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")


