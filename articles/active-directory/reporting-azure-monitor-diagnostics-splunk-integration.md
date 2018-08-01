---
title: Como integrar logs do Active Directory do Azure ao Splunk usando o Azure Monitor (visualização) | Microsoft Docs
description: Aprenda a integrar os logs do Active Directory do Azure ao Splunk usando o Azure Monitor (visualização)
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
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d1dd62d06c7e3ed634795604ce9660694ea073ca
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240305"
---
# <a name="integrate-azure-active-directory-logs-with-splunk-using-azure-monitor-preview"></a>Integrar logs do Azure Active Directory com o Splunk usando o Azure Monitor (visualização)

Neste artigo, você aprenderá a integrar os logs do Azure Active Directory ao Splunk usando o Monitor do Azure. Primeiro, você precisa rotear os logs para um hub de eventos do Azure e integrá-los ao Splunk.

## <a name="prerequisites"></a>Pré-requisitos

1. Hub de Eventos do Azure contendo logs de atividades do Azure AD. Saiba como [enviar seus logs de atividades para o Hub de eventos](reporting-azure-monitor-diagnostics-azure-event-hub.md). 
2. Use as seguintes [instruções](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md) para fazer o download do complemento do monitor do Azure para o Splunk e configurar sua instância do Splunk.

## <a name="tutorial"></a>Tutorial 

1. Abra sua instância do Splunk e clique em **Resumo dos dados**.
    ![Resumo de dados](./media/reporting-azure-monitor-diagnostics-splunk-integration/DataSummary.png "resumo de dados")

2. Navegue até a **Sourcetypes** e selecione **amal: aadal:audit** ![guia Sourcetypes](./media/reporting-azure-monitor-diagnostics-splunk-integration/sourcetypeaadal.png "Sourcetypes guia")

3. Você verá que o Azure logs de atividades do AD, conforme mostrado na figura a seguir.
    ![Os logs de atividade](./media/reporting-azure-monitor-diagnostics-splunk-integration/activitylogs.png "os Logs de atividade")

> [!NOTE]
> Se você não puder instalar um complemento em sua instância do Splunk (por exemplo, se estiver usando um proxy ou em execução no Splunk Cloud), poderá encaminhar esses eventos para o Coletor de Eventos HTTP do Splunk usando essa função [do Azure, que é acionado por novas mensagens no hub de eventos](https://github.com/Microsoft/AzureFunctionforSplunkVS)." 
>

## <a name="next-steps"></a>Próximas etapas

* [Interpretar o esquema de logs de auditoria no monitor do Azure](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Interpretar o esquema de logs de entrada no monitor do Azure](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [Perguntas frequentes e problemas conhecidos](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)