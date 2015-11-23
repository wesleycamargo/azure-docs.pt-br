<properties
	pageTitle="Criar uma VM com um modelo | Microsoft Azure"
	description="Use um modelo do Gerenciador de Recursos e o PowerShell do Azure para criar uma nova máquina virtual do Windows."
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/08/2015"
	ms.author="davidmu"/>

# Criar uma máquina virtual do Windows com um modelo do Gerenciador de Recursos e o PowerShell

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.


Você pode criar facilmente uma nova VM (máquina virtual) do Azure baseada no Windows usando um modelo do Gerenciador de Recursos com o PowerShell do Azure. Esse modelo cria uma única máquina virtual que executa o Windows em uma nova rede virtual com uma única sub-rede em um novo grupo de recursos.

![](./media/virtual-machines-create-windows-powershell-resource-manager-template-simple/windowsvm.png)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## Criar uma VM do Windows

Siga estas etapas para criar uma VM do Windows usando um modelo do Gerenciador de Recursos no repositório de modelos do Github com o PowerShell do Azure.

Preencha um nome de implantação do Azure, o nome do grupo de recursos e o local do data center do Azure e, em seguida, execute estes comandos.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	New-AzureRmResourceGroup –Name $RGName –Location $locName
	New-AzureRmResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Ao executar o comando **New-AzureRmResourceGroupDeployment**, você será solicitado a fornecer os valores dos parâmetros na seção “parâmetros” do arquivo JSON. Quando você tiver especificado todos os valores de parâmetros, o comando criará o grupo de recursos e a máquina virtual.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	New-AzureRmResourceGroup –Name $RGName –Location $locName
	New-AzureRmResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Você verá algo semelhante a:

	cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: newsaacct
	adminUsername: WinAdmin1
	adminPassword: *********
	dnsNameForPublicIP: contoso
	VERBOSE: 10:56:59 AM - Template is valid.
	VERBOSE: 10:56:59 AM - Create template deployment 'TestDeployment'.
	VERBOSE: 10:57:08 AM - Resource Microsoft.Network/virtualNetworks 'MyVNET' provisioning status is succeeded
	VERBOSE: 10:57:11 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is running
	VERBOSE: 10:57:11 AM - Resource Microsoft.Storage/storageAccounts 'newsaacct' provisioning status is running
	VERBOSE: 10:57:38 AM - Resource Microsoft.Storage/storageAccounts 'newsaacct' provisioning status is succeeded
	VERBOSE: 10:57:40 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is succeeded
	VERBOSE: 10:57:45 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is running
	VERBOSE: 10:57:45 AM - Resource Microsoft.Network/networkInterfaces 'myVMNic' provisioning status is succeeded
	VERBOSE: 11:01:59 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is succeeded


	DeploymentName    : TestDeployment
	ResourceGroupName : TestRG
	ProvisioningState : Succeeded
	Timestamp         : 4/28/2015 6:02:13 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
                    	Name             Type                       Value
	                    ===============  =========================  ==========
	                    newStorageAccountName  String                     newsaacct
	                    adminUsername    String                     WinAdmin1
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contoso
	                    windowsOSVersion  String                     2012-R2-Datacenter

	Outputs           :

Agora você tem uma nova máquina virtual do Windows chamada MyWindowsVM em seu novo grupo de recursos.

## Recursos adicionais

[Computação do Azure, Provedores de Rede e Armazenamento no Gerenciador de Recursos do Azure](virtual-machines-azurerm-versus-azuresm.md)

[Visão Geral do Gerenciador de Recursos do Azure](resource-group-overview.md)

[Criar uma máquina virtual do Windows com o Gerenciador de Recursos e o PowerShell do Azure](virtual-machines-create-windows-powershell-resource-manager.md)

[Criar máquinas virtuais do Windows com o PowerShell e o modelo de implantação clássico](virtual-machines-ps-create-preconfigure-windows-vms.md)

[Documentação de máquinas virtuais](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Como instalar e configurar o PowerShell do Azure](install-configure-powershell.md)

<!---HONumber=Nov15_HO3-->