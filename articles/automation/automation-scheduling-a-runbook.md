---
title: Agendar um runbook na Automação do Azure | Microsoft Docs
description: Descreve como criar uma agenda na Automação do Azure para que você possa iniciar um runbook automaticamente em um determinado momento ou em uma agenda recorrente.
services: automation
documentationcenter: ''
author: mgoedtel
manager: jwhit
editor: tysonn

ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/05/2016
ms.author: bwren

---
# Agendando um runbook na Automação do Azure
Para agendar um runbook na Automação do Azure para iniciar em um horário específico, você deve vinculá-lo a uma ou mais agendas. Uma agenda pode ser configurada para execução uma vez ou em uma agenda recorrente horária ou diária para runbooks no portal clássico do Azure e runbooks no portal do Azure. Adicionalmente, você pode agendar semanalmente, mensalmente, em dias específicos da semana ou do mês ou em determinado dia do mês. Um runbook pode ser vinculado a várias agendas, e uma agenda pode ter vários runbooks vinculados a ela.

## Criando uma agenda
Você pode criar uma nova agenda para runbooks no portal do Azure, no portal clássico ou com o Windows PowerShell. Você também tem a opção de criar uma nova agenda ao vincular um runbook a uma agenda usando o portal clássico do Azure ou o portal do Azure.

> [!NOTE]
> Quando você associa um agendamento a um runbook, a Automação armazena as versões atuais dos módulos em sua conta e vincula-os a esse agendamento. Isso significa que, se você tivesse um módulo com versão 1.0 na sua conta quando criou um agendamento e atualizasse o módulo para a versão 2.0, o agendamento continuaria a usar a 1.0. Para usar a versão atualizada do módulo, você deve criar um novo agendamento.
> 
> 

### Para criar uma nova agenda no portal clássico do Azure
1. No portal clássico do Azure, selecione Automação e selecione o nome de uma conta de automação.
2. Selecione a guia **Ativos**.
3. Clique em **Adicionar Configuração** na parte inferior da tela.
4. Clique em **Adicionar Agenda**.
5. Digite um **Nome** e, opcionalmente, uma **Descrição** para um novo agendamento. O agendamento será executado **Uma Vez**, **Por Hora**, **Diariamente**, **Semanalmente** ou **Mensalmente**.
6. Especifique uma **Hora de início** e outras opções, dependendo do tipo de agenda selecionado.

### Para criar uma nova agenda no portal do Azure
1. No Portal do Azure, em sua conta de Automação, clique no bloco **Ativos** para abrir a folha **Ativos**.
2. Clique no bloco **Agendamentos** para abrir a folha **Agendamentos**.
3. Clique em **Adicionar um agendamento** na parte superior da folha.
4. Na folha **Novo agendamento**, digite um **Nome** e, opcionalmente, uma **Descrição** para o novo agendamento.
5. Selecione se o agendamento será executado uma vez ou de forma recorrente selecionando **Uma vez** ou **Recorrência**. Se você selecionar **Uma vez**, especifique uma **Hora de início** e clique em **Criar**. Se você selecionar **Recorrência**, especifique uma **Hora de início** e a frequência com que deseja que o runbook seja repetido: a cada **hora**, **dia**, **semana** ou **mês**. Se você selecionar **semana** ou **mês** na lista suspensa, a **Opção de recorrência** aparecerá na folha e, após a seleção, a folha **Opção de recorrência** será apresentada e você poderá selecionar o dia da semana, se tiver selecionado **semana**. Se tiver selecionado **mês**, você poderá escolher por **dias da semana** ou dias específicos do mês no calendário e, por fim, se deseja executá-lo no último dia do mês ou não. Em seguida, clique em **OK**.

### Para criar uma nova agenda com o Windows PowerShell
Você pode usar o cmdlet [New-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690271.aspx) para criar um novo agendamento na Automação do Azure para runbooks clássicos ou o cmdlet [New-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603577.aspx) para runbooks no Portal do Azure. Você deve especificar a hora de início para a agenda e a frequência de execução.

Os comandos de exemplo a seguir mostram como criar uma nova agenda que é executada todos os dias às 15h30 começando em 20 de janeiro de 2015, com um cmdlet do Gerenciamento de Serviços do Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName –StartTime "1/20/2016 15:30:00" –DayInterval 1

Os comandos de exemplo a seguir mostram como criar uma agenda para o 15º e o 30º dia de cada mês usando um cmdlet do Gerenciador de Recursos do Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
    -DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"


