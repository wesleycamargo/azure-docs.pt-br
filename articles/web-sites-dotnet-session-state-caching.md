<properties linkid="video-center-index" urlDisplayName="índice" pageTitle="Índice da Central de Vídeos" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Como usar o estado de sessão ASP.NET com Sites do Azure" authors=""  solutions="" writer="jroth" manager="" editor=""  />




# Como usar o estado de sessão ASP.NET com Sites do Azure

Este tópico explica como usar o Serviço de Cache (Visualização) do Azure para dar suporte ao caching de estado de sessão ASP.NET.

Sem um provedor externo, o estado de sessão é armazenado, no processo, no servidor Web que hospeda o site. Para Sites do Azure, há dois problemas com o estado de sessão em processo. Em primeiro lugar, para sites com múltiplas instâncias, o estado de sessão armazenado em uma instância não é acessível a outras instâncias. Como uma solicitação de usuário pode ser encaminhada a qualquer instância, não é garantido que a informação da sessão esteja lá. Em segundo lugar, qualquer alteração nas configurações poderia fazer com que o site fosse executado em um servidor completamente diferente.

O Serviço de Cache (Visualização) fornece um serviço distribuído de cache que é externo ao site. Isto resolve o problema com o estado de sessão em processo. Para obter mais informações sobre como usar o estado de sessão, consulte [Visão geral do estado de sessão do ASP.NET][].

As etapas básicas para usar o Serviço de Cache (Visualização) para o caching do estado de sessão incluem:

* [Criar o cache.](#createcache)
* [Configurar o projeto ASP.NET para usar o Cache do Azure.](#configureproject)
* [Modificar o arquivo web.config](#configurewebconfig)
* [Usar o objeto da Sessão para armazenar e recuperar itens em cache.](#usesessionobject)

<h2><a id="createcache"></a>Criar o cache</h2>
1. Na parte inferior do Portal de Gerenciamento do Azure, clique no ícone **Novo**.

	![NewIcon][NewIcon]

2. Selecione **Serviços de Dados**, **Cache** e, em seguida, clique em **Criação Rápida**.

	![NewCacheDialog][NewCacheDialog]

3. Digite um nome exclusivo para o cache na caixa de texto **Ponto de Extremidade**. Então, selecione os valores adequados às outras propriedades do cache e clique em **Criar um Novo Cache**.

4. Selecione o ícone **Cache** no Portal de Gerenciamento para visualizar todos os seus pontos de extremidade do Serviço de Cache.

	![CacheIcon][CacheIcon]

5. Em seguida, você poderá selecionar um dos pontos de extremidade do Serviço de Cache para visualizar suas propriedades. As seções seguintes utilizarão as configurações da guia **Painel** para configurar o Caching para um projeto ASP.NET.

<h2><a id="configureproject"></a>Configurar o projeto ASP.NET</h2>
1. Primeiro, certifique-se de que você tenha [instalado o mais recente][]  **SDK do Azure SDK para .NET**.

2. No Visual Studio, clique com o botão direito do mouse no projeto ASP.NET em **Gerenciador de Soluções** e, em seguida, selecione **Gerenciar Pacotes NuGet**. (Se você está usando o WebMatrix, em vez disto, clique no botão **NuGet**).

3. Digite **WindowsAzure.Caching** na caixa de edição **Pesquisar Online**.

	![NuGetDialog][NuGetDialog]

4. Selecione o pacote **Caching do Azure** e, em seguida, clique no botão **Instalar**.

<h2><a id="configurewebconfig"></a>Modificar o arquivo .web.config</h2>
Além de realizar referências do assembly para o Cache, o pacote NuGet adiciona entradas stub no arquivo web.config. Para usar o Cache para o estado de sessão, várias modificações devem ser realizadas no arquivo web.config.

1. Abra o arquivo **web.config** para o projeto ASP.NET.

2. Encontre o elemento existente **sessionState** e comente-o (ou remova-o).

3. Em seguida, remova o comentário do elemento **sessionState** que foi adicionado pelo pacote NuGet do Caching do Azure. O resultado final deve ser semelhante à seguinte captura de tela.

	![SessionStateConfig][SessionStateConfig]

4. Em seguida, encontre a seção **dataCacheClients**. Remova o comentário do elemento filho **securityProperties**.

	![CacheConfig][CacheConfig]

5. No elemento **autoDiscover**, defina o atributo **identifier** para a URL do ponto de extremidade do cache. Para encontrar a URL do seu ponto de extremidade, vá em propriedades do cache no Portal de Gerenciamento do Azure. Na guia **Painel**, copie o valor da **URL DO PONTO DE EXTREMIDADE** na seção **Visão Rápida**.

	![EndpointURL][EndpointURL]

6. No elemento **messageSecurity**, defina o atributo **authorizationInfo** para sua chave de acesso do cache. Para encontrar a chave de acesso, selecione o seu cache no Portal de Gerenciamento Azure. Em seguida, clique no ícone **Gerenciar Chaves** na barra inferior. Clique no botão copiar próximo à caixa de texto **CHAVE DE ACESSO PRIMÁRIA**.

	![ManageKeys][ManageKeys]

<h2><a id="usesessionobject"></a>Utilizar o objeto da sessão em código</h2>
A etapa final é começar a usar o objeto da Sessão em seu código do ASP.NET. Você pode adicionar objetos ao estado de sessão usando o método **Session.Add**. Este método utiliza pares de chave/valor para armazenar itens no cache do estado de sessão.

    string strValue = "yourvalue";
	Session.Add("yourkey", strValue);

O código seguinte recupera este valor do estado de sessão.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)obj;	

Para mais detalhes sobre como usar o estado de sessão do ASP.NET, consulte [Visão geral do estado de sessão do ASP.NET][].

  
  
  
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

