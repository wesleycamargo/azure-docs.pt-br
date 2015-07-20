<properties 
	pageTitle="Criar e gerenciar uma máquina virtual do Windows com o PowerShell e o Gerenciador de Serviços do Azure" 
	description="Use o PowerShell do Azure para criar rapidamente uma nova máquina virtual do Microsoft Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/09/2015" 
	ms.author="josephd"/>

# Criar e gerenciar uma máquina virtual do Windows com o PowerShell e o Gerenciador de Serviços do Azure

Este artigo descreve como criar e gerenciar uma máquina virtual Azure baseada no Windows usando o Gerenciamento de Serviço do Azure e o PowerShell.

[AZURE.INCLUDE [service-management-pointer-to-resource-manager](../../includes/service-management-pointer-to-resource-manager.md)]

- [Implantar e gerenciar máquinas virtuais usando modelos de Gerenciador de Recursos e o PowerShell do Azure](virtual-machines-deploy-rmtemplates-powershell.md)

## Configurar o PowerShell do Azure

Se já instalou o PowerShell do Azure, você deve ter o PowerShell do Azure versão 0.8.0 ou posterior. Você pode verificar a versão do PowerShell do Azure instalada com este comando no prompt de comando do PowerShell do Azure.

	Get-Module azure | format-table version

Se você ainda não fez isso, use as instruções em [Como instalar e configurar o PowerShell do Azure](../install-configure-powershell.md) para instalar o PowerShell do Azure no computador local. Em seguida, abra um prompt de comando do PowerShell do Azure.

Primeiro, você deve fazer logon no Azure com este comando.

	Add-AzureAccount

Especifique o endereço de email de sua conta do Azure e sua senha na caixa de diálogo de entrada do Microsoft Azure.

A seguir, se tiver várias assinaturas do Azure, você precisará definir sua assinatura do Azure. Para ver uma lista de suas assinaturas atuais, execute este comando.

	Get-AzureSubscription | sort SubscriptionName | Select SubscriptionName

Agora, substitua tudo entre aspas, inclusive os caracteres < and >, pelo nome da assinatura correta e execute estes comandos.

	$subscrName="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscrName –Current

## Criar uma máquina virtual

Primeiro, você precisa de uma conta de armazenamento. Você pode exibir sua lista atual de contas de armazenamento com este comando.

	Get-AzureStorageAccount | sort Label | Select Label

Se ainda não tiver uma, crie uma nova conta de armazenamento. Você deve escolher um nome exclusivo que contenha apenas letras minúsculas e números. Você pode testar a exclusividade do nome da conta de armazenamento com este comando.

	Test-AzureName -Storage <Proposed storage account name>

Se este comando retornar "False", o nome proposto é exclusivo.

Você precisará especificar o local de um data center do Azure ao criar uma conta de armazenamento. Para obter uma lista de data centers do Azure, execute este comando.

	Get-AzureLocation | sort Name | Select Name

Agora, crie e configure a conta de armazenamento com estes comandos. Preencha os nomes da conta de armazenamento e substitua tudo entre aspas, inclusive os caracteres < and >.

	$stAccount="<chosen storage account name>"
	$locName="<Azure location>"
	New-AzureStorageAccount -StorageAccountName $stAccount -Location $locName
	Set-AzureStorageAccount -StorageAccountName $stAccount
	Set-AzureSubscription -SubscriptionName $subscrName -CurrentStorageAccountName $stAccount

Em seguida, você precisa de um serviço de nuvem. Se não tiver um serviço de nuvem existente, você deverá criar um. Você deve escolher um nome exclusivo que contenha apenas letras, números e hifens. O primeiro e o último caractere no campo devem ser uma letra ou um número.

Por exemplo, você poderia nomeá-lo como -TestCS-*UniqueSequence*, em que *UniqueSequence* é uma abreviação de sua organização. Por exemplo, se sua organização se chamasse Tailspin Toys, você poderia chamar o serviço de nuvem de TestCS-Tailspin.

Você pode testar a exclusividade do nome com o comando a seguir do PowerShell do Azure.

	Test-AzureName -Service <Proposed cloud service name>

Se este comando retornar "False", o nome proposto é exclusivo. Crie o serviço de nuvem com estes comandos.

	$csName="<cloud service name>"
	$locName="<Azure location>"
	New-AzureService -Service $csName -Location $locName

Depois, copie o conjunto de comandos do PowerShell a seguir para um editor de texto, como o Bloco de Notas.

	$vmName="<machine name>"
	$csName="<cloud service name>"
	$locName="<Azure location>"
	$vm=New-AzureVMConfig -Name $vmName -InstanceSize Medium -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	New-AzureVM –ServiceName $csName –Location $locName -VMs $vm

No editor de texto, preencha o nome da máquina virtual, o nome do serviço de nuvem e o local.

