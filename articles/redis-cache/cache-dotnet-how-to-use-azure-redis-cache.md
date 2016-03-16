<properties 
	pageTitle="Como utilizar o cache Redis do Azure" 
	description="Saiba como melhorar o desempenho de seus aplicativos do Azure com o Cache Redis do Azure" 
	services="redis-cache,app-service" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="01/21/2016" 
	ms.author="sdanie"/>

# Como utilizar o cache Redis do Azure

> [AZURE.SELECTOR]
- [.Net](cache-dotnet-how-to-use-azure-redis-cache.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Este guia mostra como começar a usar o **Cache Redis do Azure**. O cache Redis do Microsoft Azure é baseado no popular software livre Cache Redis. Ele fornece acesso a um cache Redis dedicado e seguro, gerenciado pela Microsoft. Um cache criado usando o cache Redis do Azure é acessível de qualquer aplicativo do Microsoft Azure.

O Cache Redis do Microsoft Azure está disponível nas seguintes camadas:

-	**Básico** – um único nó. Vários tamanhos acima de 53 GB.
-	**Padrão** – principal/réplica com dois nós. Vários tamanhos acima de 53 GB. SLA de 99,9%.
-	**Premium** – dois nós Primário/Réplica com até 10 fragmentos. Vários tamanhos de 6 GB a 530 GB (entre em contato conosco para obter mais informações). Todos os recursos da camada Standard e muito mais, incluindo o suporte para o [cluster Redis](cache-how-to-premium-clustering.md), [persistência do Redis](cache-how-to-premium-persistence.md) e [Rede Virtual do Azure](cache-how-to-premium-vnet.md). SLA de 99,9%.

Cada camada é diferente em termos de recursos e preços. Para saber mais sobre preços, consulte [Detalhes de preços do cache][].

Este guia mostra como usar o cliente [StackExchange.Redis][] usando o código C#. Os cenários abordados incluem a **criação e configuração de um cache**, a **configuração de clientes de cache** e a **adição e remoção de objetos do cache**. Para obter mais informações sobre como usar o Cache Redis do Azure, consulte a seção [Próximas etapas][].

<a name="getting-started-cache-service"></a>
## Introdução ao Cache Redis do Azure

Começar a usar o cache Redis do Azure é fácil. Para começar, você provisiona e configura um cache. Em seguida, você configura os clientes de cache para que possam acessar o cache. Quando os clientes de cache estiverem configurados, você pode começar a trabalhar com eles.

-	[Criar o cache][]
-	[Configurar os clientes de cache][]

<a name="create-cache"></a>
## Criar um cache

Para criar um cache, primeiro entre no [Portal do Azure][] e clique em **Novo**, **Dados + Armazenamento** e **Cache Redis**.

>[AZURE.NOTE] Além de criar caches no Portal do Azure, você também pode criá-los usando modelos do ARM, PowerShell ou CLI do Azure.
>
>-	Para criar um cache usando modelos ARM, consulte [Criar um cache Redis usando um modelo](cache-redis-cache-arm-provision.md).
>-	Para criar um cache usando o Azure PowerShell, consulte [Gerenciar Cache Redis do Azure com o Azure PowerShell](cache-howto-manage-redis-cache-powershell.md).
>-	Para criar um cache usando a CLI do Azure, consulte [Como criar e gerenciar o Cache Redis do Azure usando a Interface de linha de comando do Azure (Azure CLI)](cache-manage-cli.md).

![Novo cache][NewCacheMenu]

>[AZURE.NOTE] Se não tiver uma conta do Azure, você poderá criar uma conta gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][].

Na folha **Novo Cache Redis**, especifique a configuração desejada para o cache.

![Criar o cache][CacheCreate]

