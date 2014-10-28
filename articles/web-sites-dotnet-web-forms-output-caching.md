<properties linkid="video-center-detail" urlDisplayName="details" pageTitle="Video Center Details" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use ASP.NET Web Forms Output Caching with Azure Websites" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Como usar armazenamento em cache de saída de Formulários da Web do ASP.NET com Sites do Azure

Este tópico explica como usar o Serviço de Cache do Azure (Visualização) para dar suporte ao armazenamento em cache de saída da página do ASP.NET para Formulários da Web do ASP.NET. Armazenamento em cache de saída da página é uma otimização que retorna diretamente uma página de renderização durante um período específico. Isso é útil em situações nas quais uma página é acessada com mais frequência do que normalmente muda. É importante observar que o armazenamento em cache de saída da página não é compatível com aplicativos MVC do ASP.NET.

O Serviço de Cache (Visualização) fornece um serviço distribuído de cache que é externo ao site. Isso permite que todas as instâncias do site acessem a mesma renderização armazenada em cache de uma página.

Entre as etapas básicas para usar o Serviço de Cache (Visualização) no armazenamento em cache de saída da página estão:

-   [Criar o cache.][Criar o cache.]
-   [Configure o projeto ASP.NET para usar o Cache do Azure.][Configure o projeto ASP.NET para usar o Cache do Azure.]
-   [Modificar o arquivo web.config][Modificar o arquivo web.config]
-   [Use o armazenamento em cache de saída para retornar temporariamente versões armazenadas em cache de uma página.][Use o armazenamento em cache de saída para retornar temporariamente versões armazenadas em cache de uma página.]

## <span id="createcache"></span></a>Criar o cache

As instâncias em cache no Serviço de Cache Gerenciado são criadas usando cmdlets do PowerShell.

> Assim que uma instância de Serviço de Cache Gerenciada for criada usando os cmdlets do PowerShell, pode ser visualizada e configurada no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].

Para criar uma instância de Serviço de Cache Gerenciado, abra uma janela de comando do PowerShell.

> Para obter instruções sobre como instalar e usar o Azure PowerShell, consulte [Como instalar e configurar o Azure Powershell][Como instalar e configurar o Azure Powershell].

Invocar o cmdlet [Add-AzureAccount][Add-AzureAccount], e inserir o endereço do email e a senha associada com a sua conta. Uma assinatura é escolhida por padrão e é exibida após ter invocado o cmdlet [Add-AzureAccount][Add-AzureAccount]. Para alterar a assinatura, invocar o cmdlet [Select-AzureSubscription][Select-AzureSubscription].

> Se você tiver configurado o Azure PowerShell com um certificado para sua conta, então você pode pular esta etapa. Para obter mais informações sobre conectar o Azure PowerShell com sua conta do Azure, consultar [Como instalar e configurar o Azure PowerShell][Como instalar e configurar o Azure Powershell].

Uma assinatura é escolhida por padrão e será exibida. Para alterar a assinatura, invocar o cmdlet [Select-AzureSubscription][Select-AzureSubscription].

Invocar o cmdlet [New-AzureManagedCache][New-AzureManagedCache] e especificar o nome, região, oferta de cache, e tamanho para o cache.

Para **Nome**, insira um nome de subdomínio a ser usado para o ponto de extremidade do cache. O ponto de extremidade deve ser uma cadeia de seis a vinte caracteres, conter apenas números e letras minúsculas e deve começar com uma letra.

Para **Local**, especifique uma região para o cache. Para obter melhor desempenho, crie o cache na mesma região que o aplicativo de cliente de cache.

**Sku** e **Memória** funcionam em conjunto para determinar o tamanho do cache. O Serviço de Cache Gerenciado está disponível nas três camadas a seguir:

-   Básica - Cache em tamanhos de 128 MB a 1 GB em incrementos de 128 MB, com um cache nomeado padrão
-   Padrão - Cache em tamanhos de 1 GB a 10 GB em incrementos de 1 GB, com suporte para notificações e até dez caches nomeados
-   Premium - Cache em tamanhos de 5 GB a 150 GB em incrementos de 5 GB, com suporte para notificações, alta disponibilidade e até dez caches nomeados

Escolha a **Sku** e a **Memória** que atenda às necessidades de seu aplicativo. Observe que alguns recursos de cache, como notificações e alta disponibilidade, só estão disponíveis com determinadas ofertas de cache. Para obter mais informações sobre como escolher a oferta de cache e o tamanho mais adequado para seu aplicativo, consulte [Ofertas de cache][Ofertas de cache].

No seguinte exemplo, um cache Básico de 128MB é criado com o nome contosocache, na região geográfica centro-sul dos EUA.

    New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB

> Para uma lista completa de parâmetros e valores que podem ser usados ao criar um cache, consulte a documentação do cmdlet [New-AzureManagedCache][New-AzureManagedCache].

