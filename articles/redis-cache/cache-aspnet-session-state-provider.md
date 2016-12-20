---
title: "Armazenar em cache o Provedor de Estado de Sessão do ASP.NET | Microsoft Docs"
description: "Saiba como armazenar o Estado da sessão ASP.NET usando o Cache Redis do Azure"
services: redis-cache
documentationcenter: na
author: steved0x
manager: douge
editor: tysonn
ms.assetid: 192f384c-836a-479a-bb65-8c3e6d6522bb
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 12/13/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 034539eabb6ec7110dd584c42cf35157d39446b6


---
# <a name="aspnet-session-state-provider-for-azure-redis-cache"></a>Provedor de estado de sessão ASP.NET para Cache Redis do Azure
O Cache Redis do Azure fornece um provedor de estado da sessão que você pode usar para armazenar seu estado de sessão em um cache ao invés de fazê-lo na memória ou em um banco de dados SQL Server. Para usar o provedor de estado de sessão de cache, primeiro configure o cache e, em seguida, configure seu aplicativo ASP.NET para o cache usando o pacote NuGet de Estado de Sessão do Cache Redis.

Geralmente, não é prático em uma aplicativo em nuvem real evitar o armazenamento de alguma forma de estado para uma sessão de usuário, mas algumas abordagens afetam o desempenho e a escalabilidade mais do que outras. Se você precisar armazenar o estado, a melhor solução será manter o estado em uma quantidade pequena e armazená-lo em cookies. Se isso não for viável, a próxima solução mais adequada será usar o estado de sessão ASP.NET com um provedor para cache distribuído na memória. A pior solução de um ponto de vista de escalabilidade e desempenho é usar um provedor de estado de sessão com backup em um banco de dados. Este tópico fornece diretrizes sobre como usar o Provedor de Estado de Sessão ASP.NET para o Cache Redis do Azure. Para saber mais sobre outras opções de estado de sessão, consulte [Opções de estado da sessão ASP.NET](#aspnet-session-state-options).

## <a name="store-aspnet-session-state-in-the-cache"></a>Armazenar o estado da sessão ASP.NET no cache
Para configurar um aplicativo de cliente no Visual Studio utilizando o pacote NuGet de estado de sessão de cache Redis, clique com o botão direito no projeto no **Gerenciador de Soluções** e escolha **Gerenciar Pacotes NuGet**.

![Gerenciamento de pacotes NuGet pelo Cache Redis do Azure](./media/cache-aspnet-session-state-provider/redis-cache-manage-nuget-menu.png)

Digite **RedisSessionStateProvider** na caixa de texto de pesquisa, selecione-o nos resultados e clique em **Instalar**.

> [!IMPORTANT]
> Se você estiver usando o recurso de cluster a partir da camada premium, use [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 ou superior, caso contrário uma exceção será emitida. Isso é uma alteração significativa; para saber mais, confira [Detalhes de alteração significativa da v2.0.0](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).
> 
> 

![Provedor de estado de sessão do Cache Redis do Azure](./media/cache-aspnet-session-state-provider/redis-cache-session-state-provider.png)

O pacote NuGet do Provedor de estado de sessão Redis tem uma dependência do pacote StackExchange.Redis.StrongName. Se o pacote StackExchange.Redis.StrongName não estiver presente em seu projeto, ele será instalado. Observe que além do pacote StackExchange.Redis.StrongName de nome forte, também há a versão do StackExchange.Redis sem nome forte. Se o seu projeto estiver usando a versão do StackExchange.Redis sem nome forte, será necessário desinstalá-la, antes ou depois de instalar o pacote NuGet de Provedor de estado de sessão Redis, caso contrário, você receberá conflitos de nomenclatura em seu projeto. Para saber mais sobre esses pacotes, consulte [Configurar clientes de cache .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

Os downloads de pacote NuGet acrescentam as referências de assembly necessárias e adicionam a seguinte seção dentro de seu arquivo web.config que contém a configuração solicitada para seu aplicativo ASP.NET a fim de usar o provedor de estado de sessão de cache Redis do Azure.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
    <!--
    <add name="MySessionStateStore"
           host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        throwOnError = "true" [true|false]
        retryTimeoutInMilliseconds = "0" [number]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "5000" [number]
    />
    -->
    <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider" host="127.0.0.1" accessKey="" ssl="false"/>
    </providers>
</sessionState>
```

A seção comentada fornece um exemplo dos atributos e exemplos de configurações para cada atributo.

Configure os atributos com os valores de sua folha de cache no Portal do Microsoft Azure e configure os outros valores conforme o desejado. Para obter instruções sobre como acessar as propriedades do cache, consulte [Definir as configurações de cache Redis](cache-configure.md#configure-redis-cache-settings).

* **host** – especifique o ponto de extremidade do cache.
* **porta** – use sua porta SSL ou sua porta que não é do tipo SSL, dependendo das configurações de ssl.
* **accessKey** – use a chave primária ou secundária de seu cache.
* **ssl** – true se você quiser proteger as comunicações de cache/cliente com ssl; caso contrário, false. Especifique a porta correta.
  * A porta não SSL é desabilitada por padrão para novos caches. Especifique true para essa configuração a fim de usar a porta SSL. Para saber mais sobre como habilitar a porta que não é do tipo SSL, confira a seção [Portas de acesso](cache-configure.md#access-ports) no tópico [Configurar um cache](cache-configure.md).
* **throwOnError** – true se você quiser que uma exceção seja lançada em caso de falha, ou false se quiser que a operação falhe silenciosamente. Você pode verificar a existência de uma falha na propriedade estática Microsoft.Web.Redis.RedisSessionStateProvider.LastException. O padrão é true.
* **retryTimeoutInMilliseconds** – as operações que apresentam falhas recebem uma nova tentativa durante esse intervalo, especificado em milissegundos. A primeira nova tentativa ocorre após 20 milissegundos e outras novas tentativas ocorrem a cada segundo até que o intervalo de retryTimeoutInMilliseconds expire. Imediatamente após esse intervalo, a operação será repetida uma última vez. Se a operação ainda falhar, a exceção será lançada de volta ao chamador, dependendo da configuração de throwOnError. O valor padrão é 0, que significa nenhuma tentativa nova.
* **databaseId** – especifica qual banco de dados usar para os dados de saída do cache. Se esse campo não for especificado, o valor padrão de 0 será usado.
* **applicationName** – As chaves são armazenadas em redis como `{<Application Name>_<Session ID>}_Data`. Isso permite que vários aplicativos compartilhem a mesma chave. Esse parâmetro é opcional e, se você não fornecê-lo, um valor padrão será usado.
* **connectionTimeoutInMilliseconds** – essa configuração permite a substituição da configuração de connectTimeout no cliente StackExchange.Redis. Se ela não for especificada, a configuração padrão do connectTimeout, 5000, será usada. Para saber mais, consulte [Modelo de configuração StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).
* **operationTimeoutInMilliseconds** – essa configuração permite a substituição da configuração de syncTimeout no cliente StackExchange.Redis. Se ela não for especificada, a configuração padrão de syncTimeout, 1000, será usada. Para saber mais, consulte [Modelo de configuração StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).

Para saber mais sobre essas propriedades, consulte o anúncio original da postagem do blog em [Anunciando o Provedor de estado de sessão ASP.NET para Redis](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

Não se esqueça de comentar a seção do provedor de estado de sessão InProc padrão em seu Web.config.

```xml
<!-- <sessionState mode="InProc"
     customProvider="DefaultSessionProvider">
     <providers>
        <add name="DefaultSessionProvider"
              type="System.Web.Providers.DefaultSessionStateProvider,
                    System.Web.Providers, Version=1.0.0.0, Culture=neutral,
                    PublicKeyToken=31bf3856ad364e35"
              connectionStringName="DefaultConnection" />
      </providers>
</sessionState> -->
```

Após a execução dessas etapas, seu aplicativo será configurado para usar o Provedor de estado de sessão de cache Redis. Quando você usa o estado de sessão em seu aplicativo, ele é armazenado em uma instância do Cache Redis do Azure.

> [!NOTE]
> Observe que os dados armazenados em cache devem ser serializáveis, ao contrário dos dados que podem ser armazenados no Provedor de estado de sessão padrão do ASP.NET na memória. Quando o Provedor de estado de sessão para Redis for usado, certifique-se de que os tipos de dados que estão sendo armazenados no estado de sessão sejam serializáveis.
> 
> 

## <a name="aspnet-session-state-options"></a>Opções de estado da sessão ASP.NET
* Provedor de estado de sessão na memória - Esse provedor armazena o Estado da sessão na memória. A vantagem de usar esse provedor é que ele é simples e rápido. No entanto, não é possível dimensionar seus Aplicativos Web se você estiver usando o provedor na memória, pois ele não é distribuído.
* Provedor de estado de sessão do SQL Server - Esse provedor armazena o Estado da sessão no SQL Server. Você deve usar esse provedor se quiser manter o estado da Sessão em um armazenamento persistente. Você pode dimensionar seu Aplicativo Web, mas o uso do Sql Server para sessão afetará o desempenho de seu Aplicativo Web.
* Provedor de estado de sessão distribuído na memória, como o Provedor de estado de sessão de cache Redis - Esse provedor oferece o melhor dos dois mundos. Seu Aplicativo Web pode ter um Provedor de estado de sessão simples, rápido e escalonável. No entanto, lembre-se de que esse provedor armazena o Estado da sessão em um Cache, e seu aplicativo precisa levar em consideração todas as características associadas ao conversar com um Cache distribuído na memória, por exemplo, falhas de rede temporárias. Para conhecer as práticas recomendadas sobre o uso do Cache, consulte [Orientação sobre cache](../best-practices-caching.md) da Microsoft Patterns & Practices [Orientação sobre design e implementação de aplicativo na nuvem do Azure](https://github.com/mspnp/azure-guidance).

Para saber mais sobre o estado da sessão e outras práticas recomendadas, consulte [Práticas recomendadas para o desenvolvimento na Web (Criando aplicativos de nuvem reais com o Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices).

## <a name="next-steps"></a>Próximas etapas
Confira o [Provedor de cache de saída ASP.NET para o Cache Redis do Azure](cache-aspnet-output-cache-provider.md)




<!--HONumber=Nov16_HO3-->


