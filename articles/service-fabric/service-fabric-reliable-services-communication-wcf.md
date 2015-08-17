<properties
   pageTitle="Pilha de comunicação baseada no WCF fornecida pelo API de Serviços Confiáveis"
   description="Este artigo descreve a pilha de comunicação baseada no WCF fornecida pelo API do Serviço Confiável."
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="vipulm"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="04/13/2015"
   ms.author="bharatn@microsoft.com"/>

# Pilha de comunicação baseada no WCF para Serviços Confiáveis
A estrutura de serviços confiáveis permite que os autores de serviço decidam qual pilha de comunicação desejam usar para seu serviço. Eles podem ligar a pilha de comunicação de sua escolha por meio de `ICommunicationListener` retornado do método [`CreateCommunicationListener`](../service-fabric-reliable-service-communication.md). A estrutura fornece uma implementação baseada no WCF da pilha de comunicação para autores de serviço que desejam usar comunicação baseada no WCF.

## Ouvinte de comunicação do WCF
A implementação específica do WCF do `ICommunicationListener` é fornecida pela classe `WcfCommunicationListener`.

```csharp

public WcfCommunicationListener(
    Type communicationInterfaceType,
    Type communicationImplementationType);

protected override ICommunicationListener CreateCommunicationListener()
    {
        WcfCommunicationListener communicationListener = new WcfCommunicationListener(typeof(ICalculator), this)
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
        };

        return communicationListener;
    }

```

## Desenvolvimento de clientes para a pilha de comunicação do WCF
Para o desenvolvimento de clientes que se comuniquem com serviços usando o WCF, a estrutura fornece `WcfClientCommunicationFactory`, que é a implementação específica do WCF do [`ClientCommunicationFactoryBase`](../service-fabric-reliable-service-communication.md).

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
 

<!---HONumber=August15_HO6-->