---
title: Comunicação de serviço com o Núcleo do ASP.NET | Microsoft Docs
description: Aprenda a usar o Núcleo do ASP.NET em Serviços Confiáveis com e sem estado.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 638c06e1854504dcb7ff34b1d9df56694556c421
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939788"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET Core no Azure Service Fabric Reliable Services

ASP.NET Core é uma estrutura de software livre e plataforma cruzada. Essa estrutura foi projetada para a criação de aplicativos baseados em nuvem, conectado à internet, como aplicativos web, aplicativos de IoT e back-ends móveis.

Este artigo é um guia detalhado para hospedagem de serviços do ASP.NET Core em serviços confiáveis do Service Fabric usando o **servicefabric.** conjunto de pacotes do NuGet.

Para obter um tutorial de Introdução no ASP.NET Core no Service Fabric e instruções sobre como configurar seu ambiente de desenvolvimento, consulte [Tutorial: Criar e implantar um aplicativo com um serviço de front-end de API Web ASP.NET Core e um serviço de back-end com estado](service-fabric-tutorial-create-dotnet-app.md).

O restante deste artigo pressupõe que você já esteja familiarizado com o ASP.NET Core. Se não estiver, leia as [conceitos básicos do ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core no ambiente do Service Fabric

Aplicativos ASP.NET Core e o Service Fabric podem ser executados no .NET Core ou .NET Framework completo. Você pode usar o ASP.NET Core de duas maneiras diferentes no Service Fabric:
 - **Hospedado como um executável convidado**. Dessa forma é usada principalmente para executar aplicativos existentes do ASP.NET Core no Service Fabric sem alterações no código.
 - **Executar em um serviço confiável**. Dessa forma permite uma melhor integração com o tempo de execução do Service Fabric e permite que os serviços com monitoração de estado ASP.NET Core.

O restante deste artigo explica como usar o ASP.NET Core em um serviço confiável, através dos componentes de integração do ASP.NET Core que vêm com o SDK do Service Fabric.

## <a name="service-fabric-service-hosting"></a>Hospedagem de serviços do Service Fabric

No Service Fabric, um ou mais instâncias de e/ou réplicas do serviço são executadas em uma *processo de host do serviço*: um arquivo executável que executa o código de serviço. Você, como um autor de serviço, possui o processo de host de serviço e do Service Fabric ativa e monitora para você.

O ASP.NET tradicional (até 5 MVC) está estreitamente relacionado ao IIS por meio de System.Web.dll. O Núcleo do ASP.NET fornece uma separação entre o servidor Web e o aplicativo Web. Essa separação permite que os aplicativos da web para ser portátil entre diferentes servidores web. Ele também permite que servidores web fiquem *auto-hospedado*. Isso significa que você pode iniciar um servidor web em seu próprio processo, em vez de um processo que pertence a software de servidor web dedicado, como o IIS.

Para combinar um serviço do Service Fabric e ASP.NET, como um executável convidado ou em um serviço confiável, você deve ser capaz de iniciar o ASP.NET no seu processo de host de serviço. A hospedagem interna do Núcleo do ASP.NET permite que você faça isso.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hospedando o núcleo do ASP.NET em um serviço confiável
Normalmente, aplicativos do Núcleo do ASP.NET auto-hospedados criam um WebHost no ponto de entrada do aplicativo, como o método `static void Main()` em `Program.cs`. Nesse caso, o ciclo de vida do WebHost está associado ao ciclo de vida do processo.

![Hospedando o Núcleo do ASP.NET em um processo][0]

Mas o ponto de entrada do aplicativo não é o lugar certo para criar um WebHost em um serviço confiável. Isso ocorre porque o ponto de entrada do aplicativo só é usado para registrar um tipo de serviço com o tempo de execução do Service Fabric, para que ele possa criar instâncias desse tipo de serviço. O WebHost deve ser criado em um serviço confiável em si. Dentro do processo de host de serviço, instâncias de serviço e/ou réplicas podem passar por vários ciclos de vida. 

Uma instância de um serviço confiável é representada por sua classe de serviço derivando de `StatelessService` ou `StatefulService`. A pilha de comunicação para um serviço está contida em uma implementação `ICommunicationListener` em sua classe de serviço. O `Microsoft.ServiceFabric.AspNetCore.*` os pacotes NuGet contém implementações de `ICommunicationListener` que iniciam e gerenciam o WebHost de núcleo do ASP.NET para Kestrel ou HTTP. sys em um serviço confiável.

![Diagrama para hospedar o ASP.NET Core em um serviço confiável][1]

## <a name="aspnet-core-icommunicationlisteners"></a>Núcleo do ASP.NET ICommunicationListeners
O `ICommunicationListener` implementações para Kestrel e HTTP. sys no `Microsoft.ServiceFabric.AspNetCore.*` pacotes do NuGet têm padrões de uso semelhantes. Mas eles executam ações específicas ligeiramente diferentes para cada servidor web. 

Ambos os ouvintes de comunicação fornecem um construtor que usa os seguintes argumentos:
 - **`ServiceContext serviceContext`**: Esse é o `ServiceContext` objeto que contém informações sobre o serviço em execução.
 - **`string endpointName`**: Esse é o nome de um `Endpoint` configuração em servicemanifest. XML. É principalmente onde os dois ouvintes de comunicação são diferentes. O HTTP. sys *requer* um `Endpoint` configuração, enquanto o Kestrel não.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: Isso é uma expressão lambda que você implementa, em que você cria e retorna um `IWebHost`. Ele permite que você configure `IWebHost` da maneira como faria normalmente em um aplicativo ASP.NET Core. O lambda fornece uma URL que é gerada para você, dependendo das opções de integração do Service Fabric que você usar e o `Endpoint` configuração que você fornece. Em seguida, você pode modificar ou usar essa URL para iniciar o servidor web.

## <a name="service-fabric-integration-middleware"></a>Middleware de integração do Service Fabric
O `Microsoft.ServiceFabric.AspNetCore` pacote NuGet inclui o `UseServiceFabricIntegration` método de extensão em `IWebHostBuilder` que adiciona o middleware compatíveis com o Service Fabric. Esse middleware configura o Kestrel ou HTTP. sys `ICommunicationListener` para registrar uma URL de serviço exclusivo com o serviço de nomenclatura do Service Fabric. Ele valida solicitações do cliente para garantir que os clientes estão se conectando ao serviço certo. 

Essa etapa é necessária para impedir que os clientes conectem ao serviço errado por engano. Isso ocorre porque, em um ambiente de host compartilhado como o Service Fabric, vários aplicativos web podem ser executados na mesma máquina física ou virtual, mas não usam nomes de host exclusivo. Esse cenário é descrito em mais detalhes na próxima seção.

### <a name="a-case-of-mistaken-identity"></a>Um caso de identidade incorreta
Independentemente do protocolo, as réplicas de serviço escutam em uma combinação de IP:porta exclusiva. Depois que uma réplica de serviço começa a escutar em um ponto de extremidade IP: port, ele relata esse endereço de ponto de extremidade para o serviço de nomenclatura do Service Fabric. Lá, os clientes ou outros serviços podem descobri-lo. Se os serviços usarem portas de aplicativo atribuído dinamicamente, uma réplica de serviço Coincidentemente pode usar o mesmo ponto de extremidade IP: port de outro serviço anteriormente no mesmo físico ou máquina virtual. Isso pode fazer com que um cliente se conecte por engano ao serviço incorreto. Esse cenário pode resultar se a seguinte sequência de eventos ocorrer:

 1. O serviço A escuta em 10.0.0.1:30000 via HTTP. 
 2. Cliente resolve o serviço A e obtém o endereço 10.0.0.1: 30000.
 3. O Serviço A move-se para um nó diferente.
 4. O serviço B é colocado em 10.0.0.1 e usa coincidentemente a mesma porta 30000.
 5. O cliente tenta se conectar ao serviço A com o endereço de cache 10.0.0.1:30000.
 6. Cliente agora está conectado ao serviço B, sem perceber que está conectado ao serviço errado.

Isso pode causar bugs em momentos aleatórios, que podem ser difíceis de diagnosticar.

### <a name="using-unique-service-urls"></a>Usando URLs de serviço exclusivas
Para impedir que esses bugs, serviços podem postar um ponto de extremidade para o serviço de nomenclatura com um identificador exclusivo e, em seguida, validar esse identificador exclusivo durante as solicitações do cliente. Essa é uma ação cooperativa entre serviços em um ambiente confiável de locatário não hostil. Ele não fornece autenticação segura em um ambiente de locatário hostil.

Em um ambiente confiável, o middleware adicionado pelo `UseServiceFabricIntegration` método anexa automaticamente um identificador exclusivo para o endereço lançado para o serviço de nomenclatura. Ele valida esse identificador em cada solicitação. Se o identificador não corresponder, o middleware retornará imediatamente uma resposta HTTP 410 não existe mais.

Os serviços que usam uma porta atribuída dinamicamente devem fazer uso desse middleware.

Os serviços que usam uma porta exclusiva fixa não têm esse problema em um ambiente cooperativo. Uma porta fixa é normalmente usada para serviços voltados para o exterior que exigem uma porta conhecida para a conexão de aplicativos clientes. Por exemplo, a maioria dos aplicativos da web para a internet usará a porta 80 ou 443 para conexões do navegador da web. Nesse caso, o identificador exclusivo não deve ser habilitado.

O diagrama a seguir mostra o fluxo de solicitação com o middleware habilitado:

![Integração de núcleo do ASP.NET do Service Fabric][2]

Kestrel e HTTP. sys `ICommunicationListener` implementações usam esse mecanismo exatamente da mesma maneira. Embora o HTTP. sys possa diferenciar internamente as solicitações com base em caminhos de URL exclusivos usando subjacente **HTTP. sys** recurso, essa funcionalidade de compartilhamento de porta *não* usado pelo HTTP. sys `ICommunicationListener`implementação. Isso ocorre porque ele resulta em códigos de status de erro HTTP 503 e HTTP 404 no cenário descrito anteriormente. Que por sua vez torna difícil para os clientes determinar a intenção do erro, pois HTTP 503 e HTTP 404 normalmente são usados para indicar outros erros. 

Assim, tanto o Kestrel e HTTP. sys `ICommunicationListener` implementações padronizarem o middleware fornecido pelo `UseServiceFabricIntegration` método de extensão. Portanto, os clientes só precisem executar uma ação de resolver novamente de ponto de extremidade de serviço em respostas HTTP 410.

## <a name="httpsys-in-reliable-services"></a>O HTTP. sys em serviços confiáveis
Você pode usar o HTTP. sys em serviços confiáveis, importando o **httpsys** pacote do NuGet. Este pacote contém `HttpSysCommunicationListener`, uma implementação de `ICommunicationListener`. `HttpSysCommunicationListener` permite que você crie um WebHost de núcleo do ASP.NET em um serviço confiável usando HTTP. sys, como o servidor web.

O HTTP. sys se baseia a [Windows HTTP Server API](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Essa API usa o **HTTP. sys** driver de kernel para processar solicitações HTTP e roteá-las para processos que executam aplicativos da web. Isso permite que vários processos na mesma físico ou máquina virtual para hospedar aplicativos web na mesma porta, sem ambiguidade graças a um exclusivo URL caminho ou nome do host. Esses recursos são úteis no Service Fabric para hospedar vários sites no mesmo cluster.

>[!NOTE]
>Implementação de HTTP. sys funciona apenas na plataforma Windows.

O diagrama a seguir ilustra como o HTTP. sys usa o **HTTP. sys** driver de kernel no Windows para o compartilhamento de porta:

![Diagrama de HTTP. sys][3]

### <a name="httpsys-in-a-stateless-service"></a>O HTTP. sys em um serviço sem monitoração de estado
Para usar `HttpSys` em um serviço sem estado, substitua o método `CreateServiceInstanceListeners` e retorne uma instância `HttpSysCommunicationListener`:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseHttpSys()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="httpsys-in-a-stateful-service"></a>O HTTP. sys em um serviço com estado

`HttpSysCommunicationListener` Atualmente, não é projetado para uso em serviços com estado devido a complicações com subjacente **HTTP. sys** recurso de compartilhamento de porta. Para obter mais informações, consulte a seção a seguir sobre a alocação de porta dinâmica com HTTP. sys. Para serviços com monitoração de estado, o Kestrel é o servidor web sugeridas.

### <a name="endpoint-configuration"></a>Configuração de ponto de extremidade

Um `Endpoint` configuração é necessária para servidores web que usam a API do Windows HTTP Server, incluindo http. sys. Servidores Web que usam a API do Windows HTTP Server primeiro devem reservar a URL com HTTP. sys (Isso normalmente é feito com o [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) ferramenta). 

Esta ação exige privilégios elevados que seus serviços não têm por padrão. As opções "http" ou "https" para o `Protocol` propriedade do `Endpoint` configuração em servicemanifest. XML são usadas especificamente para instruir o tempo de execução do Service Fabric para registrar uma URL HTTP. sys em seu nome. Ele faz isso usando o [ *curinga forte* ](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) prefixo da URL.

Por exemplo, para reservar `http://+:80` para um serviço, use a seguinte configuração em servicemanifest. XML:

```xml
<ServiceManifest ... >
    ...
    <Resources>
        <Endpoints>
            <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>

</ServiceManifest>
```

E o nome do ponto de extremidade deve ser passado para o construtor `HttpSysCommunicationListener`:

```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseHttpSys()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-httpsys-with-a-static-port"></a>Usar o HTTP. sys com uma porta estática
Para usar uma porta estática com HTTP. sys, forneça o número da porta a `Endpoint` configuração:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Usar o HTTP. sys com uma porta dinâmica
Para usar uma porta atribuída dinamicamente com HTTP. sys, omita a `Port` propriedade no `Endpoint` configuração:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Uma porta dinâmica alocada por um `Endpoint` configuração fornece apenas uma porta *por processo de host*. O modelo de hospedagem do Service Fabric atual permite que várias instâncias de serviço e/ou réplicas sejam hospedadas no mesmo processo. Isso significa que cada uma delas compartilhará a mesma porta quando alocados por meio de `Endpoint` configuração. Vários **HTTP. sys** instâncias podem compartilhar uma porta com o uso subjacente **HTTP. sys** recurso de compartilhamento de porta. Mas ele não é suportado pelo `HttpSysCommunicationListener` devido às complicações introduzidas para solicitações do cliente. Para o uso de portas dinâmicas, o Kestrel é o servidor web sugeridas.

## <a name="kestrel-in-reliable-services"></a>Kestrel em Serviços Confiáveis
Você pode usar o Kestrel em serviços confiáveis, importando o **servicefabric** pacote do NuGet. Este pacote contém `KestrelCommunicationListener`, uma implementação de `ICommunicationListener`. `KestrelCommunicationListener` permite que você crie um WebHost de núcleo do ASP.NET em um serviço confiável usando o Kestrel como o servidor web.

O Kestrel é um servidor Web de plataforma cruzada para o Núcleo do ASP.NET com base em libuv, uma biblioteca de E/S assíncrona de plataforma cruzada. Ao contrário do HTTP. sys, o Kestrel não usa um Gerenciador de ponto de extremidade centralizado. Também ao contrário do HTTP. sys, Kestrel não dá suporte ao compartilhamento de porta entre vários processos. Cada instância do Kestrel deve usar uma porta exclusiva.

![Diagrama do kestrel][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel em um serviço sem estado
Para usar `Kestrel` em um serviço sem estado, substitua o método `CreateServiceInstanceListeners` e retorne uma instância `KestrelCommunicationListener`:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

### <a name="kestrel-in-a-stateful-service"></a>Kestrel em um serviço com estado
Para usar `Kestrel` em um serviço com estado, substitua o método `CreateServiceReplicaListeners` e retorne uma instância `KestrelCommunicationListener`:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new ServiceReplicaListener[]
    {
        new ServiceReplicaListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                         services => services
                             .AddSingleton<StatefulServiceContext>(serviceContext)
                             .AddSingleton<IReliableStateManager>(this.StateManager))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

Neste exemplo, uma instância única de `IReliableStateManager` é fornecida ao contêiner de injeção de dependência do WebHost. Isso não é estritamente necessário, mas permite que você use `IReliableStateManager` e coleções confiáveis em seus métodos de ação do controlador MVC.

Um nome de configuração `Endpoint` *não* é fornecido para `KestrelCommunicationListener` em um serviço com estado. Isso será explicado mais detalhadamente na seção a seguir.

### <a name="configure-kestrel-to-use-https"></a>Configurar o Kestrel para usar HTTPS
Ao habilitar o HTTPS com o Kestrel em seu serviço, você precisará definir várias opções de escuta. Atualizar o `ServiceInstanceListener` usar um *EndpointHttps* ponto de extremidade e escutar em uma porta específica (como a porta 443). Ao configurar o host da web para usar o servidor web Kestrel, você deve configurar o Kestrel para escutar para endereços IPv6 em todas as interfaces de rede: 

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

Para obter um exemplo completo em um tutorial, consulte [configurar o Kestrel para usar HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Configuração de ponto de extremidade
Um `Endpoint` configuração não é necessária para usar o Kestrel. 

O kestrel é um servidor da web autônomo simples. Ao contrário do HTTP. sys (ou HttpListener), ele não precisa de um `Endpoint` configuração em servicemanifest. XML porque ele não requer o registro de URL antes de iniciar. 

#### <a name="use-kestrel-with-a-static-port"></a>Usar Kestrel com uma porta estática
Você pode configurar uma porta estática no `Endpoint` configuração do servicemanifest. XML para uso com Kestrel. Embora não seja estritamente necessário, ele oferece dois benefícios potenciais:
 - Se a porta não se enquadra no intervalo de porta do aplicativo, ele é aberto por meio do firewall do sistema operacional pelo Service Fabric.
 - A URL fornecida através de `KestrelCommunicationListener` usará essa porta.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Se um `Endpoint` for configurado, o nome deverá ser passado para o construtor `KestrelCommunicationListener`: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Se o servicemanifest. XML não usa um `Endpoint` configuração, omita o nome no `KestrelCommunicationListener` construtor. Nesse caso, ele usará uma porta dinâmica. Consulte a próxima seção para obter mais informações sobre isso.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Usar Kestrel com uma porta dinâmica
O kestrel não é possível usar a atribuição automática de porta do `Endpoint` configuração em servicemanifest. XML. Isso ocorre porque automática portar a atribuição de um `Endpoint` configuração atribui uma porta exclusiva por *processo de host*, e um processo de host único pode conter várias instâncias do Kestrel. Isso não funciona com o Kestrel porque ele não dá suporte a compartilhamento de porta. Portanto, cada instância do Kestrel deve ser aberta em uma porta exclusiva.

Para usar a atribuição de porta dinâmica com o Kestrel, omita a `Endpoint` configuração em servicemanifest. XML totalmente e não passar um nome de ponto de extremidade para o `KestrelCommunicationListener` construtor, da seguinte maneira:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

Nessa configuração, `KestrelCommunicationListener` selecionará automaticamente uma porta não utilizada do intervalo de portas de aplicativo.

## <a name="service-fabric-configuration-provider"></a>Provedor de configuração do Service Fabric
Configuração de aplicativo no ASP.NET Core baseia-se em pares chave-valor estabelecidas pelo provedor de configuração. Leia [configuração no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/) para entender mais sobre ASP.NET Core configuração suporte geral.

Esta seção descreve como o provedor de configuração do Service Fabric integra-se com a configuração do ASP.NET Core, importando o `Microsoft.ServiceFabric.AspNetCore.Configuration` pacote do NuGet.

### <a name="addservicefabricconfiguration-startup-extensions"></a>Extensões de inicialização AddServiceFabricConfiguration
Depois de importar o `Microsoft.ServiceFabric.AspNetCore.Configuration` pacote do NuGet, você precisa registrar a origem de configuração do Service Fabric com a API de configuração do ASP.NET Core. Você pode fazer isso verificando **AddServiceFabricConfiguration** extensões na `Microsoft.ServiceFabric.AspNetCore.Configuration` namespace contra `IConfigurationBuilder`.

```csharp
using Microsoft.ServiceFabric.AspNetCore.Configuration;

public Startup(IHostingEnvironment env)
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(env.ContentRootPath)
        .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
        .AddServiceFabricConfiguration() // Add Service Fabric configuration settings.
        .AddEnvironmentVariables();
    Configuration = builder.Build();
}

public IConfigurationRoot Configuration { get; }
```

Agora, o serviço ASP.NET Core pode acessar as definições de configuração do Service Fabric, assim como outras configurações de aplicativo. Por exemplo, você pode usar o padrão de opções para carregar configurações em objetos fortemente tipados.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Mapeamento de chaves padrão
Por padrão, o provedor de configuração do Service Fabric inclui o nome do pacote, o nome da seção e o nome da propriedade. Juntas, elas formam a chave de configuração do ASP.NET Core, da seguinte maneira:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Por exemplo, se você tiver um pacote de configuração denominado `MyConfigPackage` com o conteúdo a seguir, em seguida, o valor de configuração estarão disponível no ASP.NET Core `IConfiguration` por meio de *MyConfigPackage:MyConfigSection:MyParameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Opções de configuração do Service Fabric
O provedor de configuração do Service Fabric também dá suporte a `ServiceFabricConfigurationOptions` para alterar o comportamento padrão de mapeamento de chaves.

#### <a name="encrypted-settings"></a>Configurações criptografadas
O Service Fabric dá suporte a configurações criptografadas, assim como o provedor de configuração do Service Fabric. As configurações criptografadas não são descriptografadas para o ASP.NET Core `IConfiguration` por padrão. Os valores criptografados são armazenados lá em vez disso. Mas se você quiser descriptografar o valor a ser armazenado no objeto IConfiguration do ASP.NET Core, você pode definir as *DecryptValue* sinalizador como false no `AddServiceFabricConfiguration` extensão, da seguinte maneira:

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = true); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>Vários pacotes de configuração
O Service Fabric dá suporte a vários pacotes de configuração. Por padrão, o nome do pacote está incluído na chave de configuração. Mas você pode definir o `IncludePackageName` sinalizador como false, da seguinte maneira:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        // exclude package name from key.
        .AddServiceFabricConfiguration(activationContext, (options) => options.IncludePackageName = false); 
    Configuration = builder.Build();
}
```
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Mapeamento de chaves personalizado, extração de valor e preenchimento dos dados
O provedor de configuração do Service Fabric também dá suporte a cenários mais avançados para personalizar o mapeamento de chaves com `ExtractKeyFunc` e extrair os valores com personalizado- `ExtractValueFunc`. Você pode até mesmo alterar todo o processo de popular os dados de configuração do Service Fabric para configuração do ASP.NET Core usando `ConfigAction`.

Os exemplos a seguir ilustram como usar `ConfigAction` para personalizar o preenchimento dos dados:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    
    this.valueCount = 0;
    this.sectionCount = 0;
    var builder = new ConfigurationBuilder();
    builder.AddServiceFabricConfiguration(activationContext, (options) =>
        {
            options.ConfigAction = (package, configData) =>
            {
                ILogger logger = new ConsoleLogger("Test", null, false);
                logger.LogInformation($"Config Update for package {package.Path} started");

                foreach (var section in package.Settings.Sections)
                {
                    this.sectionCount++;

                    foreach (var param in section.Parameters)
                    {
                        configData[options.ExtractKeyFunc(section, param)] = options.ExtractValueFunc(section, param);
                        this.valueCount++;
                    }
                }

                logger.LogInformation($"Config Update for package {package.Path} finished");
            };
        });
  Configuration = builder.Build();
}
```

### <a name="configuration-updates"></a>Atualizações de configuração
O provedor de configuração do Service Fabric também dá suporte a atualizações de configuração. Você pode usar o ASP.NET Core `IOptionsMonitor` para receber notificações de alteração e, em seguida, usar `IOptionsSnapshot` para recarregar os dados de configuração. Para obter mais informações, consulte [opções do ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

Essas opções têm suporte por padrão. Nenhuma outra codificação é necessária para habilitar atualizações de configuração.

## <a name="scenarios-and-configurations"></a>Cenários e configurações
Esta seção fornece a combinação de servidor web, configuração de porta, as opções de integração do Service Fabric e outras configurações, que é recomendável para solucionar problemas de cenários a seguir:
 - Serviços sem monitoração de estado do Núcleo do ASP.NET expostos externamente
 - Serviços sem monitoração de estado de somente para uso interno do ASP.NET Core
 - Serviços com monitoração de estado de somente para uso interno do ASP.NET Core

Uma **exposto externamente serviço** é aquele que expõe um ponto de extremidade que é chamado de fora do cluster, geralmente por meio de um balanceador de carga.

Uma **somente interno** de serviço é um cujo ponto de extremidade é chamado apenas de dentro do cluster.

> [!NOTE]
> Pontos de extremidade de serviço com estado geralmente não devem ser expostos à internet. Clusters por trás de balanceadores de carga que não estão cientes da resolução de serviço do Service Fabric, como o Azure Load Balancer, será possível expor serviços com monitoração de estado. Isso ocorre porque o balanceador de carga não será capaz de localizar e rotear o tráfego para a réplica de serviço com monitoração de estado apropriado. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Serviços sem monitoração de estado do Núcleo do ASP.NET expostos externamente
O kestrel é o servidor web sugeridas para os serviços de front-end que expõem pontos de extremidade HTTP externos, para a internet. No Windows, o HTTP. sys pode fornecer funcionalidade de compartilhamento de porta, que permite hospedar vários serviços web no mesmo conjunto de nós usando a mesma porta. Nesse cenário, os serviços web são diferenciados por nome de host ou o caminho, sem depender de um proxy front-end ou um gateway para fornecer roteamento HTTP.
 
Quando exposto à internet, um serviço sem estado deve usar um ponto de extremidade conhecido e estável que seja acessível por meio de um balanceador de carga. Você vai fornecer essa URL para usuários do seu aplicativo. Recomendamos a seguinte configuração:

|  |  | **Observações** |
| --- | --- | --- |
| Servidor Web | Kestrel | O kestrel é o servidor web preferencial, pois ele tem suporte no Windows e Linux. |
| Configuração de portas | estático | Uma porta estática conhecida deve ser configurada na configuração do `Endpoints` de ServiceManifest.XML, como 80 para HTTP ou 443 para HTTPS. |
| ServiceFabricIntegrationOptions | Nenhum | Use o `ServiceFabricIntegrationOptions.None` opção ao configurar o middleware de integração do Service Fabric, para que o serviço não tenta validar solicitações de entrada para um identificador exclusivo. Os usuários externos do seu aplicativo não saberão as informações de identificação exclusivas que usa o middleware. |
| Contagem de Instâncias | -1 | Em casos de uso típicos, a configuração de contagem de instância deve ser definida como *-1*. Isso é feito para que uma instância está disponível em todos os nós que recebem tráfego de um balanceador de carga. |

Se vários serviços expostos externamente compartilharem o mesmo conjunto de nós, você pode usar o HTTP. sys com um caminho de URL exclusivo e estável. Você pode fazer isso modificando a URL fornecida ao configurar IWebHost. Observe que isso se aplica somente ao HTTP. sys.

 ```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseHttpSys()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>Serviço do Núcleo do ASP.NET sem estado somente para uso interno
