---
title: "Criar aplicativos que usem as filas do Barramento de Serviço | Microsoft Docs"
description: "Como gravar um aplicativo simples baseado em filas que usa o Barramento de Serviço."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 754d91b3-1426-405e-84b4-fd36d65b114a
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/03/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 2350c3e222277b6d8e837472f55a7b79346d3d21


---
# <a name="create-applications-that-use-service-bus-queues"></a>Criar aplicativos que usem as filas do Barramento de Serviço
Este tópico descreve as filas do Barramento de Serviço e mostra como gravar um aplicativo simples baseado em filas que usa o Barramento de Serviço.

Considere um cenário do mundo de varejo no qual os dados de vendas de terminais de PDV (Ponto de Venda) individuais devem ser roteados para um sistema de gerenciamento de estoque que usa esses dados para determinar quando o estoque deverá ser reabastecido. Esta solução usa o sistema de mensagens do Barramento de Serviço para a comunicação entre os terminais e o sistema de gerenciamento de estoque, como ilustrado na figura a seguir:

![Filas de Barramento de Serviço imagem 1](./media/service-bus-create-queues/IC657161.gif)

Cada terminal de PDV reporta seus dados de vendas enviando mensagens para a **DataCollectionQueue**. Essas mensagens permanecerão nessa fila até que sejam recuperadas pelo sistema de gerenciamento de estoque. Geralmente, esse padrão é chamado de *mensagens assíncronas*, porque o terminal de PDV não precisa esperar por uma resposta do sistema de gerenciamento de estoque para continuar o processamento.

## <a name="why-queuing"></a>Por que usar filas?
Antes de examinarmos o código que é necessário para configurar este aplicativo, considere as vantagens de usar uma fila neste cenário em vez de ter os terminais de PDV se comunicando diretamente (de forma síncrona) com o sistema de gerenciamento de estoque.

### <a name="temporal-decoupling"></a>Desacoplamento temporal
Com o padrão de mensagens assíncrono, os produtores e consumidores não precisam estar online ao mesmo tempo. A infraestrutura do sistema de mensagens armazena mensagens até que a parte consumidora esteja pronta para recebê-las. Isso significa que os componentes do aplicativo distribuído podem ser desconectados, voluntariamente, para manutenção, por exemplo, ou devido a uma falha de componente, sem afetar o sistema inteiro. Além disso, o aplicativo de consumo só precisará ficar online durante determinados momentos do dia. Por exemplo, neste cenário de varejo, é possível que o sistema de gerenciamento de estoque só precise ficar online no final do dia útil.

### <a name="load-leveling"></a>Nivelamento de carga
Em muitos aplicativos, a carga do sistema varia ao longo do tempo enquanto o tempo de processamento necessário para cada unidade de trabalho é normalmente constante. Intermediar produtores de mensagem e consumidores com uma fila significa que o aplicativo de consumo (o trabalho) só precisa ser provisionado para servir uma carga média em vez de uma carga de pico. A profundidade da fila crescerá e contrairá conforme a carga de entradavariar. Isso economiza dinheiro diretamente em termos da quantidade de infraestrutura necessária para atender à carga do aplicativo.

![Filas de Barramento de Serviço imagem 2](./media/service-bus-create-queues/IC657162.gif)

### <a name="load-balancing"></a>Balanceamento de carga
À medida que a carga aumenta, mais processos de trabalho poderão ser adicionados à leitura da fila de trabalho. Cada mensagem é processada por apenas umdos processos de trabalho. Além disso, esse balanceamento de carga baseado em pull permite o uso ideal dos computadores de trabalho, mesmo se os computadores de trabalho forem diferentes em relação à capacidade de processamento, já que eles receberão as mensagens por pull em sua própria taxa máxima. Esse padrão geralmente échamado de padrão de consumidor concorrente.

![Filas de Barramento de Serviço imagem 3](./media/service-bus-create-queues/IC657163.gif)

### <a name="loose-coupling"></a>Acoplamento flexível
A utilização de filas de mensagens para intermediar entre produtores de mensagens e consumidores oferece um acoplamento flexível intrínseco entre os componentes. Como produtores e consumidores não têm conhecimento uns dos outros, um consumidor poderá ser atualizado sem afetar o produtor. Além disso, a topologia do sistema de mensagens poderá evoluir sem afetar os pontos de extremidade existentes. Discutiremos um pouco mais desse assunto quando falarmos sobre publicação/assinatura.

## <a name="show-me-the-code"></a>Mostrar-me o código
A seção a seguir mostra como usar o Barramento de Serviço para criar esse aplicativo.

### <a name="sign-up-for-an-azure-account"></a>Inscrever-se em uma conta do Azure
Você precisará de uma conta do Azure para começar a trabalhar com o Barramento de Serviço. Se você ainda não tiver uma assinatura, poderá se inscrever em uma conta gratuita [aqui](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A85619ABF).

### <a name="create-a-namespace"></a>Criar um namespace
Quando tiver uma assinatura, você poderá [criar um novo namespace](service-bus-create-namespace-portal.md). Cada namespace age como um contêiner de escopo para um conjunto de entidades do Barramento de Serviço. Dê um nome exclusivo ao seu novo namespace em todas as contas do Barramento de Serviço. 

