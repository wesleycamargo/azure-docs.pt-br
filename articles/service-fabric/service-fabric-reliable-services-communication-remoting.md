<properties
   pageTitle="Comunicação remota do serviço no Service Fabric | Microsoft Azure"
   description="A comunicação remota do Service Fabric permite que os clientes e serviços se comuniquem com serviços que usam a chamada de procedimento remoto."
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
   ms.date="03/25/2016"
   ms.author="vturecek"/>

# Comunicação remota de serviço com o Reliable Services
Para serviços que não estão vinculados a um protocolo de comunicação específico ou pilha, como WebAPI, WCF (Windows Communication Foundation) ou outros, a estrutura do Reliable Services fornece um mecanismo de comunicação remota para configurar a chamada de procedimento remoto para serviços de forma rápida e fácil.

## Configurar a comunicação remota em um serviço
A configuração da comunicação remota de um serviço é feita em duas etapas simples:

1. Crie uma interface para implementar o serviço. Essa interface define os métodos que estarão disponíveis para chamada de procedimento remoto no seu serviço. Os métodos devem ser métodos assíncronos que retornam tarefas. A interface deve implementar `Microsoft.ServiceFabric.Services.Remoting.IService` para sinalizar que o serviço tem uma interface de comunicação remota.
2. Use um ouvinte de comunicação remota em seu serviço. Esta é uma implementação de `ICommunicationListener` que fornece recursos de comunicação remota. O namespace `Microsoft.ServiceFabric.Services.Remoting.Runtime` contém um método de extensão, `CreateServiceRemotingListener`, para serviços com e sem estado que podem ser usados para criar um ouvinte de comunicação remota usando o protocolo de transporte remoto padrão.

Por exemplo, o serviço sem estado a seguir expõe um único método para obter "Hello World" pela chamada de procedimento remoto.

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> GetHelloWorld();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
{
    }

    public Task HelloWorld()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] { new ServiceInstanceListener(context => 
            this.CreateServiceRemotingListener(context)) };
    }
}
```
> [AZURE.NOTE] Os argumentos e os tipos de retorno na interface de serviço podem ser tipos simples, complexos ou personalizados, mas devem ser serializáveis pelo [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx) do .NET.


## Chamar métodos de serviços remotos
A chamada de métodos em um serviço usando a pilha de comunicação remota é feita usando um proxy local para o serviço por meio da classe `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy`. O método `ServiceProxy` cria um proxy local usando a mesma interface que o serviço implementa. Com esse proxy, você pode simplesmente chamar métodos na interface remotamente.


```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.GetHelloWorld();

```

A estrutura remota propaga exceções lançadas no serviço para o cliente. A lógica de manipulação de exceção no cliente usando `ServiceProxy` pode manipular diretamente as exceções que o serviço lança.

## Próximas etapas

* [API Web com OWIN no Reliable Services](service-fabric-reliable-services-communication-webapi.md)

* [Comunicação WCF com o Reliable Services](service-fabric-reliable-services-communication-wcf.md)

* [Securing communication for Reliable Services](service-fabric-reliable-services-secure-communication.md)

<!---HONumber=AcomDC_0511_2016-->