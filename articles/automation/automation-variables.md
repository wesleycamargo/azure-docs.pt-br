---
title: "Ativos de variáveis na Automação do Azure | Microsoft Docs"
description: "Ativos de variáveis são valores que estão disponíveis para todos os runbooks e configurações DSC na Automação do Azure.  Este artigo explica os detalhes das variáveis e como trabalhar com elas na criação de textos e gráficos."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: b880c15f-46f5-4881-8e98-e034cc5a66ec
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: magoedte;bwren
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 28896becae631eb316775e0904930f750bafa607
ms.lasthandoff: 04/27/2017


---
# <a name="variable-assets-in-azure-automation"></a>Ativos variáveis na Automação do Azure

Ativos de variáveis são valores que estão disponíveis para todos os runbooks e configurações DSC em sua conta de automação. Eles podem ser criados, modificados e recuperados no portal do Azure, no Windows PowerShell e em um runbook ou uma configuração DSC. As variáveis de automação são úteis para os seguintes cenários:

- Compartilhe um valor entre vários runbooks ou configurações DSC.

- Compartilhe um valor entre vários trabalhos do mesmo runbook ou configuração DSC.

- Gerencie um valor do portal ou da linha de comando do Windows PowerShell usada por runbooks ou configurações de DSC, tal como conjunto de itens de configuração comuns como, por exemplo, lista específica de nomes de VM, grupo de recursos específico, nome de domínio de AD.  

As variáveis de automação são mantidas para que continuem disponíveis mesmo se o runbook ou a configuração DSC falhar.  Isso também permite que um valor seja definido por um runbook que é depois usado por outro, ou que é usado pelo mesmo runbook ou configuração DSC na próxima vez em que for executado.     

Quando uma variável é criada, você pode definir que ele seja armazenado criptografado.  Quando uma variável é criptografada, ela é armazenada com segurança na Automação do Azure e seu valor não pode ser recuperado do cmdlet [Get-AzureRmAutomationVariable](/powershell/module/azurerm.automation/get-azurermautomationvariable) enviado como parte do módulo do Azure PowerShell.  A única maneira de recuperar um valor criptografado é por meio da atividade **Get-AutomationVariable** em um runbook ou configuração DSC.

> [!NOTE]
> Os ativos protegidos na Automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são criptografados e armazenados na Automação do Azure usando uma chave exclusiva que é gerada para cada conta de automação. Essa chave é criptografada por um certificado mestre e armazenada na Automação do Azure. Antes de armazenar um ativo seguro, a chave para a conta de automação é descriptografada usando o certificado mestre e usada para criptografar o ativo.

## <a name="variable-types"></a>Tipos de variável

