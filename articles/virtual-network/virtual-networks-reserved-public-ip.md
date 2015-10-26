<properties 
   pageTitle="IP Reservado"
   description="Noções básicas sobre IPs reservados, VIP, ILPIP e como gerenciá-los"
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

# Visão geral de IPs reservados
Os endereços IP no Azure recaem em duas categorias: dinâmicos e reservados. Os endereços IP públicos gerenciados pelo Azure são dinâmicos por padrão. Isso significa que o endereço IP usado para um determinado serviço de nuvem (VIP) ou para acessar uma VM ou instância de função diretamente (ILPIP) pode mudar de tempos em tempos, quando recursos forem desligados ou desalocados.

Para evitar que endereços IP sejam alterados, é possível reservar um endereço IP. Os IPs reservados podem ser usados apenas como um VIP, garantindo que o endereço IP do serviço de nuvem permaneça o mesmo, mesmo se os recursos forem desligados ou desalocados. Além disso, você pode converter IPs dinâmicos existentes usados como um VIP para um endereço IP reservado.

## Quando eu precisarei de um IP reservado?
- **Você deseja garantir que o IP seja reservado em sua assinatura**. Se você quiser reservar um endereço IP que não será liberado da sua assinatura sob nenhuma circunstância, deverá usar um IP reservado público.  
- **Você deseja que o IP permaneça com seu serviço de nuvem, mesmo nos estados parados ou desalocados (VMs)**. Se você quiser que seu serviço seja acessado usando um endereço IP que não será alterado mesmo quando VMs do serviço de nuvem estejam paradas ou desalocadas.
- **Você deseja garantir que o tráfego de saída do Azure use um endereço IP previsível**. Você pode ter seu firewall local configurado para permitir apenas o tráfego de endereços IP específicos. Ao reservar um IP, você conhecerá o endereço IP de origem e não terá de atualizar suas regras de firewall devido a uma alteração de IP.

## Perguntas frequentes
1. Posso usar um IP reservado para todos os serviços do Azure?  
  - Os IPs reservados só podem ser usados para VMs e funções de instância de serviço de nuvem.
1. Quantos IPs reservados eu posso ter?  
  - Neste momento, todas as assinaturas do Azure estão autorizadas a usar 20 IPs reservados. No entanto, você pode solicitar IPs reservados adicionais. Veja a página [Limites de assinatura e de serviço](../azure-subscription-service-limits/) para obter mais informações.
