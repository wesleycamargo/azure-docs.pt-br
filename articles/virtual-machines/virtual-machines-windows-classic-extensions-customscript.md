---
title: "Extensão de Script Personalizado em uma VM do Windows | Microsoft Docs"
description: "Automatizar tarefas de configuração de VM do Azure usando a extensão de Script Personalizado para executar scripts do PowerShell em uma VM remota do Windows"
services: virtual-machines-windows
documentationcenter: 
author: kundanap
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: ebb7340a-8f61-4d3c-a290-d7bf8de2d0bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/06/2015
ms.author: kundanap
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 5a66b7a5454ae84c656e5e38e6e7072a5de49ef0


---
# <a name="custom-script-extension-for-windows-virtual-machines"></a>Extensão de Script Personalizado para máquinas virtuais do Windows
Este artigo oferece uma visão geral de como usar a extensão de Script Personalizado nas VMs Windows usando cmdlets do Azure PowerShell com APIs de Gerenciamento de Serviços.

As extensões de VM (máquina virtual) são criadas pela Microsoft e por editores confiáveis de terceiros para estender a funcionalidade da VM. Para obter uma visão geral das extensões de VM, consulte [Recursos e extensões de VM do Azure](virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Saiba como [executar estas etapas usando o modelo do Resource Manager](virtual-machines-windows-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="custom-script-extension-overview"></a>Visão geral da extensão de Script Personalizado
Com a extensão de Script Personalizado para Windows, você pode executar scripts do PowerShell em uma VM remota, sem precisar fazer logon nela. Os scripts podem ser executados após o provisionamento da VM ou a qualquer momento durante o ciclo de vida da VM, sem abrir portas adicionais. O caso de uso mais comum de execução da extensão de Script Personalizado inclui executar, instalar e configurar software adicional na VM depois de provisionada.

### <a name="prerequisites-for-running-the-custom-script-extension"></a>Pré-requisitos para execução da extensão de Script Personalizado
1. Instale a versão 0.8.0 ou posterior dos <a href="http://azure.microsoft.com/downloads" target="_blank">cmdlets do Azure PowerShell</a>.
2. Se quiser que os scripts sejam executados em uma VM existente, verifique se o Agente de VM está habilitado na VM. Se ele não estiver instalado, siga estas [etapas](virtual-machines-windows-classic-agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Se a VM for criada no portal do Azure, o Agente da VM será instalado por padrão.
3. Carregue os scripts que você deseja executar na máquina virtual para o armazenamento do Azure. Os scripts podem vir de um único ou vários contêineres de armazenamento.
4. O script deve ser criado de forma que o script de entrada, que é iniciado pela extensão, inicie outros scripts.

## <a name="custom-script-extension-scenarios"></a>Cenários da extensão de Script Personalizado
### <a name="upload-files-to-the-default-container"></a>Carregar arquivos no contêiner padrão
O exemplo a seguir mostra como é possível executar seus scripts na VM caso eles estejam no contêiner de armazenamento da conta padrão de sua assinatura. Carregue seus scripts para ContainerName. A conta de armazenamento padrão pode ser verificada com o comando **Get-AzureSubscription –Default** .

O exemplo a seguir cria uma VM, mas o mesmo cenário também pode ser executado em uma VM existente.

    # Create a new VM in Azure.
    $vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
    $vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password
    // Add Custom Script extension to the VM. The container name refers to the storage container that contains the file.
    $vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
    New-AzureVM -ServiceName $servicename -Location $location -VMs $vm
    #  After the VM is created, the extension downloads the script from the storage location and executes it on the VM.

    # Viewing the  script execution output.
    $vm = Get-AzureVM -ServiceName $servicename -Name $name
    # Use the position of the extension in the output as index.
    $vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList

### <a name="upload-files-to-a-non-default-storage-container"></a>Carregar arquivos em um contêiner de armazenamento não padrão
Esse cenário mostra como usar um contêiner de armazenamento não padrão dentro da mesma assinatura ou em uma assinatura diferente para carregar arquivos e scripts. Esse exemplo mostra uma VM existente, mas as mesmas operações podem ser feitas durante a criação de uma nova VM.

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM

### <a name="upload-scripts-to-multiple-containers-across-different-storage-accounts"></a>Carregar scripts em vários contêineres entre diferentes contas de armazenamento
  Se os arquivos de script estiverem armazenados em vários contêineres, será necessário fornecer a URL de SAS (assinatura de acesso compartilhado) completa para que os arquivos executem os scripts.

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### <a name="add-the-custom-script-extension-from-the-azure-portal"></a>Adicionar a extensão de Script Personalizado do portal do Azure
Vá até a VM no <a href="https://portal.azure.com/ " target="_blank">portal do Azure</a> e adicione a extensão especificando o arquivo de script a ser executado.

  ![Especifique o arquivo de script][5]

### <a name="uninstall-the-custom-script-extension"></a>Desinstalar a extensão de Script Personalizado
Você pode desinstalar a extensão de Script Personalizado da VM usando o comando a seguir.

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### <a name="use-the-custom-script-extension-with-templates"></a>Usar a extensão de Script Personalizado com modelos
Para saber mais sobre como usar a extensão de Script Personalizado com modelos do Azure Resource Manager, consulte [Usando a extensão do Script Personalizado para VMs do Windows com modelos do Azure Resource Manager](virtual-machines-windows-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

<!--Image references-->
[5]: ./media/virtual-machines-windows-classic-extensions-customscript/addcse.png



<!--HONumber=Nov16_HO3-->