Os serviços sem monitoração de estado que são chamados apenas de dentro do cluster devem usar URLs exclusivas e portas atribuídas dinamicamente para garantir a cooperação entre vários serviços. Recomendamos a seguinte configuração:

|  |  | **Observações** |
| --- | --- | --- |
| Servidor Web | Kestrel | Embora você possa usar HTTP. sys para serviços sem monitoração de estado internos, o Kestrel é o melhor servidor para permitir que várias instâncias do serviço compartilhem um host.  |
| Configuração de portas | atribuídas dinamicamente | Várias réplicas de um serviço com estado podem compartilhar um processo de host ou o sistema operacional do host e, portanto, serão necessário portas exclusivas. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Com a atribuição dinâmica de portas essa configuração impede o problema de confusão de identidade descrito anteriormente. |
| InstanceCount | qualquer | A configuração de contagem de instâncias pode ser definida como qualquer valor necessário para operar o serviço. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Somente interno serviço com estado do Núcleo do ASP.NET
Os serviços com monitoração de estado que são chamados apenas de dentro do cluster devem usar portas atribuídas dinamicamente para garantir a cooperação entre vários serviços. Recomendamos a seguinte configuração:

|  |  | **Observações** |
| --- | --- | --- |
| Servidor Web | Kestrel | O `HttpSysCommunicationListener` não foi projetado para uso pelos serviços com monitoração de estado em que réplicas compartilham um processo de host. |
| Configuração de portas | atribuídas dinamicamente | Várias réplicas de um serviço com estado podem compartilhar um processo de host ou o sistema operacional do host e, portanto, serão necessário portas exclusivas. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Com a atribuição dinâmica de portas essa configuração impede o problema de confusão de identidade descrito anteriormente. |

## <a name="next-steps"></a>Próximas etapas
[Depurar seu aplicativo do Service Fabric usando o Visual Studio](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
