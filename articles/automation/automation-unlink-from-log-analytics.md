---
title: Desvincular conta de Automação do Azure do Log Analytics
description: Este artigo fornece uma visão geral de como desvincular sua conta de Automação do Azure de um espaço de trabalho do Log Analytics.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/04/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 4928f1b92e84fc2b960c1f41e7531de9e346dfa2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-unlink-your-automation-account-from-a-log-analytics-workspace"></a>Como desvincular sua conta de Automação de um espaço de trabalho do Log Analytics

A Automação do Azure integra-se ao Log Analytics não só para dar suporte ao monitoramento de seus trabalhos de runbook em todas as suas contas de Automação, mas ela também é necessária quando você importa as seguintes soluções que dependem do Log Analytics:

* [Gerenciamento de atualizações](../operations-management-suite/oms-solution-update-management.md)
* [Controle de alterações](../log-analytics/log-analytics-change-tracking.md)
* [Iniciar/parar VMs durante os horários fora de pico](automation-solution-vm-management.md)

Caso decida que não quer mais integrar sua conta de Automação ao Log Analytics, você poderá desvincular a conta diretamente no Portal do Azure.  Antes de prosseguir, você precisa remover as soluções mencionadas anteriormente, caso contrário, esse processo será impedido de continuar. Examine o tópico sobre a solução específica que você importou para entender as etapas necessárias para removê-la.

Depois de remover essas soluções, você poderá executar as etapas a seguir para desvincular sua conta de Automação.

> [!NOTE]
> Algumas soluções, incluindo versões anteriores da solução de monitoramento do Azure SQL, podem ter criado ativos de automação e também podem precisar ser removidas antes de desvincular o espaço de trabalho.

## <a name="unlink-workspace"></a>Desvincular o espaço de trabalho

1. No portal do Azure, abra sua conta da Automação e, na página da conta da Automação, selecione **Desvincular o espaço de trabalho** na seção **Recursos Relacionados** à esquerda.

   ![Opção Desvincular espaço de trabalho](media/automation-unlink-from-log-analytics/automation-unlink-workspace-option.png)

1. Na página Desvincular o espaço de trabalho, clique em **Desvincular o espaço de trabalho**.

   ![Página Desvincular espaço de trabalho](media/automation-unlink-from-log-analytics/automation-unlink-workspace-blade.png).

   Você receberá uma solicitação perguntando se deseja prosseguir.

1. Enquanto a Automação do Azure tenta desvincular a conta do seu espaço de trabalho do Log Analytics, você pode acompanhar o progresso no menu **Notificações**.

Se você tiver usado a solução Gerenciamento de Atualizações, como opção, convém remover os itens a seguir que não serão mais necessários após a remoção da solução.

* Agendas de atualizações.  Cada uma terá nomes que correspondam às implantações de atualizações que você criou)

* Grupos de trabalho híbridos criados para a solução.  Cada um receberá um nome semelhante a machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Se você tiver usado a solução Iniciar/parar VMs durante os horários fora de pico, como opção, convém remover os itens a seguir que não serão mais necessários após a remoção da solução.

* Iniciar e parar agendas de runbook da VM
* Iniciar e parar runbooks da VM
* variáveis

## <a name="next-steps"></a>Próximas etapas

Para reconfigurar a conta de automação para integração com o Log Analytics, consulte [Encaminhar status do trabalho e fluxos de trabalho de Automação para Log Analytics](automation-manage-send-joblogs-log-analytics.md).