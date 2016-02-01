<properties
   pageTitle="Padrão de computação distribuída | Microsoft Azure"
   description="Os Reliable Actors do Service Fabric funcionam bem com mensagens assíncronas paralelas, estado distribuído facilmente gerenciado e computação paralela."
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
   ms.date="11/14/2015"
   ms.author="vturecek"/>

# Padrão de design dos Reliable Actors: computação distribuída
Em parte, podemos dizer que isso se deve a um cliente real que criou um cálculo financeiro nos Reliable Actors do Service Fabric do Azure em um tempo ridiculamente curto. Foi uma simulação de Monte Carlo para calcular riscos.

Se você não tiver conhecimento específico do domínio, os benefícios do uso do Service Fabric para lidar com esse tipo de carga de trabalho em vez de uma abordagem mais tradicional (como MapReduce ou Message Passing Interface) poderão não ser imediatamente óbvios.

No entanto, o Service Fabric do Azure é uma boa opção com mensagens assíncronas paralelas, estado distribuído facilmente gerenciado e computação paralela, como mostra o diagrama a abaixo:

![Mensagens assíncronas paralelas, estado distribuído e computação paralela do Service Fabric][1]

No exemplo a seguir, simplesmente calculamos Pi usando uma simulação de Monte Carlo. Podemos empregar os seguintes atores:

* Um processador responsável por calcular Pi usando os atores da tarefa em pool.

* A tarefa em pool responsável pela simulação de Monte Carlo e por enviar os resultados a um agregador.

* Um agregador responsável por agregar resultados e enviá-los a um finalizador.

* O finalizador é responsável por calcular o resultado final e exibi-lo na tela.

## Exemplo de código de computação distribuída – simulação de Monte Carlo

```csharp
public interface IProcessor : IActor
{
    Task ProcessAsync(int tries, int seed, int taskCount);
}

public class Processor : StatelessActor, IProcessor
{
    public Task ProcessAsync(int tries, int seed, int taskCount)
    {
        var tasks = new List<Task>();
        ActorId aggregatorId = null;
        for (int i = 0; i < taskCount; i++)
        {
            var task = ActorProxy.Create<IPooledTask>(ActorId.NewId()); // stateless
            if (i % 2 == 0) // new aggregator for every 2 pooled actors
               aggregatorId = ActorId.NewId();
            tasks.Add(task.CalculateAsync(tries, seed, aggregatorId));
        }
        return Task.WhenAll(tasks);
    }
}

public interface IPooledTask : IActor
{
    Task CalculateAsync(int tries, int seed, ActorId aggregatorId);
}

public class PooledTask : StatelessActor, IPooledTask
{
    public Task CalculateAsync(int tries, int seed, ActorId aggregatorId)
    {
        var pi = new Pi()
        {
            InCircle = 0,
            Tries = tries
        };

        var random = new Random(seed);
        double x, y;
        for (int i = 0; i < tries; i++)
        {
            x = random.NextDouble();
            y = random.NextDouble();
            if (Math.Sqrt(x * x + y * y) <= 1)
                pi.InCircle++;
        }

        var agg = ActorProxy.Create<IAggregator>(aggregatorId);
        return agg.AggregateAsync(pi);
    }
}
```

Uma maneira comum de agregar resultados no Service Fabric do Azure é usar temporizadores. Estamos usando atores sem estado por dois motivos principais: o tempo de execução decidirá quantos agregadores são necessários dinamicamente, dando a escala sob demanda, e ele instanciará esses atores "localmente". Em outras palavras, isso ocorrerá no mesmo silo do ator da chamada, reduzindo saltos de rede.

Apresentamos aqui a aparência do agregador e do finalizador:

## Exemplo de código de computação distribuída – agregador

```csharp
public interface IAggregator : IActor
{
    Task AggregateAsync(Pi pi);
}

[DataContract]
class AggregatorState
{
    [DataMember]
    public Pi _pi;
    [DataMember]
    public bool _pending;
}

public class Aggregator : StatefulActor<AggregatorState>, IAggregator
{
    protected override Task OnActivateAsync()
    {
        State._pi = new Pi() { InCircle = 0, Tries = 0 };
        State._pending = false;

        this.RegisterTimer(
            ProcessAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessAsync(object obj)
    {
        if (false == _pending)
            return;

        var finaliser = ActorProxy.Create<IFinaliser>(0);
        await finaliser.FinaliseAsync(_pi);
        State._pending = false;
        State._pi.InCircle = 0;
        State._pi.Tries = 0;
    }

    public Task AggregateAsync(Pi pi)
    {
        State._pi.InCircle += pi.InCircle;
        State._pi.Tries += pi.Tries;
        State._pending = true;
        return TaskDone.Done;
    }
}

public interface IFinaliser : IActor
{
    Task FinaliseAsync(Pi pi);
}

[DataContract]
class FinalizerState
{
    [DataMember]
    public Pi _pi;
}

public class Finaliser : StatefulActor<FinalizerState>, IFinaliser
{
    public override Task OnActivateAsync()
    {
        State._pi = new Pi()
        {
            InCircle = 0,
            Tries = 0
        };

        return base.OnActivateAsync();
    }

    public Task FinaliseAsync(Pi pi)
    {
        State._pi.InCircle += pi.InCircle;
        State._pi.Tries += pi.Tries;
        Console.WriteLine(" Pi = {0:N9}  T = {1:N0}, {2}",(double)State._pi.InCircle / (double)State._pi.Tries * 4.0, State._pi.Tries, State._pi.InCircle);

        return Task.FromResult(true);
    }
}
```

Neste ponto, deve estar claro como você pode aprimorar o exemplo de placar de líderes com um agregador para dimensionamento e desempenho.

Não estamos de forma alguma afirmando que o Service Fabric do Azure seja uma substituição de outra computação distribuída de estruturas de big data ou de computação de alto desempenho. Ele foi criado para lidar melhor com algumas coisas do que com outras. No entanto, é possível modelar fluxos de trabalho e computação paralela distribuída no Service Fabric do Azure e ainda usufruir da simplicidade que ela oferece.

## Próximas etapas
[Padrão: cache inteligente](service-fabric-reliable-actors-pattern-smart-cache.md)

[Padrão: redes e gráficos distribuídos](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Padrão: governança de recursos](service-fabric-reliable-actors-pattern-resource-governance.md)

[Padrão: composição de serviço com estado](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Padrão: Internet das Coisas](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Alguns antipadrões](service-fabric-reliable-actors-anti-patterns.md)

[Introdução aos Reliable Actors do Service Fabric](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-distributed-computation/distributed-computation-1.png

<!---HONumber=AcomDC_0121_2016-->