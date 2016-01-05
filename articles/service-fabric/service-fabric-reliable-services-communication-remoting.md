<properties
   pageTitle="Comunicação remota do serviço no Service Fabric | Microsoft Azure"
   description="A comunicação remota do Service Fabric permite que os clientes e serviços se comuniquem com serviços que usam a chamada de procedimento remoto."
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
   ms.date="11/12/2015"
   ms.author="bharatn@microsoft.com"/>

# Comunicação remota de serviço com o Reliable Services
Para serviços que não estão vinculados a um protocolo de comunicação específico ou pilha, como WebAPI, WCF ou outros, a estrutura fornece um mecanismo de comunicação remota para rápida e facilmente configurar a chamada de procedimento remoto para serviços.

## Configurar a comunicação remota em um serviço
A configuração da comunicação remota de um serviço é feita em duas etapas simples.

1. Crie uma interface para implementar o serviço. Essa interface define os métodos que estarão disponíveis para chamada de procedimento remoto no seu serviço e devem ser métodos assíncronos que retornam tarefa. A interface deve implementar `Microsoft.ServiceFabric.Services.Remoting.IService` para sinalizar que o serviço tem uma interface de comunicação remota. 
2. Use `Microsoft.ServiceFabric.Services.Remoting.Runtime.ServiceRemotingListener` em seu serviço. Esta é uma implementação de `ICommunicationListener` que fornece recursos de comunicação remota.

Por exemplo, esse serviço Hello World expõe um único método para obter "Hello World" pela chamada de procedimento remoto:

```csharp
public interface IHelloWorldStateful : IService
{
    Task<string> GetHelloWorld();
}

internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[] { new ServiceReplicaListener(parameters => new ServiceRemotingListener<HelloWorldStateful>(parameters, this)) };
    }

    public Task<string> GetHelloWorld()
    {
        return Task.FromResult("Hello World!");
    }
}

```
> [AZURE.NOTE]Os argumentos e os tipos de retorno na interface de serviço podem ser quaisquer tipos simples, complexos ou personalizados, mas eles devem ser serializáveis pelo [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx) do .NET.


## Chamar métodos de serviços remotos
A chamada de métodos em um serviço usando a pilha de comunicação remota é feita usando um proxy local para o serviço por meio da classe `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy`. O `ServiceProxy` cria um proxy local usando a mesma interface que o serviço implementa. Com esse proxy, você pode simplesmente chamar métodos na interface remotamente.


```csharp

IHelloWorldStateful helloWorldClient = ServiceProxy.Create<IHelloWorldStateful>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.GetHelloWorld();

```

A estrutura remota propaga exceções lançadas no serviço para o cliente. A lógica de manipulação de exceção no cliente usando `ServiceProxy` pode manipular diretamente as exceções que o serviço lança.
 
## Próximas etapas

* [API Web com OWIN no Reliable Services](service-fabric-reliable-services-communication-webapi.md)

* [Comunicação WCF com o Reliable Services](service-fabric-reliable-services-communication-wcf.md)

<!---HONumber=Nov15_HO4-->