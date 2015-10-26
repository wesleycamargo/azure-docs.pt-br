<properties 
   pageTitle="Como definir um IP interno estático privado"
   description="Noções básicas sobre IPs estáticos internos (DIPs) e como gerenciá-los"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/17/2015"
   ms.author="telmos" />

# Como definir um IP interno estático privado
Na maioria dos casos, você não precisará especificar um endereço IP interno estático para sua máquina virtual. As VMs de uma rede virtual receberão automaticamente endereço IP interno de um intervalo especificado por você. Mas, em alguns casos, a especificação de um endereço IP estático para uma determinada VM fará sentido. Por exemplo, se a sua VM se destinar à execução de DNS ou a ser um controlador de domínio.

>[AZURE.NOTE]Um endereço IP interno estático permanece com a VM mesmo em um estado de interrupção/desprovisionamento.

## Você pode verificar se um endereço IP específico está disponível
Para verificar se o endereço IP *10.0.0.7* está disponível em uma rede virtual *TestVnet*, execute o seguinte comando do PowerShell e verifique o valor de *IsAvailable*:

	Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

	IsAvailable          : True
	AvailableAddresses   : {}
	OperationDescription : Test-AzureStaticVNetIP
	OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
	OperationStatus      : Succeeded

>[AZURE.NOTE]Se você quiser testar o comando acima em um ambiente seguro, siga as diretrizes de [Criar uma rede Virtual](../virtual-network/virtual-networks-create-vnet.md) para criar uma rede virtual denominada *TestVnet* e verifique se ela usa o espaço de endereço *10.0.0.0/8*.

## Como especificar um endereço IP interno estático ao criar uma máquina virtual
O script do PowerShell abaixo cria um novo serviço de nuvem chamado *TestService* e, em seguida, recupera uma imagem do Azure e cria uma VM denominada *TestVM* no novo serviço de nuvem usando a imagem recuperada, define a VM em uma sub-rede denominada *Subnet-1* e define *10.0.0.7* como um endereço IP interno estático para a VM:

	New-AzureService -ServiceName TestService -Location "Central US"
	$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
	New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
	| Set-AzureSubnet –SubnetNames Subnet-1 `
	| Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
	| New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## Como recuperar informações de IP interno estático para uma VM
Para exibir as informações do IP interno estático da VM criado com o script acima, execute o seguinte comando do PowerShell e observe os valores de *IpAddress*:

	Get-AzureVM -Name TestVM -ServiceName TestService

	DeploymentName              : TestService
	Name                        : TestVM
	Label                       : 
	VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
	InstanceStatus              : Provisioning
	IpAddress                   : 10.0.0.7
	InstanceStateDetails        : Windows is preparing your computer for first use...
	PowerState                  : Started
	InstanceErrorCode           : 
	InstanceFaultDomain         : 0
	InstanceName                : TestVM
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

## Como remover um endereço IP interno estático de uma VM
Para remover o IP interno estático adicionado à VM no script acima, execute o seguinte comando do PowerShell:
	
	Get-AzureVM -ServiceName TestService -Name TestVM `
	| Remove-AzureStaticVNetIP `
	| Update-AzureVM

## Como adicionar um IP interno estático a uma VM existente
Para adicionar um IP interno estático à VM criada usando o script acima, execute o comando a seguir:

	Get-AzureVM -ServiceName TestService000 -Name TestVM `
	| Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
	| Update-AzureVM

## Próximas etapas

[IP Reservado](../virtual-networks-reserved-public-ip)

[IP Público em Nível de Instância (ILPIP)](../virtual-networks-instance-level-public-ip)

[APIs REST com IP Reservado](https://msdn.microsoft.com/library/azure/dn722420.aspx)
 

<!---HONumber=Oct15_HO3-->