-	No **nome DNS**, insira um nome de cache a ser usado para o ponto de extremidade do cache. O nome de cache deve ser uma cadeia de caracteres com 1 a 63 caracteres e deve conter somente números, letras e o caractere `-`. O nome do cache não pode começar nem terminar com o caractere `-`, e os caracteres `-` consecutivos não são válidos.
-	Para **Assinatura**, selecione a assinatura do Azure que deseja usar para o cache. Se sua conta tiver apenas uma assinatura, ela será automaticamente selecionada e o menu suspenso **Assinatura** não será exibido.
-	No **Grupo de recursos**, selecione ou crie um grupo de recursos para seu cache. Para obter mais informações, consulte [Usando os grupos de recursos para gerenciar seus recursos do Azure][]. 
-	Use **Local** para especificar a localização geográfica em que o cache está hospedado. Para obter melhor desempenho, a Microsoft recomenda que você crie o cache na mesma região que o aplicativo de cliente de cache.
-	Utilize **Faixa de preço** para selecionar o tamanho e recursos de cache desejados.
-	O **cluster Redis** permite criar caches com mais de 53 GB e fragmentar dados em vários nós Redis. Para saber mais, consulte [Como configurar um cluster para um Cache Redis do Azure Premium](cache-how-to-premium-clustering.md).
-	A **persistência Redis** oferece a capacidade de persistir o cache para uma conta de Armazenamento do Azure. Para obter instruções sobre como configurar a persistência, consulte [Como configurar a persistência para um Cache Redis do Azure Premium](cache-how-to-premium-persistence.md).
-	A **Rede Virtual** fornece segurança e isolamento aprimorados ao restringir o acesso a seu cache a apenas os clientes que estiverem na Rede Virtual do Azure especificada. Você pode usar todos os recursos da VNet como sub-redes, políticas de controle de acesso e outros recursos para restringir ainda mais o acesso ao Redis. Para saber mais, consulte [Como configurar o suporte à Rede Virtual para um Cache Redis do Azure Premium](cache-how-to-premium-vnet.md).
-	Use **Diagnóstico** para especificar uma conta de armazenamento para métricas de cache. Para saber mais sobre a configuração e a exibição das métricas de cache, consulte [Como monitorar o Cache Redis do Azure](cache-how-to-monitor.md).

Uma vez que as novas opções de cache forem configuradas, clique em **Criar**. Pode levar alguns minutos para que o cache seja criado. Para verificar o status, você pode monitorar o progresso na Startboard. Depois que o cache foi criado, seu novo cache tem um status de **Executando** e está pronto para uso com configurações padrão.

![Cache criado][CacheCreated]

Uma vez criado o cache, é possível acessá-lo na lâmina de **Navegador**.

![Lâmina de navegador][BrowseCaches]

Clique em **Caches Redis** para exibir seus caches.

![Caches][Caches]

<a name="NuGet"></a>
## Configurar os clientes de cache

Um cache criado usando o Cache Redis do Azure é acessível de qualquer aplicativo do Azure. Aplicativos .NET desenvolvidos no Visual Studio podem usar o cliente de cache **StackExchange.Redis**, que pode ser configurado usando um pacote NuGet que simplifica a configuração de aplicativos cliente de cache.

>[AZURE.NOTE] Para obter mais informações, consulte a página do github [StackExchange.Redis][] e a [documentação do cliente do cache StackExchange.Redis][].

Para configurar um aplicativo de cliente no Visual Studio utilizando o pacote NuGet StackExchange.Redis, clique com o botão direito no projeto em **Gerenciador de Soluções** e escolha **Gerenciar pacotes NuGet**.

![Gerenciar pacotes NuGet][NuGetMenu]

Digite **StackExchange.Redis** ou **StackExchange.Redis.StrongName** dentro da caixa de texto **Pesquisar Online**, selecione a versão desejada dos resultados e clique em **Instalar**.

>[AZURE.NOTE] Se preferir utilizar uma versão de nome forte da biblioteca do cliente de **StackExchange.Redis**, escolha **StackExchange.Redis.StrongName**; caso contrário escolha **StackExchange.Redis**.

![Pacote NuGet StackExchange.Redis][StackExchangeNuget]

Os downloads de pacote NuGet acrescentam as referências de assembly necessárias para o seu aplicativo de cliente para acessar o cache Redis do Azure com o cliente de cache StackExchange.Redis.

Depois que o projeto de cliente estiver configurado para cache, você poderá usar as técnicas descritas nas seções a seguir para trabalhar com o cache.

<a name="working-with-caches"></a>
## Trabalhando com Caches

As etapas desta seção descrevem como realizar tarefas comuns com o Cache.

