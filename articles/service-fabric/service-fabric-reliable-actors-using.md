---
title: Implementar recursos em atores do Microsoft Azure Service Fabric | Microsoft Docs
description: Descreve como gravar seu próprio serviço de ator que implementa os recursos de nível de serviço da mesma forma como você faria ao herdar StatefulService.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 41548c3395fa0c8f56e62cfcfb7338a2d53f040f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212884"
---
# <a name="implementing-service-level-features-in-your-actor-service"></a>Implementação de recursos de nível de serviço em seu serviço de ator
Conforme descrito em [camadas de serviço](service-fabric-reliable-actors-platform.md#service-layering), o próprio serviço de ator é um serviço confiável.  É possível gravar seu próprio serviço que deriva de `ActorService` e implementar recursos no nível de serviço da mesma forma que você faria ao herdar StatefulService, tais como:

- Backup e restauração do serviço.
- Funcionalidade compartilhada para todos os atores, por exemplo, um disjuntor.
- Chamadas de procedimento remotas no próprio serviço de ator, bem como em cada ator individual.

## <a name="using-the-actor-service"></a>Usando o serviço de ator
As instâncias de ator têm acesso ao serviço de ator no qual estão sendo executadas. Por meio do serviço de ator, as instâncias de ator podem obter programaticamente o contexto de serviço. O contexto de serviço tem a ID da partição, o nome do serviço, o nome do aplicativo e outras informações específicas da plataforma do Service Fabric:

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```
```Java
CompletableFuture<?> MyActorMethod()
{
    UUID partitionId = this.getActorService().getServiceContext().getPartitionId();
    String serviceTypeName = this.getActorService().getServiceContext().getServiceTypeName();
    URI serviceInstanceName = this.getActorService().getServiceContext().getServiceName();
    String applicationInstanceName = this.getActorService().getServiceContext().getCodePackageActivationContext().getApplicationName();
}
```

Como todos os Reliable Services, o serviço de ator deve ser registrado com um tipo de serviço no tempo de execução do Service Fabric. Para que o serviço de ator seja executado em suas instâncias de ator, o tipo de ator também deve ser registrado no serviço de ator. O método de registro `ActorRuntime` realiza esse trabalho para os atores. No caso mais simples, é possível apenas registrar o tipo de ator, e o serviço de ator, com as configurações padrão, será usado implicitamente:

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

Como alternativa, você pode usar um lambda fornecido pelo método de registro para que você possa construir o serviço de ator por conta própria. Assim, você pode configurar o serviço de ator e construir explicitamente as instâncias de ator, nas quais é possível injetar dependências no ator por meio de seu construtor:

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
static class Program
{
    private static void Main()
    {
      ActorRuntime.registerActorAsync(
              MyActor.class,
              (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
              timeout);

        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="actor-service-methods"></a>Métodos do serviço de ator
O Serviço de ator implementa `IActorService` (C#) ou `ActorService` (Java), que, por sua vez, implementa `IService` (C#) ou `Service` (Java). Essa é a interface usada pela comunicação remota dos Reliable Services, que possibilita chamadas de procedimento remoto em métodos de serviço. Ela contém métodos no nível de serviço que podem ser chamados remotamente por meio da comunicação remota do serviço e permitem [enumerar](service-fabric-reliable-actors-enumerate.md) e [excluir](service-fabric-reliable-actors-delete-actors.md) atores.


## <a name="custom-actor-service"></a>Serviço de ator personalizado
Usando o lambda de registro do ator, você pode registrar seu próprio serviço de ator personalizado que deriva de `ActorService` (C#) e `FabricActorService` (Java). Neste serviço de ator personalizado, você pode implementar sua própria funcionalidade de nível de serviço, escrevendo uma classe de serviço que herda `ActorService` (C#) ou `FabricActorService` (Java). Um serviço de ator personalizado herda toda a funcionalidade de tempo de execução do ator de `ActorService` (C#) ou `FabricActorService` (Java) e pode ser usado para implementar seus próprios métodos de serviço.

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```
```Java
class MyActorService extends FabricActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, BiFunction<FabricActorService, ActorId, ActorBase> newActor)
    {
         super(context, typeInfo, newActor);
    }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="implementing-actor-backup-and-restore"></a>Implementando o backup e a restauração de ator
Um serviço de ator personalizado pode expor um método para fazer backup dos dados do ator aproveitando o ouvinte de comunicação remota já presente em `ActorService`.  Para obter um exemplo, consulte [Atores de backup e restauração](service-fabric-reliable-actors-backup-and-restore.md).

## <a name="actor-using-remoting-v2-stack"></a>Ator utilizando Pilha V2 de Comunicação Remota
Com o pacote NuGet 2.8, os usuários agora podem usar a pilha V2 de Comunicação Remota que é mais eficaz e fornece recursos como Serialização personalizada. A V2 de Comunicação Remota não é compatível com a pilha de Comunicação Remota existente (agora denominada como Pilha de Comunicação Remota V1).

As seguintes alterações são necessárias para usar a Pilha V2 de Comunicação Remota.
 1. Adicione o seguinte atributo de assembly nas Interfaces de Ator.
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
   ```

 2. Compile e atualize os projetos ActorClient e ActorService para começar a usar a Pilha V2.

### <a name="actor-service-upgrade-to-remoting-v2-stack-without-impacting-service-availability"></a>Atualize o ActorService para Pilha V2 de Comunicação Remota sem afetar a Disponibilidade do Serviço.
Essa alteração será uma atualização da etapa 2. Siga as etapas na mesma sequência, conforme listado.

1.  Adicione o seguinte atributo de assembly nas Interfaces de Ator. Este atributo iniciará dois ouvintes para ActorService, Ouvinte V1 (existente) e V2. Atualize ActorService com essa alteração.

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.CompatListener,RemotingClient = RemotingClient.V2Client)]
  ```

2. Atualize ActorClients após concluir a atualização anterior.
Esta etapa garante que o Proxy do Ator esteja usando a Pilha V2 de Comunicação Remota.

3. Esta etapa é opcional. Altere o atributo acima para remover o Ouvinte V1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
    ```

## <a name="next-steps"></a>Próximas etapas
* [Gerenciamento de estado do ator](service-fabric-reliable-actors-state-management.md)
* [Ciclo de vida do ator e coleta de lixo](service-fabric-reliable-actors-lifecycle.md)
* [Documentação de referência da API dos Atores](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Código de exemplo do .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Código de exemplo de Java](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
