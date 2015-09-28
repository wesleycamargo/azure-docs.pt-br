<properties 
   pageTitle="Criar aplicativos que usem as filas do Barramento de Serviço | Microsoft Azure"
   description="Como gravar um aplicativo simples baseado em filas que usa o Barramento de Serviço."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/16/2015"
   ms.author="sethm" />

# Criar aplicativos que usem as filas do Barramento de Serviço

Este tópico descreve as filas do Barramento de Serviço e mostra como gravar um aplicativo simples baseado em filas que usa o Barramento de Serviço.

Considere um cenário do mundo de varejo em que os dados de vendas de terminais de Ponto de Venda (PDV) individuais devem ser roteados para um sistema de gerenciamento de estoque que usa esses dados para determinar quando o estoque deverá ser reposto. Esta solução usa o sistema de mensagens do Barramento de Serviço para a comunicação entre os terminais e o sistema de gerenciamento de estoque, como ilustrado na figura a seguir:

![Service-Bus-Queues-Img1](./media/service-bus-create-queues/IC657161.gif)

Cada terminal de PDV reporta seus dados de vendas enviando mensagens para a **DataCollectionQueue**. Essas mensagens permanecerão nessa fila até que sejam recuperadas pelo sistema de gerenciamento de estoque. Geralmente, esse padrão é chamado de *mensagens assíncronas*, porque o terminal de PDV não precisa esperar por uma resposta do sistema de gerenciamento de estoque para continuar o processamento.

## Por que usar filas?

Antes de examinarmos o código que é necessário para configurar este aplicativo, considere as vantagens de usar uma fila neste cenário em vez de ter os terminais de PDV se comunicando diretamente (de forma síncrona) com o sistema de gerenciamento de estoque.

### Desacoplamento temporal

Com o padrão de mensagens assíncrono, os produtores e consumidores não precisam estar online ao mesmo tempo. A infraestrutura do sistema de mensagens armazena mensagens até que a parte consumidora esteja pronta para recebê-las. Isso permite que os componentes do aplicativo distribuído sejam desconectados, ou voluntariamente, para manutenção, por exemplo, ou devido a uma falha de componente, sem afetar o sistema inteiro. Além disso, o aplicativo de consumo só precisará ficar online durante determinados momentos do dia. Por exemplo, neste cenário de varejo, é possível que o sistema de gerenciamento de estoque só precise ficar online no final do dia útil.

### Nivelamento de carga

Em muitos aplicativos, a carga do sistema varia ao longo do tempo enquanto o tempo de processamento necessário para cada unidade de trabalho é normalmente constante. Intermediar produtores de mensagem e consumidores com uma fila significa que o aplicativo de consumo (o trabalho) só precisa ser provisionado para servir uma carga média em vez de uma carga de pico. A profundidade da fila crescerá e contrairá conforme a carga de entradavariar. Isso economiza dinheiro diretamente em termos da quantidade de infraestrutura necessária para atender à carga do aplicativo.

![Service-Bus-Queues-Img2](./media/service-bus-create-queues/IC657162.gif)

### Balanceamento de carga

À medida que a carga aumenta, mais processos de trabalho poderão ser adicionados à leitura da fila de trabalho. Cada mensagem é processada por apenas umdos processos de trabalho. Além disso, esse balanceamento de carga baseado em pull permite o uso ideal dos computadores de trabalho, mesmo se os computadores de trabalho forem diferentes em relação à capacidade de processamento, já que eles receberão as mensagens por pull em sua própria taxa máxima. Esse padrão geralmente échamado de padrão de consumidor concorrente.

![Service-Bus-Queues-Img3](./media/service-bus-create-queues/IC657163.gif)

### Acoplamento flexível

A utilização de filas de mensagens para intermediar entre produtores de mensagens e consumidores oferece um acoplamento flexível intrínseco entre os componentes. Como produtores e consumidores não têm conhecimento uns dos outros, um consumidor poderá ser atualizado sem afetar o produtor. Além disso, a topologia do sistema de mensagens poderá evoluir sem afetar os pontos de extremidade existentes. Discutiremos um pouco mais desse assunto quando falarmos sobre publicação/assinatura.

## Mostrar-me o código

A seção a seguir mostra como usar o Barramento de Serviço para criar esse aplicativo.

### Inscrever-se em uma conta e em uma assinatura do Barramento de Serviço

