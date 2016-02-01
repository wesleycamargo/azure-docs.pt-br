<properties
   pageTitle="Padrão de design de governança de recursos | Microsoft Azure"
   description="O padrão de design de como os Reliable Actors do Service Fabric podem ser usados para modelar aplicativos precisa ser escalado verticalmente, mas usar recursos restritos."
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

# Padrão de design de Reliable Actors: governança de recursos

Esse padrão e cenários relacionados podem ser facilmente reconhecidos pelos desenvolvedores (corporativos ou não) que têm recursos restritos no local ou na nuvem que eles não podem escalar imediatamente. Eles também são conhecidos dos desenvolvedores que querem enviar aplicativos e dados em larga escala para a nuvem.

Na empresa, esses recursos restritos, como bancos de dados, são executados em hardware de escala vertical. Qualquer pessoa com um longo histórico corporativo sabe que essa é uma situação comum no local. Mesmo na escala de nuvem, os desenvolvedores veem essa situação ocorrer quando um serviço de nuvem tenta ultrapassar o limite de 64.000 TCP de conexões entre uma tupla de endereço/porta. Isso também ocorre em tentativas de conexão com um banco de dados baseado em nuvem que limita o número de conexões simultâneas.

No passado, esse problema era resolvido pela limitação por meio de middleware baseado em mensagem ou por mecanismos de fachada e pooling personalizados. Não é fácil trabalhar com esses recursos, especialmente quando você precisa escalar a camada intermediária e ainda manter as contagens corretas de conexão. É uma solução frágil e complexa.

Assim como o padrão de cache inteligente, esse padrão abrange vários cenários e clientes que já vêm trabalhando em sistemas com recursos restritos. Seus sistemas precisam escalar horizontalmente não apenas serviços, mas também seu estado na memória, bem como o estado persistente no armazenamento estável.

O diagrama abaixo ilustra esse cenário:

![Atores sem estado, particionamento e recursos restritos][1]

## Modelar cenários de cache com atores

Você pode modelar o acesso a recursos usando um ator ou vários atores que atuam como proxies para um recurso ou um grupo de recursos (uma conexão, por exemplo). Você pode gerenciar o recurso diretamente por meio de atores individuais ou usar um ator de coordenação que gerencie os atores de recurso.

Para tornar esse conceito mais concreto, falaremos sobre a necessidade comum de trabalhar em uma camada de armazenamento particionada (fragmentada) por motivos de desempenho e escalabilidade. A primeira opção é bastante básica. É possível usar uma função estática para mapear e resolver os atores para recursos de downstream. Tal função pode, por exemplo, retornar uma cadeia de conexão com uma determinada entrada. Cabe a você como implementar essa função. Mas essa abordagem tem suas desvantagens, como afinidade estática, que dificulta reparticionar recursos ou remapear atores para recursos.

Veja um exemplo simples. Fazemos aritmética modular para determinar o nome do banco de dados usando **userId** e usamos **region** para identificar o servidor de banco de dados.

### Exemplo de código da governança de recursos: resolução estática

```csharp
private static string _connectionString = "none";

private static string ResolveConnectionString(long userId, int region)
{
    if (_connectionString == "none")
    {
        // an example of static mapping
        _connectionString = String.Format("Server=SERVER_{0};Database=DB_{0}", region, userId % 4);
    }
    return _connectionString;
}
```

Isso é simples, mas não é muito flexível. Agora vamos examinar uma abordagem mais avançada e útil.

Primeiro, modele a afinidade entre recursos físicos e atores. Isso é feito por meio de um ator chamado **resolver**. Ele compreende o mapeamento entre usuários, partições lógicas e recursos físicos. O resolver mantém seus dados em um repositório persistente, mas ele é armazenado em cache para que possa ser pesquisado facilmente. Conforme abordado [no exemplo de taxa de câmbio no padrão de cache inteligente](service-fabric-reliable-actors-pattern-smart-cache.md), um resolver pode buscar de modo proativo as informações mais recentes usando um temporizador. Depois que o ator do usuário determina o recurso que precisa usar, ele armazena em cache o recurso em uma variável local chamada **\_resolution** e usa o recurso durante seu tempo de vida.

