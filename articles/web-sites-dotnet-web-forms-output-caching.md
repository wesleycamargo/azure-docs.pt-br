<properties urlDisplayName="details" pageTitle="Detalhes da Central de vídeo" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use ASP.NET Web Forms Output Caching with Azure Websites" authors="sdanie" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/11/2014" ms.author="sdanie" />



# Como usar armazenamento em cache de saída de Formulários da Web do ASP.NET com Sites do Azure

Este tópico explica como utilizar o Serviço de Cache do Azure (visualização) para fornecer suporte ao armazenamento em cache de saída da página do ASP.NET para Formulários da Web do ASP.NET. Armazenamento em cache de saída da página é uma otimização que retorna diretamente uma página de renderização durante um período específico. Isso é útil em situações nas quais uma página é acessada com mais frequência do que normalmente muda. É importante observar que o armazenamento em cache de saída da página não é compatível com aplicativos ASP.NET MVC.

O Serviço de Cache (visualização) fornece um serviço distribuído de cache que é externo ao site. Isso permite que todas as instâncias do site acessem a mesma renderização armazenada em cache de uma página.

Entre as etapas básicas para usar o Serviço de Cache (Visualização) no armazenamento em cache de saída da página estão:

* [Criar o cache.](#createcache)
* [Configure o projeto ASP.NET para usar o Cache do Azure.](#configureproject)
* [Modificar o arquivo web.config](#configurewebconfig)
* [Use o armazenamento em cache de saída para retornar temporariamente versões armazenadas em cache de uma página.](#useoutputcaching)

<h2><a id="createcache"></a>Criar o cache</h2>
As instâncias em cache no Serviço de Cache Gerenciado são criadas utilizando os cmdlets do PowerShell. 

>Assim que uma instância de Serviço de Cache Gerenciado for criada utilizando os cmdlets do PowerShell, ela pode ser visualizada e configurada no [Portal de gerenciamento do Azure][].

Para criar uma instância de Serviço de Cache Gerenciado, abra uma janela de comando do PowerShell.

>Para obter instruções sobre como instalar e utilizar o PowerShell do Azure, consulte [Como instalar e configurar o Azure Powershell][].

Invocar o cmdlet [Add-AzureAccount][] e inserir o endereço do email e a senha associada com a sua conta. Uma assinatura é escolhida por padrão e é exibida após ter invocado o cmdlet [Add-AzureAccount][]. Para alterar a assinatura, invocar o cmdlet [Select-AzureSubscription][].

>Se você tiver configurado o PowerShell do Azure com um certificado para sua conta, então você pode pular esta etapa. Para obter mais informações sobre conectar o PowerShell do Azure com sua conta do Azure, consulte [Como instalar e configurar o PowerShell do Azure][].

Por padrão, uma assinatura é escolhida e exibida. Para alterar a assinatura, invocar o cmdlet [Select-AzureSubscription][].

Invocar o cmdlet [New-AzureManagedCache][] e especificar o nome, região, oferta de cache e tamanho para o cache.

Para **Nome**, insira um nome de subdomínio a ser utilizado para o ponto de extremidade do cache. O ponto de extremidade deve ser uma cadeia de seis a vinte caracteres, deve conter apenas números e letras minúsculas e deve começar com uma letra.

Para **Local**, especifique uma região para o cache. Para obter melhor desempenho, crie o cache na mesma região que o aplicativo de cliente de cache.

**Sku** e **Memória** funcionam em conjunto para determinar o tamanho do cache. O Serviço de Cache Gerenciado está disponível nas três camadas a seguir.

-	Básica - Cache em tamanhos de 128 MB a 1 GB em incrementos de 128 MB, com um cache nomeado padrão
-	Padrão - Cache em tamanhos de 1 GB a 10 GB em incrementos de 1 GB, com suporte para notificações e até dez caches nomeados
-	Premium - Cache em tamanhos de 5 GB a 150 GB em incrementos de 5 GB, com suporte para notificações, alta disponibilidade e até dez caches nomeados

Escolha o **Sku** e a **Memória** que atenda às necessidades do seu aplicativo. Observe que alguns recursos de cache, como notificações e alta disponibilidade, só estão disponíveis com determinadas ofertas de cache. Para obter mais informações sobre como escolher a oferta de cache e o tamanho mais adequado para seu aplicativo, consulte [Ofertas de cache][].

 No seguinte exemplo, um cache Básico de 128MB é criado com o nome contosocache, na região geográfica centro-sul dos EUA.

	New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB

>Para uma lista completa de parâmetros e valores que podem ser utilizados ao criar um cache, consulte a documentação do cmdlet [New-AzureManagedCache][].

Assim que o cmdlet do PowerShell for invocado, pode demorar alguns minutos para que o cache seja criado. Após o cache ser criado, seu novo cache tem um status `Running` e está pronto para o uso com as configurações padrão e pode ser visualizado e configurado no [Portal de gerenciamento do Azure][].

É possível monitorar o progresso da criação na janela do PowerShell do Azure. Assim que o cache estiver pronto para o uso, o cmdlet [New-AzureManagedCache][] mostrará a informação de cache, conforme mostrado no seguinte exemplo.

	PS C:\> Add-AzureAccount
	VERBOSE: Account "user@domain.com" has been added.
	VERBOSE: Subscription "MySubscription" is selected as the default subscription.
	VERBOSE: To view all the subscriptions, please use Get-AzureSubscription.
	VERBOSE: To switch to a different subscription, please use Select-AzureSubscription.
	PS C:\> New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB
	VERBOSE: Intializing parameters...
	VERBOSE: Creating prerequisites...
	VERBOSE: Verify cache service name...
	VERBOSE: Creating cache service...
	VERBOSE: Waiting for cache service to be in ready state...


	Name     : contosocache
	Location : South Central US
	State    : Active
	Sku      : Basic
	Memory   : 128MB



	PS C:\>





As seções seguintes utilizarão as configurações da guia **Painel** para configurar o Cache para um projeto ASP.NET.

<h2><a id="configureproject"></a>Configurar o projeto ASP.NET</h2>
1. Primeiro, verifique se você [instalou o mais recente][]  **SDK do Azure para .NET**.

2. No Visual Studio, clique com o botão direito do mouse no projeto ASP.NET em **Gerenciador de Soluções** e, em seguida, selecione **Gerenciar Pacotes NuGet**. (Se você estiver utilizando o WebMatrix, clique no botão **NuGet** da barra de ferramentas)

3. Digite **WindowsAzure.Caching** na caixa de edição **Pesquisar online**.

	![NuGetDialog][NuGetDialog]

4. Selecione o pacote **Azure Caching** e clique no botão **Instalar**.

<h2><a id="configurewebconfig"></a>Modificar o arquivo .web.config</h2>
Além de fazer referências de assembly para o Cache, o pacote NuGet adiciona entradas stub no arquivo Web.config. Para utilizar o Cache no armazenamento em cache de saída da página do ASP.NET, várias modificações devem ser feitas em Web.config.

1. Abra o arquivo **Web.config** do projeto ASP.NET.

2. Se você tiver elementos de **caching** e **outputCache** existentes, comente-os (ou remova-os).

3. Em seguida, remova o comentário do elemento **caching** que foi adicionado pelo pacote NuGet do Armazenamento em Cache do Azure. O resultado final deve ser semelhante à seguinte captura de tela.

	![OutputConfig][OutputConfig]

4. Em seguida, localize a seção **dataCacheClients**. Remova o comentário do elemento filho **securityProperties**.

	![CacheConfig][CacheConfig]

5. No elemento **autoDiscover**, defina o atributo **identifier** como a URL do ponto de extremidade do cache. Para localizar a URL do ponto de extremidade, vá até as propriedades do cache no Portal de Gerenciamento do Azure. Na guia **Painel**, copie o valor da **URL DO PONTO DE EXTREMIDADE** na seção **Visão rápida**.

	![EndpointURL][EndpointURL]

6. No elemento **messageSecurity**, defina o atributo **authorizationInfo** como a chave de acesso do cache. Para localizar a chave de acesso, selecione o cache no Portal de Gerenciamento Azure. Em seguida, clique no ícone **Gerenciar Chaves** na barra inferior. Clique no botão copiar próximo à caixa de texto **CHAVE DE ACESSO PRIMÁRIA**.

	![ManageKeys][ManageKeys]

<h2><a id="useoutputcaching"></a>Usar armazenamento em cache de saída</h2>
A etapa final é configurar páginas no aplicativo de formulários da Web do ASP.NET para utilizar o armazenamento em cache da saída. Isso pode ser feito adicionando-se um atributo **OutputCache** ao início do código-fonte .ASPX. Por exemplo:

	<%@ OutputCache Duration="45" VaryByParam="*" %>

O exemplo anterior armazena em cache a página durante quarenta e cinco segundos. Como **VaryByParam** é definido como "*", a saída dessa página armazenada em cache não mudará, mesmo que parâmetros de consulta diferentes sejam passados. Mas o exemplo a seguir armazena em cache uma versão diferente da página para cada valor do parâmetro "UserId":

	<%@ OutputCache Duration="45" VaryByParam="UserId" %>	

  
  
  
[installed the latest]: http://www.windowsazure.com/pt-br/downloads/?sdk=net
[NewIcon]: ./media/web-sites-web-forms-output-caching/CacheScreenshot_NewButton.PNG
[NewCacheDialog]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CreateOptions.PNG
[CacheIcon]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheIcon.PNG
[NuGetDialog]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_NuGet.PNG
[OutputConfig]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_OC_WebConfig.PNG
[CacheConfig]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheConfig.PNG
[EndpointURL]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_EndpointURL.PNG
[ManageKeys]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_ManageAccessKeys.PNG
  
[New-AzureManagedCache]: http://go.microsoft.com/fwlink/?LinkId=400495
[Cmdlets de Cache gerenciado do Azure]: http://go.microsoft.com/fwlink/?LinkID=398555
[Como instalar e configurar o PowerShell do Azure]: http://go.microsoft.com/fwlink/?LinkId=400494
[Add-AzureAccount]: http://msdn.microsoft.com/pt-br/library/dn495128.aspx
[Select-AzureSubscription]: http://msdn.microsoft.com/pt-br/library/dn495203.aspx
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  
  
  
