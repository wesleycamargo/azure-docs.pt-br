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
   ms.date="11/01/2015"
   ms.author="kundanap"/>

# Usando a extensão do Script Personalizado para VMs do Windows com modelos do Azure Resource Manager

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-classic-extensions-customscript.md).

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
           "typeHandlerVersion": "1.4",
           "settings": {
               "fileUris": [
               "http://Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
           ],
           "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
         }
       }
     }
     
No exemplo acima, substitua a URL e o nome do arquivo por suas próprias configurações.

Depois de criar o modelo, você poderá implantá-lo usando o Azure PowerShell.

Consulte o exemplo abaixo para ver uma amostra completa da configuração de aplicativos em uma VM que usa a extensão do Script Personalizado.

* [Extensão do Script Personalizado em uma VM do Windows](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)

<!---HONumber=AcomDC_0323_2016-->