<properties
   pageTitle="Notificações de Serviços Confiáveis | Microsoft Azure"
   description="Documentação conceitual para Notificações de Serviço Confiável do Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="masnider,vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/24/2016"
   ms.author="mcoskun"/>

# Notificações de serviço confiável

As notificações permitem que os clientes controle as alterações que estão sendo feitas no objeto no qual eles estão interessados. Há dois tipos de objeto que dão suporte à notificação: **Gerenciador de Estado Confiável** e **Dicionário Confiável**.

Os motivos comuns para usar as Notificações são

- Criação de modos de exibição materializados, como índices secundários, ou modos de exibição agregadas e filtradas do estado da réplica. Por exemplo, um índice classificado de todas as chaves em um Dicionário Confiável.
- Envio de dados de monitoramento, como o número de usuários adicionados na última hora.

As notificações são disparadas como parte da aplicação de operações. Como elas fazem parte da aplicação da operação, é importante observar que a manipulação das notificações deve ocorrer o mais rápido possível, e nenhuma operação dispendiosa devem ser feita em eventos síncronos.

## Notificações do Gerenciador de Estado Confiável

O Gerenciador de Estado Confiável fornece notificações para os eventos a seguir

- Transação
    - Confirmar
- Gerenciador de Estado
    - Recompilação
    - Adição de um estado confiável
    - Remoção de um estado confiável

O Gerenciador de Estado Confiável rastreia as transações em execução no momento. A única alteração de estado de transação que causará o disparo de uma notificação será a confirmação de uma transação.

O Gerenciador de Estado Confiável mantém uma coleção de Estados Confiáveis, como Dicionário Confiável e Fila Confiável. O Gerenciador de Estado Confiável dispara notificações quando essa coleção muda: um Estado Confiável é adicionado, removido ou toda a coleção é recompilada. A coleção do Gerenciador de Estado Confiável é recompilada em três casos

- Recuperação: quando uma réplica é iniciada, ele recuperará seu estado anterior do disco. Ao final da recuperação, ela dispare um evento com **NotifyStateManagerChangedEventArgs** que contém o conjunto de **IReliableState** recuperado.
- Cópia Completa: antes de uma réplica poder ingressar no conjunto de configurações, ela deve ser compilada. Às vezes, isso pode exigir a aplicação de uma cópia completa do estado do Gerenciador de Estado Confiável da réplica Primária para a réplica secundária ociosa. O Gerenciador de Estado Confiável na secundária disparará um evento com **NotifyStateManagerChangedEventArgs** que contém o conjunto de **IReliableState** adquirido da primária.
- Restore: em cenários de recuperação de desastres, o estado da réplica pode ser restaurado de um backup usando **RestoreAsync**. Nesses casos, o Gerenciador de Estado Confiável na primária disparará um evento com **NotifyStateManagerChangedEventArgs** que contém o conjunto de **IReliableState** restaurado do backup.

### Como usar as notificações do Gerenciador de Estado Confiável
Para se registrar e receber notificações de transação e/ou notificações do gerenciador de estado, o usuário precisa se registrar com eventos **TransactionChanged** ou **StateManagerChanged** no Gerenciador de Estado Confiável, respectivamente. Um lugar comum para registrar esses manipuladores de eventos é o construtor de seu StatefulService. Isso porque ao se registrar no construtor, o cliente garante que ele não perderá qualquer notificação causada por alterações durante o tempo de vida do **IReliableStateManager**.

```C#
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

O EventHandler **TransactionChanged** usa **NotifyTransactionChangedEventArgs** para fornecer detalhes sobre o evento. Ele contém a propriedade Action (por exemplo, **NotifyTransactionChangedAction.Commit**) que especifica o tipo de alteração, e a propriedade Transaction que fornece uma referência à Transação que foi alterada.

>[AZURE.NOTE] Hoje, os eventos TransactionChanged são emitidos somente se a Transação for confirmada, portanto, Action será igual a NotifyTransactionChangedAction.Commit. No entanto, mais Ações podem ser adicionadas em atualizações futuras. Consequentemente, recomendamos a verificação da Ação e o processamento do evento se ele for o que você esperava.

Veja a seguir um exemplo de manipulador de eventos **TransactionChanged**

```C#
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

O EventHandler **StateManagerChanged** usa **NotifyStateManagerChangedEventArgs** para fornecer detalhes sobre o evento. **NotifyStateManagerChangedEventArgs** tem duas subclasses: NotifyStateManagerRebuildEventArgs e NotifyStateManagerSingleEntityChangedEventArgs. A propriedade Action em **NotifyStateManagerChangedEventArgs** deve ser usada para converter o **NotifyStateManagerChangedEventArgs** na subclasse correta

- NotifyStateManagerChangedAction.Rebuild: NotifyStateManagerRebuildEventArgs
- NotifyStateManagerChangedAction.Add e Remove: NotifyStateManagerSingleEntityChangedEventArgs

