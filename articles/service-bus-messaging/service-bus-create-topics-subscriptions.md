---
title: "Criar aplicativos que usam os tópicos e as assinaturas do Barramento de Serviço | Microsoft Docs"
description: "Introdução às funcionalidades de publicação/assinatura oferecidas pelos tópicos e pelas assinaturas do Barramento de Serviço."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a48fc9b0-b7b0-464e-8187-a517bf8b4eb4
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/10/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 994a379129bffd7457912bc349f240a970aed253
ms.openlocfilehash: 799ef33c924a0067bb5e8da9d1b4e50091dbabf6


---
# <a name="create-applications-that-use-service-bus-topics-and-subscriptions"></a>Criar aplicativos que usam os tópicos e as assinaturas do Barramento de Serviço
O Barramento de Serviço do Azure oferece suporte a um conjunto de tecnologias middleware orientado a mensagens, baseado em nuvem, incluindo o serviço de enfileiramento de mensagens confiável e o sistema de mensagens de publicação/assinatura durável. Este artigo baseia-se nas informações fornecidas em [Criar aplicativos que usam as filas do Barramento de Serviço](service-bus-create-queues.md) e oferece uma introdução às funcionalidades de publicação/assinatura oferecidas pelos tópicos do Barramento de Serviço.

## <a name="evolving-retail-scenario"></a>Cenário de varejo em evolução
Este artigo continua o cenário de varejo usado em [Criar aplicativos que usem filas do Barramento de Serviço](service-bus-create-queues.md). Relembre os dados de vendas de terminais de Ponto de Venda (PDV) individuais devem ser roteados para um sistema de gerenciamento de estoque que usa esses dados para determinar quando o estoque deverá ser reposto. Cada terminal de PDV relata seus dados de vendas enviando mensagens para a fila **DataCollectionQueue**, onde permanecem até que sejam recebidas pelo sistema de gerenciamento de estoque, como mostrado aqui:

![Barramento de Serviço 1](./media/service-bus-create-topics-subscriptions/IC657161.gif)

Para desenvolver este cenário, um novo requisito foi adicionado ao sistema: o proprietário da loja deseja ser capaz de monitorar como está o desempenho da loja em tempo real.

Para atender a esse requisito, o sistema deverá desativar o fluxo de dados de vendas. Ainda queremos que cada mensagem enviada por terminais de PDV seja enviada para o sistema de gerenciamento de estoque como antes, mas queremos outra cópia de cada mensagem que podemos usar para apresentar o modo de exibição de painel para o proprietário da loja.

Em qualquer situação como essa, na qual você exige que cada mensagem seja consumida por várias partes, é possível usar os *tópicos* do Barramento de Serviço. Os tópicos fornecem um padrão de publicação/assinatura na qual cada mensagem publicada é disponibilizada para uma ou mais assinaturas registradas no tópico. Por outro lado, com as filas, cada mensagem é recebida por um único consumidor.

As mensagens são enviadas para um tópico da mesma maneira como são enviadas para uma fila. No entanto, as mensagens não são recebidas do tópico diretamente; elas são recebidas de assinaturas. Você pode considerar uma assinatura em relação a um tópico como uma fila virtual que recebe cópias das mensagens enviadas para esse tópico. As mensagens são recebidas de uma assinatura da mesma maneira como são recebidas de uma fila.

Voltando ao cenário de varejo, a fila é substituída por um tópico, e uma assinatura é adicionada, que pode ser usada pelo componente do sistema de gerenciamento de estoque. O sistema agora aparece da seguinte maneira:

![Barramento de Serviço 2](./media/service-bus-create-topics-subscriptions/IC657165.gif)

A configuração aqui é executada de forma idêntica ao design baseado em fila anterior. Ou seja, as mensagens enviadas para o tópico são encaminhadas para a assinatura do **Estoque**, da qual o **Sistema de Gerenciamento de Estoque** as consome.

Para dar suporte ao painel de gerenciamento, criamos uma segunda assinatura no tópico, como mostrado aqui:

![Barramento de Serviço 3](./media/service-bus-create-topics-subscriptions/IC657166.gif)

Com essa configuração, cada mensagem dos terminais de PDV será disponibilizada para ambas as assinaturas, de **Painel** e **Estoque**.