Por fim, copie o conjunto de comandos para a área de transferência e clique com o botão direito do mouse no prompt de comando aberto do PowerShell do Azure. Isso emitirá o conjunto de comandos como uma série de comandos do PowerShell, solicitará o nome e a senha da conta de administrador local e criará sua máquina virtual do Azure. Aqui está um exemplo de como executar o conjunto de comandos.

	PS C:> $vmName="PSTest"
	PS C:> $csName=" TestCS-Tailspin"
	PS C:> $locName="West US"
	PS C:> $vm=New-AzureVMConfig -Name $vmName -InstanceSize Medium -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd"
	PS C:> $cred=Get-Credential -Message "Type the name and password of the local administrator account."
	PS C:> $vm | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.
	GetNetworkCredential().Password

	
	AvailabilitySetName               :
	ConfigurationSets                 : PSTest,Microsoft.WindowsAzure.Commands.ServiceManagement.Model.NetworkConfigurationSet}
	DataVirtualHardDisks              : {}
	Label                             : PSTest
	OSVirtualHardDisk                 : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.OSVirtualHardDisk
	RoleName                          : PSTest
	RoleSize                          : Medium
	RoleType                          : PersistentVMRole
	WinRMCertificate                  :
	X509Certificates                  : {}
	NoExportPrivateKey                : False
	NoRDPEndpoint                     : False
	NoSSHEndpoint                     : False
	DefaultWinRmCertificateThumbprint :
	ProvisionGuestAgent               : True
	ResourceExtensionReferences       : {BGInfo}
	DataVirtualHardDisksToBeDeleted   :
	VMImageInput                      :
	
	PS C:> New-AzureVM -ServiceName $csName -Location $locName -VMs $vm
	VERBOSE: 3:01:46 PM - Begin Operation: New-AzureVM - Create Deployment with VM PSTest
	VERBOSE: 3:02:49 PM - Completed Operation: New-AzureVM - Create Deployment with VM PSTest
	
	OperationDescription                    OperationId                            OperationStatus
	--------------------                    -----------                            --------------
	New-AzureVM                             8072cbd1-4abe-9278-9de2-8826b56e9221   Succeeded
	
## Exibir informações sobre uma máquina virtual
Essa é uma tarefa básica que você usará com frequência. Use-a para obter informações sobre uma VM, executar tarefas em uma VM ou obter a saída para armazenar em uma variável.

Para obter informações sobre a VM, execute este comando, substituindo tudo entre aspas, inclusive os caracteres < and >:

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Para armazenar a saída em uma variável $vm, execute:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Faça logon em uma máquina virtual baseada no Windows

Execute estes comandos:

	$svcName="<cloud service name>"
	$vmName="<virtual machine name>"
	$localPath="<drive and folder location to store the downloaded RDP file, example: c:\temp >"
	$localFile=$localPath + "" + $vmname + ".rdp"
	Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch

>[AZURE.NOTE]Você pode obter o nome do serviço de nuvem e de máquina virtual na exibição do comando **Get-AzureVM**.

## Parar uma VM

Execute este comando:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

>[AZURE.IMPORTANT]Use o parâmetro **StayProvisioned** para manter o VIP (IP virtual) do serviço de nuvem, caso essa seja a última VM no serviço de nuvem. Se usar esse parâmetro, você ainda será cobrado pela VM.

## Iniciar uma VM

Execute este comando:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Anexar um Disco de Dados
Essa tarefa requer algumas etapas. Primeiro, use o cmdlet **Add-AzureDataDisk** para adicionar o disco ao objeto $vm. Em seguida, use o cmdlet Update-AzureVM para atualizar a configuração da VM.

Você também precisará decidir se deseja anexar um novo disco ou um que contenha dados. Para um novo disco, o comando cria o arquivo .vhd e anexa-o no mesmo comando.

Para anexar um novo disco, execute este comando:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM <$vm> | Update-AzureVM

Para anexar discos de dados existentes, execute este comando:

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

Para anexar discos de dados de um arquivo .vhd existente no armazenamento de blob, execute este comando:

    Add-AzureDataDisk -ImportFrom -MediaLocation  "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" -DiskLabel "<main>" -LUN <0> | Update-AzureVM

## Recursos adicionais

[Criar uma máquina virtual do Windows com o Gerenciador de Recursos e o PowerShell do Azure](virtual-machines-create-windows-powershell-resource-manager.md)

[Criar uma máquina virtual do Windows com um modelo do Gerenciador de Recursos e o PowerShell](virtual-machines-create-windows-powershell-resource-manager-template-simple.md)

[Documentação de máquinas virtuais](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Como instalar e configurar o PowerShell do Azure](../install-configure-powershell.md)

[Usar o PowerShell do Azure para criar e pré-configurar máquinas virtuais baseadas em Windows](virtual-machines-ps-create-preconfigure-windows-vms.md)

 

<!---HONumber=July15_HO2-->