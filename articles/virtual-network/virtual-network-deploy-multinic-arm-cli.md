<properties
   pageTitle="Implantar VMs com várias NICs usando a CLI do Azure no Gerenciador de Recursos | Microsoft Azure"
   description="Saiba como implantar VMs com várias NICs usando a CLI do Azure no Gerenciador de Recursos"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="telmos" />

#Implantar VMs de várias NICs usando a CLI do Azure

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-network-deploy-multinic-classic-cli.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

No momento, você não pode ter VMs com uma única NIC e VMs com várias NICs no mesmo grupo de recursos. Portanto, você precisa implementar servidores back-end em um grupo de recursos diferente de todos os outros componentes. As etapas a seguir usam um grupo de recursos chamado *IaaSStory* para o grupo de recursos principal e *IaaSStory-BackEnd* para os servidores de back-end.

## Pré-requisitos

Antes de implantar os servidor de back-end, você deve implantar o grupo de recursos principal com todos os recursos necessários para este cenário. Para implantar esses recursos, faça o seguinte:

1. Navegue até [a página do modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Na página do modelo, à direita do **Grupo de recursos pai**, clique em **Implantar no Azure**.
3. Caso necessário, altere os valores de parâmetro e siga as etapas no Portal de visualização do Azure para implantar o grupo de recursos.

> [AZURE.IMPORTANT] Verifique se os nomes da conta de armazenamento são exclusivos. Você não pode ter nomes de conta de armazenamento duplicados no Azure.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## Implantar VMs de back-end

As VMs de back-end dependem da criação dos recursos descritos abaixo.

- **Conta de armazenamento para discos de dados**. Para obter um melhor desempenho, os discos de dados dos servidores de banco de dados usam a tecnologia SDD (unidade de estado sólido), que requer uma conta de Armazenamento Premium. Verifique se o local do Azure no qual você vai implantar é compatível com o Armazenamento Premium.
- **NICs**. Cada VM tem duas NICs, uma para acesso ao banco de dados e outra para gerenciamento.
- **Conjunto de disponibilidade**. Todos os servidores de banco de dados são adicionados a um conjunto de disponibilidade único, para garantir que pelo menos uma das VMs está ativa e em execução durante a manutenção.

### Etapa 1 – Iniciar o script

Você pode baixar o script bash completo usado [aqui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/arm/virtual-network-deploy-multinic-arm-cli.sh). Realize os procedimentos abaixo para alterar o script para funcionar em seu ambiente.

1. Altere os valores das variáveis a seguir com base no grupo de recursos existente implantado acima, no tópico [Pré-requisitos](#Prerequisites).

		existingRGName="IaaSStory"
		location="westus"
		vnetName="WTestVNet"
		backendSubnetName="BackEnd"
		remoteAccessNSGName="NSG-RemoteAccess"

2. Altere os valores das variáveis a seguir de acordo com os valores que deseja usar na implantação do back-end.

		backendRGName="IaaSStory-Backend"
		prmStorageAccountName="wtestvnetstorageprm"
		avSetName="ASDB"
		vmSize="Standard_DS3"
		diskSize=127
		publisher="Canonical"
		offer="UbuntuServer"
		sku="14.04.2-LTS"
		version="latest"
		vmNamePrefix="DB"
		osDiskName="osdiskdb"
		dataDiskName="datadisk"
		nicNamePrefix="NICDB"
		ipAddressPrefix="192.168.2."
		username='adminuser'
		password='adminP@ssw0rd'
		numberOfVMs=2

3. Recuperar a ID para a sub-rede `BackEnd` onde as VMs serão criadas. Você precisa fazer isso, pois as NICs a serem associadas a esta sub-rede estão em um grupo de recursos diferente.

		subnetId="$(azure network vnet subnet show --resource-group $existingRGName \
		                --vnet-name $vnetName \
		                --name $backendSubnetName|grep Id)"
		subnetId=${subnetId#*/}

>[AZURE.TIP] O primeiro comando acima usa [grep](http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_04_02.html) e [manipulação de cadeia de caracteres](http://tldp.org/LDP/abs/html/string-manipulation.html) (mais especificamente, a remoção de subcadeia de caracteres).

4. Recuperar a ID para o NSG `NSG-RemoteAccess`. Você precisa fazer isso, pois as NICs a serem associadas a este NSG estão em um grupo de recursos diferente.

		nsgId="$(azure network nsg show --resource-group $existingRGName \
		                --name $remoteAccessNSGName|grep Id)"
		nsgId=${nsgId#*/}

### Etapa 2 – Criar recursos necessários para as VMs

1. Crie um novo grupo de recursos para todos os recursos de back-end. Observe o uso da variável `$backendRGName` para o nome do grupo de recursos e `$location` para a região do Azure.

		azure group create $backendRGName $location

2. Crie uma conta de armazenamento Premium para o sistema operacional e discos de dados a ser usada por suas VMs.

		azure storage account create $prmStorageAccountName \
		    --resource-group $backendRGName \
		    --location $location \
			--type PLRS

3. Criar um conjunto de disponibilidade para as VMs.

		azure availset create --resource-group $backendRGName \
		    --location $location \
		    --name $avSetName

### Etapa 3 - criar as NICs e as VMs de back-end

1. Inicie um loop para criar várias VMs, com base em variáveis `numberOfVMs`.

		for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
		do

2. Para cada VM, crie uma NIC para acesso ao banco de dados.

		    nic1Name=$nicNamePrefix$suffixNumber-DA
		    x=$((suffixNumber+3))
		    ipAddress1=$ipAddressPrefix$x
		    azure network nic create --name $nic1Name \
		        --resource-group $backendRGName \
		        --location $location \
		        --private-ip-address $ipAddress1 \
		        --subnet-id $subnetId

3. Para cada VM, crie uma NIC para acesso remoto. Observe que o parâmetro `--network-security-group`, usado para associar a NIC a um NSG.

		    nic2Name=$nicNamePrefix$suffixNumber-RA
		    x=$((suffixNumber+53))
		    ipAddress2=$ipAddressPrefix$x
		    azure network nic create --name $nic2Name \
		        --resource-group $backendRGName \
		        --location $location \
		        --private-ip-address $ipAddress2 \
		        --subnet-id $subnetId $vnetName \
		        --network-security-group-id $nsgId

4. Crie a VM.

		    azure vm create --resource-group $backendRGName \
		        --name $vmNamePrefix$suffixNumber \
		        --location $location \
		        --vm-size $vmSize \
		        --subnet-id $subnetId \
		        --availset-name $avSetName \
		        --nic-names $nic1Name,$nic2Name \
		        --os-type linux \
		        --image-urn $publisher:$offer:$sku:$version \
		        --storage-account-name $prmStorageAccountName \
		        --storage-account-container-name vhds \
		        --os-disk-vhd $osDiskName$suffixNumber.vhd \
		        --admin-username $username \
		        --admin-password $password

5. Para cada VM, criar dois discos de dados e encerre o loop com o comando `done`.

		    azure vm disk attach-new --resource-group $backendRGName \
		        --vm-name $vmNamePrefix$suffixNumber \        
		        --storage-account-name $prmStorageAccountName \
		        --storage-account-container-name vhds \
		        --vhd-name $dataDiskName$suffixNumber-1.vhd \
		        --size-in-gb $diskSize \
		        --lun 0

		    azure vm disk attach-new --resource-group $backendRGName \
		        --vm-name $vmNamePrefix$suffixNumber \        
		        --storage-account-name $prmStorageAccountName \
		        --storage-account-container-name vhds \
		        --vhd-name $dataDiskName$suffixNumber-2.vhd \
		        --size-in-gb $diskSize \
		        --lun 1
		done


### Etapa 4 – Executar o script

Agora que você baixou e alterou o script de acordo com suas necessidades, execute o script para criar VMs do banco de dados de back-end com várias NICs.

1. Salve seu script e execute-o em seu terminal **Bash**. A saída inicial será exibida, conforme mostrado abaixo:

		info:    Executing command group create
		info:    Getting resource group IaaSStory-Backend
		info:    Creating resource group IaaSStory-Backend
		info:    Created resource group IaaSStory-Backend
		data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
		data:    Name:                IaaSStory-Backend
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:
		info:    group create command OK
		info:    Executing command storage account create
		info:    Creating storage account
		info:    storage account create command OK
		info:    Executing command availset create
		info:    Looking up the availability set "ASDB"
		info:    Creating availability set "ASDB"
		info:    availset create command OK
		info:    Executing command network nic create
		info:    Looking up the network interface "NICDB1-DA"
		info:    Creating network interface "NICDB1-DA"
		info:    Looking up the network interface "NICDB1-DA"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/Microsoft.Network/networkInterfaces/NICDB1-DA
		data:    Name                            : NICDB1-DA
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Enable IP forwarding            : false
		data:    IP configurations:
		data:      Name                          : NIC-config
		data:      Provisioning state            : Succeeded
		data:      Private IP address            : 192.168.2.4
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/BackEnd
		data:
		info:    network nic create command OK
		info:    Executing command network nic create
		info:    Looking up the network interface "NICDB1-RA"
		info:    Creating network interface "NICDB1-RA"
		info:    Looking up the network interface "NICDB1-RA"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/Microsoft.Network/networkInterfaces/NICDB1-RA
		data:    Name                            : NICDB1-RA
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Enable IP forwarding            : false
		data:    Network security group          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/networkSecurityGroups/NSG-RemoteAccess
		data:    IP configurations:
		data:      Name                          : NIC-config
		data:      Provisioning state            : Succeeded
		data:      Private IP address            : 192.168.2.54
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/BackEnd
		data:
		info:    network nic create command OK
		info:    Executing command vm create
		info:    Looking up the VM "DB1"
		info:    Using the VM Size "Standard_DS3"
		info:    The [OS, Data] Disk or image configuration requires storage account
		info:    Looking up the storage account wtestvnetstorageprm
		info:    Looking up the availability set "ASDB"
		info:    Found an Availability set "ASDB"
		info:    Looking up the NIC "NICDB1-DA"
		info:    Looking up the NIC "NICDB1-RA"
		info:    Creating VM "DB1"

2. Depois de alguns minutos, a execução será encerrada e você verá o restante da saída conforme mostrado abaixo.

		info:    vm create command OK
		info:    Executing command vm disk attach-new
		info:    Looking up the VM "DB1"
		info:    Looking up the storage account wtestvnetstorageprm
		info:    New data disk location: https://wtestvnetstorageprm.blob.core.windows.net/vhds/datadisk1-1.vhd
		info:    Updating VM "DB1"
		info:    vm disk attach-new command OK
		info:    Executing command vm disk attach-new
		info:    Looking up the VM "DB1"
		info:    Looking up the storage account wtestvnetstorageprm
		info:    New data disk location: https://wtestvnetstorageprm.blob.core.windows.net/vhds/datadisk1-2.vhd
		info:    Updating VM "DB1"
		info:    vm disk attach-new command OK
		info:    Executing command network nic create
		info:    Looking up the network interface "NICDB2-DA"
		info:    Creating network interface "NICDB2-DA"
		info:    Looking up the network interface "NICDB2-DA"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/Microsoft.Network/networkInterfaces/NICDB2-DA
		data:    Name                            : NICDB2-DA
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Enable IP forwarding            : false
		data:    IP configurations:
		data:      Name                          : NIC-config
		data:      Provisioning state            : Succeeded
		data:      Private IP address            : 192.168.2.5
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/BackEnd
		data:
		info:    network nic create command OK
		info:    Executing command network nic create
		info:    Looking up the network interface "NICDB2-RA"
		info:    Creating network interface "NICDB2-RA"
		info:    Looking up the network interface "NICDB2-RA"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/Microsoft.Network/networkInterfaces/NICDB2-RA
		data:    Name                            : NICDB2-RA
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Enable IP forwarding            : false
		data:    Network security group          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/networkSecurityGroups/NSG-RemoteAccess
		data:    IP configurations:
		data:      Name                          : NIC-config
		data:      Provisioning state            : Succeeded
		data:      Private IP address            : 192.168.2.55
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/BackEnd
		data:
		info:    network nic create command OK
		info:    Executing command vm create
		info:    Looking up the VM "DB2"
		info:    Using the VM Size "Standard_DS3"
		info:    The [OS, Data] Disk or image configuration requires storage account
		info:    Looking up the storage account wtestvnetstorageprm
		info:    Looking up the availability set "ASDB"
		info:    Found an Availability set "ASDB"
		info:    Looking up the NIC "NICDB2-DA"
		info:    Looking up the NIC "NICDB2-RA"
		info:    Creating VM "DB2"
		info:    vm create command OK
		info:    Executing command vm disk attach-new
		info:    Looking up the VM "DB2"
		info:    Looking up the storage account wtestvnetstorageprm
		info:    New data disk location: https://wtestvnetstorageprm.blob.core.windows.net/vhds/datadisk2-1.vhd
		info:    Updating VM "DB2"
		info:    vm disk attach-new command OK
		info:    Executing command vm disk attach-new
		info:    Looking up the VM "DB2"
		info:    Looking up the storage account wtestvnetstorageprm
		info:    New data disk location: https://wtestvnetstorageprm.blob.core.windows.net/vhds/datadisk2-2.vhd
		info:    Updating VM "DB2"
		info:    vm disk attach-new command OK

<!---HONumber=AcomDC_0413_2016-->