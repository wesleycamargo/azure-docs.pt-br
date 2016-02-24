<properties
   pageTitle="Scripts personalizados em VMs com o uso de modelos | Microsoft Azure"
   description="Automatizar tarefas de configuração de VM do Azure no Windows e Linux usando a extensão de Script Personalizado com modelos do Gerenciador de Recursos"
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
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure-services"
   ms.date="11/01/2015"
   ms.author="kundanap"/>

# Usando a extensão de Script Personalizado com modelos do Gerenciador de Recursos do Azure

Este artigo fornece uma visão geral de como gravar modelos do Gerenciador de Recursos do Azure com a extensão de Script Personalizado para a inicialização de cargas de trabalho em uma VM do Linux ou do Windows.

Para obter uma visão geral da extensão de Script Personalizado, consulte o artigo [aqui](virtual-machines-extensions-customscript.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-extensions-customscript.md).

Desde seu lançamento, a extensão de Script Personalizado foi amplamente usada para configurar as cargas de trabalho de VMs do Windows e do Linux. Com a introdução dos modelos do Gerenciador de Recursos do Azure, os usuários agora podem criar um modelo único que não apenas provisiona a VM, mas também configura as cargas de trabalho nela.

## Sobre os modelos do Gerenciador de Recursos do Azure

O modelo do Gerenciador de Recursos do Azure permite que você especifique de forma declarativa a infraestrutura IaaS do Azure na linguagem JSON definindo as dependências entre recursos. Para obter uma visão geral detalhada dos modelos do Gerenciador de Recursos do Azure, consulte os seguintes artigos:

- [Visão geral do Grupo de Recursos](../resource-group-overview)
- [Implantação de modelos com CLI do Azure](virtual-machines-deploy-rmtemplates-azure-cli)
- [Implantação de modelos com Azure PowerShell](virtual-machines-deploy-rmtemplates-powershell)

### Pré-requisitos

1. Instalar os Cmdlets do Azure PowerShell ou CLI do Azure mais recentes [aqui](https://azure.microsoft.com/downloads/).
2. Se os scripts forem executados em uma VM existente, verifique se o agente de VM está habilitado na VM, caso contrário, siga [este procedimento](virtual-machines-extensions-install) para instalar uma.
3. Carregue os scripts que você deseja executar na máquina virtual para o armazenamento do Azure. Os scripts podem vir de um único ou vários contêineres de armazenamento.
4. Como alternativa, os scripts também podem ser carregados para uma conta do Github.
5. O script deve ser criado de forma que o script de entrada que por sua vez é iniciado pela extensão inicia outros scripts.

## Usando a extensão de script personalizado

Para implantar com modelos, usamos a mesma versão da Extensão de Script Personalizado disponível para APIs de Gerenciamento de Serviços do Azure. A extensão dá suporte os mesmos parâmetros e cenários de como carregar arquivos para a conta de Armazenamento do Azure ou local do Github. A principal diferença ao usar modelos é que a versão exata da extensão deve ser especificada, em vez de especificar a versão do formato de majorversion.*.

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
      "fileUris": [ "https: //raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-ubuntu/mongo-install-ubuntu.sh                        ],
      "commandToExecute": "shmongo-install-ubuntu.sh"
      }
    }
    }

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

Nos exemplos acima, substitua a URL e o nome do arquivo por suas próprias configurações.

Depois de criar o modelo, você pode implantá-lo usando a CLI do Azure ou o Azure PowerShell.

Confira os exemplos abaixo para ver amostras completas de configuração de aplicativos em uma VM usando a extensão de Script Personalizado.

<a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/" target="_blank">Extensão de Script personalizado em uma VM do Linux</a>. </br> <a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/" target="_blank">Extensão de Script personalizado em uma VM do Windows</a>.

<!---HONumber=AcomDC_0128_2016-->