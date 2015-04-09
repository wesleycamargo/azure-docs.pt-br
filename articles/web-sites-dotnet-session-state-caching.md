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
	ms.date="03/24/2015" 
	ms.author="riande"/>


# Estado de sessão com cache Redis do Azure no Serviço de Aplicativo do Azure


Este tópico explica como usar o Serviço de Cache Redis do Azure para o estado de sessão.

Se seu aplicativo Web do ASP.NET utilizar o estado de sessão, é necessário configurar um provedor externo de estado de sessão (provedor de estado de sessão de Serviço de Cache Redis ou de SQL Server). Se você usar o estado de sessão, e não usar um provedor externo, você será limitado a uma instância do seu aplicativo web. O Serviço Cache Redis é o mais rápido e o mais simples de habilitar.

<h2><a id="createcache"></a>Criar o cache</h2>
Siga [estas instruções](cache-dotnet-how-to-use-azure-redis-cache.md#create-cache) para criar o cache.

<h2><a id="configureproject"></a>Adicionar o pacote NuGet RedisSessionStateProvider a seu aplicativo Web</h2>
Instalar o pacote NuGet `RedisSessionStateProvider`.  Use o seguinte comando para instalar no console de gerenciador de pacotes (**Ferramentas** > **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**):

  `PM> Install-Package Microsoft.Web.RedisSessionStateProvider`
  
Para instalar em **Ferramentas** > **Gerenciador de Pacotes NuGet** > **Gerenciar Pacotes NuGet para Solução**, procure por `RedisSessionStateProvider`.

Para obter mais informações, consulte a página [RedisSessionStateProvider de NuGet](http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/ ) e [Configurar o cliente de cache](cache-dotnet-how-to-use-azure-redis-cache.md#NuGet).

<h2><a id="configurewebconfig"></a>Modificar o arquivo .Web.Config</h2>
Além de fazer referências do assembly para o Cache, o pacote NuGet adiciona entradas stub no arquivo *web.config*. 

1. Abra o *web.config* e localize o elemento **sessionState**.

1. Insira os valores para `host`, `accessKey`, `port` (a porta SSL deve ser 6380), e configure  `SSL` como  `true`. Esses valores podem ser obtidos na lâmina do [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715) para sua instância de cache. Para obter mais informações, consulte [Conectar-se ao cache](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-cache). Observe que a porta que não é do tipo SSL é desabilitada por padrão para novos caches. Para obter mais informações sobre como habilitar a porta que não é do tipo SSL, consulte a seção [Portas de Acesso](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) no tópico [Configurar um cache no Cache Redis do Azure](https://msdn.microsoft.com/library/azure/dn793612.aspx). A marcação a seguir mostra as alterações no arquivo *web.config*.


  <pre class="prettyprint">  
    &lt;system.web&gt;
    &lt;customErrors mode="Off" /&gt;
    &lt;authentication mode="None" /&gt;
    &lt;compilation debug="true" targetFramework="4.5" /&gt;
    &lt;httpRuntime targetFramework="4.5" /&gt;
  &lt;sessionState mode="Custom" customProvider="RedisSessionProvider"&gt;
      &lt;providers&gt;  
          &lt;!--&lt;add name="RedisSessionProvider" 
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            throwOnError = "true" [true|false]
            retryTimeoutInMilliseconds = "0" [number]
            databaseId = "0" [number]
            applicationName = "" [String]
          /&gt;--&gt;
         &lt;add name="RedisSessionProvider" 
              type="Microsoft.Web.Redis.RedisSessionStateProvider" 
              <mark>port="6380"
              host="movie2.redis.cache.windows.net" 
              accessKey="m7PNV60CrvKpLqMUxosC3dSe6kx9nQ6jP5del8TmADk=" 
              ssl="true"</mark> /&gt;
      &lt;!--&lt;add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false" /&gt;--&gt;
      &lt;/providers&gt;
    &lt;/sessionState&gt;
  &lt;/system.web&gt;</pre>


<h2><a id="usesessionobject"></a>Utilizar o objeto da sessão em código</h2>
A etapa final é começar a utilizar o objeto Session em seu código do ASP.NET. Você pode adicionar objetos ao estado de sessão usando o método **Session.Add**. Este método utiliza pares de chave/valor para armazenar itens no cache do estado de sessão.

    string strValue = "yourvalue";
	Session.Add("yourkey", strValue);

The following code retrieves this value from session state.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)objValue;	

É possível utilizar o Cache Redis para armazenar em cache objetos no seu aplicativo Web. Para obter mais informações, consulte [Aplicativo de filme MVC com o Cache Redis do Azure em 15 minutos](http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/).
Para mais detalhes sobre como usar o estado de sessão do ASP.NET, consulte [Visão geral do estado de sessão do ASP.NET][].

>[AZURE.NOTE] Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## O que mudou
* Para obter um guia para a mudança de sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia para a mudança do portal antigo para o novo portal, consulte: [Referência para navegação no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

  *Por [Rick Anderson](https://twitter.com/RickAndMSFT)*
  
  [instalado o mais recente]: http://www.windowsazure.com/downloads/?sdk=net  
  [Visão geral do estado de sessão do ASP.NET]: http://msdn.microsoft.com/library/ms178581.aspx

  [NewIcon]: ./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png
  [NewCacheDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png
  [CacheIcon]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png
  [NuGetDialog]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png
  [OutputConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_OC_WebConfig.png
  [CacheConfig]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png
  [EndpointURL]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png
  [ManageKeys]: ./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png

<!--HONumber=49-->