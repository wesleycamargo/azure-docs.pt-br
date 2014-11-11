<properties linkid="video-center-index" urlDisplayName="index" pageTitle="Use ASP.NET session state with Azure Websites" metaKeywords="azure cache service session state" description="Learn how to use the Azure Cache Service to support ASP.NET session state caching." metaCanonical="" services="cache" documentationCenter=".NET" title="How to Use ASP.NET Session State with Azure Websites" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra" />

# Como usar o estado de sessão ASP.NET com Sites do Azure

*Por [Rick Anderson][Rick Anderson] atualizado em 1 de julho de 2014.*

Este tópico explica como usar o Serviço de Cache Redis (Visualização) do Azure para o estado de sessão.

Se seu aplicativo web do ASP.NET usar o estado de sessão, você precisará configurar um provedor externo de estado de sessão (provedor de estado de sessão de Serviço de Cache Redis ou de SQL Server). Se você usar o estado de sessão, e não usar um provedor externo, você será limitado a uma instância do seu aplicativo web. O Serviço Cache Redis é o mais rápido e o mais simples de habilitar.

As etapas básicas para usar o Serviço de Cache (Visualização) para o caching do estado de sessão incluem:

-   [Criar o cache.][Criar o cache.]
-   [Adicionar o pacote NuGet RedisSessionStateProvider a seu aplicativo web.][Adicionar o pacote NuGet RedisSessionStateProvider a seu aplicativo web.]
-   [Modificar o arquivo web.config][Modificar o arquivo web.config]
-   [Usar o objeto da Sessão para armazenar e recuperar itens em cache.][Usar o objeto da Sessão para armazenar e recuperar itens em cache.]

## <span id="createcache"></span></a>Criar o cache

Seguir [estas instruções][estas instruções] para criar o cache.

## <span id="configureproject"></span></a>Adicionar o pacote NuGet RedisSessionStateProvider a seu aplicativo web.

Instalar o pacote `RedisSessionStateProvider` NuGet. Use o seguinte comando para instalar a partir da console de gerenciador de pacotes (**Ferramentas** \> **Gerenciador de pacotes NuGet** \> **Console de gerenciador de pacotes**):

`PM> Install-Package RedisSessionStateProvider -IncludePrerelease`

Para instalar a partir de **Ferramentas** \> **Gerenciador de pacotes NuGet** \> **Gerenciar pacotes NuGet para Soluções**, pesquisar o `RedisSessionStateProvider` e certificar-se de especificar **Incluir Pré-lançamento**.

Para obter mais informações consulte a [Página RedisSessionStateProvider de NuGet][Página RedisSessionStateProvider de NuGet] e [Configurar o cliente do cache][Configurar o cliente do cache].

## <span id="configurewebconfig"></span></a>Modificar o arquivo .web.config

Além de realizar referências do assembly para o Cache, o pacote NuGet adiciona entradas stub no arquivo *web.config*.

1.  Abra o *web.config* e localize o elemento **sessionState**.

2.  Inserir os valores para `host`, `accessKey`, `port` (a porta SSL deve ser 6380), e configurar o `SSL` para `true`. Esses valores podem ser obtidos da lâmina do portal de visualização de gerenciamento do Azure para sua instância de cache. Para obter mais informações, consulte [Conectar-se ao cache][Conectar-se ao cache].
    A seguinte marcação mostra as alterações no arquivo *web.config*.

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

## <span id="usesessionobject"></span></a>Utilizar o objeto da sessão em código

A etapa final é começar a usar o objeto da Sessão em seu código do ASP.NET. Você pode adicionar objetos ao estado de sessão usando o método **Session.Add**. Este método utiliza pares de chave/valor para armazenar itens no cache do estado de sessão.

    string strValue = "yourvalue";
    Session.Add("yourkey", strValue);

O código seguinte recupera este valor do estado de sessão.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)obj;  

Você também pode usar o Cache de Redis para armazenar em cache objetos no seu aplicativo web. Para obter mais informações, consulte [aplicativo de filme MVC com o Cache Redis do Azure em 15 minutos][aplicativo de filme MVC com o Cache Redis do Azure em 15 minutos].
Para obter mais detalhes sobre como usar o estado de sessão do ASP.NET, consulte [Visão geral do estado de sessão do ASP.NET][Visão geral do estado de sessão do ASP.NET].

  [Rick Anderson]: https://twitter.com/RickAndMSFT
  [Criar o cache.]: #createcache
  [Adicionar o pacote NuGet RedisSessionStateProvider a seu aplicativo web.]: #configureproject
  [Modificar o arquivo web.config]: #configurewebconfig
  [Usar o objeto da Sessão para armazenar e recuperar itens em cache.]: #usesessionobject
  [estas instruções]: http://azure.microsoft.com/pt-BR/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#create-cache
  [Página RedisSessionStateProvider de NuGet]: http://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider/
  [Configurar o cliente do cache]: http://azure.microsoft.com/pt-BR/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#NuGet
  [Conectar-se ao cache]: http://azure.microsoft.com/pt-BR/documentation/articles/cache-dotnet-how-to-use-azure-redis-cache/#connect-to-cache
  [aplicativo de filme MVC com o Cache Redis do Azure em 15 minutos]: http://azure.microsoft.com/blog/2014/06/05/mvc-movie-app-with-azure-redis-cache-in-15-minutes/
  [Visão geral do estado de sessão do ASP.NET]: http://msdn.microsoft.com/pt-BR/library/ms178581.aspx