Você precisará de uma conta do Azure para começar a trabalhar com o Barramento de Serviço. Se você ainda não tiver uma assinatura, poderá se inscrever em uma avaliação gratuita [aqui](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A85619ABF).

### Criar um namespace de serviço

Quando você tiver uma assinatura, poderá criar um novo namespace. Será necessário dar um nome exclusivo ao seu novo namespace em todas as contas do Barramento de Serviço. Cada namespace age como um contêiner para um conjunto de entidades do Barramento de Serviço. Para saber mais, confira [Como criar ou modificar um namespace do Barramento de Serviço](https://msdn.microsoft.com/library/azure/hh690931.aspx).

### Instalar o pacote NuGet

Para usar o namespace de serviço do Barramento de Serviço, um aplicativo deverá referenciar o assembly do Barramento de Serviço, especificamente o Microsoft.ServiceBus.dll. Esse assembly pode ser encontrado como parte do SDK do Microsoft Azure e o download está disponível na [página de download do SDK do Azure](http://azure.microsoft.com/downloads/). Entretanto, o pacote NuGet do Barramento de Serviço é a maneira mais fácil de obter a API do Barramento de Serviço e de configurar seu aplicativo com todas as dependências do Barramento de Serviço. Para obter detalhes sobre como usar o NuGet e o pacote do Barramento de Serviço, confira [Usando o pacote NuGet do Barramento de Serviço](https://msdn.microsoft.com/library/dn741354.aspx).

### Criar a fila

As operações de gerenciamento para as entidades do sistema de mensagens do Barramento de Serviço (filas e tópicos de publicação/assinatura) são executadas por meio da classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). O Barramento de Serviço usa um modelo de segurança baseado na [Assinatura de Acesso Compartilhado (SAS)](service-bus-sas-overview.md). A classe [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) representa um provedor de token de segurança com métodos de fábrica internos que retornam alguns provedores de token conhecidos. Usaremos um método [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) para armazenar as credenciais SAS. A instância de [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) é construída com o endereço base do namespace do Barramento de Serviço e do provedor de token.

A classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) oferece métodos para a criação, a enumeração e a exclusão de entidades do sistema de mensagens. O código exibido aqui mostra como a instância de [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) é criada e usada para criar a fila **DataCollectionQueue**.

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", 
                "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
 
TokenProvider tokenProvider = 
    TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = 
    new NamespaceManager(uri, tokenProvider);
namespaceManager.CreateQueue("DataCollectionQueue");
```

Observe que há sobrecargas do método [CreateQueue](https://msdn.microsoft.com/library/azure/hh322663.aspx) que permitem que as propriedades da fila sejam ajustadas. Por exemplo, você pode definir o valor padrão da vida útil (TTL) para as mensagens enviadas para a fila.

### Enviar mensagens para a fila

Para operações de tempo de execução em entidades do Barramento de Serviço; por exemplo, para enviar e receber mensagens, primeiro um aplicativo deverá criar um objeto [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx). Semelhante à classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx), a instância [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) será criada do endereço base do namespace de serviço e do provedor de token.

```
 BrokeredMessage bm = new BrokeredMessage(salesData);
 bm.Label = "SalesReport";
 bm.Properties["StoreName"] = "Redmond";
 bm.Properties["MachineID"] = "POS_1";
