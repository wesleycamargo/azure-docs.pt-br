<properties 
	pageTitle="Como configurar o suporte de Rede Virtual para um Cache Redis do Azure Premium | Microsoft Azure" 
	description="Saiba como criar e gerenciar o suporte de Rede Virtual para as instâncias da camada Premium do Cache Redis do Azure" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2016" 
	ms.author="sdanie"/>

# Como configurar o suporte de Rede Virtual para um Cache Redis do Azure Premium
O Cache Redis do Azure apresenta diferentes ofertas de cache que fornecem flexibilidade na escolha do tamanho e dos recursos do cache, incluindo o nova camada Premium.

A camada Premium do Cache Redis do Azure inclui o suporte a clustering, persistência e VNet (rede virtual). Uma VNet é uma rede privada na nuvem. Quando uma instância do Cache Redis do Azure é configurada com uma Vnet, ela não é publicamente endereçável e pode ser acessada apenas de máquinas virtuais e aplicativos na VNet. Este artigo descreve como configurar o suporte de rede virtual para uma instância Premium do Cache Redis do Azure.

>[AZURE.NOTE] O Cache Redis do Azure dá suporte a VNets clássicas e ARM.

Para obter informações sobre outros recursos de cache premium, veja [Como configurar a persistência para um Cache Redis do Azure Premium](cache-how-to-premium-persistence.md) e [Como configurar o clustering para um Cache Redis do Azure Premium](cache-how-to-premium-clustering.md).

