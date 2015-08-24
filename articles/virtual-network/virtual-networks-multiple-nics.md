<properties 
   pageTitle="Criar uma VM com diversos NICs"
   description="Saiba como criar e configurar máquinas virtuais com várias placas de rede"
   services="virtual-network, virtual-machines"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/10/2015"
   ms.author="telmos" />

# Criar uma VM com diversos NICs

O recurso de várias NICs permite criar e gerenciar várias placas de interface de rede virtual (NICs) nas máquinas virtuais (VMs) do Azure. Várias NICs são um requisito para muitos aplicativos virtuais de rede, como soluções de distribuição de aplicativos e de otimização de WAN. Ter várias NICs também agrega mais funcionalidade de gerenciamento de tráfego da rede, incluindo isolamento do tráfego entre uma NIC de front-end e as NICs de back-end ou a separação do tráfego do plano de dados do tráfego do plano de gerenciamento.

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
|A0\\extrapequeno|1|
|A1\\pequeno|1|
|A2\\médio|1|
|A3\\grande|2|
|A4\\extragrande|4|
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

## Grupos de segurança de rede (NSG)
Qualquer NIC em uma máquina virtual pode ser associada a um grupo de segurança de rede (NSG), incluindo todas as NICs de uma VM que seja habilitada para várias NICs. Se uma NIC tiver um endereço atribuído em uma sub-rede que esteja associada a um NSG, as regras do NSG da sub-rede também se aplicarão à NIC. Além de associar sub-redes a NSGs, você também pode associar uma NIC a um NSG.

Se uma sub-rede for associada a um NSG e uma NIC dessa sub-rede for associada individualmente a um NSG, as regras do NSG associado serão aplicadas na "**ordem de fluxo**" de acordo com a direção do tráfego que estiver sendo passado para dentro ou para fora da NIC:

- **O **tráfego de entrada **cujo destino é a NIC em questão flui primeiro pela sub-rede, acionando as regras do NSG da sub-rede, antes de passar para a NIC, quando serão acionadas as regras do NSG da NIC.
- O **tráfego de saída** cujo destino é a NIC em questão flui primeiro para fora da sub-rede, acionando as regras do NSG da NIC, antes de passar pela sub-rede, quando serão acionadas as regras do NSG da sub-rede. 

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


É necessário ter os pré-requisitos a seguir antes de tentar executar os comandos do PowerShell no exemplo.

