<properties
   pageTitle="Visão geral dos Atores da Malha do Serviço do Azure"
   description="Introdução ao modelo de programação dos Atores da Malha do Serviço do Azure"
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
   ms.date="03/02/2015"
   ms.author="claudioc"/>

# Introdução aos Atores da Malha do Serviço do Azure
Os Atores da Malha do Serviço do Azure são um modelo de programação de Ator para a [Malha do Serviço](service-fabric-technical-overview.md). A Malha do Serviço é uma plataforma para a criação de aplicativos altamente confiáveis e escalonáveis para nuvem e locais que são fáceis de desenvolver e gerenciar.

Os Atores da Malha fornecem um modelo de ator single-threaded assíncrono. Um ator representa uma unidade de estado e computação. Os Atores são distribuídos por todo o cluster para atingir alta escalabilidade. Quando um processo que hospeda o ator falha, o sistema recria o ator em outro processo. Da mesma forma, quando o nó no qual o processo de host do ator está em execução falha, o sistema recria o ator em um processo de host em outro nó. O tempo de execução dos Atores aproveita o repositório distribuído fornecido pela plataforma subjacente da Malha do Serviço para fornecer gerenciamento de estado altamente disponível e consistente para os atores. Isso torna extremamente fácil o desenvolvimento e a manutenção de aplicativos em nuvem distribuídos com base em ator.

## Atores
Os atores são componentes single-threaded isolados que encapsulam estado e comportamento. Eles são semelhantes aos objetos .NET e, portanto, fornecem um modelo de programação natural para os desenvolvedores. Cada ator é uma instância de um tipo de ator, da mesma forma que um objeto .NET é uma instância de um tipo .NET. Por exemplo, pode haver um tipo de ator que implementa a funcionalidade de uma calculadora e pode haver muitos atores desse tipo que são distribuídos em vários nós em um cluster. Cada ator desse é exclusivamente identificado por uma ID de ator.

Os atores interagem com o restante do sistema, incluindo outros atores, passando mensagens assíncronas com padrão de solicitação-resposta. Essas interações são definidas em uma interface como métodos assíncronos. Por exemplo, a interface para um tipo de ator que implementa a funcionalidade de uma calculadora pode ser definida como se segue.

```csharp
public interface ICalculatorActor : IActor
{
    Task<double> AddAsync(double valueOne, double valueTwo);
    Task<double> SubtractAsync(double valueOne, double valueTwo);
}
```

Um tipo de ator pode implementar a interface acima da seguinte maneira:

```csharp
public class CalculatorActor : Actor, ICalculatorActor
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

Para cada método de interface, os argumentos e o tipo de resultado da Tarefa que ele retorna devem ser [contrato de dados serializável](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

A implementação desses métodos não envolve lidar com bloqueio ou outros problemas de simultaneidade, pois o tempo de execução dos Atores fornece a simultaneidade baseada em turno para os métodos de ator. Mais detalhes sobre isso se encontram na seção [Simultaneidade](#concurrency).

> [AZURE.TIP]O tempo de execução dos Atores da Malha emite alguns [eventos e contadores de desempenho relacionados aos métodos de ator](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Eles são úteis no diagnóstico e monitoramento de desempenho.

Vale a pena mencionar as regras a seguir pertencentes aos métodos de interface de ator: - Métodos de interface de ator não podem ser sobrecarregados. - Métodos de interface de ator não devem ter parâmetros out, ref ou opcionais.

## Comunicação do ator
A API do cliente de Atores fornece comunicação entre uma instância do ator e um cliente do ator. Para se comunicar com um ator, um cliente cria um objeto proxy de ator que implementa a interface do ator. O cliente interage com o ator invocando métodos no objeto proxy. O proxy de ator pode ser usado para comunicação entre cliente e ator, bem como entre ator e ator. Continuando nosso exemplo de calculadora, o código do cliente para um ator de calculadora pode ser escrito conforme mostrado abaixo:

```csharp
ActorId actorId = ActorId.NewId();
string applicationName = "fabric:/CalculatorActorApp";
ICalculatorActor calculatorActor = ActorProxy.Create<ICalculatorActor>(actorId, applicationName);
double result = calculatorActor.AddAsync(2, 3).Result;
```

Conforme mostrado no exemplo acima, duas informações foram usadas para criar o objeto proxy de ator — a ID do ator e o nome do aplicativo. A ID do ator é um identificador que identifica exclusivamente o ator. O nome do aplicativo é o nome do [aplicativo da Malha do Serviço](service-fabric-reliable-actors-platform.md#service-fabric-application-model-concepts-for-actors) de como o ator é implantado.

Os atores são atores virtuais, o que significa que eles sempre existem. Você não precisa criá-los explicitamente nem destruí-los. O tempo de execução dos Atores ativa automaticamente um ator na primeira vez que ele recebe uma solicitação para esse ator. Se um ator não for usado por determinado período, o tempo de execução dos Atores o removerá pela coleta de lixo e o ativará posteriormente, se necessário. Mais detalhes sobre isso podem ser encontrados na seção sobre [Ciclo de vida do ator e coleta de lixo](service-fabric-reliable-actors-lifecycle.md).

A API do cliente de Atores também fornece a transparência e o failover do local. A classe `ActorProxy` no lado do cliente executa a resolução necessária e localiza a [partição](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) de serviço do ator, onde o ator com a ID especificada é hospedado, e abre um canal de comunicação com ela. O `ActorProxy` é repetido nas falhas de comunicação e em caso de failovers. Isso significa que é possível para uma implementação do Ator obter mensagens duplicadas do mesmo cliente.

## Simultaneidade
O tempo de execução dos Atores fornece uma simultaneidade simples baseada em turno para métodos de ator. Isso significa que não é permitido mais de um thread ativo no código do ator a qualquer momento.

Um turno consiste na execução completa de um método de ator em resposta à solicitação de outros atores ou clientes, ou a execução completa de um retorno de chamada de [temporizador/lembrete](service-fabric-reliable-actors-timers-reminders.md). Mesmo que esses métodos e retornos de chamada sejam assíncronos, o tempo de execução dos Atores não os intercala. Um turno deve ser totalmente concluído antes que um novo turno seja permitido. Em outras palavras, um método de ator ou retorno de chamada de temporizador/lembrete que está em execução no momento deve ser concluído totalmente antes que uma nova chamada a um método ou um retorno de chamada seja permitido. Um método ou retorno de chamada será considerado concluído se a execução tiver sido retornada do método ou retorno de chamada, e a Tarefa retornada pelo método ou retorno de chamada tiver sido concluída. Vale enfatizar que a simultaneidade baseada em turno é respeitada mesmo entre métodos, temporizadores e retornos de chamada diferentes.

O tempo de execução dos Atores impõe simultaneidade baseada em turno adquirindo um bloqueio por ator no início de um turno e liberando o bloqueio no fim do turno. Desse modo, a simultaneidade baseada em turno é imposta por ator e não entre atores. Os métodos de ator e retornos de chamada de temporizador/lembrete podem ser executados simultaneamente em nome de diferentes atores.

O exemplo a seguir ilustra os conceitos acima. Considere um tipo de ator que implementa dois métodos assíncronos (por exemplo, *Method1* e *Method2*), um temporizador e um lembrete. O diagrama a seguir mostra um exemplo de uma linha do tempo para a execução desses métodos e retornos de chamada em nome de dois atores — *ActorId1* e *ActorId2* — que pertencem a esse tipo de ator.

![][1]

As convenções seguidas pelo diagrama acima são:

- Cada linha vertical mostra o fluxo lógico de execução de um método ou um retorno de chamada em nome de um ator específico.
- Os eventos marcados em cada linha vertical ocorrem em ordem cronológica com os eventos mais recentes ocorrendo abaixo dos mais antigos.
- As diferentes cores são usadas para linhas do tempo que correspondem a diferentes atores.
- O realce é usado para indicar por quanto tempo o bloqueio por ator é mantido em nome de um método ou retorno de chamada.

Vale a pena mencionar os pontos a seguir sobre o diagrama acima:

- Quando *Method1* está em execução em nome de *ActorId2* em resposta à solicitação do cliente *xyz789*, outra solicitação de cliente *abc123* chega, que também exige que *Method1* seja executado por *ActorId2*. No entanto, a última execução de *Method1* não começa até que a execução anterior seja concluída. Da mesma forma, um lembrete registrado por *ActorId2* é acionado enquanto *Method1* está sendo executado em resposta à solicitação do cliente *xyz789*. O retorno de chamada de lembrete é executado somente depois que ambas as execuções de *Method1* são concluídas. Tudo isso se deve à simultaneidade baseada em turno que está sendo imposta para *ActorId2*.
- Da mesma forma, a simultaneidade baseada em turno também é imposta para *ActorId1*, conforme demonstrado pela execução de *Method1*, *Method2* e o retorno de chamada de temporizador em nome de *ActorId1* acontecendo de maneira serial.
- A execução de *Method1* em nome de *ActorId1* é sobreposta por sua execução em nome de *ActorId2*. Isso porque a simultaneidade baseada em turno é imposta apenas em um ator, e não entre atores.
- Em algumas das execuções de método/retorno de chamada, a `Task` retornada pelo método/retorno de chamada é concluída depois que o método retorna. Em outros, a `Task` já foi concluída no momento em que o método/retorno de chamada retorna. Em ambos os casos, o bloqueio por ator é liberado apenas depois que ambos acontecem, isto é, depois que o método/retorno de chamada retorna e que a `Task` é concluída.

O tempo de execução dos Atores permite reentrância por padrão. Isso significa que se um método de ator do Ator A chamar o método no Ator B que, por sua vez, chama outro método no Ator A, esse método terá permissão para ser executado, pois faz parte do mesmo contexto lógico da cadeia de chamadas. Todas as chamadas de temporizador e lembrete começam com o novo contexto lógico de chamada. Consulte a seção [Reentrância](service-fabric-reliable-actors-reentrancy.md) para obter mais detalhes.

O tempo de execução dos Atores fornece essas garantias de simultaneidade em situações em que ele controla a invocação desses métodos. Por exemplo, ele fornece essas garantias para as invocações de método que são feitas em resposta ao recebimento de uma solicitação de cliente e para retornos de chamada de temporizador e lembrete. No entanto, se o código de ator envolver diretamente esses métodos fora dos mecanismos fornecidos pelo tempo de execução dos Atores, o tempo de execução não poderá fornecer nenhuma garantia de simultaneidade. Por exemplo, se o método for invocado no contexto de alguma Tarefa que não está associada à Tarefa retornada pelos métodos de ator, ou se ela for invocada de um thread que o ator cria por sua conta, o tempo de execução não poderá fornecer garantias de simultaneidade. Portanto, para executar operações em segundo plano, os atores devem usar [Temporizadores de Ator ou Lembretes de Ator](service-fabric-reliable-actors-timers-reminders.md) que respeitem a simultaneidade baseada em turno.

> [AZURE.TIP]O tempo de execução dos Atores da Malha emite alguns [eventos e contadores de desempenho relacionados à simultaneidade](service-fabric-reliable-actors-diagnostics.md#concurrency-events-and-performance-counters). Eles são úteis no diagnóstico e monitoramento de desempenho.

## Gerenciamento de estado do ator
Os Atores da Malha permitem que você crie os atores com ou sem estado.

### Atores sem estado
Os atores sem estado, como sugere o nome, não têm nenhum estado que seja gerenciado pelo tempo de execução dos Atores. Os atores sem estado derivam da classe base Ator. Eles podem ter variáveis de membro e essas serão preservadas por todo o tempo de vida desse ator, assim como qualquer outro tipo .NET. No entanto, se essa instância de ator for coletada pelo lixo depois de ficar ociosa por algum tempo, ela perderá o estado armazenado em suas variáveis de membro. Da mesma forma, o estado pode ser perdido devido a failovers, o que poderá ocorrer em situações como atualizações, operações de balanceamento de recursos, ou à falha de um processo de ator ou do nó que está hospedando o processo do ator.

Veja a seguir o exemplo de um ator sem estado.

```csharp
class HelloActor : Actor, IHello
{
    public Task<string> SayHello(string greeting)
    {
        return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
    }
}
```

### Atores com estado
Os atores com estado têm um estado que precisa ser preservado entre coletas de lixo e failovers do ator. Os atores com estado derivam da classe base `Actor<TState>`, onde `TState` é o tipo do estado que precisa ser preservado entre coletas de lixo e failovers. O estado pode ser acessado nos métodos de ator pela propriedade `State` na classe base `Actor<TState>`. Veja a seguir o exemplo de um ator com estado acessando o estado.

```csharp
class VoicemailBoxActor : Actor<VoicemailBox>, IVoicemailBoxActor
{
    public Task<List<Voicemail>> GetMessagesAsync()
    {
        return Task.FromResult(State.MessageList);
    }
    ...
}
```

O tipo do estado do ator deve ser [contrato de dados serializável](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

> [Observação] confira o artigo [Observações sobre Atores Confiáveis em serialização](service-fabric-reliable-actors-notes-on-actor-type-serialization.md) para obter mais detalhes sobre como as interfaces e os tipos de Estado do Ator devem ser definidos.

#### Provedores de estado do ator
O armazenamento e a recuperação do estado são fornecidos por um provedor de estado do ator. O provedor de estado pode ser configurado por ator ou para todos os atores em um assembly pelo atributo específico do provedor de estado. Há alguns provedores de estado padrão do ator que são incluídos no tempo de execução dos Atores. A durabilidade e a confiabilidade do estado são determinadas pelas garantias oferecidas pelo provedor de estado. Quando um ator é ativado, seu estado é carregado na memória. Quando um método de ator é concluído, o estado modificado é salvo automaticamente pelo tempo de execução dos Atores chamando um método no provedor de estado. Se ocorrerem falhas durante o estado de salvamento, o tempo de execução dos Atores reciclará essa instância do ator. Uma nova instância do Ator é criada e carregada com o último estado consistente do provedor de estado.

Por padrão, um ator com estado usa o provedor de estado do ator do repositório de chave-valor. Esse provedor de estado é criado no repositório de chave-valor distribuído fornecido pela plataforma da Malha do Serviço. Para obter mais informações, consulte o tópico sobre [opções do provedor de estado](service-fabric-reliable-actors-platform.md#actor-state-provider-choices).

> [AZURE.TIP]O tempo de execução dos Atores da Malha emite alguns [eventos e contadores de desempenho relacionados ao gerenciamento de estado do ator](service-fabric-reliable-actors-diagnostics.md#actor-state-management-events-and-performance-counters). Eles são úteis no diagnóstico e monitoramento de desempenho.

#### Métodos Readonly
Por padrão, o tempo de execução dos Atores salva o estado do Ator na conclusão de uma chamada de método de ator, no retorno de chamada de temporizador e no retorno de chamada de lembrete. Nenhuma outra chamada de ator é permitida até que o estado de salvamento seja concluído. Dependendo do provedor de estado, salvar o estado pode demorar e, durante esse tempo, nenhuma outra chamada de ator será permitida no ator.

Pode haver métodos de ator que não modifiquem o estado e, nesse caso, o tempo adicional gasto em salvar o estado pode afetar a taxa de transferência geral do sistema. Para evitar isso, você pode marcar os métodos e os retornos de chamada de temporizador que não modificam o estado como somente leitura.

O exemplo abaixo mostra como marcar um método de ator como somente leitura usando o atributo `Readonly`.

```csharp
public interface IVoicemailBoxActor : IActor
{
    [Readonly]
    Task<List<Voicemail>> GetMessagesAsync();
}
```

Os retornos de chamada de temporizador podem ser marcados com o atributo `Readonly` de maneira semelhante. Para lembretes, o sinalizador somente leitura é passado como um argumento para o método `RegisterReminder` que é invocado para registrar o lembrete.

## Próximas etapas
### Conceitos
[Ciclo de vida do ator e coleta de lixo](service-fabric-reliable-actors-lifecycle.md)

[Temporizadores e lembretes do ator](service-fabric-reliable-actors-timers-reminders.md)

[Eventos de ator](service-fabric-reliable-actors-events.md)

[Reentrância de ator](service-fabric-reliable-actors-reentrancy.md)

[Como os Atores da Malha usam a plataforma da Malha do Serviço](service-fabric-reliable-actors-platform.md)

[Configurando o ator KVSActorStateProvider](../Service-Fabric/service-fabric-reliable-actors-KVSActorstateprovider-configuration.md)

[Diagnóstico e monitoramento de desempenho do ator](service-fabric-reliable-actors-diagnostics.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
 

<!---HONumber=July15_HO2-->