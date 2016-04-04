<properties
   pageTitle="Visão Geral dos Reliable Actors do Service Fabric | Microsoft Azure"
   description="Introdução ao modelo de programação Reliable Actors do Service Fabric"
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

# Introdução aos Reliable Actors do Service Fabric
A API Reliable Actors é uma das duas estruturas de alto nível fornecidas pelo [Service Fabric](service-fabric-technical-overview.md), junto com a [API Reliable Services](service-fabric-reliable-services-introduction.md).

A API Reliable Actors, baseada no padrão de ator, fornece um modelo de programação assíncrono e em thread único que simplifica o código e ainda aproveita a confiabilidade e a escalabilidade fornecidas pelo Service Fabric.

## Atores
Os atores são componentes em thread único e isolados que encapsulam estado e comportamento. Eles são semelhantes aos objetos .NET; portanto, fornecem um modelo de programação natural. Cada ator é uma instância de um tipo de ator, da mesma forma que um objeto .NET é uma instância de um tipo .NET. Por exemplo, um tipo de ator pode implantar a funcionalidade de uma calculadora e pode haver muitos atores desse tipo que são distribuídos em vários nós em um cluster. Cada ator desse é exclusivamente identificado por uma ID de ator.

## Definindo e implementando interfaces de ator

Os atores interagem com o restante do sistema, incluindo outros atores, passando mensagens assíncronas usando um padrão de solicitação-resposta. Essas interações são definidas em uma interface como métodos assíncronos. Por exemplo, a interface para um tipo de ator que implementa a funcionalidade de uma calculadora pode ser definida assim:

```csharp
public interface ICalculatorActor : IActor
{
    Task<double> AddAsync(double valueOne, double valueTwo);
    Task<double> SubtractAsync(double valueOne, double valueTwo);
}
```

Um tipo de ator pode implementar esta interface da seguinte maneira:

```csharp
public class CalculatorActor : StatelessActor, ICalculatorActor
{
    public Task<double> AddAsync(double valueOne, double valueTwo)
    {
        return Task.FromResult(valueOne + valueTwo);
    }

    public Task<double> SubtractAsync(double valueOne, double valueTwo)
    {
        return Task.FromResult(valueOne - valueTwo);
    }
}
```

