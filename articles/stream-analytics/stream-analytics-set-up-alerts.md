---
title: Configurar alertas de monitoramento para trabalhos do Azure Stream Analytics
description: Este artigo descreve como usar o Portal do Azure para configurar o monitoramento e alertas para trabalhos do Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 727747d84d0db32c73fc1a200bcea7e5c149d24b
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53554904"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Configurar alertas para trabalhos do Stream Analytics do Azure
Você pode configurar alertas para disparar um alerta quando uma métrica atinge uma condição que você especifica. Por exemplo, você pode configurar um alerta para uma condição semelhante à seguinte:

`If there are zero input events in the last 5 minutes, send email notification to sa-admin@example.com`

As regras podem ser configuradas em métricas por meio do portal ou [de modo programático](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) pelos dados dos Logs de Operação.

## <a name="set-up-alerts-in-the-azure-portal"></a>Configurar alertas no Portal do Azure
1. No Portal do Azure, abra o trabalho do Stream Analytics para o qual você deseja criar um alerta. 

2. Na folha **Trabalho**, clique na seção **Monitoramento**.  

3. Na folha **Métrica**, clique no comando **Adicionar alerta**.

      ![Configuração de alertas do Stream Analytics do portal do Azure](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

4. Insira um nome e uma descrição.

5. Use os seletores para definir a condição sob a qual o alerta será enviado.

6. Forneça informações sobre qual deve ser o destino do alerta.

      ![Configurar um alerta para um trabalho do Azure Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-add-alert.png)  

Para obter mais detalhes sobre como configurar alertas no portal do Azure, consulte [Receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  


## <a name="get-help"></a>Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