Quando você cria uma variável com o portal do Azure, deve especificar um tipo de dados na lista suspensa para que o portal possa exibir o controle adequado para a inserção do valor da variável. A variável não está restrita a esse tipo de dados, mas você deve definir a variável usando o Windows PowerShell se desejar especificar um valor de um tipo diferente. Se você especificar **Indefinido**, o valor da variável será definido como **$null** e você deverá definir o valor com o cmdlet [Set-AzureAutomationVariable](/powershell/module/azurerm.automation/set-azurermautomationvariable) ou atividade **Set-AutomationVariable**.  Você não pode criar ou alterar o valor de um tipo complexo de variável no portal, mas pode fornecer um valor de qualquer tipo usando o Windows PowerShell. Tipos complexos retornam como um [PSCustomObject](http://msdn.microsoft.com/library/system.management.automation.pscustomobject.aspx).

Você pode armazenar vários valores para uma única variável criando uma matriz ou hashtable e salvando-a na variável.

A seguir está uma lista de tipos de variáveis disponíveis na automação:

* string
* Número inteiro
* DateTime
* Booliano
* Nulo

>[!NOTE]
>Os ativos variáveis estão limitados a 1024 caracteres. 

## <a name="cmdlets-and-workflow-activities"></a>Atividades de fluxo de trabalho e cmdlets

Os cmdlets na tabela a seguir são usados para criar e gerenciar variáveis de automação com o Windows PowerShell. Eles são fornecidos como parte do [módulo do Azure PowerShell](/powershell/azure/overview) que está disponível para uso em runbooks de Automação e na configuração DSC.

|Cmdlets|Descrição|
|:---|:---|
|[Get-AzureRmAutomationVariable](/powershell/module/azurerm.automation/get-azurermautomationvariable)|Recupera o valor de uma variável existente.|
|[New-AzureRmAutomationVariable](/powershell/module/azurerm.automation/new-azurermautomationvariable)|Cria uma nova variável e define o seu valor.|
|[Remove-AzureRmAutomationVariable](/powershell/module/azurerm.automation/remove-azurermautomationvariable)|Remove uma variável existente.|
|[Set-AzureRmAutomationVariable](/powershell/module/azurerm.automation/set-azurermautomationvariable)|Define o valor de uma variável existente.|

As atividades de fluxo de trabalho na tabela a seguir são usadas para acessar variáveis de automação em um runbook. Elas só estão disponíveis para uso em um runbook ou uma configuração DSC e não são fornecidas como parte do módulo do Azure PowerShell.

|Atividades de fluxo de trabalho|Descrição|
|:---|:---|
|Get-AutomationVariable|Recupera o valor de uma variável existente.|
|Set-AutomationVariable|Define o valor de uma variável existente.|

> [!NOTE] 
> Evite usar variáveis no parâmetro –Name de **Get-AutomationVariable** em um runbook ou na configuração DSC, pois isso pode complicar a descoberta de dependências entre runbooks ou configurações DSC e variáveis da Automação no momento do design.

## <a name="creating-an-automation-variable"></a>Criar uma variável de Automação

### <a name="to-create-a-variable-with-the-azure-portal"></a>Para criar uma variável com o Portal do Azure

1. Na sua conta de automação, clique no bloco **Ativos** para abrir a folha **Ativos**.
1. Clique no bloco **Variáveis** para abrir a folha **Variáveis**.
1. Clique em **Adicionar uma variável** na parte superior da folha.
1. Preencha o formulário e clique em **Criar** para salvar a nova variável.


### <a name="to-create-a-variable-with-windows-powershell"></a>Para criar uma variável com o Windows PowerShell

O cmdlet [New-AzureRmAutomationVariable](/powershell/module/azurerm.automation/new-azurermautomationvariable) cria uma variável e define o seu valor inicial. Você pode recuperar o valor usando [Get-AzureRmAutomationVariable](/powershell/module/azurerm.automation/get-azurermautomationvariable). Se o valor for um tipo simples, é retornado o mesmo tipo. Se for um tipo complexo, um **PSCustomObject** é retornado.

Os comandos de exemplo a seguir mostram como criar uma variável do tipo cadeia de caracteres e retornar o seu valor.

    New-AzureRmAutomationVariable -ResourceGroupName "ResouceGroup01" 
    –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
    –Encrypted $false –Value 'My String'
    $string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResouceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value

Os comandos de exemplo a seguir mostram como criar uma variável do tipo complexo e retornar as suas propriedades. Nesse caso, é usado um objeto máquina virtual de **Get-AzureRmVm** .

    $vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
    New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm
    
    $vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
    $vmName = $vmValue.Name
    $vmIpAddress = $vmValue.IpAddress


## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Usando uma variável em um runbook ou configuração DSC 

Use a atividade **Set-AutomationVariable** para definir o valor de uma variável da Automação em um runbook ou uma configuração DSC e **Get-AutomationVariable** para recuperá-la.  Você não deve usar os cmdlets **Set-AzureAutomationVariable** ou **Get-AzureAutomationVariable** em um runbook ou configuração DSC, já que eles são menos eficientes do que as atividades de fluxo de trabalho.  Também não é possível recuperar o valor de variáveis protegidas com o **Get-AzureAutomationVariable**.  A única maneira de criar uma variável de dentro de um runbook ou configuração DSC é usar o cmdlet [New-AzureAutomationVariable](/powershell/module/azure/new-azureautomationvariable?view=azuresmps-3.7.0).


### <a name="textual-runbook-samples"></a>Exemplos de runbook textual

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>Definindo e recuperando um valor simples de uma variável

Os comandos de exemplo a seguir mostram como definir e recuperar uma variável em um runbook textual. Nesse exemplo, presume-se que as variáveis do tipo integer denominadas *NumberOfIterations* e *NumberOfRunnings*, além de uma variável do tipo cadeia de caracteres denominada *SampleMessage*, já foram criadas.

    $NumberOfIterations = Get-AutomationVariable -Name 'NumberOfIterations'
    $NumberOfRunnings = Get-AutomationVariable -Name 'NumberOfRunnings'
    $SampleMessage = Get-AutomationVariable -Name 'SampleMessage'
    
    Write-Output "Runbook has been run $NumberOfRunnings times."
    
    for ($i = 1; $i -le $NumberOfIterations; $i++) {
       Write-Output "$i`: $SampleMessage"
    }
    Set-AutomationVariable –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)