## Por que a VNet?
A implantação da [VNet (Rede Virtual) do Azure](https://azure.microsoft.com/services/virtual-network/) fornece isolamento e segurança avançados para o Cache Redis do Azure, bem como sub-redes, políticas de controle de acesso e outros recursos para restringir ainda mais o acesso ao Cache Redis do Azure.

## Suporte de rede virtual
O suporte da VNet (Rede Virtual) é configurado na folha **Novo Cache Redis** durante a criação do cache.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Depois de selecionar um tipo de preço Premium, você pode configurar a integração VNet do Cache Redis do Azure selecionando uma VNet que está na mesma assinatura e localização que seu cache. Para usar uma nova VNet, crie-a primeiro seguindo as etapas em [Criar uma Rede Virtual usando o portal do Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou [Criar uma rede virtual (clássica) usando o portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md) e volte para a folha **Novo Cache Redis** para criar e configurar seu cache Premium.

Para configurar a VNet para seu novo cache, clique em **Rede Virtual** na folha **Novo Cache Redis** e selecione a VNet desejada na lista suspensa.

![Rede virtual][redis-cache-vnet]

Selecione a sub-rede desejada da lista suspensa **Sub-rede** e especifique o **Endereço IP estático** desejado. Se você estiver usando uma VNet clássica o campo **Endereço IP estático** será opcional e se não for especificado nenhum, será escolhido um da sub-rede selecionada.

>[AZURE.IMPORTANT] Ao implantar um Cache Redis do Azure em uma VNet ARM, o cache deve estar em uma sub-rede dedicada que não contém nenhum outro recurso, exceto o Cache Redis do Azure. Se for feita uma tentativa de implantar um Cache Redis do Azure em uma VNet ARM para uma sub-rede que contém outros recursos, a implantação falhará.

![Rede virtual][redis-cache-vnet-ip]

>[AZURE.IMPORTANT] Os quatro primeiros endereços em uma sub-rede são reservados e não podem ser usados. Para obter mais informações, consulte [Existem restrições quanto ao uso de endereços IP dentro dessas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Após o cache ser criado, você pode exibir a configuração da VNet clicando em **Rede Virtual** na folha **Configurações**.

![Rede virtual][redis-cache-vnet-info]


Para se conectar à sua instância de Cache Redis do Azure usando uma VNet, especifique o nome do host do cache na cadeia de conexão, conforme mostrado no exemplo a seguir.

	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
	    return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
	});
	
	public static ConnectionMultiplexer Connection
	{
	    get
	    {
	        return lazyConnection.Value;
	    }
	}

## Perguntas frequentes sobre a Vnet do Cache Redis do Azure

A lista a seguir contém respostas para perguntas frequentes sobre a escala do Cache Redis do Azure.

-	[Quais são alguns problemas comuns de configuração incorreta no Cache Redis do Azure e nas VNets?](#what-are-some-common-misconfiguration-issues-with-azure-redis-cache-and-vnets)
-	[Posso usar VNets com um cache básico ou Standard?](#can-i-use-vnets-with-a-standard-or-basic-cache)
-	[Por que a criação de um cache Redis falha em algumas sub-redes, mas não em outras?](#why-does-creating-a-redis-cache-fail-in-some-subnets-but-not-others)


## Quais são alguns problemas comuns de configuração incorreta no Cache Redis do Azure e nas VNets?

Quando o Cache Redis do Azure estiver hospedado em uma VNet, as portas mostradas na tabela a seguir serão usadas. Se essas portas estiverem bloqueadas, é possível que o cache não funcione corretamente. A existência de uma ou mais dessas portas bloqueadas é o problema mais comum de configuração incorreta no uso do Cache Redis do Azure em uma VNet.

| Porta(s) | Direção | Protocolo de Transporte | Finalidade | IP Remoto |
|-------------|------------------|--------------------|-----------------------------------------------------------------------------------|-------------------------------------|
| 80, 443 | Saída | TCP | Dependências de Redis no Armazenamento do Azure/PKI (Internet) | * |
| 53 | Saída | TCP/UDP | Dependências Redis no DNS (Internet/Rede Virtual) | * |
| 6379, 6380 | Entrada | TCP | Comunicação do cliente com o Redis, Balanceamento de Carga do Azure | REDE\_VIRTUAL, BALANCEADORDECARGA\_AZURE |
| 8443 | Entrada/Saída | TCP | Detalhe de implementação para Redis | REDE\_VIRTUAL |
| 8500 | Entrada | TCP/UDP | Balanceamento de Carga do Azure | BALANCEADORDECARGA\_AZURE |
| 10221-10231 | Entrada/Saída | TCP | Detalhe de implementação para Redis (pode restringir o ponto de extremidade remoto para REDE\_VIRTUAL) | REDE\_VIRTUAL, BALANCEADORDECARGA\_AZURE |
| 13000-13999 | Entrada | TCP | Comunicação do cliente com Clusters Redis, Balanceamento de Carga do Azure | REDE\_VIRTUAL, BALANCEADORDECARGA\_AZURE |
| 15000-15999 | Entrada | TCP | Comunicação do cliente com Clusters Redis, Balanceamento de Carga do Azure | REDE\_VIRTUAL, BALANCEADORDECARGA\_AZURE |
| 16001 | Entrada | TCP/UDP | Balanceamento de Carga do Azure | BALANCEADORDECARGA\_AZURE |
| 20226 | Entrada + Saída | TCP | Detalhe de implementação para Clusters Redis | REDE\_VIRTUAL |


Há requisitos de conectividade de rede para o Cache Redis do Azure que podem não ser atendidos inicialmente em uma rede virtual. O Cache Redis do Azure requer todos os itens a seguir para funcionar corretamente quando usado em uma rede virtual.

-  Conectividade de rede de saída para pontos de extremidade do Armazenamento do Azure em todo o mundo. Isso inclui os pontos de extremidade localizados na mesma região que a instância do Cache Redis do Azure, bem como os pontos de extremidade de armazenamento localizados em **outras** regiões do Azure. Os pontos de extremidade do Armazenamento do Azure são resolvidos nos seguintes domínios DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* e *file.core.windows.net*. 
-  Conectividade de rede de saída para *ocsp.msocsp.com*, *mscrl.microsoft.com* e *crl.microsoft.com*. Necessária para dar suporte à funcionalidade SSL.
-  A configuração DNS para a rede virtual deve ser capaz de resolver todos os pontos de extremidade e domínios mencionados nos pontos anteriores. Esses requisitos de DNS podem ser atendidos, garantindo que uma infraestrutura de DNS válida seja configurada e mantida para a rede virtual.



### Posso usar VNets com um cache básico ou Standard?

As VNets podem ser usadas apenas com os caches Premium.

### Por que a criação de um cache Redis falha em algumas sub-redes, mas não em outras?

Se você estiver implantando um Cache Redis do Azure em uma VNet ARM, o cache deverá estar em uma sub-rede dedicada que não contém nenhum outro tipo de recurso. Se for feita uma tentativa de implantar um Cache Redis do Azure em uma sub-rede VNet ARM que contém outros recursos, a implantação falhará. É necessário excluir os recursos existentes na sub-rede antes de ser possível criar um novo Cache Redis.

Você pode implantar vários tipos de recursos para uma VNet clássica, contanto que tenha endereços IP suficientes disponíveis.

## Usar a Rota Expressa com Cache Redis do Azure

Os clientes podem conectar um circuito da [Rota Expressa do Azure](https://azure.microsoft.com/services/expressroute/) à infraestrutura de rede virtual, estendendo, assim, a rede local até o Azure.

Por padrão, um circuito da Rota Expressa recentemente criado anuncia uma rota padrão que permite conectividade de saída da Internet. Com esse cliente de configuração, os aplicativos poderão se conectar a outros pontos de extremidade do Azure, inclusive o Cache Redis do Azure.

No entanto, uma configuração de cliente comum é definir sua própria rota padrão (0.0.0.0/0), que força o tráfego de saída da Internet para o fluxo local. Esse fluxo de tráfego invariavelmente interrompe a conectividade com o Cache Redis do Azure porque o tráfego de saída é bloqueado localmente ou convertido em NAT para um conjunto irreconhecível de endereços que não funcionam mais com vários pontos de extremidade do Azure.

A solução é definir uma (ou mais) UDRs (rotas definidas pelo usuário) na sub-rede que contenham o Cache Redis do Azure. Uma UDR define rotas específicas de sub-rede que serão consideradas no lugar da rota padrão.

Se possível, é recomendável usar a seguinte configuração:

- A configuração de Rota Expressa anuncia 0.0.0.0/0 e, por padrão, encapsula à força todo o tráfego de saída no local.
- A UDR aplicada à sub-rede que contém o Cache Redis do Azure define 0.0.0.0/0 com um tipo do próximo salto da Internet (veja um exemplo disso no final deste artigo).

O efeito combinado dessas etapas é que a UDR do nível de sub-rede terá precedência sobre o túnel forçado da Rota Expressa, garantindo acesso de Internet de saída do Cache Redis do Azure.

Embora se conectar a uma instância de Cache Redis do Azure de um aplicativo local usando a Rota Expressa não seja um cenário de uso típico por motivos de desempenho (para o melhor desempenho, os clientes do Cache Redis do Azure devem estar na mesma região que o Cache Redis do Azure), nesse cenário, o caminho da rede de saída não pode se deslocar por proxies corporativos internos, e também não pode ser encapsulado a força para os locais. Isso altera o endereço NAT eficiente de tráfego de rede de saída do Cache Redis do Azure. Alterar o endereço NAT do tráfego de rede de saída de uma instância do Cache Redis do Azure causará falhas de conectividade em muitos dos pontos de extremidade listados acima. Isso resulta em tentativas de criação do Cache Redis do Azure com falha.

**IMPORTANTE:** as rotas definidas em uma UDR **devem** ser específicas o suficiente para ter precedência sobre todas as rotas anunciadas pela configuração da Rota Expressa. O exemplo a seguir usa o intervalo de endereço 0.0.0.0/0 amplo, e potencialmente pode ser substituído por acidente pelos anúncios de rota usando intervalos de endereços mais específicos.

**MUITO IMPORTANTE:** o Cache Redis do Azure não tem suporte com configurações de Rota Expressa que **incorretamente cruzam anúncios de rotas do caminho de emparelhamento público para o caminho de emparelhamento privado**. As configurações de Rota Expressa com emparelhamento público definido receberão anúncios de rota da Microsoft para um grande conjunto de intervalos de endereços IP do Microsoft Azure. Se esses intervalos de endereços forem incorretamente anunciados de modo cruzado no caminho de emparelhamento privado, o resultado final será que todos os pacotes de saída de rede da sub-rede da instância do Cache Redis do Azure serão incorretamente encapsulados à força em uma infraestrutura de rede local do cliente. Esse fluxo de rede interromperá o Cache Redis do Azure. A solução para esse problema é parar as rotas de anúncios cruzados do caminho de emparelhamento público para o caminho de emparelhamento particular.

Informações preliminares sobre as rotas definidas pelo usuário estão disponíveis nesta [visão geral](../virtual-network/virtual-networks-udr-overview.md).

Para obter mais informações sobre o Rota Expressa, consulte [Visão geral técnica do Rota Expressa](../expressroute/expressroute-introduction.md).

## Próximas etapas
Aprenda a usar mais recursos de cache premium.

-	[Como configurar a persistência para um Cache Redis do Azure Premium](cache-how-to-premium-persistence.md)
-	[Como configurar o clustering para um Cache Redis do Azure Premium](cache-how-to-premium-clustering.md)
-	[Importar e Exportar dados no Cache Redis do Azure](cache-how-to-import-export-data.md)





  
<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

<!---HONumber=AcomDC_0601_2016-->