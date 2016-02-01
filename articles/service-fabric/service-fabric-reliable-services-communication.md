<properties
   pageTitle="Visão geral da comunicação dos Reliable Services | Microsoft Azure"
   description="Visão geral do modelo de comunicação dos Reliable Services, incluindo a abertura de ouvintes, a resolução de pontos de extremidade e a comunicação entre serviços."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="BharatNarasimman"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="11/17/2015"
   ms.author="vturecek@microsoft.com"/>

# O modelo de comunicação dos Reliable Services

O Service Fabric do Azure como uma plataforma é totalmente independente quanto às comunicações entre serviços. Todos os protocolos e pilhas são aceitáveis, de UDP a HTTP. Cabe ao desenvolvedor determinar a forma de comunicação entre os serviços. A estrutura de aplicativo dos Reliable Services fornece algumas pilhas e ferramentas de comunicação predefinidas que você pode usar para produzir sua pilha de comunicação personalizada. Isso inclui abstrações que os clientes podem usar para descobrir e se comunicar com pontos de extremidade de serviço.

## Configurar a comunicação de serviço

A API do Reliable Services usa uma interface simples para comunicação de serviço. Para abrir um ponto de extremidade para o serviço, basta implementar esta interface:

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

Você pode adicionar a implementação do ouvinte de comunicação, retornando-a em uma substituição do método de classe com base no serviço.

Para serviços sem estado:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
```

Para serviços com estado:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
```

Em ambos os casos, você deve retornar uma coleção de ouvintes. Isso permite que seu serviço use facilmente vários ouvintes. Por exemplo, você pode ter um ouvinte HTTP e um ouvinte WebSocket separado. Cada ouvinte recebe um nome e a coleção resultante dos pares *nome : endereço* é representada como um objeto JSON quando um cliente solicita os endereços de escuta para uma instância ou uma partição de serviço.

Em um serviço sem estado, a substituição retorna uma coleção de ServiceInstanceListeners. Um ServiceInstanceListener contém uma função para criar um ICommunicationListener e dar um nome a ele. Para os serviços com monitoração de estado, a substituição retorna uma coleção de ServiceReplicaListeners. Esse serviço é um pouco diferente do equivalente sem estado, pois um ServiceReplicaListener tem uma opção para abrir um ICommunicationListener em réplicas secundárias. Você pode usar vários ouvintes de comunicação em um serviço, além de especificar aqueles que aceitem solicitações em réplicas secundárias e os que escutam apenas em réplicas primárias.

