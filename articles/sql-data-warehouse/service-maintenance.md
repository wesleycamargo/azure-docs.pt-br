---
title: Manutenção planejada - SQL Data Warehouse do Azure | Microsoft Docs
description: Aprenda como preparar-se para eventos de manutenção planejada no SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/19/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: c1e8f94a0131ace6354d070e932e414a1897260e
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53166295"
---
# <a name="planning-for-maintenance-on-your-azure-sql-data-warehouse"></a>Planejar a manutenção no SQL Data Warehouse do Azure

Saiba como preparar-se para eventos de manutenção planejados no SQL Data Warehouse do Azure.

## <a name="what-is-a-planned-maintenance-event"></a>O que é um evento de manutenção planejada?
Um evento de manutenção planejado é uma janela de tempo em que o data warehouse precisa estar offline para uma operação de manutenção. Esses eventos são oportunidades para aplicar atualizações de serviço, novos recursos ou patches. 

## <a name="frequency"></a>Frequência
Em média, pelo menos um evento de manutenção planejada ocorre a cada mês. 

## <a name="notifications-and-downtime"></a>Notificações e tempo de inatividade
Você receberá uma notificação antes de cada evento de manutenção planejada. O evento de manutenção cancela todas as consultas em execução e coloca o data warehouse offline. O tempo de inatividade esperado para cada data warehouse é de aproximadamente 30 minutos. Você pode esperar uma notificação quando a manutenção for concluída. 

## <a name="setting-up-alerts"></a>Configurar Alertas

É recomendável usar o [Azure Monitor](../azure-monitor/platform/alerts-activity-log-service-notifications.md) para configurar alertas de log de manutenção planejada. Os alertas podem ajudá-lo a planejar a manutenção necessária para minimizar o impacto nos negócios. 

Para configurar notificações, use estas [instruções de alerta de log](../azure-monitor/platform/alerts-activity-log-service-notifications.md). 

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre monitoramento, consulte [Monitorar a carga de trabalho](sql-data-warehouse-manage-monitor.md).
