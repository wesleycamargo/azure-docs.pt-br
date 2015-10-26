<properties
   pageTitle="Eventos de Atores Confiáveis"
   description="Introdução a eventos para Atores Confiáveis do Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/05/2015"
   ms.author="amanbha"/>


# Eventos de ator
Os eventos de ator são uma forma de enviar notificações de melhor esforço do Ator para os clientes. Os eventos de ator foram desenvolvidos para comunicação entre Ator-Cliente e NÃO devem ser usados para comunicação entre Ator-Ator.

Veja a seguir trechos de código que mostram como usar os eventos de ator em seu aplicativo.

Defina uma interface que descreva os eventos publicados pelo ator. Essa interface deve ser derivada da interface `IActorEvents`. Os argumentos dos métodos devem ser [contrato de dados serializável](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). Os métodos devem retornar void, pois as notificações de evento são o melhor esforço unidirecional.

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

    [Readonly]
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
proxy.SubscribeAsync(new GameEventsHandler()).Wait();
```

No caso de failovers, o ator pode realizar failover em um processo ou nó diferente. O proxy de ator gerencia as assinaturas ativas e as renova automaticamente. Você pode controlar o intervalo da renovação da assinatura por meio da API `ActorProxyEventExtensions.SubscribeAsync<TEvent>`. Para cancelar a assinatura, use a API `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>`.

No ator, simplesmente publique os eventos à medida que eles acontecem. Se existirem assinantes inscritos no evento, o tempo de execução dos Atores enviará a eles a notificação.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), State.Status.Score);
```

<!---HONumber=Oct15_HO3-->