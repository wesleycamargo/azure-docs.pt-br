<properties 
   pageTitle="Conectar vários sites locais a uma rede virtual"
   description="Este artigo o guiará ao conectar múltiplos sites locais a uma rede virtual."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/04/2015"
   ms.author="cherylmc" />

# Conectar vários sites locais a uma rede virtual

É possível conectar vários sites locais a uma única rede virtual. Isso é especialmente atraente para criar soluções de nuvem híbrida. Criar uma conexão de múltiplos sites ao gateway de rede virtual do Azure é muito semelhante à criação de outras conexões site a site. Na verdade, você pode usar o gateway de VPN existente do Azure desde que tenha um gateway de VPN baseado em rota (ou roteamento dinâmico) configurado para sua rede virtual.

Se o gateway for baseado em política (ou roteamento estático), você sempre poderá alterar o tipo de gateway sem a necessidade de reconstruir a rede virtual para acomodar múltiplos sites, embora também precise garantir que o seu gateway de VPN local ofereça suporte a configurações de VPN baseadas em rotas. Basta adicionar definições de configuração no arquivo de configuração de rede e criar várias conexões de VPN da sua rede virtual para os sites adicionais.

![VPN de múltiplos sites](./media/vpn-gateway-multi-site/IC727363.png)

## Considere o seguinte

**Você não poderá usar o Portal de Gerenciamento para fazer alterações nessa rede virtual.** Nesta versão, você precisará fazer alterações no arquivo de configuração de rede em vez de usar o Portal de Gerenciamento. Se você fizer alterações no Portal de Gerenciamento, elas vão substituir as configurações de referência de múltiplos sites para essa rede virtual. Será fácil usar o arquivo de configuração de rede depois de concluir o procedimento de múltiplos sites. No entanto, se você tiver várias pessoas trabalhando em sua configuração de rede, precisará certificar-se de que todos saibam desta limitação. Isso não significa que você não pode usar o Portal de Gerenciamento. Você pode usá-lo para tudo exceto fazer alterações de configuração nessa rede virtual específica.

## Antes de começar

Antes de começar a configuração, verifique se você tem os seguintes itens:

- Uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [benefícios de assinante do MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/).

