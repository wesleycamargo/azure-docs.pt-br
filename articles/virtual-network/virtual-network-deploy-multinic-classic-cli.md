<properties 
   pageTitle="Implantar VMs com várias NICs usando a CLI do Azure no modelo de implantação clássico | Microsoft Azure"
   description="Aprenda a implantar VMs com várias NICs usando a CLI do Azure no modelo clássico de implantação"
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
   ms.date="11/12/2015"
   ms.author="telmos" />

#Implantar VMs de várias NICs (clássico) usando a CLI do Azure

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-network-deploy-multinic-arm-cli.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Como, neste momento, você não pode ter VMs com uma única NIC e VMs com várias NICs no mesmo serviço de nuvem, implementará os servidores de back-end em um serviço de nuvem diferente e depois todos os outros componentes no cenário. As etapas a seguir usam um serviço de nuvem chamado *IaaSStory* para os principais recursos, e *back-end IaaSStory* para os servidores back-end.

## Pré-requisitos

Antes de implantar os servidores de back-end, você precisa implantar o serviço de nuvem principal com todos os recursos necessários para este cenário. No mínimo, você precisa criar uma rede virtual com uma sub-rede para o back-end. Visite [Criar uma rede virtual usando a CLI do Azure](virtual-networks-create-vnet-classic-cli.md) para saber como implantar uma rede virtual.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## Implantar VMs de back-end

As VMs de back-end dependem da criação dos recursos descritos abaixo.

- **Conta de armazenamento para discos de dados**. Para obter um melhor desempenho, os discos de dados dos servidores de banco de dados usam a tecnologia SDD (unidade de estado sólido), que requer uma conta de Armazenamento Premium. Verifique se o local do Azure no qual você vai implantar é compatível com o Armazenamento Premium.
- **NICs**. Cada VM tem duas NICs, uma para acesso ao banco de dados e outra para gerenciamento.
- **Conjunto de disponibilidade**. Todos os servidores de banco de dados são adicionados a um conjunto de disponibilidade único, para garantir que pelo menos uma das VMs está ativa e em execução durante a manutenção. 

### Etapa 1 – Iniciar o script

Você pode baixar o script bash completo usado [aqui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/multinic.sh). Realize os procedimentos abaixo para alterar o script para funcionar em seu ambiente.

1. Altere os valores das variáveis a seguir com base no grupo de recursos existente implantado acima, no tópico [Pré-requisitos](#Prerequisites).

		location="useast2"
		vnetName="WTestVNet"
		backendSubnetName="BackEnd"

2. Altere os valores das variáveis a seguir de acordo com os valores que deseja usar na implantação do back-end.

		backendCSName="IaaSStory-Backend"
		prmStorageAccountName="iaasstoryprmstorage"
		image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
		avSetName="ASDB"
		vmSize="Standard_DS3"
		diskSize=127
		vmNamePrefix="DB"
		osDiskName="osdiskdb"
		dataDiskPrefix="db"
		dataDiskName="datadisk"
		ipAddressPrefix="192.168.2."
		username='adminuser'
		password='adminP@ssw0rd'
		numberOfVMs=2

### Etapa 2 – Criar recursos necessários para as VMs

1. Crie um novo serviço de nuvem para todas as VMs de back-end. Observe o uso da variável `$backendCSName` para o nome do grupo de recursos e `$location` para a região do Azure.

		azure service create --serviceName $backendCSName \
		    --location $location

2. Crie uma conta de armazenamento Premium para o sistema operacional e discos de dados a ser usada por suas VMs.

		azure storage account create $prmStorageAccountName \
		    --location $location \
		    --type PLRS 

### Etapa 3 - Criar VMs com várias NICs

1. Inicie um loop para criar várias VMs, com base em variáveis `numberOfVMs`.

		for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
		do

2. Para cada VM, especifique o nome e o endereço IP de cada uma das duas NICs.

		    nic1Name=$vmNamePrefix$suffixNumber-DA
		    x=$((suffixNumber+3))
		    ipAddress1=$ipAddressPrefix$x
		
		    nic2Name=$vmNamePrefix$suffixNumber-RA
		    x=$((suffixNumber+53))
		    ipAddress2=$ipAddressPrefix$x

4. Crie a VM. Observe o uso do parâmetro `--nic-config`, que contém uma lista de todas as NICs com nome, sub-rede e endereço IP.

		    azure vm create $backendCSName $image $username $password \
		        --connect $backendCSName \
		        --vm-name $vmNamePrefix$suffixNumber \
		        --vm-size $vmSize \
		        --availability-set $avSetName \
		        --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
		        --virtual-network-name $vnetName \
		        --subnet-names $backendSubnetName \
		        --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::

5. Para cada VM, crie dois discos de dados.

		    azure vm disk attach-new $vmNamePrefix$suffixNumber \
		        $diskSize \
		        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd
		
		    azure vm disk attach-new $vmNamePrefix$suffixNumber \
		        $diskSize \
		        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
		done

### Etapa 4 – Executar o script

Agora que você baixou e alterou o script de acordo com suas necessidades, execute o script para criar VMs do banco de dados de back-end com várias NICs.

1. Salve seu script e execute-o em seu terminal **Bash**. A saída inicial será exibida, conforme mostrado abaixo:

		info:    Executing command service create
		info:    Creating cloud service
		data:    Cloud service name IaaSStory-Backend
		info:    service create command OK
		info:    Executing command storage account create
		info:    Creating storage account
		info:    storage account create command OK
		info:    Executing command vm create
		info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
		info:    Looking up virtual network
		info:    Looking up cloud service
		info:    Getting cloud service properties
		info:    Looking up deployment
		info:    Creating VM

2. Depois de alguns minutos, a execução será encerrada e você verá o restante da saída conforme mostrado abaixo.

		info:    OK
		info:    vm create command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK
		info:    Executing command vm create
		info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
		info:    Looking up virtual network
		info:    Looking up cloud service
		info:    Getting cloud service properties
		info:    Looking up deployment
		info:    Creating VM
		info:    OK
		info:    vm create command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK

<!---HONumber=Nov15_HO4-->