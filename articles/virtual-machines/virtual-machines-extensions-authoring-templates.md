<properties
   pageTitle="Criando modelos com extensões de VM do Azure | Microsoft Azure"
	description="Saiba mais sobre como criar modelos com extensões"
	services="virtual-machines"
	documentationCenter=""
	authors="kundanap"
	manager="timlt"
	editor=""/>

<tags
   ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="09/01/2015"
	ms.author="kundanap"/>

# Criando modelos do Gerenciador de Recursos do Azure com extensões de VM

## Visão geral dos Modelos do Gerenciador de Recursos do Azure.

O Modelo do Gerenciador de Recursos do Azure permite que você especifique declarativamente a infraestrutura IaaS do Azure em linguagem Json definindo as dependências entre os recursos. Para obter uma visão geral detalhada dos Modelos do Gerenciador de Recursos do Azure, consulte os artigos abaixo:

<a href="https://azure.microsoft.com/pt-BR/documentation/articles/resource-group-overview/" target="_blank">Visão geral do grupo de recursos</a>. <br/> <a href="https://azure.microsoft.com/pt-BR/documentation/articles/virtual-machines-deploy-rmtemplates-azure-cli/" target="_blank">Implantação de modelos com a CLI do Azure</a>. <br/> <a href="https://azure.microsoft.com/pt-BR/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/" target="_blank">Implantação de modelos com o Azure Powershell</a>.

## Trecho do exemplo de modelo para Extensões de VM.
O trecho do modelo para a implantação de extensões é o seguinte:

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

Para ver um exemplo de configuração de extensões do Windows, clique na documentação dos [exemplos de extensões do Windows](virtual-machines-extensions-configuration-samples-windows.md).

Para ver um exemplo de configuração de extensões do Linux, clique na documentação dos [exemplos de extensões do Linux](virtual-machines-extensions-configuration-samples-linux.md).

Consulte o seguinte com relação aos modelos de VM para obter um modelo completo com extensões de VM.

<a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/" target="_blank">Extensão de script personalizado em uma VM Linux</a>. </br> <a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/" target="_blank">Extensão de script personalizado em uma VM do Windows</a>.

Depois de criar o modelo, você poderá implantá-lo usando a CLI do Azure ou o Azure Powershell.

<!---HONumber=September15_HO1-->