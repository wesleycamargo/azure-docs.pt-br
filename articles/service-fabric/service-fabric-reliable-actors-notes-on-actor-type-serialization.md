---
title: "Observações de Reliable Actors sobre a serialização do tipo de ator | Microsoft Docs"
description: "Discute os requisitos básicos para definir as classes serializáveis que podem ser usadas para estabelecer as interfaces e o estado dos Reliable Actors do Service Fabric"
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 6e50e4dc-969a-4a1c-b36c-b292d964c7e3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: 57aec98a681e1cb5d75f910427975c6c3a1728c3
ms.openlocfilehash: f08fc1df10506dead5d049fb2c6cdc29c8f89d90


---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Observações sobre a serialização de tipo dos Reliable Actors do Service Fabric
Os argumentos de todos os métodos, os tipos de resultado das tarefas retornados por cada método em uma interface de ator e os objetos armazenados no Gerenciador de Estado de um ator devem ser [serializáveis por Contrato de Dados](https://msdn.microsoft.com/library/ms731923.aspx). Isso também se aplica aos argumentos dos métodos definidos nas [interfaces de evento de ator](service-fabric-reliable-actors-events.md). (Os métodos de interface de eventos de ator sempre retornam nulo).

## <a name="custom-data-types"></a>Tipos de dados personalizados
Neste exemplo, a interface de ator a seguir define um método que retorna um tipo de dados personalizado chamado `VoicemailBox`.

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

A interface é implementada por um ator, que usa o Gerenciador de Estado para armazenar um objeto `VoicemailBox` :

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

Neste exemplo, o objeto `VoicemailBox` é serializado quando:

* O objeto é transmitido entre uma instância do ator e um chamador.
* O objeto é salvo no Gerenciador de Estado, onde é mantido no disco e replicado para outros nós.

A estrutura Reliable Actor usa a serialização DataContract. Portanto, os objetos de dados personalizados e seus membros devem ser anotados com os atributos **DataContract** e **DataMember**, respectivamente

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```

```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```

## <a name="next-steps"></a>Próximas etapas
* [Ciclo de vida do ator e coleta de lixo](service-fabric-reliable-actors-lifecycle.md)
* [Lembretes e temporizadores de ator](service-fabric-reliable-actors-timers-reminders.md)
* [Eventos de ator](service-fabric-reliable-actors-events.md)
* [Reentrância de ator](service-fabric-reliable-actors-reentrancy.md)
* [Polimorfismo de ator e padrões de design orientado a objeto](service-fabric-reliable-actors-polymorphism.md)
* [Diagnóstico e monitoramento de desempenho do ator](service-fabric-reliable-actors-diagnostics.md)



<!--HONumber=Nov16_HO3-->


