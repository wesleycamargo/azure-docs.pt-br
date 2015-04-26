<properties 
	pageTitle="Como utilizar o cache Redis do Azure" 
	description="Saiba como melhorar o desempenho de seus aplicativos do Azure com o Cache Redis do Azure" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="sdanie"/>

# Como utilizar o cache Redis do Azure

Esse guia mostra como iniciar o uso do **Cache Redis do Azure**. Os exemplos são escritos em código C\# e utilizam a API .NET. Os cenários abordados incluem **criação e configuração de um cache**, **configuração de clientes de cache**, **adição e remoção de objetos do cache** e **armazenamento de estado da sessão ASP.NET no cache**. Para obter mais informações sobre o uso do cache Redis do Azure, consulte a seção [Próximas etapas][].

<a name="video"></a>
## Introdução ao Cache Redis do Azure (vídeo)
Neste vídeo, Saurabh Pant e Scott Hanselman apresentam o Cache Redis do Azure. (Duração: 7:45)  

> [AZURE.VIDEO azure-redis-cache-101-introduction-to-redis]

<a name="what-is"></a>
## O que é o cache Redis do Azure?

O cache Redis do Microsoft Azure é baseado no popular software livre Cache Redis. Ele fornece acesso a um cache Redis dedicado e seguro, gerenciado pela Microsoft. Um cache criado usando o cache Redis do Azure é acessível de qualquer aplicativo do Microsoft Azure.

O cache Redis do Microsoft Azure está disponível em duas faixas:

-	**Básico** - um único nó. Vários tamanhos acima de 53 GB.
-	**Padrão** - principal/réplica de dois nós. Vários tamanhos acima de 53 GB. SLA de 99,9%.

Cada camada é diferente em termos de recursos e preços. Os recursos são abordados posteriormente neste manual e, para obter mais informações sobre preços, consulte [Detalhes de preços do Cache][].

Este guia fornece uma visão geral introdutória do cache Redis do Azure. Para obter mais informações sobre esses recursos que estão além do escopo deste guia de Introdução, consulte [visão geral do Cache Redis do Azure][].

<a name="getting-started-cache-service"></a>
## Introdução ao cache Redis do Azure

Começar a usar o cache Redis do Azure é fácil. Para começar, você provisiona e configura um cache. Em seguida, você configura os clientes de cache para que possam acessar o cache. Quando os clientes de cache estiverem configurados, você pode começar a trabalhar com eles.

-	[Criar o cache][]
-	[Configurar os clientes de cache][]

<a name="create-cache"></a>
## Criar um cache

Para criar um cache, primeiro entre no portal de visualização de gerenciamento do Azure e clique em **Novo**, **Cache Redis**.

![New cache][NewCacheMenu]

Na lâmina do **Novo cache Redis**, especifique a configuração desejada para o cache.

![Create cache][CacheCreate]

No **nome DNS**, insira um nome de subdomínio a ser usado para o ponto de extremidade do cache. O ponto de extremidade deve ser uma cadeia de seis a vinte caracteres, conter apenas números e letras minúsculas e deve começar com uma letra.

Utilize **Faixa de preço** para selecionar o tamanho e recursos de cache desejados. O cache Redis está disponível em duas faixas.

-	**Básico** - nó único, vários tamanhos acima de 53 GB.
-	**Padrão** - dois nós mestre/subordinado, SLA de 99,9%, vários tamanhos acima de 53 GB.

Para **Assinatura**, selecione a assinatura do Azure que deseja usar para o cache.

>[AZURE.NOTE] Se sua conta tem apenas uma assinatura, ela será selecionada automaticamente e a lista suspensa de assinatura não será exibida.

No **Grupo de recursos**, selecione ou crie um grupo de recursos para seu cache.

>[AZURE.NOTE] Para obter mais informações, consulte [Usando os grupos de recursos para gerenciar seus recursos do Azure][]. 

Utilize a **Geolocalização** para especificar a localização geográfica em que o cache está hospedado. Para obter melhor desempenho, a Microsoft recomenda que você crie o cache na mesma região que o aplicativo de cliente de cache.

Uma vez que as novas opções de cache forem configuradas, clique em **Criar**. Pode levar alguns minutos para que o cache seja criado. Para verificar o status, você pode monitorar o progresso na Startboard. Depois que o cache foi criado, seu novo cache tem um status de **Executando** e está pronto para uso com configurações padrão.

