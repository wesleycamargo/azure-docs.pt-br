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
	ms.date="06/10/2016"
	ms.author="davidmu"/>

# Criar um conjunto de escala de Máquina Virtual do Windows usando o Azure PowerShell

Estas etapas adotam uma abordagem de preenchimento de lacunas para criar um Conjunto de escala de Máquina virtual do Azure. Consulte [Conjuntos de Dimensionamento de Máquina Virtual - Visão Geral](virtual-machine-scale-sets-overview.md) para saber mais sobre conjuntos de escala.

Deve levar cerca de 30 minutos para executar as etapas neste artigo.

## Etapa 1: instalar o PowerShell do Azure

Consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para saber mais sobre como instalar a versão mais recente do Azure PowerShell, selecionar a assinatura que você deseja usar e entrar na sua conta do Azure.

## Etapa 2: criar recursos

Crie os recursos que são necessários para o novo conjunto de escala de máquina virtual.

### Grupo de recursos

Um conjunto de escala de máquina virtual deve estar contido em um grupo de recursos.

1.  Obtenha uma lista de locais disponíveis e os serviços que têm suporte:

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

2. Escolha o local que funciona melhor para você, substitua o valor de **$locName** pelo nome do local e crie a variável:

        $locName = "location name from the list, such as Central US"

3. Substitua o valor de **$rgName** pelo nome que você deseja usar para o novo grupo de recursos e crie a variável:

        $rgName = "resource group name"
        
4. Crie o grupo de recursos:
    
        New-AzureRmResourceGroup -Name $rgName -Location $locName

    Você deverá ver algo assim:

        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### Conta de armazenamento

Uma conta de armazenamento é usada por uma máquina virtual para armazenar o disco do sistema operacional e os dados de diagnóstico utilizados para o dimensionamento. Quando possível, é recomendável ter uma conta de armazenamento para cada máquina virtual criada em um conjunto de dimensionamento. Se não for possível, planeje para não mais de 20 VMs por conta de armazenamento. O exemplo neste artigo mostra três contas de armazenamento sendo criadas para três máquinas virtuais em um conjunto de dimensionamento.

1. Substitua o valor de **saName** pelo nome que você deseja usar para a conta de armazenamento e crie a variável: 

        $saName = "storage account name"
        
2. Teste se o nome que você selecionou é exclusivo:
    
        Test-AzureName -Storage $saName

    Se a resposta for **False**, o nome proposto será exclusivo.

3. Substitua o valor de **$saType** pelo tipo de conta de armazenamento e crie a variável:

        $saType = "storage account type"
        
    Os valores possíveis são: Standard\_LRS, Standard\_GRS, Standard\_RAGRS ou Premium\_LRS.
        
4. Crie a conta:
    
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

5. Repita as etapas de 1 a 4 para criar três contas de armazenamento, por exemplo, myst1, myst2 e myst3.

### Rede virtual

Uma rede virtual é necessária para as máquinas virtuais no conjunto de escala.

1. Substitua o valor de **$subName** pelo nome que você deseja usar para a sub-rede na rede virtual e crie a variável: 

        $subName = "subnet name"
        
2. Crie as configurações de sub-rede:
    
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subName -AddressPrefix 10.0.0.0/24
        
    O prefixo do endereço pode ser diferente em sua rede virtual.

3. Substitua o valor de **$netName** pelo nome que você deseja usar para a rede virtual e crie a variável:

        $netName = "virtual network name"
        
4. Crie a rede virtual:
    
        $vnet = New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### Endereço IP público

Antes da criação de uma interface de rede, você precisa criar um endereço IP público.

1. Substitua o valor de **$domName** pelo rótulo do nome de domínio que você deseja usar com seu endereço IP público e crie a variável:  

        $domName = "domain name label"
        
    O rótulo pode conter somente letras, números e hifens, e o último caractere deve ser uma letra ou um número.
    
2. Teste se o nome é exclusivo:
    
        Test-AzureRmDnsAvailability -DomainQualifiedName $domName -Location $locName

    Se a resposta for **True**, o nome proposto será exclusivo.

3. Substitua o valor de **$pipName** pelo nome que você deseja usar para o endereço IP público e crie a variável.

        $pipName = "public ip address name"
        
4. Crie o endereço IP público:
    
        $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic -DomainNameLabel $domName

### Interface de rede

Agora que tem o endereço IP público, você pode criar a interface de rede.

1. Substitua o valor de **$nicName** pelo nome que você deseja usar para a interface de rede e crie a variável: 

        $nicName = "network interface name"
        
2. Crie a interface de rede:
    
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

### Configuração do conjunto de escala

Você tem todos os recursos necessários para a configuração do conjunto de escala, então vamos criá-lo.

