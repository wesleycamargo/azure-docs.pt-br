<properties 
   pageTitle="Criar uma máquina virtual com várias NICs"
   description="Como criar VMs com várias NICs"
   services="virtual-network, virtual-machines"
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
   ms.date="04/30/2015"
   ms.author="telmos" />

# Criar uma máquina virtual com várias NICs

O recurso de várias NICs permite criar e gerenciar várias placas de interface de rede virtual (NICs) nas máquinas virtuais (VMs) do Azure. Várias NICs é um requisito para muitos aplicativos virtuais de rede, como soluções de distribuição de aplicativos e de otimização de WAN. Ter várias NICs também agrega mais funcionalidade de gerenciamento tráfego da rede, incluindo isolamento do tráfego entre uma NIC de front-end e as NICs de back-end ou a separação do tráfego do plano de dados do tráfego do plano de gerenciamento.

![Várias NICs da VM](./media/virtual-networks-multiple-nics/IC757773.png)

A figura acima mostra uma VM com três NICs, cada uma conectada a uma sub-rede diferente.

## Limites e restrições

Neste momento, o recurso de várias NICs tem os seguintes requisitos e restrições:

- VMs de várias NICs devem ser criadas nas redes virtuais do Azure (VNets). Não há suporte para as VMs não VNet. 
- Dentro de um único serviço de nuvem, são permitidas apenas as seguintes configurações: 
	- Todas as VMs daquele serviço de nuvem devem ser habilitadas para várias NICs ou 
	- Todas as VMs daquele serviço de nuvem devem ter uma única NIC cada uma 

>[AZURE.IMPORTANT]Se você tentar adicionar uma VM de várias NICs a uma implantação (serviço de nuvem) que já contenha uma VM de única NIC (ou vice-versa), receberá o seguinte erro: Não há suporte para máquinas virtuais com interface de rede secundária e máquinas virtuais sem interface de rede secundária na mesma implantação; além disso, uma máquina virtual que não tenha interface de rede secundária não pode ser atualizada para ter interfaces de rede secundárias e vice-versa.
 
- O VIP para Internet só é suportado na NIC "padrão". Há apenas um VIP para o IP da NIC padrão. 
- Neste momento, não há suporte para endereços IP públicos no nível de instância para VMs de várias NICs. 
- A ordem das NICs de dentro da VM será aleatória e também pode ser alterada nas atualizações da infraestrutura do Azure. No entanto, os endereços IP e os endereços ethernet MAC correspondentes permanecerão os mesmos. Por exemplo, suponha que **Eth1** tenha o endereço IP 10.1.0.100 e um endereço MAC 00-0D-3A-B0-39-0D; após uma atualização de infraestrutura e reinicialização do Azure, ela pode ser alterada para Eth2, mas o emparelhamento entre IP e MAC permanecerá o mesmo. Quando a reinicialização for iniciada pelo cliente, a ordem das NICs permanecerá a mesma. 
- O endereço de cada NIC em cada máquina virtual deve estar localizado em uma sub-rede, várias NICs em uma única VM podem, cada uma, receber a atribuição de endereços que estejam na mesma sub-rede. 
- O tamanho da VM determina o número de NICS que você pode criar para uma máquina virtual. A tabela a seguir lista os números de NICs correspondentes ao tamanho das máquinas virtuais: 

|Tamanho da VM (SKUs padrão)|NICs (máx. permitido por VM)|
|---|---|
|Todos os tamanhos básicos|1|
|A0\extrapequeno|1|
|A1\pequeno|1|
|A2\médio|1|
|A3\grande|2|
|A4\extragrande|4|
|A5|1|
|A6|2|
|A7|4|
|A8|2|
|A9|4|
|A10|2|
|A11|4|
|D1|1|
|D2|2|
|D3|4|
|D4|8|
|D11|2|
|D12|4|
|D13|8|
|D14|16|
|DS1|1|
|DS2|2|
|DS3|4|
|DS4|8|
|DS11|2|
|DS12|4|
|DS13|8|
|DS14|16|
|G1|1|
|G2|2|
|G3|4|
|G4|8|
|G5|16|
|Todos os outros tamanhos|1|

## Grupos de segurança de rede
Qualquer NIC em uma máquina virtual pode ser associada a um grupo de segurança de rede (NSG), incluindo todas as NICs de uma VM que seja habilitada para várias NICs. Se uma NIC tiver um endereço atribuído em uma sub-rede que esteja associada a um NSG, as regras do NSG da sub-rede também se aplicarão à NIC. Além de associar sub-redes a NSGs, você também pode associar uma NIC a um NSG.