![Cache created][CacheCreated]

Uma vez criado o cache, é possível acessá-lo na lâmina de **Navegador**.

![Browse blade][BrowseCaches]

Clique em **Caches** para exibir seus caches.

![Caches][Caches]

<a name="NuGet"></a>
## Configurar os clientes de cache

Um cache criado utilizando o cache Redis do Azure é acessível de qualquer aplicativo Azure. Aplicativos .NET desenvolvidos no Visual Studio podem utilizar o cliente de cache **StackExchange.Redis**, que também pode ser configurado utilizando um pacote NuGet que simplifica a configuração dos aplicativos de cliente de cache. 

>[AZURE.NOTE] Para obter mais informações, consulte a página do github [StackExchange.Redis][] e a [documentação de cliente de cache StackExchange.Redis][].

Para configurar um aplicativo de cliente no Visual Studio utilizando o pacote NuGet StackExchange.Redis, clique com o botão direito no projeto em **Gerenciador de Soluções** e escolha **Gerenciar pacotes NuGet**. 

![Manage NuGet packages][NuGetMenu]

Digite **StackExchange.Redis** ou **StackExchange.Redis.StrongName** dentro da caixa de texto **Pesquisar Online**, selecione a versão desejada dos resultados e clique em **Instalar**.

>[AZURE.NOTE] Se preferir utilizar uma versão de nome forte da biblioteca do cliente de **StackExchange.Redis**, escolha **StackExchange.Redis.StrongName**; caso contrário escolha **StackExchange.Redis**.

![StackExchange.Redis NuGet package][StackExchangeNuget]

Os downloads de pacote NuGet acrescentam as referências de assembly necessárias para o seu aplicativo de cliente para acessar o cache Redis do Azure com o cliente de cache StackExchange.Redis.

Depois que o projeto de cliente estiver configurado para caching, você poderá usar as técnicas descritas nas seções a seguir para trabalhar com o cache.

<a name="working-with-caches"></a>
## Trabalhando com caches

As etapas desta seção descrevem como realizar tarefas comuns com o Cache.

-	[Conectar-se ao cache][]
-   [Adicionar e recuperar objetos do cache][]
-   [Armazenar o estado da sessão ASP.NET no cache][]

<a name="connect-to-cache"></a>
## Conectar-se ao cache

Para trabalhar de forma programática com um cache, você precisa de uma referência ao cache. Adicione o seguinte à parte superior de qualquer arquivo do qual você deseja usar o cliente StackExchange.Redis para acessar um cache Redis do Azure:

    using StackExchange.Redis;

>[AZURE.NOTE] O cliente StackExchange.Redis requer o .NET Framework 4 ou posterior.

A conexão ao cache Redis do Azure é gerenciada pela classe `ConnectionMultiplexer`. Essa classe foi projetada para ser compartilhada e reutilizada em todo aplicativo de cliente e não precisa ser criado em uma base de operação. 

Para conectar a um cache Redis do Azure e ser retornado a uma instância de um `ConnectionMultiplexer`, conectado, chame o método `Connect` estático e passe o ponto de extremidade e chave do cache como mostrado no exemplo a seguir. Utilize a chave do Azure gerada do portal de serviço como um parâmetro de senha.

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,ssl=true,password=...");

>[AZURE.NOTE] Aviso: Nunca armazene credenciais no código-fonte. Para manter esse exemplo simples, eu estou mostrando-lhes no código-fonte. Consulte [Sites do Windows Azure: Como as cadeias de caracteres de aplicativo e a cadeia de caracteres de conexão trabalham][] para obter informações sobre como armazenar credenciais.

Se você não quiser usar o SSL, defina `ssl=false` ou simplesmente passe-o no ponto de extremidade e na chave.

>[AZURE.NOTE] A porta não SSL é desabilitada por padrão para novos caches. Para obter instruções sobre como habilitar a porta não SSL, consulte a seção Portas de Acesso no tópico [Configurar um cache no Cache Redis do Azure][].

	connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,password=...");

>[AZURE.NOTE] Para obter mais informações nas opções de configuração de conexão avançada, consulte [Modelo de configuração de StackExchange.Redis][].

