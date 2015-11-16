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
   ms.date="08/11/2015"
   ms.author="vturecek"/>

# Padrão de design de Atores Confiáveis: governança de recursos

Esse padrão e cenários relacionados podem ser facilmente reconhecidos pelos desenvolvedores (corporativos ou não) que têm recursos restritos no local ou na nuvem, os quais não podem dimensionar imediatamente, ou que desejam enviar aplicativos e dados em larga escala para a nuvem.

Na empresa, esses recursos restritos, como bancos de dados, são executados em hardware de escala vertical. Qualquer pessoa com um longo histórico corporativo sabe que essa é uma situação comum no local. Mesmo em escala de nuvem, temos visto essa situação ocorrer quando um serviço de nuvem tenta exceder o limite de conexões TCP de 64 K entre uma tupla de endereço/porta ou ao tentar se conectar a um banco de dados baseado em nuvem que limita o número de conexões simultâneas.

No passado, geralmente esse problema era resolvido pela limitação por meio de middleware baseado em mensagem ou por mecanismos de fachada e pooling personalizados. Não é fácil trabalhar com esses recursos, especialmente quando precisamos escalar a camada intermediária, mas ainda manter as contagens corretas de conexão. É algo frágil e complexo.

Na verdade, como o padrão de Cache Inteligente, esse padrão abrange vários cenários e clientes que já vêm trabalhando em sistemas com recursos restritos. Eles estão criando sistemas onde precisam escalar horizontalmente, não apenas os serviços, mas seu estado na memória, bem como o estado persistente no armazenamento estável.

O diagrama abaixo ilustra esse cenário:

![][1]

## Modelando cenários de cache com atores

Basicamente, modelamos o acesso a recursos como um ator ou vários atores que atuam como proxies (digamos conexão, por exemplo) para um recurso ou um grupo de recursos. Você pode gerenciar diretamente o recurso por meio de atores individuais ou usar um ator de coordenação que gerencia os atores de recurso. Para tornar isso mais concreto, falaremos sobre a necessidade comum de ter que trabalhar em uma camada de armazenamento particionada (também conhecida como fragmentada) por motivos de desempenho e escalabilidade. Nossa primeira opção é bem básica: podemos usar uma função estática para mapear e resolver os atores para recursos de downstream. Tal função pode retornar, por exemplo, uma cadeia de conexão com determinada entrada. Cabe inteiramente a nós como implementar essa função. Obviamente, essa abordagem tem suas desvantagens, como afinidade estática, que dificulta bastante o reparticionamento de recursos ou remapeamento de um ator para recursos. Temos aqui um exemplo bastante simples — fazemos aritmética modular para determinar o nome do banco de dados usando userId e usamos região para identificar o servidor do banco de dados.

## Exemplo de código do Controle de Recursos – resolução estática

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

Simples, mas não muito flexível. Agora vamos examinar uma abordagem mais avançada e útil. Primeiro, modelamos a afinidade entre recursos físicos e atores. Isso é feito por meio de um ator chamado Resolvedor que compreende o mapeamento entre usuários, partições lógicas e recursos físicos. O Resolvedor mantém seus dados em um repositório persistente, no entanto, eles são armazenados em cache para pesquisa fácil. Como vimos anteriormente no exemplo de Taxa de Câmbio no padrão de Cache Inteligente, o Resolvedor pode buscar de modo proativo as informações mais recentes usando um temporizador. Depois que o ator do usuário determina o recurso que precisa usar, ele o armazena em cache em uma variável local chamada \_resolution e o utiliza durante seu tempo de vida. Escolhemos uma resolução baseada em pesquisa (ilustrada abaixo) em vez de um hash simples ou hash de intervalo em razão da flexibilidade fornecida em operações como expandir/reduzir ou mover um usuário de um recurso para outro.

![][2]

Na ilustração acima, vemos que o ator B23 resolve seu recurso primeiro (também conhecido como resolução) —DB1 e o armazena. Agora as operações subsequentes podem usar a resolução em cache para acessar o recurso restrito. Uma vez que os atores oferecem suporte à execução single-threaded, os desenvolvedores não precisam mais se preocupar com o acesso simultâneo ao recurso. Os atores de Usuário e Resolvedor se parecem com estes:

## Exemplo de código do Controle de Recursos – Resolvedor

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


public class User : Actor<UserState>, IUser
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

Controle de Recursos – exemplo de Resolvedor

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

public class Resolver : Actor<ResolverState>, IResolver
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

## Acessando recursos com capacidade finita

Agora vejamos outro exemplo; acesso exclusivo a recursos valiosos, como DB, contas de armazenamento e sistemas de arquivos com capacidade finita de taxa de transferência. Nosso cenário é o seguinte: queremos processar eventos usando um ator chamado EventProcessor, que é responsável por processar e persistir o evento, nesse caso, em um arquivo CSV por motivos de simplicidade. Embora possamos seguir a abordagem de particionamento discutida acima para escalar horizontalmente nossos recursos, ainda temos que lidar com os problemas de simultaneidade. Isso porque escolhemos um exemplo baseado em arquivo para ilustrar esse ponto específico — gravar em um único arquivo de vários atores levantará problemas de simultaneidade. Para resolver o problema, introduzimos outro ator chamado EventWriter que tem propriedade exclusiva dos recursos restritos. O cenário é ilustrado abaixo:

