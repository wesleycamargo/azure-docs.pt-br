<properties 
	pageTitle="Como usar o Cache na Função (.NET) | Microsoft Azure" 
	description="Saiba como usar o Cache na Função do Azure. Os exemplos são escritos em código C# e utilizam a API .NET." 
	services="cache" 
	documentationCenter=".net" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/15/2016" 
	ms.author="sdanie"/>






# Como usar o Cache na Função para Cache do Azure

Este guia mostra como começar a usar o **Cache na Função para Cache do Azure**. Os exemplos são escritos em código C# e utilizam a API .NET. Os cenários abordados incluem a **configuração de um cluster de cache**, **configuração de clientes de cache**, **adição e remoção de objetos do cache, armazenando o estado de sessão ASP.NET no cache ** e **habilitação do cache de saída da página ASP.NET usando o cache**. Para saber mais sobre como usar o Cache na Função, consulte a seção [Próximas etapas][].

>[AZURE.IMPORTANT]De acordo com o [comunicado](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) do ano passado, o Serviço de Cache Gerenciado do Azure e o serviço Cache na Função do Azure serão desativados em 30 de novembro de 2016. A nossa recomendação é usar o [Cache Redis do Azure](https://azure.microsoft.com/services/cache/). Para saber mais sobre a migração, confira [Migrar do Serviço de Cache gerenciado para o Cache Redis do Azure](../redis-cache/cache-migrate-to-redis.md).

<a name="what-is"></a>
## O que é Cache na Função?

O Cache na Função fornece uma camada de cache para seus aplicativos do Azure. O cache aumenta o desempenho armazenando temporariamente informações na memória de outras origens de back-end e pode reduzir os custos associados às transações de banco de dados na nuvem. O Cache na Função inclui os seguintes recursos:

-   Provedores ASP.NET pré-criados para cache de estado de seção e de saída de página, que habilitam a aceleração de aplicativos Web sem necessidade de modificar o código do aplicativo.
-   Armazena qualquer objeto serializável gerenciado em cache, por exemplo: objetos CLR, linhas, XML, dados binários.
-   Modelo de desenvolvimento consistente no Azure e no Windows Server AppFabric.

O Cache na Função fornece uma maneira de armazenar em cache usando uma parte da memória das máquinas virtuais que hospedam as instâncias de função em seus Serviços de Nuvem do Azure (também conhecidos como serviços hospedados). Você tem mais flexibilidade em termos de opções de implantação. Os caches podem ser muito grandes e não têm nenhuma restrição de cota específica ao cache.

>[AZURE.IMPORTANT] A partir do SDK 2.6 d Azure, o Cache na função está usando o SDK de Armazenamento do Microsoft Azure versão 4.3. Nas versões anteriores do SDK do Azure, o Cache na função usava o SDK de Armazenamento do Azure versão 1.7. Aplicativos que usam o Cache na Função com as versões do SDK do Azure anteriores à versão 2.6 devem migrar para o SDK do Azure 2.6 antes de a versão do Armazenamento do Azure 2011-08-18 ser descomissionada no dia 1º de agosto de 2016. Para obter mais informações, confira [Notas de versão do Azure SDK 2.6 – cache na função](../azure-sdk-dotnet-release-notes-2-6.md#in-role-cache-updates) e [Atualização sobre a remoção de versão do serviço de Armazenamento do Microsoft Azure: extensão até 2016](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx).

O cache em instâncias de função tem as seguintes vantagens:

-	Não paga nenhum premium para o caching. Você paga apenas pelos recursos de computação que hospedam o cache.
-	Elimina as cotas e a limitação do cache.
-	Oferece maior controle e isolamento.
-	Desempenho aprimorado.
-	Dimensiona automaticamente os caches quando as funções são expandidas ou reduzidas. Aumenta ou diminui efetivamente a memória disponível para caching baixo quando as instâncias de função são adicionadas ou removidas.
-	Fornece a depuração em tempo de desenvolvimento com fidelidade total.
-	Oferece suporte ao protocolo memcache.

Além disso, o caching em instâncias de função oferece estas opções configuráveis:

-	Configurar uma função dedicada para caching ou colocalizar o caching em funções existentes.
-	Disponibilizar seu cache para vários clientes na mesma implantação de serviço de nuvem.
-	Criar vários caches nomeados com propriedades diferentes.
-	Opcionalmente, configurar alta disponibilidade em caches individuais.
-	Usar recursos de caching expandido, como regiões, marcação e notificações.

Este guia fornece uma visão geral da introdução ao Cache na Função. Para obter mais informações sobre esses recursos que estão além do escopo deste guia de introdução, consulte [Visão geral do Cache na Função][].

<a name="getting-started-cache-role-instance"></a>
## Introdução ao Cache na Função

O Cache na Função fornece uma maneira de habilitar caching usando a memória que está nas máquinas virtuais que hospedam as instâncias de função. As instâncias de função que hospedam seus caches são conhecidas como um **cluster de cache**. Há duas topologias de implantação para caching em instâncias de função:

-	**Caching de função dedicada** - as instâncias de função são usadas exclusivamente para caching.
-	**Caching de função colocalizada** - o cache compartilha os recursos de máquina virtual (largura de banda, CPU e memória) com o aplicativo.

Para usar o caching em instâncias de função, você precisa configurar um cluster de cache e, em seguida, configurar os clientes de cache para que possam acessar o cluster de cache.

-	[Configurar o cluster de cache][]
-	[Configurar os clientes de cache][]

<a name="enable-caching"></a>
## Configurar o cluster de cache

Para configurar um cluster de cache de **Função Colocalizada**, selecione a função na qual você deseja hospedar o cluster de cache. Clique com o botão direito do mouse nas propriedades da função no **Gerenciador de Soluções** e escolha **Propriedades**.

![Cache de função 1][RoleCache1]

Alterne para a guia **Caching**, marque a caixa de seleção **Habilitar Caching** e especifique as opções de cache desejadas. Quando o caching está habilitado em uma **Função de Trabalho** ou **Função Web ASP.NET**, a configuração padrão é o caching de **Função Colocalizada** com 30% da memória das instâncias de função alocadas para o caching. Um cache padrão é configurado automaticamente, e os caches nomeados adicionais podem ser criados se desejados, e esses caches compartilharão a memória alocada.

![Cache de função 2][RoleCache2]

Para configurar um cluster de cache de **Função dedicada**, adicione uma **Função de Trabalho do Cache** a seu projeto.

![Cache de função 7][RoleCache7]

Quando uma **Função de Trabalho de Cache** é adicionada a um projeto, a configuração padrão é cache de **Função Dedicada**.

![Cache de função 8][RoleCache8]

Quando o cache estiver habilitado, a conta de armazenamento do cluster de cache pode ser configurada. O Cache na Função requer uma conta de Armazenamento do Azure. Essa conta de armazenamento é usada para armazenar dados de configuração sobre o cluster de cache que é acessado de todas as máquinas virtuais que fazem parte do cluster de cache. Essa conta de armazenamento é especificada na guia **Caching** da página de propriedades da função de cluster de cache, acima de **Configurações de Cache Nomeado**.

![Cache de função 10][RoleCache10]

>Se essa conta de armazenamento não estiver configurada, as funções não serão iniciadas.

O tamanho do cache é determinado por uma combinação do tamanho da VM da função, a contagem da instância da função e, se o cluster de cache está configurado como uma função dedicada ou cluster de cache de função colocalizado.

>Esta seção fornece uma visão simplificada sobre como configurar o tamanho do cache. Para obter mais informações sobre o tamanho do cache e outras considerações sobre o planejamento da capacidade, consulte [Considerações sobre o planejamento da capacidade do Cache na Função][].

Para configurar o tamanho da máquina virtual e o número de instâncias de função, clique com o botão direito do mouse nas propriedades da função no **Gerenciador de Soluções** e escolha **Propriedades**.

![Cache de função 1][RoleCache1]

Alterne para a guia **Configuração**. A **Contagem de instâncias** padrão é 1, e o **Tamanho da VM** padrão é **Pequena**.

![Cache de função 3][RoleCache3]

A memória total dos tamanhos de VM é a seguinte:

-	**Pequena**: 1,75 GB
-	**Média**: 3,5 GB
-	**Grande**: 7 GB
-	**Extra grande**: 14 GB


> Esses tamanhos de memória representam a quantidade total de memória disponível para a máquina virtual que é compartilhada entre o SO, o processo de cache, o cache de dados e os aplicativos. Para obter mais informações sobre como configurar tamanhos de máquina virtual, consulte [Como configurar tamanhos de máquinas virtuais][]. Observe que o cache não é suportado em tamanhos de máquinas virtuais **Extra pequeno**.

Quando o cache de **Função Colocalizada** estiver especificado, o tamanho do cache será determinado utilizando a porcentagem especificada de memória da máquina virtual. Quando caching de **Função Dedicada** estiver especificado, toda a memória disponível da máquina virtual será usada para caching. Se duas instâncias de função estiverem configuradas, a memória combinada das máquinas virtuais será usada. Isso forma um cluster de cache onde a memória de caching disponível é distribuída entre várias instâncias de função, mas é apresentada aos clientes do cache como um único recurso. Configurar instâncias de função adicionais aumenta o tamanho do cache da mesma maneira. Para determinar as configurações necessárias para provisionar um cache do tamanho desejado, você pode usar a Planilha de Planejamento da Capacidade coberta em [Considerações sobre o planejamento da capacidade de Cache na Função][].

Depois que o cluster de cache estiver configurado, você poderá configurar os clientes de cache para permitir o acesso ao cache.

<a name="NuGet"></a>
## Configurar os clientes de cache

Para acessar um cache de Cache na Função, os clientes devem estar na mesma implantação. Se o cluster de cache for um cluster de cache de função dedicado, os clientes serão outras funções na implantação. Se o cluster de cache for um cluster de cache de função colocalizado, os clientes poderão ser outras funções na implantação ou as próprias funções que hospedam o cluster de cache. É fornecido um pacote NuGet que pode ser usado para configurar cada função de cliente que acessa o cache. Para configurar uma função para acessar um cluster de cache usando o pacote NuGet de Caching, clique com o botão direito do mouse no projeto de função no **Gerenciador de Soluções** e escolha **Gerenciar Pacotes NuGet**.

![Cache de função 4][RoleCache4]

Selecione **Cache na Função**, clique em **Instalar** e, em seguida, clique em **Aceito**.

>Se **Cache na Função** não aparecer na lista, digite **WindowsAzure.Caching** na caixa de texto **Pesquisar Online** e selecione-o nos resultados.

![Cache de função 5][RoleCache5]

O pacote NuGet faz várias coisas: adiciona a configuração necessária ao arquivo de configuração da função, adiciona uma configuração em nível de diagnóstico do cliente do cache ao arquivo ServiceConfiguration.cscfg do aplicativo do Azure e adiciona as referências ao assembly necessárias.

>Para funções web ASP.NET, o pacote NuGet de Cache também adiciona duas seções comentadas ao web.config. A primeira seção permite que o estado da sessão seja armazenado no cache, e a segunda seção habilita a saída do cache da página ASP.NET. Para saber mais, consulte [Como armazenar o estado da sessão ASP.NET no cache] e [Como armazenar o cache de saída de página ASP.NET no cache][].

O pacote NuGet adiciona os seguintes elementos de configuração ao web.config ou ao app.config de sua função. Uma seção **dataCacheClients** e uma seção **cacheDiagnostics** são adicionadas sob o elemento **configSections**. Se não houver nenhum elemento **configSections** presente, será criado um como um filho do elemento **configuration**.

    <configSections>
      <!-- Existing sections omitted for clarity. -->

      <section name="dataCacheClients" 
               type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" 
               allowLocation="true" 
               allowDefinition="Everywhere" />
    
      <section name="cacheDiagnostics" 
               type="Microsoft.ApplicationServer.Caching.AzureCommon.DiagnosticsConfigurationSection, Microsoft.ApplicationServer.Caching.AzureCommon" 
               allowLocation="true" 
               allowDefinition="Everywhere" />
    </configSections>

Essas novas seções incluem referências a um elemento **dataCacheClients** e a um elemento **cacheDiagnostics**. Esses elementos também são adicionados ao elemento **configuration**.

    <dataCacheClients>
      <dataCacheClient name="default">
        <autoDiscover isEnabled="true" identifier="[cache cluster role name]" />
        <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
      </dataCacheClient>
    </dataCacheClients>
    <cacheDiagnostics>
      <crashDump dumpLevel="Off" dumpStorageQuotaInMB="100" />
    </cacheDiagnostics>

Após a configuração ser adicionada, substitua o **[nome da função do cluster de cache]** pelo nome da função que hospeda o cluster de cache.

>Se o **[nome da função do cluster de cache]** não for substituído pelo nome da função que hospeda o cluster de cache, uma **TargetInvocationException** será lançada quando o cache for acessado com uma **DatacacheException** interna com a mensagem "essa função não existe".

O pacote NuGet também adiciona uma configuração de **ClientDiagnosticLevel** ao **ConfigurationSettings** da função do cliente de cache em ServiceConfiguration.cscfg. O exemplo a seguir é a seção **WebRole1** de um arquivo com um **ClientDiagnosticLevel** igual a 1, que é o **ClientDiagnosticLevel**.

    <Role name="WebRole1">
      <Instances count="1" />
      <ConfigurationSettings>
        <!-- Existing settings omitted for clarity. -->
        <Setting name="Microsoft.WindowsAzure.Plugins.Caching.ClientDiagnosticLevel" 
                 value="1" />
      </ConfigurationSettings>
    </Role>

>O Cache na Função fornece um nível de diagnóstico de servidor de cache e de cliente de cache. O nível de diagnóstico é uma única configuração que configura o nível das informações de diagnóstico coletadas para o caching. Para obter mais informações, consulte [Solução de problemas e diagnóstico do Cache na Função][]

O pacote NuGet também adiciona referências aos seguintes assemblies:

-   Microsoft.ApplicationServer.Caching.Client.dll
-   Microsoft.ApplicationServer.Caching.Core.dll
-   Microsoft.WindowsFabric.Common.dll
-   Microsoft.WindowsFabric.Data.Common.dll
-   Microsoft.ApplicationServer.Caching.AzureCommon.dll
-   Microsoft.ApplicationServer.Caching.AzureClientHelper.dll

Se sua função for uma função web ASP.NET, a referência ao assembly a seguir também será adicionada:

-	Microsoft.Web.DistributedCache.dll.

Depois que o projeto de cliente estiver configurado para caching, você poderá usar as técnicas descritas nas seções a seguir para trabalhar com o cache.

<a name="working-with-caches"></a>
## Trabalhando com caches

As etapas desta seção descrevem como realizar tarefas comuns com o caching.

-	[Como criar um objeto DataCache][]
-   [Como adicionar e recuperar um objeto do cache][]
-   [Como especificar a expiração de um objeto no cache][]
-   [Como armazenar o estado da sessão ASP.NET no cache][]
-   [Como armazenar o cache de saída de página ASP.NET no cache][]

<a name="create-cache-object"></a>
## Como criar um objeto DataCache

Para trabalhar de forma programática com um cache, você precisa de uma referência ao cache. Adicione o seguinte à parte superior de qualquer arquivo do qual você deseja usar Cache de Função:

    using Microsoft.ApplicationServer.Caching;

>Se o Visual Studio não reconhecer os tipos na instrução "using" mesmo depois da instalação do pacote NuGet de Cache, que adiciona as referências necessárias, verifique se o perfil de destino do projeto é o .NET Framework 4,0 ou superior e selecione um dos perfis que não especificam o **Perfil do Cliente**. Para obter instruções sobre como configurar clientes de cache, consulte [Configurar os clientes de cache][].

Existem duas maneiras de criar um objeto **DataCache**. A primeira maneira é simplesmente criar um **DataCache**, passando o nome do cache desejado.

    DataCache cache = new DataCache("default");

Quando o **DataCache** for instanciado, você poderá usá-lo para interagir com o cache, conforme descrito nas seções a seguir.

Para usar a segunda maneira, crie um novo objeto **DataCacheFactory** em seu aplicativo usando o construtor padrão. Isso faz com que o cliente de cache use os parâmetros do arquivo de configuração. Chame o método **GetDefaultCache** da nova instância **DataCacheFactory** que retorna um objeto **DataCache** ou o método **GetCache** e passe o nome de seu cache. Esses métodos retornam um objeto **DataCache** que pode ser usado para acessar programaticamente o cache.

    // Cache client configured by settings in application configuration file.
    DataCacheFactory cacheFactory = new DataCacheFactory();
    DataCache cache = cacheFactory.GetDefaultCache();
    // Or DataCache cache = cacheFactory.GetCache("MyCache");
    // cache can now be used to add and retrieve items.	

<a name="add-object"></a>
## Como adicionar e recuperar um objeto do cache

Para adicionar um item ao cache, o método **Add** ou o método **Put** pode ser usado. O método **Add** adiciona o objeto especificado ao cache, inserido pelo valor do parâmetro chave.

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value");

Se um objeto com a mesma chave já estiver no cache, uma **DataCacheException** será lançada com a seguinte mensagem:

> ErrorCode:SubStatus: está sendo feita uma tentativa de criar um objeto comum à chave que já existe no cache. O cache aceita somente valores de chave exclusivos para objetos.

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
## Como especificar a expiração de um objeto no cache

Por padrão, os itens no cache expiram 10 minutos depois que são colocados no cache. Isso pode ser configurado na configuração **Vida Útil (min)** nas propriedades da função que hospeda o cluster de cache.

![Cache de função 6][RoleCache6]

Existem três tipos de **Tipo de Expiração**: **Nenhum**, **Absoluto** e **Janela Deslizante**. Esses tipos configuram como a opção **Vida Útil (min)** é usada para determinar a expiração. O **Tipo de Expiração** padrão é **Absoluto**, o que significa que o temporizador da contagem regressiva de expiração de um item começa quando o item é colocado no cache. Depois que o período de tempo especificado tiver decorrido para um item, o item expira. Se **Janela Deslizante** for especificado, a contagem regressiva da expiração de um item será zerada cada vez que o item for acessado no cache, e o item não vai expirar até que o período de tempo especificado tiver decorrido desde o seu último acesso. Se **Nenhum** for especificado, a opção **Vida Útil (min)** deverá ser definida como **0**, e os itens não expirarão e permanecerão válidos enquanto estiverem no cache.

Se um intervalo de tempo limite maior ou menor do que o que está configurado nas propriedades da função ou desejado, uma duração específica poderá ser especificada quando um item for adicionado ou atualizado no cache usando a sobrecarga de **Add** e **Put** que utilizam um parâmetro **TimeSpan**. No exemplo a seguir, a cadeia de caracteres **value** é adicionada ao cache, inserida por **item**, com um tempo limite de 30 minutos.

    // Add the string "value" to the cache, keyed by "item"
    cache.Add("item", "value", TimeSpan.FromMinutes(30));

Para exibir o intervalo de tempo limite restante de um item no cache, o método **GetCacheItem** pode ser usado para recuperar um objeto **DataCacheItem** que contém informações sobre o item no cache, incluindo o intervalo de tempo limite restante.

    // Get a DataCacheItem object that contains information about
    // "item" in the cache. If there is no object keyed by "item" null
    // is returned. 
    DataCacheItem item = cache.GetCacheItem("item");
    TimeSpan timeRemaining = item.Timeout;

<a name="store-session"></a>
## Como armazenar o estado da sessão ASP.NET no cache

O Provedor de Estado de Sessão para Cache na Função é um mecanismo de armazenamento fora do processo para aplicativos ASP.NET. Esse provedor permite que você armazene o estado da sessão em um cache do Azure, em vez de na memória ou em um banco de dados do SQL Server. Para usar o provedor de estado de sessão de cache, primeiro configure o cluster de cache e, em seguida, configure o aplicativo ASP.NET para cache usando o pacote NuGet de Cache conforme descrito em [Introdução ao Cache na Função][]. Quando o pacote NuGet de Cache está instalado, ele adiciona uma seção comentada no web.config que contém a configuração necessária para seu aplicativo ASP.NET usar o Provedor de Estado de Sessão para Cache na Função.

    <!--Uncomment this section to use In-Role Cache for session state caching
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

>Se o web.config não contiver essa seção comentada depois da instalação do pacote NuGet de Caching, verifique se o Gerenciador de Pacotes NuGet mais recente está instalado em [Instalação do Gerenciador de Pacotes NuGet][] e, em seguida, desinstale e reinstale o pacote.

Para habilitar o Provedor de Estado de Sessão para o Cache na Função, remova os comentários da seção especificada. O cache padrão é especificado no trecho de código fornecido. Para usar um outro cache, especifique o cache desejado no atributo **cacheName**.

Para saber mais sobre como usar o provedor de estado de sessão do serviço de cache, consulte [Provedor de Estado de Sessão de Cache na Função][].

<a name="store-page"></a>
## Como armazenar o cache de saída de página ASP.NET no cache

O Provedor de Cache de Saída do Cache na Função é um mecanismo de armazenamento fora do processo para dados do cache de saída. Esses dados são especificamente para respostas HTTP completas (cache de saída de página). O provedor conecta-se ao novo saída cache provedor ponto de extensibilidade que foi introduzido no ASP.NET 4. Para usar o provedor de estado de cache de saída, primeiro configure o cluster de cache e, em seguida, configure o aplicativo ASP.NET para cache usando o pacote NuGet de Cache conforme descrito em [Introdução ao Cache na Função][]. Quando o pacote NuGet de Caching está instalado, ele adiciona a seguinte seção comentada ao web.config que contém a configuração necessária para seu aplicativo ASP.NET usar o Provedor de Cache de Saída para Cache na Função.

    <!--Uncomment this section to use In-Role Cache for output caching
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

>Se o web.config não contiver essa seção comentada depois da instalação do pacote NuGet de Caching, verifique se o Gerenciador de Pacotes NuGet mais recente está instalado em [Instalação do Gerenciador de Pacotes NuGet][] e, em seguida, desinstale e reinstale o pacote.

Para habilitar o Provedor de Cache de Saída para o Cache na Função, remova o comentário da seção especificada. O cache padrão é especificado no trecho de código fornecido. Para usar um outro cache, especifique o cache desejado no atributo **cacheName**.

Adicione uma diretiva **OutputCache** a cada página da qual você deseja armazenar a saída em cache.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

Neste exemplo, os dados da página em cache permanecerão no cache por 60 segundos, e outra versão da página será armazenada em cache para cada combinação de parâmetros. Para obter mais informações sobre as opções disponíveis, consulte [Diretiva OutputCache][].

Para obter mais informações sobre como usar o Provedor de Cache de Saída para Cache na Função, consulte [Provedor de Cache de Saída de Cache na Função][].

<a name="next-steps"></a>
## Próximas etapas

Agora que você aprendeu o básico do Cache na Função, siga os links para saber como realizar tarefas de cache mais complexas.

-   Consulte a referência do MSDN: [Cache na Função][]
-   Saiba como migrar para o Cache na Função: [Migrar para o Cache na Função][]
-   Confira os exemplos: [Exemplos de Cache na Função][]
-	Assista à sessão [Desempenho máximo: acelere seus aplicativos de Serviços de Nuvem com o Caching do Azure][] no TechEd 2013 sobre Cache na Função

<!-- INTRA-TOPIC LINKS -->
[Próximas etapas]: #next-steps
[What is In-Role Cache?]: #what-is
[Create an Azure Cache]: #create-cache
[Which type of caching is right for me?]: #choosing-cache
[Getting Started with the In-Role Cache Service]: #getting-started-cache-service
[Prepare Your Visual Studio Project to Use In-Role Cache]: #prepare-vs
[Configure Your Application to Use Caching]: #configure-app
[Introdução ao Cache na Função]: #getting-started-cache-role-instance
[Configurar o cluster de cache]: #enable-caching
[Configure the desired cache size]: #cache-size
[Configurar os clientes de cache]: #NuGet
[Working with Caches]: #working-with-caches
[Como criar um objeto DataCache]: #create-cache-object
[Como adicionar e recuperar um objeto do cache]: #add-object
[Como especificar a expiração de um objeto no cache]: #specify-expiration
[Como armazenar o estado da sessão ASP.NET no cache]: #store-session
[Como armazenar o cache de saída de página ASP.NET no cache]: #store-page
[Target a Supported .NET Framework Profile]: #prepare-vs-target-net
 
<!-- IMAGES --> 
[RoleCache1]: ./media/cache-dotnet-how-to-use-in-role/cache8.png
[RoleCache2]: ./media/cache-dotnet-how-to-use-in-role/cache9.png
[RoleCache3]: ./media/cache-dotnet-how-to-use-in-role/cache10.png
[RoleCache4]: ./media/cache-dotnet-how-to-use-in-role/cache11.png
[RoleCache5]: ./media/cache-dotnet-how-to-use-in-role/cache12.png
[RoleCache6]: ./media/cache-dotnet-how-to-use-in-role/cache13.png
[RoleCache7]: ./media/cache-dotnet-how-to-use-in-role/cache14.png
[RoleCache8]: ./media/cache-dotnet-how-to-use-in-role/cache15.png
[RoleCache10]: ./media/cache-dotnet-how-to-use-in-role/cache17.png
  
<!-- LINKS -->
[Como configurar tamanhos de máquinas virtuais]: http://go.microsoft.com/fwlink/?LinkId=164387
[How to: Configure a Cache Client Programmatically]: http://msdn.microsoft.com/library/windowsazure/gg618003.aspx
[How to: Set a Page's Cacheability Programmatically]: http://msdn.microsoft.com/library/z852zf6b.aspx
[How to: Set the Cacheability of an ASP.NET Page Declaratively]: http://msdn.microsoft.com/library/zd1ysf1y.aspx
[Considerações sobre o planejamento da capacidade de Cache na Função]: http://go.microsoft.com/fwlink/?LinkId=252651
[Considerações sobre o planejamento da capacidade do Cache na Função]: http://go.microsoft.com/fwlink/?LinkId=252651
[Exemplos de Cache na Função]: http://msdn.microsoft.com/library/jj189876.aspx
[In-Role Cache]: http://go.microsoft.com/fwlink/?LinkId=252658
[Cache na Função]: http://www.microsoft.com/showcase/Search.aspx?phrase=azure+caching
[Desempenho máximo: acelere seus aplicativos de Serviços de Nuvem com o Caching do Azure]: http://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/WAD-B326#fbid=kmrzkRxQ6gU
[Migrar para o Cache na Função]: http://msdn.microsoft.com/library/hh914163.aspx
[Instalação do Gerenciador de Pacotes NuGet]: http://go.microsoft.com/fwlink/?LinkId=240311
[Provedor de Cache de Saída de Cache na Função]: http://msdn.microsoft.com/library/windowsazure/gg185662.aspx
[Diretiva OutputCache]: http://go.microsoft.com/fwlink/?LinkId=251979
[Visão geral do Cache na Função]: http://go.microsoft.com/fwlink/?LinkId=254172
[Provedor de Estado de Sessão de Cache na Função]: http://msdn.microsoft.com/library/windowsazure/gg185668.aspx
[Team Blog]: http://blogs.msdn.com/b/windowsazure/
[Solução de problemas e diagnóstico do Cache na Função]: http://msdn.microsoft.com/library/windowsazure/hh914135.aspx
[Azure AppFabric Cache: Caching Session State]: http://www.microsoft.com/showcase/details.aspx?uuid=87c833e9-97a9-42b2-8bb1-7601f9b5ca20
[Azure Shared Caching]: http://msdn.microsoft.com/library/windowsazure/gg278356.aspx

[Which Azure Cache offering is right for me?]: cache-faq.md#which-azure-cache-offering-is-right-for-me
 

<!---HONumber=AcomDC_0921_2016-->