1. Substitua o valor de **$ipName** pelo nome que você deseja usar para a configuração do IP e crie a variável: 

        $ipName = "IP configuration name"
        
2. Crie a configuração de IP:

        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id

2. Substitua o valor de **$vmssConfig** pelo nome que você deseja usar para a configuração do conjunto de dimensionamento e crie a variável:

        $vmssConfig = "Scale set configuration name"
        
3. Crie a configuração do conjunto de escala:

        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0" -UpgradePolicyMode "manual"
        
    Este exemplo mostra um conjunto de escala sendo criado com 3 máquinas virtuais. Consulte [Visão Geral dos Conjuntos de Dimensionamento da Máquina Virtual](virtual-machine-scale-sets-overview.md) para saber mais sobre a capacidade dos conjuntos de dimensionamento. Essa etapa também inclui a definição do tamanho (chamado de SkuName) das máquinas virtuais no conjunto. Examine os [Tamanhos das máquinas virtuais](../virtual-machines/virtual-machines-windows-sizes.md) para encontrar um tamanho que atenda às suas necessidades.
    
4. Adicione a configuração da interface de rede à configuração do conjunto de escala:
        
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssConfig -Primary $true -IPConfiguration $ipConfig
        
    Você deverá ver algo assim:

        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     :
        OverProvision         :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags                  :

#### Perfil do sistema operacional

1. Substitua o valor de **$computerName** pelo prefixo do nome do computador que você deseja usar e crie a variável: 

        $computerName = "computer name prefix"
        
2. Substitua o valor de **$adminName** pelo nome da conta de administrador nas máquinas virtuais e crie a variável:

        $adminName = "administrator account name"
        
3. Substitua o valor de **$adminPassword** pela senha da conta e crie a variável:

        $adminPassword = "password for administrator accounts"
        
4. Crie o perfil do sistema operacional:

        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

#### Perfil de armazenamento

1. Substitua o valor de **$storageProfile** pelo nome que você deseja usar para o perfil de armazenamento e crie a variável:  

        $storeProfile = "storage profile name"
        
2. Crie as variáveis que definem a imagem a ser usada:
      
        $imagePublisher = "MicrosoftWindowsServer"
        $imageOffer = "WindowsServer"
        $imageSku = "2012-R2-Datacenter"
        
    Consulte [Navegar e selecionar imagens da máquina virtual do Azure com o Windows PowerShell e a CLI do Azure](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md) para encontrar informações sobre outras imagens a usar.
        
3. Substitua o valor de **$vhdContainers** por uma lista que contém os caminhos nos quais os discos rígidos virtuais são armazenados, como "https://mystorage.blob.core.windows.net/vhds", em seguida, crie a variável:
       
        $vhdContainers = @("https://myst1.blob.core.windows.net/vhds","https://myst2.blob.core.windows.net/vhds","https://myst3.blob.core.windows.net/vhds")
        
4. Crie o perfil de armazenamento:

        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -Name $storeProfile -VhdContainer $vhdContainers -OsDiskCreateOption "FromImage" -OsDiskCaching "None"  

### Conjunto de escala de máquina virtual

Por fim, você pode criar o conjunto de escala.

1. Substitua o valor de **$vmssName** pelo nome do conjunto de dimensionamento da máquina virtual e crie a variável:

        $vmssName = "scale set name"
        
2. Crie o conjunto de escala:

        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss

    Você deve ver algo assim, o que indica que a implantação foi bem-sucedida:

        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     : Updating
        OverProvision         :
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microso
                               ft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags                  :

## Etapa 3: explorar recursos

Use estes recursos para explorar o conjunto de escala de máquina virtual que você acabou de criar:

- Portal do Azure - uma quantidade limitada de informações está disponível por meio do portal.
- [Azure Resource Manager](https://resources.azure.com/) - esta é a melhor ferramenta para explorar o estado atual do conjunto de dimensionamento.
- Azure Powershell - use este comando para obter informações:

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
        
        Or 
        
        Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
        

## Próximas etapas

- Gerencie o conjunto de dimensionamento que você acabou de criar usando as informações em [Gerenciar máquinas virtuais em um Conjunto de Dimensionamento da Máquina Virtual](virtual-machine-scale-sets-windows-manage.md)
- Considere configurar o dimensionamento automático de seu conjunto de dimensionamento usando as informações em [Dimensionamento automático e conjuntos de dimensionamento da máquina virtual](virtual-machine-scale-sets-autoscale-overview.md)
- Saiba mais sobre o dimensionamento vertical revisando [Dimensionamento vertical automático com conjuntos de Dimensionamento da Máquina Virtual](virtual-machine-scale-sets-vertical-scale-reprovision.md)

<!---HONumber=AcomDC_0615_2016-->