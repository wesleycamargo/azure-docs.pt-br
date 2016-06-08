<properties 
    pageTitle="Entidades de mensagens particionadas | Microsoft Azure"
    description="Descreve como particionar entidades de mensagens usando diversos agentes de mensagens."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="05/06/2016"
    ms.author="sethm" />

# Entidades de mensagens particionadas

O Barramento de Serviço do Azure emprega vários agentes de mensagens para processar mensagens e vários repositórios de mensagens para armazenar mensagens. Uma fila ou um tópico convencional é manipulado por um único agente de mensagem e armazenado em um repositório de mensagens. O Barramento de Serviço também habilita filas ou tópicos a serem particionados entre diversos agentes e repositórios de mensagens. Isso significa que a taxa de transferência geral de uma fila ou um tópico particionado não é mais limitada pelo desempenho de um único agente ou repositório de mensagens. Além disso, uma falha temporária de um repositório de mensagens não torna uma fila ou tópico particionado indisponível. Filas e tópicos particionados podem conter todos os recursos avançados do Barramento de Serviço, como o suporte a transações e sessões.

Para obter mais detalhes sobre aspectos internos do Barramento de Serviço, consulte o tópico [Arquitetura do Barramento de Serviço][].

## Filas e tópicos particionados

Cada fila ou tópico particionado consiste em vários fragmentos. Cada fragmento é armazenado em um repositório de mensagens diferente e manipulado por um agente de mensagens diferente. Quando uma mensagem é enviada a uma fila ou um tópico particionado, o Barramento de Serviço atribui a mensagem a um dos fragmentos. A seleção é feita aleatoriamente pelo Barramento de Serviço ou por uma chave de partição que pode ser especificada pelo remetente.

Quando um cliente deseja receber uma mensagem de uma fila particionada ou de uma assinatura de um tópico particionado, o Barramento de Serviço consulta todos os fragmentos de mensagens e, em seguida, retorna a primeira mensagem obtida de qualquer um dos repositórios de mensagens para o destinatário. O Barramento de Serviço armazena em cache as outras mensagens e as retorna ao receber solicitações de recebimento adicionais. Um cliente receptor não está ciente do particionamento; o comportamento voltado para o cliente de uma fila ou um tópico particionado (por exemplo, ler, concluir, adiar, colocar mensagem na fila de mensagens mortas, executar pré-busca) é idêntico ao comportamento de uma entidade regular.

Não há custo adicional ao enviar ou receber uma mensagem de uma fila ou um tópico particionado.

## Habilitar particionamento

Para usar filas e tópicos particionados com o Barramento de Serviço do Azure, use o Azure SDK versão 2.2 ou posterior ou especifique `api-version=2013-10` em suas solicitações HTTP.

Você pode criar filas e tópicos do Barramento de Serviço em tamanhos de 1, 2, 3, 4 ou 5 GB (o padrão é 1 GB). Com o particionamento habilitado, o Barramento de Serviço cria 16 partições para cada GB especificado. Assim, se você criar uma fila que tenha 5 GB, com 16 partições, o tamanho máximo da fila será (5 * 16) = 80 GB. É possível ver o tamanho máximo da fila ou do tópico particionado observando sua entrada no [portal clássico do Azure][].

Há várias maneiras de criar uma fila ou um tópico particionado. Ao criar a fila ou o tópico de seu aplicativo, você pode habilitar o particionamento para a fila ou tópico definindo respectivamente a propriedade [QueueDescription.EnablePartitioning][] ou [TopicDescription.EnablePartitioning][] como **true**. Essas propriedades devem ser definidas no momento em que a fila ou o tópico é criado. Não é possível alterar essas propriedades em uma fila ou um tópico existente. Por exemplo:

