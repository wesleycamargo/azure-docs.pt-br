---
title: Scripts personalizados em VMs do Windows com o uso de modelos | Microsoft Docs
description: Automatizar tarefas de configuração de VM do Windows usando a extensão do Script Personalizado com modelos do Gerenciador de Recursos
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap

---
# Extensões de Script Personalizado de VM do Windows com modelos do Azure Resource Manager
[!INCLUDE [virtual-machines-common-extensions-customscript](../../includes/virtual-machines-common-extensions-customscript.md)]

## Exemplo de modelo para uma VM do Windows
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

No exemplo anterior, substitua a URL do arquivo e o nome do arquivo por suas próprias configurações. Depois de criar o modelo, você poderá implantá-lo usando o Azure PowerShell.

Se você quiser manter particulares os parâmetros e as URLs de script, pode definir a URL de script como **particular**. Se a URL de script for definida como **particular**, ela só poderá ser acessada com um nome de conta de armazenamento e uma chave enviada como configurações protegidas. Os parâmetros do script também podem ser fornecidos como configurações protegidas com a versão 1.7 ou posterior da extensão de Script Personalizado.

## Exemplo de modelo para uma VM do Windows com configurações protegidas
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
Para obter informações sobre o esquema das versões mais recentes da extensão de Script Personalizado, consulte [Exemplos de configuração de extensão de VM do Windows do Azure](virtual-machines-windows-extensions-configuration-samples.md).

Para obter exemplos de configuração de aplicativo em uma VM usando a extensão de Script Personalizado, consulte [Extensão de Script Personalizado em uma VM Windows](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/).

<!---HONumber=AcomDC_0824_2016-->