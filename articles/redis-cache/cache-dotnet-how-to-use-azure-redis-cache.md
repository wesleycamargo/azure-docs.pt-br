<properties 
	pageTitle="Como utilizar o cache Redis do Azure" 
	description="Saiba como melhorar o desempenho de seus aplicativos do Azure com o Cache Redis do Azure" 
	services="redis-cache,app-service" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="08/25/2015" 
	ms.author="sdanie"/>

# Como utilizar o cache Redis do Azure

Este guia mostra como começar a usar o **Cache Redis do Azure**. Os exemplos são escritos em código C# e usam o cliente [Stackexchange][]. Os cenários abordados incluem **criação e configuração de um cache**, **configuração de clientes de cache**, **adição e remoção de objetos do cache** e **armazenamento de estado da sessão ASP.NET no cache**. Para obter mais informações sobre como usar o Cache Redis do Azure, consulte a seção [Próximas etapas][].

<a name="what-is"></a>
## O que é o Cache Redis do Azure?

O cache Redis do Microsoft Azure é baseado no popular software livre Cache Redis. Ele fornece acesso a um cache Redis dedicado e seguro, gerenciado pela Microsoft. Um cache criado usando o cache Redis do Azure é acessível de qualquer aplicativo do Microsoft Azure.

O cache Redis do Microsoft Azure está disponível em duas faixas:

-	**Básico** – um único nó. Vários tamanhos acima de 53 GB.
-	**Padrão** – principal/réplica com dois nós. Vários tamanhos acima de 53 GB. SLA de 99,9%.

Cada camada é diferente em termos de recursos e preços. Os recursos são abordados posteriormente neste manual e, para obter mais informações sobre preços, consulte [Detalhes de preços do Cache][].

Este guia fornece uma visão geral introdutória do cache Redis do Azure. Para obter informações detalhadas sobre esses recursos que estão além do escopo deste guia de introdução, consulte [Visão geral do cache Redis do Azure][].

<a name="getting-started-cache-service"></a>
## Introdução ao Cache Redis do Azure

Começar a usar o cache Redis do Azure é fácil. Para começar, você provisiona e configura um cache. Em seguida, você configura os clientes de cache para que possam acessar o cache. Quando os clientes de cache estiverem configurados, você pode começar a trabalhar com eles.

-	[Criar o cache][]
-	[Configurar os clientes de cache][]

<a name="create-cache"></a>
## Criar um cache

Para criar um cache, primeiro entre no [portal de visualização do Azure][] e clique em **Novo**, **Dados + Armazenamento**, **Cache Redis**.

![Novo cache][NewCacheMenu]

>[AZURE.NOTE]Se não tiver uma conta do Azure, você poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][].

Na folha **Novo Cache Redis**, especifique a configuração desejada para o cache.

![Criar o cache][CacheCreate]

No **nome DNS**, insira um nome de subdomínio a ser usado para o ponto de extremidade do cache. O ponto de extremidade deve ser uma cadeia de seis a vinte caracteres, conter apenas números e letras minúsculas e deve começar com uma letra.

Utilize **Faixa de preço** para selecionar o tamanho e recursos de cache desejados. Caches **Básicos** têm um único nó com vários tamanhos até 53 GB. Caches **Padrão** têm uma configuração de principal/réplica com dois nós com um SLA de 99,9% e vários tamanhos até 53 GB.

No **Grupo de recursos**, selecione ou crie um grupo de recursos para seu cache.

>[AZURE.NOTE]Para obter mais informações, consulte [Usando os grupos de recursos para gerenciar seus recursos do Azure][].

Para **Assinatura**, selecione a assinatura do Azure que deseja usar para o cache. Se sua conta tiver apenas uma assinatura, ela será automaticamente selecionada e o menu suspenso **Assinatura** não será exibido.

Use **Local** para especificar a localização geográfica em que o cache está hospedado. Para obter melhor desempenho, a Microsoft recomenda que você crie o cache na mesma região que o aplicativo de cliente de cache.

Uma vez que as novas opções de cache forem configuradas, clique em **Criar**. Pode levar alguns minutos para que o cache seja criado. Para verificar o status, você pode monitorar o progresso na Startboard. Depois que o cache foi criado, seu novo cache tem um status de **Executando** e está pronto para uso com configurações padrão.

![Cache criado][CacheCreated]

Uma vez criado o cache, é possível acessá-lo na lâmina de **Navegador**.

![Lâmina de navegador][BrowseCaches]

Clique em **Caches Redis** para exibir seus caches.

![Caches][Caches]

<a name="NuGet"></a>
## Configurar os clientes de cache

Um cache criado usando o Cache Redis do Azure é acessível de qualquer aplicativo do Azure. Aplicativos .NET desenvolvidos no Visual Studio podem usar o cliente de cache **StackExchange.Redis**, que pode ser configurado usando um pacote NuGet que simplifica a configuração de aplicativos cliente de cache.

>[AZURE.NOTE]Para obter mais informações, consulte a página do github [StackExchange.Redis][] e a [documentação do cliente do cache StackExchange.Redis][].

