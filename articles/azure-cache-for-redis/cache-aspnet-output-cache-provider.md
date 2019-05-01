---
title: Provedor de Cache de Saída ASP.NET para o Cache Redis do Azure
description: Saiba como armazenar em cache a saída de página ASP.NET usando o Cache Redis do Azure
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: 78469a66-0829-484f-8660-b2598ec60fbf
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 04/22/2018
ms.author: yegu
ms.openlocfilehash: a93d21b07dc486f743694ee99f60018ed4ef517c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943864"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>Provedor de Cache de Saída ASP.NET para o Cache Redis do Azure

O Provedor de Cache de Saída Redis é um mecanismo de armazenamento fora do processo para dados do cache de saída. Esses dados são usados especificamente para respostas HTTP completas (cache de saída de página). O provedor se conecta ao novo ponto de extensibilidade do provedor de cache de saída que foi apresentado no ASP.NET 4.

Para usar o Provedor de Cache de Saída Redis, primeiro configure seu cache e, em seguida, configure seu aplicativo ASP.NET usando o pacote NuGet do Provedor de Cache de Saída do Redis. Este tópico fornece informações sobre como configurar seu aplicativo para usar o Provedor de Cache de Saída Redis. Para saber mais sobre como criar e configurar uma instância do Cache Redis do Azure, consulte [Criar um cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

## <a name="store-aspnet-page-output-in-the-cache"></a>Armazenar a saída da página ASP.NET no cache

Para configurar um aplicativo cliente no Visual Studio usando o pacote NuGet do Estado de Sessão do Cache Redis do Azure, clique em **Gerenciador de Pacotes NuGet** e **Console do Gerenciador de Pacotes** no menu **Ferramentas**.

Execute o comando a seguir na janela `Package Manager Console`.

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Para usar o pacote NuGet do Provedor de Cache de Saída Redis, é necessário ter o pacote StackExchange.Redis.StrongName. Se o pacote StackExchange.Redis.StrongName não estiver presente em seu projeto, ele será instalado. Para obter mais informações sobre o pacote NuGet do Provedor de Cache de Saída Redis, consulte a página do NuGet [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/).

>[!NOTE]
>Além do pacote StackExchange.Redis.StrongName com nome forte, também há a versão do StackExchange.Redis sem nome forte. Se seu projeto está usando a versão de stackexchange. Redis sem nome forte, que você deve desinstalá-lo; Caso contrário, você terá conflitos de nomenclatura em seu projeto. Para saber mais sobre esses pacotes, consulte [Configurar clientes de cache .NET](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

O pacote do NuGet baixa e adiciona as referências de assembly necessárias e adiciona a seção a seguir ao seu arquivo web.config. Essa seção contém a configuração necessária para que seu aplicativo ASP.NET use o Provedor de Cache de Saída Redis.

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

Configure os atributos usando os valores da sua folha de cache no Portal do Microsoft Azure e defina os demais valores conforme sua preferência. Para obter instruções sobre como acessar as propriedades do cache, consulte [Definir configurações do Cache Redis do Azure](cache-configure.md#configure-azure-cache-for-redis-settings).

| Atributo | Type | Padrão | DESCRIÇÃO |
| --------- | ---- | ------- | ----------- |
| *host* | string | "localhost" | O nome de host ou endereço IP do servidor Redis |
| *port* | número inteiro positivo | 6379 (non-SSL)<br/>6380 (SSL) | Porta do servidor de redis |
| *accessKey* | string | "" | Senha do servidor do redis quando a autorização de Redis está habilitada. O valor é uma cadeia de caracteres vazia por padrão, o que significa que o provedor de estado de sessão não usa qualquer senha ao se conectar ao servidor Redis. **Se seu servidor do Redis estiver em uma rede acessível publicamente, como o Cache Redis do Azure, certifique-se de habilitar a autorização de Redis melhorar a segurança e forneça uma senha segura.** |
| *ssl* | boolean | **false** | Se deseja se conectar ao servidor de Redis via SSL. Esse valor é **falsos** por padrão porque o Redis não dá suporte a SSL fora da caixa. **Se você estiver usando o Cache Redis do Azure que dá suporte a SSL para uso imediato, certifique-se de definir esta opção para true para melhorar a segurança.**<br/><br/>A porta não SSL é desabilitada por padrão para novos caches. Especificar **verdadeira** para essa configuração para usar a porta SSL. Para mais informações sobre como habilitar a porta não SSL, confira a seção [Portas de acesso](cache-configure.md#access-ports) do tópico [Como configurar um cache](cache-configure.md). |
| *databaseIdNumber* | número inteiro positivo | 0 | *Esse atributo pode ser especificado somente por meio de Web. config ou AppSettings.*<br/><br/>Especifique qual banco de dados do Redis para usar. |
| *connectionTimeoutInMilliseconds* | número inteiro positivo | Fornecido pelo stackexchange. Redis | Usado para definir *ConnectTimeout* durante a criação de StackExchange.Redis.ConnectionMultiplexer. |
| *operationTimeoutInMilliseconds* | número inteiro positivo | Fornecido pelo stackexchange. Redis | Usado para definir *SyncTimeout* durante a criação de StackExchange.Redis.ConnectionMultiplexer. |
| *connectionString* (cadeia de caracteres de conexão válida stackexchange. Redis) | string | *n/a* | Qualquer uma referência de parâmetro para AppSettings ou Web. config, caso contrário, uma cadeia de conexão válida do stackexchange. Redis. Esse atributo pode fornecer valores para *host*, *porta*, *accessKey*, *ssl*e outros atributos do stackexchange. Redis. Para uma visão mais, veja *connectionString*, consulte [definindo connectionString](#setting-connectionstring) no [anotações de atributo](#attribute-notes) seção. |
| *settingsClassName*<br/>*settingsMethodName* | string<br/>string | *n/a* | *Esses atributos podem ser especificados apenas por meio de Web. config ou AppSettings.*<br/><br/>Use esses atributos para fornecer uma cadeia de caracteres de conexão. *settingsClassName* deve ser um nome de classe qualificado do assembly que contém o método especificado pela *settingsMethodName*.<br/><br/>O método especificado pela *settingsMethodName* devem ser públicos, estáticos e void (não use nenhum parâmetro), com um tipo de retorno **cadeia de caracteres**. Esse método retorna a cadeia de caracteres de conexão real. |
| *loggingClassName*<br/>*loggingMethodName* | string<br/>string | *n/a* | *Esses atributos podem ser especificados apenas por meio de Web. config ou AppSettings.*<br/><br/>Use esses atributos para depurar seu aplicativo, fornecendo os logs do Cache de estado de sessão e saída, juntamente com os logs do stackexchange. Redis. *loggingClassName* deve ser um nome de classe qualificado do assembly que contém o método especificado pela *loggingMethodName*.<br/><br/>O método especificado pela *loggingMethodName* devem ser públicos, estáticos e void (não use nenhum parâmetro), com um tipo de retorno **TextWriter**. |
| *applicationName* | string | O nome do módulo do processo atual ou "/" | *SessionStateProvider apenas*<br/>*Esse atributo pode ser especificado somente por meio de Web. config ou AppSettings.*<br/><br/>O prefixo do nome de aplicativo para usar em cache do Redis. O cliente pode usar o mesmo cache Redis para finalidades diferentes. Para garantir que as chaves de sessão não entrem em conflito, ele pode ser prefixado com o nome do aplicativo. |
| *throwOnError* | boolean | verdadeiro | *SessionStateProvider apenas*<br/>*Esse atributo pode ser especificado somente por meio de Web. config ou AppSettings.*<br/><br/>Se deve lançar uma exceção quando ocorre um erro.<br/><br/>Para obter mais informações sobre *throwOnError*, consulte [Observações sobre o *throwOnError* ](#notes-on-throwonerror) no [anotações de atributo](#attribute-notes) seção. |>*Microsoft.Web.Redis.RedisSessionStateProvider.LastException*. |
| *retryTimeoutInMilliseconds* | número inteiro positivo | 5.000 | *SessionStateProvider apenas*<br/>*Esse atributo pode ser especificado somente por meio de Web. config ou AppSettings.*<br/><br/>Quanto tempo para tentar novamente quando uma operação falhar. Se esse valor for menor que *operationTimeoutInMilliseconds*, o provedor não tentará novamente.<br/><br/>Para obter mais informações sobre *retryTimeoutInMilliseconds*, consulte [Observações sobre o *retryTimeoutInMilliseconds* ](#notes-on-retrytimeoutinmilliseconds) no [anotações de atributo](#attribute-notes) seção. |
| *redisSerializerType* | string | *n/a* | Especifica o nome de tipo qualificado do assembly de uma classe que implementa Microsoft.Web.Redis. ISerializer e que contém a lógica personalizada para serializar e desserializar os valores. Para obter mais informações, consulte [sobre *redisSerializerType* ](#about-redisserializertype) no [anotações de atributo](#attribute-notes) seção. |
|

## <a name="attribute-notes"></a>Anotações de atributo

### <a name="setting-connectionstring"></a>Definindo *connectionString*

O valor de *connectionString* é usado como chave para buscar a cadeia de caracteres de conexão real de AppSettings, se uma cadeia de caracteres existe no AppSettings. Se não for encontrado dentro de AppSettings, o valor de *connectionString* será usado como chave para buscar a cadeia de caracteres de conexão real da Web. config **ConnectionString** seção, se existir nessa seção. Se a cadeia de caracteres de conexão não existe no AppSettings ou Web. config **ConnectionString** seção, o valor literal da *connectionString* será usado como a cadeia de caracteres de conexão durante a criação StackExchange.Redis.ConnectionMultiplexer.

Os exemplos a seguir ilustram como *connectionString* é usado.

#### <a name="example-1"></a>Exemplo 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

No `web.config`, use acima chave como valor de parâmetro em vez do valor real.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-2"></a>Exemplo 2

```xml
<appSettings>
    <add key="MyRedisConnectionString" value="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</appSettings>
```

No `web.config`, use acima chave como valor de parâmetro em vez do valor real.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-3"></a>Exemplo 3

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False"/>
    </providers>
</sessionState>
```

### <a name="notes-on-throwonerror"></a>Observações sobre o *throwOnError*

Atualmente, se ocorrer um erro durante uma operação de sessão, o provedor de estado de sessão lançará uma exceção. Isso desliga o aplicativo.

Esse comportamento foi modificado de forma que suporta as expectativas dos usuários do provedor de estado da sessão do existentes ASP.NET enquanto também fornece a capacidade de agir em exceções, se desejado. O comportamento padrão ainda lança uma exceção quando ocorre um erro, consistente com outros provedores de estado de sessão do ASP.NET; código existente deverá funcionar os mesmos de antes.

Se você definir *throwOnError* à **falso**, em seguida, em vez de gerar uma exceção quando ocorre um erro, ele falhará silenciosamente. Para ver se houve um erro e, nesse caso, descubra qual era a exceção, verifique a propriedade estática *lastexception*.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>Observações sobre o *retryTimeoutInMilliseconds*

Isso fornece alguma lógica de repetição para simplificar o caso em que alguma operação de sessão deve tentar novamente em caso de falha devido a coisas como falha de rede, permitindo também que você controlar o tempo limite de repetição ou recusar inteiramente a nova tentativa.

Se você definir *retryTimeoutInMilliseconds* a um número, por exemplo 2000, em seguida, quando uma operação de sessão falhar, ele tentará novamente para 2.000 milissegundos antes de tratá-la como um erro. Portanto, para que o provedor de estado de sessão para aplicar a lógica de repetição, apenas configure o tempo limite. A primeira nova tentativa ocorrerá após 20 milissegundos, que é suficiente na maioria dos casos, quando ocorre uma falha de rede. Depois disso, ele tentará novamente a cada segundo até atingir o tempo limite. Logo após o tempo limite, ele tentará novamente mais uma vez para certificar-se de que ele não será cortado o tempo limite (no máximo) um segundo.

Se você achar necessário tentar novamente (por exemplo, quando você estiver executando o servidor Redis na mesma máquina que seu aplicativo) ou se você quiser manipular a lógica de repetição por conta própria, defini *retryTimeoutInMilliseconds* como 0.

### <a name="about-redisserializertype"></a>Sobre *redisSerializerType*

Por padrão, a serialização para armazenar os valores no Redis é feita em um formato binário fornecido pelo **BinaryFormatter** classe. Use *redisSerializerType* para especificar o nome de tipo qualificado do assembly de uma classe que implementa **Microsoft.Web.Redis.ISerializer** e tem a lógica personalizada para serializar e desserializar os valores. Por exemplo, aqui está uma classe de serializador de Json usando JSON.NET:

```cs
namespace MyCompany.Redis
{
    public class JsonSerializer : ISerializer
    {
        private static JsonSerializerSettings _settings = new JsonSerializerSettings() { TypeNameHandling = TypeNameHandling.All };

        public byte[] Serialize(object data)
        {
            return Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(data, _settings));
        }

        public object Deserialize(byte[] data)
        {
            if (data == null)6t6
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

Supondo que essa classe é definida em um assembly com nome **MyCompanyDll**, você pode definir o parâmetro *redisSerializerType* usá-lo:

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvider"
             name = "MySessionStateStore"
             redisSerializerType = "MyCompany.Redis.JsonSerializer,MyCompanyDll"
             ... />
    </providers>
</sessionState>
```

## <a name="output-cache-directive"></a>Diretiva de cache de saída

Adicione uma diretiva OutputCache a cada página da qual você deseja armazenar a saída em cache.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

No exemplo anterior, os dados da página em cache permaneceram no cache por 60 segundos, e outra versão da página foi armazenada em cache para cada combinação de parâmetros. Para saber mais sobre a diretiva OutputCache, consulte [@OutputCache](https://go.microsoft.com/fwlink/?linkid=320837).

Após a conclusão dessas etapas, seu aplicativo será configurado para usar o Provedor de Cache de Saída Redis.

## <a name="next-steps"></a>Próximas etapas

Consulte [Provedor de Estado de Sessão ASP.NET para o Cache Redis do Azure](cache-aspnet-session-state-provider.md).
