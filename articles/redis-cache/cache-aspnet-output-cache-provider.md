<properties
	pageTitle="Provedor de Cache de Saída ASP.NET do Cache"
	description="Saiba como armazenar em cache a saída de página ASP.NET usando o Cache Redis do Azure"
	services="redis-cache"
	documentationCenter="na"
	authors="steved0x"
	manager="douge"
	editor="tysonn" />
<tags
	ms.service="cache"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="09/27/2016"
	ms.author="sdanie" />

# Provedor de Cache de Saída ASP.NET do Cache Redis do Azure

O Provedor de Cache de Saída Redis é um mecanismo de armazenamento fora do processo para dados do cache de saída. Esses dados são especificamente para respostas HTTP completas (cache de saída de página). O provedor conecta-se ao novo saída cache provedor ponto de extensibilidade que foi introduzido no ASP.NET 4.

Para usar o Provedor de Cache de Saída Redis, primeiro configure seu cache e, em seguida, configure seu aplicativo ASP.NET usando o pacote NuGet do Provedor de Cache de Saída Redis. Este tópico fornece orientação sobre como configurar seu aplicativo para usar o Provedor de Cache de Saída Redis. Para saber mais sobre como criar e configurar uma instância do Cache Redis do Azure, consulte [Criar um cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## Armazenar a saída da página ASP.NET no cache

Para configurar um aplicativo de cliente no Visual Studio utilizando o pacote NuGet do Provedor de Cache de Saída Redis, clique com o botão direito em projeto no **Gerenciador de Soluções** e escolha **Gerenciar Pacotes NuGet**.

![Gerenciamento de pacotes NuGet pelo Cache Redis do Azure](./media/cache-aspnet-output-cache-provider/redis-cache-manage-nuget-menu.png)

Digite **RedisOutputCacheProvider** na caixa de texto de pesquisa, selecione-o nos resultados e clique em **Instalar**.

![Provedor de Cache de Saída do Cache Redis do Azure](./media/cache-aspnet-output-cache-provider/redis-cache-page-output-provider.png)

O pacote NuGet do Provedor de Cache de Saída Redis tem uma dependência do pacote StackExchange.Redis.StrongName. Se o pacote StackExchange.Redis.StrongName não estiver presente em seu projeto, ele será instalado. Observe que além do pacote StackExchange.Redis.StrongName de nome forte, também há a versão do StackExchange.Redis sem nome forte. Se o seu projeto estiver usando a versão do StackExchange.Redis sem um nome forte, será necessário desinstalá-la, antes ou depois de instalar o pacote NuGet do Provedor de Cache de Saída Redis, caso contrário, você receberá conflitos de nomenclatura em seu projeto. Para saber mais sobre esses pacotes, consulte [Configurar clientes de cache .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

Os downloads de pacote NuGet acrescentam as referências de assembly necessárias e adicionam a seguinte seção dentro de seu arquivo web.config que contém a configuração solicitada para seu aplicativo ASP.NET a fim de usar o Provedor de Cache de Saída Redis.

    <caching>
      <outputCachedefault Provider="MyRedisOutputCache">
        <providers>
          <!--
          <add name="MyRedisOutputCache"
            host = "127.0.0.1" [String]
            port = "" [number]
            accessKey = "" [String]
            ssl = "false" [true|false]
            databaseId = "0" [number]
            applicationName = "" [String]
            connectionTimeoutInMilliseconds = "5000" [number]
            operationTimeoutInMilliseconds = "5000" [number]
          />
          -->
          <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider" host="127.0.0.1" accessKey="" ssl="false"/>
        </providers>
      </outputCache>
    </caching>

A seção comentada fornece um exemplo dos atributos e exemplos de configurações para cada atributo.

Configure os atributos com os valores de sua folha de cache no Portal do Microsoft Azure e configure os outros valores conforme o desejado. Para obter instruções sobre como acessar as propriedades do cache, consulte [Definir as configurações de cache Redis](cache-configure.md#configure-redis-cache-settings).

-	**host** – especifique o ponto de extremidade do cache.
-	**porta** – use sua porta SSL ou sua porta que não é do tipo SSL, dependendo das configurações de ssl.
-	**accessKey** – use a chave primária ou secundária de seu cache.
-	**ssl** – true se você quiser proteger as comunicações de cache/cliente com ssl; caso contrário, false. Especifique a porta correta.
	-	A porta não SSL é desabilitada por padrão para novos caches. Especifique true para essa configuração a fim de usar a porta SSL. Para saber mais sobre como habilitar a porta que não é do tipo SSL, confira a seção [Portas de acesso](cache-configure.md#access-ports) no tópico [Configurar um cache](cache-configure.md).
-	**databaseId** – especifica qual banco de dados usar para os dados de saída do cache. Se esse campo não for especificado, o valor padrão de 0 será usado.
-	**applicationName** – as chaves são armazenadas no redis como <NomedoAplicativo>\_<IDdaSessão>\_Data. Isso permite que vários aplicativos compartilhem a mesma chave. Esse parâmetro é opcional e, se você não fornecê-lo, um valor padrão será usado.
-	**connectionTimeoutInMilliseconds** – essa configuração permite a substituição da configuração de connectTimeout no cliente StackExchange.Redis. Se ela não for especificada, a configuração padrão do connectTimeout, 5000, será usada. Para saber mais, consulte [Modelo de configuração StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).
-	**operationTimeoutInMilliseconds** – essa configuração permite a substituição da configuração de syncTimeout no cliente StackExchange.Redis. Se ela não for especificada, a configuração padrão de syncTimeout, 1000, será usada. Para saber mais, consulte [Modelo de configuração StackExchange.Redis](http://go.microsoft.com/fwlink/?LinkId=398705).

Adicione uma diretiva OutputCache a cada página da qual você deseja armazenar a saída em cache.

    <%@ OutputCache Duration="60" VaryByParam="*" %>

Neste exemplo, os dados da página em cache permanecerão no cache por 60 segundos, e outra versão da página será armazenada em cache para cada combinação de parâmetros. Para saber mais sobre a diretiva OutputCache, consulte [@OutputCache](http://go.microsoft.com/fwlink/?linkid=320837).

Após a execução dessas etapas, seu aplicativo será configurado para usar o Provedor de Cache de Saída Redis.

## Próximas etapas

Confira [Provedor de estado de sessão ASP.NET para Cache Redis do Azure](cache-aspnet-session-state-provider.md)

<!---HONumber=AcomDC_0928_2016-->