O ponto de extremidade e as chaves do cache podem ser obtidos da lâmina do portal de visualização de gerenciamento do Azure para sua instância de cache.

![Cache properties][CacheProperties]

![Manage keys][ManageKeys]

Uma vez estabelecida a conexão, ela retorna uma referência para o banco de dados de cache Redis, chamando o método `ConnectionMultiplexer.GetDatabase`.

	// connection referes to a previously configured ConnectionMultiplexer
	IDatabase cache = connection.GetDatabase();

>[AZURE.NOTE] O objeto retornado do método  `GetDatabase` é um objeto de passagem leve e não precisa ser armazenado.

	ConnectionMultiplexer connection = ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,ssl=true,password=...");

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

Os itens podem ser armazenados e recuperados de um cache usando os métodos `StringSet` e `StringGet`.

	// If key1 exists, it is overwritten.
	cache.StringSet("key1", "value1");

	string value = cache.StringGet("key1");

>[AZURE.NOTE] O Redis armazena mais dados como cadeias de caracteres Redis, mas essas cadeias de caracteres podem conter muitos tipos de dados, incluindo dados binários serializados, que podem ser usados ao armazenar objetos .NET no cache.

Ao se chamar `StringGet`, se o objeto existir, ele será retornado. Se isso não acontecer, será retornado nulo. Nesse caso você pode recuperar o valor da fonte de dados desejado e armazená-lo no cache para uso subsequente. Isso é conhecido como o padrão cache-aside.

    string value = cache.StringGet("key1");
    if (value == null)
    {
        // The item keyed by "key1" is not in the cache. Obtain
        // it from the desired data source and add it to the cache.
        value = GetValueFromDataSource();

        cache.StringSet("key1", value);
    }

>[AZURE.NOTE] O cache Redis do Azure pode armazenar objetos .NET em cache, bem como tipos de dados primitivos, mas antes um objeto .NET possa ser armazenado em cache, ele deve ser serializado. Essa é a responsabilidade do desenvolvedor de aplicativos e proporciona ao desenvolvedor flexibilidade na escolha do serializador. Para obter mais informações, consulte [Trabalhar com objetos .NET no cache][].

<a name="specify-expiration"></a>
## Especificar a expiração de um item no cache

Para especificar a expiração de um item no cache, utilize o parâmetro `TimeSpan` do `StringSet`.

	cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));


<a name="store-session"></a>
## Armazenar o estado da sessão ASP.NET no cache

O Cache Redis do Azure fornece um provedor de estado da sessão que você pode usar para armazenar seu estado de sessão em um cache ao invés de fazê-lo na memória ou em um banco de dados SQL Server. Para utilizar o provedor de estado
da sessão de caching, primeiro configure seu cache e, em seguida, configure seu aplicativo ASP.NET para cache utilizando o pacote NuGet de estado de sessão de cache Redis.

Para configurar um aplicativo de cliente no Visual Studio utilizando o pacote NuGet de estado de sessão de cache Redis, clique com o botão direito em projeto no **Gerenciador de Soluções** e escolha **Gerenciar Pacotes NuGet**. 

![Manage NuGet packages][NuGetMenu]

Digite **RedisSessionStateProvider** na caixa de texto **Pesquisar Online**, selecione-o nos resultados e clique em **Instalar**.

![Redis Cache Session State NuGet Package][SessionStateNuGet]

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

Para obter mais informações sobre configuração e uso do provedor de estado de sessão de Redis do Azure, consulte [Provedor de estado de sessão de Redis do Azure][].

<a name="next-steps"></a>
## Próximas etapas

Agora que você aprendeu o básico de cache Redis do Azure,siga os links para saber como realizar tarefas de cache mais complexas.

