---
title: "Usar os tópicos de Barramento de Serviço com o .NET | Microsoft Docs"
description: "Saiba como usar as assinaturas e os tópicos do Barramento de Serviço com o .NET no Azure. Os códigos de exemplo são escritos para aplicativos .NET."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 31d0bc29-6524-4b1b-9c7f-aa15d5a9d3b4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 12/21/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 5565ba8795127ffbdecbe8b764d3aa7f4b93f784
ms.openlocfilehash: f76734eb4081e08603d98b6a1be11cade3130b1d


---
# <a name="how-to-use-service-bus-topics-and-subscriptions"></a>Como usar tópicos e assinaturas do Barramento de Serviço
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este artigo descreve como usar tópicos e assinaturas do Barramento de Serviço. Os exemplos são escritos em C# e utilizam APIs .NET. Os cenários abordados incluem a criação de tópicos e assinaturas, a criação de filtros de assinatura, o envio de mensagens para um tópico, o recebimento de mensagens de uma assinatura e a exclusão de tópicos e assinaturas. Para saber mais sobre tópicos e assinaturas, confira a seção [Próximas etapas](#next-steps).

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## <a name="configure-the-application-to-use-service-bus"></a>Configurar o aplicativo para usar o Barramento de Serviço
Quando você cria um aplicativo que usa o Barramento de Serviço, você deve adicionar uma referência ao assembly do Barramento de Serviço e incluir os espaços para nome correspondentes. A maneira mais fácil de fazer isso é baixar o pacote [NuGet](https://www.nuget.org) adequado.

## <a name="get-the-service-bus-nuget-package"></a>Obtenha o pacote do NuGet do Barramento de Serviço
O [pacote NuGet do Barramento de Serviço](https://www.nuget.org/packages/WindowsAzure.ServiceBus) é a maneira mais fácil de obter a API do Barramento de Serviço e configurar seu aplicativo com todas as dependências de Barramento de Serviço necessárias. Para instalar o pacote do NuGet do Barramento de Serviço em seu projeto, faça o seguinte:

1. No Gerenciador de Soluções, clique com o botão direito do mouse em **Referências** e clique em **Gerenciar Pacotes NuGet**.
2. Procure "Barramento de Serviço" e selecione o item **Barramento de Serviço do Microsoft Azure** . Clique em **Instalar** para concluir a instalação e feche a seguinte caixa de diálogo:
   
   ![][7]

Agora você está pronto para escrever código para o Barramento de Serviço.

## <a name="create-a-service-bus-connection-string"></a>Criar uma cadeia de conexão do Barramento de Serviço
O Barramento de Serviço usa uma cadeia de conexão para armazenar pontos de extremidade e credenciais. Você pode colocar a cadeia de conexão em um arquivo de configuração, em vez de embuti-la no código:

* Ao usar os serviços do Azure, é recomendável armazenar a cadeia de conexão usando o sistema de configuração de serviço do Azure (arquivos .csdef e .cscfg).
* Ao usar sites do Azure ou Máquinas Virtuais do Azure, é recomendável armazenar a cadeia de conexão usando o sistema de configuração do .NET (por exemplo, o arquivo Web.config).

Nos dois casos, você pode recuperar a cadeia de conexão usando o método `CloudConfigurationManager.GetSetting`, como mostrado mais adiante neste artigo.

### <a name="configure-your-connection-string"></a>Configurar a cadeia de conexão
O mecanismo de configuração do serviço permite que você altere dinamicamente as definições da configuração no [portal do Azure][Azure portal] sem reimplantar o aplicativo. Por exemplo, adicione um rótulo `Setting` ao seu arquivo de definição de serviço (**.csdef**), como mostrado no próximo exemplo.

```xml
<ServiceDefinition name="Azure1">
...
    <WebRole name="MyRole" vmsize="Small">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
...
</ServiceDefinition>
```

Em seguida, especifique valores no arquivo de configuração do serviço (.cscfg).

```xml
<ServiceConfiguration serviceName="Azure1">
...
    <Role name="MyRole">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString"
                     value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </ConfigurationSettings>
    </Role>
...
</ServiceConfiguration>
```

Use o nome da chave da SAS (Assinatura de Acesso Compartilhado) e os valores de chave recuperados do portal, conforme descrito anteriormente.

### <a name="configure-your-connection-string-when-using-azure-websites-or-azure-virtual-machines"></a>Configurar a cadeia de conexão ao usar sites ou Máquinas Virtuais do Azure
Ao usar sites ou Máquinas Virtuais, é recomendável usar o sistema de configuração .NET (por exemplo, Web.config). Você pode armazenar a cadeia de conexão usando o elemento `<appSettings>`.

```xml
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

Use o nome da SAS e os valores de chave recuperados no [portal do Azure][Azure portal], conforme descrito anteriormente.

## <a name="create-a-topic"></a>Criar um tópico
Você pode executar operações de gerenciamento de tópicos e assinaturas do Barramento de Serviço usando a classe [NamespaceManager](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager). Essa classe fornece métodos para criar, enumerar e excluir tópicos.

O exemplo a seguir constrói um objeto `NamespaceManager` usando a classe `CloudConfigurationManager` do Azure com uma cadeia de conexão que consiste no endereço básico de um namespace do Barramento de Serviço e nas credenciais apropriadas da SAS com permissões para gerenciá-lo. Essa cadeia de conexão tem o seguinte formato:

```xml
Endpoint=sb://<yourNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<yourKey>
```

Use o exemplo a seguir, considerando as definições de configuração da seção anterior.

```csharp
// Create the topic if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic("TestTopic");
}
```

Há sobrecargas do método [CreateTopic](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager) que permitem definir as propriedades do tópico, por exemplo, definir o valor de TTL (vida útil) padrão a ser aplicado às mensagens enviadas ao tópico. Essas configurações são aplicadas usando a classe [TopicDescription](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.topicdescription). O exemplo a seguir mostra como criar um tópico chamado **TestTopic** com um tamanho máximo de 5 GB e uma mensagem padrão de TTL de 1 minuto.

```csharp
// Configure Topic Settings.
TopicDescription td = new TopicDescription("TestTopic");
td.MaxSizeInMegabytes = 5120;
td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new Topic with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic(td);
}
```

> [!NOTE]
> Você pode usar o método [TopicExists](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_TopicExists_System_String_) em objetos [NamespaceManager](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager) para verificar se uma fila com um nome especificado já existe no namespace.
> 
> 

## <a name="create-a-subscription"></a>Criar uma assinatura
Você também pode criar assinaturas de tópico usando a classe [NamespaceManager](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager). As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens passadas para a fila virtual da assinatura.

> [!IMPORTANT]
> Para que as mensagens sejam recebidas por uma assinatura, você deve criar essa assinatura antes de enviar as mensagens ao tópico. Se não houver nenhuma assinatura em um tópico, o tópico descartará as mensagens.
> 
> 

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Criar uma assinatura com o filtro padrão (MatchAll)
Se nenhum filtro for especificado quando uma nova assinatura for criada, o filtro **MatchAll** será o padrão usado. Quando você usa o filtro **MatchAll**, todas as mensagens publicadas no tópico são colocadas na fila virtual da assinatura. O exemplo a seguir cria uma assinatura denominada “AllMessages” e usa o filtro padrão **MatchAll**.

```csharp
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
{
    namespaceManager.CreateSubscription("TestTopic", "AllMessages");
}
```

### <a name="create-subscriptions-with-filters"></a>Criar assinaturas com os filtros
Você também pode configurar filtros que permitem especificar quais mensagens enviadas a um tópico devem aparecer dentro de uma assinatura específica do tópico.

O tipo de filtro mais flexível com suporte das assinaturas é a classe [SqlFilter][SqlFilter], que implementa um subconjunto do SQL92. Os filtros SQL operam nas propriedades das mensagens que são publicadas no tópico. Para saber mais sobre as expressões que podem ser usadas com um filtro SQL, confira a sintaxe [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

O exemplo a seguir cria uma assinatura denominada **HighMessages** com um objeto [SqlFilter][SqlFilter] que seleciona apenas as mensagens que tenham uma propriedade **MessageNumber** personalizada maior do que 3.

```csharp
// Create a "HighMessages" filtered subscription.
SqlFilter highMessagesFilter =
   new SqlFilter("MessageNumber > 3");

