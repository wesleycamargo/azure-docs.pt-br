---
title: "Gerenciar VMs em um Conjunto de dimensionamento de máquinas virtuais | Microsoft Docs"
description: "Gerenciar máquinas virtuais em um conjunto de escala de máquina virtual usando o Azure PowerShell."
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 88541f6054df1476866d68fdc1c9690a73ada83c
ms.lasthandoff: 04/27/2017


---
# <a name="manage-virtual-machines-in-a-virtual-machine-scale-set"></a>Gerenciar máquinas virtuais em um conjunto de dimensionamento de máquinas virtuais
Use as tarefas neste artigo para gerenciar as máquinas virtuais no conjunto de dimensionamento de máquinas virtuais.

A maior parte das tarefas que envolvem o gerenciamento de uma máquina virtual em um conjunto de dimensionamento exigem que você conheça a ID da instância do computador que deseja gerenciar. Você pode usar o [Gerenciador de Recursos do Azure](https://resources.azure.com) para localizar a ID da instância de uma máquina virtual em um conjunto de escala. Você também pode usar o Gerenciador de Recursos para verificar o status das tarefas que você concluir.

Confira [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para saber mais sobre como instalar a versão mais recente do Azure PowerShell, selecionar a assinatura e entrar em sua conta.

## <a name="display-information-about-a-scale-set"></a>Exibir informações sobre um conjunto de dimensionamento
Você pode obter informações gerais sobre um conjunto de escala, que também é conhecido como a exibição de instância. Ou então, você pode obter informações mais específicas, tais como informações sobre os recursos no conjunto de dimensionamento.

Substitua os valores entre aspas com o nome ou o grupo de recursos e o conjunto de dimensionamento e, em seguida, execute o comando:

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

Ele retorna algo semelhante a:

    Id                                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                                        : myvmss1
    Type                                        : Microsoft.Compute/virtualMachineScaleSets
    Location                                    : centralus
    Sku                                         :
      Name                                      : Standard_A0
      Tier                                      : Standard
      Capacity                                  : 3
    UpgradePolicy                               :
      Mode                                      : Manual
    VirtualMachineProfile                       :
      OsProfile                                 :
        ComputerNamePrefix                      : vmss1
        AdminUsername                           : admin1
        WindowsConfiguration                    :
          ProvisionVMAgent                      : True
          EnableAutomaticUpdates                : True
    StorageProfile                              :
      ImageReference                            :
        Publisher                               : MicrosoftWindowsServer
        Offer                                   : WindowsServer
        Sku                                     : 2012-R2-Datacenter
        Version                                 : latest
      OsDisk                                    :
        Name                                    : vmssosdisk
        Caching                                 : ReadOnly
        CreateOption                            : FromImage
        VhdContainers[0]                        : https://astore.blob.core.windows.net/vmss
        VhdContainers[1]                        : https://gstore.blob.core.windows.net/vmss
        VhdContainers[2]                        : https://mstore.blob.core.windows.net/vmss
        VhdContainers[3]                        : https://sstore.blob.core.windows.net/vmss
        VhdContainers[4]                        : https://ystore.blob.core.windows.net/vmss
    NetworkProfile                              :
      NetworkInterfaceConfigurations[0]         :
        Name                                    : mync1
        Primary                                 : True
        IpConfigurations[0]                     :
          Name                                  : ip1
          Subnet                                :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/virtualNetworks/myvn1/subnets/mysn1
          LoadBalancerBackendAddressPools[0]    :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/backendAddressPools/bepool1
        LoadBalancerInboundNatPools[0]          :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/inboundNatPools/natpool1
    ExtensionProfile                            :
      Extensions[0]                             :
        Name                                    : Microsoft.Insights.VMDiagnosticsSettings
        Publisher                               : Microsoft.Azure.Diagnostics
        Type                                    : IaaSDiagnostics
        TypeHandlerVersion                      : 1.5
        AutoUpgradeMinorVersion                 : True
        Settings                                : {"xmlCfg":"...","storageAccount":"astore"}
    ProvisioningState                           : Succeeded

Substitua os valores entre aspas com o nome do seu grupo de recursos e do conjunto de dimensionamento. Substitua *#* com o identificador de instância da máquina virtual da qual você deseja obter informações e, em seguida, execute:

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

Ele retorna algo semelhante a:

    Id                            : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/
                                    virtualMachineScaleSets/myvmss1/virtualMachines/0
    Name                          : myvmss1_0
    Type                          : Microsoft.Compute/virtualMachineScaleSets/virtualMachines
    Location                      : centralus
    InstanceId                    : 0
    Sku                           :
      Name                        : Standard_A0
      Tier                        : Standard
    LatestModelApplied            : True
    StorageProfile                :
      ImageReference              :
        Publisher                 : MicrosoftWindowsServer
        Offer                     : WindowsServer
        Sku                       : 2012-R2-Datacenter
        Version                   : 4.0.20160617
      OsDisk                      :
        OsType                    : Windows
        Name                      : vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8
        Vhd                       :
          Uri                     : https://astore.blob.core.windows.net/vmss/vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8.vhd
        Caching                   : ReadOnly
        CreateOption              : FromImage
    OsProfile                     :
      ComputerName                : myvmss1-0
      AdminUsername               : admin1
      WindowsConfiguration        :
        ProvisionVMAgent          : True
        EnableAutomaticUpdates    : True
    NetworkProfile                :
      NetworkInterfaces[0]        :
        Id                        : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/
                                    myvmss1/virtualMachines/0/networkInterfaces/mync1
    ProvisioningState             : Succeeded
    Resources[0]                  :
      Id                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachines/
                                    myvmss1_0/extensions/Microsoft.Insights.VMDiagnosticsSettings
      Name                        : Microsoft.Insights.VMDiagnosticsSettings
      Type                        : Microsoft.Compute/virtualMachines/extensions
      Location                    : centralus
      Publisher                   : Microsoft.Azure.Diagnostics
      VirtualMachineExtensionType : IaaSDiagnostics
      TypeHandlerVersion          : 1.5
      AutoUpgradeMinorVersion     : True
      Settings                    : {"xmlCfg":"...","storageAccount":"astore"}
      ProvisioningState           : Succeeded

## <a name="start-a-virtual-machine-in-a-scale-set"></a>Iniciar uma máquina virtual em um conjunto de escala
Substitua os valores entre aspas com o nome do seu grupo de recursos e do conjunto de dimensionamento. Substitua *#* com o identificador da máquina virtual que você deseja iniciar e, em seguida, execute:

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

Você poderá iniciar todas as máquinas virtuais do conjunto de dimensionamento se não usar o parâmetro -InstanceId.

## <a name="stop-a-virtual-machine-in-a-scale-set"></a>Parar uma máquina virtual em um conjunto de escala
Substitua os valores entre aspas com o nome do seu grupo de recursos e do conjunto de dimensionamento. Substitua *#* com o identificador da máquina virtual que você deseja parar e, em seguida, execute:

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

Para parar uma máquina virtual sem desalocá-la, use o parâmetro - StayProvisioned. Você poderá parar todas as máquinas virtuais do conjunto se não usar o parâmetro -InstanceId.

## <a name="restart-a-virtual-machine-in-a-scale-set"></a>Reiniciar uma máquina virtual em um conjunto de escala
Substitua os valores entre aspas com o nome do seu grupo de recursos e o conjunto de dimensionamento. Substitua *#* com o identificador da máquina virtual que você deseja reiniciar e, em seguida, execute:

    Restart-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

Você poderá reiniciar todas as máquinas virtuais do conjunto se não usar o parâmetro -InstanceId.

## <a name="remove-a-virtual-machine-from-a-scale-set"></a>Remover uma máquina virtual de um conjunto de escala
Substitua os valores entre aspas com o nome do seu grupo de recursos e o conjunto de dimensionamento. Substitua *#* com o identificador da máquina virtual que você deseja remover e, em seguida, execute:  

    Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId #

Você poderá remover o conjunto de escala da máquina virtual se não usar o parâmetro - InstanceId.

## <a name="change-the-capacity-of-a-scale-set"></a>Alterar a capacidade de um conjunto de dimensionamento
Você pode adicionar ou remover máquinas virtuais, alterando a capacidade do conjunto. Obtenha o conjunto de dimensionamento que você deseja alterar, defina a capacidade que você deseja e, em seguida, atualize o conjunto de dimensionamento com a nova capacidade. Nesses comandos, substitua os valores entre aspas com o nome do seu grupo de recursos e o conjunto de dimensionamento.

    $vmss = Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
    $vmss.sku.capacity = 5
    Update-AzureRmVmss -ResourceGroupName "resource group name" -Name "scale set name" -VirtualMachineScaleSet $vmss 

Se você estiver removendo máquinas virtuais do conjunto de dimensionamento, as máquinas virtuais com as ids de mais altas são removidas primeiro.


