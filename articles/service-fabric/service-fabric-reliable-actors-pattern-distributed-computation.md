<properties
   pageTitle="Padrão de computação distribuída de Atores Confiáveis"
   description="Os Atores Confiáveis do Service Fabric funcionam bem com mensagens assíncronas paralelas, estado distribuído facilmente gerenciado e computação paralela."
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
   ms.author="claudioc"/>

# Padrão de design de Atores Confiáveis: computação distribuída
Devemos isso, em parte, ao fato de termos observado um cliente real produzir rapidamente um cálculo financeiro nos Atores Confiáveis do Service Fabric em um tempo absurdamente pequeno, para ser mais exato, o cálculo de risco de uma simulação de Monte Carlo.

A princípio, especialmente para aqueles que não possuem conhecimento específico do domínio, a manipulação da Malha de Serviço do Azure desse tipo de carga de trabalho, em contraposição às abordagens mais tradicionais, como o Mapear/Reduzir ou MPI, talvez não seja óbvia.

Mas acontece que a Malha de Serviços do Azure é uma boa opção com mensagens assíncronas paralelas, estado distribuído facilmente gerenciado e computação paralela, como mostra o diagrama a seguir:

![][1]

No exemplo a seguir, simplesmente calculamos Pi usando uma simulação de Monte Carlo. Temos os seguintes atores:

* Processador responsável por calcular Pi usando os atores do PoolTask.

* PoolTask responsável pela simulação de Monte Carlo e por enviar os resultados em agregador.

* Agregador responsável por agregar resultados e enviá-los ao Finaliser.

* O Finaliser é responsável por calcular o resultado final e exibir na tela.

## Exemplo de código de computação distribuída – simulação de Monte Carlo

```csharp
public interface IProcessor : IActor
{
    Task ProcessAsync(int tries, int seed, int taskCount);
}

public class Processor : Actor, IProcessor
{
    public Task ProcessAsync(int tries, int seed, int taskCount)
    {
        var tasks = new List<Task>();
        for (int i = 0; i < taskCount; i++)
        {
            var task = ActorProxy.Create<IPooledTask>(0); // stateless
            tasks.Add(task.CalculateAsync(tries, seed));
        }
        return Task.WhenAll(tasks);
    }
}

public interface IPooledTask : IActor
{
    Task CalculateAsync(int tries, int seed);
}

public class PooledTask : Actor, IPooledTask
{
    public Task CalculateAsync(int tries, int seed)
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

        var agg = ActorProxy.Create<IAggregator>(0);
        return agg.AggregateAsync(pi);
    }
}
```

Uma maneira comum de resultados de agregação na Malha de Serviço do Azure é usar temporizadores. Estamos usando atores sem monitoração de estado por dois motivos principais: o tempo de execução decidirá quantos agregadores são necessários dinamicamente, fornecendo-nos a escala sob demanda, portanto; e ele instanciará esses atores "localmente" – em outras palavras, no mesmo silo de ator da chamada, reduzindo saltos de rede. Apresentamos aqui a aparência do Agregador e do Finaliser:

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

public class Aggregator : Actor<AggregatorState>, IAggregator
{
    public override Task OnActivateAsync()
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

public class Finaliser : Actor<FinalizerState>, IFinaliser
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

        return TaskDone.Done;
    }
}
```

Neste ponto, deve estar claro como podemos aprimorar potencialmente o exemplo de placar de líderes com um agregador para dimensionamento e desempenho.

Não estamos de forma alguma afirmando que a Malha de Serviço do Azure seja uma substituição para outra computação distribuída de estruturas de big data ou de computação de alto desempenho. Existem algumas coisas que somente são criadas para oferecer melhor manipulação melhor do que outras. No entanto, é possível modelar fluxos de trabalho e computação paralela distribuída na Malha de Serviço do Azure e ainda ter a simplicidade de benefícios que ela oferece.

## Próximas etapas
[Padrão: cache inteligente](service-fabric-reliable-actors-pattern-smart-cache.md)

[Padrão: redes e gráficos distribuídos](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Padrão: controle de recursos](service-fabric-reliable-actors-pattern-resource-governance.md)

[Padrão: composição de serviço com estado](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Padrão: Internet das Coisas](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Alguns antipadrões](service-fabric-reliable-actors-anti-patterns.md)

[Introdução aos Atores da Malha do Serviço](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-distributed-computation/distributed-computation-1.png

<!---HONumber=August15_HO6-->