namespaceManager.CreateSubscription("TestTopic",
   "HighMessages",
   highMessagesFilter);
```

De maneira semelhante, o exemplo a seguir cria uma assinatura denominada **LowMessages** com um [SqlFilter][SqlFilter] que seleciona apenas as mensagens que tenham uma propriedade **MessageNumber** menor ou igual a 3.

```csharp
// Create a "LowMessages" filtered subscription.
SqlFilter lowMessagesFilter =
   new SqlFilter("MessageNumber <= 3");

namespaceManager.CreateSubscription("TestTopic",
   "LowMessages",
   lowMessagesFilter);
```

Agora, quando uma mensagem for enviada para `TestTopic`, ela sempre será fornecida aos destinatários inscritos na assinatura do tópico **AllMessages** e será fornecida de forma seletiva para os destinatários inscritos nas assinaturas dos tópicos **HighMessages** e **LowMessages** (dependendo do conteúdo da mensagem).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico
Para enviar uma mensagem a um tópico do Barramento de Serviço, seu aplicativo cria um objeto [TopicClient](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.topicclient) usando a cadeia de conexão.

O código a seguir demonstra como criar um objeto [TopicClient](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.topicclient) para o tópico **TestTopic** criado anteriormente usando a chamada à API [CreateFromConnectionString](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.topicclient#Microsoft_ServiceBus_Messaging_TopicClient_CreateFromConnectionString_System_String_System_String_).

```csharp
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

