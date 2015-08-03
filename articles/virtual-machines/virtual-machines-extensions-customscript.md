<properties
   pageTitle="Extensão de script personalizada no Windows"
   description="Automatizar tarefas de configuração na máquina virtual do Azure usando a extensão de script personalizado no Windows"
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="madhana"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/19/2015"
   ms.author="kundanap"/>

# Extensão de script personalizado para o Windows

Este artigo fornece uma visão geral do uso de extensão do script personalizado no Windows usando cmdlets do Powershell do Azure.


Extensões de máquina virtual (VM) criadas pela Microsoft e editores de terceiros confiável para estender a funcionalidade da VM. Para obter uma visão geral detalhada das extensões de VM, confira a <a href="https://msdn.microsoft.com/library/azure/dn606311.aspx" target="_blank">Documentação do MSDN</a>.

## Visão geral de extensão do script personalizado

A extensão do script personalizado do Windows permite que você execute scripts do Powershell em uma máquina virtual remota, sem precisar fazer logon nele. Os scripts podem ser executados após o provisionamento da VM ou a qualquer momento durante o ciclo de vida da máquina virtual sem a necessidade de abrir portas adicionais na VM. O caso de uso mais comum de script personalizado inclui a execução da instalação e configuração de software adicional no provisionamento da postagem da VM.

### Pré-Requistes para a execução da extensão do script personalizado

1. Instale cmdlets V0.8.0 do PowerShell do Azure ou superior <a href="http://azure.microsoft.com/downloads" target="_blank">daqui</a>.
2. Se os scripts serão executados em uma VM existente, verifique se o agente de VM está habilitado na VM. Caso contrário, siga este <a href="https://msdn.microsoft.com/library/azure/dn832621.aspx" target="_blank">artigo</a> para instalar uma.
3. Carregue os scripts que você deseja executar na máquina virtual para o armazenamento do Azure. Os scripts podem vir de um único ou vários contêineres de armazenamento.
4. O script deve ser criado de forma que o script de entrada que por sua vez é iniciado pela extensão inicia outros scripts.

## Cenários de extensão do script personalizado:

 ### Carregue arquivos no contêiner padrão: se você tiver scripts no contêiner de armazenamento da conta padrão de sua assinatura, o trecho de cmdlet abaixo mostra como você pode executá-los na VM. O ContainerName no exemplo abaixo é onde você carrega os scripts. A conta de armazenamento padrão pode ser verificada usando o cmdlet ‘Get-AzureSubscription –Default’

Observação: esse caso de uso cria uma nova VM, mas as mesmas operações podem ser feitas em uma VM existente também.

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

### Carregar arquivos para um contêineres de armazenamento não padrão.

Este caso de uso mostra como usar um armazenamento fora do padrão dentro da mesma assinatura ou em uma assinatura diferente para carregar arquivos/scripts. Aqui, usaremos uma máquina virtual existente, mas as mesmas operações podem ser feitas durante a criação de uma nova VM.

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM
  ### Carregue scripts para vários contêineres em diferentes contas de armazenamento. Se os arquivos de script são armazenados em vários contêineres, para executar esses scripts no momento, você precisa fornecer a URL de SAS completa desses arquivos.

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### Adicione extensão de script personalizado pelo Portal.
Navegue até a máquina virtual no <a href="https://portal.azure.com/ " target="_blank">Portal de Visualização do Azure</a> e adicione a extensão especificando o arquivo de script a ser executado. ![][5]

  ### Desinstalando a extensão de script personalizado.

A extensão de script personalizado pode ser desinstalada da VM usando o cmdlet abaixo

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### Em breve

Em breve incluiremos uso de script personalizado para Linux e exemplos, fique atento.

<!--Image references-->
[5]: ./media/virtual-machines-extensions-customscript/addcse.png
 

<!---HONumber=July15_HO4-->