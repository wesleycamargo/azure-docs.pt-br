<properties 
	pageTitle="Estado de sessão com cache Redis do Azure no Serviço de Aplicativo do Azure" 
	description="Aprenda a usar o serviço de cache do Azure para dar suporte a cache de estado de sessão do ASP.NET." 
	services="app-service\web" 
	documentationCenter=".net" 
 	authors="Rick-Anderson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/06/2015" 
	ms.author="riande"/>


# Estado de sessão com cache Redis do Azure no Serviço de Aplicativo do Azure


Este tópico explica como usar o Serviço de Cache Redis do Azure para o estado de sessão.

Se seu aplicativo web do ASP.NET usar o estado de sessão, você precisará configurar um provedor externo de estado de sessão (provedor de estado de sessão de Serviço de Cache Redis ou de SQL Server). Se você usar o estado de sessão, e não usar um provedor externo, você será limitado a uma instância do seu aplicativo web. O Serviço Cache Redis é o mais rápido e o mais simples de habilitar.

##<a id="createcache"></a>Criar o cache
Seguir [estas instruções](../cache-dotnet-how-to-use-azure-redis-cache.md#create-cache) para criar o cache.

##<a id="configureproject"></a>Adicionar o pacote NuGet RedisSessionStateProvider em seu aplicativo Web
Instalar o pacote `RedisSessionStateProvider` NuGet. Use o seguinte comando para instalar a partir da console de gerenciador de pacotes (**Ferramentas** > **Gerenciador de pacotes NuGet** > **Console de gerenciador de pacotes**):

  `PM> Install-Package Microsoft.Web.RedisSessionStateProvider`
  
Para instalar de **Ferramentas** > **Gerenciador de Pacotes NuGet** > **Gerenciar Pacotes NuGet para Solução**, procure `RedisSessionStateProvider`.

Para obter mais informações consulte a [Página RedisSessionStateProvider de NuGet](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/) e [Configurar o cliente do cache](../cache-dotnet-how-to-use-azure-redis-cache.md#NuGet).

##<a id="configurewebconfig"></a>Modificar o arquivo Web.Config
Além de realizar referências do assembly para o Cache, o pacote NuGet adiciona entradas stub no arquivo *web.config*.

1. Abra o *web.config* e localize o elemento **sessionState**.

1. Insira os valores de `host`, `accessKey`, `port` (a porta SSL deve ser 6380) e defina `SSL` como `true`. Você pode obter esses valores na folha do [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715) da sua instância de cache. Para saber mais, confira [Conectar ao cache](../cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-cache). Observe que a porta que não é do tipo SSL é desabilitada por padrão para novos caches. Para saber mais sobre como habilitar a porta que não é do tipo SSL, confira a seção [Portas de Acesso](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) no tópico [Configurar um cache no Cache Redis do Azure](https://msdn.microsoft.com/library/azure/dn793612.aspx). A marcação a seguir mostra as alterações do arquivo *Web. config*, especificamente as alterações na *porta*, *host*, accessKey* e *ssl*.

		  <system.web>;
		    <customErrors mode="Off" />;
		    <authentication mode="None" />;
		    <compilation debug="true" targetFramework="4.5" />;
		    <httpRuntime targetFramework="4.5" />;
		    <sessionState mode="Custom" customProvider="RedisSessionProvider">;
		      <providers>;  
		          <!--<add name="RedisSessionProvider" 
		            host = "127.0.0.1" [String]
		            port = "" [number]
		            accessKey = "" [String]
		            ssl = "false" [true|false]
		            throwOnError = "true" [true|false]
		            retryTimeoutInMilliseconds = "0" [number]
		            databaseId = "0" [number]
		            applicationName = "" [String]
		          />;-->;
		         <add name="RedisSessionProvider" 
		              type="Microsoft.Web.Redis.RedisSessionStateProvider" 
		              port="6380"
		              host="movie2.redis.cache.windows.net" 
		              accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
		              ssl="true" />;
		      <!--<add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" />;-->;
		      </providers>;
		    </sessionState>;
		  </system.web>;


##<a id="usesessionobject"></a> Usar o objeto da sessão em código
A etapa final é começar a usar o objeto da Sessão em seu código do ASP.NET. Você pode adicionar objetos ao estado de sessão usando o método **Session.Add**. Este método utiliza pares de chave/valor para armazenar itens no cache do estado de sessão.

    string strValue = "yourvalue";
	Session.Add("yourkey", strValue);

O código seguinte recupera este valor do estado de sessão.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)objValue;	

Você também pode usar o Cache de Redis para armazenar em cache objetos no seu aplicativo web. Para saber mais, confira [Aplicativo de filme MVC com o Cache Redis do Azure em 15 minutos](http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/). Para obter mais detalhes sobre como utilizar o estado de sessão do ASP.NET, confira [Visão geral do estado de sessão do ASP.NET][].

>[AZURE.NOTE]Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia sobre a alteração do portal antigo para o novo portal, confira: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

  *por [Rick Anderson](https://twitter.com/RickAndMSFT)*
  
  [installed the latest]: http://www.windowsazure.com/downloads/?sdk=net
  [Visão geral do estado de sessão do ASP.NET]: http://msdn.microsoft.com/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png
 

<!---HONumber=August15_HO7-->