Escolhemos uma resolução baseada em pesquisa (ilustrada abaixo) em vez de um hash simples ou hash de intervalo em razão da flexibilidade fornecida em operações. Isso pode incluir reduzir horizontal ou verticalmente e mover um usuário de um recurso para outro.

![Uma solução de pesquisa com resolver][2]

Na ilustração acima, você pode ver que o ator B23 primeiro resolve seu recurso (resolução) **DB1** e o armazena em cache. Agora as operações subsequentes podem usar a resolução em cache para acessar o recurso restrito. Uma vez que os atores oferecem suporte à execução single-threaded, os desenvolvedores não precisam mais se preocupar com o acesso simultâneo ao recurso. Veja o exemplo de código a seguir para observar o usuário e os atores do resolver.

### Exemplo de código da governança de recursos: resolver

```csharp
public interface IUser : IActor
{
    Task UpdateProfile(string name, string country, int age);
}

[DataContract]
public class UserState
{
    [DataMember]
    private long _userId;
    [DataMember]
    private string _name;
    [DataMember]
    private string _country;
    [DataMember]
    private int _age;
    [DataMember]
    private Resolution _resolution;
}


public class User : StatefulActor<UserState>, IUser
{
    public override async Task ActivateAsync()
    {
        State._userId = this.GetPrimaryKeyLong();
        var resolver = ActorProxy.Create<IResolver>(0);
        State._resolution = await resolver.ResolveAsync(State._userId);
        await base.ActivateAsync();
    }

    public Task UpdateProfile(string name, string country, int age)
    {
        Console.WriteLine("Using {0}", State._resolution.Resource.ConnectionString);
        // this is where we use the resource...
        return TaskDone.Done;
    }
}
```

#### Governança de recursos: exemplo de resolver

```csharp
public interface IResolver : IActor
{
    Task<Resolution> ResolveAsync(long entity);
}

[DataContract]
public class ResolverState
{
    ...
}

public class Resolver : StatefulActor<ResolverState>, IResolver
{
    ...

    public Task<Resolution> ResolveAsync(long entityKey)
    {
        if (State._resolutionCache.ContainsKey(entityKey))
            return Task.FromResult(_resolutionCache[entityKey]); // return from cache

        var partitionKey = State._entityPartitions[entityKey]; // resolve partition;
        var resourceKey = State._partitionResources[partitionKey]; // resolve resource;
        var resolution =
            new Resolution()
            {
                Entity = State._entities[entityKey],
                Partition = State._partitions[partitionKey],
                Resource = State._resources[resourceKey]
            }; // create resolution

        State._resolutionCache.Add(entityKey, resolution); // cache the resolution

        return Task.FromResult(resolution);
    }

    ...
}
```

## Acessar recursos que têm capacidade finita

Agora vejamos outro exemplo: acesso exclusivo a recursos valiosos, como bancos de dados, contas de armazenamento e sistemas de arquivos, que têm capacidade finita de produtividade. Nesse cenário, queremos processar eventos usando um ator chamado EventProcessor. Esse ator é responsável por processar e persistir o evento, nesse caso, em um arquivo .csv para simplificar. Podemos seguir a abordagem de particionamento discutida acima para escalar horizontalmente os recursos, mas ainda temos que lidar com os problemas de simultaneidade. Escolhemos um exemplo baseado em arquivo para ilustrar esse ponto, pois gravar em um único arquivo de vários atores acaba gerando problemas de simultaneidade. Para resolver o problema, introduzimos outro ator, chamado EventWriter, que tem propriedade exclusiva dos recursos restritos. O cenário é ilustrado abaixo:

![Gravando e processando eventos usando EventWriter e EventProcessor][3]

Marcamos os atores EventProcessor como trabalhos sem estado, o que permite que o tempo de execução os escalone pelo cluster, conforme a necessidade. Observe que não usamos identificadores na ilustração acima para esses atores. Os atores sem estado constituem um pool de trabalhos mantido pelo tempo de execução.

