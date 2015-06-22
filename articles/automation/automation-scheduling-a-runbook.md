<properties 
   pageTitle="Agendando um runbook na Automação do Azure"
   description="Descreve como criar uma agenda na Automação do Azure para que você possa iniciar um runbook automaticamente em um determinado momento ou em uma agenda recorrente."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/30/2015"
   ms.author="bwren" />

# Agendando um runbook na Automação do Azure

Para agendar um runbook na Automação do Azure para iniciar em um horário específico, você deve vinculá-lo a uma ou mais agendas. Uma agenda pode ser configurada para ser executada uma vez ou de forma recorrente a cada intervalo definido de horas ou dias. Um runbook pode ser vinculado a várias agendas, e uma agenda pode ter vários runbooks vinculados a ela.

## Criando uma agenda

Você pode criar uma nova agenda com o Portal de Gerenciamento do Azure ou com o Windows PowerShell. Você também tem a opção de criar uma nova agenda quando vincula um runbook a uma agenda usando o Portal de Gerenciamento do Azure.

### Para criar uma nova agenda com o Portal de Gerenciamento do Azure

1. No Portal de Gerenciamento do Azure, selecione automação e clique no nome de uma conta de automação.
1. Selecione a guia **Ativos**.
1. Clique em **Adicionar Configuração** na parte inferior da tela.
1. Clique em **Adicionar Agenda**.
1. Digite um **Nome** e, opcionalmente, uma **Descrição** para a nova agenda.
1. Selecione se a agenda será executada **uma vez**, **por hora** ou **diariamente**.
1. Especifique uma **Hora de início** e outras opções, dependendo do tipo de agenda selecionado.

### Para criar uma nova agenda com o Windows PowerShell

Você pode usar o cmdlet [New-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690271.aspx) para criar uma nova agenda na Automação do Azure. Você deve especificar a hora de início para a agenda e se ela deve ser executada uma vez, por hora ou diariamente.

Os comandos de exemplo a seguir mostram como criar uma nova agenda que é executada todos os dias às 15h30 começando em 20 de janeiro de 2015.

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "Sample-DailySchedule"
	New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name $scheduleName –StartTime "1/20/2015 15:30:00" –DayInterval 1

## Vinculando uma agenda a um runbook

Um runbook pode ser vinculado a várias agendas, e uma agenda pode ter vários runbooks vinculados a ela. Se um runbook tiver parâmetros, você pode fornecer valores para eles. Você deve fornecer valores para todos os parâmetros obrigatórios e pode fornecer valores para os opcionais. Esses valores serão usados sempre que o runbook for iniciado por essa agenda. Você pode anexar o mesmo runbook a outra agenda e especificar valores de parâmetro diferentes.

### Para vincular uma agenda a um runbook com o Portal de Gerenciamento do Azure

1. No Portal de Gerenciamento do Azure, selecione **Automação** e clique no nome de uma conta de automação.
1. Selecione a guia **Runbooks**.
1. Clique no nome do runbook para agendar.
1. Clique na guia **Agenda**.
2. Se o runbook não estiver atualmente vinculado a uma agenda, você terá a opção de **Vincular a uma nova agenda** ou **Vincular a uma agenda existente**. Se o runbook estiver atualmente vinculado a uma agenda, clique em **Vincular** na parte inferior da janela para acessar essas opções.
1. Se o runbook tiver parâmetros, você será solicitado a inserir os seus valores.  

### Para vincular uma agenda a um runbook com o Windows PowerShell

Você pode usar [Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx) para vincular uma agenda a um runbook. Você pode especificar valores para os parâmetros do runbook com o parâmetro Parameters. Confira [Como iniciar um Runbook na Automação do Azure](automation-starting-a-runbook.md) para saber mais sobre como especificar valores de parâmetro.

Os comandos de exemplo a seguir mostram como vincular uma agenda a um runbook com parâmetros.

	$automationAccountName = "MyAutomationAccount"
	$runbookName = "Test-Runbook"
	$scheduleName = "Sample-DailySchedule"
	$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
	Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName –Name $runbookName –ScheduleName $scheduleName –Parameters $params

## Desabilitando uma agenda

Quando você desabilita uma agenda, todos os runbooks vinculados a ela deixam de ser executados nela. Você pode desabilitar uma agenda manualmente ou definir um tempo de expiração de agendamentos por hora ou diários quando criá-los. Quando o tempo de expiração é atingido, a agenda é desabilitada.

### Para desabilitar uma agenda com o Portal de Gerenciamento do Azure

Você pode desabilitar uma agenda no Portal de Gerenciamento do Azure da página Detalhes da Agenda.

1. No Portal de Gerenciamento do Azure, selecione automação e clique no nome de uma conta de automação.
1. Selecione a guia Ativos.
1. Clique no nome de uma agenda para abrir sua página de detalhes.
2. Altere **Habilitado** para **Não**.

### Para desabilitar uma agenda com o Windows PowerShell

Você pode usar o cmdlet [Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) para alterar as propriedades de uma agenda existente. Para desabilitar a agenda, especifique **false** para o parâmetro **IsEnabled**.

Os comandos de exemplo a seguir mostram como desabilitar uma agenda.

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "Sample-DailySchedule"
	Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name $scheduleName –IsEnabled $false

## Artigos relacionados

- [Agendar Ativos na Automação do Azure](http://msdn.microsoft.com/library/azure/dn940016.aspx)
- [Como iniciar um runbook na Automação do Azure](automation-starting-a-runbook.md)

<!---HONumber=58--> 