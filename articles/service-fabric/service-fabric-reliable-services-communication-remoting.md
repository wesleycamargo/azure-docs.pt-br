---
title: "Comunicação remota do serviço no Service Fabric | Microsoft Docs"
description: "A comunicação remota do Service Fabric permite que os clientes e serviços se comuniquem com serviços que usam a chamada de procedimento remoto."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 10/19/2016
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c568e44ac4008d4252ef2e889150506307cc7a92


---
# <a name="service-remoting-with-reliable-services"></a>Comunicação remota de serviço com o Reliable Services
Para serviços que não estão vinculados a um protocolo de comunicação específico ou pilha, como WebAPI, WCF (Windows Communication Foundation) ou outros, a estrutura do Reliable Services fornece um mecanismo de comunicação remota para configurar a chamada de procedimento remoto para serviços de forma rápida e fácil.

## <a name="set-up-remoting-on-a-service"></a>Configurar a comunicação remota em um serviço
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
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task HelloWorldAsync()
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
> [!NOTE]
> Os argumentos e os tipos de retorno na interface de serviço podem ser tipos simples, complexos ou personalizados, mas devem ser serializáveis pelo [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx)do .NET.
> 
> 

## <a name="call-remote-service-methods"></a>Chamar métodos de serviços remotos
A chamada de métodos em um serviço usando a pilha de comunicação remota é feita usando um proxy local para o serviço por meio da classe `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` . O método `ServiceProxy` cria um proxy local usando a mesma interface que o serviço implementa. Com esse proxy, você pode simplesmente chamar métodos na interface remotamente.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

A estrutura remota propaga exceções lançadas no serviço para o cliente. A lógica de manipulação de exceção no cliente usando `ServiceProxy` pode manipular diretamente as exceções que o serviço lança.

## <a name="next-steps"></a>Próximas etapas
* [API Web com OWIN no Reliable Services](service-fabric-reliable-services-communication-webapi.md)
* [Comunicação WCF com o Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Securing communication for Reliable Services](service-fabric-reliable-services-secure-communication.md)




<!--HONumber=Nov16_HO3-->


