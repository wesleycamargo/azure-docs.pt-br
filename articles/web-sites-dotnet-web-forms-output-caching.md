<properties linkid="video-center-detail" urlDisplayName="detalhes" pageTitle="Detalhes da Central de Vídeo" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Como usar armazenamento em cache de saída de Formulários da Web do ASP.NET com Sites do Azure" authors="jroth" solutions="" manager="" editor="" />



# Como usar armazenamento em cache de saída de Formulários da Web do ASP.NET com Sites do Azure

Este tópico explica como usar o Serviço de Cache do Azure (Visualização) para dar suporte ao armazenamento em cache de saída da página do ASP.NET para Formulários da Web do ASP.NET. Armazenamento em cache de saída da página é uma otimização que retorna diretamente uma página de renderização durante um período específico. Isso é útil em situações nas quais uma página é acessada com mais frequência do que normalmente muda. É importante observar que o armazenamento em cache de saída da página não é compatível com aplicativos MVC do ASP.NET.

O Serviço de Cache (Visualização) fornece um serviço distribuído de cache que é externo ao site. Isso permite que todas as instâncias do site acessem a mesma renderização armazenada em cache de uma página.

Entre as etapas básicas para usar o Serviço de Cache (Visualização) no armazenamento em cache de saída da página estão:

* [Criar o cache.](#createcache)
* [Configure o projeto ASP.NET para usar o Cache do Azure.](#configureproject)
* [Modificar o arquivo web.config](#configurewebconfig)
* [Use o armazenamento em cache de saída para retornar temporariamente versões armazenadas em cache de uma página.](#useoutputcaching)

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
1. Primeiro, verifique se você [instalou o mais recente][]  **SDK do Azure SDK para .NET**.

2. No Visual Studio, clique com o botão direito do mouse no projeto ASP.NET em **Gerenciador de Soluções** e, em seguida, selecione **Gerenciar Pacotes NuGet**. (Se você estiver usando o WebMatrix, clique no botão **NuGet** da barra de ferramentas)

3. Digite **WindowsAzure.Caching** na caixa de edição **Pesquisar Online**.

	![NuGetDialog][NuGetDialog]

4. Selecione o pacote **Azure Caching** e clique no botão **Instalar**.

<h2><a id="configurewebconfig"></a>Modificar o arquivo .web.config</h2>
Além de fazer referências de assembly para o Cache, o pacote NuGet adiciona entradas stub no arquivo web.config. Para usar o Cache no armazenamento em cache de saída da página do ASP.NET, várias modificações devem ser feitas em web.config.

1. Abra o arquivo **web.config** do projeto ASP.NET.

2. Se você tiver elementos **caching** e **outputCache** existentes, comente-os (ou remova-os).

3. Em seguida, remova o comentário do elemento **caching** que foi adicionado pelo pacote NuGet do Armazenamento em Cache do Azure. O resultado final deve ser semelhante à seguinte captura de tela.

	![OutputConfig][OutputConfig]

4. Em seguida, localize a seção **dataCacheClients**. Remova o comentário do elemento filho **securityProperties**.

	![CacheConfig][CacheConfig]

5. No elemento **autoDiscover**, defina o atributo **identifier** como a URL do ponto de extremidade do cache. Para localizar a URL do ponto de extremidade, vá até as propriedades do cache no Portal de Gerenciamento do Azure. Na guia **Painel**, copie o valor da **URL DO PONTO DE EXTREMIDADE** na seção **Visão Rápida**.

	![EndpointURL][EndpointURL]

6. No elemento **messageSecurity**, defina o atributo **authorizationInfo** como a chave de acesso do cache. Para localizar a chave de acesso, selecione o cache no Portal de Gerenciamento Azure. Em seguida, clique no ícone **Gerenciar Chaves** na barra inferior. Clique no botão copiar próximo à caixa de texto **CHAVE DE ACESSO PRIMÁRIA**.

	![ManageKeys][ManageKeys]

<h2><a id="useoutputcaching"></a>Usar armazenamento em cache de saída</h2>
A etapa final é configurar páginas no aplicativo de formulários da Web do ASP.NET para usar o armazenamento em cache da saída. Isso pode ser feito adicionando-se um atributo **OutputCache** ao início do código-fonte .ASPX. Por exemplo:

	<%@ OutputCache Duration="45" VaryByParam="*" %>

O exemplo anterior armazena em cache a página durante quarenta e cinco segundos. Como **VaryByParam** é definido como "*", a saída dessa página armazenada em cache não mudará, mesmo que parâmetros de consulta diferentes sejam passados. Mas o exemplo a seguir armazena em cache uma versão diferente da página para cada valor do parâmetro "UserId":

	<%@ OutputCache Duration="45" VaryByParam="UserId" %>	

  
  
  
[instalou o mais recente]: http://www.windowsazure.com/pt-br/downloads/?sdk=net
[NewIcon]: ./media/web-sites-web-forms-output-caching/CacheScreenshot_NewButton.PNG
[NewCacheDialog]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CreateOptions.PNG
[CacheIcon]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheIcon.PNG
[NuGetDialog]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_NuGet.PNG
[OutputConfig]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_OC_WebConfig.PNG
[CacheConfig]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheConfig.PNG
[EndpointURL]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_EndpointURL.PNG
[ManageKeys]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_ManageAccessKeys.PNG
  
  
  

