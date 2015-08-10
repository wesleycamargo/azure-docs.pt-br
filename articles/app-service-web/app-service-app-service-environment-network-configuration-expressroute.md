<properties 
	pageTitle="Detalhes da configuração de rede para trabalhar com a Rota Expressa" 
	description="Detalhes da configuração de rede para executar Ambientes de Serviço de Aplicativo em uma rede virtual conectada a um circuito da Rota Expressa." 
	services="app-service\web" 
	documentationCenter="" 
	authors="stefsch" 
	manager="nirma" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/30/2015" 
	ms.author="stefsch"/>

# Detalhes da configuração de rede para Ambientes de Aplicativo de Serviço com a Rota Expressa 

## Visão geral ##
Os clientes podem conectar um circuito da [Rota Expressa do Azure][ExpressRoute] à infraestrutura de rede virtual, estendendo, assim, a rede local até o Azure. Um Ambiente de Serviço de Aplicativo pode ser criado em uma sub-rede dessa infraestrutura de [rede virtual][virtualnetwork]. Os aplicativos em execução no Ambiente de Serviço de Aplicativo podem então estabelecer conexões seguras com recursos de back-end acessíveis apenas pela conexão de Rota Expressa.

## Conectividade de rede necessária ##
Há requisitos de conectividade de rede para Ambientes de Serviço de Aplicativo que não podem ser inicialmente atendidos em uma rede virtual conectada a uma Rota Expressa.

Os Ambientes de Serviço de Aplicativo exigem tudo o que se segue para que funcionem corretamente:


-  Conectividade de rede de saída para recursos de Banco de Dados SQL e Armazenamento do Azure localizados na mesma região que o Ambiente de Serviço de Aplicativo. Esse caminho de rede não pode viajar por meio de proxies corporativos internos, pois ao fazer isso, provavelmente mudaria o endereço NAT efetivo do tráfego de rede de saída. Alterar o endereço NAT do tráfego de rede de saída de um Ambiente de Serviço de Aplicativo direcionado aos pontos de extremidade do Banco de Dados SQL e Armazenamento do Azure causaria falhas de conectividade.
-  A configuração DNS para a rede virtual deve ser capaz de resolver pontos de extremidade nos seguintes domínios controlados pelo Azure: **.file.core.windows.net\*, **.blob.core.windows.net\*, **.database.windows.net\*. -  A configuração DNS para a rede virtual deve permanecer estável sempre que os Ambientes de Serviço de Aplicativo forem criados, bem como durante a reconfiguração e as mudanças de escala nos Ambientes de Serviço de Aplicativo.   
-  O acesso de rede de entrada a portas obrigatórias para Ambientes de Serviço de Aplicativo deve ser permitido, conforme descrito neste [artigo][requiredports].

O requisito de DNS pode ser atendido ao garantir uma configuração DNS válida para a rede virtual.

Os requisitos de acesso de rede de entrada podem ser atendidos ao configurar um [grupo de segurança de rede][NetworkSecurityGroups] na sub-rede do Ambiente de Serviço de Aplicativo para permitir o acesso obrigatório, conforme descrito neste [artigo][requiredports].

## Habilitando a conectividade de rede de saída para um Ambiente de Serviço de Aplicativo##
Por padrão, um circuito da Rota Expressa recentemente criado anuncia uma rota padrão que permite conectividade de saída da Internet. Com essa configuração, um Ambiente de Serviço de Aplicativo será capaz de se conectar a outros pontos de extremidade do Azure.

No entanto, uma configuração de cliente comum é definir sua própria rota padrão, que força o tráfego de saída da Internet para o fluxo local por meio da infraestrutura de firewall/proxy de um cliente. Esse fluxo de tráfego invariavelmente interrompe os Ambientes de Serviço de Aplicativo porque o tráfego de saída é bloqueado no local ou seu endereço de rede é convertido em um conjunto irreconhecível de endereços que não funcionam mais com vários pontos de extremidade do Azure.

A solução é definir uma (ou mais) UDRs (rotas definidas pelo usuário) na sub-rede que contém o Ambiente de Serviço de Aplicativo. Uma UDR define rotas específicas de sub-rede que serão consideradas no lugar da rota padrão.

Outras informações sobre rotas definidas pelo usuário estão disponíveis nesta [visão geral][UDROverview].

