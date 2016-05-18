<properties 
	pageTitle="Como configurar o suporte de Rede Virtual para um Cache Redis do Azure Premium" 
	description="Saiba como criar e gerenciar o suporte de Rede Virtual para as instâncias da camada Premium do Cache Redis do Azure" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2016" 
	ms.author="sdanie"/>

# Como configurar o suporte de Rede Virtual para um Cache Redis do Azure Premium
O Cache Redis do Azure apresenta diferentes ofertas de cache que fornecem flexibilidade na escolha do tamanho e dos recursos do cache, incluindo o nova camada Premium.

A camada premium do Cache Redis do Azure inclui clustering, persistência e suporte de VNET (rede virtual). Uma VNET é uma representação da sua própria rede na nuvem. Quando uma instância do Cache Redis do Azure é configurada com uma VNET, ela não é publicamente endereçável e pode ser acessada apenas de clientes na VNET. Este artigo descreve como configurar o Suporte de Rede Virtual para uma instância premium do Cache Redis do Azure.

Para obter informações sobre outros recursos de cache premium, veja [Como configurar a persistência para um Cache Redis do Azure Premium](cache-how-to-premium-persistence.md) e [Como configurar o clustering para um Cache Redis do Azure Premium](cache-how-to-premium-clustering.md).

## Por que a VNET?
A implantação da [VNET (Rede Virtual do Azure)](https://azure.microsoft.com/services/virtual-network/) fornece isolamento e segurança avançados para o Cache Redis do Azure, bem como sub-redes, políticas de controle de acesso e outros recursos para restringir ainda mais o acesso ao Cache Redis do Azure.

## Suporte de rede virtual
O suporte da VNET (Rede Virtual) é configurado na folha **Novo Cache Redis** durante a criação do cache. Para criar um cache, entre no [Portal do Azure](https://portal.azure.com) e clique em **Novo**-> **Dados + Armazenamento** > **Cache Redis**.

![Criar um Cache Redis][redis-cache-new-cache-menu]

Para configurar o suporte de VNET, primeiro selecione um dos caches **Premium** na folha **Escolha o Tipo de preço**.

![Escolha sua camada de preço][redis-cache-premium-pricing-tier]

A integração VNET do Cache Redis do Azure é configurada na folha **Rede Virtual (clássica)**. Aqui, você pode selecionar uma VNET clássica existente. Para usar uma nova VNET, siga as etapas em [Criar uma rede virtual (clássica) usando o Portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) e volte para a folha **Rede Virtual do Cache Redis** para selecioná-la.

>[AZURE.NOTE] O Cache Redis do Azure funciona com VNETs clássicas. Para obter informações sobre como criar uma VNET clássica, confira [Criar uma rede virtual (clássica) usando o Portal do Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Para obter informações sobre como conectar redes virtuais clássicas a VNETs ARM, confira [Conectando redes virtuais clássicas a novas redes virtuais](../virtual-network/virtual-networks-arm-asm-s2s.md).

Clique em **Rede Virtual (clássica)** na folha **Novo Cache Redis** e selecione a rede virtual desejada na lista suspensa para selecionar e configurar sua rede virtual.

![Rede virtual][redis-cache-vnet]

Na lista suspensa **Sub-rede**, selecione a sub-rede desejada.

![Rede virtual][redis-cache-vnet-ip]

O campo **Endereço IP estático** é opcional. Se nenhum for especificado aqui, um será escolhido da sub-rede selecionada. Se deseja um endereço IP estático específico, digite o **Endereço IP estático** desejado e clique em **OK** para salvar a configuração de VNET. Se o IP estático selecionado já estiver sendo usado, uma mensagem de erro será exibida.

Após o cache ser criado, você pode exibir o endereço IP e outras informações sobre a rede virtual clicando em **Rede Virtual** na folha **Configurações**.

![Rede virtual][redis-cache-vnet-info]

>[AZURE.IMPORTANT] Para acessar sua instância do Cache Redis do Azure ao usar uma VNET, transmita o endereço IP estático do cache na VNET como o primeiro parâmetro e transmita um parâmetro `sslhost` com o ponto de extremidade do cache. No exemplo a seguir, o endereço IP estático é `172.160.0.99` e o ponto de extremidade do cache é `contoso5.redis.cache.windows.net`.

	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
	    return ConnectionMultiplexer.Connect("172.160.0.99,sslhost=contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
	});
	
	public static ConnectionMultiplexer Connection
	{
	    get
	    {
	        return lazyConnection.Value;
	    }
	}

## Perguntas frequentes sobre a VNET do Cache Redis do Azure

A lista a seguir contém respostas para perguntas frequentes sobre a escala do Cache Redis do Azure.

## Quais são alguns problemas comuns de configuração incorreta no Cache Redis do Azure e nas VNETs?

Quando o Cache Redis do Azure estiver hospedado em uma Rede Virtual, as portas mostradas na tabela a seguir serão usadas. Se essas portas estiverem bloqueadas, é possível que o cache não funcione corretamente. A existência de uma ou mais dessas portas bloqueadas é o problema mais comum de configuração incorreta no uso do Cache Redis do Azure em uma Rede Virtual.

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




## Posso usar VNETs com um cache básico ou standard?

As VNETs podem ser usadas apenas com os caches premium.

## Próximas etapas
Aprenda a usar mais recursos de cache premium.

-	[Como configurar a persistência para um Cache Redis do Azure Premium](cache-how-to-premium-persistence.md)
-	[Como configurar o clustering para um Cache Redis do Azure Premium](cache-how-to-premium-clustering.md)





  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-vnet/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-vnet/redis-cache-premium-pricing-tier.png

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png

<!---HONumber=AcomDC_0504_2016-->