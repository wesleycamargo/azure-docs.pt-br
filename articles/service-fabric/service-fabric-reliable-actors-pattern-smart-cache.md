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
   ms.date="08/05/2015"
   ms.author="vturecek"/>

# Padrão de design de Atores Confiáveis: cache inteligente

A combinação de uma camada da Web, camada de cache, camada de armazenamento e, ocasionalmente, uma camada de trabalho, é quase uma parte padrão dos aplicativos de hoje. A camada de cache geralmente é essencial para o desempenho e pode, na verdade, ser composta por várias camadas em si. Muitos caches são pares simples de chave-valor, enquanto outros sistemas, como o [Redis](http://redis.io), são usados como caches que oferecem semântica mais rica. Ainda assim, qualquer camada de cache especial será limitada em semântica e, o mais importante, ainda é outra camada a ser gerenciada. E se, em vez disso, os objetos apenas mantivessem o estado em variáveis locais e esses objetos pudessem ser colocados em instantâneos ou persistidos em um repositório durável automaticamente? Além disso, as coleções avançadas, como listas, conjuntos classificados, filas e outros tipos personalizados para esse fim são simplesmente modelados como métodos e variáveis de membro.

![][1]

## O exemplo de placar de líderes

Tome como exemplo o placar de líderes — um objeto Leaderboard precisa manter uma lista de jogadores classificados e suas pontuações para que possamos consultá-lo. Por exemplo, para os “100 principais jogadores” ou para localizar a posição de um jogador no placar de líderes em relação a +- N jogadores acima e abaixo dele. Uma solução típica com ferramentas tradicionais exigiria obter (‘GET’) o objeto Leaderboard (coleção que oferece suporte à inserção de uma nova tupla<Player  Points> chamada Score), classificá-lo e, por fim, colocá-lo (‘PUT’) de volta no cache. Provavelmente, nós BLOQUEARÍAMOS (GETLOCK, PUTLOCK) o objeto Leaderboard para consistência. Vamos observar uma solução baseada em ator, em que o estado e o comportamento estão juntos. Há duas opções:

* Implementar a Coleção Leaderboard como parte do ator
* Ou usar o ator como uma interface para a coleção que podemos manter em uma variável de membro. Primeiramente, vamos observar que aparência a interface pode ter:

## Exemplo de código de Cache Inteligente – interface Leaderboard

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

Em seguida, implementamos essa interface, usamos a última opção e encapsulamos o comportamento dessa coleção no ator:

## Exemplo de código de Cache Inteligente — ator Leaderboard

```
public class Leaderboard : Actor<LeaderboardCollection>, ILeaderboard
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

O membro do estado da classe fornece o estado do ator, no exemplo de código acima, ele também fornece métodos de leitura/gravação de dados.

## Exemplo de código de Cache Inteligente — coleção Leaderboard

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

Nenhum envio de dados, nenhum bloqueio, apenas manipulação de objetos remotos em um tempo de execução distribuído, atendendo a vários clientes como se fossem objetos únicos em um único aplicativo atendendo a apenas um cliente. Veja o cliente de exemplo:

## Exemplo de código de Cache Inteligente — chamando o ator Leaderboard

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

## Escalando a arquitetura
Pode parecer que o exemplo acima criaria um afunilamento na instância Leaderboard. E se, por exemplo, estivéssemos planejando oferecer suporte a centenas de milhares de jogadores? Uma maneira de lidar com isso seria introduzir agregadores sem estado que atuariam como um buffer — manteria as pontuações parciais (subtotais) e as enviaria periodicamente ao ator Leaderboard, que pode manter o Leaderboard final. Discutiremos essa técnica de "agregação" em mais detalhes posteriormente. Além disso, não precisamos considerar exclusões mútuas, semáforos ou outras construções simultâneas tradicionalmente exigidas pelos programas simultâneos de comportamento correto. Veja a seguir outro exemplo de cache que demonstra a semântica avançada que pode ser implementada com atores. Dessa vez, implementamos a lógica da Fila de Prioridade (quanto mais baixo o número, mais alta a prioridade) como parte da implementação do Ator. A interface para IJobQueue se parece com esta abaixo:

## Exemplo de código de Cache Inteligente – interface da fila de trabalhos

```
public interface IJobQueue : IActor
{
    Task Enqueue(Job item);
    Task<Job> Dequeue();
    Task<Job> Peek();
    Task<int> GetCount();
}
```

Também precisamos definir o item de trabalho:

## Exemplo de código de Cache Inteligente – trabalho

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

Por fim, implementamos a interface IJobQueue no detalhamento. Observe que, aqui, omitimos os detalhes da implementação da fila de prioridade para clareza: Uma implementação de exemplo pode ser encontrada nos exemplos que acompanha este artigo.

## Exemplo de código de Cache Inteligente – fila de trabalhos

```
public class JobQueue : Actor<List<Jobs>>, IJobQueue
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

## Atores fornecem flexibilidade
Nos exemplos acima, Leaderboard e JobQueue, usamos para diferentes técnicas:

* No exemplo de Leaderboard, encapsulamos um objeto Leaderboard como uma variável de membro privada no ator e simplesmente fornecemos uma interface para esse objeto — para seu estado e funcionalidade.

* Por outro lado, no exemplo JobQueue, implementamos o ator como uma fila de prioridade, em vez de fazer referência a outro objeto definido em outro lugar.

Os atores fornecem flexibilidade ao desenvolvedor para definir estruturas de objeto avançadas como parte dos atores ou gráficos de objeto de referência fora dos atores. Em termos de cache, os atores podem write-behind ou write-through, ou podemos usar diferentes técnicas na granularidade da variável de um membro. Em outras palavras, temos total controle sobre o que persistir e quando persistir. Não é necessário persistir o estado transitório ou o estado que podemos criar a partir do estado salvo. E quanto a popular esses atores caches de ator? Há várias maneiras de se fazer isso. Os atores fornecem métodos virtuais chamados OnActivateAsync() e OnDectivateAsync() para que saibamos quando uma instância do ator é ativada e desativada. Observe que o ator é ativado sob demanda quando uma primeira solicitação é enviada a ele. Podemos usar OnActivateAsync() para popular o estado sob demanda como em read-through, talvez em um repositório estável externo. Ou podemos popular o estado em um temporizador, por exemplo, um ator Exchange Rate que forneça a função de conversão com base nas últimas taxas de câmbio. Esse ator pode popular seu estado de um serviço externo periodicamente, por exemplo, a cada 5 segundos, e usar o estado para a função de conversão. Veja o exemplo abaixo:

## Exemplo de código de Cache Inteligente – conversor de taxas

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

Basicamente, o Cache Inteligente fornece:

* Alta taxa de transferência/baixa latência por solicitações de serviço da memória.
* Roteamento de instância única e serialização single-threaded das solicitações para um item sem nenhuma contenção no repositório persistente.
* Operações semânticas, por exemplo, Enqueue (item de trabalho).
* Facilidade de implementação de write-through ou write-behind.
* Remoção automática de itens LRU (Menos Utilizado Recentemente) (gerenciamento de recursos).
* Elasticidade e confiabilidade automáticas.


## Próximas etapas

[Padrão: redes e gráficos distribuídos](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Padrão: controle de recursos](service-fabric-reliable-actors-pattern-resource-governance.md)

[Padrão: composição de serviço com estado](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Padrão: Internet das Coisas](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Padrão: computação distribuída](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Alguns antipadrões](service-fabric-reliable-actors-anti-patterns.md)

[Introdução aos Atores da Malha do Serviço](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-smart-cache/smartcache-arch.png

<!---HONumber=Nov15_HO2-->