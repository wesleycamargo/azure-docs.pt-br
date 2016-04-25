<properties
   pageTitle="Implantar VMs com várias NICs usando o PowerShell no modelo de implantação clássica | Microsoft Azure"
   description="Aprenda a implantar VMs com várias NICs usando o PowerShell no modelo de implantação clássica"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="telmos" />

#Implantar VMs com várias NICs (clássicas) usando o PowerShell

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-network-deploy-multinic-arm-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

No momento, você não pode ter VMs com uma única NIC e VMs com várias NICs no mesmo serviço de nuvem. Portanto, você precisa implementar servidores back-end em um serviço de nuvem diferente de todos os outros componentes no cenário. As etapas a seguir usam um serviço de nuvem chamado *IaaSStory* para os principais recursos, e *back-end IaaSStory* para os servidores back-end.

## Pré-requisitos

Antes de implantar os servidores de back-end, você precisa implantar o serviço de nuvem principal com todos os recursos necessários para este cenário. No mínimo, você precisa criar uma rede virtual com uma sub-rede para o back-end. Visite [Criar uma rede virtual usando o PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md) para saber como implantar uma rede virtual.

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Implantar VMs de back-end

As VMs de back-end dependem da criação dos recursos descritos abaixo.

- **Sub-rede de back-end**. Os servidores de banco de dados serão parte de uma sub-rede separada, para segregar o tráfego. O script a seguir espera que essa sub-rede exista em uma vnet chamada *WTestVnet*.
- **Conta de armazenamento para discos de dados**. Para obter um melhor desempenho, os discos de dados dos servidores de banco de dados usam a tecnologia SDD (unidade de estado sólido), que requer uma conta de Armazenamento Premium. Verifique se o local do Azure no qual você vai implantar é compatível com o Armazenamento Premium.
- **Conjunto de disponibilidade**. Todos os servidores de banco de dados são adicionados a um conjunto de disponibilidade único, para garantir que pelo menos uma das VMs está ativa e em execução durante a manutenção.

### Etapa 1: iniciar o script

Baixe o script completo do PowerShell usado [aqui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1). Realize os procedimentos abaixo para alterar o script para funcionar em seu ambiente.

1. Altere os valores das variáveis a seguir com base no grupo de recursos existente implantado acima, no tópico [Pré-requisitos](#Prerequisites).

		$location              = "West US"
		$vnetName              = "WTestVNet"
		$backendSubnetName     = "BackEnd"

2. Altere os valores das variáveis a seguir de acordo com os valores que deseja usar na implantação do back-end.

		$backendCSName         = "IaaSStory-Backend"
		$prmStorageAccountName = "iaasstoryprmstorage"
		$avSetName             = "ASDB"
		$vmSize                = "Standard_DS3"
		$diskSize              = 127
		$vmNamePrefix          = "DB"
		$dataDiskSuffix        = "datadisk"
		$ipAddressPrefix       = "192.168.2."
		$numberOfVMs           = 2

### Etapa 2 – Criar recursos necessários para as VMs

Você precisa criar um novo serviço de nuvem e uma conta de armazenamento para os discos de dados para todas as máquinas virtuais. Você também precisa especificar uma imagem e uma conta de administrador local para as VMs. Para criar esses recursos, faça o seguinte:

1. Crie um novo serviço de nuvem

		New-AzureService -ServiceName $backendCSName -Location $location

2. Crie uma nova conta de armazenamento Premium.

		New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
		    -Location $location `
		    -Type Premium_LRS

3. Defina a conta de armazenamento criada acima como a conta de armazenamento para sua assinatura.

		$subscription = Get-AzureSubscription `
		    | where {$_.IsCurrent -eq $true}  
		Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
		    -CurrentStorageAccountName $prmStorageAccountName

4. Selecione uma imagem para a VM.

		$image = Get-AzureVMImage `
		    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
		    | sort PublishedDate -Descending `
		    | select -ExpandProperty ImageName -First 1

5. Defina as credenciais da conta de administrador local.

		$cred = Get-Credential -Message "Enter username and password for local admin account"

### Etapa 3 - Criar VMs

Você deve usar um loop para criar várias VMs desejadas e para criar as NICs e VMs necessárias dentro do loop. Para criar as NICs e VMs, faça o seguinte:

1. Inicie um loop `for` para repetir os comandos para criar uma VM e duas NICs, quantas vezes forem necessárias, com base no valor da variável `$numberOfVMs`.

		for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){

2. Crie um objeto `VMConfig` que especifica a imagem, o tamanho e o conjunto de disponibilidade da VM.

		    $vmName = $vmNamePrefix + $suffixNumber
		    $vmConfig = New-AzureVMConfig -Name $vmName `
		                    -ImageName $image `
		                    -InstanceSize $vmSize `
		                    -AvailabilitySetName $avSetName  

3. Provisione a VM como uma VM do Windows.

		    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
		        -AdminUsername $cred.UserName `
		        -Password $cred.Password

4. Defina a NIC padrão e atribua um endereço IP estático.

		    Set-AzureSubnet -SubnetNames $backendSubnetName -VM $vmConfig
		    Set-AzureStaticVNetIP -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig

5. Adicione uma segunda NIC para cada VM.

		    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
		        -SubnetName $backendSubnetName `
		        -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
		        -VM $vmConfig

6. Crie discos de dados para cada VM.

		    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
		    Add-AzureDataDisk -CreateNew -VM $vmConfig `
		        -DiskSizeInGB $diskSize `
		        -DiskLabel $dataDisk1Name `
		        -LUN 0       

		    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
		    Add-AzureDataDisk -CreateNew -VM $vmConfig `
		        -DiskSizeInGB $diskSize `
		        -DiskLabel $dataDisk2Name `
		        -LUN 1

7. Crie cada VM e encerre o loop.

		    New-AzureVM -VM $vmConfig `
		        -ServiceName $backendCSName `
		        -Location $location `
		        -VNetName $vnetName
		}

### Etapa 4 – Executar o script

Agora que você baixou e alterou o script de acordo com suas necessidades, execute o script para criar VMs do banco de dados de back-end com várias NICs.

1. Salve seu script e execute-o no prompt de comando do **PowerShell** ou **PowerShell ISE**. A saída inicial será exibida, conforme mostrado abaixo:

		OperationDescription    OperationId                          OperationStatus
		--------------------    -----------                          ---------------
		New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      
		New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      

		WARNING: No deployment found in service: 'IaaSStory-Backend'.

2. Preencha as informações necessárias na solicitação de credenciais e clique em **OK**. A saída abaixo será exibida.

		New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
		New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded

<!---HONumber=AcomDC_0413_2016-->