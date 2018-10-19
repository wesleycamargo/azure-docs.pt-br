---
title: Agendamentos de manutenção do Azure (versão prévia) | Microsoft Docs
description: O agendamento de manutenção permite que os clientes planejem os eventos de manutenção agendada necessários que o serviço do SQL Data Warehouse do Azure usa para distribuir novos patches, atualizações e recursos.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 10/06/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 6fbf914c8035344d33e8d2739fb9d92d5757c3d1
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228026"
---
# <a name="viewing-a-maintenance-schedule"></a>Exibir um agendamento de manutenção 

## <a name="portal"></a>Portal

Por padrão, todas as instâncias recém-criadas do SQL Data Warehouse do Azure têm uma janela de manutenção Primária e Secundária de oito horas definida pelo sistema aplicada durante a implantação, o que pode ser editado assim que a implantação é concluída. Sem notificação prévia, nenhuma manutenção ocorrerá fora das janelas de manutenção especificadas.
Conclua as seguintes etapas para exibir o agendamento de manutenção que foi aplicado ao seu data warehouse no portal.

Conclua as seguintes etapas para exibir o agendamento de manutenção que foi aplicado ao seu data warehouse no portal.
1.  Entre no [portal do Azure](https://portal.azure.com/).
2.  Selecione o data warehouse que você deseja exibir. 
3.  O SQL Data Warehouse do Azure selecionado será aberto na folha de visão geral. O Agendamento de manutenção aplicado ao data warehouse selecionado será mostrado abaixo do Agendamento de manutenção (versão prévia).

![Folha de visão geral](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="next-steps"></a>Próximas etapas
[Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) sobre como criar, exibir e gerenciar alertas usando o Azure Monitor.
[Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sobre ações de webhook para regras de alerta de log.
[Saiba mais](https://docs.microsoft.com/azure/service-health/service-health-overview) sobre a Integridade do Serviço do Azure