Se uma sub-rede for associada a um NSG e uma NIC dessa sub-rede for associada individualmente a um NSG, as regras do NSG associado serão aplicadas na "**ordem de fluxo**" de acordo com a direção do tráfego que estiver sendo passado para dentro ou para fora da NIC:

- O **tráfego de entrada **cujo destino é a NIC em questão flui primeiro pela sub-rede, acionando as regras do NSG da sub-rede, antes de passar para a NIC, quando serão acionadas as regras do NSG da NIC.- O **tráfego de saída** cujo destino é a NIC em questão flui primeiro para fora da sub-rede, acionando as regras do NSG da NIC, antes de passar pela sub-rede, quando serão acionadas as regras do NSG da sub-rede. 

A figura acima representa como a aplicação das regras do NSG é feita de acordo com o fluxo do tráfego (da VM para a sub-rede ou da sub-rede para a VM).

## Como configurar uma VM de várias NICs

As instruções a seguir o ajudarão a criar uma VM de várias NICs contendo 3 NICs: uma NIC padrão e duas NICs adicionais. As etapas da configuração criarão uma VM que será configurada de acordo com o fragmento do arquivo de configuração de serviços abaixo:

	<VirtualNetworkSite name="MultiNIC-VNet" Location="North Europe">
	<AddressSpace>
	  <AddressPrefix>10.1.0.0/16</AddressPrefix>
	    </AddressSpace>
	    <Subnets>
	      <Subnet name="Frontend">
	        <AddressPrefix>10.1.0.0/24</AddressPrefix>
	      </Subnet>
	      <Subnet name="Midtier">
	        <AddressPrefix>10.1.1.0/24</AddressPrefix>
	      </Subnet>
	      <Subnet name="Backend">
	        <AddressPrefix>10.1.2.0/23</AddressPrefix>
	      </Subnet>
	      <Subnet name="GatewaySubnet">
	        <AddressPrefix>10.1.200.0/28</AddressPrefix>
	      </Subnet>
	    </Subnets>
	… Skip over the remainder section …
	</VirtualNetworkSite>


É necessário primeiro seguir os pré-requisitos a seguir antes de executar os comandos do PowerShell do exemplo.

- Uma assinatura do Azure.
- Uma rede virtual configurada. Confira a [Visão geral da Rede Virtual](https://msdn.microsoft.com/library/azure/jj156007.aspx) para saber mais sobre VNets.
- A versão mais recente do Azure PowerShell baixada e instalada. Consulte [Como instalar e configurar o PowerShell do Azure](../install-configure-powershell).

1. Selecione uma imagem de VM na galeria de imagens de VMs do Azure. Observe que as imagens são alteradas frequentemente e estão disponíveis por região. A imagem especificada no exemplo a seguir pode ser alterada ou pode não ser da sua região, portanto certifique-se de especificar a imagem correta. 

	    $image = Get-AzureVMImage `
	    	-ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201410.01-en.us-127GB.vhd"

1. Crie a configuração da VM.

		$vm = New-AzureVMConfig -Name "MultiNicVM" -InstanceSize "ExtraLarge" `
			-Image $image.ImageName –AvailabilitySetName "MyAVSet"

1. Crie o logon de administrador padrão.

		Add-AzureProvisioningConfig –VM $vm -Windows -AdminUserName "<YourAdminUID>" `
			-Password "<YourAdminPassword>"

1. Adicione outras NICs à configuração da VM.

		Add-AzureNetworkInterfaceConfig -Name "Ethernet1" `
			-SubnetName "Midtier" -StaticVNetIPAddress "10.1.1.111" -VM $vm 
		Add-AzureNetworkInterfaceConfig -Name "Ethernet2" `
			-SubnetName "Backend" -StaticVNetIPAddress "10.1.2.222" -VM $vm

1. Especifique a sub-rede e o endereço IP da NIC padrão.

		Set-AzureSubnet -SubnetNames "Frontend" -VM $vm Set-AzureStaticVNetIP  `
			-IPAddress "10.1.0.100" -VM $vm

1. Crie a VM na sua rede virtual.

		New-AzureVM -ServiceName "MultiNIC-CS" –VNetName "MultiNIC-VNet" –VMs $vm

>[AZURE.NOTE]A VNet que você especificar aqui já deve existir (conforme mencionado nos pré-requisitos). O exemplo a seguir especifica uma rede virtual chamada "MultiNIC VNet".

## Consulte também

[Visão geral da Rede Virtual](https://msdn.microsoft.com/library/azure/jj156007.aspx)

[Tarefas de configuração da rede virtual](https://msdn.microsoft.com/library/azure/jj156206.aspx)

[Postagem do blog - várias NICs de VM e dispositivos VNet no Azure](../multiple-vm-nics-and-network-virtual-appliances-in-azure)

<!---HONumber=58--> 