---
title: Implantar modelos com o PowerShell na pilha do Azure | Microsoft Docs
description: "Saiba como implantar uma máquina virtual usando um modelo do Gerenciador de recursos e o PowerShell."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: d271b155d65a7dd95a92262da338cf3a272d140b
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-templates-in-azure-stack-using-powershell"></a>Implantar modelos no Azure Stack usando o PowerShell

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Use o PowerShell para implantar modelos do Azure Resource Manager o Kit de desenvolvimento de pilha do Azure.  Modelos do Gerenciador de recursos implantar e provisionar todos os recursos para seu aplicativo em uma única operação coordenado.

## <a name="run-azurerm-powershell-cmdlets"></a>Executar cmdlets do AzureRM PowerShell
Neste exemplo, execute um script para implantar uma máquina virtual para o Kit de desenvolvimento de pilha do Azure usando um modelo do Gerenciador de recursos.  Antes de prosseguir, verifique se você tem [configurado do PowerShell](azure-stack-powershell-configure-user.md)  

O VHD usado nesse modelo de exemplo é WindowsServer-2012 R2 Datacenter.

1. Vá para <http://aka.ms/AzureStackGitHub>, procure o **101-simples-windows-vm** modelo e salve-o no seguinte local: c:\\modelos\\ azuredeploy-101-simples-windows-vm.json.
2. No PowerShell, execute o seguinte script de implantação. Substituir *username* e *senha* com seu nome de usuário e senha. Em usos subsequentes, incremente o valor para o *$myNum* parâmetro para evitar a substituição de sua implantação.
   
   ```PowerShell
       # Set Deployment Variables
       $myNum = "001" #Modify this per deployment
       $RGName = "myRG$myNum"
       $myLocation = "local"
   
       # Create Resource Group for Template Deployment
       New-AzureRmResourceGroup -Name $RGName -Location $myLocation
   
       # Deploy Simple IaaS Template
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
3. Abrir o Azure pilha portal, **procurar**, clique em **máquinas virtuais**e procure a nova máquina virtual (*myDeployment001*).


## <a name="next-steps"></a>Próximas etapas
[Implantar modelos com o Visual Studio](azure-stack-deploy-template-visual-studio.md)

