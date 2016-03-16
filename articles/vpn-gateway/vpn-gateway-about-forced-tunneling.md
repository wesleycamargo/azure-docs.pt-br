<properties pageTitle="Configurar túnel forçado para Gateways de VPN usando o PowerShell | Microsoft Azure" description="Caso tenha uma rede virtual do modelo de implantação clássica com um gateway de VPN entre locais, será possível redirecionar ou “forçar” todo o tráfego da Internet de volta para sua instalação local." services="vpn-gateway" documentationCenter="na" authors="cherylmc" manager="carmonm" editor="" tags="azure-service-management"/>
<tags  
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/24/2016"
   ms.author="cherylmc" />

# Configurar o túnel forçado para Gateways de VPN usando o modelo de implantação clássica

> [AZURE.SELECTOR]
- [PowerShell – Gerenciamento de Serviços](vpn-gateway-about-forced-tunneling.md)
- [PowerShell – Resource Manager](vpn-gateway-forced-tunneling-rm.md)

O túnel forçado permite redirecionar ou "forçar" todo o tráfego direcionado para a Internet de volta para seu local por meio de um túnel VPN de site a site para inspeção e auditoria. Esse é um requisito crítico de segurança para a maioria das políticas de TI empresariais. Sem o túnel forçado, o tráfego direcionado para Internet de suas VMs no Azure sempre percorrerão da infraestrutura de rede do Azure diretamente para a Internet, sem a opção para permitir que você inspecione ou audite o tráfego. O acesso não autorizado à Internet pode levar à divulgação de informações ou outros tipos de violações de segurança.


[AZURE.INCLUDE [vpn-gateway-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)]


**Sobre modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Requisitos e considerações

O túnel forçado no Azure é configurado por meio de Rotas de Rede virtual Definidas pelo Usuário. Redirecionar o tráfego para um site local é expressado como uma Rota Padrão para o gateway de VPN do Azure. A seção a seguir lista a limitação atual da tabela de roteamento e as rotas para uma Rede Virtual do Azure:


-  Cada sub-rede de rede virtual tem uma tabela de roteamento interna do sistema. A tabela de roteamento do sistema tem estes três grupos de rotas:

	- **Rotas locais de Rede Virtual:** diretamente para as VMs de destino na mesma rede virtual
	
	- **Rotas locais:** gateway de VPN do Azure
	
	- **Rota padrão:** diretamente para a Internet. Observe que os pacotes destinados para os endereços IP privados não cobertos pelas duas rotas anteriores serão removidos.


-  Com a liberação de rotas definidas pelo usuário, você poderá criar uma tabela de roteamento para adicionar uma rota padrão e, em seguida, associar a tabela de roteamento às suas sub-redes de VNet para habilitar o túnel forçado nessas sub-redes.

- Você precisa definir um "site padrão" entre sites locais entre locais conectado à rede virtual.

- O túnel forçado deve ser associado a uma Rede Virtual que tem um gateway de VPN de roteamento dinâmico (e não um gateway estático).
 
