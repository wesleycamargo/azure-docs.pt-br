<properties 
   pageTitle="Conectar múltiplos sites locais a uma rede virtual usando um Gateway de VPN"
   description="Este artigo explica passo a passo como conectar múltiplos sites locais a uma rede virtual usando um Gateway de VPN para o modelo de implantação clássica."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/11/2016"
   ms.author="yushwang" />

# Conectar vários sites locais a uma rede virtual

Este artigo aplica-se à conexão de vários sites locais a uma rede virtual criada usando o modelo de implantação clássica (também conhecido como Gerenciamento de Serviços). Já que temos um artigo com as etapas para redes virtuais criadas usando o modelo do Gerenciador de Recursos, vou me vincular a ele a partir desta página.

**Sobre modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

**Modelos de implantação e ferramentas para conexões multissites**

[AZURE.INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]


## Sobre a conexão

É possível conectar vários sites locais a uma única rede virtual. Isso é especialmente atraente para criar soluções de nuvem híbrida. A criação de uma conexão de múltiplos sites ao gateway de rede virtual do Azure é muito semelhante à criação de outras conexões site a site. Na verdade, você pode usar um gateway de VPN do Azure existente, desde que o gateway seja dinâmico (baseado em rotas).

Se já houver um gateway estático conectado à sua rede virtual, você poderá alterar o tipo de gateway para dinâmico sem precisar reconstruir a rede virtual a fim de acomodar os vários sites. Antes de alterar o tipo de roteamento, verifique se o gateway de VPN local oferece suporte às configurações de VPN baseada em rota.

![diagrama multissites](./media/vpn-gateway-multi-site/multisite.png "vários sites")

## Considere o seguinte

**Você não poderá usar o Portal Clássico do Azure para fazer alterações nessa rede virtual.** Nesta versão, você precisará fazer alterações no arquivo de configuração de rede em vez de usar o Portal Clássico do Azure. Se você fizer alterações no Portal Clássico do Azure, elas vão substituir as configurações de referência de múltiplos sites para essa rede virtual.

Será fácil usar o arquivo de configuração de rede depois de concluir o procedimento de múltiplos sites. No entanto, se você tiver várias pessoas trabalhando em sua configuração de rede, precisará certificar-se de que todos saibam desta limitação. Isso não significa que você não pode usar o Portal Clássico do Azure. Você pode usá-lo para tudo, exceto fazer alterações de configuração nessa rede virtual específica.

## Antes de começar

Antes de começar a configuração, verifique se você tem os seguintes itens:

- Uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Hardware de VPN compatível para cada caminho no local. Confira [Sobre dispositivos VPN para conectividade de rede virtual](vpn-gateway-about-vpn-devices.md) para verificar se o dispositivo que você deseja usar é sabidamente compatível.

- Um endereço IP IPv4 público voltado para o exterior para cada dispositivo VPN. O endereço IP não pode estar localizado atrás de um NAT. Isso é obrigatório.

