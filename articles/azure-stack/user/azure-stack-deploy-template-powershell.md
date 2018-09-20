---
title: Implantar modelos usando o PowerShell no Azure Stack | Microsoft Docs
description: Implante um modelo para o Azure Stack usando o PowerShell.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 4b254f9a4446a1b0ff400e0d63effe68fc4f82b4
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363659"
---
# <a name="deploy-a-template-to-azure-stack-using-powershell"></a>Implantar um modelo para o Azure Stack usando o PowerShell

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Você pode usar o PowerShell para implantar modelos do Azure Resource Manager para o Azure Stack. Este artigo descreve como usar o PowerShell para implantar um modelo.

## <a name="run-azurerm-powershell-cmdlets"></a>Executar cmdlets do AzureRM PowerShell

Este exemplo usa cmdlets do AzureRM PowerShell e um modelo armazenado no GitHub. O modelo cria uma máquina virtual do Windows Server 2012 R2 Datacenter.

>[!NOTE]
>Antes de tentar Este exemplo, certifique-se de que você já [configurado o PowerShell](azure-stack-powershell-configure-user.md) para um usuário do Azure Stack.

1. Vá para [ http://aka.ms/AzureStackGitHub ](http://aka.ms/AzureStackGitHub) e localize o **101-simple-windows-vm** modelo. Salve o modelo para este local: c:\\modelos\\azuredeploy-101-simple-windows-vm.json.
2. Abra um prompt de comando elevado do PowerShell.
3. Substitua *nome de usuário* e *senha* no script a seguir com seu nome de usuário e senha e, em seguida, execute o script.

   ```PowerShell
   # Set deployment variables
   $myNum = "001" #Modify this per deployment
   $RGName = "myRG$myNum"
   $myLocation = "local"
   
   # Create resource group for template deployment
   New-AzureRmResourceGroup -Name $RGName -Location $myLocation
   
   # Deploy simple IaaS template
   New-AzureRmResourceGroupDeployment `
       -Name myDeployment$myNum `
       -ResourceGroupName $RGName `
       -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
       -NewStorageAccountName mystorage$myNum `
       -DnsNameForPublicIP mydns$myNum `
       -AdminUsername <username> `
       -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
       -VmName myVM$myNum `
       -WindowsOSVersion 2012-R2-Datacenter
   ```

   >[!IMPORTANT]
   >Sempre que você executar esse script, incremente o valor da `$myNum` parâmetro para evitar a substituição de sua implantação.

4. Abra o portal, selecione Azure Stack **navegue**e, em seguida, selecione **máquinas virtuais** para encontrar sua nova máquina virtual (**myDeployment001**).

## <a name="next-steps"></a>Próximas etapas

[Implantar modelos com o Visual Studio](azure-stack-deploy-template-visual-studio.md)
