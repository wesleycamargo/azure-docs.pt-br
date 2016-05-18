<properties
	pageTitle="Gerenciar VMs em um Conjunto de Escala de Máquina Virtual | Microsoft Azure"
	description="Gerenciar máquinas virtuais em um conjunto de escala de máquina virtual usando o Azure PowerShell."
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="davidmu"/>

# Gerenciar máquinas virtuais em um conjunto de escala de máquina virtual

Use as tarefas neste artigo para gerenciar os recursos de máquina virtual no seu conjunto de escala de máquina virtual.

Todas as tarefas que envolvem o gerenciamento de uma máquina virtual em um conjunto de escala exigem que você conheça a ID da instância da máquina que deseja gerenciar. Você pode usar o [Gerenciador de Recursos do Azure](https://resources.azure.com) para localizar a ID da instância de uma máquina virtual em um conjunto de escala. Você também pode usar o Gerenciador de Recursos para verificar o status das tarefas que você concluir.

Consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para saber mais sobre como instalar a versão mais recente do Azure PowerShell, selecionar a assinatura que você deseja usar e entrar na sua conta do Azure.

## Exibir informações sobre um conjunto de escala de máquina virtual

Você pode obter informações gerais sobre um conjunto de escala, que também é conhecido como a exibição de instância. Ou então, você pode obter informações mais específicas, tais como informações sobre os recursos no conjunto.

Neste comando, substitua o *nome do grupo de recursos* pelo nome do grupo de recursos que contém o conjunto de escala de máquina virtual, o *nome do conjunto de escala* pelo nome do conjunto de escala da máquina virtual e execute-o:

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

Ele retorna algo semelhante a:

    Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
    UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
    VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
    ProvisioningState     : Succeeded
    OverProvision         :
    Id                    : /subscriptions/{subscription-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                  : myvmss1
    Type                  : Microsoft.Compute/virtualMachineScaleSets
    Location              : centralus
    Tags                  :

Neste comando, substitua o *nome do grupo de recursos* pelo nome do grupo de recursos que contém o conjunto de escala de máquina virtual, o *nome do conjunto de escala* pelo nome do conjunto de escala da máquina virtual e *#* pelo identificador da instância da máquina virtual sobre a qual deseja obter informações e execute-o:

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
        
Ele retorna algo semelhante a:

    InstanceId         : 1
    Sku                : Microsoft.Azure.Management.Compute.Models.Sku
    LatestModelApplied : True
    InstanceView       :
    HardwareProfile    :
    StorageProfile     : Microsoft.Azure.Management.Compute.Models.StorageProfile
    OsProfile          : Microsoft.Azure.Management.Compute.Models.OSProfile
    NetworkProfile     : Microsoft.Azure.Management.Compute.Models.NetworkProfile
    DiagnosticsProfile :
    AvailabilitySet    :
    ProvisioningState  : Succeeded
    LicenseType        :
    Plan               :
    Resources          :
    Id                 : /subscriptions/{subscription-id}/resourceGroups/myrg1/providers/Microsoft.
                         Compute/virtualMachineScaleSets/myvmss1/virtualMachines/1
    Name               : myvmss1_1
    Type               : Microsoft.Compute/virtualMachineScaleSets/virtualMachines
    Location           : centralus
    Tags               :
        
## Iniciar uma máquina virtual em um conjunto de escala

Neste comando, substitua o *nome do grupo de recursos* pelo nome do grupo de recursos que contém o conjunto de escala de máquina virtual, o *nome do conjunto de escala* pelo nome do conjunto de escala, *#* pelo identificador da máquina virtual que você deseja iniciar e execute-o:

    Start-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

No Gerenciador de Recursos, podemos ver que o status da instância é **em execução**:

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T02:10:08.0730839+00:00"
      },
      {
        "code": "PowerState/running",
        "level": "Info",
        "displayStatus": "VM running"
      }
    ]

Você poderá iniciar todas as máquinas virtuais do conjunto se não usar o parâmetro - InstanceId.
    
## Parar uma máquina virtual em um conjunto de escala

Neste comando, substitua o *nome do grupo de recursos* pelo nome do grupo de recursos que contém o conjunto de escala de máquina virtual, o *nome do conjunto de escala* pelo nome do conjunto de escala, *#* pelo identificador da máquina virtual que você deseja parar e execute-o:

	Stop-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

No Gerenciador de Recursos, podemos ver que o status da instância é **desalocada**:

	"statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T01:25:17.8792929+00:00"
      },
      {
        "code": "PowerState/deallocated",
        "level": "Info",
        "displayStatus": "VM deallocated"
      }
    ]
    
Para parar uma máquina virtual sem desalocá-la, use o parâmetro - StayProvisioned. Você poderá parar todas as máquinas virtuais do conjunto se não usar o parâmetro - InstanceId.
    
## Reiniciar uma máquina virtual em um conjunto de escala

Neste comando, substitua o *nome do grupo de recursos* pelo nome do grupo de recursos que contém o conjunto de escala de máquina virtual, o *nome do conjunto de escala* pelo nome do conjunto de escala, *#* pelo identificador da máquina virtual que você deseja reiniciar e execute-o:

	Restart-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
    
Você poderá reiniciar todas as máquinas virtuais do conjunto se não usar o parâmetro - InstanceId.

## Remover uma máquina virtual de um conjunto de escala

Neste comando, substitua o *nome do grupo de recursos* pelo nome do grupo de recursos que contém o conjunto de escala de máquina virtual, o *nome do conjunto de escala* pelo nome do conjunto de escala, *#* pelo identificador da máquina virtual que você deseja remover do conjunto de escala e execute-o:

	Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId #

Você poderá remover o conjunto de escala da máquina virtual se não usar o parâmetro - InstanceId.

<!---HONumber=AcomDC_0504_2016-->