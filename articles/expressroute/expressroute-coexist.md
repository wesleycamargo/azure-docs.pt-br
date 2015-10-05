<properties
   pageTitle="Configurar conexões de VPN Site a Site e de Rota Expressa que possam coexistir| Microsoft Azure"
   description="Este artigo orienta você na configuração de conexões de Rota Expressa e VPN Site a Site que possam coexistir."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/17/2015"
   ms.author="cherylmc"/>

# Configurar conexões de VPN Site a Site e de Rota Expressa do Azure que coexistam

Você pode conectar a Rota Expressa e VPNs Site a Site à mesma rede virtual. Existem dois cenários diferentes onde talvez você queira usar conexões coexistentes. O Cenário 1 usa a Rota Expressa como o link ativo e a conexão site a site como a conexão de backup. No cenário 2, ambas as conexões estão ativas.

As etapas neste artigo se aplicam a redes virtuais usando o modelo de implantação clássica. Esse tipo de conexão coexistente ainda não está disponível para redes virtuais usando o modelo de implantação do Gerenciador de Recursos.



### Cenário 1

Neste cenário, você tem uma rede local. A Rota Expressa é um link ativo e sua VPN Site a Site é o backup. Se a conexão da Rota Expressa falhar, a conexão da VPN Site a Site permanecerá ativa. Esse cenário é mais apropriado para alta disponibilidade.

![Coexistência](media/expressroute-coexist/scenario1.jpg)



### Cenário 2

Neste cenário, você tem duas redes locais. Você poderá conectar uma ao Azure por meio da Rota Expressa e a outra por meio da VPN Site a Site. Nesse caso, ambas as conexões ficam ativas simultaneamente.

![Coexistência](media/expressroute-coexist/scenario2.jpg)


## Escolhendo um procedimento de configuração

Há duas opções diferentes de conjuntos de procedimentos para configurar as conexões para que elas coexistam. O procedimento de configuração selecionado dependerá caso você já tenha uma rede virtual à qual deseja se conectar ou caso queira criar uma nova rede virtual.

