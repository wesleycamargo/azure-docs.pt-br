---
title: Eventos de Reliable Actors | Microsoft Docs
description: Introdução a eventos para Service Fabric Reliable Actors.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/30/2016
ms.author: amanbha

---
# Eventos de ator
Os eventos de ator são uma forma de enviar notificações de melhor esforço do ator para os clientes. Os eventos de ator foram desenvolvidos para comunicação entre ator e cliente e não devem ser usados para comunicação entre ator e ator.

Os trechos de código a seguir mostram como usar os eventos de ator em seu aplicativo.

Defina uma interface que descreva os eventos publicados pelo ator. Essa interface deve ser derivada da interface `IActorEvents`. Os argumentos dos métodos devem ser [contrato de dados serializável](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). Os métodos devem retornar valor nulo, pois as notificações de evento são o melhor esforço e unidirecionais.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```

Declare os eventos publicados pelo ator na interface do ator.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```

No lado do cliente, implemente o manipulador de eventos.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

No cliente, crie um proxy para o ator que publica o evento e assine para receber os eventos.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

No caso de failovers, o ator pode realizar failover para um processo ou nó diferente. O proxy de ator gerencia as assinaturas ativas e as renova automaticamente. Você pode controlar o intervalo da renovação da assinatura por meio da API `ActorProxyEventExtensions.SubscribeAsync<TEvent>`. Para cancelar a assinatura, use a API `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>`.

No ator, simplesmente publique os eventos à medida que eles acontecem. Se existirem assinantes no evento, o tempo de execução dos Atores enviará a eles a notificação.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```

## Próximas etapas
* [Reentrância de ator](service-fabric-reliable-actors-reentrancy.md)
* [Diagnóstico e monitoramento de desempenho do ator](service-fabric-reliable-actors-diagnostics.md)
* [Documentação de referência da API do Ator](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Exemplo de código](https://github.com/Azure/servicefabric-samples)

<!---HONumber=AcomDC_0907_2016-->