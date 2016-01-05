<properties
   pageTitle="Comunicação de serviço WCF do Reliable services | Microsoft Azure"
   description="A comunicação de serviço WCF interna no Services Fabric fornece a comunicação WCF cliente-serviço para o Reliable Services."
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="11/17/2015"
   ms.author="bharatn@microsoft.com"/>

# Pilha de comunicação baseada no WCF para Serviços Confiáveis
A estrutura de serviços confiáveis permite que os autores de serviço decidam qual pilha de comunicação desejam usar para seu serviço. Eles podem ligar a pilha de comunicação de sua escolha por meio do `ICommunicationListener` retornado dos métodos [CreateServiceReplicaListeners ou CreateServiceInstanceListeners](service-fabric-reliable-service-communication.md). A estrutura fornece uma implementação baseada no WCF da pilha de comunicação para autores de serviço que desejam usar comunicação baseada no WCF.

## Ouvinte de comunicação do WCF
A implementação específica do WCF do `ICommunicationListener` é fornecida pela classe `Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener`.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    // TODO: If your service needs to handle user requests, return a list of ServiceReplicaListeners here.
    return new[] { new ServiceReplicaListener(parameters =>
        new WcfCommunicationListener(typeof(ICalculator), this)
        {
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // which identifies the endpoint that the wcf servicehost should listen on.
            //
            EndpointResourceName = "ServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            Binding = this.CreateListenBinding()
        }
    )};
}

```

## Desenvolvimento de clientes para a pilha de comunicação do WCF
Para o desenvolvimento de clientes que se comuniquem com serviços usando o WCF, a estrutura fornece `WcfClientCommunicationFactory`, que é a implementação específica do WCF do [`ClientCommunicationFactoryBase`](service-fabric-reliable-service-communication.md).

```csharp

public WcfCommunicationClientFactory(
    ServicePartitionResolver servicePartitionResolver = null,
    Binding binding = null,
    object callback = null,
    IList<IExceptionHandler> exceptionHandlers = null,
    IEnumerable<Type> doNotRetryExceptionTypes = null)

```

O canal de comunicação do WCF pode ser acessado no `WcfCommunicationClient` criados por `WcfCommunicationClientFactory`

```csharp

public class WcfCommunicationClient<TChannel> : ICommunicationClient where TChannel : class
{
    public TChannel Channel { get; }
    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}

```

O código do cliente pode usar o `WcfCommunicationClientFactory` junto com o `ServicePartitionClient` para determinar o ponto de extremidade do serviço e fazer a comunicação com o serviço.

```csharp

//
// Create a service resolver for resolving the endpoints of the calculator service.
//
ServicePartitionResolver serviceResolver = new ServicePartitionResolver(() => new FabricClient());

//
// Create the binding.
//
NetTcpBinding binding = CreateClientConnectionBinding();

var clientFactory = new WcfCommunicationClientFactory<ICalculator>(
    serviceResolver,// ServicePartitionResolver
    binding,        // Client binding
    null,           // Callback object
    null);          // donot retry Exception types


//
// Create a client for communicating with the calc service which has been created with
// Singleton partition scheme.
//
var calculatorServicePartitionClient = new ServicePartitionClient<WcfCommunicationClient<ICalculator>>(
    clientFactory,
    ServiceName);

//
// Call the service to perform the operation.
//
var result = calculatorServicePartitionClient.InvokeWithRetryAsync(
    client => client.Channel.AddAsync(2, 3)).Result;

```
 
## Próximas etapas
* [Chamada de procedimento remoto com Reliable Services remoto](service-fabric-reliable-services-communication-remoting.md)

* [API Web com OWIN no Reliable Services](service-fabric-reliable-services-communication-webapi.md)

<!---HONumber=AcomDC_1125_2015-->