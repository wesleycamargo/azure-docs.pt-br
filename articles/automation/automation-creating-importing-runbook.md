---
title: Criando ou importando um runbook na Automação do Azure
description: Este artigo descreve como criar um novo runbook na Automação do Azure ou como importar um de um arquivo.
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
ms.date: 09/12/2016
ms.author: magoedte;bwren

---
# Criando ou importando um runbook na Automação do Azure
Você pode adicionar um runbook à Automação do Azure [criando um novo](#creating-a-new-runbook) ou importando um runbook existente de um arquivo ou da [Galeria de Runbook](automation-runbook-gallery.md). Este artigo oferece informações sobre a criação e a importação de runbooks de um arquivo. Você pode obter todos os detalhes sobre o acesso a runbooks e a módulos da comunidade em [Galerias de runbook e de módulos para a Automação do Azure](automation-runbook-gallery.md).

## Criando um novo runbook
Você pode criar um novo runbook na Automação do Azure usando um dos portais do Azure ou o Windows PowerShell. Quando o runbook tiver sido criado, você poderá editá-lo usando as informações em [Noções básicas sobre o fluxo de trabalho do PowerShell](automation-powershell-workflow.md) e [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md).

### Para criar um novo runbook da Automação do Azure com o portal Clássico do Azure
No portal do Azure, você só poderá trabalhar com os [runbooks do Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).

1. No portal Clássico do Azure, clique em **Novo**, **Serviços de Aplicativos**, **Automação**, **Runbook** e **Criação Rápida**.
2. Insira as informações necessárias e clique em **Criar**. O nome do runbook deve começar com uma letra e pode ter letras, números, sublinhados e traços.
3. Se você quiser editar o runbook agora, clique em **Editar Runbook**. Caso contrário, clique em **OK**.
4. Seu novo runbook será exibido na guia **Runbooks**.

### Para criar um novo runbook da Automação do Azure com o portal do Azure
1. No portal do Azure, abra sua conta da Automação.
2. Clique no bloco **Runbooks** para abrir a lista de runbooks.
3. Clique no botão **Adicionar um runbook** e em **Criar um novo runbook**.
4. Digite um **Nome** para o runbook e selecione seu [Tipo](automation-runbook-types.md). O nome do runbook deve começar com uma letra e pode ter letras, números, sublinhados e traços.
5. Clique em **Criar** para criar o runbook e abrir o editor.

### Para criar um novo runbook da Automação do Azure com o Windows PowerShell
Você pode usar o cmdlet [New-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt619376.aspx) para criar um [runbook do Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) vazio. Você pode especificar o parâmetro **Nome** para criar um runbook vazio que você pode editar mais tarde ou pode especificar o parâmetro **Caminho** para importar um arquivo de runbook. O parâmetro **Tipo** também deve ser incluído para especificar um dos quatro tipos de runbook.

Os comandos de exemplo a seguir mostram como criar um novo runbook vazio.

    New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
    -Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell

## Importando um runbook de um arquivo para a Automação do Azure
Você pode criar um novo runbook na Automação do Azure importando um script do PowerShell ou o Fluxo de Trabalho do PowerShell (extensão .ps1) ou um runbook gráfico exportado (.graphrunbook). Você deve especificar o [tipo de runbook](automation-runbook-types.md) que será criado a partir da importação, levando em conta as considerações a seguir.

* Um arquivo .graphrunbook só pode ser importado em um novo [runbook gráfico](automation-runbook-types.md#graphical-runbooks) e os runbooks gráficos só podem ser criados de um arquivo .graphrunbook.
* Um arquivo .ps1 com um Fluxo de Trabalho do PowerShell só poderá ser importado para um [runbook do Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Se o arquivo contiver vários Fluxos de Trabalho do PowerShell, a importação falhará. Você deve salvar cada fluxo de trabalho em seu próprio arquivo e importá-los separadamente.
* Um arquivo .ps1 que não contiver um fluxo de trabalho poderá ser importado para um [runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) ou para um [runbook do Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Se ele for importado para um runbook do Fluxo de Trabalho do PowerShell, será convertido em um fluxo de trabalho e serão incluídos comentários no runbook especificando as alterações feitas.

### Para importar um runbook de um arquivo com o portal Clássico do Azure
Você pode usar o procedimento a seguir para importar um arquivo de script para a Automação do Azure. Observe que você só poderá importar um arquivo .ps1 para um runbook do Fluxo de Trabalho do PowerShell usando este portal. É necessário usar o portal do Azure para os outros tipos.

1. No portal de Gerenciamento do Azure, selecione **Automação** e selecione uma Conta de Automação.
2. Clique em **Importar**.
3. Clique em **Procurar Arquivo** e localize o arquivo de script a ser importado.
4. Se você quiser editar o runbook agora, clique em **Editar Runbook**. Caso contrário, clique em OK.
5. O novo runbook aparecerá na guia **Runbooks** para a Conta de Automação.
6. Será necessário [publicar o runbook](#publishing-a-runbook) antes de executá-lo.

### Para importar um runbook de um arquivo com o portal do Azure
Você pode usar o procedimento a seguir para importar um arquivo de script para a Automação do Azure.

> [!NOTE]
> Observe que você só poderá importar um arquivo .ps1 para um runbook do Fluxo de Trabalho do PowerShell usando o portal.
> 
> 

1. No portal do Azure, abra sua conta da Automação.
2. Clique no bloco **Runbooks** para abrir a lista de runbooks.
3. Clique no botão **Adicionar um runbook** e em **Importar**.
4. Clique em **Arquivo de runbook** para selecionar o arquivo a ser importado
5. Se o campo **Nome** estiver habilitado, você terá a opção de alterá-lo. O nome do runbook deve começar com uma letra e pode ter letras, números, sublinhados e traços.
6. O [tipo de runbook](automation-runbook-types.md) será selecionado automaticamente, mas você pode alterar o tipo depois levar as restrições aplicáveis em consideração.
7. O novo runbook aparecerá na lista de runbooks da Conta de Automação.
8. Será necessário [publicar o runbook](#publishing-a-runbook) antes de executá-lo.

> [!NOTE]
> Depois de importar um runbook gráfico ou um runbook gráfico do fluxo de trabalho do PowerShell, você terá a opção de converter para o outro tipo, se desejar. Você não pode converter o tipo de texto.
> 
> 

### Para importar um runbook de um arquivo de script com o Windows PowerShell
Você pode usar o cmdlet [Import-AzureRMAutomationRunbook](https://msdn.microsoft.com/library/mt603735.aspx) para importar um arquivo de script como um rascunho do runbook do Fluxo de Trabalho do PowerShell. Se o runbook já existir, a importação falhará a menos que você use o parâmetro *-Force*.

Os comandos de exemplo a seguir mostram como importar um arquivo de script para um runbook.

    $automationAccountName =  "AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
    $RGName = "ResourceGroup"

    Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
    -ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
    -Type PowerShellWorkflow 


## Publicando um runbook
Quando você criar ou importar um novo runbook, deverá publicá-lo antes de poder executá-lo. Cada runbook na Automação tem um Rascunho e uma versão Publicada. Somente a versão Publicada está disponível para ser executada, e somente a versão de Rascunho pode ser editada. A versão Publicada não é afetada pelas alterações feitas na versão de Rascunho. Quando a versão de Rascunho estiver pronta para ser disponibilizada, você a publicará e ela substituirá a versão Publicada pela versão de Rascunho.

## Para publicar um runbook usando o portal Clássico do Azure
1. Abra o runbook no portal Clássico do Azure.
2. Na parte superior da tela, clique em **Autor**.
3. Na parte inferior da tela, clique em **Publicar** e em **Sim** na mensagem de verificação.

## Para publicar um runbook usando o portal do Azure
1. Abra o runbook no portal do Azure.
2. Clique no botão **Editar**.
3. Clique no botão **Publicar** e em **Sim** na mensagem de verificação.

## Para publicar um runbook usando o Windows PowerShell
Você pode usar o cmdlet [Publish-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603705.aspx) para publicar um runbook com o Windows PowerShell. Os comandos de exemplo a seguir mostram como publicar um runbook de exemplo.

    $automationAccountName =  AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $RGName = "ResourceGroup"

    Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
    -Name $runbookName -ResourceGroupName $RGName


## Próximas etapas
* Para saber mais sobre como você pode aproveitar a Galeria de Módulos do PowerShell e Runbook, consulte [Galerias de runbook e de módulos para a Automação do Azure](automation-runbook-gallery.md)
* Para saber mais sobre como editar runbooks do PowerShell e do Fluxo de Trabalho do PowerShell com um editor de texto, consulte [Editando runbooks textuais na Automação do Azure](automation-edit-textual-runbook.md)
* Para saber mais sobre a criação de runbooks Gráficos, veja [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md)

<!---HONumber=AcomDC_0914_2016-->