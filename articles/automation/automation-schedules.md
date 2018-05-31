---
title: Agendas na Automação do Azure
description: As agendas de automação são usadas para agendar o início automático de runbooks na Automação do Azure. Descreve como criar e gerenciar uma agenda para que você possa iniciar um runbook automaticamente em um determinado momento ou em um agendamento recorrente.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/08/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 97922acdd6b14e594e4274b0db0f930a02b3b7c3
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33936953"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Agendando um runbook na Automação do Azure

Para agendar um runbook na Automação do Azure para iniciar em um horário específico, você deve vinculá-lo a uma ou mais agendas. Uma agenda pode ser configurada para ser executada uma vez ou de forma recorrente a cada hora ou diariamente para runbooks no portal do Azure. Você também pode agendá-los para semanal, mensal, dias específicos da semana ou dias do mês ou um determinado dia do mês. Um runbook pode ser vinculado a várias agendas, e uma agenda pode ter vários runbooks vinculados a ela.

> [!NOTE]
> Atualmente, os agendamentos não dão suporte às configurações DSC de Automação do Azure.

## <a name="windows-powershell-cmdlets"></a>Cmdlets do Windows PowerShell

Os cmdlets na tabela a seguir são usados para criar e gerenciar agendas com o Windows PowerShell na Automação do Azure. Eles são enviados como parte do [módulo do PowerShell do Azure](/powershell/azure/overview).

| Cmdlets | DESCRIÇÃO |
|:--- |:--- |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Recupera uma agenda. |
| [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Cria uma nova agenda. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Remove uma agenda. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Define as propriedades de uma agenda existente. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/set-azurermautomationscheduledrunbook) |Recupera runbooks agendados. |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Associa um runbook a uma agenda. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Dissocia um runbook de uma agenda. |

## <a name="creating-a-schedule"></a>Criando uma agenda

Você pode criar uma nova agenda para runbooks no portal do Azure ou com o Windows PowerShell.

> [!NOTE]
> A Automação do Azure usa os módulos mais recentes de sua conta de Automação quando um novo trabalho agendado é executado.  Para evitar o impacto em seus runbooks e os processos que eles automatizam, primeiro você deve testar quaisquer runbooks que tenham agendas vinculadas com uma conta de Automação dedicada a teste.  Isso valida que seus runbooks agendados continuam funcionando corretamente e, caso não continuem, você pode solucionar problemas e aplicar as alterações necessárias antes de migrar a versão atualizada do runbook para produção.
> Sua conta de Automação não recebe automaticamente nenhuma nova versão dos módulos, a menos que você os tenha atualizado manualmente selecionando a opção [Atualizar Módulos do Azure](automation-update-azure-modules.md) em **Módulos**.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Para criar uma nova agenda no portal do Azure

1. No portal do Azure, em sua conta de Automação, selecione **Agendas** na seção **Recursos Compartilhados** à esquerda.
1. Clique em **Adicionar um agendamento** na parte superior da página.
1. No painel **Novo agendamento**, digite um **Nome** e, opcionalmente, uma **Descrição** para a nova agenda.
1. Selecione se a agenda deve ser executada uma vez ou de forma recorrente selecionando **Uma vez** ou **Recorrência**. Se você selecionar **Uma vez**, especifique uma **Hora de início** e clique em **Criar**. Se você selecionar **Recorrência**, especifique uma **Hora de início** e a frequência com que deseja que o runbook seja repetido: a cada **hora**, **dia**, **semana** ou **mês**. Se você selecionar **semana** ou **mês** na lista suspensa, a **Opção de recorrência** aparecerá no painel e, após a seleção, o painel **Opção de recorrência** será apresentado e você poderá selecionar o dia da semana, se tiver selecionado **semana**. Se tiver selecionado **mês**, você poderá escolher por **dias da semana** ou dias específicos do mês no calendário e, por fim, se deseja executar no último dia do mês ou não. Em seguida, clique em **OK**.

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>Para criar uma nova agenda com o Windows PowerShell