```
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Como alternativa, você pode criar uma fila ou um tópico particionado no Visual Studio ou no [portal clássico do Azure][]. Ao criar uma nova fila ou tópico no portal, marque a opção **Habilitar Particionamento** na guia **Configurar** da janela da fila ou do tópico. No Visual Studio, clique na caixa de seleção **Habilitar Particionamento** da caixa de diálogo **Nova Fila** ou **Novo Tópico**.

## Uso de chaves de partição

Quando uma mensagem é enfileirada em uma fila ou um tópico particionado, o Barramento de Serviço verifica a presença de uma chave de partição. Se encontrar uma, ele selecionará o fragmento com base na chave. Se não encontrar uma chave de partição, ele selecionará o fragmento com base em um algoritmo interno.

### Usar uma chave de partição

Alguns cenários, como sessões ou transações, exigem que as mensagens sejam armazenadas em um fragmento específico. Todos esses cenários exigem o uso de uma chave de partição. Todas as mensagens que usam a mesma chave de partição são atribuídas ao mesmo fragmento. Se o fragmento estiver indisponível temporariamente, o Barramento de Serviço retornará um erro.

Dependendo do cenário, propriedades de mensagem diferentes são usadas como uma chave de partição:

**SessionId**: se uma mensagem tiver a propriedade [BrokeredMessage.SessionId][] definida, o Barramento de Serviço usará essa propriedade como a chave da partição. Dessa forma, todas as mensagens que pertencem à mesma sessão são manipuladas pelo mesmo agente de mensagens. Isso habilita o Barramento de Serviço a garantir a ordenação das mensagens, bem como a consistência dos estados de sessão.

**PartitionKey**: se uma mensagem tiver a propriedade [BrokeredMessage.PartitionKey][] definida, mas não a propriedade [BrokeredMessage.SessionId][], o Barramento de Serviço usará a propriedade [PartitionKey][] como a chave da partição. Se a mensagem tiver as propriedades [SessionId][] e [PartitionKey][] definidas, as propriedades deverão ser idênticas. Se a propriedade [PartitionKey][] for definida como um valor diferente da propriedade [SessionId][], o Barramento de Serviço retornará uma exceção **InvalidOperationException**. A propriedade [PartitionKey][] deverá ser usada se um remetente enviar mensagens transacionais de conhecimento sem sessão. A chave de partição garante que todas as mensagens enviadas em uma transação sejam manipuladas pelo mesmo agente de mensagens.

**MessageId**: se a fila ou o tópico tiver a propriedade [QueueDescription.RequiresDuplicateDetection][] definida como **true** e as propriedades [BrokeredMessage.SessionId][] ou [BrokeredMessage.PartitionKey][] não forem definidas, a propriedade [BrokeredMessage.MessageId][] servirá como a chave da partição. (Observe que as bibliotecas Microsoft .NET e AMQP atribuirão automaticamente uma ID de mensagem se o aplicativo de envio não o fizer.) Nesse caso, todas as cópias da mesma mensagem são manipuladas pelo mesmo agente de mensagens. Isso permite que o Barramento de Serviço detecte e elimine mensagens duplicadas. Se a propriedade [QueueDescription.RequiresDuplicateDetection][] não for definida como **true**, o Barramento de Serviço não considerará a propriedade [MessageId][] como uma chave de partição.

### Não usando uma chave de partição

Na ausência de uma chave de partição, o Barramento de Serviço distribui mensagens em forma de round robin para todos os fragmentos da fila ou do tópico particionado. Se o fragmento escolhido não estiver disponível, o Barramento de Serviço atribuirá a mensagem a um fragmento diferente. Dessa forma, a operação de envio será bem-sucedida independentemente da indisponibilidade temporária de um repositório de mensagens.

Para dar ao Barramento de Serviço tempo suficiente para enfileirar a mensagem em um fragmento diferente, o valor de [MessagingFactorySettings.OperationTimeout][] especificado pelo cliente que envia a mensagem deve ser maior que 15 segundos. É recomendável que você defina a propriedade [OperationTimeout][] com o valor padrão de 60 segundos.

Observe que uma chave de partição "fixa" uma mensagem a um fragmento específico. Se o repositório de mensagens que mantém o fragmento estiver indisponível, o Barramento de Serviço retornará um erro. Na ausência de uma chave de partição, o Barramento de Serviço pode escolher um fragmento diferente, e a operação será bem-sucedida. Portanto, é recomendável que você não forneça uma chave de partição, a menos que seja necessário.

## Tópicos avançados: usar transações com entidades particionadas

As mensagens enviadas como parte de uma transação devem especificar uma chave de partição. Essa pode ser uma das seguintes propriedades: [BrokeredMessage.SessionId][], [BrokeredMessage.PartitionKey][] ou [BrokeredMessage.MessageId][]. Todas as mensagens que são enviadas como parte da mesma transação devem especificar a mesma chave de partição. Se você tentar enviar uma mensagem sem uma chave de partição em uma transação, o Barramento de Serviço retornará uma exceção **InvalidOperationException**. Se você tentar enviar várias mensagens na mesma transação que tenham chaves de partição diferentes, o Barramento de Serviço retornará uma exceção **InvalidOperationException**. Por exemplo:

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

Se qualquer uma das propriedades que servem como uma chave de partição for definida, o Barramento de Serviço fixará a mensagem a um fragmento específico. Esse comportamento ocorre quer uma transação seja usada ou não. É recomendável que você não especifique uma chave de partição se isso não for necessário.

## Usar sessões com entidades particionadas

Para enviar uma mensagem transacional a uma fila ou um tópico com reconhecimento de sessão, a mensagem deve ter a propriedade [BrokeredMessage.SessionId][] definida. Se a propriedade [BrokeredMessage.PartitionKey][] também for especificada, ela deverá ser idêntica à propriedade [SessionId][]. Se elas forem diferentes, o Barramento de Serviço retornará uma exceção **InvalidOperationException**.

Diferentemente de filas ou tópicos regulares (não particionados), não é possível usar uma única transação para enviar várias mensagens a sessões diferentes. Se isso for tentado, o Barramento de Serviço retornará uma exceção **InvalidOperationException **. Por exemplo:

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.SessionId = "mySession";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

## Encaminhamento automático de mensagens com entidades particionadas

O Barramento de Serviço do Azure dá suporte ao encaminhamento automático de mensagens de, para ou entre entidades particionadas. Para habilitar o encaminhamento automático de mensagens, defina a propriedade [QueueDescription.ForwardTo][] na fila de origem ou assinatura. Se a mensagem especificar uma chave de partição ([SessionId][], [PartitionKey][] ou [MessageId][]), essa chave será usada para a entidade de destino.

## Limitações das entidades particionadas

Em sua implementação atual, o Barramento de Serviço impõe as seguintes limitações a filas e tópicos particionados:

-   Filas e tópicos particionados estão disponíveis via SBMP ou HTTP/HTTPS, bem como com AMQP.

-   Filas e tópicos particionados não dão suporte ao envio de mensagens que pertencem a sessões diferentes em uma única transação.

-   O Barramento de Serviço atualmente permite até 100 filas ou tópicos particionados por namespace. Cada fila ou tópico particionado conta para a cota de 10.000 entidades por namespace.

-   Filas e tópicos particionados não têm suporte no Barramento de Serviço para Windows Server versões 1.0 e 1.1.

## Próximas etapas

Veja a discussão sobre o [suporte do AMQP 1.0 para filas e tópicos particionados do Barramento de Serviço][] para saber mais sobre como particionar entidades de mensagens.

  [Arquitetura do Barramento de Serviço]: service-bus-architecture.md
  [portal clássico do Azure]: http://manage.windowsazure.com
  [QueueDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx
  [TopicDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx
  [BrokeredMessage.SessionId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [BrokeredMessage.PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [SessionId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [BrokeredMessage.MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [MessagingFactorySettings.OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
  [suporte do AMQP 1.0 para filas e tópicos particionados do Barramento de Serviço]: service-bus-partitioned-queues-and-topics-amqp-overview.md

<!---HONumber=AcomDC_0518_2016-->