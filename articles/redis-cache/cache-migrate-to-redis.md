<properties 
    pageTitle="Migração do cache para Redis | Microsoft Azure"
    description="Saiba como migrar aplicativos do Serviço de Cache Gerenciado para Cache Redis do Azure"
    services="redis-cache"
    documentationCenter="na"
    authors="steved0x"
    manager="douge"
    editor="tysonn" />
<tags 
    ms.service="cache"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="06/09/2016"
    ms.author="sdanie" />

# Migrar do Serviço de Cache gerenciado para o Cache Redis do Azure

Migrar seus aplicativos que usam o Serviço de Cache Gerenciado do Azure para Cache Redis do Azure pode ser feito com alterações mínimas no seu aplicativo, dependendo dos recursos do Serviço de Cache Gerenciado usados pelo seu aplicativo de cache. Apesar das APIs não serem exatamente a mesmo coisa elas são semelhantes e grande parte do seu código existente que usa o Serviço de Cache Gerenciado para acessar um cache pode ser reutilizado com alterações mínimas. Este tópico mostra como fazer as alterações de configuração e aplicativo necessárias para migrar seus aplicativos de Serviço de Cache Gerenciado para usar o Cache Redis do Azure e mostra como alguns dos recursos de Cache Redis do Azure podem ser usados para implementar a funcionalidade de um cache de Serviço de Cache Gerenciado.

## Etapas da migração

As etapas a seguir são necessárias para migrar um aplicativo de Serviço de Cache Gerenciado para usar o Cache Redis do Azure.

-	Mapear recursos do Serviço de Cache Gerenciado para o Cache Redis do Azure
-	Escolher uma oferta de Cache
-	Criar um Cache
-	Configurar os clientes de Cache
	-	Remover a configuração do Serviço de Cache Gerenciado
	-	Configurar um cliente de cache usando o Pacote NuGet Stackexchange.Redis
-	Migrar o código do Serviço de Cache Gerenciado
	-	Conectar-se ao cache usando a classe ConnectionMultiplexer
	-	Tipos de dados primitivos de acesso no cache
	-	Trabalhar com objetos .NET no cache
-	Migrar o Estado de sessão do ASP.NET e cache de saída para o Cache Redis do Azure 

## Mapear recursos do Serviço de Cache Gerenciado para o Cache Redis do Azure

Serviço de Cache Gerenciado do e Cache Redis do Azure são semelhantes, mas implementam alguns dos seus recursos de maneiras diferentes. Esta seção descreve algumas das diferenças e fornece orientação sobre como implementar os recursos do Serviço de Cache Gerenciado no Cache Redis do Azure.