TopicClient Client =
    TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

Client.Send(new BrokeredMessage());
```

As mensagens enviadas aos tópicos de Barramento de Serviço são instâncias da classe [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). Os objetos **BrokeredMessage** têm um conjunto de propriedades padrão (como [Label](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label) e [TimeToLive](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive)), um dicionário usado para manter as propriedades personalizadas específicas do aplicativo e um corpo de dados de aplicativo arbitrários. Um aplicativo pode definir o corpo da mensagem, passando qualquer objeto serializável para o construtor do objeto **BrokeredMessage** e o **DataContractSerializer** adequado será usado para serializar o objeto. Como alternativa, é possível fornecer um objeto **System.IO.Stream**.

O exemplo a seguir demonstra como enviar cinco mensagens de teste ao objeto **TestTopic** [TopicClient](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.topicclient) obtido no exemplo de código anterior. Observe que o valor da propriedade [MessageId](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) de cada mensagem varia dependendo da iteração do loop (isso determina qual assinatura o recebe).

```csharp
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set additional custom app-specific property.
  message.Properties["MessageId"] = i;

  // Send message to the topic.
  Client.Send(message);
}
```

Os tópicos do Barramento de Serviço dão suporte ao tamanho máximo de mensagem de 256 KB na [camada Standard](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicativo padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não há nenhum limite no número de mensagens mantidas em um tópico, mas há uma capacidade do tamanho total das mensagens mantidas por um tópico. O tamanho do tópico é definido no momento da criação, com um limite máximo de 5 GB. Se o particionamento estiver habilitado, o limite superior será maior. Para obter mais informações, consulte [Entidades de mensagens particionadas](service-bus-partitioning.md).

## <a name="how-to-receive-messages-from-a-subscription"></a>Como receber mensagens de uma assinatura
A maneira recomendada de receber mensagens de uma assinatura é usar um objeto [SubscriptionClient](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptionclient). Os objetos **SubscriptionClient** podem funcionar em dois modos diferentes: [*ReceiveAndDelete* e *PeekLock*](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode).

Quando o modo **ReceiveAndDelete** for usado, o recebimento será uma operação única, ou seja, quando o Barramento de Serviço receber uma solicitação de leitura de uma mensagem em uma assinatura, ele marcará a mensagem como sendo consumida e a retornará ao aplicativo. O modo **ReceiveAndDelete** é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo pode tolerar o não processamento de uma mensagem em caso de falha. Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la. Como o Barramento de Serviço marca a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

No modo **PeekLock** (que é o modo padrão), o processo de recebimento se torna uma operação de duas etapas, o que torna possível o suporte a aplicativos que não toleram mensagens ausentes. Quando o Barramento de Serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, a bloqueia para evitar que outros clientes a recebam e a retorna para o aplicativo. Depois que o aplicativo conclui o processamento da mensagem (ou a armazena de forma segura para processamento futuro), ele conclui a segunda etapa do processo de recebimento chamando [Complete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) na mensagem recebida. Quando o Barramento de Serviço vê a chamada **Complete**, ele marca a mensagem como sendo consumida e remove-a da assinatura.

O exemplo a seguir demonstra como as mensagens podem ser recebidas e processadas usando o modo **PeekLock** padrão. Para especificar outro valor de [ReceiveMode](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode), você pode usar outra sobrecarga para [CreateFromConnectionString](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_CreateFromConnectionString_System_String_System_String_System_String_Microsoft_ServiceBus_Messaging_ReceiveMode_). Este exemplo usa o retorno de chamada [OnMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__Microsoft_ServiceBus_Messaging_OnMessageOptions_) para processar mensagens assim que chegam à assinatura **HighMessages**.

```csharp
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

