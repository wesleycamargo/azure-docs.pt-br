---
title: Usar os tópicos de Barramento de Serviço com o .NET | Microsoft Docs
description: Saiba como usar as assinaturas e os tópicos do Barramento de Serviço com o .NET no Azure. Os códigos de exemplo são escritos para aplicativos .NET.
services: service-bus
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''

ms.service: service-bus
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 09/16/2016
ms.author: sethm

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
O mecanismo de configuração do serviço permite que você altere dinamicamente as definições da configuração no [portal do Azure][portal do Azure] sem reimplantar o aplicativo. Por exemplo, adicione um rótulo `Setting` ao seu arquivo de definição de serviço (**.csdef**), como mostrado no próximo exemplo.

```
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

```
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

```
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

Use o nome da SAS e os valores de chave recuperados no [portal do Azure][portal do Azure], conforme descrito anteriormente.

## <a name="create-a-topic"></a>Criar um tópico
Você pode executar operações de gerenciamento de tópicos e assinaturas do Barramento de Serviço usando a classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). Essa classe fornece métodos para criar, enumerar e excluir tópicos.

O exemplo a seguir constrói um objeto `NamespaceManager` usando a classe `CloudConfigurationManager` do Azure com uma cadeia de conexão que consiste no endereço básico de um namespace do Barramento de Serviço e nas credenciais apropriadas da SAS com permissões para gerenciá-lo. Essa cadeia de conexão tem o seguinte formato:

```
Endpoint=sb://<yourNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<yourKey>
```

Use o exemplo a seguir, considerando as definições de configuração da seção anterior.

```
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

Há sobrecargas do método [CreateTopic](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createtopic.aspx) que permitem definir as propriedades do tópico, por exemplo, definir o valor de TTL (vida útil) padrão a ser aplicado às mensagens enviadas ao tópico. Essas configurações são aplicadas usando a classe [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx). O exemplo a seguir mostra como criar um tópico chamado **TestTopic** com um tamanho máximo de 5 GB e uma mensagem padrão de TTL de 1 minuto.

```
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
> Você pode usar o método [TopicExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.topicexists.aspx) em objetos [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) para verificar se uma fila com um nome especificado já existe no namespace.
> 
> 

## <a name="create-a-subscription"></a>Criar uma assinatura
Você também pode criar assinaturas de tópico usando a classe [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx). As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens passadas para a fila virtual da assinatura.

> [!IMPORTANT]
> Para que as mensagens sejam recebidas por uma assinatura, você deve criar essa assinatura antes de enviar as mensagens ao tópico. Se não houver nenhuma assinatura em um tópico, o tópico descartará as mensagens.
> 
> 

### <a name="create-a-subscription-with-the-default-(matchall)-filter"></a>Criar uma assinatura com o filtro padrão (MatchAll)
Se nenhum filtro for especificado quando uma nova assinatura for criada, o filtro **MatchAll** será o padrão usado. Quando você usa o filtro **MatchAll**, todas as mensagens publicadas no tópico são colocadas na fila virtual da assinatura. O exemplo a seguir cria uma assinatura denominada “AllMessages” e usa o filtro padrão **MatchAll**.

```
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

