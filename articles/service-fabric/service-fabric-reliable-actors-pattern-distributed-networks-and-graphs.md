<properties
   pageTitle="Padrão de projeto de redes e gráficos distribuídos dos Atores da Malha de Serviço do Azure"
   description="O padrão de projeto de como os atores de malha do serviço pode ser usado para modelar o aplicativos como redes distribuídas e gráficos"
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
   ms.date="08/11/2015"
   ms.author="claudioc"/>

# Padrão de design de Atores Confiáveis: gráficos e redes distribuídos
Os Atores Confiáveis do Service Fabric representam uma opção natural para soluções complexas de modelagem que envolvam relações e a modelagem dessas relações como objetos.

![][1]

Como ilustra o diagrama, a modelagem um usuário como uma instância de ator (nó na rede) é simples. Por exemplo, o "Friends Feed" (às vezes mencionado como o problema de "seguidor") permite que os usuários visualizem as atualizações de status das pessoas as quais eles estão conectados, um funcionamento semelhante ao do Facebook e Twitter. O modelo de ator fornece flexibilidade para abordar o problema de materialização. É possível popular o Friends Feed no momento em que ocorre o evento, atualizando o Friends Feed de todos os meus amigos no momento em que uma atualização é publicada, conforme ilustrado abaixo:

![][2]


## Exemplo de código de Cache Inteligente – Friends Feed da rede social (no momento do evento)

Código de exemplo para popular o Friends Feed:

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

public class SocialPerson : Actor, ISocialPerson
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

Como alternativa, é possível modelar nossos atores para distribuir e compilar o Friends Feed usando o temporizador de consulta, em outras palavras quando o usuário solicita seu Friends Feed. Outro método que podemos usar é a materializar os Friends Feed em um temporizador, por exemplo, a cada 5 minutos. Ou podemos otimizar o modelo e combinar a hora do evento e processamento de tempo de uma consulta com um modelo baseado em temporizador que dependa dos hábitos de usuário, como a frequência com que ele efetua logon ou publica uma atualização. Ao modelar um ator em uma rede social, também deve-se considerar os usuários "superusuários," com milhões de seguidores. Os desenvolvedores devem modelar o estado e o comportamento desses usuários de maneira diferente para atender à demanda. Da mesma forma, se quisermos modelar uma atividade que conecta vários atores de usuário a um único ator em atividade (hub e spoke) que também possa ser feito. Cenários bate-papo em grupo e de hospedagem de jogo são dois exemplos. Vamos tomar o chat de grupo como exemplo; um conjunto de participantes cria um ator de bate-papo em grupo que pode distribuir mensagens de um participante ao grupo, como no exemplo a seguir:

## Exemplo de código de Cache Inteligente – GroupChat

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

public class GroupChatParticipant : Actor<GroupChatParticipantState>, IGroupParticipant
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


public class GroupChat : Actor<GroupChatState>, IGroupChat
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

Tudo o que ele realmente faz é aproveitar a capacidade dos atores confiáveis para permitir que qualquer ator trate de qualquer outro ator do cluster por id e se comunique com ele sem a necessidade de se preocupar com posicionamento, endereçamento, caching, mensagens, serialização ou roteamento.

## Próximas etapas
[Padrão: cache inteligente](service-fabric-reliable-actors-pattern-smart-cache.md)

[Padrão: controle de recursos](service-fabric-reliable-actors-pattern-resource-governance.md)

[Padrão: composição de serviço com estado](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Padrão: Internet das Coisas](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Padrão: computação distribuída](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Alguns antipadrões](service-fabric-reliable-actors-anti-patterns.md)

[Introdução aos Atores da Malha do Serviço](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs/distributedNetworks_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs/distributedNetworks_arch2.png

<!---HONumber=August15_HO7-->