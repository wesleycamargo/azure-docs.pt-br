---
title: Como integrar logs do Microsoft Azure Active Directory ao Splunk usando o Azure Monitor (visualização) | Microsoft Docs
description: Aprenda a integrar os logs do Microsoft Azure Active Directory ao Splunk usando o Azure Monitor (visualização)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: c4b605b6-6fc0-40dc-bd49-101d03f34665
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: bafd130b2fbd22f85407590ff2e86ecf5c6dfe67
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2018
ms.locfileid: "42145036"
---
# <a name="integrate-azure-ad-logs-with-splunk-by-using-azure-monitor-preview"></a>Integrar logs do Microsoft Azure Active Directory com o Splunk usando o Azure Monitor (visualização)

Neste artigo, você aprenderá a integrar os logs do Azure Active Directory (Microsoft Azure AD) ao Splunk usando o Azure Monitor. Primeiro encaminhe os logs para um hub de eventos do Azure e, em seguida, integre o hub de eventos Splunk.

## <a name="prerequisites"></a>Pré-requisitos

Para usar esse recurso, você precisa de:
* Um hub de eventos do azure contendo logs de atividades do Microsoft Azure AD. Saiba como [enviar seus logs de atividades para um hub de eventos](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Complemento Azure Monitor para Splunk. [Baixar e configurar sua instância do Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md).

## <a name="tutorial"></a>Tutorial 

1. Abra sua instância do Splunk e clique em **Resumo dos dados**.

    ![Botão "Resumo de dados"](./media/tutorial-integrate-activity-logs-with-splunk/DataSummary.png)

2. Selecione a guia **Sourcetypes** e, em seguida, selecione **amal: aadal:audit**

    ![Na guia Sourcetypes de resumo de dados](./media/tutorial-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    Você verá que os logs de atividade do Azure Active Directory são mostrados na figura a seguir:

    ![Logs de atividade](./media/tutorial-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Se você não puder instalar um complemento em sua instância do Splunk (por exemplo, se estiver usando um proxy ou em execução no Splunk Cloud), poderá encaminhar esses eventos para o Coletor de Eventos HTTP. Para fazer isso, use esta [função do Azure](https://github.com/Microsoft/AzureFunctionforSplunkVS), que é disparada por novas mensagens no hub de eventos. 
>

## <a name="next-steps"></a>Próximas etapas

* [Interpretar o esquema de trilhas de auditoria no Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpretar o esquema de logs de entrada no Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Perguntas frequentes e problemas conhecidos](overview-activity-logs-in-azure-monitor.md#frequently-asked-questions)
