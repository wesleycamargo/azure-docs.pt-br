<properties
   pageTitle="Usar a Modelos do Gerenciador de Recursos do Azure para Extensão de Script Personalizado"
	description="Automatização de tarefas de configuração de Máquina Virtual do Azure usando script personalizado com modelos ARM"
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
	ms.date="07/01/2015"
	ms.author="kundanap"/>

# Usar a Extensão de Script Personalizado com modelos do Gerenciador de Recursos do Azure

Este artigo fornece uma visão geral da gravação de modelos de Gerenciador de Recursos do Azure com a Extensão de Script Personalizado para a inicialização de cargas de trabalho em uma VM do Linux ou do Windows.

Para obter uma visão geral da Extensão de Script Personalizado, consulte este artigo <a href="https://azure.microsoft.com/pt-br/documentation/articles/virtual-machines-extensions-customscript/" target="_blank">aqui</a>.

Desde seu lançamento, a Extensão de Script Personalizado foi usada amplamente para configurar as cargas de trabalho de VMs do Windows e do Linux. Com a introdução dos Modelos do Gerenciador de Recursos do Azure, os usuários agora podem criar um modelo único que não apenas provisiona a VM, mas também configura suas cargas de trabalho.

## Visão geral dos Modelos do Gerenciador de Recursos do Azure.

O Modelo do Gerenciador de Recursos do Azure permitem que você especifique declarativamente a infraestrutura IaaS do Azure em linguagem Json definindo as dependências entre os recursos. Para obter uma visão geral detalhada dos Modelos do Gerenciador de Recursos do Azure, consulte os artigos abaixo:

<a href="https://azure.microsoft.com/pt-br/documentation/articles/resource-group-overview/" target="_blank">Visão geral do Grupo de Recursos</a>. <br/> <a href="https://azure.microsoft.com/pt-br/documentation/articles/virtual-machines-deploy-rmtemplates-azure-cli/" target="_blank">Implantação de Modelos com CLI do Azure</a>. <br/> <a href="https://azure.microsoft.com/pt-br/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/" target="_blank">Implantação de Modelos com o Azure PowerShell</a>.

### Pré-Requistes para a execução da extensão do script personalizado

1. Instale os Cmdlets do Azure PowerShell ou CLI do Azure mais recentes <a href="http://azure.microsoft.com/downloads" target="_blank">aqui</a>.
2. Se os scripts serão executados em uma VM existente, verifique se o agente de VM está habilitado na VM. Caso contrário, siga este <a href="https://msdn.microsoft.com/library/azure/dn832621.aspx" target="_blank">artigo</a> para instalar uma.
3. Carregue os scripts que você deseja executar na máquina virtual para o armazenamento do Azure. Os scripts podem vir de um único ou vários contêineres de armazenamento.
4. Como alternativa, os scripts também podem ser carregados para uma conta do Github.
5. O script deve ser criado de forma que o script de entrada que por sua vez é iniciado pela extensão inicia outros scripts.

## Visão geral do uso da Extensão de Script Personalizado com modelos:

Para implantar com modelos, usamos a mesma versão da Extensão de Script Personalizado disponível para APIs de Gerenciamento de Serviços do Azure. A extensão dá suporte os mesmos parâmetros e cenários de como carregar arquivos para a conta de Armazenamento do Azure ou local do Github. A principal diferença ao usar modelos é que a versão exata da extensão deve ser especificada, em vez de especificar a versão do formato de majorversion.*.

 ## Trecho do modelo para a Extensão de Script Personalizado em uma VM do Linux

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

## Trecho do modelo para a Extensão de Script Personalizado em uma VM do Windows

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

Consulte os exemplos abaixo para ver amostras completas de configuração de aplicativos em uma VM usando a extensão de script personalizado.

<a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/" target="_blank">Extensão de Script Personalizado em uma VM do Linux</a>. </br> <a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/" target="_blank">Extensão de Script personalizado em uma VM do Windows</a>.

<!---HONumber=August15_HO9-->