---
title: Implantar modelos usando o PowerShell na pilha do Azure | Microsoft Docs
description: Implante um modelo com a pilha do Azure usando o PowerShell.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 4af82deef029120aa2699e7c69c501ae61a1e8bd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-a-template-to-azure-stack-using-powershell"></a>Implantar um modelo com a pilha do Azure usando o PowerShell

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode usar o PowerShell para implantar modelos do Azure Resource Manager pilha do Azure. Este artigo mostra como usar o PowerShell para implantar um modelo.

## <a name="run-azurerm-powershell-cmdlets"></a>Executar cmdlets do AzureRM PowerShell

Este exemplo usa um modelo armazenado no GitHub e cmdlets do PowerShell de AzureRM. O modelo cria uma máquina de virtual do Windows Server 2012 R2 Datacenter.

>[!NOTE]
>Antes de você testar este exemplo, certifique-se de que você já [configurado PowerShell](azure-stack-powershell-configure-user.md) para um usuário de pilha do Azure.

1. Vá para <http://aka.ms/AzureStackGitHub> e localize o **101-simples-windows-vm** modelo. Salvar o modelo neste local: c:\\modelos\\azuredeploy-101-simples-windows-vm.json.
2. Abra um prompt de comando do PowerShell.
3. Substituir *username* e *senha* no script a seguir com o nome de usuário e senha e, em seguida, execute o script.

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

   >[!IMPORTANT]
   >Toda vez que você executar esse script, incremente o valor do parâmetro "$myNum" para evitar a substituição de sua implantação.

4. Abra a seleção de pilha do Azure portal, **procurar**e, em seguida, selecione **máquinas virtuais** para localizar a nova máquina virtual (*myDeployment001*).

## <a name="next-steps"></a>Próximas etapas

[Implantar modelos com o Visual Studio](azure-stack-deploy-template-visual-studio.md)