- **Criar uma nova rede virtual e conexões coexistentes:**

	Se você ainda não tiver uma rede virtual, este procedimento explicará como criar uma nova rede virtual e como criar novas conexões de VPN Site a Site e de Rota Expressa. Para configurar, siga as etapas para [Criar uma nova rede virtual com conexões coexistentes](#create-a-new-vnet-with-coexisting-connections).

- **Configurar sua rede virtual existente para conexões coexistentes:**

	Talvez você já tenha uma rede virtual implementada com uma conexão de VPN Site a Site ou uma conexão de Rota Expressa existente. O procedimento [Configurar conexões coexistentes para sua rede virtual existente](#configure-coexisting-connections-for-an-existing-vnet) orientará você na exclusão do gateway e na criação de novas conexões de VPN Site a Site e de Rota Expressa. Observe que, ao criar novas conexões, as etapas devem ser concluídas em uma ordem muito específica. Não use as instruções de outros artigos para criar seus gateways e conexões.

	Neste procedimento, a criação de conexões que possam coexistir exigirá que você exclua seu gateway e então configure novos gateways. Isso significa que haverá tempo de inatividade nas suas conexões entre locais durante o processo de exclusão e recriação de seu gateway e conexões, mas você não precisará migrar nenhuma das suas VMs ou serviços para uma nova rede virtual. Suas VMs e serviços ainda serão capazes de comunicar-se por meio do balanceador de carga, se eles estiverem configurados fazê-lo, enquanto você configura o seu gateway.


### Observações e limitações

- Você não conseguirá fazer o roteamento (por meio do Azure) entre sua rede local conectada via VPN Site a Site e sua rede local conectada via Rota Expressa.
- Não é possível habilitar conexões VPN de ponto ao local para a mesma VNet que está conectada à Rota Expressa. A VPN de ponto ao local e a Rota Expressa não podem coexistir para a mesma VNet.
- O gateway de Rota Expressa e o gateway de VPN Site a Site devem ser um SKU de gateway Standard ou HighPerformance. Para obter informações sobre os SKUs de gateway, consulte [SKUs de Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- Se sua rede local está conectada tanto a um gateway de VPN Site a Site quanto a um de Rota Expressa (cenário 1), você deve ter uma rota estática configurada em sua rede local para rotear a conexão VPN Site a Site para a Internet pública.
- Você deve criar o gateway de Rota Expressa primeiro, antes de adicionar o gateway de VPN Site a Site.
- Ambos os procedimentos supõem que você já tem um circuito de Rota Expressa configurado. Se este não é o caso, consulte os seguintes artigos:

	- [Configurar uma conexão de Rota Expressa por meio de um NSP (provedor de serviços de rede)](expressroute-configuring-nsps.md)
	- [Configurar uma conexão de Rota Expressa por meio de um EXP (provedor do Exchange)](expressroute-configuring-exps.md)


## Criar uma nova VNet com conexões coexistentes

Este procedimento orientará você na criação de uma rede virtual, bem como na criação das conexões de Site a Site e de Rota Expressa que coexistirão.

1. Verifique se você tem a versão mais recente dos cmdlets do PowerShell. Você pode baixar e instalar os cmdlets mais recentes do PowerShell na seção [Página de download](http://azure.microsoft.com/downloads/) do PowerShell.
2. Crie um esquema para a sua rede virtual. Para obter mais informações sobre como trabalhar com o arquivo de configuração de rede, consulte [Como criar uma rede virtual usando um arquivo de configuração de rede](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal). Para obter mais informações sobre o esquema de configuração, consulte [Esquema de Configuração de Rede Virtual do Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

	Quando criar seu esquema, certifique-se de usar os seguintes valores:

	- O comprimento do prefixo de rede da sub-rede do gateway para a rede virtual deve ser 27 ou menor (/26, /25, etc.).
	- O tipo de conexão de gateway é "Dedicated".

		      <VirtualNetworkSite name="MyAzureVNET" Location="Central US">
		        <AddressSpace>
		          <AddressPrefix>10.17.159.192/26</AddressPrefix>
		        </AddressSpace>
		        <Subnets>
		          <Subnet name="Subnet-1">
		            <AddressPrefix>10.17.159.192/27</AddressPrefix>
		          </Subnet>
		          <Subnet name="GatewaySubnet">
		            <AddressPrefix>10.17.159.224/27</AddressPrefix>
		          </Subnet>
		        </Subnets>
		        <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="MyLocalNetwork">
		              <Connection type="Dedicated" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>
		      </VirtualNetworkSite>



3. Depois de criar e configurar o arquivo de esquema XML, carregue o arquivo. Isso criará sua rede virtual.

	Use o cmdlet a seguir para carregar seu arquivo, substituindo o valor existente pelo seu.

	`Set-AzureVNetConfig -ConfigurationPath 'C:\NetworkConfig.xml'`
4. Crie um gateway de Rota Expressa. Certifique-se de especificar GatewaySKU como *Standard* ou *HighPerformance* e GatewayType como *DynamicRouting*.

	Use o exemplo a seguir, substituindo os valores existentes pelos seus.

	`New-AzureVNetGateway -VNetName MyAzureVNET -GatewayType DynamicRouting -GatewaySKU HighPerformance`

5. Vincule o gateway de Rota Expressa ao circuito de Rota Expressa. Após essa etapa for concluída, a conexão entre sua rede local e o Azure, por meio de Rota Expressa, é estabelecida.

	`New-AzureDedicatedCircuitLink -ServiceKey <service-key> -VNetName MyAzureVNET`

6. Em seguida, crie seu gateway de VPN Site a Site. O GatewaySKU deve ser especificado como *Standard* ou *HighPerformance*, enquanto o GatewayType deve ser especificado como *DynamicRouting*.

	`New-AzureVirtualNetworkGateway -VNetName MyAzureVNET -GatewayName S2SVPN -GatewayType DynamicRouting -GatewaySKU  HighPerformance`

	Para obter as configurações de gateway de rede virtual, incluindo a ID do gateway e o IP público, use o cmdlet `Get-AzureVirtualNetworkGateway`.

		Get-AzureVirtualNetworkGateway

		GatewayId            : 348ae011-ffa9-4add-b530-7cb30010565e
		GatewayName          : S2SVPN
		LastEventData        :
		GatewayType          : DynamicRouting
		LastEventTimeStamp   : 5/29/2015 4:41:41 PM
		LastEventMessage     : Successfully created a gateway for the following virtual network: GNSDesMoines
		LastEventID          : 23002
		State                : Provisioned
		VIPAddress           : 104.43.x.y
		DefaultSite          :
		GatewaySKU           : HighPerformance
		Location             :
		VnetId               : 979aabcf-e47f-4136-ab9b-b4780c1e1bd5
		SubnetId             :
		EnableBgp            : False
		OperationDescription : Get-AzureVirtualNetworkGateway
		OperationId          : 42773656-85e1-a6b6-8705-35473f1e6f6a
		OperationStatus      : Succeeded

7. Crie uma entidade de gateway de VPN de site local. Esse comando não configura seu gateway de VPN local. Em vez disso, ele permite que você forneça as configurações de gateway local, como o IP público e o espaço para endereço local, para que o gateway de VPN do Azure possa se conectar a ele.

	> [AZURE.IMPORTANT]O site local para o VPN de site a site não está definido no netcfg. Em vez disso, você deve usar esse cmdlet para especificar os parâmetros do site local. Você não pode defini-los usando o Portal de Gerenciamento ou o arquivo netcfg.

	Use o exemplo a seguir, substituindo os valores existentes pelos seus.

	`New-AzureLocalNetworkGateway -Gatewayname MyLocalNetwork -IpAddress <MyLocalGatewayIp> -AddressSpace <MyLocalNetworkAddress>`

	> [AZURE.IMPORTANT]Se sua rede local tiver várias rotas, você poderá passá-las como uma matriz. $MyLocalNetworkAddress = @("10.1.2.0/24","10.1.3.0/24","10.2.1.0/24")


	Para obter as configurações de gateway de rede virtual, incluindo a ID do gateway e o IP público, use o cmdlet `Get-AzureVirtualNetworkGateway`. Veja os exemplos a seguir.

		Get-AzureLocalNetworkGateway

		GatewayId            : 532cb428-8c8c-4596-9a4f-7ae3a9fcd01b
		GatewayName          : MyLocalNetwork
		IpAddress            : 23.39.x.y
		AddressSpace         : {10.1.2.0/24}
		OperationDescription : Get-AzureLocalNetworkGateway
		OperationId          : ddc4bfae-502c-adc7-bd7d-1efbc00b3fe5
		OperationStatus      : Succeeded


8. Configure seu dispositivo VPN local para conectar-se ao novo gateway. Ao configurar seu dispositivo VPN, use as informações que você recuperou na etapa 6. Para obter mais informações sobre a configuração de dispositivo VPN, consulte [Configuração do Dispositivo VPN](http://go.microsoft.com/fwlink/p/?linkid=615099).


9. Vincule o gateway de VPN Site a Site no Azure ao gateway local.

	Neste exemplo, connectedEntityId é a ID de gateway local, que você pode encontrar executando `Get-AzureLocalNetworkGateway`. Você pode encontrar virtualNetworkGatewayId usando o cmdlet `Get-AzureVirtualNetworkGateway`. Após essa etapa, é estabelecida a conexão entre sua rede local e o Azure, por meio da conexão VPN Site a Site.


	`New-AzureVirtualNetworkGatewayConnection -connectedEntityId <local-network-gateway-id> -gatewayConnectionName Azure2Local -gatewayConnectionType IPsec -sharedKey abc123 -virtualNetworkGatewayId <azure-s2s-vpn-gateway-id>`


## Configurar conexões coexistentes para uma rede virtual existente

Se você tiver uma rede virtual existente conectada via conexão VPN de Rota Expressa ou Site a Site, será necessário primeiro excluir o gateway existente para que as conexões possam conectar-se à rede virtual existente. Isso significa que suas instalações locais perderão a conexão à sua rede virtual por meio do gateway enquanto você estiver trabalhando nessa configuração.

**Antes de começar a configuração:** verifique se você tem um número suficiente de endereços IP restantes em sua rede virtual, para que você pode aumentar o tamanho da sub-rede de gateway.


1. Baixe a versão mais recente dos cmdlets do PowerShell. Você pode baixar e instalar os cmdlets mais recentes do PowerShell na seção [Página de download](http://azure.microsoft.com/downloads/) do PowerShell.

2. Exclua o gateway de VPN Site a Site existente. Use o cmdlet a seguir, substituindo os valores existentes pelos seus.

	`Remove-AzureVNetGateway –VnetName MyAzureVNET`

2. Exporte o esquema da rede virtual. Use o cmdlet do PowerShell a seguir, substituindo os valores existentes pelos seus.

	`Get-AzureVNetConfig –ExportToFile “C:\NetworkConfig.xml”`
3. Edite o esquema de arquivo de configuração de rede para que a sub-rede de gateway seja /27 ou menor (/26, /25 etc.). Veja os exemplos a seguir. Para obter mais informações sobre como trabalhar com o arquivo de configuração de rede, consulte [Como criar uma rede virtual usando um arquivo de configuração de rede](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal). Para obter mais informações sobre o esquema de configuração, consulte [Esquema de Configuração de Rede Virtual do Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).


          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.17.159.224/27</AddressPrefix>
          </Subnet>
4. Se o seu gateway anterior era de VPN Site a Site, você também deve alterar o tipo de conexão para **Dedicated**.

		         <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="MyLocalNetwork">
		              <Connection type="Dedicated" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>
5. Neste ponto, você terá uma VNet sem nenhum gateway. Você pode prosseguir com a **Etapa 3** neste artigo, [Criar uma nova rede virtual com conexões coexistentes](#create-a-new-vnet-with-coexisting-connections), para criar novos gateways e concluir suas conexões.



## Próximas etapas

Saiba mais sobre Rota Expressa. Consulte [Visão geral de Rota Expressa](expressroute-introduction.md).

Saiba mais sobre gateways de VPN. Consulte [Sobre gateways de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

<!---HONumber=Sept15_HO4-->