No código de exemplo abaixo, o ator EventProcessor faz duas coisas. Primeiro, ele decide qual EventWriter (e, portanto, recurso) usar e, em seguida, invoca o ator escolhido para gravar o evento processado. Para simplificar, escolhemos o tipo de evento como o identificador para o ator EventWriter. Desse modo, há apenas um EventWriter para esse tipo de evento, e ele fornece single-thread e acesso exclusivo ao recurso.

### Exemplo de código da governança de recursos: EventProcessor

```csharp
public interface IEventProcessor : IActor
{
    Task ProcessEventAsync(long eventId, long eventType, DateTime eventTime, string payload);
}

public class EventProcessor : StatelessActor, IEventProcessor
{
    public Task ProcessEventAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        // This where we write to constrained resource...
        var eventWriterKey = ResolveWriter(eventType, eventTime);
        var eventWriter = ActorProxy.Create<IEventWriter>(eventWriterKey);

        return eventWriter.WriteEventAsync(eventId, eventType, eventTime, payload);
    }

    private long ResolveWriter(long eventType, DateTime eventTime)
    {
        // To simplify, we are returning event type as to identify the event writer actor.
        return eventType;
    }
}
```

Agora vamos examinar o ator EventWriter. Ele controla o acesso exclusivo ao recurso restrito (nesse caso, o arquivo e os eventos de gravação nele), e não passa muito disso.

### Exemplo de código da governança de recursos: EventWriter

```csharp
public interface IEventWriter : IActor
{
    Task WriteEventAsync(long eventId, long eventType, DateTime eventTime, string payload);
    Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload);
}

[DataContract]
public class EventWriterState
{
    [DataMember]
    public string _filename;
}

public class EventWriter : StatefulActor<EventWriterState>, IEventWriter
{
    private StreamWriter _writer;

    protected override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.Id);
        _writer = new StreamWriter(_filename);

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
    {
        _writer.Close();
        return base.OnDeactivateAsync();
    }

    public async Task WriteEventAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var text = string.Format("{0}, {1}, {2}, {3}", eventId, eventType, eventTime, payload);
        await _writer.WriteLineAsync(text);
        await _writer.FlushAsync();
    }
 }
```

Ao usar um único ator responsável pelo recurso, você pode adicionar recursos, como buffer. Você pode armazenar em buffer eventos de entrada e gravar esses eventos periodicamente usando um temporizador ou quando o buffer estiver cheio. O exemplo de código a seguir fornece um exemplo simples baseado em temporizador.

### Exemplo de código da governança de recursos: EventWriter com buffer

```csharp
[DataMember]
public class EventWriterState
{
    [DataMember]
    public string _filename;
    [DataMember]
    public Queue<CustomEvent> _buffer;
}

public class EventWriter : StatefulActor<EventWriterState>, IEventWriter
{
    private StreamWriter _writer;
    private IActorTimer _timer;

    protected override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.Id);
        _writer = new StreamWriter(_filename);
        State._buffer = new Queue<CustomEvent>();

        this.RegisterTimer(
            ProcessBatchAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessBatchAsync(object obj)
    {
        if (State._buffer.Count == 0)
            return;

        while (State._buffer.Count > 0)
        {
            var customEvent = State._buffer.Dequeue();
            await this.WriteEventAsync(customEvent.EventId, customEvent.EventType, customEvent.EventTime, customEvent.Payload);
        }
    }

    public Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var customEvent = new CustomEvent()
        {
            EventId = eventId,
            EventType = eventType,
            EventTime = eventTime,
            Payload = payload
        };

        State._buffer.Enqueue(customEvent);

        return TaskDone.Done;
    }
}
```

O código acima funcionará bem, mas os clientes não saberão se seu evento o criou no repositório subjacente. Para permitir o armazenamento em buffer e fornecer aos clientes informações sobre o que está acontecendo com a respectiva solicitação, a abordagem a seguir permite que os clientes esperem até que o evento seja gravado no arquivo .csv.

### Exemplo de código da governança de recursos: envio em lote assíncrono

