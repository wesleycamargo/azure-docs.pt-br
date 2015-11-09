<properties 
   pageTitle="Agendas na Automação do Azure | Microsoft Azure"
   description="As agendas de automação são usadas para agendar o início automático de runbooks na Automação do Azure. Este artigo descreve como criar agendas."
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
   ms.date="10/26/2015"
   ms.author="bwren" />

# Agendas na Automação do Azure

As Agendas de automação são usadas para agendar runbooks para serem executados automaticamente. Isso pode ser uma única data e hora para que o runbook seja executado uma vez. Ou pode ser um agendamento recorrente para iniciar o runbook várias vezes. As agendas normalmente não são acessadas dos runbooks.

>[AZURE.NOTE]Atualmente, os agendamentos não dão suporte às configurações DSC de Automação do Azure.

## Cmdlets do Windows PowerShell

Os cmdlets na tabela a seguir são usados para criar e gerenciar variáveis com o Windows PowerShell na Automação do Azure. Eles são enviados como parte do [módulo do PowerShell do Azure](../powershell-install-configure.md).

|Cmdlets|Descrição|
|:---|:---|
|[Get-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690274.aspx)|Recupera uma agenda.|
|[New-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690271.aspx)|Cria uma nova agenda.|
|[Remove-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690279.aspx)|Remove uma agenda.|
|[Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690270.aspx)|Define as propriedades de uma agenda existente.|
|[Get-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn913778.aspx)|Recupera runbooks agendados.|
|[Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn690265.aspx)|Associa um runbook a uma agenda.|
|[Unregister-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn690273.aspx)|Dissocia um runbook de uma agenda.|

## Criando uma nova Agenda

### Para criar uma nova agenda com o portal do Azure


1. Em sua conta de automação, clique em **Ativos** na parte superior da janela.
1. Clique em **Adicionar Configuração** na parte inferior da tela.
1. Clique em **Adicionar Agenda**.
1. Conclua o assistente e clique na caixa de seleção para salvar a nova variável.

### Para criar uma nova agenda com o portal de visualização do Azure

1. Na sua conta de automação, clique na parte de **Ativos** para abrir a folha de **Ativos**.
1. Clique na parte de **Agendas** para abrir a folha de **Agendas**.
1. Clique em **Adicionar uma Agenda** na parte superior da folha.
1. Preencha o formulário e clique em **Criar** para salvar a nova agenda.

### Para criar uma nova agenda com o Windows PowerShell

O cmdlet [New-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690271.aspx) cria uma nova agenda e define o valor de uma agenda existente. Os seguintes comandos de exemplo do Windows PowerShell criam uma nova agenda chamada Minha Agenda Diária que inicia amanhã ao meio-dia e é acionada todo dia em um ano:

	$automationAccountName = "MyAutomationAccount"
	$scheduleName = "My Daily Schedule"
	$startTime = (Get-Date).Date.AddDays(1).AddHours(12)
	$expiryTime = $startTime.AddYears(1)
	
	New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name $scheduleName –StartTime $startTime –ExpiryTime $expiryTime –DayInterval 1


## Veja também
- [Agendar um runbook na Automação do Azure](automation-scheduling-a-runbook.md)
 

<!---HONumber=Nov15_HO1-->