### <a name="install-the-nuget-package"></a>Instalar o pacote NuGet
Para usar o namespace do Barramento de Serviço, um aplicativo deverá fazer referência ao assembly do Barramento de Serviço, especificamente, Microsoft.ServiceBus.dll. Esse assembly pode ser encontrado como parte do SDK do Microsoft Azure e o download está disponível na [página de download do SDK do Azure](https://azure.microsoft.com/downloads/). Entretanto, o [pacote NuGet do Barramento de Serviço](https://www.nuget.org/packages/WindowsAzure.ServiceBus) é a maneira mais fácil de obter a API do Barramento de Serviço e de configurar seu aplicativo com todas as dependências do Barramento de Serviço.

### <a name="create-the-queue"></a>Criar a fila
As operações de gerenciamento para as entidades do sistema de mensagens do Barramento de Serviço (filas e tópicos de publicação/assinatura) são executadas por meio da classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). O Barramento de Serviço usa um modelo de segurança baseado na [Assinatura de Acesso Compartilhado (SAS)](service-bus-sas-overview.md). A classe [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) representa um provedor de token de segurança com métodos de fábrica internos que retornam alguns provedores de token conhecidos. Usaremos um método [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) para armazenar as credenciais SAS. Assim, a instância de [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) é construída com o endereço base do namespace do Barramento de Serviço e do provedor de token.

A classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) oferece métodos para criar, enumerar e excluir entidades do sistema de mensagens. O código exibido aqui mostra como a instância de [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) é criada e usada para criar a fila **DataCollectionQueue**.

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

### <a name="send-messages-to-the-queue"></a>Enviar mensagens para a fila
Para operações de tempo de execução em entidades do Barramento de Serviço; por exemplo, para enviar e receber mensagens, primeiro um aplicativo deverá criar um objeto [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx). Semelhante à classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx), a instância [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) será criada do endereço base do namespace de serviço e do provedor de token.

```
 BrokeredMessage bm = new BrokeredMessage(salesData);
 bm.Label = "SalesReport";
 bm.Properties["StoreName"] = "Redmond";
 bm.Properties["MachineID"] = "POS_1";
```

As mensagens enviadas para (e recebidas de) filas de Barramento de Serviço são instâncias da classe [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) . Essa classe consiste em um conjunto de propriedades padrão (como [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) e [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), um dicionário usado para manter as propriedades do aplicativo e um corpo de dados de aplicativo arbitrários. Um aplicativo pode definir o corpo passando qualquer objeto serializável (o exemplo a seguir passa um objeto **SalesData** que representa os dados de vendas do terminal de PDV), que usará o [DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) para serializar o objeto. Como alternativa, poderá ser fornecido um objeto [Stream](https://msdn.microsoft.com/library/azure/system.io.stream.aspx).

A maneira mais fácil de enviar mensagens para uma determinada fila, no nosso caso, **DataCollectionQueue**, é usar [CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) para criar um objeto [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) diretamente da instância de [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx).

```
MessageSender sender = factory.CreateMessageSender("DataCollectionQueue");
sender.Send(bm);
```

### <a name="receiving-messages-from-the-queue"></a>Recebendo mensagens da fila
Para receber mensagens da fila, você pode usar um objeto [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx), que pode ser criado diretamente de [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) usando [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx). Os receptores da mensagem poderão trabalhar em dois modos diferentes: **ReceiveAndDelete** e **PeekLock**. O [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) é definido quando o receptor da mensagem é criado como um parâmetro para a chamada a [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx).

Ao usar o modo **ReceiveAndDelete**, o recebimento é uma operação única, isto é, quando o Barramento de Serviço recebe uma solicitação de leitura de uma mensagem, ele marca a mensagem como sendo consumida e a retorna para o aplicativo. O modo **ReceiveAndDelete** é o modelo mais simples e funciona melhor em cenários nos quais o aplicativo pode tolerar o não processamento de uma mensagem caso ocorra uma falha. Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la. Já que o Barramento de Serviço marcou a mensagem como consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, terá perdido a mensagem consumida antes da falha.

No modo **PeekLock**, o recebimento de uma mensagem se torna uma operação de dois estágios, o que possibilita o suporte aos aplicativos que não podem tolerar mensagens ausentes. Quando o Barramento de Serviço recebe a solicitação, ele encontra a próxima mensagem a ser consumida, a bloqueia para evitar que outros clientes a recebam e a retorna para o aplicativo. Depois que o aplicativo conclui o processamento da mensagem (ou a armazena de forma segura para processamento futuro), ele conclui a segunda etapa do processo de recebimento chamando [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) na mensagem recebida. Quando o Barramento de Serviço vê a chamada a [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx), marca a mensagem como sendo consumida.

Dois outros resultados são possíveis. Primeiro, se o aplicativo não for capaz de processar a mensagem por algum motivo, ele chamará [Abandon](https://msdn.microsoft.com/library/azure/hh181837.aspx) na mensagem recebida (em vez do método [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). Isso fará com que o Barramento de Serviço desbloqueie a mensagem na fila e disponibilize-a para que ela possa ser recebida novamente pelo mesmo consumidor ou por outro consumidor concorrente. Em segundo lugar, há um tempo limite associado a um bloqueio e, se o aplicativo não conseguir processar a mensagem antes da expiração do tempo limite de bloqueio (por exemplo, se o aplicativo falhar), o Barramento de Serviço desbloqueará a mensagem e a disponibilizará para ser recebida novamente (basicamente executando uma operação [Abandon](https://msdn.microsoft.com/library/azure/hh181837.aspx) por padrão).

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

### <a name="use-the-queue-client"></a>Usar o cliente de fila
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

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu os conceitos básicos sobre filas, confira [Criar aplicativos que usam os tópicos e as assinaturas do Barramento de Serviço](service-bus-create-topics-subscriptions.md) para continuar essa discussão usando as funcionalidades de publicação/assinatura dos tópicos e das assinaturas do Barramento de Serviço.




<!--HONumber=Nov16_HO3-->