Assim que o cmdlet do PowerShell for invocado, ele pode demorar alguns minutos para que o cache seja criado. Após que o cache for criado, seu novo cache tem um status `Running` e está pronto para o uso com as configurações padrão, e pode ser visualizado e configurado no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].

Você pode monitorar o progresso da criação na janela do Azure PowerShell. Assim que o cache estiver pronto para o uso, o cmdlet [New-AzureManagedCache][New-AzureManagedCache] mostrará a informação de cache, conforme mostrado no seguinte exemplo.

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

As seções seguintes utilizarão as configurações da guia **Painel** para configurar o Caching para um projeto ASP.NET.

## <span id="configureproject"></span></a>Configurar o projeto ASP.NET

1.  Primeiro, verifique se você [instalou o mais recente][instalou o mais recente] **SDK do Azure SDK para .NET**.

2.  No Visual Studio, clique com o botão direito do mouse no projeto ASP.NET em **Gerenciador de Soluções** e, em seguida, selecione **Gerenciar Pacotes NuGet**. (Se você estiver usando o WebMatrix, clique no botão **NuGet** da barra de ferramentas)

3.  Digite **WindowsAzure.Caching** na caixa de edição **Pesquisar Online**.

    ![NuGetDialog][NuGetDialog]

4.  Selecione o pacote **Azure Caching** e clique no botão **Instalar**.

## <span id="configurewebconfig"></span></a>Modificar o arquivo .web.config

Além de fazer referências de assembly para o Cache, o pacote NuGet adiciona entradas stub no arquivo web.config. Para usar o Cache no armazenamento em cache de saída da página do ASP.NET, várias modificações devem ser feitas em web.config.

1.  Abra o arquivo **web.config** do projeto ASP.NET.

2.  Se você tiver elementos **caching** e **outputCache** existentes, comente-os (ou remova-os).

3.  Em seguida, remova o comentário do elemento **caching** que foi adicionado pelo pacote NuGet do Armazenamento em Cache do Azure. O resultado final deve ser semelhante à seguinte captura de tela.

    ![OutputConfig][OutputConfig]

4.  Em seguida, localize a seção **dataCacheClients**. Remova o comentário do elemento filho **securityProperties**.

    ![CacheConfig][CacheConfig]

5.  No elemento **autoDiscover**, defina o atributo **identifier** como a URL do ponto de extremidade do cache. Para localizar a URL do ponto de extremidade, vá até as propriedades do cache no Portal de Gerenciamento do Azure. Na guia **Painel**, copie o valor da **URL DO PONTO DE EXTREMIDADE** na seção **Visão Rápida**.

    ![EndpointURL][EndpointURL]

6.  No elemento **messageSecurity**, defina o atributo **authorizationInfo** como a chave de acesso do cache. Para localizar a chave de acesso, selecione o cache no Portal de Gerenciamento Azure. Em seguida, clique no ícone **Gerenciar Chaves** na barra inferior. Clique no botão copiar próximo à caixa de texto **CHAVE DE ACESSO PRIMÁRIA**.

    ![ManageKeys][ManageKeys]

## <span id="useoutputcaching"></span></a>Usar armazenamento em cache de saída

A etapa final é configurar páginas no aplicativo de formulários da Web do ASP.NET para usar o armazenamento em cache da saída. Isso pode ser feito adicionando-se um atributo **OutputCache** ao início do código-fonte .ASPX. Por exemplo:

    <%@ OutputCache Duration="45" VaryByParam="*" %>

O exemplo anterior armazena em cache a página durante quarenta e cinco segundos. Como **VaryByParam** é definido como "\*", a saída dessa página armazenada em cache não mudará, mesmo que parâmetros de consulta diferentes sejam passados. Mas o exemplo a seguir armazena em cache uma versão diferente da página para cada valor do parâmetro "UserId":

    <%@ OutputCache Duration="45" VaryByParam="UserId" %>   

  [Criar o cache.]: #createcache
  [Configure o projeto ASP.NET para usar o Cache do Azure.]: #configureproject
  [Modificar o arquivo web.config]: #configurewebconfig
  [Use o armazenamento em cache de saída para retornar temporariamente versões armazenadas em cache de uma página.]: #useoutputcaching
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [Como instalar e configurar o Azure Powershell]: http://go.microsoft.com/fwlink/?LinkId=400494
  [Add-AzureAccount]: http://msdn.microsoft.com/pt-br/library/dn495128.aspx
  [Select-AzureSubscription]: http://msdn.microsoft.com/pt-br/library/dn495203.aspx
  [New-AzureManagedCache]: http://go.microsoft.com/fwlink/?LinkId=400495
  [instalou o mais recente]: http://www.windowsazure.com/pt-br/downloads/?sdk=net
  [NuGetDialog]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_NuGet.PNG
  [OutputConfig]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_OC_WebConfig.PNG
  [CacheConfig]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheConfig.PNG
  [EndpointURL]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_EndpointURL.PNG
  [ManageKeys]: ./media/web-sites-web-forms-output-caching/CachingScreenshot_ManageAccessKeys.PNG
