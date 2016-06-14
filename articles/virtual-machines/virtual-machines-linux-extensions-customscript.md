<properties
   pageTitle="Scripts personalizados em VMs do Linux com o uso de modelos | Microsoft Azure"
   description="Automatizar tarefas de configuração de VM do Linux usando a extensão do Script Personalizado com modelos do Gerenciador de Recursos"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# Usando a extensão do Script Personalizado para VMs do Linux com modelos do Azure Resource Manager

Este artigo fornece uma visão geral de como gravar modelos do Azure Resource Manager com a extensão do Script Personalizado para a inicialização de cargas de trabalho em uma VM do Linux.

[AZURE.INCLUDE [virtual-machines-common-extensions-customscript](../../includes/virtual-machines-common-extensions-customscript.md)]

## Exemplo de modelo para uma VM do Linux

Defina o recurso de extensão a seguir na seção de Recursos do modelo

      {
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "MyCustomScriptExtension",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('location')]",
    "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
    "properties":
    {
      "publisher": "Microsoft.OSTCExtensions",
      "type": "CustomScriptForLinux",
      "typeHandlerVersion": "1.2",
      "settings": {
      "fileUris": [ "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-ubuntu/mongo-install-ubuntu.sh"],
      "commandToExecute": "sh mongo-install-ubuntu.sh"
      }
    }
    }

No exemplo acima, substitua a URL e o nome do arquivo por suas próprias configurações.

Depois de criar o modelo, é possível implantá-lo usando a CLI do Azure.

Consulte o exemplo abaixo para ver uma amostra completa da configuração de aplicativos em uma VM que usa a extensão do Script Personalizado.

* [Extensão do Script personalizado em uma VM do Linux](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

<!---HONumber=AcomDC_0601_2016-->