Você usa o cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) criar agendas. Especifique a hora de início para a agenda e a frequência de execução.

Os comandos de exemplo a seguir mostram como criar uma agenda para os dias 15 e o 30 de cada mês usando um cmdlet do Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
$scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
-DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"
```

## <a name="linking-a-schedule-to-a-runbook"></a>Vinculando uma agenda a um runbook

Um runbook pode ser vinculado a várias agendas, e uma agenda pode ter vários runbooks vinculados a ela. Se um runbook tiver parâmetros, você pode fornecer valores para eles. Você deve fornecer valores para todos os parâmetros obrigatórios e pode fornecer valores para os opcionais. Esses valores são usados sempre que o runbook é iniciado por essa agenda. Você pode anexar o mesmo runbook a outra agenda e especificar valores de parâmetro diferentes.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Para vincular uma agenda a um runbook com o portal do Azure

1. No portal do Azure, em sua conta da Automação, selecione **Runbooks** na seção **Automação de Processos** à esquerda.
1. Clique no nome do runbook para agendar.
1. Se o runbook não estiver vinculado atualmente a uma agenda, você terá a opção de criar uma nova agenda ou de vincular a uma agenda existente.
1. Se o runbook tiver parâmetros, você poderá selecionar a opção **Modificar configurações de execução (Padrão: Azure)** e será apresentado o painel **Parâmetros**, no qual você poderá inserir as informações de forma adequada.

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Para vincular uma agenda a um runbook com o Windows PowerShell

Você pode usar o cmdlet [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) para vincular um agendamento. Você pode especificar valores para os parâmetros do runbook com o parâmetro Parameters. Para obter mais informações sobre os valores do parâmetro, confira [Como iniciar um Runbook na Automação do Azure](automation-starting-a-runbook.md).
Os comandos de exemplo a seguir mostram como vincular uma agenda a um runbook usando um cmdlet do Gerenciador de Recursos do Azure com parâmetros.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>Agendamento de runbooks com mais frequência

O intervalo mais frequente que a Automação do Azure pode ser configurada é uma hora. Se você precisar de agendas para executar com mais frequência que isso, há duas opções:

* Criar um [webhook](automation-webhooks.md) para o runbook e usar o [Agendador do Azure](../scheduler/scheduler-get-started-portal.md) para chamar o webhook. O Agendador do Azure fornece granularidade mais refinada ao definir uma agenda.

* Crie quatro agendas todas iniciando dentro de 15 minutos entre si em execução uma vez a cada hora. Este cenário permite que o runbook seja executado a cada 15 minutos com as diferentes agendas.

## <a name="disabling-a-schedule"></a>Desabilitando uma agenda

Quando você desabilita uma agenda, qualquer runbook vinculados a ela deixam de ser executados segundo ela. Você pode desabilitar manualmente uma agenda ou definir uma hora de expiração para agendas com uma frequência ao criá-las. Quando o tempo de expiração é atingido, a agenda é desabilitada.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Para desabilitar uma agenda no portal do Azure

1. No portal do Azure, em sua conta de Automação, selecione **Agendas** na seção **Recursos Compartilhados** à esquerda.
1. Clique no nome de uma agenda para abrir o painel de detalhes.
1. Altere **Habilitado** para **Não**.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>Para desabilitar uma agenda com o Windows PowerShell

Você pode usar o cmdlet [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) para alterar as propriedades de uma agenda existente. Para desabilitar a agenda, especifique **false** para o parâmetro **IsEnabled**.

Os comandos de exemplo a seguir mostram como desabilitar uma agenda para um runbook usando um cmdlet do Gerenciador de Recursos do Azure.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Próximas etapas

* Para começar a usar runbooks na Automação do Azure, confira [Iniciar um Runbook na Automação do Azure](automation-starting-a-runbook.md)