Por exemplo, você pode ter um ServiceRemotingListener que faz chamadas RPC apenas em réplicas primárias e um ouvinte personalizado que faz solicitações de leitura em réplicas secundárias:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(initParams =>
            new MyCustomListener(initParams),
            "customReadonlyEndpoint",
            true),

        new ServiceReplicaListener(initParams =>
            new ServiceRemotingListener<IMyStatefulInterface2>(initParams, this),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

Por fim, você pode descrever os pontos de extremidade necessários para o serviço no [manifesto do serviço](service-fabric-application-model.md), na seção sobre pontos de extremidade.

```xml

<Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http" Type="Input" />
    <Endpoints>
</Resources>

```

O ouvinte de comunicação pode acessar os recursos do ponto de extremidade alocados para ele no `CodePackageActivationContext`, no `ServiceInitializationParameters`. O ouvinte pode então começar a escutar solicitações quando é aberto.

```csharp

var codePackageActivationContext = this.serviceInitializationParameters.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE]Os recursos do ponto de extremidade são comuns a todo o pacote de serviço e são alocados pelo Service Fabric quando o pacote de serviço é ativado. Todas as réplicas hospedadas no mesmo ServiceHost compartilham a mesma porta. Isso significa que o ouvinte de comunicação deve oferecer suporte ao compartilhamento de porta. A maneira recomendada de fazer isso é que o ouvinte de comunicação use a ID da partição e a ID de réplica/instância ao gerar o endereço de escuta.

```csharp

var replicaOrInstanceId = 0;
var parameters = this.serviceInitializationParameters as StatelessServiceInitializationParameters;
if (parameters != null)
{
   replicaOrInstanceId = parameters.InstanceId;
}
else
{
   replicaOrInstanceId = ((StatefulServiceInitializationParameters) this.serviceInitializationParameters).ReplicaId;
}

var nodeContext = FabricRuntime.GetNodeContext();

var listenAddress = new Uri(
    string.Format(
        CultureInfo.InvariantCulture,
        "{0}://{1}:{2}/{5}/{3}-{4}",
        scheme,
        nodeContext.IPAddressOrFQDN,
        port,
        this.serviceInitializationParameters.PartitionId,
        replicaOrInstanceId,
        Guid.NewGuid().ToString()));

```

Para uma explicação completa de como escrever um `ICommunicationListener`, confira [Serviços de API Web do Service Fabric com auto-hospedagem OWIN](service-fabric-reliable-services-communication-webapi.md)

### Comunicação do cliente com o serviço
A API dos Reliable Services fornece as abstrações a seguir, que facilitam a criação de clientes que se comunicam com serviços.

#### ServicePartitionResolver
Essa classe do utilitário ajuda o cliente a determinar o ponto de extremidade de um serviço Service Fabric no tempo de execução. Na terminologia do Service Fabric, o processo de determinar o ponto de extremidade de um serviço é conhecido como *resolução do ponto de extremidade de serviço*.

```csharp

public delegate FabricClient CreateFabricClientDelegate();

// ServicePartitionResolver methods

public ServicePartitionResolver(CreateFabricClientDelegate createFabricClient);

Task<ResolvedServicePartition> ResolveAsync(Uri serviceName,
   long partitionKey,
   CancellationToken cancellationToken);

Task<ResolvedServicePartition> ResolveAsync(ResolvedServicePartition previousRsp,
   CancellationToken cancellationToken);


```
> [AZURE.NOTE]O FabricClient é o objeto usado para se comunicar com o cluster do Service Fabric em várias operações de gerenciamento no cluster.

Normalmente, o código cliente não precisa trabalhar diretamente com o `ServicePartitionResolver`. Ele é criado e passado para outras classes auxiliares na API dos Reliable Services. Essas classes usam o resolvedor internamente e ajudam o cliente a se comunicar com o serviço.

#### CommunicationClientFactory
`ICommunicationClientFactory` define a interface base implementada por uma fábrica de clientes de comunicação que produz clientes que podem se comunicar com um serviço do Service Fabric. A implementação de CommunicationClientFactory dependerá da pilha de comunicação usada pelo serviço do Service Fabric em que o cliente quer se comunicar. A API dos Reliable Services fornece um `CommunicationClientFactoryBase<TCommunicationClient>`, que, por sua vez, fornece uma implementação básica da interface `ICommunicationClientFactory` e executa as tarefas comuns a todas as pilhas de comunicação. (Essas tarefas incluem o uso de um `ServicePartitionResolver` para determinar o ponto de extremidade de serviço). Os clientes geralmente implementam a classe abstrata CommunicationClientFactoryBase para tratar da lógica específica da pilha de comunicação.

```csharp

protected CommunicationClientFactoryBase(
   ServicePartitionResolver servicePartitionResolver = null,
   IEnumerable<IExceptionHandler> exceptionHandlers = null,
   IEnumerable<Type> doNotRetryExceptionTypes = null);


public class MyCommunicationClient : ICommunicationClient
{
   public MyCommunicationClient(MyCommunicationChannel communicationChannel)
   {
      this.CommunicationChannel = communicationChannel;
   }
   public MyCommunicationChannel CommunicationChannel { get; private set; }
   public ResolvedServicePartition ResolvedServicePartition;
}

public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }
}

```

#### ServicePartitionClient
`ServicePartitionClient` usa a CommunicationClientFactory (e o ServicePartitionResolver internamente). Ele também ajuda na comunicação com o serviço tratando das repetições para exceções transitórias do Service Fabric e da comunicação comum.

```csharp

public ServicePartitionClient(
    ICommunicationClientFactory<TCommunicationClient> factory,
    Uri serviceName,
    long partitionKey);

public async Task<TResult> InvokeWithRetryAsync<TResult>(
    Func<TCommunicationClient, Task<TResult>> func,
    CancellationToken cancellationToken,
    params Type[] doNotRetryExceptionTypes)

```

Um padrão de uso típico se parece com este:

```csharp

public MyCommunicationClientFactory myCommunicationClientFactory;
public Uri myServiceUri;

... other client code ..

// Call the service corresponding to the partitionKey myKey.

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(
   client =>
   {
      // Communicate with the service using the client.
      throw new NotImplementedException();
   },
   CancellationToken.None);


... other client code ...

```

## Próximas etapas
* [Comunicação remota de serviço com os Reliable Services](service-fabric-reliable-services-communication-remoting.md)

* [API Web que usa o OWIN nos Reliable Services](service-fabric-reliable-services-communication-webapi.md)

* [Comunicação WCF usando os Reliable Services](service-fabric-reliable-services-communication-wcf.md)

<!---HONumber=AcomDC_0121_2016-->