Detalhes sobre como criar e configurar rotas definidas pelo usuário estão disponíveis neste [Guia de Instruções][UDRHowTo].

## Exemplo de configuração da UDR para um Ambiente de Serviço de Aplicativo ##

**Pré-requisitos**

1. Instale o Azure PowerShell mais recente da [página de Downloads do Azure][AzureDownloads] (com a data de junho de 2015 ou posterior). Em "Ferramentas de linha de comando", há um link "Instalar" em "Windows Powershell" que instalará os cmdlets mais recentes do PowerShell.

2. É recomendável que uma sub-rede exclusiva seja criada para uso exclusivo por um Ambiente de Serviço de Aplicativo. Isso garante que as UDRs aplicadas à sub-rede abrirão apenas tráfego de saída para o Ambiente de Serviço de Aplicativo.
3. **Importante**: não implante o Ambiente de Serviço de Aplicativo **até** que as etapas de configuração a seguir sejam executadas. Isso garante que a conectividade de rede de saída esteja disponível antes da tentativa de implantar um Ambiente de Serviço de Aplicativo.

**Etapa 1: Criar uma tabela de rotas nomeada**

O trecho a seguir cria uma tabela de rota chamada "DirectInternetRouteTable" na região oeste dos EUA no Azure:

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**Etapa 2: Criar uma ou mais rotas na tabela de rotas**

Você precisará adicionar uma ou mais rotas à tabela de rotas para permitir acesso de saída da Internet. O exemplo abaixo adiciona rotas suficientes para cobrir todos os endereços possíveis do Azure usados na região oeste dos EUA.

    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 1' -AddressPrefix 23.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 2' -AddressPrefix 40.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 3' -AddressPrefix 65.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 4' -AddressPrefix 104.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 5' -AddressPrefix 137.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 6' -AddressPrefix 138.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 7' -AddressPrefix 157.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 8' -AddressPrefix 168.0.0.0/8 -NextHopType Internet
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 9' -AddressPrefix 191.0.0.0/8 -NextHopType Internet


Para obter uma lista abrangente e atualizada dos intervalos CIDR em uso pelo Azure, você pode baixar um arquivo Xml contendo todos os intervalos do [Centro de Download da Microsoft][DownloadCenterAddressRanges]

**Observação:** em algum ponto, um CIDR abreviado de 0.0.0.0/0 estará disponível para uso no parâmetro *AddressPrefix*. Essa abreviação se compara a “todos os endereços da Internet”. Por enquanto, os desenvolvedores precisarão usar um conjunto amplo de intervalos de CIDR, suficiente para cobrir todos os possíveis intervalos de endereços do Azure usados na região em que o Ambiente de Serviço de Aplicativo foi implantado.

**Etapa 3: Associar a tabela de rotas à sub-rede que contém o Ambiente de Serviço de Aplicativo**

A última etapa da configuração é associar a tabela de rotas à sub-rede em que o Ambiente de Serviço de Aplicativo será implantado. O comando a seguir associa "DirectInternetRouteTable" a "ASESubnet" que, no fim, conterá um Ambiente de Serviço de Aplicativo.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**Etapa 4: Etapas finais**

Depois que a tabela de rotas estiver associada à sub-rede, é recomendável primeiro testar e confirmar o efeito desejado. Por exemplo, implante uma máquina virtual na sub-rede e verifique se:


- O tráfego de saída para pontos de extremidade do Azure não está fluindo pelo circuito da Rota Expressa.
- As pesquisas DNS para pontos de extremidade do Azure estão sendo resolvidas corretamente. 

Depois que as etapas acima forem confirmadas, você poderá prosseguir com a criação de um Ambiente de Serviço de Aplicativo!

## Introdução

Para se familiarizar com os ambientes de serviço de aplicativo, consulte [Introdução ao ambiente do serviço de aplicativo][IntroToAppServiceEnvironment]

Para obter mais informações sobre a plataforma de Serviço de Aplicativo do Azure, consulte [Serviço de Aplicativo do Azure][AzureAppService].

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureDownloads]: http://azure.microsoft.com/downloads/
[DownloadCenterAddressRanges]: http://www.microsoft.com/pt-br/download/details.aspx?id=41653
[NetworkSecurityGroups]: https://msdn.microsoft.com/library/azure/dn848316.aspx
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
 

<!-- IMAGES -->

<!---HONumber=July15_HO5-->