<properties
   pageTitle="Padrão de design de cache inteligente | Microsoft Azure"
   description="Padrão de design sobre como usar o modelo de programação dos Reliable Actors do Service Fabric para criar uma infraestrutura de caching para aplicativos baseados na Web."
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
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# Padrão de design de Reliable Actors: cache inteligente

A combinação de uma camada da Web, uma camada de cache, uma camada de armazenamento e (ocasionalmente) uma camada de trabalho compõe as partes padrão dos aplicativos atuais. A camada de cache geralmente é essencial para o desempenho e pode, em si, ser composta por várias camadas. Muitos caches são pares simples de chave/valor. Outros sistemas, como o [Redis](http://redis.io), são usados como cache, mas eles também oferecem semântica mais rica. De qualquer forma, qualquer camada de cache especial terá semântica limitada. E o que é mais importante, ainda é outra camada a ser gerenciada.

Já os objetos podem manter o estado em variáveis locais e podem ser colocados em instantâneos ou mantidos em um repositório durável automaticamente. Além disso, as coleções avançadas, como listas, conjuntos classificados, filas e outros tipos personalizados, podem ser modelados simplesmente como métodos e variáveis de membro.

![Atores e cache][1]

## Explorar o exemplo de placar de líderes

Vamos observar os placares de líderes como um exemplo. Um objeto placar de líderes precisa manter uma lista de jogadores classificados e suas pontuações para que ela possa ser consultada. Uma consulta pode localizar os 100 principais jogadores. Ela também pode encontrar a posição de um jogador no placar de líderes em relação a um determinado número de jogadores acima e abaixo dele. Uma solução tradicional exigiria a obtenção do objeto placar de líderes (uma coleção que oferece suporte à inserção de uma nova tupla `<Player, Points>` chamada **Score**) usando GET, classificando-o e colocando-o de volta no cache usando PUT. Provavelmente, você bloquearia (GETLOCK, PUTLOCK) o objeto placar de líderes para consistência. Vamos observar uma solução baseada em atores, em que o estado e o comportamento estão juntos. Há duas opções:

* Implementar a coleção placar de líderes como parte do ator.
* Usar o ator como uma interface para a coleção que podemos manter em uma variável de membro.

O exemplo de código a seguir mostra como poderia ser a interface.

### Exemplo de código de cache inteligente: interface placar de líderes

```
public interface ILeaderboard : IActor
{
    // Updates the leaderboard with the score - player, points
    Task UpdateLeaderboard(Score score);

    // Returns the Top [count] from the leaderboard e.g., Top 10
    Task<List<Score>> GetLeaderboard(int count);

    // Returns the specific position of the player relative to other players
    Task<List<Score>> GetPosition(long player, int range);
}

```

Em seguida, você pode implementar essa interface usando a última opção e encapsulando o comportamento da coleção no ator:

### Exemplo de código de cache inteligente: ator placar de líderes

```
public class Leaderboard : StatefulActor<LeaderboardCollection>, ILeaderboard
{
    // Specialised collection, could be part of the actor

    public Task UpdateLeaderboard(Score score)
    {
        State.UpdateLeaderboard(score);
    }

    public Task<List<Score>> GetLeaderboard(int count)
    {
        // Return top N from Leaderboard
        return Task.FromResult(State.GetLeaderboard(count));
    }

    public Task<List<Score>> GetPosition(long player, int range)
    {
        // Return player position and other players in range from Leaderboard
        return Task.FromResult(State.FindPosition(player, range));
    }
}

```

O membro de estado da classe fornece o estado do ator. No código de exemplo acima, ele também fornece métodos para ler e gravar dados.

### Exemplo de código de cache inteligente: LeaderboardCollection

```
[DataContract]
public class LeaderboardCollection
{
    // Specialised collection, could be part of the actor
    [DataMember]
    Private List<score> _leaderboard = new List<score>();

    public void UpdateLeaderboard(Score score)
    {
        _leaderboard.add(score);
    }

    public List<Score> GetLeaderboard(int count)
    {
        …
    }

    public List<Score> GetPosition(long player, int range)
    {
        …
    }
}

```

Não existem bloqueios nem envio de dados nessa abordagem. Ela apenas manipula objetos remotos em um tempo de execução distribuído, o que atende a vários clientes como se fossem objetos únicos em um único aplicativo atendendo a apenas um cliente. O exemplo de código a seguir concentra-se no cliente de exemplo.

### Exemplo de código de cache inteligente: chamando o ator placar de líderes

```
// Get reference to Leaderboard
const string appName = "fabric:/FunnyGameApp";
var leaderboard = ActorProxy.Create<ILeaderboard>(1001, appName);

// Update Leaderboard with dummy players and scores
await leaderboard.UpdateLeaderboard(new Score() { Player = 1, Points = 500 });
await leaderboard.UpdateLeaderboard(new Score() { Player = 2, Points = 100 });
await leaderboard.UpdateLeaderboard(new Score() { Player = 3, Points = 1500 });

// Finally, Get the Leaderboard. 0 represents ALL, any other number > 0 represents TOP N
var result = await leaderboard.GetLeaderboard(0);
```

A saída se parece com esta:

```
Player = 3 Points = 1500
Player = 1 Points = 500
Player = 2 Points = 100
```

## Escalar a arquitetura
Pode parecer que o exemplo acima criaria um afunilamento na instância de placar de líderes. Por exemplo, se você planejasse oferecer suporte a milhares de jogadores? Uma maneira de lidar com isso seria introduzir agregadores sem estado que agissem como buffers. Esses agregadores manteriam pontuações parciais (subtotais) e, periodicamente, as enviariam ao ator placar de líderes, que manteria o placar de líderes final. Discutiremos essa técnica em mais detalhes posteriormente. Além disso, não precisamos considerar exclusões mútuas, semáforos ou outras construções simultâneas que tradicionalmente são exigidas pelos programas simultâneos que se comportam corretamente.

Veja a seguir outro exemplo de cache que demonstra a semântica avançada que pode ser implementada com atores. Dessa vez, implementamos a lógica de uma fila de prioridade (quanto mais baixo o número, mais alta a prioridade) como parte da implementação do ator. O exemplo de código a seguir fornece uma interface para **IJobQueue**.

### Exemplo de código de cache inteligente: interface da fila de trabalhos

```
public interface IJobQueue : IActor
{
    Task Enqueue(Job item);
    Task<Job> Dequeue();
    Task<Job> Peek();
    Task<int> GetCount();
}
```

Também precisamos definir o item **Trabalho**:

### Exemplo de código de cache inteligente: trabalho

```
public class Job : IComparable<Job>
{
    public double Priority { get; set; }
    public string Name { get; set; }

    public override string ToString()
    {
        return string.Format("Job = {0} Priority = {1}", Name, Priority);
    }

    public int CompareTo(Job other)
    {
        return Priority.CompareTo(other.Priority);
    }
}
```

Por fim, implementamos a interface IJobQueue no ator. Observe que, aqui, omitimos os detalhes da implementação da fila de prioridade para clareza: Uma ilustração da implementação é fornecida nos exemplos complementares.

### Exemplo de código de cache inteligente: fila de trabalhos

```
public class JobQueue : StatefulActor<List<Jobs>>, IJobQueue
{

    public override Task OnActivateAsync()
    {
        State = new List<Job>();
    }

    public Task Enqueue(Job item)
    {
        // this is where we add to the queue

        ...

    }

    public Task<Job> Dequeue()
    {
        // this is where we remove from the head of the queue

        ...

        return Task.FromResult(frontItem);
    }

    public Task<Job> Peek()
    {
        // this is where we peek at the head of the queue

        ...

        return Task.FromResult(frontItem);
    }

    public Task<int> GetCount()
    {

        // this is where we return the number of items in the queue

        return Task.FromResult(data.Count);
    }
}

```

A saída se parece com esta:

```
Job = 2 Priority = 0.0323341116459733
Job = 3 Priority = 0.125596747792138
Job = 4 Priority = 0.208425460480352
Job = 0 Priority = 0.304352047063574
Job = 8 Priority = 0.415597594070992
Job = 7 Priority = 0.477669881413537
Job = 5 Priority = 0.525898784178262
Job = 9 Priority = 0.921959541701693
Job = 6 Priority = 0.962653734238191
Job = 1 Priority = 0.97444181375878
```

## Usar atores para fornecer flexibilidade
Nos exemplos de placar de líderes e de fila de trabalhos acima, usamos duas técnicas diferentes:

* No exemplo de placar de líderes, encapsulamos um objeto placar de líderes como uma variável de membro privado no ator. Em seguida, simplesmente fornecemos uma interface para esse objeto, tanto para seu estado, quanto para sua funcionalidade.

* No exemplo de fila de trabalhos, implementamos o ator como uma fila de prioridade em si, em vez de fazer referência a outro objeto definido em algum outro lugar.

Os atores fornecem flexibilidade ao desenvolvedor para definir estruturas de objeto avançadas como parte dos atores ou gráficos de objeto de referência fora dos atores. Em termos de cache, os atores podem executar write-behind ou write-through, ou podemos usar diferentes técnicas na granularidade das variáveis de membro. Você tem total controle sobre o que persistir e quando persistir. Não é necessário persistir o estado transitório ou o estado que você pode criar do estado salvo.

Como são os caches desses atores populados? Há várias maneiras de se fazer isso. Os atores fornecem os métodos virtuais **OnActivateAsync()** e **OnDeactivateAsync()** para que você saiba quando uma instância do ator é ativada e desativada. Observe que o ator é ativado sob demanda quando uma solicitação é enviada a ele pela primeira vez.

É possível usar OnActivateAsync() para popular o estado sob demanda, como em read-through, tal como em um repositório estável externo. Você também pode popular o estado em um temporizador, por exemplo, usando um ator de taxa de câmbio que forneça uma função de conversão baseada nas taxas de câmbio mais atuais. Esse ator pode popular seu estado de um serviço externo periodicamente (por exemplo, a cada cinco segundos) e usar o estado para a função de conversão. O exemplo de código a seguir mostra como isso pode ser feito.

### Exemplo de código de cache inteligente: conversor de taxas

```
...

private List<ExchangeRate> _rates;
private IActorTimer _timer;

public Task Activate()
{
    // registering a timer that will live as long as the actor...
    _timer = this.RegisterTimer((obj) =>
    {
        Console.WriteLine("Refreshing rates...");
        return this.RefreshRates(); // call to external service/source to retrieve exchange rates
    },
    null,
    TimeSpan.FromSeconds(0), // start immediately
    TimeSpan.FromSeconds(5)); // refresh every 5 seconds

}

public Task RefreshRates()
{
    // this is where we will make an external call and populate rates
}

```

Basicamente, a abordagem de cache inteligente fornece:

* Alta taxa de transferência/baixa latência por solicitações de serviço da memória.
* Roteamento de instância única e serialização single-thread das solicitações para um item sem nenhuma contenção em um repositório persistente.
* Operações semânticas, por exemplo, **Enqueue(item Trabalho)**.
* Facilidade de implementação de write-through e de write-behind.
* Remoção automática de itens LRU (menos utilizado recentemente) (gerenciamento de recursos).
* Elasticidade e confiabilidade automáticas.


## Próximas etapas

[Padrão: redes e gráficos distribuídos](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Padrão: governança de recursos](service-fabric-reliable-actors-pattern-resource-governance.md)

[Padrão: composição de serviço com estado](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Padrão: Internet das Coisas](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Padrão: computação distribuída](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Alguns antipadrões](service-fabric-reliable-actors-anti-patterns.md)

[Introdução aos Reliable Actors do Service Fabric](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-smart-cache/smartcache-arch.png

<!---HONumber=AcomDC_0121_2016-->