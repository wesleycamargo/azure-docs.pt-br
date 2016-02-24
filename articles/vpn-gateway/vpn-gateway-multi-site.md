<properties 
   pageTitle="Conectar múltiplos sites locais a uma rede virtual usando um Gateway de VPN"
   description="Este artigo explica passo a passo como conectar múltiplos sites locais a uma rede virtual usando um Gateway de VPN para o modelo de implantação clássica."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/17/2015"
   ms.author="cherylmc" />

# Conectar vários sites locais a uma rede virtual

Este artigo aplica-se à conexão de vários sites locais a uma rede virtual criada usando o modelo de implantação clássica (também conhecido como Gerenciamento de Serviços).

**Sobre modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]Já que temos um artigo com as etapas para redes virtuais criadas usando o modelo do Gerenciador de Recursos, vou me vincular a ele a partir desta página.

## Sobre a conexão

É possível conectar vários sites locais a uma única rede virtual. Isso é especialmente atraente para criar soluções de nuvem híbrida. A criação de uma conexão de múltiplos sites ao gateway de rede virtual do Azure é muito semelhante à criação de outras conexões site a site. Na verdade, você pode usar um gateway de VPN do Azure existente, desde que o gateway seja dinâmico (baseado em rotas).

Se já houver um gateway estático conectado à sua rede virtual, você poderá alterar o tipo de gateway para dinâmico sem precisar reconstruir a rede virtual a fim de acomodar os vários sites. Antes de alterar o tipo de roteamento, verifique se o gateway de VPN local oferece suporte às configurações de VPN baseada em rota.

![VPN de múltiplos sites](./media/vpn-gateway-multi-site/IC727363.png)

## Considere o seguinte

**Você não poderá usar o Portal Clássico do Azure para fazer alterações nessa rede virtual.** Nesta versão, você precisará fazer alterações no arquivo de configuração de rede em vez de usar o Portal Clássico do Azure. Se você fizer alterações no Portal Clássico do Azure, elas vão substituir as configurações de referência de múltiplos sites para essa rede virtual. Será fácil usar o arquivo de configuração de rede depois de concluir o procedimento de múltiplos sites. No entanto, se você tiver várias pessoas trabalhando em sua configuração de rede, precisará certificar-se de que todos saibam desta limitação. Isso não significa que você não pode usar o Portal Clássico do Azure. Você pode usá-lo para tudo, exceto fazer alterações de configuração nessa rede virtual específica.

## Antes de começar

Antes de começar a configuração, verifique se você tem os seguintes itens:

- Uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Hardware de VPN compatível para cada caminho no local. Confira [Sobre dispositivos VPN para conectividade de rede virtual](http://go.microsoft.com/fwlink/p/?linkid=615099) para verificar se o dispositivo que você deseja usar é sabidamente compatível.

- Um endereço IP IPv4 público voltado para o exterior para cada dispositivo VPN. O endereço IP não pode estar localizado atrás de um NAT. Isso é obrigatório.

- A versão mais recente dos cmdlets do Azure PowerShell. Você pode baixar e instalar a versão mais recente na seção Windows PowerShell da página [Downloads](https://azure.microsoft.com/downloads/).

- Alguém que seja proficiente na configuração de seu hardware de VPN. Você não poderá usar os scripts VPN gerados automaticamente no Portal de Clássico do Azure para configurar os dispositivos VPN. Isso significa que você precisará ter um grande conhecimento de como configurar seu dispositivo VPN ou trabalhar com alguém que tenha.

- Os intervalos de endereços IP que você deseja usar na sua rede virtual (se ainda não tiver criado uma).

- Os intervalos de endereços IP para cada um dos sites de rede local aos quais se conectará. Você precisará certificar-se de que os intervalos de endereço IP para cada um dos sites de rede local aos quais quer se conectar não se sobrepõem. Caso contrário, o Portal Clássico do Azure ou a API REST rejeitará a configuração que está sendo carregada. Por exemplo, se você tiver dois sites de rede local que contenham o intervalo de endereço IP 10.2.3.0/24 e tiver um pacote com um endereço de destino 10.2.3.3, o Azure não saberá para qual site você deseja enviar o pacote porque os intervalos de endereço estão sobrepostos. Para evitar problemas de roteamento, o Azure não permite carregar um arquivo de configuração com sobreposição de intervalos.

## Criar sua rede virtual e o gateway

1. **Crie uma VPN site a site com um gateway de roteamento dinâmico (baseado em rotas).** Se você já tiver uma, ótimo! Continue em [Exporte as definições da configuração de rede virtual](#export). Caso contrário, faça o seguinte:

	**Se você já tem uma rede virtual site a site, mas ela tem um gateway de roteamento estático (baseado em políticas):** Etapa 1: altere o tipo do gateway para roteamento dinâmico. Uma VPN de múltiplos sites requer um gateway de roteamento dinâmico. Para alterar o tipo de gateway, você precisará primeiro excluir o gateway existente e, em seguida, criar um novo. Para obter instruções, confira [Change a VPN Gateway Routing Type](vpn-gateway-configure-vpn-gateway-mp.md/#how-to-change-your-vpn-gateway-type). Etapa 2: configure o novo gateway e crie o túnel da VPN. Para obter instruções, confira [Configure um gateway de VPN no Portal Clássico do Azure](vpn-gateway-configure-vpn-gateway-mp.md). Primeiramente, altere o tipo de gateway para roteamento dinâmico.

	**Se você não tem uma rede virtual site a site:** Etapa 1: crie a sua rede virtual site a site usando estas instruções: [Criar uma rede virtual com uma conexão VPN site a site usando o Portal Clássico do Azure](vpn-gateway-site-to-site-create.md). Etapa 2: configure um gateway de roteamento dinâmico usando estas instruções: [Configure a VPN Gateway](vpn-gateway-configure-vpn-gateway-mp.md). Lembre-se de selecionar **roteamento dinâmico** para o tipo de gateway.

2. **<a name="export"></a>Exporte as definições da configuração de rede virtual.** Para exportar o arquivo de configuração da rede, confira [Para exportar configurações da rede](../virtual-network/virtual-networks-using-network-configuration-file.md). O arquivo exportado será usado usado para configurar as novas definições de múltiplos sites.

3. **Abra o arquivo de configuração de rede.** Abra o arquivo de configuração de rede que você baixou na última etapa. Use qualquer editor de xml que desejar. O arquivo deve ser semelhante ao seguinte:

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

4. **Adicione referências a múltiplos sites ao arquivo de configuração de rede**. Ao adicionar ou remover informações de referência do site, alterações de configuração serão feitas em ConnectionsToLocalNetwork/LocalNetworkSiteRef. A adição de uma nova referência de site local faz com que o Azure crie um novo túnel. No exemplo abaixo, a configuração de rede é uma conexão de site único.

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

5. **Salve o arquivo de configuração de rede e importe-o.** Para importar o arquivo de configuração da rede, consulte [Para importar as definições da sua rede](../virtual-network/../virtual-network/virtual-networks-using-network-configuration-file.md#export-and-import-virtual-network-settings-using-the-management-portal). Ao importar esse arquivo com as alterações, os novos túneis serão adicionados. Os túneis usarão o gateway dinâmico que você criou anteriormente.

6. **Baixe as chaves pré-compartilhadas para os túneis de VPN.** Depois de adicionar os novos túneis, use o cmdlet do PowerShell Get-AzureVNetGatewayKey para obter as chaves pré-compartilhadas IPsec/IKE para cada túnel.

	Por exemplo:

		Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"

		Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"

	Se preferir, você também pode usar o API REST *Obter chave compartilhada de gateway de rede* para obter as chaves pré-compartilhadas.

## Verificar as conexões

**Verifique o status do túnel de múltiplos sites.** Depois de baixar as chaves para cada túnel, é recomendável verificar as conexões. Use *Get-AzureVnetConnection* para obter uma lista dos túneis de rede virtual, conforme mostrado no exemplo a seguir. VNet1 é o nome do VNet.

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

<!---HONumber=AcomDC_0128_2016-->