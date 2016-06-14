<properties
   pageTitle="Criando modelos com extensões de VM do Windows | Microsoft Azure"
   description="Saiba mais sobre como criar modelos do Azure Resource Manager com extensões de VMs do Windows"
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

# Criando modelos do Azure Resource Manager com extensões de VM do Windows

[AZURE.INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

No Azure PowerShell, execute o seguinte cmdlet do Azure PowerShell:

      Get-AzureVMAvailableExtension


Esse cmdlet retorna o nome do editor, o nome da extensão e a versão da seguinte maneira:

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Essas três propriedades são mapeadas para “publisher”, “type” e “typeHandlerVersion”, respectivamente, no trecho de código do modelo acima.

>[AZURE.NOTE]É sempre recomendado usar a versão mais recente da extensão para obter a funcionalidade mais atualizada.

## Identificando o esquema dos parâmetros de configuração da extensão

A próxima etapa da criação de um modelo de extensão é identificar o formato para fornecer os parâmetros de configuração. Cada extensão dá suporte a seu próprio conjunto de parâmetros.

Para obter configurações de exemplo para extensões do Windows, veja [Amostras de extensões do Windows](virtual-machines-windows-extensions-configuration-samples.md).


Consulte o seguinte para obter um modelo completo com extensões de VM.

[Extensão de script personalizado em uma VM do Windows](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)


Depois de criar o modelo, você poderá implantá-lo usando o Azure PowerShell.

<!---HONumber=AcomDC_0601_2016-->