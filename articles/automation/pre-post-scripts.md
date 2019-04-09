---
title: Configurar pré-scripts e pós-scripts na sua implantação do Gerenciamento de Atualizações no Azure (versão prévia)
description: Este artigo descreve como configurar e gerenciar pré-scripts e pós-scripts para implantações de atualização
services: automation
ms.service: automation
ms.subservice: update-management
author: georgewallace
ms.author: gwallace
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 76cd877380090ccad8b2f7b7dbe79957e0eab5bb
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59056670"
---
# <a name="manage-pre-and-post-scripts-preview"></a>Gerenciar pré-scripts e pós-scripts (versão prévia)

Com os pré-scripts e pós-scripts, é possível executar runbooks do PowerShell na sua Conta de Automação antes (pré-tarefa) e depois (pós-tarefa) de uma implantação de atualização. Os pré-scripts e pós-scripts são executados no contexto do Azure, não localmente. Os pré-scripts são executados no início da implantação da atualização. Pós-scripts executam no final da implantação e após quaisquer reinícios configurados.

## <a name="runbook-requirements"></a>Requisitos do runbook

Para um runbook ser usado como um pré-script ou um pós-script, ele precisa ser importado para sua conta de automação e publicado. Para saber mais sobre esse processo, consulte [Publicação de um runbook](manage-runbooks.md#publish-a-runbook).

## <a name="using-a-prepost-script"></a>Usando um script pré/pós

Para usar um pré-script ou um pós-script em uma Implantação de Atualização, comece criando uma Implantação de Atualização. Selecione **Pré-Scripts + Pós-scripts (versão prévia)**. Essa ação abre a página **Selecionar Pré-scripts + Pós-scripts**.  

![Selecionar scripts](./media/pre-post-scripts/select-scripts.png)

Selecione o script que deseja usar; neste exemplo, você usou o runbook **UpdateManagement-TurnOnVms**. Quando você seleciona o runbook, a página **Configurar script** é aberta; forneça valores para os parâmetros e escolha **Pré-Script**. Toque em **OK** quando terminar.

![Configurar script](./media/pre-post-scripts/configure-script.png)

Repita esse processo para o script **UpdateManagement-TurnOffVms**. Porém, ao escolher o **Tipo de script**, escolha **Pós-Script**.

Agora, a seção **Itens selecionados** mostra os dois scripts selecionados; um é um pré-script e o outro é um pós-script.

![Itens selecionados](./media/pre-post-scripts/selected-items.png)

Termine de configurar sua Implantação de Atualização.

Quando sua Implantação de Atualização for concluída, você poderá acessar **Implantações de atualização** para exibir os resultados. Como pode ver, são informados os status do pré-script e do pós-script.

![Atualizar resultados](./media/pre-post-scripts/update-results.png)

Ao clicar na execução da implantação de atualização, você recebe detalhes adicionais dos pré-scripts e pós-scripts. Um link para a fonte do script no momento da execução é fornecido.

![Resultados da execução da implantação](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>Passagem de parâmetros

Ao configurar pré-scripts e pós-scripts, você pode passar parâmetros, assim como agendar um runbook. Os parâmetros são definidos no momento da criação da implantação de atualização. Os scripts prévios e posteriores são compatíveis com os seguintes tipos:

* [char]
* [byte]
* [int]
* [long]
* [decimal]
* [single]
* [double]
* [DateTime]
* [string]

Se precisar de outro tipo de objeto, você poderá convertê-lo em outro tipo usando com sua própria lógica no runbook.

Além dos parâmetros do runbook padrão, um parâmetro adicional é fornecido. Esse parâmetro é **SoftwareUpdateConfigurationRunContext**. Esse parâmetro é uma cadeia de caracteres JSON; se você definir o parâmetro no pré-script ou no pós-script, ele será passado automaticamente pela implantação de atualização. O parâmetro contém informações sobre a implantação de atualização, que é um subconjunto das informações devolvidas pela [API SoftwareUpdateconfigurations](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration). A tabela a seguir mostra as propriedades que são fornecidas na variável:

## <a name="stopping-a-deployment"></a>Parada de uma implantação

Se você deseja parar uma implantação com base em um script de versão anterior, você deve [lançar](automation-runbook-execution.md#throw) uma exceção. Se você não lançar uma exceção, a implantação e o pós-script ainda serão executado. O [exemplo de runbook](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44?redir=0) na galeria mostra como você pode fazer isso. A seguir está um trecho de código do runbook.

```powershell
#In this case, we want to terminate the patch job if any run fails.
#This logic might not hold for all cases - you might want to allow success as long as at least 1 run succeeds
foreach($summary in $finalStatus)
{
    if ($summary.Type -eq "Error")
    {
        #We must throw in order to fail the patch deployment.  
        throw $summary.Summary
    }
}
```

### <a name="softwareupdateconfigurationruncontext-properties"></a>Propriedades de SoftwareUpdateConfigurationRunContext

|Propriedade  |DESCRIÇÃO  |
|---------|---------|
|SoftwareUpdateConfigurationName     | O nome da configuração da atualização de software        |
|SoftwareUpdateConfigurationRunId     | A ID exclusiva para a execução.        |
|SoftwareUpdateConfigurationSettings     | Uma coleção de propriedades relacionadas à configuração da atualização de software         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Os sistemas operacionais selecionados para a implantação de atualização         |
|SoftwareUpdateConfigurationSettings.duration     | A duração máxima da execução da implantação de atualização, `PT[n]H[n]M[n]S` conforme a norma ISO8601, também chamada de "janela de manutenção"          |
|SoftwareUpdateConfigurationSettings.Windows     | Uma coleção de propriedades relacionadas a computadores Windows         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | Uma lista de KBs que são excluídas da implantação de atualização        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | Atualizar as classificações selecionadas para a implantação de atualização        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Reinicializar as configurações para a implantação de atualização        |
|azureVirtualMachines     | Uma lista de resourceIds para as VMs do Azure na implantação de atualização        |
|nonAzureComputerNames|Uma lista dos FQDNs de computadores não Azure na implantação de atualização|

A seguir há um exemplo de cadeia de caracteres JSON passada para o parâmetro **SoftwareUpdateConfigurationRunContext**:

```json
"SoftwareUpdateConfigurationRunContext":{
      "SoftwareUpdateConfigurationName":"sampleConfiguration",
      "SoftwareUpdateConfigurationRunId":"00000000-0000-0000-0000-000000000000",
      "SoftwareUpdateConfigurationSettings":{
         "operatingSystem":"Windows",
         "duration":"PT2H0M",
         "windows":{
            "excludedKbNumbers":[
               "168934",
               "168973"
            ],
            "includedUpdateClassifications":"Critical",
            "rebootSetting":"IfRequired"
         },
         "azureVirtualMachines":[
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-01",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-02",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-03"
         ], 
         "nonAzureComputerNames":[
            "box1.contoso.com",
            "box2.contoso.com"
         ]
      }
   }
```

Um exemplo completo com todas as propriedades pode ser encontrado em: [Configurações de atualização de software – obter por nome](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)

> [!NOTE]
> O `SoftwareUpdateConfigurationRunContext` objeto pode conter entradas duplicadas para as máquinas. Isso pode causar scripts pré e pós seja executada várias vezes na mesma máquina. Solução alternativa para esse comportamento, use `Sort-Object -Unique` para selecionar apenas nomes exclusivos de VM em seu script.

## <a name="samples"></a>Exemplos

Os exemplos para pré-scripts e pós-scripts podem ser encontrados na [Galeria da central de scripts](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) ou importados por meio do portal do Azure. Para importá-los por meio do portal, na sua Conta de Automação, em **Automação de processo**, selecione **Galeria de runbooks**. Use **Gerenciamento de Atualizações** para o filtro.

![Lista de galerias](./media/pre-post-scripts/runbook-gallery.png)

Ou pode pesquisar por elas usando o nome do script, como visto na lista a seguir:

* Gerenciamento de Atualizações – Ligar VMs
* Gerenciamento de Atualizações – Desligar VMs
* Gerenciamento de Atualizações – Executar script localmente
* Gerenciamento de Atualizações – Modelo para scripts pré/pós
* Gerenciamento de Atualizações – Executar script com comando de execução

> [!IMPORTANT]
> Depois de importar os runbooks, você precisa **Publicar** antes que possam ser usados. Para fazer isso, localize o runbook na sua Conta de Automação, selecione **Editar** e clique em **Publicar**.

Todos os exemplos se baseiam no modelo básico que é definido no exemplo a seguir. Esse modelo pode ser usado para criar seu próprio runbook para usar com pré-scripts e pós-scripts. A lógica necessária para autenticar com o Azure e lidar com o parâmetro `SoftwareUpdateConfigurationRunContext` está inclusa.

```powershell
<# 
.SYNOPSIS 
 Barebones script for Update Management Pre/Post 
 
.DESCRIPTION 
  This script is intended to be run as a part of Update Management Pre/Post scripts.  
  It requires a RunAs account. 
 
.PARAMETER SoftwareUpdateConfigurationRunContext 
  This is a system variable which is automatically passed in by Update Management during a deployment. 
#> 
 
param( 
    [string]$SoftwareUpdateConfigurationRunContext 
) 
#region BoilerplateAuthentication 
#This requires a RunAs account 
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection' 
 
Add-AzureRmAccount ` 
    -ServicePrincipal ` 
    -TenantId $ServicePrincipalConnection.TenantId ` 
    -ApplicationId $ServicePrincipalConnection.ApplicationId ` 
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint 
 
$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID 
#endregion BoilerplateAuthentication 
 
#If you wish to use the run context, it must be converted from JSON 
$context = ConvertFrom-Json  $SoftwareUpdateConfigurationRunContext 
#Access the properties of the SoftwareUpdateConfigurationRunContext 
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines | Sort-Object -Unique
$runId = $context.SoftwareUpdateConfigurationRunId 
 
Write-Output $context 
 
#Example: How to create and write to a variable using the pre-script: 
<# 
#Create variable named after this run so it can be retrieved 
New-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false 
#Set value of variable  
Set-AutomationVariable –Name $runId -Value $vmIds 
#> 
 
#Example: How to retrieve information from a variable set during the pre-script 
<# 
$variable = Get-AutomationVariable -Name $runId 
#>      
```

## <a name="interacting-with-non-azure-machines"></a>Interação com computadores não Azure

As pré-tarefas e as pós-tarefas são executadas no contexto do Azure e não têm acesso a computadores não Azure. Para interagir com os computadores não Azure, você precisa ter os seguintes itens:

* Uma conta Executar como
* Um Hybrid Runbook Worker instalado no computador
* Um runbook que deseja executar localmente
* Um runbook pai

Para interagir com computadores não Azure, um runbook pai é executado no contexto do Azure. Esse runbook chama um runbook filho com o cmdlet [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook). É necessário especificar o parâmetro `-RunOn` e fornecer o nome do Hybrid Runbook Worker para o script ser executado.

```powershell
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$resourceGroup = "AzureAutomationResourceGroup"
$aaName = "AzureAutomationAccountName"

$output = Start-AzureRmAutomationRunbook -Name "StartService" -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName -RunOn "hybridWorker"

$status = Get-AzureRmAutomationJob -Id $output.jobid -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName
while ($status.status -ne "Completed")
{ 
    Start-Sleep -Seconds 5
    $status = Get-AzureRmAutomationJob -Id $output.jobid -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName
}

$summary = Get-AzureRmAutomationJobOutput -Id $output.jobid -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName

if ($summary.Type -eq "Error")
{
    Write-Error -Message $summary.Summary
}
```

## <a name="abort-patch-deployment"></a>Anular a implantação de patches

Se seu script pré retornará um erro, você talvez queira anular sua implantação. Para fazer isso, você deve [lançar](/powershell/module/microsoft.powershell.core/about/about_throw) um erro no script para qualquer lógica que constituiria uma falha.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.  
    throw "There was an error, abort deployment"
}
```
## <a name="known-issues"></a>Problemas conhecidos

* Não é permitido passar objetos ou matrizes aos parâmetros ao usar pré-scripts e pós-scripts. Ocorrerá uma falha no runbook.

## <a name="next-steps"></a>Próximas etapas

Continue no tutorial para saber como gerenciar atualizações para as máquinas virtuais do Windows.

> [!div class="nextstepaction"]
> [Gerenciar atualizações e patches para VMs do Microsoft Azure](automation-tutorial-update-management.md)