Já que as invocações de método e suas respostas resultam, no fim das contas, em solicitações de rede no cluster, os argumentos e o tipo de resultado das tarefas que elas retorna devem ser serializáveis pela plataforma. Em particular, eles devem ser [contrato de dados serializáveis](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

> [AZURE.TIP] O tempo de execução dos Atores do Service Fabric emitem alguns [eventos e contadores de desempenho relacionados aos métodos de ator](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Eles são úteis para diagnóstico e monitoramento de desempenho.

Vale a pena mencionar as seguintes regras que pertencem aos métodos de interface de ator:
- Métodos da interface de ator não podem ser sobrecarregados.
- Métodos da interface de ator não podem ter parâmetros de saída, de referência e opcionais.

## Comunicação do ator
### O proxy de ator
A API do cliente Reliable Actors permite a comunicação entre uma instância do ator e um cliente do ator. Para se comunicar com um ator, um cliente cria um objeto proxy de ator que implementa a interface do ator. O cliente interage com o ator invocando métodos no objeto proxy. O proxy de ator pode ser usado para a comunicação entre cliente e ator, bem como entre ator e ator. Continuando nosso exemplo de calculadora, o código do cliente para um ator de calculadora pode ser escrito conforme mostrado abaixo:

```csharp
ActorId actorId = ActorId.NewId();
string applicationName = "fabric:/CalculatorActorApp";
ICalculatorActor calculatorActor = ActorProxy.Create<ICalculatorActor>(actorId, applicationName);
double result = calculatorActor.AddAsync(2, 3).Result;
```

Observe que os dois conjuntos de informações usadas para criar o objeto de proxy de ator são a ID do ator e o nome do aplicativo. A ID de ator é um identificador exclusivo do ator, enquanto o nome do aplicativo identifica o [aplicativo do Service Fabric](service-fabric-reliable-actors-platform.md#service-fabric-application-model-concepts-for-actors) em que o ator foi implantado.

### Tempo de vida do ator

Os atores de Malha de Serviço são virtuais, o que significa que o tempo de vida não está associado à sua representação na memória. Como resultado, eles não precisam ser explicitamente criados ou destruídos. O tempo de execução dos Atores ativa automaticamente um ator na primeira vez que ele recebe uma solicitação para esse ator. Se um ator não é usado por um determinado tempo, o tempo de execução Atores irá coletar o lixo do objeto na memória. Ele também mantém a informação da existência do ator caso seja necessário reativá-lo mais tarde. Para obter mais detalhes, veja [Ciclo de vida do ator e coleta de lixo](service-fabric-reliable-actors-lifecycle.md).

### Transparência da localização e failover automático

Para fornecer alta escalabilidade e confiabilidade, a Malha de Serviço distribui atores em todo o cluster e os migra automaticamente de nós com falha para os nós íntegros conforme necessário. A classe `ActorProxy` no lado do cliente executa a resolução necessária para localizar o ator por [partição](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) de ID e abrir um canal de comunicação com ela. O `ActorProxy` também tenta novamente localizar o ator no caso de falhas de comunicação e failovers. Isso garante que as mensagens serão entregues com certeza mesmo quando houver falhas. Isso significa que é possível que uma implementação do ator obtenha mensagens duplicadas do mesmo cliente.

## Simultaneidade
### Acesso com base em vez

O tempo de execução dos Atores fornece um modelo baseado em vez simples para acessar os métodos de ator. Isso significa que não é permitido mais de um thread ativo no código do ator a qualquer momento.

Um turno consiste na execução completa de um método de ator em resposta a uma solicitação de outros atores ou clientes, ou a execução completa de um retorno de chamada de [temporizador/lembrete](service-fabric-reliable-actors-timers-reminders.md). Mesmo que esses métodos e retornos de chamada sejam assíncronos, o tempo de execução dos Atores não os intercala. Um turno deve ser totalmente concluído antes que um novo turno seja permitido. Em outras palavras, um método de ator ou retorno de chamada de temporizador/lembrete que está em execução no momento deve ser concluído totalmente antes que uma nova chamada a um método ou um retorno de chamada seja permitido. Um método ou retorno de chamada será considerado concluído se a execução tiver sido retornada do método ou retorno de chamada, e a Tarefa retornada pelo método ou retorno de chamada tiver sido concluída. Vale enfatizar que a simultaneidade baseada em turno é respeitada mesmo entre métodos, temporizadores e retornos de chamada diferentes.

O tempo de execução dos Atores impõe simultaneidade baseada em turno adquirindo um bloqueio por ator no início de um turno e liberando o bloqueio no fim do turno. Desse modo, a simultaneidade baseada em turno é imposta por ator e não entre atores. Os métodos de ator e retornos de chamada de temporizador/lembrete podem ser executados simultaneamente em nome de diferentes atores.

O exemplo a seguir ilustra os conceitos acima. Considere um tipo de ator que implementa dois métodos assíncronos (digamos, *Method1* e *Method2*), um temporizador e um lembrete. O diagrama abaixo mostra um exemplo de uma linha do tempo para a execução desses métodos e retornos de chamada em nome de dois atores (*ActorId1* e *ActorId2*) que pertencem a esse tipo de ator.

![Simultaneidade e acesso com base em turno do Reliable Actors][1]

O diagrama acima segue as seguintes convenções:

- Cada linha vertical mostra o fluxo lógico de execução de um método ou um retorno de chamada em nome de um ator específico.
- Os eventos marcados em cada linha vertical ocorrem em ordem cronológica com os eventos mais recentes ocorrendo abaixo dos mais antigos.
- As diferentes cores são usadas para linhas do tempo que correspondem a diferentes atores.
- O realce é usado para indicar por quanto tempo o bloqueio por ator é mantido em nome de um método ou retorno de chamada.

Vale a pena mencionar os pontos a seguir sobre o diagrama acima:

- Enquanto *Method1* está sendo em executado em nome de *ActorId2* em resposta à solicitação do cliente *xyz789*, outra solicitação de cliente *abc123* chega e também exige que *Method1* seja executado por *ActorId2*. No entanto, a segunda execução de *Method1* não é iniciada até que a execução anterior seja concluída. Da mesma forma, um lembrete registrado por *ActorId2* é acionado enquanto *Method1* está sendo executado em resposta à solicitação do cliente *xyz789*. O retorno de chamada de lembrete é executado somente depois que ambas as execuções de *Method1* são concluídas. Tudo isso se deve à simultaneidade baseada em turno que está sendo imposta para *ActorId2*.
- Da mesma forma, a simultaneidade baseada em turno também é imposta para *ActorId1*, como demonstrado pela execução de *Method1*, *Method2* e o retorno de chamada de temporizador em nome de *ActorId1* ocorrendo de maneira serial.
- A execução de *Method1* em nome de *ActorId1* é sobreposta por sua execução em nome de *ActorId2*. Isso porque a simultaneidade baseada em turno é imposta apenas em um ator, e não entre atores.
- Em algumas das execuções de método/retorno de chamada, a `Task` retornada pelo método/retorno de chamada é concluída depois que o método é retornado. Em outras, a `Task` já terá sido concluída quando o método/retorno de chamada for retornado. Em ambos os casos, o bloqueio por ator será liberado apenas depois que o método e o retorno de chamada forem retornados e `Task` for concluída.

### Reentrada

O tempo de execução dos Atores permite reentrância por padrão. Isso significa que se um método de ator do *Ator A* chamar um método no *Ator B* que, por sua vez, chamar outro método no *Ator A*, esse método terá permissão para ser executado. Isso ocorre porque ele faz parte do mesmo contexto lógico da cadeia de chamadas. Todas as chamadas de temporizador e lembrete começam com o novo contexto lógico de chamada. Veja a [Reentrância do Reliable Actors](service-fabric-reliable-actors-reentrancy.md) para obter mais detalhes.

### Escopo de garantias de simultaneidade

O tempo de execução dos Atores fornece essas garantias de simultaneidade em situações em que ele controla a invocação desses métodos. Por exemplo, ele fornece essas garantias para as invocações de método que são feitas em resposta à solicitação de cliente, bem como para retornos de chamada de temporizador e lembrete. No entanto, se o código de ator envolver diretamente esses métodos fora dos mecanismos fornecidos pelo tempo de execução dos Atores, o tempo de execução não poderá fornecer nenhuma garantia de simultaneidade. Por exemplo, se o método for invocado no contexto de alguma tarefa que não está associada à tarefa retornada pelos métodos de ator, o tempo de execução não poderá fornecer garantias de simultaneidade. Se o método for chamado de um thread criado pelo ator por conta própria, o tempo de execução também não poderá fornecer garantias de simultaneidade. Portanto, para executar operações em segundo plano, os atores devem usar [temporizadores de ator ou lembretes de ator](service-fabric-reliable-actors-timers-reminders.md) que respeitam a simultaneidade baseada em turno.

> [AZURE.TIP] O tempo de execução dos Atores do Service Fabric emite alguns [eventos e contadores de desempenho relacionados à simultaneidade](service-fabric-reliable-actors-diagnostics.md#concurrency-events-and-performance-counters). Eles são úteis para diagnóstico e monitoramento de desempenho.

## Gerenciamento de estado do ator
Você pode usar o Service Fabric para criar atores com ou sem estado.

### Atores sem estado
Atores sem monitoração de estado, que derivam da classe base `StatelessActor`, não têm nenhum estado que seja gerenciado pelo tempo de execução de Atores. Suas variáveis de membro são preservadas durante sua vida útil na memória, assim como qualquer outro tipo .NET. No entanto, quando eles são coletados como lixo após um período de inatividade, seu estado é perdido. Da mesma forma, o estado pode ser perdido devido a failovers, que ocorrem durante atualizações, operações de balanceamento de recursos ou como resultado de falhas no processo de ator ou no seu nó de hospedagem.

Veja abaixo o exemplo de um ator sem estado:

```csharp
class HelloActor : StatelessActor, IHello
{
    public Task<string> SayHello(string greeting)
    {
        return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
    }
}
```

### Atores com estado
Os atores com estado têm um estado que precisa ser preservado entre coletas de lixo e failovers. Eles derivam de `StatefulActor<TState>`, em que `TState` está o tipo de estado que deve ser preservado. O estado pode ser acessado nos métodos de ator pela propriedade `State` na classe base.

Veja abaixo um exemplo de um ator com estado acessando o estado:

```csharp
class VoicemailBoxActor : StatefulActor<VoicemailBox>, IVoicemailBoxActor
{
    public Task<List<Voicemail>> GetMessagesAsync()
    {
        return Task.FromResult(State.MessageList);
    }
    ...
}
```

O estado de ator é preservado em coletas de lixo e em failovers quando é persistido no disco e replicado entre vários nós no cluster. Isso significa que, assim como ocorre com argumentos de método e valores de retorno, o tipo de estado do ator deve ser [serializável de acordo com o contrato de dados](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

> [AZURE.NOTE] Consulte o artigo [Observações do Reliable Actors sobre serialização](service-fabric-reliable-actors-notes-on-actor-type-serialization.md) para obter mais detalhes sobre como as interfaces e os tipos de estado do Ator devem ser definidos.

#### Provedores de estado do ator
O armazenamento e a recuperação do estado são fornecidos por um provedor de estado do ator. Os provedores de estado podem ser configurados por ator ou para todos os atores em um assembly pelo atributo específico do provedor de estado. Quando um ator é ativado, seu estado é carregado na memória. Quando um método de ator é concluído, o tempo de execução dos Atores salva o estado modificado automaticamente chamando um método no provedor de estado. Se ocorrer uma falha durante a operação **Salvar**, o tempo de execução dos Atores criará uma nova instância de ator e carregará o último estado consistente por meio do provedor de estado.

Por padrão, atores de estado usam o provedor de estado de ator do repositório de chave-valor, que é criado no repositório de chave-valor distribuído fornecido pela plataforma de Malha de serviço. Para obter mais informações, confira o tópico sobre [opções do provedor de estado](service-fabric-reliable-actors-platform.md#actor-state-provider-choices).

> [AZURE.TIP] O tempo de execução de Atores emite alguns [contadores de desempenho e evento relacionados ao gerenciamento de estado do ator](service-fabric-reliable-actors-diagnostics.md#actor-state-management-events-and-performance-counters). Eles são úteis para diagnóstico e monitoramento de desempenho.

#### Métodos somente leitura
Por padrão, o tempo de execução dos Atores salva o estado do ator na conclusão de uma chamada de método de ator, no retorno de chamada de temporizador e no retorno de chamada de lembrete. Nenhuma outra chamada de ator é permitida até que o estado de salvamento seja concluído.

No entanto, pode haver métodos de ator que não modificam o estado. Nesse caso, o tempo adicional gasto salvando o estado pode afetar a taxa de transferência geral do sistema. Para evitar isso, você pode marcar os métodos e os retornos de chamada de temporizador que não modificam o estado como somente leitura.

Este exemplo mostra como marcar um método de ator como somente leitura usando o atributo `Readonly`:

```csharp
public interface IVoicemailBoxActor : IActor
{
    [Readonly]
    Task<List<Voicemail>> GetMessagesAsync();
}
```

Os retornos de chamada de temporizador podem ser marcados com o atributo `Readonly` de maneira semelhante. Para lembretes, o sinalizador somente leitura é passado como um argumento para o método `RegisterReminder` que é invocado para registrar o lembrete.

## Próximas etapas
[Ciclo de vida do ator e coleta de lixo](service-fabric-reliable-actors-lifecycle.md)

[Lembretes e temporizadores de ator](service-fabric-reliable-actors-timers-reminders.md)

[Eventos de ator](service-fabric-reliable-actors-events.md)

[Reentrância de ator](service-fabric-reliable-actors-reentrancy.md)

[Como os Reliable Actors usam a plataforma do Service Fabric](service-fabric-reliable-actors-platform.md)

[Configurar o ator KVSActorStateProvider](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)

[Diagnóstico e monitoramento de desempenho do ator](service-fabric-reliable-actors-diagnostics.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png

<!---HONumber=AcomDC_0323_2016-->