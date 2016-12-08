---
title: "Acompanhe a integridade do serviço do Azure usando Logs de atividade do Azure Monitor | Microsoft Docs"
description: "Saiba quando o Azure sofreu interrupções de serviço ou degradação do desempenho. "
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 30db9258-98e6-4855-abcd-f06c06277aa8
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d31ee12c783b845e0a8ff1791d21b2ef9013eee3


---
# <a name="track-azure-service-health-using-azure-monitor-activity-logs"></a>Acompanhe a integridade do serviço do Azure usando Logs de atividade do Azure Monitor
O Azure publica sempre que há uma degradação no desempenho ou interrupção do serviço. Você pode navegar por esses eventos no Portal do Azure, e também pode usar a [API REST](https://msdn.microsoft.com/library/azure/dn931927.aspx) ou o [SDK .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para acessar o conjunto completo de eventos programaticamente.

## <a name="browse-the-service-health-logs-for-your-subscription"></a>Procure os logs de integridade de serviço para sua assinatura
1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Em **Página Inicial** você deve ver um bloco chamado **Integridade do serviço**. Clique nele.
   
    ![Home](./media/insights-service-health/Insights_Home.png)
3. Você verá uma lista de todas as regiões no Azure. Clique em qualquer região para exibir a consulta do Log de atividade que mostra os incidentes de serviço que afetaram qualquer uma das suas assinaturas nas últimas 24 horas.
   
    ![Integridade do serviço de assinatura do Log de atividade](./media/insights-service-health/AzureActivityLogServiceHealth3.png)
4. Você pode ver os detalhes de um incidente individual clicando nele na tabela.
5. Altere o **Timespan** para ver um período de tempo maior.

## <a name="next-steps"></a>Próximas etapas
* [Monitore a disponibilidade e a capacidade de resposta de qualquer página da Web](../application-insights/app-insights-monitor-web-app-availability.md) com o Application Insights para que você possa descobrir se a página está inativa.




<!--HONumber=Nov16_HO3-->