## <a name="show-me-the-code"></a>Mostrar-me o código
O artigo [Criar aplicativos que usem as filas do Barramento de Serviço](service-bus-create-queues.md) descreve como se inscrever em uma conta do Azure e criar um namespace de serviço. Para usar o namespace do Barramento de Serviço, um aplicativo deverá referenciar o assembly do Barramento de Serviço, especificamente o Microsoft.ServiceBus.dll. A maneira mais fácil de referenciar as dependências do Barramento de Serviço é instalar o [pacote NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) do Barramento de Serviço. Você também pode encontrar o assembly como parte do SDK do Azure. O download está disponível na [página de download do SDK do Azure](https://azure.microsoft.com/downloads/).

### <a name="create-the-topic-and-subscriptions"></a>Criar o tópico e as assinaturas
As operações de gerenciamento para as entidades do sistema de mensagens do Barramento de Serviço (filas e tópicos de publicação/assinatura) são executadas por meio da classe [NamespaceManager](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager). As credenciais apropriadas são necessárias para criar uma instância do **NamespaceManager** para um namespace específico. O Barramento de Serviço usa um modelo de segurança baseado na [Assinatura de Acesso Compartilhado (SAS)](service-bus-sas-overview.md). A classe [TokenProvider](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.tokenprovider#microsoft_servicebus_tokenprovider) representa um provedor de token de segurança com métodos de fábrica internos que retornam alguns provedores de token conhecidos. Usaremos um método [CreateSharedAccessSignatureTokenProvider](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.tokenprovider#Microsoft_ServiceBus_TokenProvider_CreateSharedAccessSignatureTokenProvider_System_String_) para armazenar as credenciais SAS. Assim, a instância de [NamespaceManager](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) é construída com o endereço base do namespace do Barramento de Serviço e do provedor de token.

A classe [NamespaceManager](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) oferece métodos para criar, enumerar e excluir entidades do sistema de mensagens. O código exibido aqui mostra como a instância de **NamespaceManager** é criada e usada para criar o tópico **DataCollectionTopic**.

```csharp
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";

TokenProvider tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = new NamespaceManager(uri, tokenProvider);

namespaceManager.CreateTopic("DataCollectionTopic");
```

Observe que há sobrecargas do método [CreateTopic](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateTopic_System_String_) que permitem que as propriedades do tópico sejam definidas. Por exemplo, você pode definir o valor padrão da TTL (vida útil) das mensagens enviadas para o tópico. Em seguida, adicione as assinaturas de **Estoque** e **Painel**.

```csharp
namespaceManager.CreateSubscription("DataCollectionTopic", "Inventory");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard");
```

### <a name="send-messages-to-the-topic"></a>Enviar mensagens para o tópico
Para operações de tempo de execução em entidades do Barramento de Serviço; por exemplo, para enviar e receber mensagens, primeiro um aplicativo deverá criar um objeto [MessagingFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagingfactory#microsoft_servicebus_messaging_messagingfactory). Semelhante à classe [NamespaceManager](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager), a instância **MessagingFactory** será criada do endereço base do namespace de serviço e do provedor de token.

```
MessagingFactory factory = MessagingFactory.Create(uri, tokenProvider);
```

As mensagens enviadas para os tópicos do Barramento de Serviço e recebidas dele são instâncias da classe [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). Essa classe consiste em um conjunto de propriedades padrão (como [Label](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) e [TimeToLive](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive)), um dicionário usado para manter as propriedades do aplicativo e um corpo de dados de aplicativo arbitrários. Um aplicativo pode definir o corpo passando qualquer objeto serializável (o exemplo a seguir passa um objeto **SalesData** que representa os dados de vendas do terminal de PDV), que usará o [DataContractSerializer](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractserializer.aspx) para serializar o objeto. Como alternativa, poderá ser fornecido um objeto [Stream](https://msdn.microsoft.com/library/system.io.stream.aspx).

```csharp
BrokeredMessage bm = new BrokeredMessage(salesData);
bm.Label = "SalesReport";
bm.Properties["StoreName"] = "Redmond";
bm.Properties["MachineID"] = "POS_1";
```

A maneira mais fácil de enviar mensagens para o tópico é usar [CreateMessageSender](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_CreateMessageSender_System_String_) para criar um objeto [MessageSender](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagesender) diretamente da instância [MessagingFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagingfactory).

```csharp
MessageSender sender = factory.CreateMessageSender("DataCollectionTopic");
sender.Send(bm);
```

### <a name="receive-messages-from-a-subscription"></a>Receber mensagens de uma assinatura
Semelhante ao uso de filas, para receber mensagens de uma assinatura, você poderá usar um objeto [MessageReceiver](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagereceiver), que pode ser criado diretamente de [MessagingFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagingfactory) usando [CreateMessageReceiver](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_CreateMessageReceiver_System_String_). Você pode usar um de dois modos de recebimento diferentes (**ReceiveAndDelete** e **PeekLock**), como discutido em [Criar aplicativos que usam filas do Barramento de Serviço](service-bus-create-queues.md).

Observe que quando você cria um **MessageReceiver** para assinaturas, o parâmetro *entityPath* fica no formato`topicPath/subscriptions/subscriptionName`. Portanto, para criar um **MessageReceiver** para a assinatura de **Estoque** do tópico **DataCollectionTopic**, *entityPath* deverá ser definido como `DataCollectionTopic/subscriptions/Inventory`. O código aparece da seguinte maneira:

```csharp
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionTopic/subscriptions/Inventory");
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

## <a name="subscription-filters"></a>Filtros de assinatura
Até agora neste cenário, todas as mensagens enviadas ao tópico são disponibilizadas para todas as assinaturas registradas. A frase-chave aqui é "disponibilizadas". Embora as assinaturas do Barramento de Serviço vejam todas as mensagens enviadas para o tópico, você só poderá copiar um subconjunto dessas mensagens na fila de assinatura virtual. Isso é feito usando *filtros* de assinatura. Quando você cria uma assinatura, pode fornecer uma expressão de filtro na forma de um predicado de estilo SQL92 que funciona sobre as propriedades da mensagem, as propriedades do sistema (por exemplo, [Label](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label)) e as propriedades do aplicativo, como **StoreName** no exemplo anterior.

Fazendo o cenário evoluir para ilustrar isso, uma segunda loja deverá ser adicionada ao nosso cenário de varejo. Os dados de vendas de todos os terminais de PDV de ambas as lojas ainda precisam ser roteados para o sistema de gerenciamento de estoque centralizado, mas um gerente de loja usando a ferramenta de painel só está interessado no desempenho da loja dele. Você pode usar a filtragem de assinatura para conseguir isso. Observe que, quando os terminais de PDV publicarem mensagens, eles definem a propriedade do aplicativo **StoreName** na mensagem. Dadas duas lojas, por exemplo, **Redmond** e **Seattle**, os terminais de PDV na loja de Redmond carimbam as mensagens de dados sobre as vendas com um **StoreName** igual a **Redmond**, enquanto os terminais de PDV da loja de Seattle usam um **StoreName** igual a **Seattle**. O gerente da loja da loja de Redmond só deseja ver os dados de seus terminais de PDV. O sistema aparece da seguinte maneira:

![Service-Bus4](./media/service-bus-create-topics-subscriptions/IC657167.gif)

Para configurar esse roteamento, crie a assinatura de **Painel** da seguinte maneira:

```csharp
SqlFilter dashboardFilter = new SqlFilter("StoreName = 'Redmond'");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard", dashboardFilter);
```

Com esse [filtro de assinatura](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sqlfilter), somente as mensagens com a propriedade **StoreName** definida como **Redmond** serão copiadas na fila virtual da assinatura de **Painel**. No entanto, há muito mais sobre a filtragem de assinatura. Os aplicativos podem ter várias regras de filtro por assinatura, além da capacidade de modificar as propriedades de uma mensagem à medida que ela passa para uma fila virtual de uma assinatura.

## <a name="summary"></a>Resumo
Todos os motivos para usar o enfileiramento de mensagens descrito em [Criar aplicativos que usam as filas do Barramento de Serviço](service-bus-create-queues.md) também se aplicam aos tópicos, especificamente:

* Desacoplamento tempo - os produtores e os consumidores da mensagem não precisam estar online ao mesmo tempo.
* Nivelamento de carga – os picos de carga são suavizados pelo tópico, permitindo que os aplicativos de consumo sejam provisionados para a carga média em vez de para a carga de pico.
* Balanceamento de carga – semelhante a uma fila, você pode ter vários consumidores concorrentes escutando em uma única assinatura, com cada mensagem entregue para um dos consumidores, balanceando, assim, a carga.
* Acoplamento flexível – você pode fazer a rede de mensagens evoluir sem afetar os pontos de extremidade existentes; por exemplo, adicionando assinaturas ou alterando filtros de um tópico para permitir novos consumidores.

## <a name="next-steps"></a>Próximas etapas
Consulte [Criar aplicativos que usem as filas do Barramento de Serviço](service-bus-create-queues.md) para informações sobre como usar as filas no cenário de varejo de PDV.




<!--HONumber=Jan17_HO2-->


