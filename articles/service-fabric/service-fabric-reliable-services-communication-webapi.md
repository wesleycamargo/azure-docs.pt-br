<properties
   pageTitle="Comunicação de serviço com a API Web ASP.NET | Microsoft Azure"
   description="Saiba como implementar passo a passo a comunicação de serviço usando a API Web ASP.NET com auto-hospedagem OWIN na API dos Reliable Services."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="03/25/2016"
   ms.author="vturecek"/>

# Introdução aos serviços de API Web do Service Fabric com auto-hospedagem OWIN

Com o Service Fabric, você decide como deseja que seus serviços se comuniquem com os usuários e entre si. Este tutorial se concentra na implementação da comunicação de serviço usando a API Web ASP.NET com auto-hospedagem OWIN (Open Web Interface for .NET) na API dos Reliable Services do Service Fabric. Vamos abordar detalhadamente a API de comunicação conectável dos Reliable Services. Também usaremos a API Web em um exemplo passo a passo para mostrar a você como configurar um ouvinte de comunicação personalizado.


## Introdução às APIs Web no Service Fabric

O API Web ASP.NET é uma estrutura popular e poderosa para criar APIs HTTP sobre o .NET Framework. Se você não ainda estiver familiarizado com a estrutura, confira [Getting started with ASP.NET Web API 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) para saber mais.

A API Web na Malha de Serviço é o mesma API Web ASP.NET que você conhece e adora. A diferença está em como você *hospeda* um aplicativo de API Web. Você não usará o IIS (Serviços de Informações da Internet) da Microsoft. Para compreender melhor a diferença, vamos dividir isso em duas partes:

 1. O aplicativo de API Web (incluindo controladores e modelos)
 2. O host (o servidor web, geralmente IIS)

O aplicativo de API Web, em si, não muda. Ele não é diferente dos aplicativos de API Web que você possa ter desenvolvido no passado, e você poderá simplesmente mover a maioria dos códigos do aplicativo diretamente. Mas, se você usava a hospedagem no IIS, onde você hospedará o aplicativo pode ser um pouco diferente do que você estava acostumado. Antes de adentrarmos na parte de hospedagem, vamos começar com algo mais familiar: o aplicativo de API Web.


## Criar o aplicativo

Comece criando um novo aplicativo do Service Fabric com um único serviço sem estado no Visual Studio 2015:

![Criar um novo aplicativo da Malha de Serviço](media/service-fabric-reliable-services-communication-webapi/webapi-newproject.png)

Existe um modelo do Visual Studio para um serviço sem estado usando a API Web à sua disposição. Neste tutorial, vamos criar um projeto que resulta no que você obteria se selecionasse esse modelo. Aqui, você pode começar com a API Web do serviço sem estado e seguir com ela ou começar com um serviço sem estado vazio e criar do zero.

![Criar um único serviço sem estado](media/service-fabric-reliable-services-communication-webapi/webapi-newproject2.png)

A primeira etapa é obter alguns pacotes NuGet para o API Web. O pacote que queremos usar é o Microsoft.AspNet.WebApi.OwinSelfHost. Esse pacote inclui todos os pacotes de API Web necessários e os pacotes de *hospedagem*. Isso será importante mais tarde.

![Criar a API Web usando o Gerenciador de Pacotes NuGet](media/service-fabric-reliable-services-communication-webapi/webapi-nuget.png)

Depois que os pacotes tiverem sido instalados, você poderá começar a criar a estrutura do projeto básico de API Web. Se você já usou uma API Web, a estrutura do projeto deverá ser bastante familiar. Comece adicionando um diretório `Controllers` e um controlador de valores simples:

**ValuesController.cs**

```csharp
using System.Collections.Generic;
using System.Web.Http;
    
namespace WebService.Controllers
{
    public class ValuesController : ApiController
    {
        // GET api/values 
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }

        // GET api/values/5 
        public string Get(int id)
        {
            return "value";
        }

        // POST api/values 
        public void Post([FromBody]string value)
        {
        }

        // PUT api/values/5 
        public void Put(int id, [FromBody]string value)
        {
        }

        // DELETE api/values/5 
        public void Delete(int id)
        {
        }
    }
}

```

Em seguida, adicione uma classe de Inicialização à raiz do projeto para registrar o roteamento, formatadores e qualquer outra configuração. Também é aqui onde a API Web se conecta ao *host*, que será revisto novamente mais tarde.

**Startup.cs**

