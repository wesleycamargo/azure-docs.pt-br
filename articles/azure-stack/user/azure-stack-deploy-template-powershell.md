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
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 76348ef6984585856565ff39bfa707f1319477bb
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54107188"
---
# <a name="deploy-a-template-to-azure-stack-using-powershell"></a>Implantar um modelo para o Azure Stack usando o PowerShell

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Você pode usar o PowerShell para implantar modelos do Azure Resource Manager para o Azure Stack. Este artigo descreve como usar o PowerShell para implantar um modelo.

## <a name="run-azurerm-powershell-cmdlets"></a>Executar cmdlets do AzureRM PowerShell

Este exemplo usa **AzureRM** cmdlets do PowerShell e um modelo armazenado no GitHub. O modelo cria uma máquina virtual do Windows Server 2012 R2 Datacenter.

>[!NOTE]
>Antes de tentar Este exemplo, certifique-se de que você já [configurado o PowerShell](azure-stack-powershell-configure-user.md) para um usuário do Azure Stack.

1. Vá para [ https://aka.ms/AzureStackGitHub ](https://aka.ms/AzureStackGitHub) e localize o **101-simple-windows-vm** modelo. Salve o modelo para este local: `C:\templates\azuredeploy-101-simple-windows-vm.json`.
2. Abra um prompt de comando elevado do PowerShell.
3. Substitua `username` e `password` no script a seguir com seu nome de usuário e senha e, em seguida, execute o script:

    ```PowerShell
    # Set deployment variables
    $myNum = "001" # Modify this per deployment
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

- [Implantar modelos com o Visual Studio](azure-stack-deploy-template-visual-studio.md)