1. Há uma cobrança para IPs reservados? 
  - Veja [Detalhes sobre preços de endereços IP reservados](http://go.microsoft.com/fwlink/?LinkID=398482) para obter informações sobre preços.
1. Como eu reservo um endereço IP? 
  - Você pode usar o PowerShell ou a [API REST de Gerenciamento do Azure](https://msdn.microsoft.com/library/azure/dn722420.aspx) para solicitar um IP reservado de uma região específica. O Azure reservará um endereço IP dessa região e fará a correlação com a sua assinatura. Você então poderá usar o IP reservado nessa região. Não é possível reservar um endereço IP usando o Portal de Gerenciamento.
1. Posso usar isso com redes virtuais baseadas em grupos de afinidade? 
  - Os IPs reservados têm suporte apenas em redes virtuais regionais. Eles não têm suporte para redes virtuais associadas a grupos de afinidade. Para obter mais informações sobre a associação de uma rede virtual a uma região ou a um grupo de afinidade, veja [Sobre redes virtuais regionais e grupos de afinidade](virtual-networks-migrate-to-regional-vnet.md). 

## Como gerenciar VIPs reservados

Antes de poder usar IPs reservados, você deverá adicioná-los à sua assinatura. Para criar um IP reservado do pool de endereços IP públicos disponíveis no local *EUA Central*, execute o seguinte comando do PowerShell:

	New-AzureReservedIP –ReservedIPName MyReservedIP –Location “Central US”

Observe, entretanto, que você não pode especificar qual IP está sendo reservado. Para exibir quais endereços IP estão reservados em sua assinatura, execute o seguinte comando do PowerShell e observe os valores de *ReservedIPName* e *Endereço*:

	Get-AzureReservedIP

	ReservedIPName       : MyReservedIP
	Address              : 23.101.114.211
	Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
	Label                : 
	Location             : Central US
	State                : Created
	InUse                : False
	ServiceName          : 
	DeploymentName       : 
	OperationDescription : Get-AzureReservedIP
	OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
	OperationStatus      : Succeeded

Depois que um IP for reservado, ele permanecerá associado à sua assinatura até você excluí-lo. Para excluir o IP reservado mostrado acima, execute o seguinte comando do PowerShell:

	Remove-AzureReservedIP -ReservedIPName "MyReservedIP"

## Como associar um IP reservado a um novo serviço de nuvem
O script a seguir cria um novo IP reservado e o associa a um novo serviço de nuvem chamado *TestService*.

	New-AzureReservedIP –ReservedIPName MyReservedIP –Location “Central US”
	$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
	New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
	| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"

>[AZURE.NOTE]Quando você cria um IP reservado a ser usado com um serviço de nuvem, você ainda precisará fazer referência à VM usando *VIP:&lt;número de porta>* para comunicação de entrada. Reservar um IP não significa que você pode conectar-se à VM diretamente. O IP reservado é atribuído ao serviço de nuvem no qual a VM foi implantada. Se você quiser se conectar diretamente a uma VM por IP, precisará configurar um IP público em nível de instância. Um IP público em nível de instância é um tipo de IP público (chamado de ILPIP) atribuído diretamente à sua VM. Ele não pode ser reservado. Veja [IP Público em Nível de Instância (ILPIP)](../virtual-networks-instance-level-public-ip) para obter mais informações.

## Como remover um IP reservado de uma implantação em execução
Para remover o IP reservado adicionado ao novo serviço criado no script acima, execute o seguinte comando do PowerShell:

	Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService

>[AZURE.NOTE]Removendo um IP reservado de uma implantação em execução não remove a reserva de sua assinatura. Ele simplesmente libera o IP para ser usado por outro recurso em sua assinatura.

## Como associar um IP reservado a uma implantação em execução
O script abaixo cria um novo serviço de nuvem chamado *TestService2* com uma nova VM denominada *TestVM2*, e então associa o IP reservado existente chamado *MyReservedIP* ao serviço de nuvem.

	$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
	New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
	| New-AzureVM -ServiceName TestService2 -Location "Central US"
	Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2

## Como associar um IP reservado a um serviço de nuvem usando um arquivo de configuração de serviço
Você também pode associar um IP reservado a um serviço de nuvem usando um arquivo de configuração de serviço (CSCFG). O xml de exemplo abaixo mostra como configurar um serviço de nuvem para usar um VIP reservado denominado *MyReservedIP*:
	
	<?xml version="1.0" encoding="utf-8"?>
	<ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
	  <Role name="WebRole1">
	    <Instances count="1" />
	    <ConfigurationSettings>
	      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
	    </ConfigurationSettings>
	  </Role>
	  <NetworkConfiguration>
	    <AddressAssignments>
	      <ReservedIPs>
	       <ReservedIP name="MyReservedIP"/>
	      </ReservedIPs>
	    </AddressAssignments>
	  </NetworkConfiguration>
	</ServiceConfiguration>

## Próximas etapas

- Saiba mais sobre [endereços IP privados reservados](../virtual-networks-reserved-private-ip).

- Saiba mais sobre [endereços ILPIP (IP Público de Nível de Instância)](../virtual-networks-instance-level-public-ip).

- Verifique as [APIs REST do IP reservado](https://msdn.microsoft.com/library/azure/dn722420.aspx).

<!---HONumber=Oct15_HO3-->