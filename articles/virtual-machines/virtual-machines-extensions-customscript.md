<properties
   pageTitle="Extensão de Script Personalizado em uma VM do Windows | Microsoft Azure"
   description="Automatizar tarefas de configuração de VM do Azure usando a extensão de Script Personalizado para executar scripts do PowerShell em uma VM remota do Windows"
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/06/2015"
   ms.author="kundanap"/>

# Extensão de Script Personalizado para máquinas virtuais do Windows

Este artigo fornece uma visão geral de como usar a extensão de Script Personalizado nas VMs do Windows usando cmdlets do Azure PowerShell.

As extensões de VM (máquina virtual) são criadas pela Microsoft e por editores confiáveis de terceiros para estender a funcionalidade da VM. Para obter uma visão geral das extensões de VM, veja [Recursos e extensões de VM do Azure](virtual-machines-extensions-features.md).

Link:[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-machines-extensions-customscript%20-with%20template.md).


## Visão geral da extensão de Script Personalizado

A extensão de Script Personalizado para Windows permite executar scripts do PowerShell em uma VM remota, sem precisar fazer logon nela. Os scripts podem ser executados após o provisionamento da VM ou a qualquer momento durante o ciclo de vida da VM sem a necessidade de abrir portas adicionais na VM. O caso de uso mais comum da Extensão de Script Personalizado inclui execução, instalação e configuração de software adicional na VM depois de provisionada.

### Pré-requisitos para execução da Extensão de Script Personalizado

1. Instale a versão 0.8.0 ou posterior dos cmdlets do Azure PowerShell <a href="http://azure.microsoft.com/downloads" target="_blank">aqui</a>.
2. Se os scripts forem executados em uma VM existente, verifique se o Agente de VM está habilitado na VM; se não estiver, siga este <a href="https://msdn.microsoft.com/library/azure/dn832621.aspx" target="_blank">artigo</a> para instalar um. (Se você estiver provisionando a VM a partir da galeria do Azure, os agentes da VM estarão habilitados por padrão e não será necessário habilitá-los)
3. Carregue os scripts que você deseja executar na máquina virtual para o armazenamento do Azure. Os scripts podem vir de um único ou vários contêineres de armazenamento.
4. O script deve ser criado de forma que o script de entrada, que é iniciado pela extensão, inicie outros scripts.

## Cenários da extensão de Script Personalizado

### Carregar arquivos no contêiner padrão

Se você tiver scripts no contêiner de armazenamento da conta padrão de sua assinatura, o exemplo a seguir mostra como é possível executá-los na VM. O ContainerName é onde você carrega os scripts. A conta de armazenamento padrão pode ser verificada com o comando **Get-AzureSubscription –Default**.

O exemplo a seguir cria uma nova VM, mas o mesmo cenário também pode ser executado em uma VM existente.

    # create a new VM in Azure.
    $vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
    $vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password
    // Add Custom Script Extension to the VM. The container name refer to the storage container which contains the file.
    $vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
    New-AzureVM -ServiceName $servicename -Location $location -VMs $vm
    #  After the VM is created, the extension downloads the script from the storage location and executes it on the VM.

    # Viewing the  script execution output.
    $vm = Get-AzureVM -ServiceName $servicename -Name $name
    # Use the position of the extension in the output as index.
    $vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList

### Carregar arquivos em um contêiner de armazenamento não padrão

Esse cenário mostra como usar um armazenamento fora do padrão dentro da mesma assinatura ou em uma assinatura diferente para carregar arquivos e scripts. Aqui, usaremos uma máquina virtual existente, mas as mesmas operações podem ser feitas durante a criação de uma nova VM.

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM

### Carregar scripts em vários contêineres entre diferentes contas de armazenamento

  Se os arquivos de script estiverem armazenados em vários contêineres, para executar os scripts, é preciso fornecer a URL SAS completa dos arquivos.

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### Adicionar a extensão de Script Personalizado por meio do portal do Azure

Navegue até a VM no <a href="https://portal.azure.com/ " target="_blank">portal do Azure</a> e adicione a extensão especificando o arquivo de script a ser executado.

  ![][5]


### Desinstalando a extensão de Script Personalizado

A Extensão de Script Personalizado pode ser desinstalada da VM usando o cmdlet a seguir.

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### Usando a extensão de Script Personalizado com modelos

Para saber mais sobre como usar a extensão de Script Personalizado com modelos do Gerenciador de Recursos do Azure, confira a documentação [aqui](virtual-machines-extensions-customscript%20-with%20template.md).

<!--Image references-->
[5]: ./media/virtual-machines-extensions-customscript/addcse.png

<!---HONumber=AcomDC_1223_2015-->