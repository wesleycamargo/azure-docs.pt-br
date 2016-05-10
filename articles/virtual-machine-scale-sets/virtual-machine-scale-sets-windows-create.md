<properties
	pageTitle="Criar um Conjunto de Escala de Máquina Virtual | Microsoft Azure"
	description="Criar um Conjunto de Escala de Máquina Virtual usando o Powershell"
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
	ms.date="03/22/2016"
	ms.author="davidmu"/>

# Criar um conjunto de escala de Máquina Virtual do Windows usando o Azure PowerShell

Estas etapas seguem uma abordagem de preenchimento de lacunas para criar um conjunto de escala de máquina virtual do Azure. Em todo o artigo, há variáveis que precisam de valores fornecidos por você. Substitua tudo entre aspas por valores que façam sentido para sua assinatura e seu aplicativo.

## Etapa 1: instalar o PowerShell do Azure

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## Etapa 2: definir sua assinatura

1. Inicie um prompt do PowerShell.

2. Faça logon em sua conta:

        Login-AzureRmAccount

3. Obtenha sua assinatura:

        Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

4. Defina a assinatura que você deseja usar como atual:

        $subscr = "subscription name"
        Select-AzureSubscription -SubscriptionName $subscr –Current


## Etapa 2: criar recursos

Crie os recursos que são necessários para o novo conjunto de escala de máquina virtual.

### Grupo de recursos

Um conjunto de escala de máquina virtual deve estar contido em um grupo de recursos.

1.  Execute este comando para obter uma lista de locais disponíveis e os serviços que têm suporte:

        Get-AzureLocation | Sort Name | Select Name, AvailableServices

    Você deverá ver algo assim

        Name                AvailableServices
        ----                -----------------
        Australia East      {Compute, Storage, PersistentVMRole, HighMemory}
        Australia Southeast {Compute, Storage, PersistentVMRole, HighMemory}
        Brazil South        {Compute, Storage, PersistentVMRole, HighMemory}
        Central India       {Compute, Storage, PersistentVMRole, HighMemory}
        Central US          {Compute, Storage, PersistentVMRole, HighMemory}
        East Asia           {Compute, Storage, PersistentVMRole, HighMemory}
        East US             {Compute, Storage, PersistentVMRole, HighMemory}
        East US 2           {Compute, Storage, PersistentVMRole, HighMemory}
        Japan East          {Compute, Storage, PersistentVMRole, HighMemory}
        Japan West          {Compute, Storage, PersistentVMRole, HighMemory}
        North Central US    {Compute, Storage, PersistentVMRole, HighMemory}
        North Europe        {Compute, Storage, PersistentVMRole, HighMemory}
        South Central US    {Compute, Storage, PersistentVMRole, HighMemory}
        South India         {Compute, Storage, PersistentVMRole, HighMemory}
        Southeast Asia      {Compute, Storage, PersistentVMRole, HighMemory}
        West Europe         {Compute, Storage, PersistentVMRole, HighMemory}
        West India          {Compute, Storage, PersistentVMRole, HighMemory}
        West US             {Compute, Storage, PersistentVMRole, HighMemory}

    Escolha o local que funciona melhor para você e substitua o texto entre aspas pelo nome do local:

        $locName = "location name from the list, such as Central US"

4. Substitua o texto entre aspas pelo nome que você deseja usar para o novo grupo de recursos e crie-o no local definido anteriormente:

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName

    Você deverá ver algo assim:

        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### Conta de armazenamento

Máquinas virtuais criadas em um conjunto de escala exigem uma conta de armazenamento para armazenar os discos associados.

1. Substitua o texto entre aspas pelo nome que você deseja usar para a conta de armazenamento e teste se ele é exclusivo:

        $saName = "storage account name"
        Test-AzureName -Storage $saName

    Se a resposta for **False**, isso indicará que o nome proposto é exclusivo.

2. Substitua o texto entre aspas pelo tipo da conta de armazenamento e crie a conta com o nome e o local definidos anteriormente. Os valores possíveis são: Standard\_LRS, Standard\_GRS, Standard\_RAGRS ou Premium\_LRS:

        $saType = "storage account type"
        New-AzureRmStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

    Você deverá ver algo assim:

        ResourceGroupName   : myrg1
        StorageAccountName  : myst1
        Id                  : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft
	                    	.Storage/storageAccounts/myst1
        Location            : centralus
        AccountType         : StandardLRS
        CreationTime        : 3/15/2016 4:51:52 PM
        CustomDomain        :
        LastGeoFailoverTime :
        PrimaryEndpoints    : Microsoft.Azure.Management.Storage.Models.Endpoints
        PrimaryLocation     : centralus
        ProvisioningState   : Succeeded
        SecondaryEndpoints  :
        SecondaryLocation   :
        StatusOfPrimary     : Available
        StatusOfSecondary   :
        Tags                : {}
        Context             : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext

### Rede virtual

Uma rede virtual é necessária para as máquinas virtuais no conjunto de escala.

1. Substitua o texto entre aspas pelo nome que você deseja usar para a sub-rede na rede virtual e crie a configuração:

        $subName = "subnet name"
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subName -AddressPrefix 10.0.0.0/24

2. Substitua o texto entre aspas pelo nome que você deseja usar para a rede virtual e crie-a usando as informações e os recursos definidos anteriormente:

        $netName="virtual network name"
        $vnet=New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Endereço IP público

Antes da criação de uma interface de rede, você precisa criar um endereço IP público.

1. Substitua o texto entre aspas pelo rótulo de nome de domínio que você deseja usar com o endereço IP público e teste se o nome é exclusivo. O rótulo pode conter apenas letras, números e hifens, e o último caractere deve ser uma letra ou um número:

        $domName = "domain name label"
        Test-AzureRmDnsAvailability -DomainQualifiedName $domName -Location $locName

    Se a resposta for **False**, isso indicará que o nome proposto é exclusivo.