Para configurar um aplicativo de cliente no Visual Studio utilizando o pacote NuGet StackExchange.Redis, clique com o botão direito no projeto em **Gerenciador de Soluções** e escolha **Gerenciar pacotes NuGet**.

![Gerenciar pacotes NuGet][NuGetMenu]

Digite **StackExchange.Redis** ou **StackExchange.Redis.StrongName** dentro da caixa de texto **Pesquisar Online**, selecione a versão desejada dos resultados e clique em **Instalar**.

>[AZURE.NOTE]Se preferir utilizar uma versão de nome forte da biblioteca do cliente de **StackExchange.Redis**, escolha **StackExchange.Redis.StrongName**; caso contrário escolha **StackExchange.Redis**.

![Pacote NuGet StackExchange.Redis][StackExchangeNuget]

Os downloads de pacote NuGet acrescentam as referências de assembly necessárias para o seu aplicativo de cliente para acessar o cache Redis do Azure com o cliente de cache StackExchange.Redis.

Depois que o projeto de cliente estiver configurado para cache, você poderá usar as técnicas descritas nas seções a seguir para trabalhar com o cache.

<a name="working-with-caches"></a>
## Trabalhando com Caches

As etapas desta seção descrevem como realizar tarefas comuns com o Cache.

-	[Conectar-se ao cache][]
-   [Adicionar e recuperar objetos do cache][]
-   [Armazenar o estado da sessão ASP.NET no cache][]

<a name="connect-to-cache"></a>
## Conectar-se ao cache

Para trabalhar de forma programática com um cache, você precisa de uma referência ao cache. Adicione o seguinte à parte superior de qualquer arquivo do qual você deseja usar o cliente StackExchange.Redis para acessar um cache Redis do Azure:

    using StackExchange.Redis;

>[AZURE.NOTE]O cliente StackExchange.Redis requer o .NET Framework 4 ou posterior.

A conexão com o Cache Redis do Azure é gerenciada pela classe `ConnectionMultiplexer`. Essa classe foi projetada para ser compartilhada e reutilizada em todo aplicativo de cliente e não precisa ser criado em uma base de operação.

Para se conectar a um Cache Redis do Azure e obter uma instância de um `ConnectionMultiplexer` conectado, chame o método estático `Connect` e passe a chave e o ponto de extremidade do cache, como no exemplo a seguir. Use a chave do Azure gerada do portal de visualização como o parâmetro de senha.

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

>[AZURE.IMPORTANT]Aviso: nunca armazene credenciais no código-fonte. Para manter esse exemplo simples, eu estou mostrando-lhes no código-fonte. Consulte [Como cadeias de caracteres de aplicativo e cadeias de caracteres de conexão funcionam][] para obter informações sobre como armazenar credenciais.

Se você não quiser usar SSL, defina `ssl=false` ou omita o parâmetro `ssl`.

>[AZURE.NOTE]A porta não SSL é desabilitada por padrão para novos caches. Para obter instruções sobre como habilitar a porta não SSL, consulte a seção Portas de acesso no tópico [Configurar um cache no Cache Redis do Azure ][].

Para obter mais informações nas opções de configuração de conexão avançada, consulte [Modelo de configuração de StackExchange.Redis][].

O ponto de extremidade do cache e as chaves podem ser obtidos na folha **Cache Redis** de sua instância de cache.

![Propriedades de cache][CacheProperties]

![Gerenciar chaves][ManageKeys]

Quando a conexão for estabelecida, retorne uma referência para o banco de dados do redis cache chamando o método `ConnectionMultiplexer.GetDatabase`.

	// connection refers to a previously configured ConnectionMultiplexer
	IDatabase cache = connection.GetDatabase();

>[AZURE.NOTE]O objeto retornado pelo método `GetDatabase` é um objeto leve de passagem e não precisa ser armazenado.

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

	IDatabase cache = connection.GetDatabase();

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

>[AZURE.NOTE]O Redis armazena mais dados como cadeias de caracteres Redis, mas essas cadeias de caracteres podem conter muitos tipos de dados, incluindo dados binários serializados, que podem ser usados ao armazenar objetos .NET no cache.

Ao se chamar `StringGet`, se o objeto existir, ele será retornado e, se não existir, será retornado nulo. Nesse caso você pode recuperar o valor da fonte de dados desejado e armazená-lo no cache para uso subsequente. Isso é conhecido como o padrão cache-aside.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

>[AZURE.NOTE]O cache Redis do Azure pode armazenar objetos .NET em cache, bem como tipos de dados primitivos, mas antes um objeto .NET possa ser armazenado em cache, ele deve ser serializado. Essa é a responsabilidade do desenvolvedor de aplicativos e proporciona ao desenvolvedor flexibilidade na escolha do serializador. Para obter mais informações, consulte [Trabalhar com objetos .NET no cache][].

<a name="specify-expiration"></a>
## Especificar a expiração de um item no cache

Para especificar a expiração de um item no cache, use o parâmetro `TimeSpan` de `StringSet`.

	cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));


<a name="store-session"></a>
## Armazenar o estado da sessão ASP.NET no cache