-	[Habilite o diagnóstico de cache](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) para que você possa [monitorar](https://msdn.microsoft.com/library/azure/dn763945.aspx) a integridade do cache. Você pode exibir as métricas no portal e também pode [baixá-las e analisá-las](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) usando as ferramentas de sua escolha.
-	Aprenda mais sobre o cliente StackExchange.Redis: [documentação de cliente de cache StackExchange.Redis][]
	-	O Cache Redis do Azure pode ser acessado por meio de muitos clientes Redis e linguagens de desenvolvimento. Para obter mais informações, consulte [http://redis.io/clients][] e [Desenvolver em outras linguagens para o Cache Redis do Azure][].
	-	O Cache Redis do Azure também pode ser usado com serviços como Redsmin. Para obter mais informações, consulte [Como recuperar uma cadeia de conexão do Redis do Azure e usá-la com Redsmin][].
-	Consulte a documentação do [Redis][] e leia sobre [tipos de dados de Redis][] e [uma introdução de quinze minutos aos tipos de dados de Redis][].
-   Consulte a referência de MSDN: [Cache Redis do Azure][]


<!-- INTRA-TOPIC LINKS -->
[Próximas etapas]: #next-steps
[Introdução ao Cache Redis do Azure (vídeo)]: #video
[O que é o Cache Redis do Azure?]: #what-is
[Criar um Cache do Azure]: #create-cache
[Qual tipo de caching é mais adequado para mim?]: #choosing-cache
[Preparar seu projeto do Visual Studio para usar Caching do Azure]: #prepare-vs
[Configurar seu aplicativo para usar Caching]: #configure-app
[Introdução ao Cache Redis do Azure]: #getting-started-cache-service
[Criar o cache]: #create-cache
[Configurar o cache]: #enable-caching
[Configurar os clientes de cache]: #NuGet
[Trabalhando com caches]: #working-with-caches
[Conectar-se ao cache]: #connect-to-cache
[Adicionar e recuperar objetos do cache]: #add-object
[Especificar o término de um objeto no cache]: #specify-expiration
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
[Provedor de estado de sessão de Redis do Azure]: http://go.microsoft.com/fwlink/?LinkId=398249
[Portal de Gerenciamento do Azure]: http://windows.azure.com/
[Como: Configurar um cliente de cache de modo programático]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Provedor de Estado de Sessão para Cache do Azure]: http://go.microsoft.com/fwlink/?LinkId=320835
[Cache do Azure AppFabric: Cache de estado de sessão]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Provedor de Cache de Saída para Cache do Azure]: http://go.microsoft.com/fwlink/?LinkId=320837
[Caching compartilhado do Azure]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Blog da equipe]: http://blogs.msdn.com/b/windowsazure/
[Caching do Azure]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[Como configurar tamanhos de máquina virtual]: http://go.microsoft.com/fwlink/?LinkId=164387
[Considerações sobre o planejamento da capacidade do Cache do Azure]: http://go.microsoft.com/fwlink/?LinkId=320167
[Caching do Azure]: http://go.microsoft.com/fwlink/?LinkId=252658
[Como: Definir a capacidade de cache de uma página ASP.NET declarativamente]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[Como: Definir a capacidade de cache de uma página programaticamente]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Configurar um cache no Cache Redis do Azure]: http://msdn.microsoft.com/library/azure/dn793612.aspx

[Modelo de configuração de Stackexchange.Redis]: http://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md

[Trabalhar com objetos .NET no cache]: http://msdn.microsoft.com/library/dn690521.aspx#Objects


[Instalação do Gerenciador de Pacotes NuGet]: http://go.microsoft.com/fwlink/?LinkId=240311
[Detalhes de Preços do Cache]: http://www.windowsazure.com/pricing/details/cache/
[Portal de Gerenciamento]: https://manage.windowsazure.com/

[Visão geral do Cache Redis do Azure]: http://go.microsoft.com/fwlink/?LinkId=320830
[Cache Redis do Azure]: http://go.microsoft.com/fwlink/?LinkId=398247

[Migrar para o Cache Redis do Azure]: http://go.microsoft.com/fwlink/?LinkId=317347
[Exemplos do Cache Redis do Azure]: http://go.microsoft.com/fwlink/?LinkId=320840
[Usando os grupos de recursos para gerenciar seus recursos do Azure]: http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups/

[StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis
[documentação de cliente de cache StackExchange.Redis]: http://github.com/StackExchange/StackExchange.Redis#documentation

[Redis]: http://redis.io/documentation
[Tipos de dados de Redis]: http://redis.io/topics/data-types
[uma introdução de quinze minutos aos tipos de dados de Redis]: http://redis.io/topics/data-types-intro

[Sites do Windows Azure: Como as cadeias de caracteres de aplicativo e a cadeia de caracteres de conexão trabalham]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/

<!--HONumber=49-->