#### <a name="setting-and-retrieving-a-complex-object-in-a-variable"></a>Definindo e recuperando um objeto complexo em uma variável

O código de exemplo a seguir mostra como atualizar uma variável com um valor complexo em um runbook textual. Nesse exemplo, uma máquina virtual do Azure é recuperada com **Get-AzureVM** e salva em uma variável de automação existente.  Como explicado em [Tipos de variáveis](#variable-types), ela é armazenada como um PSCustomObject.

    $vm = Get-AzureVM -ServiceName "MyVM" -Name "MyVM"
    Set-AutomationVariable -Name "MyComplexVariable" -Value $vm


No código a seguir, o valor é recuperado da variável e usado para iniciar a máquina virtual.

    $vmObject = Get-AutomationVariable -Name "MyComplexVariable"
    if ($vmObject.PowerState -eq 'Stopped') {
       Start-AzureVM -ServiceName $vmObject.ServiceName -Name $vmObject.Name
    }


#### <a name="setting-and-retrieving-a-collection-in-a-variable"></a>Definindo e recuperando uma coleção em uma variável

O código de exemplo a seguir mostra como usar uma variável com uma coleção de valores complexos em um runbook textual. Nesse exemplo, várias máquinas virtuais do Azure são recuperadas com **Get-AzureVM** e salvas em uma variável de automação existente.  Como explicado em [Tipos de variáveis](#variable-types), elas são armazenadas como uma coleção de PSCustomObjects.

    $vms = Get-AzureVM | Where -FilterScript {$_.Name -match "my"}     
    Set-AutomationVariable -Name 'MyComplexVariable' -Value $vms

No código a seguir, a coleção é recuperada da variável e usada para iniciar cada máquina virtual.

    $vmValues = Get-AutomationVariable -Name "MyComplexVariable"
    ForEach ($vmValue in $vmValues)
    {
       if ($vmValue.PowerState -eq 'Stopped') {
          Start-AzureVM -ServiceName $vmValue.ServiceName -Name $vmValue.Name
       }
    }

#### <a name="setting-and-retrieving-a-secure-string"></a>Configurar e recuperar uma cadeia de caracteres segura

Se você precisar passar uma cadeia de caracteres ou credencial segura, primeiro crie esse ativo como uma credencial ou variável segura. 

    $securecredential = get-credential

    New-AzureRmAutomationCredential -ResourceGroupName contoso `
    -AutomationAccountName contosoaccount -Name ContosoCredentialAsset -Value $securecredential

Depois, você pode passar o nome desse ativo como um parâmetro para o runbook e usar as atividades internas para recuperar e usar em seu script, conforme demonstrado no exemplo de código a seguir:  

    ExampleScript
    Param

      (
         $ContosoCredentialAssetName
      )

    $ContosoCred = Get-AutomationPSCredential -Name $ContosoCredentialAssetName

O exemplo a seguir mostra como chamar seu runbook:  

    $RunbookParams = @{"ContosoCredentialAssetName"="ContosoCredentialAsset"}

    Start-AzureRMAutomationRunbook -ResourceGroupName contoso `
    -AutomationAccountName contosoaccount -Name ExampleScript -Parameters $RunbookParams

### <a name="graphical-runbook-samples"></a>Exemplos de runbook gráfico

Em um runbook gráfico, você adiciona o **Get-AutomationVariable** ou o **Set-AutomationVariable** clicando na variável com o botão direito do mouse no painel Biblioteca do editor gráfico e selecionando a atividade desejada.

![Adicionar variável à tela](media/automation-variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>Definindo valores em uma variável
A imagem a seguir mostra as atividades de exemplo para atualizar uma variável com um valor simples em um runbook gráfico. Nesse exemplo, uma única máquina virtual do Azure é recuperada com **Get-AzureRmVM** e o nome do computador é salvo em uma variável de automação existente com um tipo de cadeia de caracteres.  Não importa se o [link é um pipeline ou uma sequência](automation-graphical-authoring-intro.md#links-and-workflow) , já que esperamos somente um único objeto na saída.

![Definir variável simples](media/automation-variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como interligar as atividades na criação gráfica, confira [Links na criação gráfica](automation-graphical-authoring-intro.md#links-and-workflow)
* Para começar a usar runbooks gráficos, veja [O meu primeiro runbook gráfico](automation-first-runbook-graphical.md) 


