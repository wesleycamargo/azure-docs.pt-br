---
title: "Com usar o Serviço de Cache gerenciado do Azure"
description: "Saiba como melhorar o desempenho de seus aplicativos do Azure com o Serviço de Cache Gerenciado do Azure"
services: cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 80552353-6682-4b96-a4cc-4e975a25980c
ms.service: cache
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/15/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6feeeaef2bd5e5ff665ca870b61036365d75f53d


---
# <a name="how-to-use-azure-managed-cache-service"></a>Com usar o Serviço de Cache gerenciado do Azure
Este guia mostra como começar a usar o **Serviço de Cache Gerenciado do Azure**. Os exemplos são escritos em código C\# e utilizam a API .NET. Os cenários abordados incluem **criação e configuração de um cache**, **configuração de clientes de cache**, **adição e remoção de objetos do cache, armazenando o estado da sessão ASP.NET no cache** e **habilitação do cache de saída da página ASP.NET usando o cache**. Para saber mais sobre como usar o Cache do Azure, consulte a seção [Next Steps][Next Steps].

> [!IMPORTANT]
> De acordo com o [comunicado](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)do ano passado, o Serviço de Cache Gerenciado do Azure e o serviço Cache na Função do Azure serão desativados em 30 de novembro de 2016. A nossa recomendação é usar o [Cache Redis do Azure](https://azure.microsoft.com/services/cache/). Para saber mais sobre a migração, confira [Migrar do Serviço de Cache gerenciado para o Cache Redis do Azure](../redis-cache/cache-migrate-to-redis.md).
> 
> 

<a name="what-is"></a>

## <a name="what-is-azure-managed-cache-service"></a>O que é o Serviço de Cache gerenciado do Azure?
O Serviço de Cache Gerenciado do Azure é uma solução distribuída, na memória e dimensionável que permite que você crie aplicativos altamente dimensionáveis e dinâmicos fornecendo acesso super-rápido aos dados.

O Serviço de Cache Gerenciado do Azure inclui os seguintes recursos:

* Provedores ASP.NET pré-criados para cache de estado de seção e de saída de página, que habilitam a aceleração de aplicativos Web sem necessidade de modificar o código do aplicativo.
* Armazena qualquer objeto serializável gerenciado em cache, por exemplo: objetos CLR, linhas, XML, dados binários.
* Modelo de desenvolvimento consistente no Azure e no Windows Server AppFabric.

O Serviço de Cache Gerenciado oferece acesso a um cache seguro e dedicado, que é gerenciado pela Microsoft. Um cache criado usando o Serviço de Cache Gerenciado pode ser acessado em aplicativos dentro do Azure que executam Sites do Azure, Funções Web e de Trabalho e Máquinas Virtuais.

O Serviço de Cache Gerenciado está disponível em três camadas:

* Básica – Cache em tamanhos de 128 MB a 1 GB
* Padrão – Cache em tamanhos de 1 GB a 10 GB
* Premium – Cache em tamanhos de 5 GB a 150 GB

Cada camada é diferente em termos de recursos e preços. Os recursos são abordados posteriormente neste manual e, para obter mais informações sobre preços, consulte [Detalhes de preços do Cache][Detalhes de preços do Cache].

Este guia fornece uma visão geral da introdução ao Serviço de Cache Gerenciado. Para obter informações mais detalhadas sobre esses recursos que estão além do escopo deste guia de introdução, consulte [Visão geral do Serviço de Cache do Azure Gerenciado][Visão geral do Serviço de Cache do Azure Gerenciado].

<a name="getting-started-cache-service"></a>

## <a name="getting-started-with-cache-service"></a>Introdução ao Serviço de Cache
Introdução ao Serviço de Cache Gerenciado é fácil. Para começar, você provisiona e configura um cache. Em seguida, você configura os clientes de cache para que possam acessar o cache. Quando os clientes de cache estiverem configurados, você pode começar a trabalhar com eles.

* [Criar o cache][Criar o cache]
* [Configurar o cache][Configurar o cache]
* [Configurar os clientes de cache][Configurar os clientes de cache]

<a name="create-cache"></a>

## <a name="create-a-cache"></a>Criar um cache
As instâncias de cache no Serviço de Cache Gerenciado são criadas usando os cmdlets do PowerShell. 

> Assim que uma instância de Serviço de Cache Gerenciado for criada usando os cmdlets do PowerShell, pode ser visualizada e configurada no [Portal Clássico do Azure][Portal Clássico do Azure].
> 
> 

Para criar uma instância de Serviço de Cache Gerenciado, abra uma janela de comando do PowerShell.

> Para obter instruções sobre como instalar e usar o Azure PowerShell, veja [Como instalar e configurar o Azure PowerShell][Como instalar e configurar o Azure PowerShell].
> 
> 

Invoque o cmdlet [Add-AzureAccount][Add-AzureAccount] e insira o endereço do email e a senha associada à sua conta. Uma assinatura é escolhida por padrão e é exibida após ter invocado o cmdlet [Add-AzureAccount][Add-AzureAccount]. Para alterar a assinatura, invoque o cmdlet [Select-AzureSubscription][Select-AzureSubscription].

> Se você tiver configurado o PowerShell do Azure com um certificado para sua conta, então você pode pular esta etapa. Para saber mais sobre como conectar o Azure PowerShell à sua conta do Azure, veja [Como instalar e configurar o Azure PowerShell][Como instalar e configurar o Azure PowerShell].
> 
> 

Por padrão, uma assinatura é escolhida e exibida. Para alterar a assinatura, invoque o cmdlet [Select-AzureSubscription][Select-AzureSubscription].

Invoque o cmdlet [New-AzureManagedCache][New-AzureManagedCache] e especifique o nome, região, a oferta de cache e o tamanho para o cache.

Para **Nome**, insira um nome de subdomínio a ser usado para o ponto de extremidade do cache. O ponto de extremidade deve ser uma cadeia de seis a vinte caracteres, conter apenas números e letras minúsculas e deve começar com uma letra.

Para **Local**, especifique uma região para o cache. Para obter melhor desempenho, crie o cache na mesma região que o aplicativo de cliente de cache.

**Sku** e **Memória** funcionam em conjunto para determinar o tamanho do cache. O Serviço de Cache Gerenciado está disponível nas três camadas a seguir:

* Básica - Cache em tamanhos de 128 MB a 1 GB em incrementos de 128 MB, com um cache nomeado padrão
* Padrão - Cache em tamanhos de 1 GB a 10 GB em incrementos de 1 GB, com suporte para notificações e até dez caches nomeados
* Premium - Cache em tamanhos de 5 GB a 150 GB em incrementos de 5 GB, com suporte para notificações, alta disponibilidade e até dez caches nomeados

Escolha a **Sku** e a **Memória** que atenda às necessidades de seu aplicativo. Observe que alguns recursos de cache, como notificações e alta disponibilidade, só estão disponíveis com determinadas ofertas de cache. Para saber mais sobre como escolher a oferta de cache e o tamanho mais adequado para seu aplicativo, consulte [Ofertas de cache][Ofertas de cache].

 No seguinte exemplo, um cache Básico de 128MB é criado com o nome contosocache, na região geográfica centro-sul dos EUA.

    New-AzureManagedCache -Name contosocache -Location "South Central US" -Sku Basic -Memory 128MB

> Para uma lista completa de parâmetros e valores que podem ser usados ao criar um cache, consulte a documentação do cmdlet [New-AzureManagedCache][New-AzureManagedCache].
> 
> 

Assim que o cmdlet do PowerShell for invocado, ele pode demorar alguns minutos para que o cache seja criado. Após a criação do cache, seu novo cache terá um status de `Running` e estará pronto para o uso com as configurações padrão e poderá ser exibido e configurado no [Portal Clássico do Azure][Portal Clássico do Azure]. Para personalizar a configuração do cache, veja a seção [Configurar o cache][Configurar o cache] a seguir.

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




<a name="enable-caching"></a>

## <a name="configure-the-cache"></a>Configurar o cache
A guia **Configurar** para Cache no Portal Clássico do Azure é o local em que você configura as opções do seu cache. Cada cache tem um cache nomeado **padrão** , e as ofertas de cache Padrão e Premium suportam até nove caches nomeados adicionais, em um total de dez. Cada cache nomeado tem seu próprio conjunto de opções que permitem configurar seu cache de maneira altamente flexível.

![NamedCaches][NamedCaches]

Para criar um cache nomeado, digite o nome do novo cache na caixa **Nome**, especifique as opções desejadas, clique em **Salvar** e clique em **Sim** para confirmar. Para cancelar todas as alterações, clique em **Descartar**.

## <a name="expiry-policy-and-time-min"></a>Política e tempo de expiração (min)
A **Política de Expiração** funciona em conjunto com a configuração de **Tempo (min)** para determinar quando expiram os itens armazenados em cache. Há três tipos de política de expiração: **Absoluto**, **Deslizante** e **Nunca**. 

Quando **Absoluto** for especificada, o intervalo de expiração especificado por **Tempo (min)** começa quando um item é adicionado ao cache. Quando decorre o intervalo especificado por **Tempo (min)** , o item expira. 

Quando **Deslizante** for especificado, o intervalo de expiração especificado por **Tempo (min)** é redefinido a cada vez que um item é acessado no cache. O item não expira até que o intervalo especificado por **Tempo (min)** tenha decorrido após o último acesso ao item.

Quando **Nunca** for especificado, **Tempo (min)** deve ser definido como **0**, e os itens não expiram.

**Absoluto** é a política de expiração padrão, e 10 minutos é a configuração padrão para **Tempo (min)**. A política de expiração é fixa para cada item em um cache nomeado, mas o **Tempo (min)** pode ser personalizado para cada item usando sobrecargas de **Add** e **Put** que utilizam um parâmetro de tempo limite.

Para saber mais sobre políticas de remoção e expiração, consulte [Expiração e remoção][Expiração e remoção].

## <a name="notifications"></a>Notificações
Notificações de cache que permitem que seus aplicativos recebam notificações assíncronas quando várias operações de cache ocorrem no cluster de cache. A notificações de cache também fornecem invalidação automática de objetos armazenados em cache localmente. Para saber mais, veja [Notificações][Notificações].

> As Notificações só estão disponíveis nas ofertas de cache Padrão e Premium e não estão disponíveis na oferta de cache básico. Para saber mais, veja [Ofertas de cache][Ofertas de cache].
> 
> 

## <a name="high-availability"></a>Alta disponibilidade
Quando a alta disponibilidade está habilitada, é feita uma cópia de backup de cada item adicionado ao cache. Se ocorrer uma falha inesperada na cópia principal do item, a cópia de backup ainda estará disponível.

Por definição, o uso de alta disponibilidade multiplica por dois a quantidade de memória necessária para cada item armazenado em cache. Considere esse impacto de memória durante as tarefas de planejamento da capacidade. Para saber mais, consulte [Alta disponibilidade][Alta disponibilidade].

> A Alta disponibilidade só está disponível na oferta de cache Premium e não está disponível nas ofertas de cache Básica e Padrão. Para saber mais, veja [Ofertas de cache][Ofertas de cache].
> 
> 

## <a name="eviction"></a>Remoção
Para manter a capacidade de memória disponível em um cache, há suporte para a remoção de LRU (menos utilizado recentemente). Quando o consumo de memória excede o limite de memória, os objetos são removidos da memória, independentemente de terem expirado ou não, até que a pressão de memória seja aliviada.
Por padrão, a remoção está habilitada. Se a remoção estiver desabilitada, os itens não serão removidos do cache quando a capacidade for atingida e, em vez disso, haverá falha nas operações Put e Add.

Para saber mais sobre políticas de remoção e expiração, consulte [Expiração e remoção][Expiração e remoção].

Depois que o cache estiver configurado, você poderá configurar os clientes de cache para permitir o acesso ao cache.

<a name="NuGet"></a>

## <a name="configure-the-cache-clients"></a>Configurar os clientes de cache
Um cache criado usando o Serviço de Cache Gerenciado pode ser acessado em aplicativos do Azure que executam Sites do Azure, Funções Web e de Trabalho e Máquinas Virtuais. É fornecido um pacote NuGet que simplifica a configuração de aplicativos de cliente do cache. 

Para configurar um aplicativo cliente usando o pacote NuGet do Cache, clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e escolha **Gerenciar Pacotes NuGet**. 

![NuGetPackageMenu][NuGetPackageMenu]

Digite **WindowsAzure.Caching** na caixa de texto **Pesquisar Online** e selecione **Windows**  
**Cache do** **Azure** dos resultados. Clique em **Instalar** e clique em **Aceito**.

![NuGetPackage][NuGetPackage]

O pacote NuGet faz várias coisas: adiciona a configuração necessária ao arquivo de configuração do aplicativo e adiciona as referências necessárias ao assembly. Para projetos de Serviços de Nuvem, ele também adiciona uma configuração de nível de diagnóstico do cliente de cache ao arquivo Introdução ao Serviço de Cache do Serviço de Nuvem.

> Para projetos web ASP.NET, o pacote NuGet do Cache também adiciona duas seções comentadas ao web.config. A primeira seção permite que o estado da sessão seja armazenado no cache, e a segunda seção habilita a saída do cache da página ASP.NET. Para saber mais, consulte [Como armazenar o estado da sessão ASP.NET no cache] e [Como armazenar o cache de saída de página ASP.NET no cache][Como armazenar o cache de saída de página ASP.NET no cache].
> 
> 

O pacote NuGet adiciona os seguintes elementos de configuração ao web.config ou ao app.config de seu aplicativo. Uma seção **dataCacheClients** e uma seção **cacheDiagnostics** são adicionadas sob o elemento **configSections**. Se não houver nenhum elemento **configSections** presente, será criado um como um filho do elemento **configuration**.

    <configSections>
      <!-- Existing sections omitted for clarity. -->
      <section name="dataCacheClients" 
        type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection,
              Microsoft.ApplicationServer.Caching.Core" 
        allowLocation="true" 
        allowDefinition="Everywhere" />

    <section name="cacheDiagnostics" 
        type="Microsoft.ApplicationServer.Caching.AzureCommon.DiagnosticsConfigurationSection,
              Microsoft.ApplicationServer.Caching.AzureCommon" 
        allowLocation="true" 
        allowDefinition="Everywhere" />


Essas novas seções incluem referências a um elemento **dataCacheClients**, que também é adicionado ao elemento **configuration**.

    <dataCacheClients>
      <dataCacheClient name="default">
        <!--To use the in-role flavor of Azure Caching, 
            set identifier to be the cache cluster role name -->
        <!--To use the Azure Caching Service,
            set identifier to be the endpoint of the cache cluster -->
        <autoDiscover isEnabled="true" identifier="[Cache role name or Service Endpoint]" />
        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
        <!--Use this section to specify security settings for connecting to your cache. 
            This section is not required if your cache is hosted on a role that is a part
            of your cloud service. -->
        <!--<securityProperties mode="Message" sslEnabled="false">
          <messageSecurity authorizationInfo="[Authentication Key]" />
        </securityProperties>-->
      </dataCacheClient>
    </dataCacheClients>


Após a configuração ser adicionada, substitua os seguintes dois itens na configuração recém-adicionada.

1. Substitua **Portal Clássico do AzurePortal Clássico do Azure[Nome da função do cache ou ponto de extremidade de serviço]** Portal Clássico do AzurePortal Clássico do Azure pelo ponto de extremidade que é exibido no Painel no Portal Clássico do Azure.
   
    ![Ponto de extremidade][Endpoint]
2. Remova os comentários da seção securityProperties e substitua **[Authentication Key]** pela chave de autenticação, que pode ser encontrada no Portal Clássico do Azure clicando em **Gerenciar Chaves** no painel de cache.
   
    ![AccessKeys][AccessKeys]

> Essas configurações devem ser definidas corretamente ou os clientes não poderão acessar o cache.
> 
> 

Para projetos de Serviços de Nuvem, o pacote NuGet também adiciona uma configuração de **ClientDiagnosticLevel** ao **ConfigurationSettings** da função do cliente do cache em ServiceConfiguration.cscfg. O exemplo a seguir é a seção **WebRole1** de um arquivo ServiceConfiguration.cscfg com um **ClientDiagnosticLevel** igual a 1, que é o **ClientDiagnosticLevel**.

    <Role name="WebRole1">
      <Instances count="1" />
      <ConfigurationSettings>
        <!-- Existing settings omitted for clarity. -->
        <Setting name="Microsoft.WindowsAzure.Plugins.Caching.ClientDiagnosticLevel" 
                 value="1" />
      </ConfigurationSettings>
    </Role>

> O nível de diagnóstico do cliente configura o nível das informações de diagnóstico do cache coletadas para clientes de cache. Para saber mais, veja [Solução de problemas e diagnóstico][Solução de problemas e diagnóstico]
> 
> 

O pacote NuGet também adiciona referências aos seguintes assemblies:

* Microsoft.ApplicationServer.Caching.Client.dll
* Microsoft.ApplicationServer.Caching.Core.dll
* Microsoft.WindowsFabric.Common.dll
* Microsoft.WindowsFabric.Data.Common.dll
* Microsoft.ApplicationServer.Caching.AzureCommon.dll
* Microsoft.ApplicationServer.Caching.AzureClientHelper.dll

Se seu projeto for um projeto web, a referência ao assembly a seguir também será adicionada:

* Microsoft.Web.DistributedCache.dll.

Depois que o projeto de cliente estiver configurado para caching, você poderá usar as técnicas descritas nas seções a seguir para trabalhar com o cache.

<a name="working-with-caches"></a>

## <a name="working-with-caches"></a>Trabalhando com caches
As etapas desta seção descrevem como realizar tarefas comuns com o Cache.

* [Como criar um objeto DataCache][Como criar um objeto DataCache]
* [Como adicionar e recuperar um objeto do cache][Como adicionar e recuperar um objeto do cache]
* [Como especificar a expiração de um objeto no cache][Como especificar a expiração de um objeto no cache]
* [Como armazenar o estado da sessão ASP.NET no cache][Como armazenar o estado da sessão ASP.NET no cache]
* [Como armazenar em cache o cache de saída de uma página ASP.NET][Como armazenar o cache de saída de página ASP.NET no cache]

<a name="create-cache-object"></a>

## <a name="how-to-create-a-datacache-object"></a>Como criar um objeto DataCache
Para trabalhar de forma programática com um cache, você precisa de uma referência ao cache. Adicione o seguinte à parte superior de qualquer arquivo no qual você deseja usar o Cache do Azure:

    using Microsoft.ApplicationServer.Caching;

> Se o Visual Studio não reconhecer os tipos na instrução using mesmo depois da instalação do pacote NuGet de Cache, que adiciona as referências necessárias, verifique se o perfil de destino do projeto é o .NET Framework 4 ou superior e selecione um dos perfis que não especificam o **Perfil de Cliente**. Para obter instruções sobre como configurar clientes de cache, consulte [Configurar os clientes de cache][Configurar os clientes de cache].
> 
> 

Existem duas maneiras de criar um objeto **DataCache** . A primeira maneira é simplesmente criar um **DataCache**, passando o nome do cache desejado.

    DataCache cache = new DataCache("default");

Quando o **DataCache** for instanciado, você poderá usá-lo para interagir com o cache, conforme descrito nas seções a seguir.

Para usar a segunda maneira, crie um novo objeto **DataCacheFactory** em seu aplicativo usando o construtor padrão. Isso faz com que o cliente de cache use os parâmetros do arquivo de configuração. Chame o método **GetDefaultCache** da nova instância **DataCacheFactory** que retorna um objeto **DataCache** ou o método **GetCache** e passe o nome de seu cache. Esses métodos retornam um objeto **DataCache** que pode ser usado para acessar programaticamente o cache.

    // Cache client configured by settings in application configuration file.
    DataCacheFactory cacheFactory = new DataCacheFactory();
    DataCache cache = cacheFactory.GetDefaultCache();
    // Or DataCache cache = cacheFactory.GetCache("MyCache");
    // cache can now be used to add and retrieve items.    

<a name="add-object"></a>

## <a name="how-to-add-and-retrieve-an-object-from-the-cache"></a>Como adicionar e recuperar um objeto do cache
Para adicionar um item ao cache, o método **Add** ou o método **Put** pode ser usado. O método **Add** adiciona o objeto especificado ao cache, inserido pelo valor do parâmetro chave.

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value");

Se um objeto com a mesma chave já estiver no cache, uma **DataCacheException** será lançada com a seguinte mensagem:

> ErrorCode:SubStatus: está sendo feita uma tentativa de criar um objeto comum à chave que já existe no cache. O cache aceita somente valores de chave exclusivos para objetos.
> 
> 

Para recuperar um objeto com uma chave específica, o método **Get** pode ser usado. Se o objeto existir, ele é retornado. Se isso não acontecer, nulo será retornado.

    // Add the string "value" to the cache, keyed by "key"
    object result = cache.Get("Item");
    if (result == null)
    {
        // "Item" not in cache. Obtain it from specified data source
        // and add it.
        string value = GetItemValue(...);
        cache.Add("item", value);
    }
    else
    {
        // "Item" is in cache, cast result to correct type.
    }

O método **Put** adiciona o objeto com a chave especificada ao cache se ele não existir ou substitui o objeto se ele existir.

    // Add the string "value" to the cache, keyed by "item". If it exists,
    // replace it.
    cache.Put("item", "value");

<a name="specify-expiration"></a>

## <a name="how-to-specify-the-expiration-of-an-object-in-the-cache"></a>Como especificar a expiração de um objeto no cache
Por padrão, os itens no cache expiram 10 minutos depois que são colocados no cache. Isso pode ser configurado no parâmetro **Tempo (min)** na guia Configurar do Cache no Portal Clássico do Azure.

![NamedCaches][NamedCaches]

Existem três tipos de **Política de Expiração**: **Nunca**, **Absoluto** e **Deslizante**. Esses tipos configuram como a opção **Tempo (min)** é usada para determinar a expiração. O **Tipo de Expiração** padrão é **Absoluto**, o que significa que o temporizador da contagem regressiva de expiração de um item começa quando o item é colocado no cache. Depois que o período de tempo especificado tiver decorrido para um item, o item expira. Se **Deslizante** for especificado, a contagem regressiva da expiração de um item será redefinida cada vez que o item for acessado no cache, e o item não irá expirar até que o período de tempo especificado tiver decorrido desde o seu último acesso. Se **Nunca** for especificado, a opção **Tempo (min)** deverá ser definida como **0**, e os itens não expirarão e permanecerão válidos enquanto estiverem no cache.

Se um intervalo de tempo limite maior ou menor do que o que está configurado nas propriedades do cache for desejado, uma duração específica poderá ser especificada quando um item for adicionado ou atualizado no cache usando a sobrecarga de **Add** e **Put** que usam um parâmetro **TimeSpan**. No exemplo a seguir, a cadeia de caracteres **value** é adicionada ao cache, inserida por **item**, com um tempo limite de 30 minutos.

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value", TimeSpan.FromMinutes(30));

Para exibir o intervalo de tempo limite restante de um item no cache, o método **GetCacheItem** pode ser usado para recuperar um objeto **DataCacheItem** que contém informações sobre o item no cache, incluindo o intervalo de tempo limite restante.

    // Get a DataCacheItem object that contains information about
    // "item" in the cache. If there is no object keyed by "item" null
    // is returned. 
    DataCacheItem item = cache.GetCacheItem("item");
    TimeSpan timeRemaining = item.Timeout;

<a name="store-session"></a>

## <a name="how-to-store-aspnet-session-state-in-the-cache"></a>Como armazenar o estado da sessão ASP.NET no cache
O Provedor de Estado de Sessão para Cache do Azure é um mecanismo de armazenamento fora do processo para aplicativos ASP.NET. Esse provedor permite que você armazene o estado da sessão em um cache do Azure, em vez de na memória ou em um banco de dados do SQL Server. Para usar o provedor de estado de sessão de cache, primeiro configure o cache e, em seguida, configure o aplicativo ASP.NET para Cache usando o pacote NuGet de Cache conforme descrito em [Introdução ao Serviço de Cache][Introdução ao Serviço de Cache]. Quando o pacote NuGet do Cache está instalado, ele adiciona uma seção comentada no web.config que contém a configuração necessária para seu aplicativo ASP.NET usar o Provedor de Estado de Sessão para o Cache do Azure.

    <!--Uncomment this section to use Azure Caching for session state caching
    <system.web>
      <sessionState mode="Custom" customProvider="AFCacheSessionStateProvider">
        <providers>
          <add name="AFCacheSessionStateProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheSessionStateStoreProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default"/>
        </providers>
      </sessionState>
    </system.web>-->

> Se o web.config não contiver essa seção comentada depois da instalação do pacote NuGet do Cache, verifique se o Gerenciador de Pacotes NuGet mais recente está instalado em [Instalação do Gerenciador de Pacotes NuGet][Instalação do Gerenciador de Pacotes NuGet] e desinstale e reinstale o pacote.
> 
> 

Para habilitar o Provedor de Estado de Sessão para o Cache do Azure, remova os comentários da seção especificada. O cache padrão é especificado no trecho de código fornecido. Para usar um outro cache, especifique o cache desejado no atributo **cacheName** .

Para saber mais sobre como usar o provedor de estado de sessão do Serviço de Cache Gerenciado, veja [Provedor de estado de sessão para o Cache do Azure][Provedor de estado de sessão para o Cache do Azure].

<a name="store-page"></a>

## <a name="how-to-store-aspnet-page-output-caching-in-the-cache"></a>Como armazenar o cache de saída de página ASP.NET no cache
O Provedor de Cache de Saída para o Cache do Azure é um mecanismo de armazenamento fora do processo para dados do cache de saída. Esses dados são especificamente para respostas HTTP completas (cache de saída de página). O provedor conecta-se ao novo saída cache provedor ponto de extensibilidade que foi introduzido no ASP.NET 4. Para usar o provedor de cache de saída, primeiro configure o cluster de cache e, em seguida, configure seu aplicativo ASP.NET para cache usando o pacote NuGet de Cache, conforme descrito em [Introdução ao Serviço de Cache][Introdução ao Serviço de Cache]. Quando o pacote NuGet de Caching está instalado, ele adiciona a seguinte seção comentada ao web.config que contém a configuração necessária para seu aplicativo ASP.NET usar o Provedor de Cache de Saída para Caching do Azure.

    <!--Uncomment this section to use Azure Caching for output caching
    <caching>
      <outputCache defaultProvider="AFCacheOutputCacheProvider">
        <providers>
          <add name="AFCacheOutputCacheProvider" 
            type="Microsoft.Web.DistributedCache.DistributedCacheOutputCacheProvider,
                  Microsoft.Web.DistributedCache" 
            cacheName="default" 
            dataCacheClientName="default" />
        </providers>
      </outputCache>
    </caching>-->

> Se o web.config não contiver essa seção comentada depois da instalação do pacote NuGet do Cache, verifique se o Gerenciador de Pacotes NuGet mais recente está instalado em [Instalação do Gerenciador de Pacotes NuGet][Instalação do Gerenciador de Pacotes NuGet] e desinstale e reinstale o pacote.
> 
> 

Para habilitar o Provedor de Cache de Saída para o Cache do Azure, remova o comentário da seção especificada. O cache padrão é especificado no trecho de código fornecido. Para usar um outro cache, especifique o cache desejado no atributo **cacheName** .

Adicione uma diretiva **OutputCache** a cada página da qual você deseja armazenar a saída em cache.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

Neste exemplo, os dados da página em cache permanecerão no cache por 60 segundos, e outra versão da página será armazenada em cache para cada combinação de parâmetros. Para obter mais informações sobre as opções disponíveis, consulte [Diretiva OutputCache][Diretiva OutputCache].

Para saber mais sobre como usar o Provedor de Cache de Saída para Cache do Azure, consulte [Provedor de Cache de Saída para o Cache do Azure][Provedor de Cache de Saída para o Cache do Azure].

<a name="next-steps"></a>

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu o básico do Serviço de Cache gerenciado, siga os links para saber como realizar tarefas de cache mais complexas.

* Consulte a referência do MSDN: [Serviço de Cache Gerenciado][Serviço de Cache Gerenciado]
* Saiba como migrar para o Serviço de Cache Gerenciado: [Migrar para o Serviço de Cache Gerenciado][Migrar para o Serviço de Cache Gerenciado]
* Confira os exemplos: [Exemplos de Serviço de Cache Gerenciado][Exemplos de Serviço de Cache Gerenciado]

<!-- INTRA-TOPIC LINKS -->
[Next Steps]: #next-steps
[O que é o Serviço de Cache gerenciado do Azure?]: #what-is
[Criar um Cache do Azure]: #create-cache
[Qual tipo de cache é mais adequado para mim?]: #choosing-cache
[Preparar seu projeto do Visual Studio para usar Cache do Azure]: #prepare-vs
[Configurar seu aplicativo para usar Caching]: #configure-app
[Introdução ao Serviço de Cache]: #getting-started-cache-service
[Criar o cache]: #create-cache
[Configurar o cache]: #enable-caching
[Configurar os clientes de cache]: #NuGet
[Trabalhando com caches]: #working-with-caches
[Como criar um objeto DataCache]: #create-cache-object
[Como adicionar e recuperar um objeto do cache]: #add-object
[Como especificar a expiração de um objeto no cache]: #specify-expiration
[Como armazenar o estado da sessão ASP.NET no cache]: #store-session
[Como armazenar o cache de saída de página ASP.NET no cache]: #store-page
[Ter Como Meta um Perfil do .NET Framework com Suporte]: #prepare-vs-target-net

<!-- IMAGES -->
[NewCacheMenu]: ./media/cache-dotnet-how-to-use-service/CacheServiceNewCacheMenu.png

[QuickCreate]: ./media/cache-dotnet-how-to-use-service/CacheServiceQuickCreate.png

[Ponto de extremidade]: ./media/cache-dotnet-how-to-use-service/CacheServiceEndpoint.png

[AccessKeys]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageAccessKeys.png

[NuGetPackage]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackage.png

[NuGetPackageMenu]: ./media/cache-dotnet-how-to-use-service/CacheServiceManageNuGetPackagesMenu.png

[NamedCaches]: ./media/cache-dotnet-how-to-use-service/CacheServiceNamedCaches.jpg


<!-- LINKS -->
[Portal Clássico do Azure]: https://manage.windowsazure.com/
[Como configurar um cliente de cache de forma programática]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[Provedor de estado de sessão para o Cache do Azure]: http://go.microsoft.com/fwlink/?LinkId=320835
[Cache do Azure AppFabric: estado de sessão do caching]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Provedor de Cache de Saída para o Cache do Azure]: http://go.microsoft.com/fwlink/?LinkId=320837
[Cache compartilhado do Azure]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx
[Blog da equipe]: http://blogs.msdn.com/b/windowsazure/
[Cache do Azure]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[Como configurar tamanhos de máquinas virtuais]: http://go.microsoft.com/fwlink/?LinkId=164387
[Considerações sobre o planejamento da capacidade do Cache do Azure]: http://go.microsoft.com/fwlink/?LinkId=320167
[Cache do Azure]: http://go.microsoft.com/fwlink/?LinkId=252658
[Como definir a capacidade de cache de uma página ASP.NET de forma declarativa]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[Como definir a capacidade de cache de uma página de forma programática]: http://msdn.microsoft.com/library/z852zf6b.aspx
[Visão geral do Serviço de Cache do Azure Gerenciado]: http://go.microsoft.com/fwlink/?LinkId=320830
[Serviço de Cache Gerenciado]: http://go.microsoft.com/fwlink/?LinkId=320830
[Diretiva OutputCache]: http://go.microsoft.com/fwlink/?LinkId=251979
[Solução de problemas e diagnóstico]: http://go.microsoft.com/fwlink/?LinkId=320839
[Instalação do Gerenciador de Pacotes NuGet]: http://go.microsoft.com/fwlink/?LinkId=240311
[Detalhes de preços do Cache]: http://www.windowsazure.com/pricing/details/cache/
[Ofertas de cache]: http://go.microsoft.com/fwlink/?LinkId=317277
[Planejamento da capacidade]: http://go.microsoft.com/fwlink/?LinkId=320167
[Expiração e remoção]: http://go.microsoft.com/fwlink/?LinkId=317278
[Alta disponibilidade]: http://go.microsoft.com/fwlink/?LinkId=317329
[Notificações]: http://go.microsoft.com/fwlink/?LinkId=317276
[Migrar para o Serviço de Cache Gerenciado]: http://go.microsoft.com/fwlink/?LinkId=317347
[Exemplos de Serviço de Cache Gerenciado]: http://go.microsoft.com/fwlink/?LinkId=320840
[New-AzureManagedCache]: http://go.microsoft.com/fwlink/?LinkId=400495
[Cmdlets de Cache gerenciado do Azure]: http://go.microsoft.com/fwlink/?LinkID=398555
[Como instalar e configurar o Azure PowerShell]: http://go.microsoft.com/fwlink/?LinkId=400494
[Add-AzureAccount]: http://msdn.microsoft.com/library/dn495128.aspx
[Select-AzureSubscription]: http://msdn.microsoft.com/library/dn495203.aspx

[Qual oferta de Cache do Azure é a correta para mim?]: cache-faq.md#which-azure-cache-offering-is-right-for-me




<!--HONumber=Nov16_HO3-->


