<properties 
   pageTitle="Como definir um IP estático e privado no modo clássico usando o PowerShell| Microsoft Azure"
   description="Noções básicas sobre IPs privados e estáticos (DIPs) e como gerenciá-los no modo clássico usando o PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/08/2015"
   ms.author="telmos" />

# Como definir um endereço IP privado estático (clássico) no PowerShell

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo aborda o modelo de implantação clássico. Você também pode [gerenciar um endereço IP privado estático no modelo de implantação do Gerenciador de Recursos](virtual-networks-static-private-ip-arm-ps).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Os exemplos de comando abaixo do PowerShell esperam um ambiente simples já criado. Se você quiser executar os comandos da forma como eles aparecem neste documento, primeiro crie o ambiente de teste descrito em [criar uma rede virtual](virtual-networks-create-vnet-classic-netcfg-ps.md).

## Você pode verificar se um endereço IP específico está disponível
Para verificar se o endereço IP *192.168.1.101* está disponível em uma rede virtual *TestVnet*, execute o seguinte comando do PowerShell e verifique o valor de *IsAvailable*:

	Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 192.168.1.101 

Saída esperada:

	IsAvailable          : True
	AvailableAddresses   : {}
	OperationDescription : Test-AzureStaticVNetIP
	OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
	OperationStatus      : Succeeded

## Como especificar um endereço IP privado estático ao criar uma VM
O script do PowerShell abaixo cria um novo serviço de nuvem chamado *TestService* e, em seguida, recupera uma imagem do Azure e cria uma VM denominada *DNS01* no novo serviço de nuvem usando a imagem recuperada, define a VM em uma sub-rede denominada *FrontEnd* e define *192.168.1.7* como um endereço IP privado estático para a VM:

	New-AzureService -ServiceName TestService -Location "Central US"
	$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
	New-AzureVMConfig -Name DNS01 -InstanceSize Small -ImageName $image.ImageName `
	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
	| Set-AzureSubnet –SubnetNames FrontEnd `
	| Set-AzureStaticVNetIP -IPAddress 192.168.1.7 `
	| New-AzureVM -ServiceName "TestService" –VNetName TestVNet

Saída esperada:

	WARNING: No deployment found in service: 'TestService'.
	OperationDescription OperationId                          OperationStatus
	-------------------- -----------                          ---------------
	New-AzureService     fcf705f1-d902-011c-95c7-b690735e7412 Succeeded      
	New-AzureVM          3b99a86d-84f8-04e5-888e-b6fc3c73c4b9 Succeeded  

## Como recuperar informações do endereço IP privado estático de uma VM
Para exibir as informações do IP privado estático da VM criada com o script acima, execute o seguinte comando do PowerShell e observe os valores de *IpAddress*:

	Get-AzureVM -Name DNS01 -ServiceName TestService

Saída esperada:

	DeploymentName              : TestService
	Name                        : DNS01
	Label                       : 
	VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
	InstanceStatus              : Provisioning
	IpAddress                   : 192.168.1.7
	InstanceStateDetails        : Windows is preparing your computer for first use...
	PowerState                  : Started
	InstanceErrorCode           : 
	InstanceFaultDomain         : 0
	InstanceName                : DNS01
	InstanceUpgradeDomain       : 0
	InstanceSize                : Small
	HostName                    : rsR2-797
	AvailabilitySetName         : 
	DNSName                     : http://testservice000.cloudapp.net/
	Status                      : Provisioning
	GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
	ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
	PublicIPAddress             : 
	PublicIPName                : 
	NetworkInterfaces           : {}
	ServiceName                 : TestService
	OperationDescription        : Get-AzureVM
	OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
	OperationStatus             : OK

## Como remover o endereço IP privado estático de uma VM
Para remover o endereço IP estático e privado adicionado à VM no script acima, execute o seguinte comando do PowerShell:
	
	Get-AzureVM -ServiceName TestService -Name DNS01 `
	| Remove-AzureStaticVNetIP `
	| Update-AzureVM

Saída esperada:

	OperationDescription OperationId                          OperationStatus
	-------------------- -----------                          ---------------
	Update-AzureVM       052fa6f6-1483-0ede-a7bf-14f91f805483 Succeeded

## Como adicionar um endereço IP privado estático a uma VM existente
Para adicionar um IP privado estático à VM criada com o script acima, execute o comando a seguir:

	Get-AzureVM -ServiceName TestService -Name DNS01 `
	| Set-AzureStaticVNetIP -IPAddress 192.168.1.7 `
	| Update-AzureVM

Saída esperada:

	OperationDescription OperationId                          OperationStatus
	-------------------- -----------                          ---------------
	Update-AzureVM       77d8cae2-87e6-0ead-9738-7c7dae9810cb Succeeded 

## Próximas etapas

- Saiba mais sobre endereços [IP públicos reservados](../virtual-networks-reserved-public-ip).
- Saiba mais sobre endereços [ILPIP (IP público em nível de instância)](../virtual-networks-instance-level-public-ip).
- Consulte as [APIs REST de IP reservado](https://msdn.microsoft.com/library/azure/dn722420.aspx).

<!---HONumber=Oct15_HO3-->