- O túnel forçado da Rota Expressa não é configurado por meio deste mecanismo, mas é habilitado por meio do anúncio de uma rota padrão por meio de sessões de emparelhamento via protocolo BGP da Rota Expressa. Confira a [Documentação da Rota Expressa](https://azure.microsoft.com/documentation/services/expressroute/) para saber mais.



## Visão geral de configuração

No exemplo acima, a sub-rede Front-end não é um túnel forçado. As cargas de trabalho na sub-rede do front-end podem continuar a aceitar e a responder diretamente às solicitações de clientes da Internet. As sub-redes de Camada intermediária e Back-end são túneis forçados.

As conexões de saída dessas duas sub-redes com a Internet serão forçadas ou redirecionadas de volta ao site local por meio de túneis de VPN S2S. Isso permite que você restrinja e inspecione o acesso à Internet de suas Máquinas Virtuais ou Serviços de Nuvem no Azure, continuando a habilitar a arquitetura de serviço de várias camadas necessária. Você também tem a opção de aplicar o túnel forçado às redes virtuais inteiras se não houver nenhuma carga de trabalho voltada para a Internet em suas redes virtuais.


![Túnel forçado](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)



## Antes de começar

Verifique se você tem os itens a seguir antes de iniciar a configuração.

- Uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [benefícios para assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou se inscrever para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Uma rede virtual configurada.

- A versão mais recente dos cmdlets do Azure PowerShell usando o Web Platform Installer. Você pode baixar e instalar a versão mais recente da seção **Windows PowerShell** da [página de download](https://azure.microsoft.com/downloads/).

## Configurar o túnel forçado

O procedimento a seguir ajudará você a especificar um túnel forçado em uma rede virtual. As etapas de configuração correspondem ao exemplo de arquivo de configuração (netcfg) de rede da rede virtual abaixo.



	<VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
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
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
	</VirtualNetworkSite>

Neste exemplo, a rede virtual “MultiTier-VNet” tem 3 sub-redes: *Front-end*, *Mid-Tier* e *Back-end*, com 4 conexões entre locais: *DefaultSiteHQ* e 3 *Branches*. As etapas do procedimento definem *DefaultSiteHQ* como a conexão de site padrão para o túnel forçado e configuram as sub-redes *Mid-Tier* e *Back-end* para usarem túnel forçado.


1. Crie uma tabela de roteamento. Use o cmdlet a seguir para criar sua tabela de rotas.

		New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"

1. Adicione uma rota padrão à tabela de roteamento.

	O exemplo de cmdlet a seguir adiciona uma rota padrão à tabela de roteamento criada na Etapa 1. Observe que a única rota com suporte é o prefixo de destino de "0.0.0.0/0" para o próximo salto "VPNGateway".
 
		Set-AzureRoute –RouteTableName "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway

1. Associe a tabela de roteamento às sub-redes.

	Depois que uma tabela de roteamento é criada e uma rota é adicionada, use o cmdlet abaixo para adicionar ou associar a tabela de rotas a uma sub-rede de rede virtual. Os exemplos abaixo adicionam a tabela de rota "MyRouteTable" para as sub-redes Intermediária e Back-end de rede virtual de várias camadas.

		Set-AzureSubnetRouteTable -VNetName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"

		Set-AzureSubnetRouteTable -VNetName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"

1. Atribua um site padrão ao túnel forçado.

	Na etapa anterior, os scripts de cmdlet de exemplo criaram a tabela de roteamento e associou a tabela de rotas a duas sub-redes de rede virtual. A etapa restante é selecionar um site local entre as conexões de vários locais da rede virtual como o site padrão ou túnel.

		$DefaultSite = @("DefaultSiteHQ")
		Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"

## Cmdlets do PowerShell adicionais

Abaixo estão alguns cmdlets adicionais do PowerShell que podem ser úteis ao trabalhar com configurações de túnel forçado.

**Para excluir uma tabela de rotas:**

	Remove-AzureRouteTable -RouteTableName <routeTableName>

**Para listar uma tabela de rotas:**

	Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]

**Para excluir uma rota de uma tabela de rotas:**

	Remove-AzureRouteTable –Name <routeTableName>

**Para remover uma rota de uma sub-rede:**

	Remove-AzureSubnetRouteTable –VNetName <virtualNetworkName> -SubnetName <subnetName>

**Para listar a tabela de rotas associadas a uma sub-rede:**
	
	Get-AzureSubnetRouteTable -VNetName <virtualNetworkName> -SubnetName <subnetName>

**Para remover um site padrão de um gateway de VPN de rede virtual:**

	Remove-AzureVnetGatewayDefaultSites -VNetName <virtualNetworkName>

<!---HONumber=AcomDC_0302_2016-->