<properties
   pageTitle="Configurar conexões de VPN Site a Site e de Rota Expressa que possam coexistir| Microsoft Azure"
   description="Este artigo o orienta na configuração da Rota Expressa e de uma conexão VPN Site a Site que pode coexistir para o modelo de implantação clássico."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/18/2016"
   ms.author="cherylmc"/>

# Configurar conexões coexistentes Site a Site e de Rota Expressa

Poder configurar a VPN site a site e a Rota Expressa tem várias vantagens. Você pode configurar a VPN site a site como um caminho de failover seguro para a Rota Expressa, ou usar VPNs site a site para se conectar a sites que não fazem parte da sua rede, mas que são conectados por meio da Rota Expressa. Neste artigo, analisaremos as etapas para configurar as duas situações. **Atualmente, você só pode criar essa configuração para redes virtuais usando o modelo de implantação clássico**. Quando tivermos uma documentação que se aplique ao modelo de implantação do Gerenciador de Recursos, criaremos um link para ela aqui.


**Sobre modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


Os circuitos de Rota Expressa devem ser previamente configurados antes de você seguir as instruções abaixo. É necessário que você tenha seguido os guias para [criar um circuito da Rota Expressa](expressroute-howto-circuit-classic.md) e [configurar o roteamento](expressroute-howto-routing-classic.md) antes de seguir as etapas abaixo.

## Limites e limitações

- **O roteamento em trânsito não tem suporte:** não é possível fazer o roteamento (via Azure) entre a sua rede local conectada via VPN site a site e a sua rede local conectada via Rota Expressa.
- **Não há suporte para ponto a site:** não é possível habilitar conexões VPN ponto a site para a mesma Rede Virtual conectada à Rota Expressa. A VPN de ponto a site e a Rota Expressa não podem coexistir para a mesma Rede Virtual.
- **O túnel forçado não pode ser habilitado no gateway de VPN site a site:** você só pode “forçar” todo o tráfego voltado para a Internet de volta para sua rede local por meio da Rota Expressa. 
- **Somente gateways padrão ou de alto desempenho:** você deve usar um gateway padrão ou de alto desempenho para o gateway de Rota Expressa e para o gateway de VPN site a site. Confira [SKUs de gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) para obter informações sobre SKUs de gateway.
- **Somente gateway de VPN baseada em rota:** você deve usar um gateway VPN baseado em rota. Consulte [Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) para obter informações sobre o gateway de VPN baseado em rota.
- **Requisito de rota estática:** se a sua rede local estiver conectada à VPN site a site e a uma Rota Expressa, será necessário ter uma rota estática configurada em sua rede local para rotear a conexão VPN site a site para a Internet pública.
- **O gateway de Rota Expressa deve ser configurado primeiro:** você deve criar o gateway de Rota Expressa primeiro, antes de adicionar o gateway de VPN site a site.


## Designs de configuração

### Configurar uma VPN site a site como um caminho de failover para a Rota Expressa

Você pode configurar uma conexão VPN site a site como um backup para a Rota Expressa. Isso se aplica apenas às redes virtuais vinculadas ao caminho de emparelhamento privado do Azure. Não há uma solução de failover com base em VPN para serviços acessíveis por meio de emparelhamentos público do Azure e da Microsoft. O circuito da Rota Expressa sempre será o link principal. Os dados só fluirão pelo caminho da VPN site a site se o circuito da Rota Expressa falhar.

![Coexistência](media/expressroute-howto-coexist-classic/scenario1.jpg)

### Configurar uma VPN site a site para se conectar a sites não conectados por meio da Rota Expressa

Você pode configurar sua rede de modo que alguns sites se conectem diretamente ao Azure através da VPN site a site, e alguns sites se conectem por meio da Rota Expressa.

![Coexistência](media/expressroute-howto-coexist-classic/scenario2.jpg)

>[AZURE.NOTE] Não é possível configurar uma rede virtual como um roteador de trânsito.

## Selecionando as etapas de uso

Há dois conjuntos de procedimentos diferentes para configurar as conexões de modo que elas coexistam. O procedimento de configuração selecionado dependerá caso você já tenha uma rede virtual à qual deseja se conectar ou caso queira criar uma nova rede virtual.


