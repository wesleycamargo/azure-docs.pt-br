<properties
   pageTitle="Padrão de redes e gráficos distribuídos | Microsoft Azure"
   description="Padrão de design de como os Reliable Actors do Service Fabric podem ser usados para modelar os aplicativos como redes e gráficos distribuídos."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/29/2015"
   ms.author="vturecek"/>

# Padrão de design dos Reliable Actors: gráficos e redes distribuídos
O modelo de programação Reliable Actors do Service Fabric do Azure representa uma opção natural para soluções complexas de modelagem que envolvam relações e para a modelagem dessas relações como objetos.

![Modelagem dos Reliable Actors do Service Fabric do Azure][1]

Conforme ilustrado pelo diagrama acima, a modelagem de um usuário como instância de ator (um nó na rede) é simples. Por exemplo, o "Friends Feed" (às vezes chamado de problema de "seguidor") permite que os usuários visualizem as atualizações de status das pessoas com quem estão conectados, um funcionamento semelhante ao do Facebook e do Twitter.

O modelo Reliable Actors apresenta uma abordagem flexível para o problema de materialização. Podemos popular o Friends Feed no momento em que ocorre o evento, atualizando o Friends Feed de todos os meus amigos no momento em que uma atualização é publicada, conforme ilustrado abaixo:

![O modelo Reliable Actors e a população do Friends Feed][2]


## Exemplo de código de Cache Inteligente – Friends Feed da rede social (no momento do evento)

Exemplo de código para popular o Friends Feed:

```csharp
public interface ISocialPerson : IActor
{
    Task AddFriend(long person);
    Task RemoveFriend(long person);
    Task<List<SocialStatus>> GetFriendsFeed();
    Task<SocialStatus> GetStatus();
    Task<List<SocialStatus>> GetMyFeed();
    Task UpdateStatus(string status);
    Task UpdateFriendFeedAsync(SocialStatus status);
}

[DataContract]
Public class SocialPersonState
{
    [DataMember]
    public string _name; // my name
    [DataMember]
    public List<long> _friends; // list of my friends' IDs
    [DataMember]
    public List<SocialStatus> _friendsFeed; // my friends feeds
    [DataMember]
    public List<SocialStatus> _myFeed; // this is my feed, all my status updates
    [DataMember]
    public SocialStatus _lastStatus; // this is my last update
}

public class SocialPerson : StatefulActor<SocialPersonState>, ISocialPerson
{
    public override Task ActivateAsync()
    {
        CreateOrRestoreState();
        return base.ActivateAsync();
    }

    public Task AddFriend(long person)
    {
        State._friends.Add(person);
        return TaskDone.Done;
    }

    public Task RemoveFriend(long person)
    {
        State._friends.Remove(person);
        return TaskDone.Done;
    }

    public Task<List<SocialStatus>> GetFriendsFeed()
    {
        return Task.FromResult(State._friendsFeed);
    }

    public Task UpdateStatus(string status)
    {
        State._lastStatus = new SocialStatus()
        {
            Name = _name,
            Status = status,
            Timestamp = DateTime.UtcNow
        };
        State._myFeed.Add(_lastStatus);

        var taskList = new List<Task>();

        foreach(var friendId in _friends)
        {
            var friend = ActorProxy.Create<ISocialPerson>(friendId);
            taskList.Add(friend.UpdateFriendFeedAsync(_lastStatus));
        }

        return Task.WhenAll(taskList);
    }

    public Task UpdateFriendFeed(SocialStatus status)
    {
        State._friendsFeed.Add(status);

        return TaskDone.Done;
    }

    public Task<SocialStatus> GetStatus()
    {
        return Task.FromResult(State._lastStatus);
    }

    public Task<List<SocialStatus>> GetMyFeed()
    {
        return Task.FromResult(State._myFeed);
    }
}
```

Como alternativa, é possível modelar atores para distribuir e compilar o Friends Feed usando o temporizador de consulta, quando o usuário solicita seu Friends Feed. Você também pode materializar o Friends Feed em um temporizador (por exemplo, a cada cinco minutos). Ou podemos otimizar o modelo e combinar tanto o processamento da hora do evento quanto do tempo de uma consulta com um modelo baseado em temporizador que dependa dos hábitos de usuário, como a frequência com que ele faz logon ou publica uma atualização.

Ao modelar um ator em uma rede social, você também deverá considerar os “superusuários”, aqueles com milhões de seguidores. Os desenvolvedores devem modelar o estado e o comportamento desses usuários de maneira diferente para atender à demanda maior.

Da mesma forma, se você quiser modelar uma atividade que conecta vários atores de usuário a um único ator em atividade (hub e spoke), também poderá fazê-lo. Chat em grupo e hospedagem de jogo são dois exemplos. Vejamos o exemplo de chat de grupo. Um conjunto de participantes cria um ator de chat em grupo que pode distribuir mensagens de um participante para o grupo. Isso é mostrado no exemplo abaixo:

## Exemplo de código de Cache Inteligente – chat em grupo

```csharp
public interface IGroupChat : IActor
{
    Task PublishMessageAsync(long participantId, string message);
    Task<List<GroupChatMessage>> GetMessagesAsync();
    Task AddParticipantAsync(long participantId);
    Task RemoveParticipantAsync(long partitipantId);
}

[DataContract]
public class GroupChatParticipantState
{
    [DataMember]
    Public long _groupChatId;
    [DataMember]
    public List<GroupChatMessage> _messages;
}

public class GroupChatParticipant : StatefulActor<GroupChatParticipantState>, IGroupParticipant
{
    public Task SendMessageAsync(string message)
    {
        if (State._groupChatId != -1)
        {
            var groupChat = ActorProxy.Create<IGroupChat>(State._groupChatId);
            return groupChat.PublishMessageAsync(this.Id, message);
        }

        return TaskDone.Done;
    }

    ...
}

[DataContract]
public class GroupChatState
{
    [DataMember]
    Public List<long> _participants;
    [DataMember]
    Public List<GroupChatMessage> _messages;
}


public class GroupChat : StatefulActor<GroupChatState>, IGroupChat
{

public Task PublishMessageAsync(long participantId, string message)
{
    var chatMessage = new GroupChatMessage()
    {
        ParticipantId = participantId,
        Message = message,
        Timestamp = DateTime.Now
    };

    State._messages.Add(chatMessage);

    var taskList = new List<Task>();

    foreach(var id in State._participants)
    {
        if (id != participantId)
        {
            var participant = ActorProxy.Create<IGroupParticipant>.Create(id);
            taskList.Add(participant.ReceiveMessageAsync(chatMessage));
        }
    }
    return Task.WhenAll(taskList);
}

...

}
```

Essa abordagem aproveita a capacidade do modelo Reliable Actors para permitir que qualquer ator se dirija a qualquer outro ator do cluster por ID. Eles podem se comunicar sem a necessidade de se preocupar com posicionamento, endereçamento, cache, mensagens, serialização ou roteamento.

## Próximas etapas
[Padrão: cache inteligente](service-fabric-reliable-actors-pattern-smart-cache.md)

[Padrão: governança de recursos](service-fabric-reliable-actors-pattern-resource-governance.md)

[Padrão: composição de serviço com estado](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Padrão: Internet das Coisas](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Padrão: computação distribuída](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Alguns antipadrões](service-fabric-reliable-actors-anti-patterns.md)

[Introdução aos Atores da Malha do Serviço](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs/distributedNetworks_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs/distributedNetworks_arch2.png

<!---HONumber=AcomDC_0128_2016-->