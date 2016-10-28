<properties
   pageTitle="Temporizadores e lembretes de Reliable Actors | Microsoft Azure"
   description="Introdução a temporizadores e lembretes para Reliable Actors do Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="amanbha"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/06/2016"
   ms.author="vturecek"/>


# Lembretes e temporizadores de ator
Os atores podem agendar o trabalho periódico neles mesmos ao registrarem temporizadores ou lembretes. Este artigo mostra como usar temporizadores e lembretes e explica as diferenças entre eles.

## Temporizadores de ator
Os temporizadores de ator oferecem um wrapper simples em torno de temporizadores .NET para garantir que os métodos de retorno de chamada respeitem as garantias de simultaneidade baseada em turnos fornecidas pelo tempo de execução dos Atores.

Os atores podem usar os métodos `RegisterTimer` e `UnregisterTimer` em sua classe de base para registrar e cancelar o registro em seus temporizadores. O exemplo a seguir mostra o uso de APIs de temorizador. Os APIs são muito semelhantes ao temporizador do .NET. Neste exemplo, quando o temporizador chega ao fim, o tempo de execução dos Atores chamará o método `MoveObject`. É garantido que o método respeitará a simultaneidade baseada em turnos. Isso significa que nenhum outro método de ator ou retornos de chamada de temporizador/lembrete estará em andamento até que a execução desse retorno de chamada seja concluída.

```csharp
class VisualObjectActor : Actor, IVisualObject
{
    private IActorTimer _updateTimer;

    protected override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // Callback method
            null,                           // Parameter to pass to the callback method
            TimeSpan.FromMilliseconds(15),  // Amount of time to delay before the callback is invoked
            TimeSpan.FromMilliseconds(15)); // Time interval between invocations of the callback method

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
    {
        if (_updateTimer != null)
        {
            UnregisterTimer(_updateTimer);
        }

        return base.OnDeactivateAsync();
    }

    private Task MoveObject(object state)
    {
        ...
        return Task.FromResult(true);
    }
}
```

O período seguinte do temporizador é iniciado depois que o retorno de chamada concluir a execução. Isso significa que o temporizador será interrompido enquanto o retorno de chamada estiver em execução será iniciado quando o retorno de chamada for concluído.

O tempo de execução dos Atores salva as alterações feitas ao Gerenciador de Estado do ator quando o retorno de chamada é concluído. Se ocorrer um erro ao salvar o estado, esse objeto de ator será desativado e uma nova instância será ativada.

Todos os temporizadores são interrompidos quando o ator é desativado como parte da coleta de lixo. Nenhum retorno de chamada de temporizador é chamado depois disso. Além disso, o tempo de execução de atores não retém todas as informações sobre os temporizadores que estavam em execução antes de desativação. Cabe ao ator registrar todos os temporizadores necessários quando ele for reativado no futuro. Para obter mais informações, consulte a seção sobre [coleta de lixo de ator](service-fabric-reliable-actors-lifecycle.md).

## Lembretes de ator
Os lembretes são um mecanismo para disparar chamadas de retorno persistentes em um ator em horários específicos. Sua funcionalidade é semelhante à dos temporizadores. Mas, ao contrário dos temporizadores, os lembretes são acionados em todas as circunstâncias até que o ator cancele o registro deles explicitamente ou até que o ator seja explicitamente excluído. Especificamente, os lembretes são acionados em todas as desativações e failovers ator porque o tempo de execução de atores retém as informações sobre os lembretes do ator.

Para registrar um lembrete, um ator chama o método `RegisterReminderAsync` fornecido na classe base, como mostrado no exemplo a seguir:

```csharp
protected override async Task OnActivateAsync()
{
    string reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    IActorReminder reminderRegistration = await this.RegisterReminderAsync(
        reminderName,
        BitConverter.GetBytes(amountInDollars),
        TimeSpan.FromDays(3),
        TimeSpan.FromDays(1));
}
```

Neste exemplo, `"Pay cell phone bill"` é o nome do lembrete. Essa é uma cadeia de caracteres usada pelo ator para identificar exclusivamente um lembrete. `BitConverter.GetBytes(amountInDollars)` é o contexto que está associado ao lembrete. Ele será devolvido para o ator como um argumento para o retorno de chamada do lembrete, ou seja, `IRemindable.ReceiveReminderAsync`.

Os atores que usam lembretes devem implementar a interface `IRemindable` como mostrado no exemplo a seguir.

```csharp
public class ToDoListActor : Actor, IToDoListActor, IRemindable
{
    public Task ReceiveReminderAsync(string reminderName, byte[] context, TimeSpan dueTime, TimeSpan period)
    {
        if (reminderName.Equals("Pay cell phone bill"))
        {
            int amountToPay = BitConverter.ToInt32(context, 0);
            System.Console.WriteLine("Please pay your cell phone bill of ${0}!", amountToPay);
        }
        return Task.FromResult(true);
    }
}
```

Quando um lembrete é disparado, o tempo de execução dos Reliable Actors invocará o método `ReceiveReminderAsync` no Ator. Um ator pode registrar vários lembretes, que o método `ReceiveReminderAsync` será chamado quando qualquer um desses lembretes for disparado. O ator pode usar o nome de lembrete que é passado para o método `ReceiveReminderAsync` para descobrir qual lembrete foi acionado.

O tempo de execução dos Atores salva o estado do ator quando a chamada `ReceiveReminderAsync` é concluída. Se ocorrer um erro ao salvar o estado, esse objeto de ator será desativado e uma nova instância será ativada.

Para cancelar o registro de um lembrete, um ator chama o método `UnregisterReminder` como mostrado no exemplo abaixo.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminder(reminder);
```

Como mostrado acima, o método `UnregisterReminder` aceita uma interface `IActorReminder`. A classe base de ator oferece suporte a um método `GetReminder`, que pode ser usado para recuperar a interface `IActorReminder` passando-se o nome do lembrete. Isso é conveniente porque o ator não precisa manter a interface `IActorReminder` que foi retornada com a chamada do método `RegisterReminder`.

## Próximas etapas
 - [Eventos de ator](service-fabric-reliable-actors-events.md)
 - [Reentrância de ator](service-fabric-reliable-actors-reentrancy.md)
 - [Diagnóstico e monitoramento de desempenho do ator](service-fabric-reliable-actors-diagnostics.md)
 - [Documentação de referência da API do Ator](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Exemplo de código](https://github.com/Azure/servicefabric-samples)

<!---HONumber=AcomDC_0713_2016-->