O Cache Redis do Azure fornece um provedor de estado da sessão que você pode usar para armazenar seu estado de sessão em um cache ao invés de fazê-lo na memória ou em um banco de dados SQL Server. Para usar o provedor de estado de sessão de cache, primeiro configure o cache e, em seguida, configure seu aplicativo ASP.NET para o cache usando o pacote NuGet de Estado de Sessão do Cache Redis.

Para configurar um aplicativo de cliente no Visual Studio utilizando o pacote NuGet de estado de sessão de cache Redis, clique com o botão direito em projeto no **Gerenciador de Soluções** e escolha **Gerenciar Pacotes NuGet**.

![Gerenciar pacotes NuGet][NuGetMenu]

Digite **RedisSessionStateProvider** na caixa de texto **Pesquisar Online**, selecione-o nos resultados e clique em **Instalar**.

![Pacote NuGet de estado de sessão de cache Redis][SessionStateNuGet]

Os downloads de pacote NuGet acrescentam as referências de assembly necessárias e adicionam a seguinte seção dentro de seu arquivo web.config que contém a configuração solicitada para seu aplicativo ASP.NET a fim de usar o provedor de estado de sessão de cache Redis do Azure.

    <sessionState mode="Custom" customProvider="MySessionStateStore">
      <providers>
        <!--
          <add name="MySessionStateStore" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />
      </providers>
    </sessionState>

A seção comentada fornece um exemplo dos atributos e configurações simples para ambos.

Configure os atributos com o valor de sua lâmina de cache no portal de visualização e configure os outros valores como desejado.

	<sessionState mode="Custom" customProvider="MySessionStateStore">
      <providers>
        <!--
          <add name="MySessionStateStore" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
        -->
        <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="contoso5.redis.cache.windows.net" 
		accessKey="..." ssl="true" />
      </providers>
    </sessionState>

Certifique-se de comentar o provedor de estado de sessão **InProc** padrão.

    <!-- <sessionState mode="InProc" customProvider="DefaultSessionProvider">
      <providers>
        <add name="DefaultSessionProvider" type="System.Web.Providers.DefaultSessionStateProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" />
      </providers>
    </sessionState> -->

Para obter mais informações sobre como definir essas configurações e usar o Provedor de Estado de Sessão do Redis do Azure, consulte [Provedor de Estado de Sessão do Redis do Azure][].

<a name="next-steps"></a>
## Próximas etapas

Agora que você aprendeu o básico de cache Redis do Azure,siga os links para saber como realizar tarefas de cache mais complexas.

-	[Habilite o diagnóstico de cache](cache-how-to-monitor.md#enable-cache-diagnostics) para que você possa monitorar [cache-how-to-monitor.md) a integridade do cache. Você pode exibir as métricas no portal de visualização e também pode [baixá-las e analisá-las](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) usando as ferramentas de sua escolha.
-	Confira a [documentação do cliente do cache StackExchange.Redis][].
	-	O Cache Redis do Azure pode ser acessado por meio de muitos clientes Redis e linguagens de desenvolvimento. Para obter mais informações, consulte [http://redis.io/clients][] e [Desenvolver em outras linguagens para o Cache Redis do Azure][].
	-	O Cache Redis do Azure também pode ser usado com serviços como Redsmin. Para obter mais informações, consulte [Como recuperar uma cadeia de conexão do Redis do Azure e usá-la com Redsmin][].
-	Consulte a documentação do [Redis][] e leia sobre [tipos de dados de Redis][] e [uma introdução de quinze minutos aos tipos de dados de Redis][].
-   Consulte a referência do MSDN para o [Cache Redis do Azure][]. 


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
[Armazenar o estado da sessão ASP.NET no cache]: #store-session

  
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
[Provedor de Estado de Sessão do Redis do Azure]: http://go.microsoft.com/fwlink/?LinkId=398249
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
[Configurar um cache no Cache Redis do Azure ]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[Modelo de configuração de StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[Trabalhar com objetos .NET no cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[NuGet Package Manager Installation]: http://go.microsoft.com/fwlink/?LinkId=240311
[Detalhes de preços do Cache]: http://www.windowsazure.com/pricing/details/cache/
[portal de visualização do Azure]: https://portal.azure.com/

[Visão geral do cache Redis do Azure]: http://go.microsoft.com/fwlink/?LinkId=320830
[Cache Redis do Azure]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrate to Azure Redis Cache]: http://go.microsoft.com/fwlink/?LinkId=317347
[Azure Redis Cache Samples]: http://go.microsoft.com/fwlink/?LinkId=320840
[Usando os grupos de recursos para gerenciar seus recursos do Azure]: http://azure.microsoft.com/documentation/articles/resource-group-overview/

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[Stackexchange]: http://github.com/StackExchange/StackExchange.Redis
[documentação do cliente do cache StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[tipos de dados de Redis]: http://redis.io/topics/data-types
[uma introdução de quinze minutos aos tipos de dados de Redis]: http://redis.io/topics/data-types-intro

[Como cadeias de caracteres de aplicativo e cadeias de caracteres de conexão funcionam]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/

[Avaliação gratuita do Azure]: http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero

<!---HONumber=Sept15_HO3-->