- Uma assinatura do Azure.
- Uma rede virtual configurada. Confira a [Visão geral da Rede Virtual](https://msdn.microsoft.com/library/azure/jj156007.aspx) para saber mais sobre VNets.
- A versão mais recente do Azure PowerShell baixada e instalada. Consulte [Como instalar e configurar o PowerShell do Azure](../install-configure-powershell).

Para criar uma máquina virtual com várias placas de rede (NICs), siga as etapas abaixo:

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

		Set-AzureSubnet -SubnetNames "Frontend" -VM $vm 
		Set-AzureStaticVNetIP -IPAddress "10.1.0.100" -VM $vm

1. Crie a VM na sua rede virtual.

		New-AzureVM -ServiceName "MultiNIC-CS" –VNetName "MultiNIC-VNet" –VMs $vm

>[AZURE.NOTE]A VNet que você especificar aqui já deve existir (conforme mencionado nos pré-requisitos). O exemplo a seguir especifica uma rede virtual chamada **MultiNIC-VNet**.

## Acesso secundário à NIC para outras sub-redes

O modelo atual no Azure é que, todas as NICs em uma máquina virtual são configuradas com um gateway padrão. Isso permite que as NICs se comuniquem com endereços IP fora de sua sub-rede. Em sistemas operacionais que usam o modelo de roteamento de host fraco, como o Linux, a conectividade da Internet será interrompida se o tráfego de entrada e saída usar NICs diferentes.

Para corrigir esse problema, o Azure disponibilizará uma atualização nas primeiras semanas de julho de 2015 para a plataforma que removerá o gateway padrão das placas de rede secundárias. Isso não afetará as máquinas virtuais existentes até que elas sejam reinicializadas. Após a reinicialização, as novas configurações entrarão em vigor; neste momento, o fluxo de tráfego nas NICs secundárias será limitado para dentro da mesma sub-rede. Se os usuários desejarem habilitar NICs secundárias para falar fora da sua própria sub-rede, precisarão adicionar uma entrada à tabela de roteamento para configurar o gateway, conforme descrito abaixo.

### Configurar máquinas virtuais do Windows

Suponha que você tenha uma VM do Windows com duas NICs da seguinte maneira:

- Endereço IP primário da NIC: 192.168.1.4
- Endereço IP secundário da NIC: 192.168.2.5

A tabela de rotas do IPv4 para essa VM ficaria assim:

	IPv4 Route Table
	===========================================================================
	Active Routes:
	Network Destination        Netmask          Gateway       Interface  Metric
	          0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
	        127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
	        127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
	  127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
	    168.63.129.16  255.255.255.255      192.168.1.1      192.168.1.4      6
	      192.168.1.0    255.255.255.0         On-link       192.168.1.4    261
	      192.168.1.4  255.255.255.255         On-link       192.168.1.4    261
	    192.168.1.255  255.255.255.255         On-link       192.168.1.4    261
	      192.168.2.0    255.255.255.0         On-link       192.168.2.5    261
	      192.168.2.5  255.255.255.255         On-link       192.168.2.5    261
	    192.168.2.255  255.255.255.255         On-link       192.168.2.5    261
	        224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
	        224.0.0.0        240.0.0.0         On-link       192.168.1.4    261
	        224.0.0.0        240.0.0.0         On-link       192.168.2.5    261
	  255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
	  255.255.255.255  255.255.255.255         On-link       192.168.1.4    261
	  255.255.255.255  255.255.255.255         On-link       192.168.2.5    261
	===========================================================================

Observe que a rota padrão (0.0.0.0) só está disponível para a NIC primária. Você não conseguirá acessar recursos fora da sub-rede para a NIC secundária, conforme mostrado abaixo:

	C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5
	 
	Pinging 192.168.1.7 from 192.165.2.5 with 32 bytes of data:
	PING: transmit failed. General failure.
	PING: transmit failed. General failure.
	PING: transmit failed. General failure.
	PING: transmit failed. General failure.

Para adicionar uma rota padrão à NIC secundária, siga as etapas abaixo:

1. Em um prompt de comando, execute o comando a seguir para identificar o número de índice para a NIC secundária:

		C:\Users\Administrator>route print
		===========================================================================
		Interface List
		 29...00 15 17 d9 b1 6d ......Microsoft Virtual Machine Bus Network Adapter #16
		 27...00 15 17 d9 b1 41 ......Microsoft Virtual Machine Bus Network Adapter #14
		  1...........................Software Loopback Interface 1
		 14...00 00 00 00 00 00 00 e0 Teredo Tunneling Pseudo-Interface
		 20...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
		===========================================================================

2. Observe a segunda entrada na tabela, com um índice de 27 (no exemplo).
3. No prompt de comando, execute o comando **route add** conforme mostrado abaixo. Neste exemplo, você está especificando 192.168.2.1 como o gateway padrão para a NIC secundária:

		route ADD -p 0.0.0.0 MASK 0.0.0.0 192.168.2.1 METRIC 5000 IF 27

4. Para testar a conectividade, volte ao prompt de comando e tente executar o ping em uma sub-rede diferente da NIC secundária, como int eh, mostrado no exemplo a seguir:

		C:\Users\Administrator>ping 192.168.1.7 -S 192.165.2.5
		 
		Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
		Reply from 192.168.1.7: bytes=32 time<1ms TTL=128
		Reply from 192.168.1.7: bytes=32 time=2ms TTL=128
		Reply from 192.168.1.7: bytes=32 time<1ms TTL=128

5. Você também pode conferir a sua tabela de rotas para verificar a rota recém-adicionada, conforme mostrado abaixo:

		C:\Users\Administrator>route print

		...

		IPv4 Route Table
		===========================================================================
		Active Routes:
		Network Destination        Netmask          Gateway       Interface  Metric
		          0.0.0.0          0.0.0.0      192.168.1.1      192.168.1.4      5
		          0.0.0.0          0.0.0.0      192.168.2.1      192.168.2.5   5005
		        127.0.0.0        255.0.0.0         On-link         127.0.0.1    306

### Configurar máquinas virtuais Linux

Para VMs do Linux, como o comportamento padrão usa roteamento de host fraco, recomendamos que as NICs secundárias sejam restritas a fluxos de tráfego somente dentro da mesma sub-rede. No entanto, se determinados cenários exigirem conectividade fora da sub-rede, os usuários devem habilitar a política com base em roteamento para garantir que o tráfego de entrada e saída use a mesma NIC.

<!---HONumber=August15_HO7-->