```csharp
using System.Web.Http;
using Owin;

namespace WebService
{
    public static class Startup
    {
        public static void ConfigureApp(IAppBuilder appBuilder)
        {
            // Configure Web API for self-host. 
            HttpConfiguration config = new HttpConfiguration();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );

            appBuilder.UseWebApi(config);
        }
    }
}
```

Isso é tudo para a parte do aplicativo. Nesse ponto, configuramos apenas o layout do projeto básico de API Web. Até agora, esse processo não parece muito diferente dos projetos de API Web que você possa ter desenvolvido no passado ou do modelo básico de API Web. A lógica de negócios vai para os controladores e modelos como de costume.

Agora o que fazemos com a hospedagem para que possamos executá-la de fato?

## Hospedagem do serviço

No Service Fabric, seu serviço é executado em um *processo de host do serviço*, um arquivo executável que executa o código do serviço. Quando você escreve um serviço usando a API dos Reliable Services, seu projeto de serviço é compilado em um arquivo executável que registra o tipo de serviço e executa o código. Isso se aplica à maioria dos casos em que você escreve um serviço no Service Fabric no .NET. Ao abrir o Program.cs no projeto de serviço sem estado, você deverá ver:

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric.Services.Runtime;

internal static class Program
{
    private static void Main()
    {
        try
        {
            ServiceRuntime.RegisterServiceAsync("WebServiceType",
                context => new WebService(context)).GetAwaiter().GetResult();

            ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(WebService).Name);

            // Prevents this host process from terminating so services keeps running. 
            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

Se parecer anormalmente o ponto de entrada para um aplicativo de console, é porque é.

Neste artigo, não entraremos em mais detalhes sobre o processo de host do serviço e o registro do serviço. Mas é importante saber, por enquanto, que *o código de serviço está em execução em seu próprio processo*.

## Auto-hospedar a API Web com um host OWIN

Uma vez que o código do aplicativo de API Web está hospedado em seu próprio processo, como você o conecta a um servidor Web? Digite [OWIN](http://owin.org/). OWIN é simplesmente um contrato entre aplicativos web do .NET e servidores web. Normalmente, quando o ASP.NET (até o MVC 5) é usado, o aplicativo Web é rigidamente associado ao IIS por meio do System.Web. No entanto, a API Web implementa o OWIN para que você possa escrever um aplicativo Web que seja dissociado do servidor Web que o hospeda. Por isso, você pode usar um servidor Web OWIN *auto-hospedado* que pode ser iniciado em seu próprio processo. Isso se ajusta perfeitamente com o modelo de hospedagem do Service Fabric que acabamos de descrever.

Neste artigo, vamos usar Katana como host OWIN para o aplicativo API Web. Katana é uma implementação de host OWIN de código aberto.

> [AZURE.NOTE] Para saber mais sobre o Katana, acesse o [site do Katana](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana). Para obter uma visão geral rápida de como usar o Katana para a auto-hospedagem da API Web, confira [Use OWIN to Self-Host ASP.NET Web API 2](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api).


## Configuração do servidor Web

A API dos Reliable Services fornece um ponto de entrada de comunicação no qual você pode conectar pilhas de comunicação que permitem aos usuários e clientes se conectarem ao serviço:

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}

```

O servidor Web (e qualquer outra pilha de comunicação que você possa usar no futuro, como WebSockets) deve usar a interface ICommunicationListener para se integrar corretamente ao sistema. As razões para isso ficarão mais evidentes nas etapas a seguir.

Primeiro, crie uma classe chamada OwinCommunicationListener que implementa ICommunicationListener:

**OwinCommunicationListener.cs**

```csharp
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;
using System;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;

namespace WebService
{
    public class OwinCommunicationListener : ICommunicationListener
    {
        public void Abort()
        {
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
        }
    }
}
```

A interface ICommunicationListener fornece três métodos para gerenciar um ouvinte de comunicação para seu serviço:

 - *OpenAsync*. Começar a ouvir solicitações.
 - *CloseAsync*. Parar de ouvir solicitações, concluir todas as solicitações em andamento e desligar normalmente.
 - *Abort*. Cancelar tudo e parar imediatamente.

Para começar, adicione membros de classe privada para coisas de que o ouvinte precisa para funcionar. Eles serão inicializados por meio do construtor e usados posteriormente quando você configurar a URL de escuta.

```csharp
public class OwinCommunicationListener : ICommunicationListener
{
    private readonly ServiceEventSource eventSource;
    private readonly Action<IAppBuilder> startup;
    private readonly ServiceContext serviceContext;
    private readonly string endpointName;
    private readonly string appRoot;

    private IDisposable webApp;
    private string publishAddress;
    private string listeningAddress;

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
        : this(startup, serviceContext, eventSource, endpointName, null)
    {
    }

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
    {
        if (startup == null)
        {
            throw new ArgumentNullException(nameof(startup));
        }

        if (serviceContext == null)
        {
            throw new ArgumentNullException(nameof(serviceContext));
        }

        if (endpointName == null)
        {
            throw new ArgumentNullException(nameof(endpointName));
        }

        if (eventSource == null)
        {
            throw new ArgumentNullException(nameof(eventSource));
        }

        this.startup = startup;
        this.serviceContext = serviceContext;
        this.endpointName = endpointName;
        this.eventSource = eventSource;
        this.appRoot = appRoot;
    }
   

    ...

```

## Implementar o OpenAsync

Para configurar o servidor Web, você precisa de duas informações:

 - *Um prefixo de caminho de URL*. Embora seja opcional, é bom configurar isso agora para que você possa hospedar com segurança vários serviços Web em seu aplicativo.
 - *Uma porta*.

Antes de obter uma porta para o servidor Web, é importante compreender que o Service Fabric fornece uma camada de aplicativo que age como um buffer entre seu aplicativo e o sistema operacional subjacente em que ele é executado. Dessa forma, a Malha de Serviços fornece uma maneira de configurar *pontos de extremidade* para seus serviços. O Service Fabric garante que os pontos de extremidade estejam disponíveis para o serviço a ser usado. Dessa forma, você não precisa configurá-los no ambiente do sistema operacional subjacente. Você pode hospedar facilmente seu aplicativo do Service Fabric em diferentes ambientes sem a necessidade de fazer alterações nele. (Por exemplo, é possível hospedar o mesmo aplicativo no Azure ou em seu próprio datacenter.)

Configure um ponto de extremidade de HTTP em PackageRoot\\ServiceManifest.xml:

```xml

<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="8281" />
    </Endpoints>
</Resources>

```

Essa etapa é importante porque o processo de host do serviço é executado com credenciais restritas (Serviço de Rede no Windows). Isso significa que o serviço não terá acesso para configurar um ponto de extremidade HTTP por si só. Ao usar a configuração de ponto de extremidade, o Service Fabric sabe como definir a ACL (lista de controle de acesso) apropriada para a URL na qual o serviço escutará. O Service Fabric também fornece um local padrão para configurar pontos de extremidade.


Em OwinCommunicationListener.cs, você pode começar a implementar o OpenAsync. É onde você inicia o servidor Web. Primeiro, obtenha as informações do ponto de extremidade e crie a URL que o serviço escutará. A URL será diferente dependendo do ouvinte ser usado em um serviço sem estado ou com estado. Para um serviço com estado, o ouvinte deve criar um endereço exclusivo para cada réplica de serviço com estado que ele escuta. Para serviços sem estado, o endereço pode ser muito mais simples.

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
    var protocol = serviceEndpoint.Protocol;
    int port = serviceEndpoint.Port;

    if (this.serviceContext is StatefulServiceContext)
    {
        StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}{3}/{4}/{5}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/',
            statefulServiceContext.PartitionId,
            statefulServiceContext.ReplicaId,
            Guid.NewGuid());
    }
    else if (this.serviceContext is StatelessServiceContext)
    {
        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/');
    }
    else
    {
        throw new InvalidOperationException();
    }
    
    ...

```

Observe que "http://+" é usado aqui. Isso é para garantir que o servidor Web esteja escutando em todos os endereços disponíveis, incluindo localhost, FQDN e o IP da máquina.

A implementação do OpenAsync é um dos motivos mais importantes pelos quais o servidor Web (ou qualquer pilha de comunicação) é implementado como um ICommunicationListener, em vez de apenas tê-lo aberto diretamente do `RunAsync()` no serviço. O valor de retorno de OpenAsync é o endereço que o servidor Web está escutando. Quando esse endereço é retornado ao sistema, ele registra o endereço com o serviço. O Service Fabric fornece uma API que permite a clientes e outros serviços pedir esse endereço pelo nome do serviço. Isso é importante porque o endereço do serviço não é estático. Os serviços são movimentados no cluster para fins de disponibilidade e balanceamento de recursos. Esse é o mecanismo que permite aos clientes resolver o endereço de escuta de um serviço.

Com isso em mente, o OpenAsync inicia o servidor Web e retorna o endereço em que está escutando. Observe que ele escuta em "http://+", mas antes de o OpenAsync retornar o endereço, o "+" é substituído pelo IP ou FQDN do nó em que ele está no momento. O endereço retornado por esse método é o que está registrado no sistema. Também é o que os clientes e outros serviços veem quando eles solicitam um endereço do serviço. Para que os clientes se conectem corretamente a ele, eles precisam de um IP ou FQDN real no endereço.

```csharp
    ...

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    try
    {
        this.eventSource.ServiceMessage(this.serviceContext, "Starting web server on " + this.listeningAddress);

        this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

        this.eventSource.ServiceMessage(this.serviceContext, "Listening on " + this.publishAddress);

        return Task.FromResult(this.publishAddress);
    }
    catch (Exception ex)
    {
        this.eventSource.ServiceMessage(this.serviceContext, "Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

        this.StopWebServer();

        throw;
    }
}

```

Observe que isso faz referência à classe Startup que foi passada para OwinCommunicationListener no construtor. Essa instância de inicialização é usada pelo servidor Web para inicializar o aplicativo de API Web.

A linha `ServiceEventSource.Current.ServiceMessage()` aparecerá na janela de Eventos de Diagnóstico mais tarde, quando você executar o aplicativo para confirmar que o servidor Web foi iniciado com êxito.

## Implementar CloseAsync e Abort

Finalmente, implemente o CloseAsync e o Abort para interromper o servidor Web. O servidor Web pode ser interrompido eliminando-se o indicador de servidor que foi criado durante o OpenAsync.

```csharp
public Task CloseAsync(CancellationToken cancellationToken)
{
    this.eventSource.ServiceMessage(this.serviceContext, "Closing web server on endpoint {0}", this.endpointName);
            
    this.StopWebServer();

    return Task.FromResult(true);
}

public void Abort()
{
    this.eventSource.ServiceMessage(this.serviceContext, "Aborting web server on endpoint {0}", this.endpointName);
    
    this.StopWebServer();
}

private void StopWebServer()
{
    if (this.serverHandle != null)
    {
        try
        {
            this.serverHandle.Dispose();
        }
        catch (ObjectDisposedException)
        {
            // no-op
        }
    }
}
```

Nesse exemplo de implementação, CloseAsync e Abort simplesmente interrompem o servidor Web. Você pode optar por executar um desligamento mais coordenado do servidor Web no CloseAsync. Por exemplo, o desligamento pode aguardar que as solicitações em andamento sejam concluídas antes de retornar.

## Inicie o servidor Web.

Agora você está pronto para criar e retornar uma instância de OwinCommunicationListener para iniciar o servidor web. De volta à classe de serviço (Service.cs), substitua o método `CreateServiceInstanceListeners()`:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                           .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                           .Select(endpoint => endpoint.Name);

    return endpoints.Select(endpoint => new ServiceInstanceListener(
        serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
}
```

É nesse ponto que o *aplicativo* de API Web e o *host* OWIN finalmente se encontram. O host (OwinCommunicationListener) recebe uma instância do *aplicativo* (a API Web por meio de inicialização). O Service Fabric gerencia seu ciclo de vida. Esse mesmo padrão normalmente pode ser seguido com qualquer pilha de comunicação.

## Colocar tudo isso junto

Neste exemplo, você não precisa fazer nada no método `RunAsync()`, de forma que a substituição pode ser simplesmente removida.

A implementação do serviço final deve ser muito simples. Ele só precisa criar o ouvinte de comunicação:

```csharp
using System;
using System.Collections.Generic;
using System.Fabric;
using System.Fabric.Description;
using System.Linq;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace WebService
{
    internal sealed class WebService : StatelessService
    {
        public WebService(StatelessServiceContext context)
            : base(context)
        { }

        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
            var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                                   .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                                   .Select(endpoint => endpoint.Name);

            return endpoints.Select(endpoint => new ServiceInstanceListener(
                serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
        }
    }
}
```

A classe `OwinCommunicationListener` completa:

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;

namespace WebService
{
    internal class OwinCommunicationListener : ICommunicationListener
    {
    private readonly ServiceEventSource eventSource;
    private readonly Action<IAppBuilder> startup;
    private readonly ServiceContext serviceContext;
    private readonly string endpointName;
    private readonly string appRoot;

    private IDisposable webApp;
    private string publishAddress;
    private string listeningAddress;

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
        : this(startup, serviceContext, eventSource, endpointName, null)
    {
    }

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
    {
        if (startup == null)
        {
            throw new ArgumentNullException(nameof(startup));
        }

        if (serviceContext == null)
        {
            throw new ArgumentNullException(nameof(serviceContext));
        }

        if (endpointName == null)
        {
            throw new ArgumentNullException(nameof(endpointName));
        }

        if (eventSource == null)
        {
            throw new ArgumentNullException(nameof(eventSource));
        }

        this.startup = startup;
        this.serviceContext = serviceContext;
        this.endpointName = endpointName;
        this.eventSource = eventSource;
        this.appRoot = appRoot;
    }

        public bool ListenOnSecondary { get; set; }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
            var protocol = serviceEndpoint.Protocol;
            int port = serviceEndpoint.Port;

            if (this.serviceContext is StatefulServiceContext)
            {
                StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}{3}/{4}/{5}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/',
                    statefulServiceContext.PartitionId,
                    statefulServiceContext.ReplicaId,
                    Guid.NewGuid());
            }
            else if (this.serviceContext is StatelessServiceContext)
            {
                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/');
            }
            else
            {
                throw new InvalidOperationException();
            }

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    try
    {
        this.eventSource.ServiceMessage(this.serviceContext, "Starting web server on " + this.listeningAddress);

        this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

        this.eventSource.ServiceMessage(this.serviceContext, "Listening on " + this.publishAddress);

        return Task.FromResult(this.publishAddress);
    }
    catch (Exception ex)
    {
        this.eventSource.ServiceMessage(this.serviceContext, "Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

        this.StopWebServer();

        throw;
    }
}

        public Task CloseAsync(CancellationToken cancellationToken)
        {
            this.eventSource.ServiceMessage(this.serviceContext, "Closing web server on endpoint {0}", this.endpointName);

            this.StopWebServer();

            return Task.FromResult(true);
        }

        public void Abort()
        {
            this.eventSource.ServiceMessage(this.serviceContext, "Aborting web server on endpoint {0}", this.endpointName);

            this.StopWebServer();
        }

        private void StopWebServer()
        {
            if (this.webApp != null)
            {
                try
                {
                    this.webApp.Dispose();
                }
                catch (ObjectDisposedException)
                {
                    // no-op
                }
            }
        }
    }
}

```

Agora que você juntou todas as peças, seu projeto deve se parecer com um típico aplicativo de API Web, com pontos de entrada API dos Reliable Services e um host OWIN:


![API Web com pontos de entrada da API dos Reliable Services e hospedagem OWIN](media/service-fabric-reliable-services-communication-webapi/webapi-projectstructure.png)

## Execução e conexão por meio de um navegador da Web

[Configure seu ambiente de desenvolvimento](service-fabric-get-started.md), caso ainda não tenha feito isso.


Agora você pode criar e implantar seu serviço. Pressione **F5** no Visual Studio para compilar e implantar o aplicativo. Na janela Eventos de Diagnóstico, você deverá ver uma mensagem indicando que o servidor Web foi aberto em http://localhost:8281/.


![Janela Eventos de Diagnóstico do Visual Studio](media/service-fabric-reliable-services-communication-webapi/webapi-diagnostics.png)

> [AZURE.NOTE] Se a porta já tiver sido aberta por outro processo em seu computador, você poderá ver um erro aqui. Isso indica que o ouvinte não pôde ser aberto. Se esse for o caso, tente usar outra porta na configuração do ponto de extremidade em ServiceManifest.xml.


Quando o serviço estiver em execução, abra um navegador e navegue para [http://localhost:8281/api/values](http://localhost:8281/api/values) para testá-lo.

## Escalar horizontalmente

Escalar horizontalmente aplicativos Web sem estado, normalmente, significa adicionar mais computadores e fazer um rodízio de aplicativos Web neles. O mecanismo de orquestração da Malha de Serviço pode fazer isso para você sempre que novos nós forem adicionados a um cluster. Ao criar instâncias de um serviço sem estado, você pode especificar o número de instâncias que deseja criar. O Service Fabric coloca esse número de instâncias em nós no cluster. Isso garante que não se crie mais de uma instância em algum nó. Você também pode orientar o Service Fabric a sempre criar uma instância em cada nó, especificando **-1** para a contagem de instâncias. Isso garante que sempre que você adicionar nós para escalar horizontalmente o cluster, uma instância do seu serviço sem estado será criada em novos nós. Esse valor é uma propriedade da instância do serviço, de modo que ele é definido quando você cria uma instância de serviço. Você pode fazer isso usando o PowerShell:

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

Ou ao definir um serviço padrão em um projeto de serviço sem estado do Visual Studio:

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

Para obter mais informações sobre como criar aplicativos e instâncias de serviço, confira [Implantar um aplicativo](service-fabric-deploy-remove-applications.md).

## Próximas etapas

[Depurar seu aplicativo do Service Fabric usando o Visual Studio](service-fabric-debugging-your-application.md)

<!---HONumber=AcomDC_0511_2016-->