---
title: Agendamentos de manutenção do Azure (versão prévia) | Microsoft Docs
description: O agendamento de manutenção permite que os clientes planejem os eventos de manutenção agendada necessários que o serviço do SQL Data Warehouse do Azure usa para distribuir novos patches, atualizações e recursos.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 10/07/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: a6eedc0bac7aab69a9138f4f63d0d9d802e74dfc
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228032"
---
# <a name="change-a-maintenance-schedule"></a>Alterar um agendamento de manutenção 

## <a name="portal"></a>Portal
Um agendamento de manutenção pode ser atualizado ou alterado a qualquer momento. No entanto, se a instância selecionada atualmente estiver passando pelo ciclo de manutenção ativa, as configurações serão salvas e somente ficarão ativas durante o próximo período de manutenção identificado. [Saiba mais](https://docs.microsoft.com/azure/service-health/resource-health-overview) sobre como monitorar seu data warehouse durante um evento de manutenção ativa. 

Na versão prévia, pediremos que você selecione duas janelas de manutenção durante um período de sete dias. Cada janela de manutenção pode ter entre três e oito horas. No momento, três horas é a opção mais curta disponível. A manutenção pode ocorrer a qualquer momento dentro de uma janela de manutenção identificada, porém não ocorrerá fora dessas janelas de tempo identificadas com notificação prévia. Além disso, ocorrerá uma breve perda de conectividade, pois o serviço implanta o novo código em seu data warehouse. 

## <a name="identifying-the-primary-and-secondary-windows"></a>Identificando as janelas principal e secundária

As janelas principal e secundária precisam ser identificadas em intervalos de dia separados, ou seja, janela principal (terça-feira a quinta-feira), janela secundária (sábado a domingo)

Conclua as seguintes etapas para alterar o agendamento de manutenção que foi aplicado ao seu data warehouse no portal.
1.  Entre no [portal do Azure](https://portal.azure.com/).
2.  Selecione o data warehouse que você deseja atualizar. A página será aberta na folha de visão geral. 
3.  A página de configurações de Agendamento de manutenção pode ser acessada clicando no link Resumo de agendamento de manutenção (versão prévia) na folha de visão geral ou por meio da opção Agendamento de manutenção no Menu Recursos à esquerda.  

    ![Opções da folha Visão geral](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. Você pode identificar o intervalo de dia preferencial para a janela de manutenção principal usando os botões de opção na parte superior da página. Essa seleção determina se a janela principal ocorrerá em um dia da semana ou no final de semana. Sua seleção atualizará corretamente os valores da lista suspensa abaixo. Durante a versão prévia, algumas regiões talvez ainda não deem suporte ao conjunto completo de opções de Dia disponíveis. Esses valores serão atualizado nos próximos meses.

   ![Folha de configurações de manutenção](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. Escolha suas janelas de manutenção principal e secundária usando as listas suspensas Dia, Hora de início e Janela de tempo abaixo. O Resumo de agendamento na parte inferior da folha será atualizado com base nos valores de lista suspensa selecionados.

#### <a name="dropdown-options"></a>Opções do menu suspenso
- Dia: dia preferencial para executar a manutenção durante a janela selecionada.
- Hora de início: hora de início preferencial da janela de manutenção.
- Janela de tempo: duração preferencial da janela de tempo.

  Clique em Salvar depois de selecionar as janelas de manutenção preferenciais. Uma mensagem de confirmação será exibida confirmando que seu novo agendamento não está ativo. Se você estiver salvando um agendamento em uma região que ainda não dê suporte ao Agendamento de manutenção, a seguinte mensagem será exibida. Suas configurações serão salvas e ficarão ativas quando o recurso ficar disponível em sua região selecionada.    

    ![Notificação do sistema de que não está ativo na de região](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>Próximas etapas
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sobre ações de webhook para regras de alerta de log.
- [Saiba mais](https://docs.microsoft.com/azure/service-health/service-health-overview) sobre a Integridade do Serviço do Azure