-	[Conectar-se ao cache][]
-   [Adicionar e recuperar objetos do cache][]
-   [Trabalhar com objetos .NET no cache](#work-with-net-objects-in-the-cache)

<a name="connect-to-cache"></a>
## Conectar-se ao cache

Para trabalhar de forma programática com um cache, você precisa de uma referência ao cache. Adicione o seguinte à parte superior de qualquer arquivo do qual você deseja usar o cliente StackExchange.Redis para acessar um cache Redis do Azure:

    using StackExchange.Redis;

>[AZURE.NOTE] O cliente StackExchange.Redis requer o .NET Framework 4 ou posterior.

A conexão com o Cache Redis do Azure é gerenciada pela classe `ConnectionMultiplexer`. Essa classe foi projetada para ser compartilhada e reutilizada em todo aplicativo de cliente e não precisa ser criado em uma base de operação.

Para se conectar a um Cache Redis do Azure e obter uma instância de um `ConnectionMultiplexer` conectado, chame o método estático `Connect` e passe a chave e o ponto de extremidade do cache, como no exemplo a seguir. Use a chave gerada do Portal do Azure como o parâmetro de senha.

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

>[AZURE.IMPORTANT] Aviso: nunca armazene credenciais no código-fonte. Para manter esse exemplo simples, eu estou mostrando-lhes no código-fonte. Consulte [Como cadeias de caracteres de aplicativo e cadeias de caracteres de conexão funcionam][] para obter informações sobre como armazenar credenciais.

Se não desejar usar o SSL, defina `ssl=false` ou omita o parâmetro `ssl`.

>[AZURE.NOTE] A porta não SSL é desabilitada por padrão para novos caches. Para obter instruções sobre como habilitar a porta não SSL, consulte as [Portas de acesso](cache-configure.md#access-ports).

Uma abordagem para compartilhar uma instância do `ConnectionMultiplexer` em seu aplicativo deve ter uma propriedade estática que retorna uma instância conectada, semelhante ao exemplo a seguir. Isso oferece uma maneira segura para o thread para inicializar somente uma única instância conectada do `ConnectionMultiplexer`. Nestes exemplos, `abortConnect` é definido como false, o que significa que a chamada terá êxito mesmo que não seja possível estabelecer uma conexão com o Cache Redis do Azure. Um recurso chave do `ConnectionMultiplexer` é que ele vai restaurar automaticamente a conectividade ao cache assim que o problema de rede ou outras causas sejam resolvidos.

	private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
	{
	    return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
	});
	
	public static ConnectionMultiplexer Connection
	{
	    get
	    {
	        return lazyConnection.Value;
	    }
	}

Para obter mais informações nas opções de configuração de conexão avançada, consulte [Modelo de configuração de StackExchange.Redis][].

O ponto de extremidade do cache e as chaves podem ser obtidos na folha **Cache Redis** de sua instância de cache.

![Propriedades de cache][CacheProperties]

![Gerenciar chaves][ManageKeys]

Quando a conexão for estabelecida, retorne uma referência para o banco de dados do redis cache chamando o método `ConnectionMultiplexer.GetDatabase`. O objeto retornado pelo método `GetDatabase` é um objeto leve de passagem e não precisa ser armazenado.

	// Connection refers to a property that returns a ConnectionMultiplexer
	// as shown in the previous example.
	IDatabase cache = Connection.GetDatabase();

	// Perform cache operations using the cache object...
	// Simple put of integral data types into the cache
	cache.StringSet("key1", "value");
	cache.StringSet("key2", 25);

	// Simple get of data types from the cache
	string key1 = cache.StringGet("key1");
	int key2 = (int)cache.StringGet("key2");

Agora que você já sabe como conectar uma instância de cache Redis do Azure e retornar uma referência para o banco de dados de cache, vamos dar uma olhada em como trabalhar com o cache.

<a name="add-object"></a>
## Adicionar e recuperar objetos do cache

Itens podem ser armazenados e recuperados de um cache usando os métodos `StringSet` e `StringGet`.

	// If key1 exists, it is overwritten.
	cache.StringSet("key1", "value1");

	string value = cache.StringGet("key1");

O Redis armazena mais dados como cadeias de caracteres Redis, mas essas cadeias de caracteres podem conter muitos tipos de dados, incluindo dados binários serializados, que podem ser usados ao armazenar objetos .NET no cache.

Ao se chamar `StringGet`, se o objeto existir, ele será retornado e, se não existir, será retornado `null`. Nesse caso você pode recuperar o valor da fonte de dados desejado e armazená-lo no cache para uso subsequente. Isso é conhecido como o padrão cache-aside.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

Para especificar a expiração de um item no cache, use o parâmetro `TimeSpan` de `StringSet`.

	cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

## Trabalhar com objetos .NET no cache

O cache Redis do Azure pode armazenar objetos .NET em cache, bem como tipos de dados primitivos, mas antes um objeto .NET possa ser armazenado em cache, ele deve ser serializado. Essa é a responsabilidade do desenvolvedor de aplicativos e proporciona ao desenvolvedor flexibilidade na escolha do serializador.

Uma maneira simples de serializar objetos é usar os métodos de serialização do `JsonConvert` em [Newtonsoft.Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/8.0.1-beta1) e serializar para e do JSON. O exemplo a seguir mostra um get e set usando uma instância do objeto `Employee`.


	class Employee
	{
	    public int Id { get; set; }
	    public string Name { get; set; }
	
	    public Employee(int EmployeeId, string Name)
	    {
	        this.Id = EmployeeId;
	        this.Name = Name;
	    }
	}

    // Store to cache
    cache.StringSet("e25", JsonConvert.SerializeObject(new Employee(25, "Clayton Gragg")));

    // Retrieve from cache
    Employee e25 = JsonConvert.DeserializeObject<Employee>(cache.StringGet("e25"));

<a name="next-steps"></a>
## Próximas etapas

Agora que você aprendeu os conceitos básicos, siga estes links para saber mais sobre o Cache Redis do Azure.

-	Confira os provedores ASP.NET para o Cache Redis do Azure.
	-	[Provedor de estado de sessão do Redis do Azure](cache-asp.net-session-state-provider.md)
	-	[Provedor de Cache de Saída ASP.NET do Cache Redis do Azure](cache-asp.net-output-cache-provider.md)
-	[Habilite o diagnóstico de cache](cache-how-to-monitor.md#enable-cache-diagnostics) para que você possa [monitorar](cache-how-to-monitor.md) a integridade do cache. Você pode exibir as métricas no Portal do Azure e também pode [baixá-las e analisá-las](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) usando as ferramentas de sua escolha.
-	Confira a [documentação do cliente do cache StackExchange.Redis][].
	-	O Cache Redis do Azure pode ser acessado por meio de muitos clientes Redis e linguagens de desenvolvimento. Para obter mais informações, consulte [http://redis.io/clients][] e [Desenvolver em outras linguagens para o Cache Redis do Azure][].
	-	O Cache Redis do Azure também pode ser usado com serviços como Redsmin. Para obter mais informações, consulte [Como recuperar uma cadeia de conexão do Redis do Azure e usá-la com Redsmin][].
-	Consulte a documentação do [Redis][] e leia sobre [tipos de dados de Redis][] e [uma introdução de quinze minutos aos tipos de dados de Redis][].



<!-- INTRA-TOPIC LINKS -->
[Próximas etapas]: #next-steps
[Introduction to Azure Redis Cache (Video)]: #video
[What is Azure Redis Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Prepare Your Visual Studio Project to Use Azure Caching]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Get Started with Azure Redis Cache]: #getting-started-cache-service
[Criar o cache]: #create-cache
[Configure the cache]: #enable-caching
[Configurar os clientes de cache]: #NuGet
[Working with Caches]: #working-with-caches
[Conectar-se ao cache]: #connect-to-cache
[Adicionar e recuperar objetos do cache]: #add-object
[Specify the expiration of an object in the cache]: #specify-expiration
[Store ASP.NET session state in the cache]: #store-session

  
<!-- IMAGES -->
[NewCacheMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-new-cache-menu.png

[CacheCreate]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-create.png

[StackExchangeNuget]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-stackexchange-redis.png

[NuGetMenu]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-nuget-menu.png

[CacheProperties]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-properties.png

[ManageKeys]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-manage-keys.png

[SessionStateNuGet]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-session-state-provider.png

[BrowseCaches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-browse-caches.png

[Caches]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-caches.png

[CacheCreated]: ./media/cache-dotnet-how-to-use-azure-redis-cache/redis-cache-cache-created.png




   
<!-- LINKS -->
[http://redis.io/clients]: http://redis.io/clients
[Desenvolver em outras linguagens para o Cache Redis do Azure]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[Como recuperar uma cadeia de conexão do Redis do Azure e usá-la com Redsmin]: https://redsmin.uservoice.com/knowledgebase/articles/485711-how-to-connect-redsmin-to-azure-redis-cache
[Azure Redis Session State Provider]: http://go.microsoft.com/fwlink/?LinkId=398249
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Session State Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320835
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Output Cache Provider for Azure Cache]: http://go.microsoft.com/fwlink/?LinkId=320837
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Azure Caching]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[How to Configure Virtual Machine Sizes]: http://go.microsoft.com/fwlink/?LinkId=164387
[Azure Caching Capacity Planning Considerations]: http://go.microsoft.com/fwlink/?LinkId=320167
[Azure Caching]: http://go.microsoft.com/fwlink/?LinkId=252658
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Configure a cache in Azure Redis Cache]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[Modelo de configuração de StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[Work with .NET objects in the cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Detalhes de preços do cache]: http://www.windowsazure.com/pricing/details/cache/
[Portal do Azure]: https://portal.azure.com/

[Overview of Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=320830
[Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrate to Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[Usando os grupos de recursos para gerenciar seus recursos do Azure]: http://azure.microsoft.com/documentation/articles/resource-group-overview/

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[documentação do cliente do cache StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[tipos de dados de Redis]: http://redis.io/topics/data-types
[uma introdução de quinze minutos aos tipos de dados de Redis]: http://redis.io/topics/data-types-intro

[Como cadeias de caracteres de aplicativo e cadeias de caracteres de conexão funcionam]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/

[Avaliação gratuita do Azure]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero

<!---HONumber=AcomDC_0309_2016-->