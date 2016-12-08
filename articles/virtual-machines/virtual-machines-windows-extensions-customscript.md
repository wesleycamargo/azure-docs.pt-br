---
title: Scripts personalizados em VMs do Windows com o uso de modelos | Microsoft Docs
description: "Automatizar tarefas de configuração de VM do Windows usando a extensão do Script Personalizado com modelos do Gerenciador de Recursos"
services: virtual-machines-windows
documentationcenter: 
author: kundanap
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f4181fee-7a9d-4a1c-b517-52956f5b7fa1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: ac63fd346f158d52b359f2d6b398d0d00ea0c67e


---
# <a name="windows-vm-custom-script-extensions-with-azure-resource-manager-templates"></a>Extensões de Script Personalizado de VM do Windows com modelos do Azure Resource Manager
[!INCLUDE [virtual-machines-common-extensions-customscript](../../includes/virtual-machines-common-extensions-customscript.md)]

## <a name="template-example-for-a-windows-vm"></a>Exemplo de modelo para uma VM do Windows
Defina o recurso a seguir na seção de Recursos do modelo.

       {
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "MyCustomScriptExtension",
       "apiVersion": "2015-05-01-preview",
       "location": "[parameters('location')]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"
       ],
       "properties": {
           "publisher": "Microsoft.Compute",
           "type": "CustomScriptExtension",
           "typeHandlerVersion": "1.7",
           "autoUpgradeMinorVersion":true,
           "settings": {
               "fileUris": [
               "http://Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
           ],
           "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
         }
       }
     }

No exemplo anterior, substitua a URL do arquivo e o nome do arquivo por suas próprias configurações.
Depois de criar o modelo, você poderá implantá-lo usando o Azure PowerShell.

Se você quiser manter particulares os parâmetros e as URLs de script, pode definir a URL de script como **particular**. Se a URL de script for definida como **particular**, ela só poderá ser acessada com um nome de conta de armazenamento e uma chave enviada como configurações protegidas. Os parâmetros do script também podem ser fornecidos como configurações protegidas com a versão 1.7 ou posterior da extensão de Script Personalizado.

## <a name="template-example-for-a-windows-vm-with-protected-settings"></a>Exemplo de modelo para uma VM do Windows com configurações protegidas
        {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        "settings": {
        "fileUris": [
        "http: //Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
        ]
        },
        "protectedSettings": {
        "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -start.ps1",
        "storageAccountName": "yourStorageAccountName",
        "storageAccountKey": "yourStorageAccountKey"
        }
        }
Para obter informações sobre o esquema das versões mais recentes da extensão de Script Personalizado, consulte [Exemplos de configuração de extensão de VM do Windows do Azure](virtual-machines-windows-extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para obter exemplos de configuração de aplicativo em uma VM usando a extensão de Script Personalizado, consulte [Extensão de Script Personalizado em uma VM Windows](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/).




<!--HONumber=Nov16_HO3-->


