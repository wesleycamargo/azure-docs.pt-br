<properties 
	pageTitle="Como configurar o clustering do Redis para um Cache Redis do Azure Premium" 
	description="Saiba como criar e gerenciar o clustering do Redis para as instâncias da camada Premium do Cache Redis do Azure" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/16/2015" 
	ms.author="sdanie"/>

# Como configurar o clustering do Redis para um Cache Redis do Azure Premium
O Cache Redis do Azure apresenta diferentes ofertas de cache que fornecem flexibilidade na escolha do tamanho e dos recursos do cache, incluindo o nova camada Premium.

A camada premium do Cache Redis do Azure inclui clustering, persistência e suporte de rede virtual. Este artigo descreve como configurar o clustering em uma instância premium do Cache Redis do Azure.

Para obter informações sobre outros recursos de cache premium, veja [Como configurar a persistência para um Cache Redis do Azure Premium](cache-how-to-premium-persistence.md) e [Como configurar o suporte de Rede Virtual para um Cache Redis do Azure Premium](cache-how-to-premium-vnet.md).

## O que é o Cluster Redis?
O Cache Redis do Azure oferece o cluster Redis como [implementado no Redis](http://redis.io/topics/cluster-tutorial). Com o Cluster Redis, você obtém os seguintes benefícios:

-	A capacidade de dividir automaticamente o conjunto de dados entre vários nós. 
-	A capacidade de continuar as operações quando um subconjunto dos nós estiver apresentando falhas ou não conseguir se comunicar com o restante do cluster. 
-	Mais taxa de transferência: a taxa de transferência aumenta linearmente à medida que o número de fragmentos aumenta. 
-	Mais tamanho de memória: aumenta linearmente à medida que o número de fragmentos aumenta.  

Consulte [Perguntas frequentes sobre o Cache Redis do Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) para obter mais detalhes sobre o tamanho, taxa de transferência e largura de banda com caches premium.

No Azure, o cluster Redis é oferecido como um modelo primário/de réplica em que cada fragmento tem um par primário/de réplica, com a replicação gerenciada pelo serviço de Cache Redis do Azure.

## Clustering
O clustering é habilitado na folha **Novo Cache Redis** durante a criação do cache. Para criar um cache, entre no [Portal do Azure](https://portal.azure.com) e clique em **Novo**->**Dados + Armazenamento**>**Cache Redis**.

![Criar um Cache Redis][redis-cache-new-cache-menu]

Para configurar o clustering, primeiro selecione um dos caches **Premium** na folha **Escolha o tipo de preço**.

![Escolha sua camada de preço][redis-cache-premium-pricing-tier]

O clustering é configurado na folha **Redis Cluster**.

![Clustering][redis-cache-clustering]

Você pode ter até 10 fragmentos no cluster. Clique em **Habilitado** e mova o controle deslizante ou digite um número entre 1 e 10 para a **Contagem de fragmentos** e clique em **OK**.

Cada fragmento é um par de cache primário/de réplica gerenciado pelo Azure, e o tamanho total do cache é calculado multiplicando o número de fragmentos pelo tamanho do cache selecionado no tipo de preço.

![Clustering][redis-cache-clustering-selected]

Depois que o cache for criado, conecte-se a ele e use-o apenas como um cache não clusterizado, e o Redis distribuirá os dados por todos os fragmentos do Cache. Se o diagnóstico for [habilitado](cache-how-to-monitor.md#enable-cache-diagnostics), as métricas serão capturadas separadamente para cada fragmento e poderão ser [exibidas](cache-how-to-monitor.md) na folha Cache Redis.

Para obter um exemplo de código sobre como trabalhar com clustering com o cliente StackExchange.Redis, confira a parte [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) da amostra [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).

>[AZURE.IMPORTANT]Ao se conectar a um Cache Redis do Azure com clustering habilitado usando StackExchange.Redis, você pode enfrentar um problema e receber exceções `MOVE`. Isso ocorre porque ele usa um intervalo curto para o cliente de cache StackExchange.Redis coletar informações sobre os nós no cluster de cache. Essas exceções podem ocorrer se você se conectar ao cache pela primeira vez e fizer imediatamente chamadas para o cache antes do cliente concluir a coleta dessas informações. A maneira mais simples de resolver o problema no aplicativo é conectar-se ao cache e, em seguida, esperar um segundo antes de fazer as chamadas para o cache. Você pode fazer isso adicionando um `Thread.Sleep(1000)`, conforme mostrado no código de exemplo a seguir. Observe que o `Thread.Sleep(1000)` ocorre apenas durante a conexão inicial com o cache. Para saber mais, confira [StackExchange.Redis.RedisServerException - MOVED #248](https://github.com/StackExchange/StackExchange.Redis/issues/248). Uma correção para esse problema está sendo desenvolvida e as atualizações serão postadas aqui.

	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
        // Connect to the Redis cache for the first time
	    var connection =  ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

		// Wait for 1 second
		Thread.Sleep(1000);

		// Return the connected ConnectionMultiplexer
		return connection;
	});
	
	public static ConnectionMultiplexer Connection
	{
	    get
	    {
	        return lazyConnection.Value;
	    }
	}

<a name="cluster-size"></a>
## Alterar o tamanho do cluster em um cache premium em execução

Para alterar o tamanho do cache em um cache premium em execução com clustering habilitado, clique em **Tamanho do Cluster Redis (VISUALIZAÇÃO)**, na folha **Configurações**.

>[AZURE.NOTE]Observe que, enquanto a camada Cache Redis do Azure Premium foi liberada para disponibilidade geral, o recurso de Tamanho do Cluster Redis está atualmente em visualização.

![Tamanho do cluster Redis][redis-cache-redis-cluster-size]

Para alterar o tamanho do cluster, use o controle deslizante ou digite um número entre 1 e 10 na caixa de texto **Contagem de fragmentos** e clique em **OK** para salvar.

## Perguntas frequentes sobre clustering

A lista a seguir contém respostas para perguntas frequentes sobre o clustering do Cache Redis do Azure.

## Preciso fazer alguma alteração no meu aplicativo cliente para usar clustering?

-	Quando o clustering estiver habilitado, somente o banco de dados 0 estará disponível. Se o seu aplicativo cliente usar vários bancos de dados e tentar ler ou gravar em um banco de dados diferente de 0, a seguinte exceção será lançada. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
-	Se você estiver usando [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/), use a versão 1.0.481 ou posterior. É possível se conectar ao cache usando os mesmos [pontos de extremidade, portas e chaves](cache-configure.md#properties) usados ao se conectar a um cache que não tem o clustering habilitado. A única diferença é que todas as leituras e gravações devem ser feitas no banco de dados 0.
	-	Outros clientes podem ter requisitos diferentes. Confira [Todos os clientes do Redis dão suporte ao clustering?](#do-all-redis-clients-support-clustering).
-	Se seu aplicativo usa várias operações de chave em lote em um único comando, todas as chaves devem estar localizadas no mesmo fragmento. Para fazer isso, confira [Como as chaves são distribuídas em um cluster?](#how-are-keys-distributed-in-a-cluster).
-	Se estiver usando o provedor de Estado de Sessão ASP.NET Redis, você deverá usar a versão 2.0.1 ou superior. Confira [Posso usar clustering com os provedores de Estado de Sessão ASP.NET Redis e de Cache de Saída?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers).

## Como as chaves são distribuídas em um cluster?

De acordo com a documentação do [Modelo de distribuição de chaves](http://redis.io/topics/cluster-spec#keys-distribution-model) do Redis: o espaço da chave é dividido em 16384 slots. Cada chave é resumida e atribuída a um desses slots, que são distribuídos entre os nós do cluster. Você pode configurar qual parte da chave é resumida para garantir que várias chaves estejam localizadas no mesmo fragmento usando hashtags.

-	Chaves com marca hash: se alguma parte da chave estiver entre `{` e `}`, somente essa parte da chave estará entre chaves a fim de determinar o slot de hash de uma chave. Por exemplo, as 3 chaves a seguir devem estar localizadas no mesmo fragmento: `{key}1`, `{key}2` e `{key}3`, uma vez que apenas a parte `key` do nome está entre chaves. Para obter uma lista completa das especificações da marca hash de chaves, confira [Keys hash tags](http://redis.io/topics/cluster-spec#keys-hash-tags).
-	Chaves sem uma hashtag: o nome completo da chave é usado para fazer hash. Isso resulta em uma distribuição estatisticamente uniforme entre os fragmentos do cache.

Para obter um melhor desempenho e uma melhor taxa de transferência, recomendamos distribuir as chaves por igual. Se você estiver usando chaves com uma hashtag, é responsabilidade do aplicativo garantir que as chaves sejam distribuídas uniformemente.

Para saber mais, confira [Keys distribution model](http://redis.io/topics/cluster-spec#keys-distribution-model), [Redis Cluster data sharding](http://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding) e [Keys hash tags](http://redis.io/topics/cluster-spec#keys-hash-tags).

Para obter um exemplo de código sobre como trabalhar com clustering e localizar chaves no mesmo fragmento com o cliente StackExchange.Redis, confira a parte [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) da amostra [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).

## Qual é o maior tamanho de cache que eu posso criar?

O maior tamanho de cache premium é de 53 GB. Você pode criar até 10 fragmentos, fornecendo um tamanho máximo de 530 GB. Se precisar de um tamanho maior, você pode [solicitar mais](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Para saber mais, confira [Preço do Cache Redis do Azure](https://azure.microsoft.com/pricing/details/cache/).

## Todos os clientes do Redis dão suporte ao clustering?

No momento, nem todos os clientes dão suporte ao clustering do Redis. StackExchange.Redis é o que dá suporte a ele. Para obter mais informações sobre outros clientes, confira a seção [Playing with the cluster](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) do [Redis cluster tutorial](http://redis.io/topics/cluster-tutorial).

>[AZURE.NOTE]Se estiver usando o StackExchange.Redis como seu cliente, verifique se está usando a versão mais recente do [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 ou posterior para que o clustering funcione corretamente.

## Como posso me conectar ao meu cache quando o clustering estiver habilitado?

Você pode conectar ao seu cache usando os mesmos [pontos de extremidade, portas e chaves](cache-configure.md#properties) usados ao conectar a um cache que não tem o clustering habilitado. O Redis gerencia o clustering no back-end para que você não precise gerenciá-lo por meio de seu cliente.

## Posso me conectar diretamente aos fragmentos individuais do meu cache?

Oficialmente, não há suporte para isso. Dito isso, cada fragmento consiste em um par de cache primário/de réplica que é conhecido coletivamente como uma instância de cache. Você pode se conectar a essas instâncias de cache usando o utilitário redis-cli na ramificação [unstable](http://redis.io/download) do repositório do Redis no GitHub. Esta versão implementa o suporte básico quando iniciado com o switch `-c`. Para obter mais informações, confira [Playing with the cluster](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) em [http://redis.io](http://redis.io) no [Redis cluster tutorial](http://redis.io/topics/cluster-tutorial).

Para não SSL, use os comandos a seguir.

	Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
	Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
	Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
	...
	Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

Para SSL, substitua `1300N` por `1500N`.

## Posso configurar o clustering para um cache criado anteriormente?

Neste momento, você pode habilitar e configurar o clustering apenas durante a criação de um cache. Você pode alterar o tamanho do cluster depois que o cache for criado, mas não pode adicionar nem remover clustering em um cache premium depois que ele é criado. Um cache premium com clustering habilitado e apenas um fragmento é diferente de um cache premium do mesmo tamanho sem clustering.

## Posso configurar o clustering para um cache básico ou standard?

O clustering está disponível apenas para os caches premium.

## Posso usar clustering com os provedores de Estado de Sessão ASP.NET Redis e Caching de Saída?

-	**Provedor de Cache de Saída Redis**: sem necessidade de alterações.
-	**Provedor de Estado de Sessão Redis**: para usar o clustering, você deve usar [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 ou superior; do contrário, uma exceção será lançada. Essa é uma alteração significativa; para saber mais, confira [v2.0.0 Breaking Change Details](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

## Próximas etapas
Aprenda a usar mais recursos de cache premium.

-	[Como configurar a persistência para um Cache Redis do Azure Premium](cache-how-to-premium-persistence.md)
-	[Como configurar o suporte de Rede Virtual para um Cache Redis do Azure Premium](cache-how-to-premium-vnet.md)
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-clustering/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-clustering/redis-cache-premium-pricing-tier.png

[NewCacheMenu]: ./media/cache-how-to-premium-clustering/redis-cache-new-cache-menu.png

[CacheCreate]: ./media/cache-how-to-premium-clustering/redis-cache-cache-create.png

[redis-cache-premium-pricing-group]: ./media/cache-how-to-premium-clustering/redis-cache-premium-pricing-group.png

[redis-cache-premium-features]: ./media/cache-how-to-premium-clustering/redis-cache-premium-features.png

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png

<!---HONumber=AcomDC_1223_2015-->