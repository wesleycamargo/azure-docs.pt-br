<properties
	pageTitle="Gerenciar as VMs do Gerenciador de Recursos do Azure | Microsoft Azure"
	description="Gerenciar máquinas virtuais usando o PowerShell, modelos e o Gerenciador de Recursos do Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/10/2015"
	ms.author="davidmu"/>

# Gerenciar máquinas virtuais usando o PowerShell e o Gerenciador de Recursos do Azure

> [AZURE.SELECTOR]
- [Portal](virtual-machines-windows-tutorial.md)
- [PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

Usar modelos do Azure PowerShell e do Gerenciador de Recursos fornece muita eficiência e flexibilidade ao gerenciar recursos no Microsoft Azure. Você pode usar as tarefas neste artigo para criar e gerenciar recursos de máquina virtual.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Este artigo aborda o gerenciamento de recursos com o modelo de implantação do Gerenciador de Recursos. Você também pode gerenciar recursos com o [modelo de implantação clássico](virtual-machines-windows-tutorial-classic-portal.md).

Essas tarefas usam um modelo do Gerenciador de Recursos e o PowerShell:

- [Criar uma máquina virtual](#windowsvm)
- [Criar uma máquina virtual com um disco especializado](#customvm)
- [Criar várias máquinas virtuais em uma rede virtual com um balanceador externo de carga](#multivm)

Essas tarefas usam somente o PowerShell:

- [Remover um grupo de recursos](#removerg)
- [Exibir informações sobre uma máquina virtual](#displayvm)
- [Iniciar uma máquina virtual](#start)
- [Parar uma máquina virtual](#stop)
- [Reiniciar uma máquina virtual](#restart)
- [Excluir uma máquina virtual](#delete)

Antes de começar, verifique se que você tem o PowerShell do Azure pronto para o uso.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]



## Modelos e grupos de recursos do Gerenciador de Recursos do Azure

Algumas das tarefas nesse artigo mostram a você como usar modelos do Gerenciador de Recursos do Azure e do PowerShell para implantar e gerenciar máquinas virtuais do Azure automaticamente.

A maioria dos aplicativos em execução no Microsoft Azure é criada por meio de uma combinação de diferentes tipos de recursos de nuvem, como uma ou mais máquinas virtuais e contas de armazenamento, um banco de dados SQL ou uma rede virtual. Com os modelos do Gerenciador de Recursos do Azure, é possível gerenciar esses recursos diferentes em conjunto usando uma descrição JSON dos recursos e parâmetros associados de configuração e implantação.

Depois de definir um modelo de recurso com base em JSON, você pode usá-lo com um comando do PowerShell para implantar os recursos definidos no Azure. Você pode executar esses comandos separadamente no shell de comando do PowerShell ou integrá-los a um script que contenha lógica de automação adicional.

Os recursos que você criar usando modelos do Gerenciador de Recursos do Azure são implantados em um *grupo de recursos do Azure* novo ou já existente. Um grupo de recursos permite que você gerencie vários recursos implantados juntos como um grupo lógico. Isso significa que você pode gerenciar o ciclo de vida total do grupo/aplicativo.

Se você está interessado na criação de modelos, consulte [Criação de Modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md).

### Criar um grupos de recursos

Para tarefas que criam um recurso, será necessário um grupo de recursos se você ainda não tiver um.

No comando a seguir, substitua *nome do grupo de recursos* elo nome do novo grupo de recursos e *local do Azure* pelo local do datacenter do Azure onde você deseja que o recurso esteja localizado; em seguida, execute-o:

	New-AzureResourceGroup -Name "resource group name" -Location "Azure location"

## <a id="windowsvm"></a>TAREFA: criar uma máquina virtual

Essa tarefa usa um modelo da galeria de modelos. Para saber mais sobre o modelo, consulte [implantar uma VM simples do Windows no Oeste dos EUA](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/).

![](./media/virtual-machines-deploy-rmtemplates-powershell/windowsvm.png)

No comando a seguir, substitua *nome da implantação* pelo nome que você deseja usar para a implantação e *nome do grupo de recursos* pelo nome do recurso existente de grupo; em seguida, execute-o:

	New-AzureResourceGroupDeployment -Name "deployment name" -ResourceGroupName "resource group name" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"

Aqui está um exemplo:

	New-AzureResourceGroupDeployment -Name "TestDeployment" -ResourceGroupName "TestRG" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"

Você será solicitado a fornecer os valores dos parâmetros na seção **parâmetros** do arquivo JSON:

	cmdlet New-AzureResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saacct
	adminUsername: WinAdmin1
	adminPassword: *********
	dnsNameForPublicIP: contoso

Ele retorna algo semelhante a:

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
	                    newStorageAccountName  String                     saacct
	                    adminUsername    String                     WinAdmin1
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contoso9875
	                    windowsOSVersion  String                     2012-R2-Datacenter

	Outputs           :

Se você gostaria de ver um vídeo desta tarefa sendo realizada, dê uma olhada nisso:

[AZURE.VIDEO deploy-a-windows-virtual-machine-with-azure-resource-manager-templates-and-powershell]

## TAREFA<a id="customvm"></a>: criar uma máquina virtual com um disco especializado

Essa tarefa usa um modelo da galeria de modelos. Para saber mais sobre o modelo, consulte [Criar uma máquina virtual de um disco VHD especializado](https://azure.microsoft.com/documentation/templates/201-vm-from-specialized-vhd/).

No comando a seguir, substitua *nome da implantação* pelo nome que você deseja usar para a implantação e *nome do grupo de recursos* pelo nome do recurso existente de grupo; em seguida, execute-o:

	New-AzureResourceGroupDeployment -Name "deployment name" -ResourceGroupName "resource group name" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json"

Aqui está um exemplo:

	New-AzureResourceGroupDeployment -Name "TestDeployment" -ResourceGroupName "TestRG" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json"

Você será solicitado a fornecer os valores dos parâmetros na seção **parâmetros** do arquivo JSON:

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	osDiskVhdUri: http://saacct.blob.core.windows.net/vhds/osdiskforwindows.vhd
	osType: windows
	location: West US
	vmSize: Standard_A3
	...

> [AZURE.NOTE]O exemplo acima usa um arquivo vhd existente na conta de armazenamento saacct. O nome do disco foi fornecido como um parâmetro para o modelo.

Se você gostaria de ver um vídeo desta tarefa sendo realizada, dê uma olhada nisso:

[AZURE.VIDEO create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell]

## <a id="multivm"></a>TAREFA: criar várias máquinas virtuais em uma rede virtual com um balanceador externo de carga

Essa tarefa usa um modelo da galeria de modelos. Para saber mais sobre o modelo, consulte [Criar uma máquina virtual de um disco VHD especializado](https://azure.microsoft.com/documentation/templates/201-2-vms-loadbalancer-lbrules/).

![](./media/virtual-machines-deploy-rmtemplates-powershell/multivmextlb.png)

No comando a seguir, substitua *nome da implantação* pelo nome que você deseja usar para a implantação e *nome do grupo de recursos* pelo nome do recurso existente de grupo; em seguida, execute-o:

	New-AzureResourceGroupDeployment -Name "deployment name" -ResourceGroupName "resource group name" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json"

Você será solicitado a fornecer os valores dos parâmetros na seção **parâmetros** do arquivo JSON:

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	adminUserName: WebAdmin1
	adminPassword: *******
	dnsNameforLBIP: web07
	backendPort: 80
	vmNamePrefix: WEBFARM
	...

Se você gostaria de ver um vídeo desta tarefa sendo realizada, dê uma olhada nisso:

[AZURE.VIDEO deploy-multi-vm-app-with-a-virtual-network-and-load-balancer-in-azure-resource-manager]

## <a id="removerg"></a>TAREFA: Remover um grupo de recursos

NO comando a seguir, substitua *nome do grupo de recursos* pelo nome do grupo de recursos que você deseja remover e, em seguida, execute-o:

	Remove-AzureResourceGroup  -Name "resource group name"

> [AZURE.NOTE]Use o parâmetro **-Force** para ignorar o prompt de confirmação.

É solicitado que você confirme se você usou ou não o parâmetro -Force:

	Confirm
	Are you sure you want to remove resource group 'BuildRG'
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Se você gostaria de ver um vídeo desta tarefa sendo realizada, dê uma olhada nisso:

[AZURE.VIDEO removing-a-resource-group-in-azure]

## <a id="displayvm"></a>TAREFA: Exibir informações sobre uma máquina virtual

No comando a seguir, substitua *nome do grupo de recursos* pelo nome do grupo de recursos que contém a máquina virtual e *nome da VM* pelo nome da máquina; em seguida, execute-o:

	Get-AzureVM -ResourceGroupName "resource group name" -Name "VM name"

Ele retorna algo semelhante a:

	AvailabilitySetReference : null
	Extensions               : []
	HardwareProfile          : {
	                             "VirtualMachineSize": "Standard_D1"
	                           }
	Id                       : /subscriptions/{subscription-id}/resourceGroups/BuildRG/providers/Microso
	                           ft.Compute/virtualMachines/MyWindowsVM
	InstanceView             : null
	Location                 : westus
	Name                     : MyWindowsVM
	NetworkProfile           : {
	                             "NetworkInterfaces": [
	                               {
	                                 "Primary": null,
	                                 "ReferenceUri": "/subscriptions/{subscription-id}/resourceGroups/Bu
	                           ildRG/providers/Microsoft.Network/networkInterfaces/myVMNic"
	                               }
	                             ]
	                           }
	OSProfile                : {
	                             "AdminPassword": null,
	                             "AdminUsername": "WinAdmin1",
	                             "ComputerName": "MyWindowsVM",
	                             "CustomData": null,
	                             "LinuxConfiguration": null,
	                             "Secrets": [],
	                             "WindowsConfiguration": {
	                               "AdditionalUnattendContents": [],
	                               "EnableAutomaticUpdates": true,
	                               "ProvisionVMAgent": true,
	                               "TimeZone": null,
	                               "WinRMConfiguration": null
	                             }
	                           }
	Plan                     : null
	ProvisioningState        : Succeeded
	StorageProfile           : {
	                             "DataDisks": [],
	                             "ImageReference": {
	                               "Offer": "WindowsServer",
	                               "Publisher": "MicrosoftWindowsServer",
	                               "Sku": "2012-R2-Datacenter",
	                               "Version": "latest"
	                             },
	                             "OSDisk": {
	                               "OperatingSystemType": "Windows",
	                               "Caching": "ReadWrite",
	                               "CreateOption": "FromImage",
	                               "Name": "osdisk",
	                               "SourceImage": null,
	                               "VirtualHardDisk": {
	                                 "Uri": "http://saacct.blob.core.windows.net/vhds/osdiskforwindowssimple.vhd"
	                               }
	                             },
	                             "SourceImage": null
	                           }
	Tags                     : {}
	Type                     : Microsoft.Compute/virtualMachines

Se você gostaria de ver um vídeo desta tarefa sendo realizada, dê uma olhada nisso:

[AZURE.VIDEO displaying-information-about-a-virtual-machine-in-microsoft-azure-with-powershell]

## <a id="start"></a>TAREFA: Iniciar uma máquina virtual

No comando a seguir, substitua *nome do grupo de recursos* pelo nome do grupo de recursos que contém a máquina virtual e *nome da VM* pelo nome da máquina; em seguida, execute-o:

	Start-AzureVM -ResourceGroupName "resource group name" -Name "VM name"

Ele retorna algo semelhante a:

	EndTime             : 4/28/2015 11:11:41 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:10:35 AM -07:00
	Status              : Succeeded
	TrackingOperationId : e1705973-d266-467e-8655-920016145347
	RequestId           : aac41de1-b85d-4429-9a3d-040b922d2e6d
	StatusCode          : OK

Se você gostaria de ver um vídeo desta tarefa sendo realizada, dê uma olhada nisso:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="stop"></a>TAREFA: Parar uma máquina virtual

No comando a seguir, substitua *nome do grupo de recursos* pelo nome do grupo de recursos que contém a máquina virtual e *nome da VM* pelo nome da máquina; em seguida, execute-o:

	Stop-AzureVM -ResourceGroupName "resource group name" -Name "VM name"

É solicitado que você faça a confirmação:

	Virtual machine stopping operation
	This cmdlet will stop the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Ele retorna algo semelhante a:

	EndTime             : 4/28/2015 11:09:08 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:06:55 AM -07:00
	Status              : Succeeded
	TrackingOperationId : 0c94dc74-c553-412c-a187-108bdb29657e
	RequestId           : 5cc9ddba-0643-4b5e-82b6-287b321394ee
	StatusCode          : OK

Se você gostaria de ver um vídeo desta tarefa sendo realizada, dê uma olhada nisso:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="restart"></a>TAREFA: Reiniciar uma máquina virtual

No comando a seguir, substitua *nome do grupo de recursos* pelo nome do grupo de recursos que contém a máquina virtual e *nome da VM* pelo nome da máquina; em seguida, execute-o:

	Restart-AzureVM -ResourceGroupName "resource group name" -Name "VM name"

Ele retorna algo semelhante a:

	EndTime             : 4/28/2015 11:16:26 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:16:25 AM -07:00
	Status              : Succeeded
	TrackingOperationId : 390571e0-c804-43ce-88c5-f98e0feb588e
	RequestId           : 7dac33e3-0164-4a08-be33-96205284cb0b
	StatusCode          : OK

Se você gostaria de ver um vídeo desta tarefa sendo realizada, dê uma olhada nisso:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="delete"></a>TAREFA: Excluir uma máquina virtual

No comando a seguir, substitua *nome do grupo de recursos* pelo nome do grupo de recursos que contém a máquina virtual e *nome da VM* pelo nome da máquina; em seguida, execute-o:

	Remove-AzureVM -ResourceGroupName "resource group name" –Name "VM name"

> [AZURE.NOTE]Use o parâmetro **-Force** para ignorar o prompt de confirmação.

É solicitado que você confirme se você usou ou não o parâmetro -Force:

	Virtual machine removal operation
	This cmdlet will remove the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Ele retorna algo semelhante a:

	EndTime             : 4/28/2015 11:21:55 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:20:13 AM -07:00
	Status              : Succeeded
	TrackingOperationId : f74fad9e-f6bc-46ae-82b1-bfad3952aa44
	RequestId           : 6a30d2e0-63ca-43cf-975b-058631e048e7
	StatusCode          : OK

Se você gostaria de ver um vídeo desta tarefa sendo realizada, dê uma olhada nisso:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## Recursos adicionais
[Modelos de Início Rápido do Azure](http://azure.microsoft.com/documentation/templates/) e [Estruturas do Aplicativo](virtual-machines-app-frameworks.md)

[Computação do Azure, provedores de rede e armazenamento no Gerenciador de Recursos do Azure](virtual-machines-azurerm-versus-azuresm.md)

[Visão geral do Gerenciador de Recursos do Azure](resource-group-overview.md)

[Documentação de máquinas virtuais](http://azure.microsoft.com/documentation/services/virtual-machines/)

<!---HONumber=Oct15_HO2-->