- Você precisará instalar a versão mais recente dos cmdlets do Azure PowerShell. Confira [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para saber mais sobre como instalar os cmdlets do PowerShell.

- Alguém que seja proficiente na configuração de seu hardware de VPN. Você não poderá usar os scripts VPN gerados automaticamente no Portal de Clássico do Azure para configurar os dispositivos VPN. Isso significa que você precisará ter um grande conhecimento de como configurar seu dispositivo VPN ou trabalhar com alguém que tenha.

- Os intervalos de endereços IP que você deseja usar na sua rede virtual (se ainda não tiver criado uma).

- Os intervalos de endereços IP para cada um dos sites de rede local aos quais se conectará. Você precisará certificar-se de que os intervalos de endereço IP para cada um dos sites de rede local aos quais quer se conectar não se sobrepõem. Caso contrário, o Portal Clássico do Azure ou a API REST rejeitará a configuração que está sendo carregada.

	Por exemplo, se você tiver dois sites de rede local que contenham o intervalo de endereço IP 10.2.3.0/24 e tiver um pacote com um endereço de destino 10.2.3.3, o Azure não saberá para qual site você deseja enviar o pacote porque os intervalos de endereço estão sobrepostos. Para evitar problemas de roteamento, o Azure não permite carregar um arquivo de configuração com sobreposição de intervalos.



## 1\. Criar uma VPN site a site

Se você já tiver uma VPN site a site com um gateway de roteamento dinâmico, ótimo! Continue em [Exporte as definições da configuração de rede virtual](#export). Caso contrário, faça o seguinte:

### Se você já tiver uma rede virtual site a site, mas ela tiver um gateway de roteamento estático (baseado em política):

1. Altere o tipo de gateway para roteamento dinâmico. Uma VPN multissites exige um gateway de roteamento dinâmico (também chamado de baseado em rota). Para alterar o tipo de gateway, você precisará primeiro excluir o gateway existente e, em seguida, criar um novo. Para obter instruções, confira [Change a VPN Gateway Routing Type](vpn-gateway-configure-vpn-gateway-mp.md/#how-to-change-your-vpn-gateway-type).  

2. Configure seu novo gateway e crie seu túnel de VPN. Para obter instruções, consulte [Configurar um gateway de VPN no Portal Clássico do Azure](vpn-gateway-configure-vpn-gateway-mp.md). Primeiro, altere o tipo de gateway para roteamento dinâmico.

### Se você não tiver uma rede virtual site a site:

1. Crie a sua rede virtual site a site usando estas instruções: [Criar uma rede virtual com uma conexão de VPN site a site no Portal Clássico do Azure](vpn-gateway-site-to-site-create.md).  

2. Configure um gateway de roteamento dinâmico usando estas instruções: [Configurar um gateway de VPN](vpn-gateway-configure-vpn-gateway-mp.md). Lembre-se de selecionar **roteamento dinâmico** para o tipo de gateway.

## <a name="export"></a>2. Exportar o arquivo de configuração de rede 

Exporte o arquivo de configuração de rede. O arquivo exportado será usado usado para configurar as novas definições de múltiplos sites. Se você precisar de instruções sobre como exportar um arquivo, consulte a seção neste artigo: [Como criar uma rede virtual usando um arquivo de configuração de rede no Portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal).

## 3\. Abrir o arquivo de configuração de rede

Abra o arquivo de configuração de rede que você baixou na última etapa. Use qualquer editor de xml que desejar. O arquivo deve ser semelhante ao seguinte:

		<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
		  <VirtualNetworkConfiguration>
		    <LocalNetworkSites>
		      <LocalNetworkSite name="Site1">
		        <AddressSpace>
		          <AddressPrefix>10.0.0.0/16</AddressPrefix>
		          <AddressPrefix>10.1.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
		      </LocalNetworkSite>
		      <LocalNetworkSite name="Site2">
		        <AddressSpace>
		          <AddressPrefix>10.2.0.0/16</AddressPrefix>
		          <AddressPrefix>10.3.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
		      </LocalNetworkSite>
		    </LocalNetworkSites>
		    <VirtualNetworkSites>
		      <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
		        <AddressSpace>
		          <AddressPrefix>10.20.0.0/16</AddressPrefix>
		          <AddressPrefix>10.21.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <Subnets>
		          <Subnet name="FE">
		            <AddressPrefix>10.20.0.0/24</AddressPrefix>
		          </Subnet>
		          <Subnet name="BE">
		            <AddressPrefix>10.20.1.0/24</AddressPrefix>
		          </Subnet>
		          <Subnet name="GatewaySubnet">
		            <AddressPrefix>10.20.2.0/29</AddressPrefix>
		          </Subnet>
		        </Subnets>
		        <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="Site1">
		              <Connection type="IPsec" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>
		      </VirtualNetworkSite>
		    </VirtualNetworkSites>
		  </VirtualNetworkConfiguration>
		</NetworkConfiguration>

## 4\. Adicionar referências a vários sites

Ao adicionar ou remover informações de referência do site, alterações de configuração serão feitas em ConnectionsToLocalNetwork/LocalNetworkSiteRef. A adição de uma nova referência de site local faz com que o Azure crie um novo túnel. No exemplo abaixo, a configuração de rede é uma conexão de site único. Quando terminar de fazer suas alterações, salve o arquivo.

		<Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

	To add additional site references (create a multi-site configuration), simply add additional "LocalNetworkSiteRef" lines, as shown in the example below: 

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

## 5\. Importar o arquivo de configuração de rede

Importe o arquivo de configuração de rede. Ao importar esse arquivo com as alterações, os novos túneis serão adicionados. Os túneis usarão o gateway dinâmico que você criou anteriormente. Se você precisar de instruções sobre como importar o arquivo, consulte a seção neste artigo: [Como criar uma rede virtual usando um arquivo de configuração de rede no Portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md#how-to-create-a-vnet-using-a-network-config-file-in-the-azure-portal).

## 6\. Baixar chaves

Depois de adicionar os novos túneis, use o cmdlet do PowerShell `Get-AzureVNetGatewayKey` para obter as chaves pré-compartilhadas IPsec/IKE para cada túnel.

Por exemplo:

	Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"

	Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"

Se preferir, você também pode usar o API REST *Obter chave compartilhada de gateway de rede* para obter as chaves pré-compartilhadas.

## 7\. Verificar as conexões

Verifique o status do túnel de múltiplos sites. Depois de baixar as chaves para cada túnel, é recomendável verificar as conexões. Use `Get-AzureVnetConnection` para obter uma lista dos túneis de rede virtual, conforme mostrado no exemplo a seguir. VNet1 é o nome do VNet.

	Get-AzureVnetConnection -VNetName VNET1
		
	ConnectivityState         : Connected
	EgressBytesTransferred    : 661530
	IngressBytesTransferred   : 519207
	LastConnectionEstablished : 5/2/2014 2:51:40 PM
	LastEventID               : 23401
	LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
	LastEventTimeStamp        : 5/2/2014 2:51:40 PM
	LocalNetworkSiteName      : Site1
	OperationDescription      : Get-AzureVNetConnection
	OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
	OperationStatus           : Succeeded
		
	ConnectivityState         : Connected
	EgressBytesTransferred    : 789398
	IngressBytesTransferred   : 143908
	LastConnectionEstablished : 5/2/2014 3:20:40 PM
	LastEventID               : 23401
	LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
	LastEventTimeStamp        : 5/2/2014 2:51:40 PM
	LocalNetworkSiteName      : Site2
	OperationDescription      : Get-AzureVNetConnection
	OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
	OperationStatus           : Succeeded

## Próximas etapas

Para saber mais sobre Gateways de VPN, confira [Sobre gateways de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

<!---HONumber=AcomDC_0518_2016-->