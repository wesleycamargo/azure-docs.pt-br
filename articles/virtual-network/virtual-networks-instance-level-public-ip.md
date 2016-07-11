<properties 
   pageTitle="IP Público em Nível de Instância (ILPIP) |e Microsoft Azure"
   description="Noções básicas sobre ILPIP (PIP) e como gerenciá-los"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/10/2016"
   ms.author="telmos" />

# Visão geral de IP público em nível de instância
Um ILPIP (IP público em nível de instância) é um endereço IP público que você pode atribuir diretamente à sua VM ou instância da função, e não ao serviço de nuvem no qual a sua VM ou instância de função reside. Isso não substitui o VIP (IP virtual) que é atribuído ao serviço de nuvem. Ao contrário, é um endereço IP adicional que você pode usar para se conectar diretamente à sua VM ou instância de função.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] Saiba como [executar estas etapas usando o modelo do Resource Manager](virtual-network-ip-addresses-overview-arm.md).

Certifique-se de que você entenda como os [endereços IP](virtual-network-ip-addresses-overview-classic.md) funcionam no Azure.

>[AZURE.NOTE] No passado, o ILPIP era conhecido como PIP, que significa IP público.

![Diferença entre ILPIP e VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Conforme mostrado na Figura 1, o serviço de nuvem é acessado por meio de um VIP, enquanto as VMs individuais são normalmente acessadas usando VIP:& lt;número da porta&gt;. Ao atribuir um ILPIP a uma VM específica, essa VM pode ser acessada diretamente por meio desse endereço IP.

Quando você cria um serviço de nuvem no Azure, registros de DNS A correspondentes são criados automaticamente para permitir o acesso ao serviço por meio de um nome de domínio totalmente qualificado (FQDN) em vez de usar o VIP real. O mesmo processo ocorre para ILPIP, permitindo o acesso à VM ou instância de função pelo FQDN em vez de pelo ILPIP. Por exemplo, se você criar um serviço de nuvem chamado *contosoadservice*, e configurar uma função web chamada *contosoweb* com duas instâncias, o Azure registrará os seguintes registros para as instâncias:

- contosoweb\_IN\_0.contosoadservice.cloudapp.net
- contosoweb\_IN\_1.contosoadservice.cloudapp.net

>[AZURE.NOTE] Você pode atribuir apenas um ILPIP para cada VM ou instância de função. Você pode usar até 5 ILPIPs por assinatura. Neste momento, não há suporte de ILPIP para VMs de várias NICs.

## Por que eu deveria solicitar um ILPIP?
Se você quiser ser capaz de conectar a sua VM ou instância de função por um endereço IP atribuído diretamente a ela, em vez de usar o <número da porta> do VIP de serviço de nuvem, solicite um ILPIP para sua VM ou instância de função.
- **FTP passivo** - por ter um ILPIP na sua VM, você pode receber tráfego em praticamente qualquer porta e não precisará abrir um ponto de extremidade para receber o tráfego. Isso possibilita cenários como o FTP passivo, em que as portas são escolhidas dinamicamente.
- **IP de saída** - O tráfego de saída proveniente da VM sai com o ILPIP como a origem, o que identifica exclusivamente a VM para entidades externas.

## Como solicitar um ILPIP durante a criação da VM
O script do PowerShell abaixo cria um novo serviço de nuvem chamado *FTPService*; em seguida, recupera uma imagem do Azure e cria uma VM denominada *FTPInstance* usando a imagem recuperada, define a VM para usar um ILPIP e adiciona a VM ao novo serviço:

	New-AzureService -ServiceName FTPService -Location "Central US"
	$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
	New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
	| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"

## Como recuperar informações de ILPIP para uma máquina virtual
Para exibir as informações de ILPIP da VM criada com o script acima, execute o seguinte comando do PowerShell e observe os valores de *PublicIPAddress* e *PublicIPName*:

	Get-AzureVM -Name FTPInstance -ServiceName FTPService

	DeploymentName              : FTPService
	Name                        : FTPInstance
	Label                       : 
	VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
	InstanceStatus              : ReadyRole
	IpAddress                   : 100.74.118.91
	InstanceStateDetails        : 
	PowerState                  : Started
	InstanceErrorCode           : 
	InstanceFaultDomain         : 0
	InstanceName                : FTPInstance
	InstanceUpgradeDomain       : 0
	InstanceSize                : Small
	HostName                    : FTPInstance
	AvailabilitySetName         : 
	DNSName                     : http://ftpservice888.cloudapp.net/
	Status                      : ReadyRole
	GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
	ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
	PublicIPAddress             : 104.43.142.188
	PublicIPName                : ftpip
	NetworkInterfaces           : {}
	ServiceName                 : FTPService
	OperationDescription        : Get-AzureVM
	OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
	OperationStatus             : OK

## Como remover um ILPIP de uma máquina virtual
Para remover o ILPIP adicionado à VM no script anterior, execute o seguinte comando do PowerShell:
	
	Get-AzureVM -ServiceName FTPService -Name FTPInstance `
	| Remove-AzurePublicIP `
	| Update-AzureVM

## Como adicionar um ILPIP a uma VM existente
Para adicionar um ILPIP à VM criada usando o script anterior, execute o comando a seguir:

	Get-AzureVM -ServiceName FTPService -Name FTPInstance `
	| Set-AzurePublicIP -PublicIPName ftpip2 `
	| Update-AzureVM

## Como associar um ILPIP a uma VM usando um arquivo de configuração de serviço
Também é possível associar um ILPIP a uma máquina virtual usando um arquivo de configuração de serviço (CSCFG). O XML de exemplo a seguir mostra como configurar um serviço de nuvem para usar um ILPIP denominado *MyPublicIP* para uma instância de função:
	
	<?xml version="1.0" encoding="utf-8"?>
	<ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
	  <Role name="WebRole1">
	    <Instances count="1" />
	    <ConfigurationSettings>
	      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
	    </ConfigurationSettings>
	  </Role>
      <NetworkConfiguration>
	    <VirtualNetworkSite name="VNet"/>
	    <AddressAssignments>
	      <InstanceAddress roleName="VMRolePersisted">
	        <Subnets>
	          <Subnet name="Subnet1"/>
	          <Subnet name="Subnet2"/>
	        </Subnets>
	        <PublicIPs>
	          <PublicIP name="MyPublicIP" domainNameLabel="MyPublicIP" />
	        </PublicIPs>
	      </InstanceAddress>
	    </AddressAssignments>
	  </NetworkConfiguration>
	</ServiceConfiguration>

## Próximas etapas

- Entenda como o [endereçamento IP](virtual-network-ip-addresses-overview-classic.md) funciona no modelo de implantação clássica.

- Saiba mais sobre [IPs reservados](virtual-networks-reserved-public-ip.md).
 

<!---HONumber=AcomDC_0629_2016-->