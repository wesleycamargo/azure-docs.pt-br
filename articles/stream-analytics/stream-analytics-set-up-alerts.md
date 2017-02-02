---
title: Configurar alertas para consultas no Stream Analytics | Microsoft Docs
description: "Noções básicas sobre alertas do Stream Analytics"
keywords: configurar alertas
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 9952e2cf-b335-4a5c-8f45-8d3e1eda2e20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/24/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c8463e68600edefecd0f3bdd19c13b13f676b82b


---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Configurar alertas para trabalhos do Stream Analytics do Azure
## <a name="introduction-monitor-page"></a>Introdução: Página do monitor
Você pode configurar alertas para disparar um alerta quando uma métrica atinge uma condição que você especifica.

Por exemplo, “Se Eventos de Saída dos últimos 15 minutos forem inferiores a 100, enviar notificação por email à ID de email: xyz@company.com”.

As regras podem ser configuradas em métricas por meio do portal ou [de modo programático](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) pelos dados dos Logs de Operação.

## <a name="set-up-alerts-through-the-azure-classic-portal"></a>Configurar alertas por meio do Portal Clássico do Azure
Há duas maneiras de configurar alertas no Portal Clássico do Azure:  

1. Na guia **Monitorar** do seu trabalho do Stream Analytics  
2. No Log de Operações nos Serviços de gerenciamento  

## <a name="set-up-alert-through-the-monitor-tab-of-the-job-in-the-portal"></a>Configurar alertas por meio da guia Monitor do trabalho no portal
1. Selecione a métrica na guia Monitorar, clique no botão **Adicionar Regra** na parte inferior do painel e configure as regras.  
   
   ![Painel](./media/stream-analytics-set-up-alerts/01-stream-analytics-set-up-alerts.png)  
2. Defina o nome e a descrição do alerta  
   
   ![Criar Regra](./media/stream-analytics-set-up-alerts/02-stream-analytics-set-up-alerts.png)  
3. Insira os limites, a janela de avaliação do alerta e as ações para o alerta  
   
   ![Definir Condições](./media/stream-analytics-set-up-alerts/03-stream-analytics-set-up-alerts.png)  

## <a name="set-up-alerts-through-the-operations-logs"></a>Configurar alertas pelos Logs de operações
1. Vá para a guia **Alertas** nos Serviços de Gerenciamento, no [Portal Clássico do Azure](https://manage.windowsazure.com).  
2. Clique em **Adicionar Regra**  
   
   ![Critérios](./media/stream-analytics-set-up-alerts/04-stream-analytics-set-up-alerts.png)  
3. Defina o nome e a descrição do alerta. Selecione 'Stream Analytics' como Tipo de Serviço e o nome do trabalho como o Nome do Serviço.  
   
   ![Definir Alerta](./media/stream-analytics-set-up-alerts/05-stream-analytics-set-up-alerts.png)  

## <a name="set-up-alerts-in-the-azure-portal"></a>Configurar alertas no Portal do Azure
No portal do Azure, navegue até o trabalho do Stream Analytics do qual você deseja receber alertas e clique na seção **Monitoramento** .  Na folha **Métrica** que é aberta, clique no comando **Adicionar alerta**.

  ![Configuração do portal do Azure](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

Você pode nomear a regra de alerta e escolher uma descrição que aparecerá no email de notificação.

Ao selecionar Métricas, você escolherá uma condição e um valor de limite para a métrica.

  ![Seleção de métrica no portal do Azure](./media/stream-analytics-set-up-alerts/07-stream-analytics-set-up-alerts.png)  

Para obter mais detalhes sobre como configurar alertas no portal do Azure, consulte [Receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  

## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Nov16_HO3-->