- Não tiver uma rede virtual e precisar criar um
	
	Se você ainda não tiver uma rede virtual, esse procedimento explicará isso criando uma nova rede virtual usando o modelo de implantação clássico e criando novas conexões de VPN Site a Site e de Rota Expressa. Para configurar, siga as etapas na seção do artigo [Para criar uma nova rede virtual e conexões coexistentes](#new).

- Eu já tenho um modelo de implantação clássico de rede virtual

	Talvez você já tenha uma rede virtual implementada com uma conexão de VPN Site a Site ou uma conexão de Rota Expressa existente. A seção [Configurar conexões coexistentes para uma rede virtual já existente](#add) explica como excluir o gateway e a criação de novas conexões de VPN Site a Site e de Rota Expressa. Observe que, ao criar novas conexões, as etapas devem ser concluídas em uma ordem muito específica. Não use as instruções de outros artigos para criar seus gateways e conexões.

	Neste procedimento, a criação de conexões que possam coexistir exigirá que você exclua seu gateway e então configure novos gateways. Isso significa que haverá tempo de inatividade nas suas conexões entre locais durante o processo de exclusão e recriação de seu gateway e conexões, mas você não precisará migrar nenhuma das suas VMs ou serviços para uma nova rede virtual. Suas VMs e serviços ainda poderão se comunicar por meio do balanceador de carga enquanto você configura o seu gateway, se estiverem configurados para fazer isso.


## <a name ="new"/> Para criar uma nova rede virtual e conexões coexistentes

Este procedimento orientará você na criação de uma Rede Virtual, bem como na criação das conexões site a site e de Rota Expressa que coexistirão.

1. Você precisará instalar a versão mais recente dos cmdlets do PowerShell do Gerenciador de Recursos do Azure. Confira [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell. Observe que os cmdlets que você usará para essa configuração podem ser ligeiramente diferentes daqueles com os quais você talvez esteja familiarizado. Certifique-se de usar os cmdlets especificados nestas instruções. 

2. Crie um esquema para a sua rede virtual. Para saber mais sobre o esquema de configuração, confira [Esquema de configuração de Rede Virtual do Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

	Quando criar seu esquema, certifique-se de usar os seguintes valores:

	- A sub-rede de gateway para a rede virtual deve ser /27 ou um prefixo menor (como /26 ou /25).
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

		Set-AzureVNetConfig -ConfigurationPath 'C:\NetworkConfig.xml'

4. <a name ="gw"/>Crie um gateway de Rota Expressa. Certifique-se de especificar GatewaySKU como *Standard* ou *HighPerformance* e GatewayType como *DynamicRouting*.

	Use o exemplo a seguir, substituindo os valores existentes pelos seus.

		New-AzureVNetGateway -VNetName MyAzureVNET -GatewayType DynamicRouting -GatewaySKU HighPerformance

5. Vincule o gateway de Rota Expressa ao circuito de Rota Expressa. Após essa etapa for concluída, a conexão entre sua rede local e o Azure, por meio de Rota Expressa, é estabelecida.

		New-AzureDedicatedCircuitLink -ServiceKey <service-key> -VNetName MyAzureVNET

6. Em seguida, crie seu gateway de VPN Site a Site. O GatewaySKU deve ser especificado como *Standard* ou *HighPerformance*, enquanto o GatewayType deve ser especificado como *DynamicRouting*.

		New-AzureVirtualNetworkGateway -VNetName MyAzureVNET -GatewayName S2SVPN -GatewayType DynamicRouting -GatewaySKU  HighPerformance

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

	> [AZURE.IMPORTANT] O site local para o VPN Site a site não está definido no netcfg. Em vez disso, você deve usar esse cmdlet para especificar os parâmetros do site local. Você não pode defini-lo usando o portal ou o arquivo netcfg.

	Use o exemplo a seguir, substituindo os valores existentes pelos seus.

	`New-AzureLocalNetworkGateway -GatewayName MyLocalNetwork -IpAddress <MyLocalGatewayIp> -AddressSpace <MyLocalNetworkAddress>`

	> [AZURE.NOTE] Se sua rede local tiver várias rotas, você poderá passá-las como uma matriz. $MyLocalNetworkAddress = @("10.1.2.0/24","10.1.3.0/24","10.2.1.0/24")


	Para obter as configurações de gateway de rede virtual, incluindo a ID do gateway e o IP público, use o cmdlet `Get-AzureVirtualNetworkGateway`. Veja os exemplos a seguir.

		Get-AzureLocalNetworkGateway

		GatewayId            : 532cb428-8c8c-4596-9a4f-7ae3a9fcd01b
		GatewayName          : MyLocalNetwork
		IpAddress            : 23.39.x.y
		AddressSpace         : {10.1.2.0/24}
		OperationDescription : Get-AzureLocalNetworkGateway
		OperationId          : ddc4bfae-502c-adc7-bd7d-1efbc00b3fe5
		OperationStatus      : Succeeded


8. Configure seu dispositivo VPN local para conectar-se ao novo gateway. Ao configurar seu dispositivo VPN, use as informações que você recuperou na etapa 6. Para obter mais informações sobre a configuração de dispositivo VPN, consulte [Configuração do Dispositivo VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

9. Vincule o gateway de VPN Site a Site no Azure ao gateway local.

	Neste exemplo, connectedEntityId é a ID de gateway local, que você pode encontrar executando `Get-AzureLocalNetworkGateway`. Você pode encontrar virtualNetworkGatewayId usando o cmdlet `Get-AzureVirtualNetworkGateway`. Após essa etapa, é estabelecida a conexão entre sua rede local e o Azure, por meio da conexão VPN Site a Site.


	`New-AzureVirtualNetworkGatewayConnection -connectedEntityId <local-network-gateway-id> -gatewayConnectionName Azure2Local -gatewayConnectionType IPsec -sharedKey abc123 -virtualNetworkGatewayId <azure-s2s-vpn-gateway-id>`

## <a name ="add"/> Configurar conexões coexistentes para uma rede virtual já existente

Se você tiver uma rede virtual existente conectada via conexão VPN de Rota Expressa ou Site a Site, será necessário primeiro excluir o gateway existente para que as conexões possam conectar-se à rede virtual existente. Isso significa que suas instalações locais perderão a conexão à sua rede virtual por meio do gateway enquanto você estiver trabalhando nessa configuração.

**Antes de começar a configuração:** verifique se você tem um número suficiente de endereços IP restantes em sua rede virtual, para que você pode aumentar o tamanho da sub-rede de gateway. Observe que você precisará excluir o gateway e recriá-lo, mesmo se você tiver endereços IP suficientes. Isso ocorre porque o gateway deve ser recriado para acomodar as conexões coexistentes.

1. Você precisará instalar a versão mais recente dos cmdlets do PowerShell do Gerenciador de Recursos do Azure. Confira [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell. Observe que os cmdlets que você usará para essa configuração podem ser ligeiramente diferentes daqueles com os quais você talvez esteja familiarizado. Certifique-se de usar os cmdlets especificados nestas instruções. 

2. Exclua o gateway de Rota Expressa ou gateway de VPN Site a Site existente. Use o cmdlet a seguir, substituindo os valores existentes pelos seus.

	`Remove-AzureVNetGateway –VnetName MyAzureVNET`

3. Exporte o esquema da rede virtual. Use o cmdlet do PowerShell a seguir, substituindo os valores existentes pelos seus.

	`Get-AzureVNetConfig –ExportToFile “C:\NetworkConfig.xml”`

4. Edite o esquema do arquivo de configuração de rede para que a sub-rede de gateway seja /27 ou um prefixo menor (como /26 ou /25). Veja os exemplos a seguir. Para saber mais sobre o esquema de configuração, confira [Esquema de configuração de Rede Virtual do Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.17.159.224/27</AddressPrefix>
          </Subnet>

5. Se o seu gateway anterior era de VPN Site a Site, você também deve alterar o tipo de conexão para **Dedicated**.

		         <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="MyLocalNetwork">
		              <Connection type="Dedicated" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>

6. Neste ponto, você terá uma VNet sem nenhum gateway. Para criar novos gateways e concluir suas conexões, você pode prosseguir com a [Etapa 4: criar um gateway de Rota Expressa](#gw), encontrado no conjunto de etapas anterior.

## Próximas etapas

Para saber mais sobre a Rota Expressa, confira [Perguntas frequentes sobre Rota Expressa](expressroute-faqs.md).

<!---HONumber=AcomDC_0323_2016-->