|Recurso do Serviço de Cache Gerenciado|Suporte do Serviço de Cache Gerenciado|Suporte do Cache Redis do Azure|
|---|---|---|
|Caches nomeados|Um cache padrão está configurado e nas ofertas de cache Standard e Premium, até nove caches nomeados adicionais podem ser configuradas se desejado.|Os Caches Redis do Azure possuem um número configurável de bancos de dados (padrão de 16) que podem ser usados para implementar uma funcionalidade semelhante em caches nomeados. Para obter mais informações, confira [Configuração padrão do servidor Redis](cache-configure.md#default-redis-server-configuration).|
|Alta Disponibilidade|Fornece alta disponibilidade para itens no cache nas ofertas de cache Standard e Premium. Se os itens são perdidos devido a uma falha, cópias de backup dos itens no cache ainda ficam disponíveis. Gravações no cache secundário são feitas de forma síncrona.|Alta disponibilidade nas ofertas de cache Standard e Premium, que possuem uma configuração Principal/Réplica de dois nós (cada fragmento em um cache Premium tem um par de principal/réplica). Gravações de réplica são feitas de forma assíncrona. Para obter mais informações, confira [Preço do Cache Redis do Azure](https://azure.microsoft.com/pricing/details/cache/).|
|Notificações|Permite que os clientes recebam notificações assíncronas quando várias operações de cache ocorrem em um cache nomeado.|Aplicativos cliente podem usar o Redis pub/sub ou [Notificações de keyspace](cache-configure.md#keyspace-notifications-advanced-settings) para obter uma funcionalidade semelhante para notificações.|
|Cache local|Armazena uma cópia dos objetos armazenados em cache localmente no cliente para acesso extremamente rápido.|Os aplicativos cliente precisariam implementar essa funcionalidade usando um dicionário ou uma estrutura de dados semelhantes.|
|Política de remoção|Nenhuma ou LRU. A política padrão é LRU.|O Cache Redis do Azure suporta as seguintes políticas de remoção: volatile-lru, allkeys-lru, volatil-random, allkeys-random, volatile-ttl, noeviction. A política padrão é volatile-lru. Para obter mais informações, confira [Configuração padrão do servidor Redis](cache-configure.md#default-redis-server-configuration).|
|Política de expiração|A política de expiração padrão é Absolute e o intervalo de expiração padrão é de dez minutos. As políticas Sliding e Never também estão disponíveis.|Por padrão, itens no cache não expiram, mas uma expiração pode ser configurada por gravação usando sobrecargas de conjunto de cache. Para obter mais informações, consulte [Adicionar e recuperar objetos do cache](cache-dotnet-how-to-use-azure-redis-cache.md#add-and-retrieve-objects-from-the-cache).|
|Regiões e marcação|As regiões são subgrupos para itens em cache. Regiões também oferecem suporte à anotação dos itens em cache com mais sequências de caracteres descritivas denominadas marcas. As regiões oferecem suporte à capacidade de realizar operações de pesquisa em qualquer item marcado nessa região. Todos os itens dentro de uma região estão localizados dentro de um único nó do cluster de cache.|Um cache Redis consiste em um único nó (a menos que o cluster Redis esteja habilitado) então o conceito de regiões do Serviço de Cache Gerenciado não se aplica. O Redis oferece suporte à pesquisa e operações com caractere curinga ao recuperar chaves para que marcas descritivas possam ser incorporadas nos nomes de chave e usadas para recuperar os itens posteriormente. Para obter um exemplo de implementação de uma solução de marcação usando Redis, consulte [Implementar marcação de cache com Redis](http://stackify.com/implementing-cache-tagging-redis/).|
|Serialização|O Cache gerenciado suporta o NetDataContractSerializer, BinaryFormatter e o uso de serializadores personalizados. O padrão é o NetDataContractSerializer.|É responsabilidade do aplicativo cliente para serializar objetos .NET antes de colocá-los no cache, com a opção do serializador para o desenvolvedor do aplicativo cliente. Para obter mais informações e um código de exemplo, consulte [Trabalhar com objetos .NET no cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).|
| Emulador de cache | O Cache Gerenciado fornece um emulador de cache local. | O Cache Redis do Azure não tem um emulador, mas é possível [executar o build MSOpenTech do redis-server.exe localmente](cache-faq.md#cache-emulator) para fornecer uma experiência de emulador. |

## Escolher uma oferta de Cache

O Cache Redis do Microsoft Azure está disponível nas seguintes camadas:

-	**Básico** – um único nó. Vários tamanhos acima de 53 GB.
-	**Padrão** – principal/réplica com dois nós. Vários tamanhos acima de 53 GB. SLA de 99,9%.
-	**Premium** – dois nós Primário/Réplica com até 10 fragmentos. Vários tamanhos de 6 GB a 530 GB (entre em contato conosco para obter mais informações). Todos os recursos da camada Standard e muito mais, incluindo o suporte para o [cluster Redis](cache-how-to-premium-clustering.md), [persistência do Redis](cache-how-to-premium-persistence.md) e [Rede Virtual do Azure](cache-how-to-premium-vnet.md). SLA de 99,9%.

Cada camada é diferente em termos de recursos e preços. Os recursos são abordados posteriormente neste manual e, para obter mais informações sobre preços, consulte [Detalhes de preços do Cache](https://azure.microsoft.com/pricing/details/cache/).

Um ponto de partida para a migração é escolher o tamanho que corresponda ao tamanho do cache do Serviço de Cache Gerenciado anterior e, em seguida, aumentar ou diminuir, dependendo dos requisitos do seu aplicativo. Para obter instruções sobre como escolher a oferta certa do Cache Redis do Azure, consulte [Qual oferta de Cache Redis e tamanho devo usar?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## Criar um Cache

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## Configurar os clientes de Cache

Depois que o cache é criado e configurado, a próxima etapa é remover a configuração do Serviço de Cache Gerenciado e adicionar a configuração de Cache Redis do Azure e referências para que os clientes de cache possam acessar o cache.

-	Remover a configuração do Serviço de Cache Gerenciado
-	Configurar um cliente de cache usando o Pacote NuGet Stackexchange.Redis

### Remover a configuração do Serviço de Cache Gerenciado

Antes dos aplicativos cliente poderem ser configurados para o Cache Redis do Azure, a configuração do Serviço de Cache Gerenciado existente e referências de assembly devem ser removidas desinstalando o pacote NuGet de Serviço de Cache Gerenciado.

Para desinstalar o pacote NuGet do Serviço de Cache Gerenciado, clique com o botão direito do mouse no projeto do cliente no **Gerenciador de Soluções** e escolha **Gerenciar Pacotes NuGet**. Selecione o nó **Pacotes instalados** e digite W**indowsAzure.Caching** na caixa Pesquisar pacotes instalados. Selecione **Windows** **Cache do Azure** (ou **Windows** **Caching do Azure** dependendo da versão do pacote NuGet), clique em **Desinstalar**, e, em seguida, clique em **Fechar**.

![Desinstalar Packag de NuGet do Serviço de Cache Gerenciado do Azure](./media/cache-migrate-to-redis/IC757666.jpg)

Desinstalar o pacote NuGet de Serviço de Cache Gerenciado remove os assemblies do Serviço de Cache Gerenciado e as entradas do Serviço de Cache Gerenciado no app.config ou web.config do aplicativo cliente. Como algumas configurações personalizadas não podem ser removidas quando você desinstala o pacote NuGet, abra web.config ou app.config e certifique-se de que os seguintes elementos são completamente removidos.

Verifique se a entrada `dataCacheClients` é removida do elemento `configSections`. Não remova todo o elemento `configSections`; remova apenas a entrada `dataCacheClients`, se estiver presente.

    <configSections>
      <!-- Existing sections omitted for clarity. -->
      <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
    </configSections>

Verifique se a seção `dataCacheClients` é removida. A seção `dataCacheClients` será semelhante ao exemplo a seguir.

    <dataCacheClients>
      <dataCacheClientname="default">
        <!--To use the in-role flavor of Azure Cache, set identifier to be the cache cluster role name -->
        <!--To use the Azure Managed Cache Service, set identifier to be the endpoint of the cache cluster -->
        <autoDiscoverisEnabled="true"identifier="[Cache role name or Service Endpoint]"/>

        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
        <!--Use this section to specify security settings for connecting to your cache. This section is not required if your cache is hosted on a role that is a part of your cloud service. -->
        <!--<securityProperties mode="Message" sslEnabled="true">
          <messageSecurity authorizationInfo="[Authentication Key]" />
        </securityProperties>-->
      </dataCacheClient>
    </dataCacheClients>

Depois que a configuração do Serviço de Cache Gerenciado é removida, você pode configurar o cliente de cache conforme descrito na seção a seguir.

### Configurar um cliente de cache usando o Pacote NuGet Stackexchange.Redis

[AZURE.INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## Migrar o código do Serviço de Cache Gerenciado

A API para o cliente de cache StackExchange.Redis é semelhante para o Serviço de Cache Gerenciado. Esta seção fornece uma visão geral das diferenças.

### Conectar-se ao cache usando a classe ConnectionMultiplexer

No Serviço de Cache Gerenciado, as conexões com o cache eram manipuladas pelas classes `DataCacheFactory` e `DataCache`. No Cache Redis do Azure, essas conexões são gerenciadas pela classe `ConnectionMultiplexer`.

Adicione o seguinte usando a instrução na parte superior de qualquer arquivo do qual você deseja acessar o cache.

	using StackExchange.Redis
								
Se esse namespace não for resolvido, certifique-se de que você tenha adicionado o pacote NuGet StackExchange.Redis conforme descrito em [Configurar os clientes de cache](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

>[AZURE.NOTE] Observe que o cliente StackExchange.Redis requer o .NET Framework 4 ou superior.

Para se conectar a uma instância de Cache Redis do Azure, chame o método estático `ConnectionMultiplexer.Connect` e passe-o na chave e no ponto de extremidade. Uma abordagem para compartilhar uma instância do `ConnectionMultiplexer` em seu aplicativo deve ter uma propriedade estática que retorna uma instância conectada, semelhante ao exemplo a seguir. Isso oferece uma maneira segura para o thread para inicializar somente uma única instância conectada do `ConnectionMultiplexer`. Neste exemplo, `abortConnect` é definido como false, o que significa que a chamada terá êxito mesmo que não seja possível estabelecer uma conexão com o cache. Um recurso chave do `ConnectionMultiplexer` é que ele vai restaurar automaticamente a conectividade ao cache assim que o problema de rede ou outras causas sejam resolvidos.

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

O ponto de extremidade do cache, as chaves e portas podem ser obtidos na folha **Cache Redis** de sua instância de cache. Para obter mais informações, consulte as [Propriedades do Cache Redis](cache-configure.md#properties).

Quando a conexão for estabelecida, retorne uma referência para o banco de dados do redis cache chamando o método `ConnectionMultiplexer.GetDatabase`. O objeto retornado pelo método `GetDatabase` é um objeto leve de passagem e não precisa ser armazenado.

	IDatabase cache = Connection.GetDatabase();
	
	// Perform cache operations using the cache object...
	// Simple put of integral data types into the cache
	cache.StringSet("key1", "value");
	cache.StringSet("key2", 25);
	
	// Simple get of data types from the cache
	string key1 = cache.StringGet("key1");
	int key2 = (int)cache.StringGet("key2");

O cliente StackExchange.Redis usa os tipos `RedisKey` e `RedisValue` tipos para acessar e armazenar itens no cache. Esses tipos mapeiam para tipos de linguagem mais primitivos, incluindo a cadeia de caracteres e geralmente não são usados diretamente. As Cadeias de caracteres Redis são o tipo mais básico de valor do Redis e podem conter muitos tipos de dados, incluindo fluxos de binários serializados e apesar de você não poderá usar o tipo diretamente, você usará métodos que contêm `String` no nome. Para a maioria dos tipos de dados primitivos você armazena e recupera itens do cache usando os métodos `StringSet` e `StringGet`, a menos que você pretenda armazenar coleções ou outros tipos de dados Redis no cache.

`StringSet` e `StringGet` são muito semelhantes aos métodos `Put` e `Get` do Serviço de Cache Gerenciado, com uma grande diferença de que antes de definir e obter um objeto .NET no cache você deve serializá-lo primeiro.

Ao se chamar `StringGet`, se o objeto existir, ele será retornado e, se não existir, será retornado nulo. Nesse caso você pode recuperar o valor da fonte de dados desejado e armazená-lo no cache para uso subsequente. Isso é conhecido como o padrão cache-aside.

Para especificar a expiração de um item no cache, use o parâmetro `TimeSpan` de `StringSet`.

	cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));

O Cache Redis do Azure pode trabalhar com objetos .NET, bem como tipos de dados primitivos, mas antes que um objeto .NET possa ser armazenado em cache, ele deve ser serializado. Isso é responsabilidade do desenvolvedor do aplicativo. Isso oferece flexibilidade ao desenvolvedor na escolha do serializador. Para obter mais informações e um código de exemplo, consulte [Trabalhar com objetos .NET no cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

## Migrar o Estado de sessão do ASP.NET e cache de saída para o Cache Redis do Azure

O Cache Redis do Azure possui provedores de Estado da Sessão ASP.NET e o caching de Saída de Página. Para migrar seu aplicativo que usa as versões de Serviço de Cache Gerenciado desses provedores, primeiro remova as seções existentes do web.config e configure as versões de Cache Redis do Azure dos provedores. Para obter instruções sobre como usar os provedores ASP.NET de Cache Redis do Azure, consulte [Provedor de estado de sessão ASP.NET para Cache Redis do Azure](cache-aspnet-session-state-provider.md) e [Provedor de Cache de saída do ASP.NET para Cache Redis do Azure](cache-aspnet-output-cache-provider.md).

## Próximas etapas

Explore a [documentação do Cache Redis do Azure](https://azure.microsoft.com/documentation/services/cache/) e veja os tutoriais, exemplos, vídeos e muito mais.

<!---HONumber=AcomDC_0615_2016-->