O tipo de filtro mais flexível com suporte das assinaturas é a classe [SqlFilter][SqlFilter], que implementa um subconjunto do SQL92. Os filtros SQL operam nas propriedades das mensagens que são publicadas no tópico. Para obter mais informações sobre as expressões que podem ser usadas com um filtro SQL, consulte a sintaxe [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

O exemplo a seguir cria uma assinatura denominada **HighMessages** com um objeto [SqlFilter][SqlFilter] que seleciona apenas as mensagens que tenham uma propriedade **MessageNumber** personalizada maior do que 3.

```
// Create a "HighMessages" filtered subscription.
SqlFilter highMessagesFilter =
   new SqlFilter("MessageNumber > 3");

namespaceManager.CreateSubscription("TestTopic",
   "HighMessages",
   highMessagesFilter);
```

De maneira semelhante, o exemplo a seguir cria uma assinatura denominada **LowMessages** com um [SqlFilter][SqlFilter] que seleciona apenas as mensagens que tenham uma propriedade **MessageNumber** menor ou igual a 3.

```
// Create a "LowMessages" filtered subscription.
SqlFilter lowMessagesFilter =
   new SqlFilter("MessageNumber <= 3");

namespaceManager.CreateSubscription("TestTopic",
   "LowMessages",
   lowMessagesFilter);
```

Agora, quando uma mensagem for enviada para `TestTopic`, ela sempre será fornecida aos destinatários inscritos na assinatura do tópico **AllMessages** e será fornecida de forma seletiva para os destinatários inscritos nas assinaturas dos tópicos **HighMessages** e **LowMessages** (dependendo do conteúdo da mensagem).

## <a name="send-messages-to-a-topic"></a>Enviar mensagens para um tópico
Para enviar uma mensagem a um tópico do Barramento de Serviço, seu aplicativo cria um objeto [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) usando a cadeia de conexão.

O código a seguir demonstra como criar um objeto [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) para o tópico **TestTopic** criado anteriormente usando a chamada à API [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.createfromconnectionstring.aspx).

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

TopicClient Client =
    TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

Client.Send(new BrokeredMessage());
```

As mensagens enviadas aos tópicos de Barramento de Serviço são instâncias da classe [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Os objetos [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) têm um conjunto de propriedades padrão (como [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) e [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), um dicionário usado para manter as propriedades personalizadas específicas do aplicativo e um corpo de dados de aplicativo arbitrários. Um aplicativo pode definir o corpo da mensagem, passando qualquer objeto serializável para o construtor do objeto [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) e o **DataContractSerializer** adequado será usado para serializar o objeto. Como alternativa, é possível fornecer um **System.IO.Stream**.

O exemplo a seguir demonstra como enviar cinco mensagens de teste ao objeto **TestTopic** [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) obtido no exemplo de código anterior. Observe que o valor da propriedade [MessageNumber](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.properties.aspx) de cada mensagem varia dependendo da iteração do loop (isso determina qual assinatura o recebe).

```
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set additional custom app-specific property.
  message.Properties["MessageNumber"] = i;

  // Send message to the topic.
  Client.Send(message);
}
```

Os tópicos do Barramento de Serviço dão suporte ao tamanho máximo de mensagem de 256 KB na [camada Standard](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicativo padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não há nenhum limite no número de mensagens mantidas em um tópico, mas há uma capacidade do tamanho total das mensagens mantidas por um tópico. O tamanho do tópico é definido no momento da criação, com um limite máximo de 5 GB. Se o particionamento estiver habilitado, o limite superior será maior. Para obter mais informações, consulte [Entidades de mensagens particionadas](service-bus-partitioning.md).

## <a name="how-to-receive-messages-from-a-subscription"></a>Como receber mensagens de uma assinatura
A maneira recomendada de receber mensagens de uma assinatura é usar um objeto [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx). Os objetos [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) podem funcionar em dois modos diferentes: [*ReceiveAndDelete* e *PeekLock*](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

Quando o modo **ReceiveAndDelete** for usado, o recebimento será uma operação única, ou seja, quando o Barramento de Serviço receber uma solicitação de leitura de uma mensagem em uma assinatura, ele marcará a mensagem como sendo consumida e a retornará ao aplicativo. O modo **ReceiveAndDelete** é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo pode tolerar o não processamento de uma mensagem em caso de falha. Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la. Como o Barramento de Serviço marca a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

No modo **PeekLock** (que é o modo padrão), o processo de recebimento se torna uma operação de duas etapas, o que torna possível o suporte a aplicativos que não toleram mensagens ausentes. Quando o Barramento de Serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, a bloqueia para evitar que outros clientes a recebam e a retorna para o aplicativo. Depois que o aplicativo conclui o processamento da mensagem (ou a armazena de forma segura para processamento futuro), ele conclui a segunda etapa do processo de recebimento chamando [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) na mensagem recebida. Quando o Barramento de Serviço vê a chamada **Complete**, ele marca a mensagem como sendo consumida e remove-a da assinatura.

O exemplo a seguir demonstra como as mensagens podem ser recebidas e processadas usando o modo **PeekLock** padrão. Para especificar outro valor de [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx), você pode usar outra sobrecarga para [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.createfromconnectionstring.aspx). Este exemplo usa o retorno de chamada [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) para processar mensagens assim que chegam à assinatura **HighMessages**.

```
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

