<properties 
    pageTitle="Filas de mensagens mortas do Barramento de Serviço | Microsoft Azure" 
    description="Visão geral das filas de mensagens mortas do Barramento de Serviço" 
    services="service-bus-messaging" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags
    ms.service="service-bus-messaging"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na" 
    ms.date="06/21/2016"
    ms.author="clemensv;sethm"/>

# Visão geral das filas de mensagens mortas do Barramento de Serviço

As filas e as assinaturas de tópico do Barramento de Serviço fornecem uma subfila secundária chamada DLQ (*fila de mensagens mortas*). A fila de mensagens mortas não precisa ser explicitamente criada e não pode ser excluída ou de alguma forma gerenciada independentemente da entidade principal.

A finalidade da fila de mensagens mortas é manter mensagens que não podem ser entregues a algum receptor ou simplesmente mensagens que não puderam ser processadas. As mensagens podem ser removidas da DLQ e inspecionadas. Um aplicativo pode, com a ajuda de um operador, corrigir problemas e reenviar a mensagem, registrar o fato de que houve um erro e/ou tomar uma medida corretiva.

De uma perspectiva de API e protocolo, a DLQ é bem semelhante a qualquer outra fila, com exceção de que essas mensagens podem ser enviadas apenas por meio do gesto de mensagens mortas da entidade pai. Além disso, a vida útil não é observada, e você não pode colocar uma mensagem em estado morto na DLQ. A fila de mensagens mortas é totalmente compatível com operações transacionais e de entrega de espiada-bloqueio.

Observe que não há limpeza automática da DLQ. As mensagens permanecem na DLQ até você recuperá-las de lá explicitamente e chamar [Complete()](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.completeasync.aspx) na mensagem morta.

## Movendo mensagens para a DLQ

Há várias atividades no Barramento de Serviço que fazem com que as mensagens sejam enviadas por push à DLQ de dentro do próprio mecanismo de mensagens. Um aplicativo também pode enviar mensagens por push explicitamente à DLQ.

Como a mensagem é movida pelo agente, duas propriedades são adicionadas à mensagem, uma vez que o agente chama sua versão interna do método [DeadLetter](https://msdn.microsoft.com/library/azure/hh291941.aspx) na mensagem: `DeadLetterReason` e `DeadLetterErrorDescription`.

Os aplicativos podem definir seus próprios códigos para a propriedade `DeadLetterReason`, mas o sistema define os valores a seguir.

| Condição | DeadLetterReason | DeadLetterErrorDescription |
|---------------------------------------------------------------------------------------------------------------------------------------|-----------------------------|----------------------------------------------------------------------------------|
| Sempre | HeaderSizeExceeded | A cota de tamanho para este fluxo foi excedida. |
| !TopicDescription.<br />EnableFilteringMessagesBeforePublishing and SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationExceptions | exception.GetType().Name | exception.Message |
| EnableDeadLetteringOnMessageExpiration | TTLExpiredException | A mensagem expirou e foi colocada no estado de mensagem morta. |
| SubscriptionDescription.RequiresSession | A ID da sessão é nula. | A entidade habilitada para sessão não permite uma mensagem cuja identificação de sessão seja nula. |
| !fila de mensagens mortas | MaxTransferHopCountExceeded | Nulo |
| Mensagem morta explícita do aplicativo | Especificado pelo aplicativo | Especificado pelo aplicativo |

## Excedendo MaxDeliveryCount

Cada uma das filas e assinaturas tem uma propriedade [QueueDescription.MaxDeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.maxdeliverycount.aspx) e [SubscriptionDescription.MaxDeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount.aspx), respectivamente; o valor padrão é 10. Sempre que uma mensagem tiver sido entregue em um bloqueio ([ReceiveMode.PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx)), mas tiver sido explicitamente abandonada ou o bloqueio tiver expirado, [BrokeredMessage.DeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.deliverycount.aspx) será incrementada. Quando [DeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.deliverycount.aspx) exceder [MaxDeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.maxdeliverycount.aspx), a mensagem será movida para a DLQ, especificando o código de motivo `MaxDeliveryCountExceeded`.

Esse comportamento não pode ser desabilitado, mas você pode definir [MaxDeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.maxdeliverycount.aspx) para um número bem alto.

## Excedendo TimeToLive

Quando a propriedade [QueueDescription.EnableDeadLetteringOnMessageExpiration](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration.aspx) ou [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.enabledeadletteringonmessageexpiration.aspx) for definida como **true** (o padrão é **false**), todas as mensagens expiradas serão movidas para a DLQ, especificando o código de motivo `TTLExpiredException`.

Observe que as mensagens expiradas são apenas limpas e, portanto, movidas para a DLQ quando há pelo menos um receptor ativo extraindo na assinatura ou fila principal; esse comportamento é intencional.

## Erros ao processar as regras de assinatura

Quando a propriedade [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.enabledeadletteringonfilterevaluationexceptions.aspx) for habilitada para uma assinatura, qualquer erro que ocorra durante as execuções da regra de filtro SQL de uma assinatura será capturado na DLQ com a mensagem ofensiva.

## Mensagens mortas no nível de aplicativo

Além dos recursos de mensagens mortas fornecidas pelo sistema, os aplicativos podem usar a DLQ para rejeitar explicitamente mensagens inaceitáveis. Isso pode incluir mensagens que não podem ser processadas adequadamente devido a qualquer tipo de problema no sistema, mensagens que mantêm cargas malformadas ou mensagens que falham na autenticação quando algum esquema de segurança no nível de mensagem é usado.

## Exemplo

O trecho de código a seguir cria um receptor de mensagem. No loop de recebimento da fila principal, o código recupera a mensagem com [Receive(TimeSpan.Zero)](https://msdn.microsoft.com/library/azure/dn130350.aspx), que solicita ao agente para retornar instantaneamente qualquer mensagem prontamente disponível ou retornar sem resultado. Se o código receber uma mensagem, ele a abandonará imediatamente, o que incrementa a `DeliveryCount`. Depois que o sistema move a mensagem para a DLQ, a fila principal é esvaziada e o loop é encerrado, pois [ReceiveAsync](https://msdn.microsoft.com/library/azure/dn130350.aspx) retorna **null**.

```
var receiver = await receiverFactory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);
while(true)
{
    var msg = await receiver.ReceiveAsync(TimeSpan.Zero);
    if (msg != null)
    {
        Console.WriteLine("Picked up message; DeliveryCount {0}", msg.DeliveryCount);
        await msg.AbandonAsync();
    }
    else
    {
        break;
    }
}
```

## Próximas etapas

Confira os artigos a seguir para obter mais informações sobre as filas do Barramento de Serviço:

- [Introdução às filas do Barramento de Serviço](service-bus-dotnet-get-started-with-queues.md)
- [Filas do Azure e filas do Barramento de Serviço – comparações](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

<!---HONumber=AcomDC_0928_2016-->