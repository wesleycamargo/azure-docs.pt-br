---
title: Gerenciar VMs em um Conjunto de Escala de Máquina Virtual | Microsoft Docs
description: Gerenciar máquinas virtuais em um conjunto de escala de máquina virtual usando o Azure PowerShell.
services: virtual-machine-scale-sets
documentationcenter: ''
author: davidmu1
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2016
ms.author: davidmu

---
# Gerenciar máquinas virtuais em um conjunto de escala de máquina virtual
Use as tarefas neste artigo para gerenciar os recursos de máquina virtual no seu conjunto de escala de máquina virtual.

Todas as tarefas que envolvem o gerenciamento de uma máquina virtual em um conjunto de escala exigem que você conheça a ID da instância da máquina que deseja gerenciar. Você pode usar o [Gerenciador de Recursos do Azure](https://resources.azure.com) para localizar a ID da instância de uma máquina virtual em um conjunto de escala. Você também pode usar o Gerenciador de Recursos para verificar o status das tarefas que você concluir.

Consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para saber mais sobre como instalar a versão mais recente do Azure PowerShell, selecionar a assinatura que você deseja usar e entrar na sua conta do Azure.

## Exibir informações sobre um conjunto de escala de máquina virtual
Você pode obter informações gerais sobre um conjunto de escala, que também é conhecido como a exibição de instância. Ou então, você pode obter informações mais específicas, tais como informações sobre os recursos no conjunto.

Neste comando, substitua o *nome do grupo de recursos* pelo nome do grupo de recursos que contém o conjunto de escala de máquina virtual, o *nome do conjunto de escala* pelo nome do conjunto de escala da máquina virtual e execute-o:

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

Neste comando, substitua o *nome do grupo de recursos* pelo nome do grupo de recursos que contém o conjunto de escala de máquina virtual, o *nome do conjunto de escala* pelo nome do conjunto de escala da máquina virtual e *#* pelo identificador da instância da máquina virtual sobre a qual deseja obter informações e execute-o:

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

<!---HONumber=AcomDC_0720_2016-->