SubscriptionClient Client =
    SubscriptionClient.CreateFromConnectionString
            (connectionString, "TestTopic", "HighMessages");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

Client.OnMessage((message) =>
{
    try
    {
        // Process message from subscription.
        Console.WriteLine("\n**High Messages**");
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Message Number: " +
            message.Properties["MessageNumber"]);

        // Remove message from subscription.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in subscription.
        message.Abandon();
    }
}, options);
```

Este exemplo configura o retorno de chamada [OnMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptionclient#Microsoft_ServiceBus_Messaging_SubscriptionClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__Microsoft_ServiceBus_Messaging_OnMessageOptions_) usando um objeto [OnMessageOptions](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.onmessageoptions). [AutoComplete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.onmessageoptions#Microsoft_ServiceBus_Messaging_OnMessageOptions_AutoComplete) é definido como **false** para habilitar o controle manual de quando chamar [Complete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) na mensagem recebida. [AutoRenewTimeout](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.onmessageoptions#Microsoft_ServiceBus_Messaging_OnMessageOptions_AutoRenewTimeout) é definido como 1 minuto, o que faz com que o cliente aguarde até um minuto antes do encerramento do recurso de renovação automática e que o cliente faça uma nova chamada para verificar se há mensagens. Esse valor de propriedade reduz o número de vezes que o cliente faz chamadas passíveis de cobrança que não recuperam mensagens.

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como tratar falhas do aplicativo e mensagens ilegíveis
O Barramento de Serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não for capaz de processar a mensagem por algum motivo, ele chamará o método [Abando](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon_System_Collections_Generic_IDictionary_System_String_System_Object__)n na mensagem recebida (em vez do método [Complete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete)). Isso fará com que o Barramento de Serviço desbloqueie a mensagem na assinatura e disponibilize-a para ser recebida novamente, pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite associado a uma mensagem bloqueada na assinatura e, se houver falha no processamento da mensagem pelo aplicativo antes da expiração do tempo limite de bloqueio (por exemplo, se o aplicativo travar), o Barramento de Serviço desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Se houver falha do aplicativo após o processamento da mensagem, mas antes de a solicitação [Complete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) ser emitida, a mensagem será entregue novamente ao aplicativo quando ele reiniciar. Isso é frequentemente chamado de *Processamento de pelo menos uma vez*, ou seja, cada mensagem será processada pelo menos uma vez, mas, em algumas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não tolerar o processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega de mensagem duplicada. Isso geralmente é obtido com a propriedade [MessageId](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) da mensagem, que permanece constante nas tentativas da entrega.

## <a name="delete-topics-and-subscriptions"></a>Excluir tópicos e assinaturas
O exemplo a seguir demonstra como excluir o tópico **TestTopic** do namespace de serviço **HowToSample**.

```csharp
// Delete Topic.
namespaceManager.DeleteTopic("TestTopic");
```

A exclusão de um tópico também exclui todas as assinaturas registradas com o tópico. As assinaturas também podem ser excluídas de forma independente. O código a seguir demonstra como excluir uma assinatura denominada **HighMessages** do tópico **TestTopic**.

```csharp
namespaceManager.DeleteSubscription("TestTopic", "HighMessages");
```

## <a name="next-steps"></a>Próximas etapas
Agora que você já sabe os princípios dos tópicos do Barramento de Serviço, acesse estes links para saber mais.

* [Filas, tópicos e assinaturas][Queues, topics, and subscriptions].
* [Exemplo de filtros do tópico][Topic filters sample]
* Referência da API para [SqlFilter][SqlFilter].
* Compile um aplicativo de trabalho que envie e receba mensagens de e para uma fila do Barramento de Serviço: [tutorial do .NET do Sistema de Mensagens agenciado do Barramento de Serviço][Service Bus brokered messaging .NET tutorial].
* Exemplos do Barramento de Serviço: baixe de [Exemplos do Azure][Azure samples] ou confira a [visão geral](service-bus-samples.md).

[Azure portal]: https://portal.azure.com

[7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png

[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Topic filters sample]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters
[SqlFilter]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sqlfilter
[SqlFilter.SqlExpression]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sqlfilter#Microsoft_ServiceBus_Messaging_SqlFilter_SqlExpression
[Service Bus brokered messaging .NET tutorial]: service-bus-brokered-tutorial-dotnet.md
[Azure samples]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2



<!--HONumber=Dec16_HO4-->


