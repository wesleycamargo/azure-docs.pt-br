<properties
   pageTitle="Scripts personalizados em VMs do Windows com o uso de modelos | Microsoft Azure"
   description="Automatizar tarefas de configuração de VM do Windows usando a extensão do Script Personalizado com modelos do Gerenciador de Recursos"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# Usando a extensão do Script Personalizado para VMs do Windows com modelos do Azure Resource Manager

[AZURE.INCLUDE [virtual-machines-common-extensions-customscript](../../includes/virtual-machines-common-extensions-customscript.md)]

## Exemplo de modelo para uma VM do Windows

Defina o recurso a seguir na seção de Recursos do modelo

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

No exemplo acima, substitua a URL e o nome do arquivo por suas próprias configurações. Depois de criar o modelo, você poderá implantá-lo usando o Azure PowerShell.

Em muitos cenários, os clientes desejam manter as URLs de script e os parâmetros como privados. Para conseguir isso, mantenha a URL de script como privada, para que ela somente possa ser acessada com um nome e uma chave da conta de armazenamento, enviados como configurações protegidas. Além disso, os parâmetros do script também podem ser fornecidos como configurações protegidas com a versão 1.7 ou posterior, para a extensão de script personalizado do Windows.

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
Para obter informações sobre o esquema das versões mais recentes da extensão de script personalizado, consulte a documentação [aqui](virtual-machines-windows-extensions-configuration-samples.md)

Consulte o exemplo abaixo para ver uma amostra completa da configuração de aplicativos em uma VM que usa a extensão do Script Personalizado.

* [Extensão do Script Personalizado em uma VM do Windows](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)

<!---HONumber=AcomDC_0803_2016-->