```csharp
public class AsyncBatchExecutor
{
    private readonly List<TaskCompletionSource<bool>> actionPromises;

    public AsyncBatchExecutor()
    {
        this.actionPromises = new List<TaskCompletionSource<bool>>();
    }

    public int Count
    {
        get
        {
            return actionPromises.Count;
        }
    }

    public Task SubmitNext()
    {
        var resolver = new TaskCompletionSource<bool>();
        actionPromises.Add(resolver);
        return resolver.Task;
    }

    public void Flush()
    {
        foreach (var tcs in actionPromises)
        {
            tcs.TrySetResult(true);

        }
        actionPromises.Clear();
    }
}
```

Usaremos essa classe para criar e manter uma lista de tarefas não concluídas (para bloquear clientes). Nós as concluiremos em um passo depois de gravarmos os eventos em buffer no armazenamento.

Na classe EventWriter, precisamos fazer três coisas: marcar a classe de ator como reentrante, retornar o resultado de **SubmitNext()** e liberar nosso temporizador. Veja o código modificado abaixo.

### Exemplo de código da governança de recursos: armazenamento em buffer com envio em lote assíncrono

```csharp
public class EventWriter : StatefulActor<EventWriterState>, IEventWriter
{
    protected override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.GetPrimaryKeyLong());
        _writer = new StreamWriter(_filename);
        State._buffer = new Queue<CustomEvent>();
        _batchExecuter = new AsyncBatchExecutor();

        this.RegisterTimer(
            ProcessBatchAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessBatchAsync(object obj)
    {
        if (_batchExecuter.Count > 0)
        {
            // take snapshot of the batch tasks
            var batchSnapshot = _batchExecuter;
            _batchExecuter = new AsyncBatchExecutor();

            if (State._buffer.Count == 0)
                return;

            while (State._buffer.Count > 0)
            {
                var customEvent = State._buffer.Dequeue();
                await this.WriteEventAsync(customEvent.EventId, customEvent.EventType, customEvent.EventTime, customEvent.Payload);
            }

            _batchExecuter.Flush();
        }
    }
    ...

    public Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var customEvent = new CustomEvent()
        {
            EventId = eventId,
            EventType = eventType,
            EventTime = eventTime,
            Payload = payload
        };

        State._buffer.Enqueue(customEvent);

        // we are adding an incomplete task to batch executer and returning this task.
        // this will block until task is completed when we call Flush();
        return _batchExecuter.SubmitNext();
    }
}
```

A facilidade dessa abordagem esconde o poder da empresa. Ao usar essa arquitetura, você obtém:

* Endereçamento de recursos independentes de local.
* Tamanho do pool ajustável baseado simplesmente na mudança do número de atores que atuam em nome de um recurso.
* Uso de pool coordenado do lado do cliente (conforme demonstrado) ou lado do servidor (imagine um único ator na frente de cada um desses pools na imagem).
* Adição de pool escalonável (basta adicionar atores que representam o novo recurso).
* Atores que podem armazenar resultados em cache dos recursos de back-end sob demanda ou pré-armazenar em cache usando um temporizador, conforme demonstrado anteriormente. Isso reduz a necessidade de acessar recursos de back-end.
* Expedição assíncrona eficiente.
* Um ambiente de codificação que é conhecido de qualquer desenvolvedor, não apenas por especialistas de middleware.

Esse padrão é comum em cenários onde os desenvolvedores têm recursos restritos que precisam no desenvolvimento. Ele também é comum quando os desenvolvedores criam sistemas amplos de escala horizontal.


## Próximas etapas

[Padrão: cache inteligente](service-fabric-reliable-actors-pattern-smart-cache.md)

[Padrão: redes e gráficos distribuídos](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Padrão: composição de serviço com estado](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Padrão: Internet das Coisas](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Padrão: computação distribuída](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Alguns antipadrões](service-fabric-reliable-actors-anti-patterns.md)

[Introdução aos Reliable Actors do Service Fabric](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch2.png
[3]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch3.png

<!---HONumber=AcomDC_0121_2016-->