Veja a seguir um exemplo de manipulador de notificação **StateManagerChanged**

```C#
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStataManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## Notificações de Dicionário Confiável

O Dicionário Confiável fornece notificações para os eventos a seguir

- Rebuild: chamado quando o **ReliableDictionary** tiver recuperado o estado do disco local, da cópia do Primário ou de um backup.
- Clear: chamado quando o estado do **ReliableDictionary** tiver sido apagado usando o método **ClearAsync**.
- Adicione: Chamado quando um item foi adicionado ao **ReliableDictionary**.
- Update: chamado quando um item em **IReliableDictionary** tiver sido atualizado.
- Remove: chamado quando um item em **IReliableDictionary** tiver sido removido.

### Como usar as Notificações de Dicionário Confiável
Para se registrar a fim de receber as notificações do Dicionário Confiável, o usuário precisa se registrar com o manipulador de eventos **DictionaryChanaged** em **IReliableDictionary**. Um lugar comum para se registrar com esses manipuladores de eventos é na notificação de adição **ReliableStateManager.StateManagerChanged**. Isso ocorre porque o registro no momento da adição de **IReliableDictionary** ao **IReliableStateManager**, garante que você não perderá quaisquer notificações.

```C#
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
            }
        }
    }
}
```

>[AZURE.NOTE] ProcessStateManagerSingleEntityNotification é o método de exemplo chamado pelo exemplo de OnStateManagerChangedHandler acima.

Observe que o código acima define o IReliableNotificationAsyncCallback, bem como a **DictionaryChanged**. Como **NotifyDictionaryRebuildEventArgs** contém um **IAsyncEnumerable** que precisa ser enumerado de forma assíncrona, as notificações de recompilação são disparadas por meio de **RebuildNotificationAsyncCallback** em vez de **OnDictionaryChangedHandler**.

```C#
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

>[AZURE.NOTE] No código acima, como parte do processo da notificação de recompilação, primeiro, o estado agregado mantido é apagado. Isso ocorre porque, como a Coleção Confiável está sendo recompilada com um novo estado, todas as notificações anteriores são irrelevantes.

O EventHandler **DictionaryChanged** usa **NotifyDictionaryChangedEventArgs** para fornecer detalhes sobre o evento. **NotifyDictionaryChangedEventArgs** tem cinco subclasses. A propriedade Action em **NotifyDictionaryChangedEventArgs** deve ser usada para converter o **NotifyDictionaryChangedEventArgs** na subclasse correta

- NotifyDictionaryChangedAction.Rebuild: NotifyDictionaryRebuildEventArgs
- NotifyDictionaryChangedAction.Clear: NotifyDictionaryClearEventArgs
- NotifyDictionaryChangedAction.Add e Remove: NotifyDictionaryItemAddedEventArgs
- NotifyDictionaryChangedAction.Update: NotifyDictionaryItemUpdatedEventArgs
- NotifyDictionaryChangedAction.Remove: NotifyDictionaryItemRemovedEventArgs

```C#
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## Recomendações

- CONCLUA os eventos de notificações o mais rápido possível.
- NÃO execute quaisquer operações dispendiosas (como operações de E/S) como parte de eventos síncronos.
- VERIFIQUE o tipo de Ação antes de processar o evento. Novos tipos de Ação podem ser adicionados no futuro.

Eis aqui algumas coisas que se deve manter em mente:
- As notificações são disparadas como parte da execução de uma operação. Por exemplo, uma notificação de restauração será acionada como a última etapa do processo de restauração, e a restauração não será concluída até que o evento de notificação seja processado.
- Como as notificações são disparadas como parte das operações de aplicação, os clientes verão apenas as notificações para operações confirmadas localmente. Observe que como há garantia apenas para a confirmação local das operações (em outras palavras, registro em log), elas podem ou não ser desfeitas no futuro.
- No caminho de restauração, uma única notificação será disparada para cada operação aplicada. Isso significa que, se uma transação T1 incluir Create(X), Delete(X), Create(X), você receberá uma notificação para a criação de X, uma para a exclusão e outra para a nova criação, nessa ordem.
- Para transações que contêm várias operações, as operações serão aplicadas na ordem em que foram recebidas no Primário do usuário.
- Como parte do processamento do progresso falso, algumas operações podem ser desfeitas. As notificações serão geradas para essas operações da ação de desfazer, revertendo o estado da réplica de volta ao ponto estável. Uma diferença importante das notificações de ação de desfazer é que os eventos com chaves duplicadas serão agregados. Por exemplo, se a T1 acima estiver sendo desfeita, o usuário verá uma notificação única para Delete(X).

## Próximas etapas

- [Início Rápido dos Serviços Confiáveis](service-fabric-reliable-services-quick-start.md)
- [Backup e restauração do Reliable Services (recuperação de desastre)](service-fabric-reliable-services-backup-restore.md)
- [Referência do desenvolvedor para Coleções Confiáveis](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

<!---HONumber=AcomDC_0629_2016-->