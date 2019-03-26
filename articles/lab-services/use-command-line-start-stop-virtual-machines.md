---
title: Usar ferramentas de linha de comando para iniciar e parar máquinas virtuais do Azure DevTest Labs | Microsoft Docs
description: Saiba como usar as ferramentas de linha de comando para iniciar e parar máquinas virtuais no Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 9b7df83b710bac0b37ac28c432f63a47ddda21d1
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439757"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Usar ferramentas de linha de comando para iniciar e parar máquinas de virtuais do Azure DevTest Labs
Este artigo mostra como usar o Azure PowerShell ou CLI do Azure para iniciar ou parar máquinas virtuais em um laboratório no Azure DevTest Labs. Você pode criar scripts CLI/PowerShell para automatizar essas operações. 

## <a name="overview"></a>Visão geral
O Azure DevTest Labs é uma maneira de criar ambientes de desenvolvimento/teste rápidos, fáceis e descomplicados. Ele permite gerenciar o custo, provisionar máquinas virtuais rapidamente e minimizar o desperdício.  Há recursos internos no portal do Azure que permitem que você configure as VMs em um laboratório automaticamente, iniciar e parar em horários específicos. 

No entanto, em alguns cenários, você talvez queira automatizar Iniciando e parando de VMs de scripts do PowerShell/CLI. Ele fornece certa flexibilidade com Iniciando e parando máquinas individuais a qualquer momento, em vez de em horários específicos. Aqui estão algumas das situações nas quais executar essas tarefas usando scripts seria útil.

- Ao usar um aplicativo da camada 3 como parte de um ambiente de teste, as camadas precisarão ser iniciados em uma sequência. 
- Desliga uma máquina virtual quando um personalizado critérios são atendidos para economizar dinheiro. 
- Use-o como uma tarefa em um fluxo de trabalho de CI/CD para começar do início do fluxo, usar as VMs como máquinas de compilação, máquinas ou infraestrutura de teste e parar as VMs quando o processo for concluído. Um exemplo disso seria a fábrica de imagem personalizada com o Azure DevTest Labs.  

## <a name="azure-powershell"></a>Azure PowerShell
O seguinte script do PowerShell inicia uma VM em um laboratório. [Invoke-AzureRmResourceAction](/powershell/module/azurerm.resources/invoke-azurermresourceaction?view=azurermps-6.13.0) é o principal foco para esse script. O **ResourceId** parâmetro é a ID de recurso totalmente qualificado para a VM no laboratório. O **ação** parâmetro é onde o **inicie** ou **parar** opções forem definidas, dependendo do que é necessário.

```powershell
# The id of the subscription
$subscriptionId = "111111-11111-11111-1111111"

# The name of the lab
$devTestLabName = "yourlabname"

# The name of the virtual machine to be started
$vMToStart = "vmname"

# The action on the virtual machine (Start or Stop)
$vmAction = "Start"

# Select the Azure subscription
Select-AzureRMSubscription -SubscriptionId $subscriptionId

# Get the lab information
if ($(Get-Module -Name AzureRM).Version.Major -eq 6) {
    $devTestLab = Get-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' -Name $devTestLabName
} else {
    $devTestLab = Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $devTestLabName
}

# Start the VM and return a succeeded or failed status
$returnStatus = Invoke-AzureRmResourceAction `
                    -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vMToStart" `
                    -Action $vmAction `
                    -Force

if ($returnStatus.Status -eq 'Succeeded') {
    Write-Output "##[section] Successfully updated DTL machine: $vMToStart, Action: $vmAction"
}
else {
    Write-Error "##[error]Failed to update DTL machine: $vMToStart, Action: $vmAction"
}
```


## <a name="azure-cli"></a>CLI do Azure
O [CLI do Azure](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) é outra maneira de automatizar o início e máquinas virtuais do DevTest Labs. CLI do Azure pode ser [instalado](/cli/azure/install-azure-cli?view=azure-cli-latest) em diferentes sistemas operacionais. O script a seguir fornece comandos para iniciar e interromper uma VM em um laboratório. 

```azurecli
# Sign in to Azure
az login 

## Get the name of the resource group that contains the lab
az resource list --resource-type "Microsoft.DevTestLab/labs" --name "yourlabname" --query "[0].resourceGroup" 

## Start the VM
az lab vm start --lab-name yourlabname --name vmname --resource-group labResourceGroupName

## Stop the VM
az lab vm stop --lab-name yourlabname --name vmname --resource-group labResourceGroupName
```


## <a name="next-steps"></a>Próximas etapas
Consulte o artigo a seguir para usar o portal do Azure para fazer essas operações: [Reiniciar uma VM](devtest-lab-restart-vm.md).