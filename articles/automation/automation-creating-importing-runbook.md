---
title: "Criando ou importando um runbook na Automação do Azure"
description: "Este artigo descreve como criar um novo runbook na Automação do Azure ou como importar um de um arquivo."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 24414362-b690-4474-8ca7-df18e30fc31d
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 0484b1f230a8544e3de2388df2cbdab3b54f9d3d
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2018
---
# <a name="creating-or-importing-a-runbook-in-azure-automation"></a>Criando ou importando um runbook na Automação do Azure
Você pode adicionar um runbook à Automação do Azure [criando um novo](#creating-a-new-runbook) ou importando um runbook existente de um arquivo ou da [Galeria de Runbook](automation-runbook-gallery.md). Este artigo oferece informações sobre a criação e a importação de runbooks de um arquivo.  Você pode obter todos os detalhes sobre o acesso a runbooks e a módulos da comunidade em [Galerias de runbook e de módulos para a Automação do Azure](automation-runbook-gallery.md).

## <a name="creating-a-new-runbook"></a>Criando um novo runbook
Você pode criar um novo runbook na Automação do Azure usando um dos portais do Azure ou o Windows PowerShell. Quando o runbook tiver sido criado, você poderá editá-lo usando as informações das [Noções básicas sobre o fluxo de trabalho do PowerShell](automation-powershell-workflow.md) e [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md).

### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-portal"></a>Para criar um novo runbook da Automação do Azure com o portal do Azure
1. No portal do Azure, abra sua conta da Automação.
2. No Hub, selecione **Runbooks** para abrir a lista de runbooks.
3. Clique no botão **Adicionar um runbook** e em **Criar um novo runbook**.
4. Digite um **Nome** para o runbook e selecione seu [Tipo](automation-runbook-types.md). O nome do runbook deve começar com uma letra e pode ter letras, números, sublinhados e traços.
5. Clique em **Criar** para criar o runbook e abrir o editor.

### <a name="to-create-a-new-azure-automation-runbook-with-windows-powershell"></a>Para criar um novo runbook da Automação do Azure com o Windows PowerShell
Você pode usar o cmdlet [New-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt619376.aspx) para criar um [runbook do Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) vazio. Você pode especificar o parâmetro **Nome** para criar um runbook vazio a ser editado posteriormente ou especificar o parâmetro **Caminho** para importar um arquivo de runbook. O parâmetro **Tipo** também deve ser incluído para especificar um dos quatro tipos de runbook.

Os comandos de exemplo a seguir mostram como criar um novo runbook vazio.

    New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
    -Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell

## <a name="importing-a-runbook-from-a-file-into-azure-automation"></a>Importando um runbook de um arquivo para a Automação do Azure
Você pode criar um novo runbook na Automação do Azure importando um script do PowerShell ou o Fluxo de Trabalho do PowerShell (extensão .ps1), um runbook gráfico exportado (.graphrunbook) ou um script Python 2 (extensão .py).  Você precisa especificar o [tipo de runbook](automation-runbook-types.md) que é criado durante a importação, levando em consideração os aspectos a seguir.

* Um arquivo .graphrunbook só pode ser importado em um novo [runbook gráfico](automation-runbook-types.md#graphical-runbooks)e os runbooks gráficos só podem ser criados de um arquivo .graphrunbook.
* Um arquivo .ps1 com um Fluxo de Trabalho do PowerShell só poderá ser importado para um [runbook do Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).  Se o arquivo contiver vários Fluxos de Trabalho do PowerShell, a importação falhará. Você deve salvar cada fluxo de trabalho em seu próprio arquivo e importá-los separadamente.
* Um arquivo .ps1 que não contenha um fluxo de trabalho poderá ser importado para um [runbook do PowerShell](automation-runbook-types.md#powershell-runbooks) ou para um [runbook do Fluxo de Trabalho do PowerShell](automation-runbook-types.md#powershell-workflow-runbooks).  Se for importado para um runbook de Fluxo de Trabalho do PowerShell, ele será convertido em um fluxo de trabalho e serão incluídos comentários no runbook especificando as alterações feitas.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Para importar um runbook de um arquivo com o portal do Azure
Você pode usar o procedimento a seguir para importar um arquivo de script para a Automação do Azure.  

> [!NOTE]
> Observe que você só poderá importar um arquivo .ps1 para um runbook do Fluxo de Trabalho do PowerShell usando o portal.
> 
> 

1. No portal do Azure, abra sua conta da Automação.
2. No Hub, selecione **Runbooks** para abrir a lista de runbooks.
3. Clique no botão **Adicionar um runbook** e em **Importar**.
4. Clique em **Arquivo de runbook** para selecionar o arquivo a ser importado
5. Se o campo **Nome** estiver habilitado, você terá a opção de alterá-lo.  O nome do runbook deve começar com uma letra e pode ter letras, números, sublinhados e traços.
6. O [tipo de runbook](automation-runbook-types.md) é selecionado automaticamente, mas você pode alterá-lo depois levar as restrições aplicáveis em consideração. 
7. O novo runbook é exibido na lista de runbooks da Conta de Automação.
8. Será necessário [publicar o runbook](#publishing-a-runbook) antes de executá-lo.

> [!NOTE]
> Depois de importar um runbook gráfico ou um runbook gráfico do fluxo de trabalho do PowerShell, você terá a opção de converter para o outro tipo, se desejar. Não é possível converter para um runbook de texto.
>  
> 

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Para importar um runbook de um arquivo de script com o Windows PowerShell
Você pode usar o cmdlet [Import-AzureRMAutomationRunbook](https://msdn.microsoft.com/library/mt603735.aspx) para importar um arquivo de script como um rascunho do runbook do Fluxo de Trabalho do PowerShell. Se o runbook já existir, a importação falhará a menos que você use o parâmetro *-Force*. 

Os comandos de exemplo a seguir mostram como importar um arquivo de script para um runbook.

    $automationAccountName =  "AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
    $RGName = "ResourceGroup"

    Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
    -ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
    -Type PowerShellWorkflow 


## <a name="publishing-a-runbook"></a>Publicando um runbook
Quando você criar ou importar um novo runbook, deverá publicá-lo antes de poder executá-lo.  Cada runbook na Automação tem um Rascunho e uma versão Publicada. Somente a versão Publicada está disponível para ser executada, e somente a versão de Rascunho pode ser editada. A versão Publicada não é afetada pelas alterações feitas na versão de Rascunho. Quando a versão de Rascunho estiver pronta para ser disponibilizada, você a publicará e ela substituirá a versão Publicada pela versão de Rascunho.

## <a name="to-publish-a-runbook-using-the-azure-portal"></a>Para publicar um runbook usando o portal do Azure
1. Abra o runbook no portal do Azure.
2. Clique no botão **Editar** .
3. Clique no botão **Publicar** e em **Sim** na mensagem de verificação.

## <a name="to-publish-a-runbook-using-windows-powershell"></a>Para publicar um runbook usando o Windows PowerShell
Você pode usar o cmdlet [Publish-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603705.aspx) para publicar um runbook com o Windows PowerShell. Os comandos de exemplo a seguir mostram como publicar um runbook de exemplo.

    $automationAccountName =  AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $RGName = "ResourceGroup"

    Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
    -Name $runbookName -ResourceGroupName $RGName


## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre como você pode aproveitar a Galeria de Módulos do PowerShell e do Runbook, consulte [Galerias de runbook e de módulos para a Automação do Azure](automation-runbook-gallery.md)
* Para saber mais sobre como editar runbooks do PowerShell e do Fluxo de Trabalho do PowerShell com um editor de texto, consulte [Editando runbooks textuais na Automação do Azure](automation-edit-textual-runbook.md)
* Para saber mais sobre a criação de runbooks Gráficos, veja [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md)