- Hardware de VPN compatível para cada caminho no local. Consulte [Sobre dispositivos VPN para conectividade de rede virtual](http://go.microsoft.com/fwlink/p/?linkid=615099) para verificar se o dispositivo que você deseja usar é sabidamente compatível.

- Um endereço IP IPv4 público voltado para o exterior para cada dispositivo VPN. O endereço IP não pode estar localizado atrás de um NAT. Isso é obrigatório.

-   A versão mais recente dos cmdlets do Azure PowerShell. Você pode baixar e instalar a versão mais recente na seção Windows PowerShell da [página de downloads](http://azure.microsoft.com/downloads/).

- Alguém que seja proficiente na configuração de seu hardware de VPN. Você não poderá usar os scripts VPN gerados automaticamente no Portal de Gerenciamento para configurar os dispositivos VPN. Isso significa que você precisará ter um grande conhecimento de como configurar seu dispositivo VPN ou trabalhar com alguém que tenha.

- Os intervalos de endereços IP que você deseja usar na sua rede virtual (se ainda não tiver criado uma).

- Os intervalos de endereços IP para cada um dos sites de rede local aos quais se conectará. Você precisará certificar-se de que os intervalos de endereço IP para cada um dos sites de rede local aos quais quer se conectar não se sobrepõem. Caso contrário, o Portal de Gerenciamento ou a API REST rejeitará a configuração que está sendo carregada. Por exemplo, se você tiver dois sites de rede local que contenham o intervalo de endereço IP 10.2.3.0/24 e tiver um pacote com um endereço de destino 10.2.3.3, o Azure não saberá para qual site você deseja enviar o pacote porque os intervalos de endereço estão sobrepostos. Para evitar problemas de roteamento, o Azure não permite carregar um arquivo de configuração com sobreposição de intervalos.

## Criar sua rede virtual e o gateway

1. **Crie uma VPN site a site com um gateway de roteamento dinâmico.** Se você já tiver uma, ótimo! Você pode continuar para [Exportar as definições de configuração de rede virtual](#export). Caso contrário, faça o seguinte:

	**Se você já tiver uma rede virtual site a site, mas ela tiver um gateway de roteamento estático:** **1.** Altere o tipo de gateway para roteamento dinâmico. Uma VPN de múltiplos sites requer um gateway de roteamento dinâmico. Para alterar o tipo de gateway, você precisará primeiro excluir o gateway existente e, em seguida, criar um novo. Para obter instruções, consulte [Alterar o tipo de roteamento do gateway de VPN](vpn-gateway-configure-vpn-gateway-mp.md/#how-to-change-your-vpn-gateway-type). **2.** Configure seu novo gateway e crie seu túnel de VPN. Para obter instruções, consulte [Configurar um gateway de VPN no Portal de Gerenciamento](vpn-gateway-configure-vpn-gateway-mp.md).
	
	**Caso não tenha uma rede virtual site a site:****1.** Crie a sua rede virtual site a site usando estas instruções: [Criar uma rede virtual com uma conexão de VPN site a site no Portal de Gerenciamento](vpn-gateway-site-to-site-create.md). **2.** Configure um gateway de roteamento dinâmico usando estas instruções: [Configurar um gateway de VPN no Portal de Gerenciamento](vpn-gateway-configure-vpn-gateway-mp.md). Lembre-se de selecionar **roteamento dinâmico** para o tipo de gateway.



1. **<a name="export"></a>Exportar as definições de configuração de rede virtual.** Para exportar o arquivo de configuração da sua rede, consulte [Para exportar as definições da sua rede](../virtual-network/virtual-networks-using-network-configuration-file.md#export-and-import-virtual-network-settings-using-the-management-portal). O arquivo exportado será usado usado para configurar as novas definições de múltiplos sites.

1. **Abra o arquivo de configuração de rede.** Abra o arquivo de configuração de rede que você baixou na última etapa. Use qualquer editor de xml que desejar. O arquivo deve ser semelhante ao seguinte:

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

1. Adicione referências a múltiplos sites ao arquivo de configuração de rede. Ao adicionar ou remover informações de referência do site, alterações de configuração serão feitas em ConnectionsToLocalNetwork/LocalNetworkSiteRef. A adição de uma nova referência de site local faz com que o Azure crie um novo túnel. No exemplo abaixo, a configuração de rede é uma conexão de site único.

		<Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

	Para adicionar referências a sites adicionais (criar uma configuração de múltiplos sites), basta adicionar linhas "LocalNetworkSiteRef" adicionais, como mostrado no exemplo a seguir:

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

1. **Salve o arquivo de configuração de rede e importe-o.** Para importar o arquivo de configuração da rede, consulte Para importar as definições da sua rede. Ao importar esse arquivo com as alterações, os novos túneis serão adicionados. Os túneis usarão o gateway dinâmico que você criou anteriormente.



1. **Baixe as chaves pré-compartilhadas para os túneis de VPN.** Depois de adicionar os novos túneis, use o cmdlet do PowerShell Get-AzureVNetGatewayKey para obter as chaves pré-compartilhadas IPsec/IKE para cada túnel.

	Por exemplo:

		PS C:\> Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"

		PS C:\> Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"

	Se preferir, você também pode usar o API REST *Obter chave compartilhada de gateway de rede* para obter as chaves pré-compartilhadas.

## Verificar as conexões

**Verifique o status do túnel de múltiplos sites.** Depois de baixar as chaves para cada túnel, é recomendável verificar as conexões. Use *Get-AzureVnetConnection* para obter uma lista dos túneis de rede virtual, conforme mostrado no exemplo a seguir. VNet1 é o nome do VNet.

		PS C:\Users\yushwang\Azure> Get-AzureVnetConnection -VNetName VNET1
		
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

Para saber mais sobre gateways de VPN, consulte [Sobre gateways de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

<!---HONumber=Oct15_HO3-->