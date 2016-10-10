<properties 
	pageTitle="Suporte a AMQP 1.0 para filas e tópicos particionados do Barramento de Serviço | Microsoft Azure" 
	description="Saiba como usar o protocolo AMQP (Advanced Message Queuing Protocol) 1.0 com filas e tópicos particionados do Barramento de Serviço." 
	services="service-bus-messaging" 
	documentationCenter=".net" 
	authors="hillaryc" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus-messaging" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="hillaryc;sethm"/>

# Suporte a AMQP 1.0 para filas e tópicos particionados do Barramento de Serviço 

Agora, o Barramento de Serviço do Azure dá suporte ao Advanced Message Queuing Protocol (**AMQP**) 1.0 para **Filas e tópicos particionados** do Barramento de Serviço.

**AMQP** é um protocolo de enfileiramento de mensagens de padrão aberto que permite o desenvolvimento de aplicativos de plataforma cruzada usando linguagens de programação diferentes. Para obter informações gerais sobre o suporte a AMQP no Barramento de Serviço, consulte [Suporte ao AMQP 1.0 no Barramento de Serviço](service-bus-amqp-overview.md).

**Filas e tópicos particionados**, também chamados de *entidades particionadas*, oferecem alta disponibilidade, confiabilidade e taxa de transferência acima das filas e tópicos não particionados convencionais. Para obter mais informações sobre entidades particionadas, consulte [Entidades de mensagens particionadas](service-bus-partitioning.md).

A inclusão do AMQP 1.0 como um protocolo para comunicação com filas e tópicos particionados permite que você crie aplicativos interoperáveis que podem aproveitar os benefícios de maior disponibilidade, confiabilidade e taxa de transferência oferecidos por entidades particionadas do Barramento de Serviço.

Para um guia de protocolo AMQP 1.0 detalhado no nível de transmissão, que explica como o Barramento de Serviço implementa e se baseia na especificação técnica AMQP OASIS, confira o [AMQP 1.0 no guia de protocolo do Barramento de Serviço do Azure e dos Hubs de Eventos](service-bus-amqp-protocol-guide.md).

## Usando o AMQP com filas particionadas

As filas são úteis para situações que exigem o desacoplamento temporário, nivelamento de carga, balanceamento de carga e acoplamento flexível. Com uma fila, os editores enviam mensagens para a fila e os consumidores recebem mensagens da fila, em situações nas quais uma mensagem só pode ser recebida uma vez. Um bom exemplo disso é um sistema de inventário, no qual os terminais de ponto de vendas publicam dados em uma fila em vez de publicar diretamente no sistema de gerenciamento de inventário. Em seguida, o sistema de gerenciamento de inventário consome os dados a qualquer momento a fim de gerenciar o reabastecimento do estoque. Há muitas vantagens nisso, incluindo o sistema de gerenciamento de estoque não precisar estar sempre online. Para obter mais detalhes sobre as filas do Barramento de Serviço, consulte [Criar aplicativos que usam filas do Barramento de Serviço](service-bus-create-queues.md).

Uma fila particionada aumenta ainda mais a disponibilidade, a confiabilidade e a taxa de transferência de aplicativos, pois essas filas são particionadas em vários agentes e repositórios de mensagens.

### Criar filas particionadas

Você pode criar uma fila particionada por meio do [Portal clássico do Azure][] e do SDK do Barramento de Serviço. Para criar uma fila particionada, defina a propriedade [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx) como **true** na instância [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx). O código a seguir mostra como criar uma fila particionada usando o SDK do Barramento de Serviço.
 
```
// Create partitioned queue
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var queueDescription = new QueueDescription("myQueue");
queueDescription.EnablePartitioning = true;
nm.CreateQueue(queueDescription);
```

### Enviar e receber mensagens usando o AMQP

Você pode enviar mensagens para uma fila particionada e receber mensagens dela usando o AMQP como protocolo por meio da definição da propriedade [TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) como [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx), como mostra o trecho de código a seguir.

```
// Sending and receiving messages to and from a queue
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
var queueClient = QueueClient.CreateFromConnectionString(amqpConnectionString, "myQueue");

BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
queueClient.Send(message);

var receivedMessage = queueClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## Usar o AMQP com tópicos particionados

Tópicos são conceitualmente semelhantes a filas, mas podem encaminhar uma cópia da mesma mensagem para várias *assinaturas*. Em um tópico, os editores enviam mensagens para o tópico e os consumidores recebem as mensagens pelas assinaturas. No cenário de ponto de venda do sistema de estoque, os terminais publicam os dados no tópico. Em seguida, o sistema de gerenciamento de inventário recebe mensagens de uma assinatura. Além disso, um sistema de monitoramento pode receber a mesma mensagem de uma assinatura diferente. Para obter mais detalhes sobre tópicos e assinaturas do Barramento de Serviço, veja [Criar aplicativos que usam tópicos e assinaturas do Barramento de Serviço](service-bus-create-topics-subscriptions.md).

Assim como as filas, os tópicos particionados aumentam ainda mais a disponibilidade, a confiabilidade e a taxa de transferência de aplicativos, pois esses tópicos e suas assinaturas são particionadas em vários agentes e repositórios de mensagens.

### Criar tópicos particionados

É possível criar um tópico particionado usando o [portal clássico do Azure][] e o SDK do Barramento de Serviço. Para criar um tópico particionado, a propriedade [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx) deve ser definida como **true** na instância [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx). O código a seguir mostra como criar um tópico particionado usando o SDK do Barramento de Serviço.
	
```
// Create partitioned topic
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var topicDescription = new TopicDescription("myTopic");
topicDescription.EnablePartitioning = true;
nm.CreateTopic(topicDescription);

var subscriptionDescription = new SubscriptionDescription("myTopic", "mySubscription");
nm.CreateSubscription(subscriptionDescription);
```

### Enviar e receber mensagens usando o AMQP

É possível enviar mensagens para uma assinatura de tópico particionado e receber mensagens dela usando o AMQP como um protocolo, por meio da definição da propriedade [TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) como [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx), como mostra o código a seguir.

```
// Sending and receiving messages to a topic and from a subscription
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
	
var topicClient = TopicClient.CreateFromConnectionString(amqpConnectionString, "myTopic");
BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
topicClient.Send(message);
	
var subcriptionClient = SubscriptionClient.CreateFromConnectionString(amqpConnectionString, "myTopic", "mySubscription");
var receivedMessage = subcriptionClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## Próximas etapas

Veja as informações adicionais a seguir para saber mais sobre entidades de mensagens particionadas e sobre AMQP.

*    [Entidades de mensagens particionadas](service-bus-partitioning.md)
*    [OASIS Advanced Message Queuing Protocol (AMQP) Versão 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
*    [Suporte ao AMQP 1.0 no Barramento de Serviço](service-bus-amqp-overview.md)
*    [AMQP 1.0 no guia de protocolo do Barramento de Serviço e dos Hubs de Eventos do Azure](service-bus-amqp-protocol-guide.md)
*    [Como usar a API do Serviço de Mensagem Java (JMS) com Barramento de Serviço e AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
*    [Como usar os tópicos AMQP 1.0 com o Service Bus .NET API](service-bus-dotnet-advanced-message-queuing.md)

[Portal clássico do Azure]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0928_2016-->