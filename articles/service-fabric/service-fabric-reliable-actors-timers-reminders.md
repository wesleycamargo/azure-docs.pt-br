<properties
   pageTitle="Temporizadores e lembretes de Reliable Actors | Microsoft Azure"
   description="Introdução a temporizadores e lembretes para Reliable Actors do Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="myamanbh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/15/2016"
   ms.author="amanbha"/>


# Temporizadores de ator
Os temporizadores de ator oferecem um wrapper simples em torno de temporizadores .NET, de modo que os métodos de retorno de chamada respeitam as garantias de simultaneidade baseada em turnos fornecidas pelo tempo de execução dos Atores.

Os atores podem usar os métodos `RegisterTimer` e `UnregisterTimer` em sua classe de base para registrar e cancelar o registro em seus temporizadores. O exemplo a seguir mostra o uso de APIs de temorizador. Os APIs são muito semelhantes ao temporizador do .NET. No exemplo, quando o temporizador chega ao fim, o tempo de execução dos Atores chamará o método `MoveObject`. É garantido que o método respeitará a simultaneidade baseada em turnos. Isso significa que nenhum outro método de ator ou retornos de chamada de temporizador/lembrete estará em andamento até que a execução desse retorno de chamada seja concluída.

```csharp
class VisualObjectActor : StatefulActor<VisualObject>, IVisualObject
{
    private IActorTimer _updateTimer;

    protected override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // Callback method
            state,                          // State to pass to the callback method
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

No caso de o ator ser um ator com monitoração de estado, o tempo de execução de Atores salva o estado do ator quando o retorno de chamada é concluído, como no exemplo acima. Se ocorrer um erro ao salvar o estado, esse objeto de ator será desativado e uma nova instância será ativada. Um método de retorno de chamada que não modifica o estado de ator pode ser registrado como um retorno de chamada do temporizador no modo somente leitura, especificando o atributo Readonly no retorno de chamada do temporizador. Isso é descrito na seção sobre [Métodos Readonly](service-fabric-reliable-actors-introduction.md#readonly-methods).

Todos os temporizadores são interrompidos quando o ator é desativado como parte da coleta de lixo. Nenhum retorno de chamada de temporizador é chamado depois disso. Além disso, o tempo de execução de atores não retém todas as informações sobre os temporizadores que estavam em execução antes de desativação. Cabe ao ator registrar todos os temporizadores necessários quando ele for reativado no futuro. Para obter mais informações, consulte a seção sobre [coleta de lixo de ator](service-fabric-reliable-actors-lifecycle.md).

## Lembretes de ator
Os lembretes são um mecanismo para disparar chamadas de retorno persistentes em um ator em horários específicos. Sua funcionalidade é semelhante à dos temporizadores. Mas, ao contrário dos temporizadores, os lembretes são acionados em todas as circunstâncias até que o ator cancele o registro deles explicitamente. Especificamente, os lembretes são acionados em todas as desativações e failovers ator porque o tempo de execução de atores retém as informações sobre os lembretes do ator.

Os lembretes têm suporte apenas para atores com monitoração de estado. Atores sem monitoração de estado não podem usar lembretes. Os provedores de estado do ator são responsáveis por armazenar informações sobre os lembretes que foram registradas por atores.

Para registrar um lembrete, um ator chama o método `RegisterReminder` fornecido na classe base, como mostrado no exemplo a seguir.

```csharp
string task = "Pay cell phone bill";
int amountInDollars = 100;
Task<IActorReminder> reminderRegistration = RegisterReminder(
                                                task,
                                                BitConverter.GetBytes(amountInDollars),
                                                TimeSpan.FromDays(3),
                                                TimeSpan.FromDays(1),
                                                ActorReminderAttributes.None);
```

No exemplo acima, `"Pay cell phone bill"` é o nome do lembrete. Essa é uma cadeia de caracteres usada pelo ator para identificar exclusivamente um lembrete. `BitConverter.GetBytes(amountInDollars)` é o contexto que está associado ao lembrete. Ele será devolvido para o ator como um argumento para o retorno de chamada do lembrete, ou seja, `IRemindable.ReceiveReminderAsync`.

Os atores que usam lembretes devem implementar a interface `IRemindable`, conforme mostrado no exemplo a seguir.

```csharp
public class ToDoListActor : StatefulActor<ToDoList>, IToDoListActor, IRemindable
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

Quando um lembrete é disparado, o tempo de execução de Atores do Fabric invocará o método `ReceiveReminderAsync` no Ator. Um ator pode registrar vários lembretes, que o método `ReceiveReminderAsync` será chamado quando qualquer um desses lembretes for disparado. O ator pode usar o nome de lembrete que é passado para o método `ReceiveReminderAsync` para descobrir qual lembrete foi acionado.

O tempo de execução dos atores salva o estado do ator quando a chamada `ReceiveReminderAsync` é concluída. Se ocorrer um erro ao salvar o estado, esse objeto de ator será desativado e uma nova instância será ativada. Para especificar que o estado não precisa ser salvo após a conclusão do retorno de chamada do lembrete, o sinalizador `ActorReminderAttributes.ReadOnly` pode ser definido no parâmetro dos `attributes` quando o método `RegisterReminder` for chamado para criar o lembrete.

Para cancelar o registro de um lembrete, um ator chama o método `UnregisterReminder` conforme mostrado no exemplo abaixo.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminder(reminder);
```

Como mostrado acima, o método `UnregisterReminder` aceita uma interface `IActorReminder`. A classe base de ator oferece suporte a um método `GetReminder`, que pode ser usado para recuperar a interface `IActorReminder` passando-se o nome do lembrete. Isso é conveniente porque o ator não precisa manter a interface `IActorReminder` que foi retornada com a chamada do método `RegisterReminder`.

<!---HONumber=AcomDC_0316_2016-->