Este exemplo configura o retorno de chamada [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) usando um objeto [OnMessageOptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx). [AutoComplete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) é definido como **false** para habilitar o controle manual de quando chamar [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) na mensagem recebida. [AutoRenewTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) é definido como 1 minuto, o que faz com que o cliente aguarde até um minuto antes do encerramento do recurso de renovação automática e que o cliente faça uma nova chamada para verificar se há mensagens. Esse valor de propriedade reduz o número de vezes que o cliente faz chamadas passíveis de cobrança que não recuperam mensagens.

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como tratar falhas do aplicativo e mensagens ilegíveis
O Barramento de Serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não for capaz de processar a mensagem por algum motivo, ele chamará o método [Abando](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx)n na mensagem recebida (em vez do método [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). Isso fará com que o Barramento de Serviço desbloqueie a mensagem na assinatura e disponibilize-a para ser recebida novamente, pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite associado a uma mensagem bloqueada na assinatura e, se houver falha no processamento da mensagem pelo aplicativo antes da expiração do tempo limite de bloqueio (por exemplo, se o aplicativo travar), o Barramento de Serviço desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Se houver falha do aplicativo após o processamento da mensagem, mas antes de a solicitação [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) ser emitida, a mensagem será entregue novamente ao aplicativo quando ele reiniciar. Isso é frequentemente chamado de *Processamento de pelo menos uma vez*, ou seja, cada mensagem será processada pelo menos uma vez, mas, em algumas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não tolerar o processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega de mensagem duplicada. Isso geralmente é obtido com a propriedade [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) da mensagem, que permanece constante nas tentativas da entrega.

## <a name="delete-topics-and-subscriptions"></a>Excluir tópicos e assinaturas
O exemplo a seguir demonstra como excluir o tópico **TestTopic** do namespace de serviço **HowToSample**.

```
// Delete Topic.
namespaceManager.DeleteTopic("TestTopic");
```

A exclusão de um tópico também exclui todas as assinaturas registradas com o tópico. As assinaturas também podem ser excluídas de forma independente. O código a seguir demonstra como excluir uma assinatura denominada **HighMessages** do tópico **TestTopic**.

```
namespaceManager.DeleteSubscription("TestTopic", "HighMessages");
```

## <a name="next-steps"></a>Próximas etapas
Agora que você já sabe os princípios dos tópicos do Barramento de Serviço, acesse estes links para saber mais.

* [Filas, tópicos e assinaturas][Filas, tópicos e assinaturas].
* [Exemplo de filtros do tópico][Exemplo de filtros do tópico]
* Referência da API para [SqlFilter][SqlFilter].
* Compile um aplicativo de trabalho que envie e receba mensagens de e para uma fila do Barramento de Serviço: [tutorial do .NET do sistema de mensagens agenciado do Barramento de Serviço][].
* Exemplos do Barramento de Serviço: baixe de [Exemplos do Azure][] ou consulte a [visão geral](../service-bus/service-bus-samples.md).

[Portal do Azure]: https://portal.azure.com

[7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png

[Filas, tópicos e assinaturas]: service-bus-queues-topics-subscriptions.md
[Exemplo de filtros do tópico]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples/tree/master/TopicFilters
[SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
[SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
[Tutorial do .NET do sistema de mensagens agenciado do Barramento do Serviço]: service-bus-brokered-tutorial-dotnet.md
[Amostras do Azure]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2



<!--HONumber=Oct16_HO2-->


