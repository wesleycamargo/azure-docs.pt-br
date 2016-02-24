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
	ms.date="01/07/2016"
	ms.author="davidmu"/>

# Gerenciar máquinas virtuais usando o PowerShell e o Gerenciador de Recursos do Azure

> [AZURE.SELECTOR]
- [PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)
- [CLI](virtual-machines-deploy-rmtemplates-azure-cli.md)

<br>


Usar modelos do Azure PowerShell e do Gerenciador de Recursos fornece muita eficiência e flexibilidade ao gerenciar recursos no Microsoft Azure. Você pode usar as tarefas neste artigo para gerenciar recursos de máquina virtual.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.

Essas tarefas usam somente o PowerShell:

- [Exibir informações sobre uma máquina virtual](#displayvm)
- [Iniciar uma máquina virtual](#start)
- [Parar uma máquina virtual](#stop)
- [Reiniciar uma máquina virtual](#restart)
- [Excluir uma máquina virtual](#delete)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## <a id="displayvm"></a>Exibir informações sobre uma máquina virtual

No comando a seguir, substitua *nome do grupo de recursos* pelo nome do grupo de recursos que contém a máquina virtual e *nome da VM* pelo nome da máquina e execute-o:

	Get-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Ele retorna algo semelhante a:


	ResourceGroupName        : rg1
	Id                       : /subscriptions/{subscription-id}/resourceGroups/
															rg1/providers/Microsoft.Compute/virtualMachines/vm1
	Name                     : vm1
	Type                     : Microsoft.Azure.Management.Compute.Models.VirtualMachineGetResponse
	Location                 : westus
	Tags                     : {}
	AvailabilitySetReference : null
	Extensions               : []
	HardwareProfile          :  {
																"VirtualMachineSize": "Standard_D1"
															}
	InstanceView             : null
	Location                 : westus
	Name                     : vm1
	NetworkProfile           :  {
																"NetworkInterfaces": [
																	{
																		"Primary": null,
																		"ReferenceUri": "/subscriptions/{subscription-id}/resourceGroups/
																		rg1/providers/Microsoft.Network/networkInterfaces/nc1"
																	}
																]
															}
	OSProfile                :  {
																"AdminPassword": null,
																"AdminUsername": "WinAdmin1",
																"ComputerName": "vm1",
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
	StorageProfile           : 	{
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
																		"Uri": "http://sa1.blob.core.windows.net/vhds/osdisk1.vhd"
																	}
																}
															}
	DataDiskNames            :  {}
	NetworkInterfaceIDs      : { /subscriptions/{subscription-id}/resourceGroups/
																rg1/providers/Microsoft.Network/networkInterfaces/nc1}

Se você gostaria de ver um vídeo desta tarefa sendo realizada, dê uma olhada nisso:

[AZURE.VIDEO displaying-information-about-a-virtual-machine-in-microsoft-azure-with-powershell]

## <a id="start"></a>Iniciar uma máquina virtual

No comando a seguir, substitua *nome do grupo de recursos* pelo nome do grupo de recursos que contém a máquina virtual e *nome da VM* pelo nome da máquina e execute-o:

	Start-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Ele retorna algo semelhante a:

	Status              : Succeeded
	StatusCode          : OK
	RequestId           : 06935ddf-6e89-48d2-b46a-229493e3e9d1
	Output              :
	Error               :
	StartTime           : 4/28/2015 11:10:35 AM -07:00
	EndTime             : 4/28/2015 11:11:41 AM -07:00
	TrackingOperationId : c1aa0a70-4f4f-4d6c-a8ac-7ea35c004ce0

Se você gostaria de ver um vídeo desta tarefa sendo realizada, dê uma olhada nisso:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="stop"></a>Parar uma máquina virtual

No comando a seguir, substitua *nome do grupo de recursos* pelo nome do grupo de recursos que contém a máquina virtual e *nome da VM* pelo nome da máquina e execute-o:

	Stop-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

É solicitado que você faça a confirmação:

	Virtual machine stopping operation
	This cmdlet will stop the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Ele retorna algo semelhante a:

	Status              : Succeeded
	StatusCode          : OK
	RequestId           : aac41de1-b85d-4429-9a3d-040b922d2e6d
	Output              :
	Error               :
	StartTime           : 4/28/2015 11:10:35 AM -07:00
	EndTime             : 4/28/2015 11:11:41 AM -07:00
	TrackingOperationId : e1705973-d266-467e-8655-920016145347

Se você gostaria de ver um vídeo desta tarefa sendo realizada, dê uma olhada nisso:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="restart"></a>Reiniciar uma máquina virtual

No comando a seguir, substitua *nome do grupo de recursos* pelo nome do grupo de recursos que contém a máquina virtual e *nome da VM* pelo nome da máquina e execute-o:

	Restart-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Ele retorna algo semelhante a:

	Status              : Succeeded
	StatusCode          : OK
	RequestId           : 4b05891c-fdff-4c9a-89ca-e4f1d7691aed
	Output              :
	Error               :
	StartTime           : 1/5/2016 12:06:53 PM -08:00
	EndTime             : 1/5/2016 12:06:54 PM -08:00
	TrackingOperationId : 5aeeab89-45ab-41b9-84ef-9e9a7e732207


Se você gostaria de ver um vídeo desta tarefa sendo realizada, dê uma olhada nisso:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="delete"></a>Excluir uma máquina virtual

No comando a seguir, substitua *nome do grupo de recursos* pelo nome do grupo de recursos que contém a máquina virtual e *nome da VM* pelo nome da máquina e execute-o:

	Remove-AzureRmVM -ResourceGroupName "resource group name" –Name "VM name"

> [AZURE.NOTE] Use o parâmetro **-Force** para ignorar o prompt de confirmação.

É solicitado que você confirme se você usou ou não o parâmetro -Force:

	Virtual machine removal operation
	This cmdlet will remove the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Ele retorna algo semelhante a:

	Status              : Succeeded
	StatusCode          : OK
	RequestId           : 2d723b40-ce1f-4b11-a603-dc659a13b6f0
	Output              :
	Error               :
	StartTime           : 1/5/2016 12:10:28 PM -08:00
	EndTime             : 1/5/2016 12:12:12 PM -08:00
	TrackingOperationId : d138ab29-83bf-4948-9d13-dab87db1a639

Se você gostaria de ver um vídeo desta tarefa sendo realizada, dê uma olhada nisso:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

<!---HONumber=AcomDC_0128_2016-->