```

As mensagens enviadas e recebidas das filas do Barramento de Serviço são instâncias da classe [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Essa classe consiste em um conjunto de propriedades padrão (como [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) e [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), um dicionário usado para manter as propriedades do aplicativo e um corpo de dados de aplicativo arbitrários. Um aplicativo pode definir o corpo passando qualquer objeto serializável (o exemplo a seguir passa um objeto **SalesData** que representa os dados de vendas do terminal de PDV), que usará o [DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) para serializar o objeto. Como alternativa, poderá ser fornecido um objeto [Stream](https://msdn.microsoft.com/library/azure/system.io.stream.aspx).

A maneira mais fácil de enviar mensagens para uma determinada fila, no nosso caso, **DataCollectionQueue**, é usar [CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) para criar um objeto [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) diretamente da instância de [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx).

```
MessageSender sender = factory.CreateMessageSender("DataCollectionQueue");
sender.Send(bm);
```

### Recebendo mensagens da fila

A maneira mais fácil de receber mensagens da fila é usar um objeto [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx), que pode ser criado diretamente de [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx), usando o [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx). Os receptores da mensagem poderão trabalhar em dois modos diferentes: **ReceiveAndDelete** e **PeekLock**. O [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) é definido quando o receptor da mensagem é criado como um parâmetro para a chamada a [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx).


Quando você usar o modo **ReceiveAndDelete**, a recepção será uma operação única; ou seja, quando o Barramento de Serviço receber a solicitação, marcará a mensagem como sendo consumida e a retornará ao aplicativo. O modo **ReceiveAndDelete** é o modelo mais simples e funciona melhor em cenários nos quais o aplicativo pode tolerar o não processamento de uma mensagem caso ocorra uma falha. Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la. Já que o Barramento de Serviço marcou a mensagem como consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, terá perdido a mensagem consumida antes da falha.

No modo **PeekLock**, o recebimento de uma mensagem se torna uma operação de dois estágios, o que possibilita o suporte aos aplicativos que não podem tolerar mensagens ausentes. Quando o Barramento de Serviço recebe a solicitação, ele encontra a próxima mensagem a ser consumida, a bloqueia para evitar que outros clientes a recebam e a retorna para o aplicativo. Depois que o aplicativo conclui o processamento da mensagem (ou a armazena de forma segura para processamento futuro), ele conclui a segunda etapa do processo de recebimento chamando [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) na mensagem recebida. Quando o Barramento de Serviço vê a chamada a [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx), marca a mensagem como sendo consumida.

Dois outros resultados são possíveis. Primeiro, se o aplicativo não for capaz de processar a mensagem por algum motivo, ele chamará [Abandon](https://msdn.microsoft.com/library/azure/hh181837.aspx) na mensagem recebida (em vez do método [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). Isso fará com que o Barramento de Serviço desbloqueie a mensagem na fila e disponibilize-a para que ela possa ser recebida novamente pelo mesmo consumidor ou por outro consumidor concorrente. Em segundo lugar, há um tempo limite associado a um bloqueio e, se o aplicativo não conseguir processar a mensagem antes da expiração do tempo limite de bloqueio (por exemplo, se o aplicativo falhar), o Barramento de Serviço desbloqueará a mensagem e a disponibilizará para ser recebida novamente.

Observe que, se houver falha do aplicativo após o processamento da mensagem, mas antes da solicitação [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) ser emitida, a mensagem será entregue novamente ao aplicativo quando ele reiniciar. Isso é geralmente chamado de processamento *Pelo Menos Uma Vez*. Isso significa que cada mensagem será processada pelo menos uma vez mas, em determinadas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não tolerar o processamento duplicado, será necessária lógica adicional no aplicativo para detectar duplicatas. Isso pode ser feito com base na propriedade [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) da mensagem. O valor dessa propriedade permanece constante nas tentativas de entrega. Isso é conhecido como processamento *Exatamente Uma Vez*.

O código que é mostrado aqui recebe e processa uma mensagem usando o modo **PeekLock**, que será o padrão caso nenhum valor [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) seja explicitamente fornecido.

```
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionQueue");
BrokeredMessage receivedMessage = receiver.Receive();
try
{
    ProcessMessage(receivedMessage);
    receivedMessage.Complete();
}
catch (Exception e)
{
    receivedMessage.Abandon();
}
```

### Usar o cliente de fila

Os exemplos anteriores desta seção criaram objetos [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) e [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) diretamente da [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) para enviar e receber mensagens da fila, respectivamente. Uma abordagem alternativa é usar a classe [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx), que dá suporte a operações de envio e de recebimento, além de recursos mais avançados, como as sessões.

```
QueueClient queueClient = factory.CreateQueueClient("DataCollectionQueue");
queueClient.Send(bm);
            
BrokeredMessage message = queueClient.Receive();

try
{
    ProcessMessage(message);
    message.Complete();
}
catch (Exception e)
{
    message.Abandon();
} 
```

## Próximas etapas

Agora que você aprendeu os conceitos básicos sobre filas, confira [Criar aplicativos que usam assinaturas e tópicos do Barramento de Serviço](service-bus-create-topics-subscriptions.md) para continuar essa discussão usando os recursos de publicação/assinatura do sistema de mensagens agenciado do Barramento de Serviço.

<!---HONumber=Sept15_HO3-->