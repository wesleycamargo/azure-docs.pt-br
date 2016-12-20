---
title: "Filas e tópicos particionados | Microsoft Docs"
description: "Descreve como particionar filas e tópicos do Barramento de Serviço usando vários agentes de mensagem."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a0c7d5a2-4876-42cb-8344-a1fc988746e7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/02/2016
ms.author: sethm;hillaryc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 03e68436372414a25d87f50c2b057c2f8d193401


---
# <a name="partitioned-queues-and-topics"></a>Filas e tópicos particionados
O Barramento de Serviço do Azure emprega vários agentes de mensagens para processar mensagens e vários repositórios de mensagens para armazenar mensagens. Uma fila ou um tópico convencional é manipulado por um único agente de mensagem e armazenado em um repositório de mensagens. O Barramento de Serviço também habilita filas ou tópicos a serem particionados entre diversos agentes e repositórios de mensagens. Isso significa que a produtividade geral de uma fila ou um tópico particionado não é mais limitada pelo desempenho de um único agente ou repositório de mensagens. Além disso, uma interrupção temporária de um repositório de mensagens não torna uma fila ou um tópico particionado indisponível. Filas e tópicos particionados podem conter todos os recursos avançados do Barramento de Serviço, como o suporte a transações e sessões.

Para obter mais detalhes sobre aspectos internos do Barramento de Serviço, consulte o tópico [Arquitetura do Barramento de Serviço][Arquitetura do Barramento de Serviço].

## <a name="how-it-works"></a>Como ele funciona
Cada fila ou tópico particionado consiste em vários fragmentos. Cada fragmento é armazenado em um repositório de mensagens diferente e manipulado por um agente de mensagens diferente. Quando uma mensagem é enviada a uma fila ou um tópico particionado, o Barramento de Serviço atribui a mensagem a um dos fragmentos. A seleção é feita aleatoriamente pelo Barramento de Serviço ou por uma chave de partição que pode ser especificada pelo remetente.

Quando um cliente deseja receber uma mensagem de uma fila particionada ou de uma assinatura de um tópico particionado, o Barramento de Serviço consulta todos os fragmentos de mensagens e, em seguida, retorna a primeira mensagem obtida de qualquer um dos repositórios de mensagens para o destinatário. O Barramento de Serviço armazena em cache as outras mensagens e as retorna ao receber solicitações de recebimento adicionais. Um cliente receptor não está ciente do particionamento; o comportamento voltado para o cliente de uma fila ou um tópico particionado (por exemplo, ler, concluir, adiar, colocar mensagem na fila de mensagens mortas, executar pré-busca) é idêntico ao comportamento de uma entidade regular.

Não há custo adicional ao enviar ou receber uma mensagem de uma fila ou um tópico particionado.

## <a name="enable-partitioning"></a>Habilitar particionamento
Para usar filas e tópicos particionados com o Barramento de Serviço do Azure, use o SDK do Azure versão 2.2 ou posterior, ou especifique `api-version=2013-10` em suas solicitações HTTP.

Você pode criar filas e tópicos do Barramento de Serviço em tamanhos de 1, 2, 3, 4 ou 5 GB (o padrão é 1 GB). Com o particionamento habilitado, o Barramento de Serviço cria 16 partições para cada GB especificado. Assim, se você criar uma fila que tenha 5 GB, com 16 partições, o tamanho máximo da fila será (5 \* 16) = 80 GB. É possível ver o tamanho máximo da fila ou do tópico particionado observando sua entrada no [Portal do Azure][Portal do Azure].

Há várias maneiras de criar uma fila ou um tópico particionado. Ao criar a fila ou o tópico de seu aplicativo, é possível habilitar o particionamento para a fila ou tópico definindo respectivamente a propriedade [QueueDescription.EnablePartitioning][QueueDescription.EnablePartitioning] ou [TopicDescription.EnablePartitioning][TopicDescription.EnablePartitioning] como **true**. Essas propriedades devem ser definidas no momento em que a fila ou o tópico é criado. Não é possível alterar essas propriedades em uma fila ou um tópico existente. Por exemplo:

```
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Como alternativa, você pode criar uma fila ou um tópico particionado no Visual Studio ou no [Portal do Azure][Portal do Azure]. Ao criar uma nova fila ou um novo tópico no portal, defina a opção **Habilitar Particionamento** na folha **Configurações gerais** da janela **Configurações** da fila ou do tópico como **verdadeiro**. No Visual Studio, clique na caixa de seleção **Habilitar Particionamento** na caixa de diálogo **Nova Fila** ou **Novo Tópico**.

## <a name="use-of-partition-keys"></a>Uso de chaves de partição
Quando uma mensagem é enfileirada em uma fila ou um tópico particionado, o Barramento de Serviço verifica a presença de uma chave de partição. Se encontrar uma, ele selecionará o fragmento com base na chave. Se não encontrar uma chave de partição, ele selecionará o fragmento com base em um algoritmo interno.

### <a name="using-a-partition-key"></a>Usando uma chave de partição
Alguns cenários, como sessões ou transações, exigem que as mensagens sejam armazenadas em um fragmento específico. Todos esses cenários exigem o uso de uma chave de partição. Todas as mensagens que usam a mesma chave de partição são atribuídas ao mesmo fragmento. Se o fragmento estiver indisponível temporariamente, o Barramento de Serviço retornará um erro.

Dependendo do cenário, propriedades de mensagem diferentes são usadas como uma chave de partição:

**SessionId**: caso uma mensagem tenha a propriedade [BrokeredMessage.SessionId][BrokeredMessage.SessionId] definida, o Barramento de Serviço usará essa propriedade como a chave de partição. Dessa forma, todas as mensagens que pertencem à mesma sessão são manipuladas pelo mesmo agente de mensagens. Isso habilita o Barramento de Serviço a garantir a ordenação das mensagens, bem como a consistência dos estados de sessão.

**PartitionKey**: caso uma mensagem tenha a propriedade [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey], mas não a propriedade [BrokeredMessage.SessionId][BrokeredMessage.SessionId] definida, o Barramento de Serviço usará a propriedade [PartitionKey][PartitionKey] como a chave de partição. Caso a mensagem tenha as propriedades [SessionId][SessionId] e [PartitionKey][PartitionKey] definidas, as propriedades deverão ser idênticas. Se a propriedade [PartitionKey][PartitionKey] for definida como um valor diferente do que o da propriedade [SessionId][SessionId], o Barramento de Serviço retornará uma exceção **InvalidOperationException**. A propriedade [PartitionKey][PartitionKey] deverá ser usada se um remetente enviar mensagens transacionais sem reconhecimento de sessão. A chave de partição garante que todas as mensagens enviadas em uma transação sejam manipuladas pelo mesmo agente de mensagens.

**MessageId**: caso a fila ou o tópico tenha a propriedade [QueueDescription.RequiresDuplicateDetection][QueueDescription.RequiresDuplicateDetection] definida como **true** e as propriedades [BrokeredMessage.SessionId][BrokeredMessage.SessionId] ou [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey] não forem definidas, a propriedade [BrokeredMessage.MessageId][BrokeredMessage.MessageId] servirá como a chave de partição. (Observe que as bibliotecas Microsoft .NET e AMQP atribuirão automaticamente uma ID de mensagem se o aplicativo de envio não o fizer.) Nesse caso, todas as cópias da mesma mensagem são manipuladas pelo mesmo agente de mensagens. Isso permite que o Barramento de Serviço detecte e elimine mensagens duplicadas. Se a propriedade [QueueDescription.RequiresDuplicateDetection][QueueDescription.RequiresDuplicateDetection] não for definida como **true**, o Barramento de Serviço não considerará a propriedade [MessageId][MessageId] como uma chave de partição.

### <a name="not-using-a-partition-key"></a>Não usando uma chave de partição
Na ausência de uma chave de partição, o Barramento de Serviço distribui mensagens em forma de round robin para todos os fragmentos da fila ou do tópico particionado. Se o fragmento escolhido não estiver disponível, o Barramento de Serviço atribuirá a mensagem a um fragmento diferente. Dessa forma, a operação de envio será bem-sucedida independentemente da indisponibilidade temporária de um repositório de mensagens.

Para que o Barramento de Serviço tenha tempo suficiente para enfileirar a mensagem em um fragmento diferente, o valor [MessagingFactorySettings.OperationTimeout][MessagingFactorySettings.OperationTimeout] especificado pelo cliente que envia a mensagem deve ser maior que 15 segundos. É recomendável definir a propriedade [OperationTimeout][OperationTimeout] com o valor padrão de 60 segundos.

Observe que uma chave de partição "fixa" uma mensagem a um fragmento específico. Se o repositório de mensagens que mantém o fragmento estiver indisponível, o Barramento de Serviço retornará um erro. Na ausência de uma chave de partição, o Barramento de Serviço pode escolher um fragmento diferente, e a operação será bem-sucedida. Portanto, é recomendável que você não forneça uma chave de partição, a menos que seja necessário.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Tópicos avançados: usar transações com entidades particionadas
As mensagens enviadas como parte de uma transação devem especificar uma chave de partição. Essa pode ser uma das seguintes propriedades: [BrokeredMessage.SessionId][BrokeredMessage.SessionId], [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey] ou [BrokeredMessage.MessageId][BrokeredMessage.MessageId]. Todas as mensagens que são enviadas como parte da mesma transação devem especificar a mesma chave de partição. Caso você tente enviar uma mensagem sem uma chave de partição em uma transação, o Barramento de Serviço resultará em uma exceção **InvalidOperationException**. Caso você tente enviar várias mensagens que tenham chaves de partição diferentes na mesma transação, o Barramento de Serviço resultará em uma exceção **InvalidOperationException**. Por exemplo:

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

## <a name="using-sessions-with-partitioned-entities"></a>Usando sessões com entidades particionadas
Para enviar uma mensagem transacional a uma fila ou um tópico com reconhecimento de sessão, a mensagem deve ter a propriedade [BrokeredMessage.SessionId][BrokeredMessage.SessionId] definida. Se a propriedade [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey] também for especificada, ela deverá ser idêntica à propriedade [SessionId][SessionId]. Caso elas sejam diferentes, o Barramento de Serviço resultará em uma exceção **InvalidOperationException**.

Diferentemente de filas ou tópicos regulares (não particionados), não é possível usar uma única transação para enviar várias mensagens a sessões diferentes. Caso haja a tentativa, o Barramento de Serviço resultará em uma exceção **InvalidOperationException**. Por exemplo:

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

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Encaminhamento automático de mensagens com entidades particionadas
O Barramento de Serviço do Azure dá suporte ao encaminhamento automático de mensagens de, para ou entre entidades particionadas. Para habilitar o encaminhamento automático de mensagens, defina a propriedade [QueueDescription.ForwardTo][QueueDescription.ForwardTo] na assinatura ou na fila de origem. Se a mensagem especificar uma chave de partição ([SessionId][SessionId], [PartitionKey][PartitionKey] ou [MessageId][MessageId]), essa chave será usada para a entidade de destino.

## <a name="considerations-and-guidelines"></a>Considerações e diretrizes
* **Recursos de alta consistência**: se uma entidade usa recursos como sessões, detecção de duplicidades ou controle explícito de chave de particionamento, as operações de mensagens serão sempre roteadas para fragmentos específicos. Se qualquer um dos fragmentos tiver alto tráfego ou se o repositório subjacente não estiver íntegro, essas operações falharão e a disponibilidade será reduzida. Em geral, a consistência ainda é muito maior do que em entidades não particionadas; somente um subconjunto de tráfego tem problemas, em vez de todo o tráfego.
* **Gerenciamento**: operações como Criar, Atualizar e Excluir devem ser executadas em todos os fragmentos da entidade. Se qualquer fragmento não estiver íntegro, isso poderá resultar em falhas para essas operações. Para a operação Get, informações como contagens de mensagens devem ser agregadas de todos os fragmentos. Se qualquer fragmento não estiver íntegro, o status de disponibilidade será relatado como limitado.
* **Cenários de mensagens de baixo volume**: para esses cenários, particularmente ao usar o protocolo HTTP, talvez você precise executar várias operações de recebimento para obter todas as mensagens. Para solicitações de recebimento, o front-end executa um recebimento em todos os fragmentos e armazena em cache todas as respostas recebidas. Uma solicitação de recebimento subsequente na mesma conexão se beneficiará desse armazenamento em cache, e as latências de recebimento serão menores. No entanto, se você tiver várias conexões ou se usar HTTP, isso estabelecerá uma nova conexão para cada solicitação. Assim, não há garantia de que ela chegará ao mesmo nó. Se todas as mensagens existentes forem bloqueadas e armazenadas em cache no outro front-end, a operação de recebimento resultará em **nulo**. As mensagens eventualmente expiram e você pode recebê-las novamente. O keep-alive de HTTP é recomendável.
* **Pesquisar/inspecionar mensagens**: o PeekBatch nem sempre retorna o número de mensagens especificado na [propriedade MessageCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.messagecount.aspx). Há duas razões comuns para isso. Uma razão é que o tamanho agregado da coleção de mensagens excede o tamanho máximo de 256 KB. Outra razão é que, se a fila ou o tópico tiver a [Propriedade EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx) definida como **verdadeiro**, uma partição poderá não ter mensagens suficientes para concluir o número solicitado de mensagens. Em geral, se um aplicativo quiser receber um número específico de mensagens, ele deverá solicitar [PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peekbatch.aspx) repetidamente até obter o número de mensagens ou até não existirem mais mensagens para inspecionar. Para obter mais informações, incluindo exemplos de código, confira [QueueClient.PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peekbatch.aspx) ou [SubscriptionClient.PeekBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.peekbatch.aspx).

## <a name="latest-added-features"></a>Últimos recursos adicionados
* Agora há suporte para adicionar ou remover regras com entidades particionadas. Diferentes de entidades não particionadas, essas operações não têm suporte em transações. 
* Agora há suporte a AMQP para enviar e receber mensagens de e para uma entidade particionada.
* O AMQP agora tem suporte para as seguintes operações: [Enviar em Lote](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.sendbatch.aspx), [Receber em Lote](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.receivebatch.aspx), [Receber pelo Número de Sequência](https://msdn.microsoft.com/library/azure/hh330765.aspx), [Inspecionar](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.peek.aspx), [Renovar Bloqueio](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.renewmessagelock.aspx), [Agendar Mensagem](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.schedulemessageasync.aspx), [Cancelar Mensagem Agendada](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.cancelscheduledmessageasync.aspx), [Adicionar Regra](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ruledescription.aspx), [Remover Regra](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ruledescription.aspx), [Renovar Bloqueio de Sessão](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.renewlock.aspx), [Definir Estado de Sessão](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.setstate.aspx), [Obter Estado de Sessão](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.getstate.aspx), [Inspecionar Mensagens de Sessão](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesession.peek.aspx) e [Enumerar Sessões](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.queueclient.getmessagesessionsasync.aspx).

## <a name="partitioned-entities-limitations"></a>Limitações das entidades particionadas
Atualmente, o Barramento de Serviço impõe as seguintes limitações a filas e tópicos particionados:

* Filas e tópicos particionados não dão suporte ao envio de mensagens que pertencem a sessões diferentes em uma única transação.
* O Barramento de Serviço atualmente permite até 100 filas ou tópicos particionados por namespace. Cada fila ou tópico particionado conta para a cota de 10.000 entidades por namespace (não se aplica à camada Premium).

## <a name="next-steps"></a>Próximas etapas
Veja a discussão de [Suporte a AMQP 1.0 para filas e tópicos particionados do Barramento de Serviço][Suporte a AMQP 1.0 para filas e tópicos particionados do Barramento de Serviço] para saber mais sobre como particionar entidades de mensagens. 

[Arquitetura do Barramento de Serviço]: service-bus-architecture.md
[Portal do Azure]: https://portal.azure.com
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
[Suporte a AMQP 1.0 para filas e tópicos particionados do Barramento de Serviço]: service-bus-partitioned-queues-and-topics-amqp-overview.md



<!--HONumber=Nov16_HO3-->


