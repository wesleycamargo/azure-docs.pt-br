<properties
   pageTitle="Criando modelos com extensões de VM do Azure | Microsoft Azure"
   description="Saiba mais sobre como criar modelos com extensões"
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/01/2015"
   ms.author="kundanap"/>

# Criando modelos do Gerenciador de Recursos do Azure com extensões de VM

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Este artigo aborda o uso do modelo de implantação do Gerenciador de Recursos.

## Visão geral dos Modelos do Gerenciador de Recursos do Azure.

O Modelo do Gerenciador de Recursos do Azure permite que você especifique declarativamente a infraestrutura IaaS do Azure em linguagem Json definindo as dependências entre os recursos. Para obter uma visão geral detalhada dos Modelos do Gerenciador de Recursos do Azure, consulte os artigos abaixo:

[Visão geral do Grupo de Recursos](../resource-group-overview.md)

## Trecho do exemplo de modelo para Extensões de VM.
Implantar a Extensão de VM como parte do modelo do Gerenciador de Recursos do Azure requer que você especifique de forma declarativa a configuração da Extensão no modelo. Veja abaixo o formato para especificar a configuração da extensão.

      {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "MyExtension",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
      "properties":
      {
      "publisher": "Publisher Namespace",
      "type": "extension Name",
      "typeHandlerVersion": "extension version",
      "settings": {
      // Extension specific configuration goes in here.
      }
      }
      }

Como você pode ver no exemplo acima, o modelo de extensão contém duas partes principais:

1. Nome da extensão, editor e versão.
2. Configuração da Extensão.

## Identificando o editor, o tipo e a typeHandlerVersion de qualquer extensão.

As Extensões de VM do Azure são publicadas pela Microsoft e por editores terceiros confiáveis, e cada extensão é identificada exclusivamente por editor, tipo e typeHandlerVersion. Elas podem ser determinadas da seguinte maneira:

No Azure PowerShell, execute o seguinte cmdlet do Azure PowerShell:

      Get-AzureVMAvailableExtension

Na CLI do Azure, execute o seguinte cmdlet do Azure PowerShell:

      Azure VM Extension list

Esse cmdlet retorna o nome do editor, o nome da extensão e a versão da seguinte maneira:

       Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Essas três propriedades são mapeadas como "publisher”, "type" e "typeHandlerVersion" respectivamente no trecho de modelo acima. Observação: sempre recomendamos o uso da versão mais recente da extensão para obter a funcionalidade mais atualizada.

## Identificando o esquema dos parâmetros de configuração da extensão

A próxima etapa da criação do modelo de extensão é identificar o formato a fim de fornecer os parâmetros de configuração. Cada extensão dá suporte a seu próprio conjunto de parâmetros.

Para ver um exemplo de configuração das Extensões do Windows, clique na documentação [Exemplos de Extensões do Windows](virtual-machines-extensions-configuration-samples-windows.md).

Para ver um exemplo de configuração das Extensões do Linux, clique na documentação [Exemplos de Extensões do Linux](virtual-machines-extensions-configuration-samples-linux.md).

Consulte o seguinte com relação aos modelos de VM para obter um modelo completo com extensões de VM.

[Extensão de script personalizado em uma VM do Windows](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)

[Extensão de script personalizado em uma VM do Linux](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

Depois de criar o modelo, você poderá implantá-lo usando a CLI do Azure ou o Azure Powershell.

<!---HONumber=Sept15_HO4-->