## Vinculando uma agenda a um runbook
Um runbook pode ser vinculado a várias agendas, e uma agenda pode ter vários runbooks vinculados a ela. Se um runbook tiver parâmetros, você pode fornecer valores para eles. Você deve fornecer valores para todos os parâmetros obrigatórios e pode fornecer valores para os opcionais. Esses valores serão usados sempre que o runbook for iniciado por essa agenda. Você pode anexar o mesmo runbook a outra agenda e especificar valores de parâmetro diferentes.

### Para vincular uma agenda a um runbook com o portal clássico do Azure
1. No portal clássico do Azure, selecione **Automação** e clique no nome de uma conta de Automação.
2. Selecione a guia **Runbooks**.
3. Clique no nome do runbook para agendar.
4. Clique na guia **Agenda**.
5. Se o runbook não estiver atualmente vinculado a uma agenda, você terá a opção de **Vincular a uma nova agenda** ou **Vincular a uma agenda existente**. Se o runbook estiver atualmente vinculado a uma agenda, clique em **Vincular** na parte inferior da janela para acessar essas opções.
6. Se o runbook tiver parâmetros, você será solicitado a inserir os seus valores.

### Para vincular uma agenda a um runbook com o portal do Azure
1. No Portal do Azure, em sua conta de Automação, clique no bloco **Runbooks** para abrir a folha **Runbooks**.
2. Clique no nome do runbook para agendar.
3. Se o runbook não estiver atualmente vinculado a uma agenda, você terá a opção de criar uma nova agenda ou vincular a uma agenda existente.
4. Se o runbook tiver parâmetros, você poderá selecionar a opção **Modificar configurações de execução (Padrão: Azure)**, e será apresentada a folha **Parâmetros**, em que você pode inserir as informações de forma adequada.

### Para vincular uma agenda a um runbook com o Windows PowerShell
Você pode usar [Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx) para vincular um agendamento a um runbook clássico ou o cmdlet [Register-AzureRmAutomationScheduledRunbook](https://msdn.microsoft.com/library/mt603575.aspx) para runbooks no Portal do Azure. Você pode especificar valores para os parâmetros do runbook com o parâmetro Parameters. Confira [Como iniciar um Runbook na Automação do Azure](automation-starting-a-runbook.md) para saber mais sobre como especificar valores de parâmetro.

Os comandos de exemplo a seguir mostram como vincular uma agenda usando um cmdlet do Gerenciamento de Serviços do Azure com parâmetros.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params

Os comandos de exemplo a seguir mostram como vincular uma agenda a um runbook usando um cmdlet do Gerenciador de Recursos do Azure com parâmetros.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params `
    -ResourceGroupName "ResourceGroup01"

## Desabilitando uma agenda
Quando você desabilita uma agenda, todos os runbooks vinculados a ela deixam de ser executados nela. Você pode desabilitar manualmente uma agenda ou definir uma hora de expiração para agendas com uma frequência ao criá-las. Quando o tempo de expiração é atingido, a agenda é desabilitada.

### Para desabilitar uma agenda no portal clássico do Azure
Você pode desabilitar uma agenda no portal clássico do Azure da página Detalhes da Agenda.

1. No portal clássico do Azure, selecione Automação e clique no nome de uma conta de automação.
2. Selecione a guia Ativos.
3. Clique no nome de uma agenda para abrir sua página de detalhes.
4. Altere **Habilitado** para **Não**.

### Para desabilitar uma agenda no portal do Azure
1. No Portal do Azure, em sua conta de Automação, clique no bloco **Ativos** para abrir a folha **Ativos**.
2. Clique no bloco **Agendamentos** para abrir a folha **Agendamentos**.
3. Clique no nome de uma agenda para abrir a folha de detalhes.
4. Altere **Habilitado** para **Não**.

### Para desabilitar uma agenda com o Windows PowerShell
Você pode usar o cmdlet [Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) para alterar as propriedades de um agendamento existente para um runbook clássico ou o cmdlet [Set-AzureRmAutomationSchedule](https://msdn.microsoft.com/library/mt603566.aspx) para runbooks no Portal do Azure. Para desabilitar a agenda, especifique **false** para o parâmetro **IsEnabled**.

Os comandos de exemplo a seguir mostram como desabilitar uma agenda usando o cmdlet do Gerenciamento de Serviços do Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false

Os comandos de exemplo a seguir mostram como desabilitar uma agenda para um runbook usando um cmdlet do Gerenciador de Recursos do Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"


## Próximas etapas
* Para saber mais sobre como trabalhar com agendamentos, confira [Agendar Ativos na Automação do Azure](http://msdn.microsoft.com/library/azure/dn940016.aspx)
* Para começar a usar runbooks na Automação do Azure, confira [Iniciar um Runbook na Automação do Azure](automation-starting-a-runbook.md)

<!---HONumber=AcomDC_0810_2016-->