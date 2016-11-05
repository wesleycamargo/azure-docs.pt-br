---
title: Implantar VMs com várias NICs usando o Azure PowerShell no Gerenciador de Recursos | Microsoft Docs
description: Saiba como implantar VMs com várias NICs usando o Azure PowerShell no Gerenciador de Recursos
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial

---
# Implantar VMs com várias NICs usando o Azure PowerShell
[!INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[!INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

[classic deployment model](virtual-network-deploy-multinic-classic-ps.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

No momento, você não pode ter VMs com uma única NIC e VMs com várias NICs no mesmo conjunto de disponibilidade. Portanto, você precisa implementar servidores back-end em um grupo de recursos diferente de todos os outros componentes. As etapas a seguir usam um grupo de recursos chamado *IaaSStory* para o grupo de recursos principal e *IaaSStory-BackEnd* para os servidores de back-end.

## Pré-requisitos
Antes de implantar os servidor de back-end, você deve implantar o grupo de recursos principal com todos os recursos necessários para este cenário. Para implantar esses recursos, faça o seguinte:

1. Navegue até [a página do modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Na página do modelo, à direita do **Grupo de recursos pai**, clique em **Implantar no Azure**.
3. Caso necessário, altere os valores de parâmetro e siga as etapas no Portal de visualização do Azure para implantar o grupo de recursos.

> [!IMPORTANT]
> Verifique se os nomes da conta de armazenamento são exclusivos. Você não pode ter nomes de conta de armazenamento duplicados no Azure.
> 
> 

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Implantar VMs de back-end
As VMs de back-end dependem da criação dos recursos descritos abaixo.

* **Conta de armazenamento para discos de dados**. Para obter um melhor desempenho, os discos de dados dos servidores de banco de dados usam a tecnologia SDD (unidade de estado sólido), que requer uma conta de Armazenamento Premium. Verifique se o local do Azure no qual você vai implantar é compatível com o Armazenamento Premium.
* **NICs**. Cada VM tem duas NICs, uma para acesso ao banco de dados e outra para gerenciamento.
* **Conjunto de disponibilidade**. Todos os servidores de banco de dados são adicionados a um conjunto de disponibilidade único, para garantir que pelo menos uma das VMs está ativa e em execução durante a manutenção.

### Etapa 1 – Iniciar o script
Baixe o script completo do PowerShell usado [aqui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/arm/virtual-network-deploy-multinic-arm-ps.ps1). Realize os procedimentos abaixo para alterar o script para funcionar em seu ambiente.

[!INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. Altere os valores das variáveis a seguir com base no seu grupo de recursos existente implantado acima em [pré-requisitos](#Prerequisites).
   
        $existingRGName        = "IaaSStory"
        $location              = "West US"
        $vnetName              = "WTestVNet"
        $backendSubnetName     = "BackEnd"
        $remoteAccessNSGName   = "NSG-RemoteAccess"
        $stdStorageAccountName = "wtestvnetstoragestd"
2. Altere os valores das variáveis a seguir de acordo com os valores que deseja usar na implantação do back-end.
   
        $backendRGName         = "IaaSStory-Backend"
        $prmStorageAccountName = "wtestvnetstorageprm"
        $avSetName             = "ASDB"
        $vmSize                = "Standard_DS3"
        $publisher             = "MicrosoftSQLServer"
        $offer                 = "SQL2014SP1-WS2012R2"
        $sku                   = "Standard"
        $version               = "latest"
        $vmNamePrefix          = "DB"
        $osDiskPrefix          = "osdiskdb"
        $dataDiskPrefix        = "datadisk"
        $diskSize               = "120"    
        $nicNamePrefix         = "NICDB"
        $ipAddressPrefix       = "192.168.2."
        $numberOfVMs           = 2
3. Recupere os recursos existentes necessários para a implantação.
   
        $vnet                  = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $existingRGName
        $backendSubnet         = $vnet.Subnets|?{$_.Name -eq $backendSubnetName}
        $remoteAccessNSG       = Get-AzureRmNetworkSecurityGroup -Name $remoteAccessNSGName -ResourceGroupName $existingRGName
        $stdStorageAccount     = Get-AzureRmStorageAccount -Name $stdStorageAccountName -ResourceGroupName $existingRGName

### Etapa 2: criar recursos necessários para as VMs
Você deve criar um novo grupo de recursos, uma conta de armazenamento para os discos de dados e um conjunto de disponibilidade para todas as VMs. Além disso, você precisa de credenciais da conta de administrador local para cada VM. Para criar esses recursos, faça o seguinte:

1. Crie um novo grupo de recursos.
   
        New-AzureRmResourceGroup -Name $backendRGName -Location $location
2. Crie uma nova conta de Armazenamento Premium no grupo de recursos criado acima.
   
        $prmStorageAccount = New-AzureRmStorageAccount -Name $prmStorageAccountName `
            -ResourceGroupName $backendRGName -Type Premium_LRS -Location $location
3. Crie um novo conjunto de disponibilidade.
   
        $avSet = New-AzureRmAvailabilitySet -Name $avSetName -ResourceGroupName $backendRGName -Location $location
4. Obtenha as credenciais da conta de administrador local para usar em cada VM.
   
        $cred = Get-Credential -Message "Type the name and password for the local administrator account."

### Etapa 3: criar as NICs e as VMs de back-end
Você deve usar um loop para criar várias VMs desejadas e para criar as NICs e VMs necessárias dentro do loop. Para criar as NICs e VMs, faça o seguinte:

1. Inicie um `for` loop para repetir os comandos para criar uma VM e duas NICs quantas vezes forem necessárias, com base no valor da `$numberOfVMs` variável.
   
        for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
2. Crie a NIC usada para acesso ao banco de dados.
   
            $nic1Name = $nicNamePrefix + $suffixNumber + "-DA"
            $ipAddress1 = $ipAddressPrefix + ($suffixNumber + 3)
            $nic1 = New-AzureRmNetworkInterface -Name $nic1Name -ResourceGroupName $backendRGName `
                -Location $location -SubnetId $backendSubnet.Id -PrivateIpAddress $ipAddress1
3. Crie a NIC usada para acesso remoto. Observe que esta NIC tem um NSG associado a ela.
   
            $nic2Name = $nicNamePrefix + $suffixNumber + "-RA"
            $ipAddress2 = $ipAddressPrefix + (53 + $suffixNumber)
            $nic2 = New-AzureRmNetworkInterface -Name $nic2Name -ResourceGroupName $backendRGName `
                -Location $location -SubnetId $backendSubnet.Id -PrivateIpAddress $ipAddress2 `
                -NetworkSecurityGroupId $remoteAccessNSG.Id
4. Criar `vmConfig` objeto.
   
            $vmName = $vmNamePrefix + $suffixNumber
            $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id
5. Crie dois discos de dados por VM. Observe que os discos de dados se encontram na conta de Armazenamento Premium criada anteriormente.
   
            $dataDisk1Name = $vmName + "-" + $osDiskPrefix + "-1"    
            $data1VhdUri = $prmStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $dataDisk1Name + ".vhd"
            Add-AzureRmVMDataDisk -VM $vmConfig -Name $dataDisk1Name -DiskSizeInGB $diskSize `
                -VhdUri $data1VhdUri -CreateOption empty -Lun 0
   
            $dataDisk2Name = $vmName + "-" + $dataDiskPrefix + "-2"    
            $data2VhdUri = $prmStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $dataDisk2Name + ".vhd"
            Add-AzureRmVMDataDisk -VM $vmConfig -Name $dataDisk2Name -DiskSizeInGB $diskSize `
                -VhdUri $data2VhdUri -CreateOption empty -Lun 1
6. Configure o sistema operacional e a imagem que vai usar na VM.
   
            $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
            $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher -Offer $offer -Skus $sku -Version $version
7. Adicione as duas NICs criadas acima ao `vmConfig` objeto.
   
            $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic1.Id -Primary
            $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic2.Id
8. Crie o disco do sistema operacional e a VM. Observe que o `}` terminando o `for` loop.
   
            $osDiskName = $vmName + "-" + $osDiskSuffix
            $osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
            $vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage
            New-AzureRmVM -VM $vmConfig -ResourceGroupName $backendRGName -Location $location
        }

### Etapa 4: executar o script
Agora que você baixou e alterou o script de acordo com suas necessidades, execute o script para criar VMs do banco de dados de back-end com várias NICs.

1. Salve seu script e execute-o no prompt de comando do **PowerShell** ou **PowerShell ISE**. A saída inicial será exibida, conforme mostrado abaixo:
   
        ResourceGroupName : IaaSStory-Backend
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *                  
   
        ResourceId        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/IaaSStory-Backend
2. Após alguns minutos, preencha a solicitação de credenciais e clique em **OK**. A saída abaixo representa uma VM única. Observe que o processo inteiro levou 8 minutos para ser concluído.
   
        ResourceGroupName            :
        Id                           :
        Name                         : DB2
        Type                         :
        Location                     :
        Tags                         :
        TagsText                     : null
        AvailabilitySetReference     : Microsoft.Azure.Management.Compute.Models.AvailabilitySetReference
        AvailabilitySetReferenceText : {
                                         "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/
                                       Microsoft.Compute/availabilitySets/ASDB"
                                       }
        Extensions                   :
        ExtensionsText               : null
        HardwareProfile              : Microsoft.Azure.Management.Compute.Models.HardwareProfile
        HardwareProfileText          : {
                                         "VirtualMachineSize": "Standard_DS3"
                                       }
        InstanceView                 :
        InstanceViewText             : null
        NetworkProfile               :
        NetworkProfileText           : null
        OSProfile                    :
        OSProfileText                : null
        Plan                         :
        PlanText                     : null
        ProvisioningState            :
        StorageProfile               : Microsoft.Azure.Management.Compute.Models.StorageProfile
        StorageProfileText           : {
                                         "DataDisks": [
                                           {
                                             "Lun": 0,
                                             "Caching": null,
                                             "CreateOption": "empty",
                                             "DiskSizeGB": 127,
                                             "Name": "DB2-datadisk-1",
                                             "SourceImage": null,
                                             "VirtualHardDisk": {
                                               "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-1.vhd"
                                             }
                                           }
                                         ],
                                         "ImageReference": null,
                                         "OSDisk": null
                                       }
        DataDiskNames                : {DB2-datadisk-1}
        NetworkInterfaceIDs          :
        RequestId                    :
        StatusCode                   : 0

        ResourceGroupName            :
        Id                           :
        Name                         : DB2
        Type                         :
        Location                     :
        Tags                         :
        TagsText                     : null
        AvailabilitySetReference     : Microsoft.Azure.Management.Compute.Models.AvailabilitySetReference
        AvailabilitySetReferenceText : {
                                         "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/
                                       Microsoft.Compute/availabilitySets/ASDB"
                                       }
        Extensions                   :
        ExtensionsText               : null
        HardwareProfile              : Microsoft.Azure.Management.Compute.Models.HardwareProfile
        HardwareProfileText          : {
                                         "VirtualMachineSize": "Standard_DS3"
                                       }
        InstanceView                 :
        InstanceViewText             : null
        NetworkProfile               :
        NetworkProfileText           : null
        OSProfile                    :
        OSProfileText                : null
        Plan                         :
        PlanText                     : null
        ProvisioningState            :
        StorageProfile               : Microsoft.Azure.Management.Compute.Models.StorageProfile
        StorageProfileText           : {
                                         "DataDisks": [
                                           {
                                             "Lun": 0,
                                             "Caching": null,
                                             "CreateOption": "empty",
                                             "DiskSizeGB": 127,
                                             "Name": "DB2-datadisk-1",
                                             "SourceImage": null,
                                             "VirtualHardDisk": {
                                               "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-1.vhd"
                                             }
                                           },
                                           {
                                             "Lun": 1,
                                             "Caching": null,
                                             "CreateOption": "empty",
                                             "DiskSizeGB": 127,
                                             "Name": "DB2-datadisk-2",
                                             "SourceImage": null,
                                             "VirtualHardDisk": {
                                               "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-2.vhd"
                                             }
                                           }
                                         ],
                                         "ImageReference": null,
                                         "OSDisk": null
                                       }
        DataDiskNames                : {DB2-datadisk-1, DB2-datadisk-2}
        NetworkInterfaceIDs          :
        RequestId                    :
        StatusCode                   : 0


        EndTime             : 10/30/2015 9:30:03 AM -08:00
        Error               :
        Output              :
        StartTime           : 10/30/2015 9:22:54 AM -08:00
        Status              : Succeeded
        TrackingOperationId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        RequestId           : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        StatusCode          : OK

<!---HONumber=AcomDC_0914_2016-->