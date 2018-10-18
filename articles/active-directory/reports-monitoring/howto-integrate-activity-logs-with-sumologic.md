---
title: Como integrar os logs do Active Directory do Azure ao SumoLogic usando o Monitor do Azure (visualização) | Microsoft Docs
description: Aprenda a integrar os logs do Azure Active Directory ao SumoLogic usando o Azure Monitor (visualização)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d13eb22bd58dc7e680a27738549665bc2b691898
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49392203"
---
# <a name="integrate-azure-ad-logs-with-sumologic-by-using-azure-monitor-preview"></a>Integrar logs do Azure AD ao SumoLogic usando o Monitor do Azure (visualização)

Neste artigo, você aprenderá como integrar logs do Azure Active Directory (Azure AD) ao SumoLogic usando o Monitor do Azure. Você primeiro encaminhar os logs para um hub de eventos do Azure e, em seguida, integre o hub de eventos com o SumoLogic.

## <a name="prerequisites"></a>Pré-requisitos

Para usar esse recurso, você precisa de:
* Um hub de eventos do azure contendo logs de atividades do Microsoft Azure AD. Saiba como [enviar seus logs de atividades para um hub de eventos](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Um SumoLogic o logon único assinatura habilitada.

## <a name="steps-to-integrate-azure-ad-logs-with-sumologic"></a>Etapas para integrar os logs do Azure AD com o SumoLogic 

1. Primeiro, [transmitir os logs do Azure AD para um hub de eventos do Azure](quickstart-azure-monitor-stream-logs-to-event-hub.md).
2. Configurar a instância de SumoLogic [coletar logs do Azure Active Directory](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory).
3. [Instalar o aplicativo do Azure AD SumoLogic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards) para usar os painéis pré-configurados que fornecem uma análise em tempo real de seu ambiente.

 ![painel](./media/howto-integrate-activity-logs-with-sumologic/overview-dashboard.png)

## <a name="next-steps"></a>Próximas etapas

* [Interpretar o esquema de trilhas de auditoria no Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpretar o esquema de logs de entrada no Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Perguntas frequentes e problemas conhecidos](concept-activity-logs-azure-monitor.md#frequently-asked-questions)
