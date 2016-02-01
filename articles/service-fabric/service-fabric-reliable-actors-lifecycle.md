<properties
   pageTitle="Ciclo de vida de Reliable Actors | Microsoft Azure"
   description="Explica o ciclo de vida e a coleta de lixo para Reliable Actors do Service Fabric"
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
   ms.date="01/20/2016"
   ms.author="amanbha"/>


# Ciclo de vida do ator e coleta de lixo
Um ator é ativado quando a primeira chamada é feita a ele. Um ator é desativado (lixo coletado pelo tempo de execução dos Atores) se ele não for usado por algum tempo. Para configurar esse período de tempo, confira a seção na Coleta de Lixo de Ator abaixo.

O que acontece na ativação do ator?

- Quando uma chamada chega para um ator e ele ainda não está ativo, é criado um novo ator.
- Seu estado é carregado (se for um ator com monitoração de estado).
- O método `OnActivateAsync` (que pode ser substituído na implementação do ator) é chamado.
- Ele é adicionado a uma tabela de Atores Ativos.

O que acontece na desativação do ator?

- Quando um ator não é usado por algum tempo, ele é removido da tabela de Atores Ativos.
- O método `OnDeactivateAsync` (que pode ser substituído na implementação do ator) é chamado. Isso limpa todos os medidores de tempo do ator.

> [AZURE.TIP]O tempo de execução da malha atores emite alguns eventos de [ relacionados à desativação e ativação de ator](service-fabric-reliable-actors-diagnostics.md#actor-activation-and-deactivation-events). Eles são úteis para diagnóstico e monitoramento de desempenho.

## Coleta de Lixo de Ator
O tempo de execução de Atores verifica periodicamente os atores que não foram usados durante um determinado período de tempo e os desativa. Depois que os atores são desativados, eles podem ter o lixo coletado pelo common language runtime (CLR).

O que conta como "está sendo usado" para fins de coleta de lixo?

- Recebimento de chamadas
- O método `IRemindable.ReceiveReminderAsync` que está sendo invocado (aplicável somente se o ator usar lembretes).

Vale a pena notar que, se o ator usa temporizadores e o temporizador de seu retorno de chamada é chamado, ele **não** conta como "está sendo usado".

Antes de entrar nos detalhes da coleta de lixo, é importante definir os seguintes termos:

- *Intervalo de verificação*. É o intervalo no qual o tempo de execução dos Atores verifica a tabela de Atores Ativos para saber se há atores que podem ter o lixo coletado. O valor padrão é 1 minuto.
- *Tempo limite de ociosidade*. Este é o período de tempo que um ator deve permanecer sem utilização (ocioso) antes que possa ter o lixo coletado. O valor padrão é 60 minuto.

Normalmente não é necessário alterar esses padrões. No entanto, se necessário, esses intervalos podem ser alterados em nível de assembly para todos os tipos de ator nesse assembly ou no nível do tipo do ator usando o atributo `ActorGarbageCollection`. O exemplo a seguir mostra a alteração nos intervalos de coleta de lixo para HelloActor.

```csharp
[ActorGarbageCollection(IdleTimeoutInSeconds = 10, ScanIntervalInSeconds = 2)]
class HelloActor : Actor, IHello
{
    public Task<string> SayHello(string greeting)
    {
        return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
    }
}
```

Para alterar o valor padrão do atributo `ActorGarbageCollection` no nível do assembly, adicione o seguinte trecho de código para `AssemblyInfo.cs`.

```csharp
[assembly: ActorGarbageCollection(IdleTimeoutInSeconds = 10, ScanIntervalInSeconds = 2)]
```

Para cada ator em sua tabela de Atores Ativo, o tempo de execução dos Atores controla quanto tempo que ele permanece ocioso (ou seja, não usado). O tempo de execução dos Atores verifica cada um dos atores a cada `ScanIntervalInSeconds` para saber se ele pode ser lixo coletado e coleta-o se ficar ocioso por `IdleTimeoutInSeconds`.

Sempre que um ator é usado, seu tempo ocioso é redefinido como 0. Depois disso, o ator só pode ter seu lixo coletado se permanecer ocioso novamente por `IdleTimeoutInSeconds`. Lembre-se de que um ator é considerado como usado se o método de interface ator ou um retorno de chamada de lembrete de ator for executado. Um ator **não** é considerado usado se seu retorno de chamada do temporizador for executado.

O diagrama abaixo contém um exemplo para ilustrar esses conceitos.

![Exemplo de tempo ocioso][1]

O exemplo supõe que há apenas um ator ativo na tabela de Atores Ativos. O exemplo mostra o impacto das chamadas de método de ator, lembretes e medidores de tempo no tempo de vida desse ator. Vale a pena mencionar os pontos a seguir sobre o exemplo:

- ScanInterval e IdleTimeout são definidos como 5 e 10, respectivamente. (As unidades não importam aqui, pois nosso objetivo é apenas para ilustrar o conceito.)
- A verificação de atores para terem o lixo coletado ocorre em T = 0, 5, 10, 15, 20, 25, conforme definido pelo ScanInterval de 5.
- Um medidor de tempo periódico é acionado em T = 4, 8, 12, 16, 20, 24 e executa seu retorno de chamada. Ela não afeta o tempo ocioso do ator.
- Uma chamada de método de ator em T = 7, redefine o tempo ocioso para 0 e atrasa a coleta de lixo do ator.
- Um retorno de chamada de lembrete de ator é executado a cada T = 14 e atrasa ainda mais a coleta de lixo do ator.
- Durante a verificação de coleta de lixo em T = 25, o tempo ocioso do ator finalmente excede o IdleTimeout de 10 e o ator tem seu lixo coletado.

Observe que um ator nunca terá seu lixo coletado durante a execução de um de seus métodos, não importa quanto tempo seja gasto na execução do método. Como mencionado anteriormente, a execução de métodos de interface de ator e retornos de chamada de lembrete impede a coleta de lixo, redefinindo o tempo ocioso do ator como 0. A execução de retornos de chamada do temporizador não redefine o tempo ocioso para 0. No entanto, a coleta de lixo do ator é adiada até que o retorno de chamada do temporizador tenha concluído a execução.

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png

<!---HONumber=AcomDC_0121_2016-->