![][3]

Marcamos o ator EventProcessor como “Trabalhos sem Estado”, que permite que o tempo de execução os dimensione pelo cluster conforme a necessidade. Portanto, não usamos nenhum identificador na ilustração acima para esses atores. Em outras palavras, atores sem estado são um pool de trabalhos mantido pelo tempo de execução. No exemplo de código abaixo, o ator EventProcessor tem duas funções: primeiro ele decide qual EventWriter (portanto, recurso) usar e depois invoca o ator escolhido para gravar o evento processado. Para simplificar, estamos escolhendo Tipo de Evento como o identificador do ator EventWriter. Em outras palavras, haverá apenas um EventWriter para esse Tipo de Evento, fornecendo acesso single-threaded e exclusivo ao recurso.

## Exemplo de código do Controle de Recursos – processador de eventos

```csharp
public interface IEventProcessor : IActor
{
    Task ProcessEventAsync(long eventId, long eventType, DateTime eventTime, string payload);
}

public class EventProcessor : Actor, IEventProcessor
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

Agora vamos examinar o ator EventWriter. Ele realmente não faz muito coisa além de controlar o acesso exclusivo ao recurso restrito, nesse caso, o arquivo e os eventos de gravação nele.
## Exemplo de código do Controle de Recursos – gravador de eventos

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

public class EventWriter : Actor<EventWriterState>, IEventWriter
{
    private StreamWriter _writer;

    public override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.Id);
        _writer = new StreamWriter(_filename);

        return base.OnActivateAsync();
    }

    public override Task OnDeactivateAsync()
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

Ter um único ator responsável pelo recurso nos permite adicionar recursos como armazenamento em buffer. Podemos armazenar em buffer eventos de entrada e gravar esses eventos periodicamente usando um temporizador ou quando nosso buffer estiver cheio. Eis um exemplo simples baseado em temporizador:
## Exemplo de código do Controle de Recursos – gravador de eventos com buffer

```csharp
[DataMember]
public class EventWriterState
{
    [DataMember]
    public string _filename;
    [DataMember]
    public Queue<CustomEvent> _buffer;
}

public class EventWriter : Actor<EventWriterState>, IEventWriter
{
    private StreamWriter _writer;
    private IActorTimer _timer;

    public override Task OnActivateAsync()
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

Embora o código acima funcione bem, os clientes não saberão se seu evento foi realizado no repositório subjacente. Para permitir o armazenamento em buffer e que os clientes fiquem cientes do que está acontecendo com a respectiva solicitação, introduzimos a seguinte abordagem para permitir que os clientes esperem até que o evento seja gravado no arquivo .CSV:

## Exemplo de código do Controle de Recursos – envio em lote assíncrono

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

Usaremos essa classe para criar e manter uma lista de tarefas não concluídas (para bloquear clientes) e concluí-las de uma só vez depois que gravarmos os eventos em buffer no repositório. Na classe EventWriter, precisamos fazer três coisas: marcar a classe de ator como Reentrante, retornar o resultado de SubmitNext() e Liberar nosso temporizador. O código modificado é como se segue:

## Exemplo de código do Controle de Recursos – armazenamento em buffer com envio em lote assíncrono

```csharp
public class EventWriter : Actor<EventWriterState>, IEventWriter
{
    public override Task OnActivateAsync()
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

Parece fácil? E é. Mas a facilidade esconde o poder da empresa. Com essa arquitetura, obtemos:

* Endereçamento de recursos independentes de local.
* Tamanho do pool ajustável baseado simplesmente na mudança do número de atores que atuam em nome de um recurso.
* Uso de pool coordenado do lado do cliente (conforme demonstrado) ou lado do servidor (apenas imagine um único ator na frente de cada um desses pools na imagem).
* Adição de pool escalonável (basta adicionar atores representando o novo recurso).
* O ator (como demonstrado anteriormente) pode armazenar em cache os resultados de recursos de back-end sob demanda ou pré-armazenar em cache usando um temporizador, reduzindo a necessidade de alcançar o recurso de back-end.
* Expedição assíncrona eficiente.
* Um ambiente de codificação que será familiar a qualquer desenvolvedor, não apenas para os especialistas em middleware.

Esse padrão é muito comum em cenários onde os desenvolvedores têm recursos restritos para desenvolvimento ou estão criando amplos sistemas de escala vertical.


## Próximas etapas

[Padrão: cache inteligente](service-fabric-reliable-actors-pattern-smart-cache.md)

[Padrão: redes e gráficos distribuídos](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Padrão: composição de serviço com estado](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Padrão: Internet das Coisas](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Padrão: computação distribuída](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Alguns antipadrões](service-fabric-reliable-actors-anti-patterns.md)

[Introdução aos Atores da Malha do Serviço](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch2.png
[3]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch3.png

<!---HONumber=Nov15_HO2-->