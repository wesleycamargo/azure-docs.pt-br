<properties 
	pageTitle="Suporte a AMQP 1.0 para filas e tópicos particionados do Barramento de Serviço | Microsoft Azure" 
	description="Saiba como usar o protocolo Advanced Message Queuing Protocol (AMQP) 1.0 com filas e tópicos particionados do Barramento de Serviço." 
	services="service-bus" 
	documentationCenter=".net" 
	authors="hillaryc" 
	manager="hillaryc" 
	editor="hillaryc"/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="hillaryc"/>



# Suporte a AMQP 1.0 para filas e tópicos particionados do Barramento de Serviço 

Agora, o Barramento de Serviço do Azure dá suporte ao Advanced Message Queuing Protocol (**AMQP**) 1.0 para **Filas e tópicos particionados** do Barramento de Serviço do Azure.

**AMQP** é um protocolo padrão e aberto de enfileiramento de mensagens que permite o desenvolvimento de aplicativos entre plataformas usando várias linguagens de programação. Saiba mais sobre o suporte geral a AMQP pelo Barramento de Serviço em: [Suporte a AMQP 1.0 no Barramento de Serviço](service-bus-amqp-overview.md).

**Filas e tópicos particionados**, também chamados de entidades particionadas, oferecem alta disponibilidade, confiabilidade e taxa de transferência acima das filas e tópicos não particionados convencionais. Para obter mais detalhes sobre as entidades particionadas, confira [Particionando entidades de mensagens](https://msdn.microsoft.com/library/azure/dn520246.aspx).

A inclusão do AMQP 1.0 como um protocolo para comunicação com filas e tópicos particionados permite que os clientes criem aplicativos interoperáveis, aproveitando os benefícios de maior disponibilidade, confiabilidade e taxa de transferência oferecidos por entidades particionadas do Barramento de Serviço.

### Usando o AMQP com filas particionadas

As filas são úteis para situações que exigem o desacoplamento temporário, nivelamento de carga, balanceamento de carga e acoplamento flexível. Em uma fila, os editores enviam mensagens para a fila e os consumidores recebem mensagens da fila, sendo que uma mensagem só pode ser recebida uma vez. Um exemplo clássico é um sistema de inventário no qual os terminais de ponto de vendas publicariam dados em uma fila, em vez de publicar diretamente no sistema de gerenciamento de inventário. Em seguida, o sistema de gerenciamento de inventário consumiria os dados a qualquer momento a fim de gerenciar o reabastecimento do estoque. Há muitas vantagens, incluindo o sistema de gerenciamento de estoque não precisar estar sempre online. Encontre mais detalhes sobre as filas do Barramento de Serviço aqui: [Criando aplicativos que usam filas do Barramento de Serviço](https://msdn.microsoft.com/library/azure/hh689723.aspx)

Uma fila particionada aumentaria ainda mais a disponibilidade, a confiabilidade e a taxa de transferência de aplicativos, pois essas filas são particionadas em vários agentes e repositórios de mensagens.

#### Criando filas particionadas

É possível criar uma fila particionada por meio do Portal do Azure e do SDK do Barramento de Serviço. Para criar uma fila particionada, a propriedade EnablePartitioning deve ser definida como verdadeira na instância QueueDescription. O trecho de código a seguir mostra como criar uma usando o SDK do Barramento de Serviço.
 
	// Create partitioned queue
	var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
	var queueDescription = new QueueDescription("myQueue");
	queueDescription.EnablePartitioning = true;
	nm.CreateQueue(queueDescription);

#### Enviando e recebendo mensagens usando o AMQP

O envio de mensagens a uma fila particionada, e o recebimento de mensagens a partir de uma fila particionada, usando o AMQP como protocolo é realizado por meio da configuração de TransportType como TransportType.Amqp, como mostra o trecho de código a seguir.

	// Sending and receiving messages to and from a Queue
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


### Usando o AMQP com tópicos particionados

Assim como ocorre com as filas, os tópicos são úteis para situações que exigem o desacoplamento temporário, nivelamento de carga, balanceamento de carga e acoplamento flexível. Diferente das filas, os tópicos podem rotear uma cópia da mesma mensagem a vários assinantes. Em um tópico, os editores enviam mensagens para o tópico e os consumidores recebem as mensagens pelas assinaturas. No exemplo do sistema de inventário, os terminais de ponto de vendas publicariam os dados no tópico. Em seguida, o sistema de gerenciamento de inventário receberia mensagens de uma assinatura. Além disso, um sistema de monitoramento pode receber a mesma mensagem de uma assinatura diferente. Encontre mais detalhes sobre tópicos do Barramento de Serviço aqui: [Criando aplicativos que usam tópicos e assinaturas do Barramento de Serviço](https://msdn.microsoft.com/library/azure/hh699844.aspx)

Um tópico particionado aumentaria ainda mais a disponibilidade, a confiabilidade e a taxa de transferência de aplicativos, pois esses tópicos e suas assinaturas são particionadas em vários agentes e repositórios de mensagens.

#### Criando tópicos particionados

É possível criar um tópico particionado por meio do Portal do Azure e do SDK do Barramento de Serviço. Para criar um tópico particionado, a propriedade EnablePartitioning deve ser definida como verdadeira na instância TopicDescription. O trecho de código a seguir mostra como criar uma usando o SDK do Barramento de Serviço.
	
	// Create partitioned topic
	var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
	var topicDescription = new TopicDescription("myTopic");
	topicDescription.EnablePartitioning = true;
	nm.CreateTopic(topicDescription);

	var subscriptionDescription = new SubscriptionDescription("myTopic", "mySubscription");
	nm.CreateSubscription(subscriptionDescription);

#### Enviando e recebendo mensagens usando o AMQP

O envio de mensagens a um tópico particionado, e o recebimento de mensagens a partir da assinatura de um tópico particionado, usando o AMQP como protocolo é realizado por meio da configuração de TransportType como TransportType.Amqp, como mostra o trecho de código a seguir.

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


## Referências

*    [Entidades do Sistema de Mensagens de Particionamento](https://msdn.microsoft.com/library/azure/dn520246.aspx)
*    [OASIS Advanced Message Queuing Protocol (AMQP) Versão 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
*    [Suporte ao AMQP 1.0 no Barramento de Serviço](service-bus-amqp-overview.md)
*    [AMQP e Barramento de Serviço: Guia do desenvolvedor]("https://msdn.microsoft.com/library/azure/jj841071.aspx")
*    [Como usar a API do Serviço de Mensagem Java (JMS) com Barramento de Serviço e AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
*    [Como usar os tópicos AMQP 1.0 com o Service Bus .NET API](service-bus-dotnet-advanced-message-queuing.md)

<!---HONumber=Sept15_HO4-->