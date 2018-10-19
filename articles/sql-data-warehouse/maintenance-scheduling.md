---
title: Agendamentos de manutenção do Azure (versão prévia) | Microsoft Docs
description: O agendamento de manutenção permite que os clientes planejem os eventos de manutenção agendada necessários que o serviço do SQL Data Warehouse do Azure usa para distribuir novos patches, atualizações e recursos.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 09/20/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 85e8327d1cac17059b2e91b1ea21becc23002c8e
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228040"
---
# <a name="using-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Usando agendamentos de manutenção para gerenciar atualizações e manutenção

O agendamento de manutenção do SQL Data Warehouse do Azure está agora na versão prévia. Esse novo recurso integra-se perfeitamente às notificações de manutenção planejada da Integridade do Serviço, ao Monitor de verificação do Resource Health e ao serviço de agendamento de manutenção do SQL Data Warehouse do Azure.

O agendamento de manutenção permite que você agende uma janela de tempo para quando for conveniente receber novos recursos, atualizações e patches. Os clientes selecionarão uma janela de manutenção principal e uma secundária em um período de sete dias, ou seja, de sábado às 22h a domingo à 1h (principal) e quarta-feira das 19h às 22h (secundária). Se não for possível realizar a manutenção durante a janela de manutenção principal, tentaremos novamente durante a janela de manutenção secundária.

Todas as instâncias recém-criados do SQL Data Warehouse do Azure terão um agendamento de manutenção definida pelo sistema aplicada durante a implantação. O agendamento poderá ser editado assim que a implantação for concluída.

Cada janela de manutenção pode ter entre três e oito horas. No momento, três horas é a opção mais curta disponível. A manutenção pode ocorrer a qualquer momento dentro da janela, e você deve esperar uma breve perda de conectividade, pois o serviço implanta um novo código em seu data warehouse. 

Durante a versão prévia do recurso, estamos pedindo que os clientes identifiquem suas janelas principal e secundária dentro dos intervalos de dia separados.   
Todas as operações de manutenção devem ser concluídas dentro das janelas de manutenção agendada e não ocorrerá nenhuma manutenção fora das janelas de manutenção especificadas sem notificação prévia. Se o data warehouse estiver em pausa durante a manutenção agendada, ele será atualizado durante a operação de retomada.  


## <a name="alerts-and-monitoring"></a>Alertas e monitoramento

A integração perfeita com as notificações de Integridade do Serviço e o monitor de verificação do Resource Health permite que os clientes se mantenham informados sobre a atividade de manutenção iminente. A nova automação aproveita o Azure Monitor e permite que os clientes determinem como desejam ser notificados sobre eventos de manutenção iminentes e quais fluxos automatizados devem ser disparados para gerenciar o tempo de inatividade e minimizar o impacto nas operações.

Todos os eventos de manutenção são precedidos por uma notificação antecipada de 24 horas. Para minimizar o tempo de inatividade da instância, você deve garantir que não haja transações de execução longa em seu data warehouse antes do início do período de manutenção escolhido. Quando a manutenção for iniciada, todas as sessões ativas serão canceladas, as transações não confirmadas serão revertidas e o data warehouse apresentará uma breve perda de conectividade. Você será notificado imediatamente quando a manutenção for concluída em seu data warehouse. 

Se você receber uma notificação antecipada de que a manutenção ocorrerá, mas não for possível realizar a manutenção durante esse período, você receberá uma notificação de cancelamento. A manutenção será retomada durante o próximo período de manutenção agendada.
 
Todos os eventos de manutenção ativos serão exibidos na seção 'Integridade do Serviço – Manutenção Planejada'. Um registro completo de eventos anteriores será mantido como parte do histórico de Integridade do Serviço. A manutenção pode ser monitorada por meio do painel do portal de verificação de Integridade do Serviço do Azure durante um evento ativo.

### <a name="maintenance-schedule-availability"></a>Disponibilidade de agendamento de manutenção

Mesmo se o agendamento de manutenção ainda não estiver disponível em sua região selecionada, você poderá exibir e editar o agendamento de manutenção a qualquer momento. Quando o agendamento de manutenção estiver disponível em sua região, o agendamento identificado ficará ativo imediatamente no seu data warehouse.


## <a name="next-steps"></a>Próximas etapas

- [Saiba mais](viewing-maintenance-schedule.md) sobre como exibir um agendamento de manutenção 
- [Saiba mais](changing-maintenance-schedule.md) sobre como alterar um agendamento de manutenção
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) sobre como criar, exibir e gerenciar alertas usando o Azure Monitor
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sobre as ações de webhook para regras de alerta de log
- [Saiba mais](https://docs.microsoft.com/azure/service-health/service-health-overview) sobre a Integridade do Serviço do Azure







