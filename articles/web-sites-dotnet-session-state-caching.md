<properties urlDisplayName="index" pageTitle="Utilizar o estado de sessão ASP.NET com Sites do Azure" metaKeywords="azure cache service session state" description="Learn how to use the Azure Cache Service to support ASP.NET session state caching." metaCanonical="" services="cache" documentationCenter=".NET" title="How to Use ASP.NET Session State with Azure Websites" authors="riande"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/13/2014" ms.author="riande" />


# Como usar o estado de sessão ASP.NET com Sites do Azure

*por [Rick Anderson](https://twitter.com/RickAndMSFT). Atualizado em 1 de julho de 2014.*

Este tópico explica como usar o Serviço de Cache Redis (Visualização) do Azure para o estado de sessão.

Se seu aplicativo Web do ASP.NET utilizar o estado de sessão, é necessário configurar um provedor externo de estado de sessão (provedor de estado de sessão de Serviço de Cache Redis ou de SQL Server). Se você usar o estado de sessão, e não usar um provedor externo, você será limitado a uma instância do seu aplicativo web. O Serviço de Cache Redis é o mais rápido e o mais simples de habilitar.

As etapas básicas para usar o Serviço de Cache (Visualização) para o caching do estado de sessão incluem:

* [Criar o cache.](#createcache)
* [Adicionar o pacote NuGet RedisSessionStateProvider a seu aplicativo web.](#configureproject)
* [Modificar o arquivo web.config](#configurewebconfig)
* [Usar o objeto da Sessão para armazenar e recuperar itens em cache.](#usesessionobject)

<h2><a id="createcache"></a>Criar o cache</h2>
Seguir [estas instruções](http://azure.microsoft.com/pt-br/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#create-cache) para criar o cache.

<h2><a id="configureproject"></a>Adicionar o pacote NuGet RedisSessionStateProvider a seu aplicativo Web</h2>
Instalar o pacote NuGet 'RedisSessionStateProvider'.  Utilize o seguinte comando para fazer a instalação no console do gerenciador de pacotes (**Ferramentas** > **Gerenciador de pacotes NuGet** > **Console do gerenciador de pacotes**):

  `PM> Install-Package RedisSessionStateProvider -IncludePrerelease`
  
Para fazer a instalação em **Ferramentas** > **Gerenciador de pacotes NuGet** > **Gerenciador de pacotes NuGe para soluções**, pesquise por `RedisSessionStateProvider` e certifique-se de especificar **Incluir pré-lançamento**.

Para obter mais informações consulte a [Página do NuGet RedisSessionStateProvider](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/ ) e [Configure o cliente de cache](http://azure.microsoft.com/pt-br/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#NuGet).

<h2><a id="configurewebconfig"></a>Modificar o arquivo .web.config</h2>
Além de fazer referências de assembly para o Cache, o pacote NuGet adiciona entradas stub no arquivo *web.config*. 

1. Abra o *web.config* e localize o elemento **sessionState**.

1. Digite os valores de 'host', 'accessKey', 'porta' (a porta SSL deve ser 6380) e defina 'SSL' como 'true'. Esses valores podem ser obtidos da lâmina do portal de visualização de gerenciamento do Azure para sua instância de cache. Para obter mais informações, consulte [Conectar ao cache](http://azure.microsoft.com/pt-br/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#connect-to-cache).
A marcação a seguir mostra as alterações no arquivo *web.config*.


  <pre class="prettyprint">  
    <system.web>
    <customErrors mode="Off" />
    <authentication mode="None" />
    <compilation debug="true" targetFramework="4.5" />
    <httpRuntime targetFramework="4.5" />
  <sessionState mode="Custom" customProvider="RedisSessionProvider">
      <providers>  
          <!--<add name="RedisSessionProvider" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
          />-->
         <add name="RedisSessionProvider" 
              type="Microsoft.Web.Redis.RedisSessionStateProvider" 
              <mark>port="6380"
              host="movie2.redis.cache.windows.net" 
              accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
              ssl="true"</mark> />
      <!--<add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />-->
      </providers>
    </sessionState>
  </system.web></pre>


<h2><a id="usesessionobject"></a>Utilizar o objeto da sessão em código</h2>
A etapa final é começar a utilizar o objeto Session em seu código do ASP.NET. Você pode adicionar objetos ao estado de sessão usando o método **Session.Add**. Este método utiliza pares de chave/valor para armazenar itens no cache do estado de sessão.

    string strValue = "yourvalue";
	Session.Add("yourkey", strValue);

O código seguinte recupera este valor do estado de sessão.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)obj;	

É possível utilizar o Cache Redis para armazenar em cache objetos no seu aplicativo Web. Para obter mais informações, consulte [Aplicativo de filme MVC com o Cache Redis do Azure em 15 minutos](http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/).
Para obter mais detalhes sobre como utilizar o estado de sessão do ASP.NET, consulte [Visão geral do estado de sessão do ASP.NET][].

  
  
  [instalado o mais recente]: http://www.windowsazure.com/pt-br/downloads/?sdk=net  
  [Visão geral do estado de sessão do ASP.NET]: http://msdn.microsoft.com/pt-br/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png