2. Substitua o texto entre aspas pelo nome que você deseja usar para o endereço IP público e crie-o:

        $pipName = "public ip address name"
        $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic -DomainNameLabel $domName

### Interface de rede

Agora que tem o endereço IP público, você pode criar a interface de rede.

1. Substitua o texto entre aspas pelo nome que você deseja usar para a interface de rede e crie-a usando os recursos criados anteriormente:

        $nicName = "network interface name"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


### Criar o conjunto de escala de máquina virtual

Você tem todos os recursos de que precisa. Agora é hora de criar o conjunto de escala.

1. Substitua o texto entre aspas pelo nome que você deseja usar para a configuração de IP e crie-a:

        $ipName = "IP configuration name"
        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id

2. Substitua o texto entre aspas pelo nome que você deseja usar para a configuração do conjunto de escala e crie-o. Essa etapa inclui a definição do tamanho (conhecido como SkuName) das máquinas virtuais no conjunto. Examine [Tamanhos de máquinas virtuais](..\virtual-machines\virtual-machines-windows-sizes.md) para localizar um tamanho que atenda às suas necessidades. Para este exemplo, é recomendável usar Standard\_A0:

        $vmssName = "Scale set configuration name"
        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0"
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssName -Primary $true -IPConfiguration $ipConfig

    Você deverá ver algo assim:

        Sku                   : {
                                  "name": "Standard_A0",
                                  "tier": null,
                                  "capacity": 3
        						}
        UpgradePolicy         : {
                                  "mode": "automatic"
                                }
        VirtualMachineProfile : {
                                  "osProfile": null,
                                  "storageProfile": null,
                                  "networkProfile": {
                                    "networkInterfaceConfigurations": [
                                      {
                                        "name": "myniccfg1",
                                        "properties.primary": true,
                                        "properties.ipConfigurations": [
                                          {
                                            "name": "myipconfig1",
                                            "properties.subnet": {
                                              "id": "/subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft.Network/virtualNetworks/myvn1/subnets/mysn1"
                                            },
                                            "properties.loadBalancerBackendAddressPools": [],
                                            "properties.loadBalancerInboundNatPools": [],
                                            "id": null
                                          }
                                        ],
                                        "id": null
                                      }
                                    ]
                                  },
                                  "extensionProfile": {
                                    "extensions": null
                                  }
                                }
        ProvisioningState     :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags.Count            : 0
        Tags                  :

3. Substitua o texto entre aspas pelo prefixo de nome de computador que você deseja usar, pelo nome da conta de administrador nas máquinas virtuais e pela senha da conta e, em seguida, crie o perfil do sistema operacional:

        $computerName = "computer name prefix"
        $adminName = "administrator account name"
        $adminPassword = "password for administrator accounts"
        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

    Você verá algo assim na seção osProfile:

        "osProfile": {
          "computerNamePrefix": "myvmss1",
          "adminUsername": "########",
          "adminPassword": "########",
          "customData": null,
          "windowsConfiguration": null,
          "linuxConfiguration": null,
          "secrets": null
        },

4. Substitua o texto entre aspas pelo nome que você deseja usar para o perfil de armazenamento, as informações da imagem e o caminho de armazenamento para onde os discos das máquinas virtuais são armazenados e, em seguida, crie o perfil. Examine [Navegar e selecionar imagens de máquina virtual do Azure com o Windows PowerShell e a CLI do Azure](..\virtual-machines\virtual-machines-windows-cli-ps-findimage.md) para encontrar as informações de que você precisa:

        $storeProfile = "storage profile name"
        $imagePublisher = "image publisher name, such as MicrosoftWindowsServer"
        $imageOffer = "offer from publisher, such as WindowsServer"
        $imageSku = "sku of image, such as 2012-R2-Datacenter"
        $vhdContainer = "URI of storage container"
        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -Name $storeProfile -VhdContainer $vhdContainer -OsDiskCreateOption "FromImage" -OsDiskCaching "None"

    Você deve ver algo assim na seção storageProfile:

        "storageProfile": {
          "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2012-R2-Datacenter",
            "version": "latest"
          },
          "osDisk": {
            "name": "mystore1",
            "caching": "None",
            "createOption": "FromImage",
            "osType": null,
            "image": null,
            "vhdContainers": {
              "http://myst1.blob.core.windows.net/vhds"
            }
          }
        },

5. Substitua o texto entre aspas pelo nome do conjunto de escala de máquina virtual e crie-o:

        $vmssName = "scale set name"
        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss

    Você deve ver algo assim, o que indica que a implantação foi bem-sucedida:

        ProvisioningState     : Succeeded
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags.Count            : 0
        Tags                  :

## Etapa 3: explorar recursos

Use estes recursos para explorar o conjunto de escala de máquina virtual que você acabou de criar:

- Portal do Azure - uma quantidade limitada de informações está disponível por meio do portal.
- [Azure Resource Explorer](https://resources.azure.com/) - essa é a melhor ferramenta para explorar o estado atual do conjunto de escala.
- Azure Powershell - use este comando para obter informações:

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

## Próximas etapas

1. Confira a [Visão Geral de Conjuntos de Escala de Máquina Virtual](virtual-machine-scale-sets-overview.md) para saber mais.

2. Considere a configuração do dimensionamento automático do conjunto de escala usando as informações em [Dimensionamento automático e conjuntos de escala de máquina virtual](virtual-machine-scale-sets-autoscale-overview.md)

<!---HONumber=AcomDC_0427_2016-->