<properties
    pageTitle="Como usar os tópicos de Barramento de Serviço (.NET) | Microsoft Azure"
	description="Aprenda a usar assinaturas e tópicos do barramento de serviço no Azure. Os códigos de exemplo são escritos para aplicativos .NET."
	services="service-bus"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
    ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="07/02/2015"
	ms.author="sethm"/>

# Como usar tópicos e assinaturas do Barramento de Serviço do Azure

Este artigo descreve como usar tópicos e assinaturas do Barramento de Serviço. Os exemplos são escritos em C# e utilizam APIs .NET. Os cenários abordados incluem a criação de tópicos e assinaturas, a criação de filtros de assinatura, o envio de mensagens para um tópico, o recebimento de mensagens de uma assinatura e a exclusão de tópicos e assinaturas. Para obter mais informações sobre tópicos e assinaturas, consulte a seção [Próximas etapas](#Next-steps).

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## Configurar o aplicativo para usar o Barramento de Serviço

Quando você cria um aplicativo que usa o Barramento de Serviço, você deve adicionar uma referência ao assembly do Barramento de Serviço e incluir os espaços para nome correspondentes.

## Obtenha o pacote do NuGet do Barramento de Serviço

O pacote NuGet de Barramento de serviço é a maneira mais fácil de obter a API do Barramento de serviço e configurar seu aplicativo com todas as dependências de Barramento de serviço. A extensão do Visual Studio do NuGet facilita a instalação e a atualização de bibliotecas e ferramentas no Visual Studio e no Visual Studio Express. O pacote NuGet de Barramento de serviço é a maneira mais fácil de obter a API do Barramento de serviço e configurar seu aplicativo com todas as dependências de Barramento de serviço.

Para instalar o pacote do NuGet em seu aplicativo, proceda da seguinte maneira:

1.  No Gerenciador de Soluções, clique com o botão direito do mouse em **Referências** e clique em **Gerenciar Pacotes NuGet**.
2.  Procure "Barramento de Serviço" e selecione o item **Barramento de Serviço do Microsoft Azure**. Clique em **Instalar** para concluir a instalação e, em seguida, feche essa caixa de diálogo a seguir.

    ![][7]

Agora você está pronto para escrever código para o Barramento de Serviço.

## Como configurar uma cadeia de conexão do Barramento de Serviço

O Barramento de Serviço usa uma cadeia de conexão para armazenar pontos de extremidade e credenciais. Você pode colocar a cadeia de conexão em um arquivo de configuração, em vez de embuti-la no código:

- Ao usar os Serviços de Nuvem do Azure, é recomendável armazenar a cadeia de conexão usando o sistema de configuração de serviço do Azure (arquivos .csdef e .cscfg).
- Ao usar Sites do Azure ou Máquinas Virtuais do Azure, é recomendável armazenar a cadeia de conexão usando o sistema de configuração .NET (por exemplo, o arquivo Web.config).

Nos dois casos, você pode recuperar a cadeia de conexão usando o método `CloudConfigurationManager.GetSetting`, conforme mostrado mais adiante neste artigo.

### Configurando a cadeia de conexão ao usar Serviços de Nuvem

O mecanismo de configuração de serviço é exclusivo para projetos de Serviços de Nuvem do Azure e permite que você altere dinamicamente os parâmetros de configuração no portal do Azure sem reimplantar o aplicativo. Por exemplo, adicione um rótulo `Setting` ao seu arquivo de definição de serviço (****.csdef**), conforme mostrado no próximo exemplo.

    <ServiceDefinition name="Azure1">
    ...
        <WebRole name="MyRole" vmsize="Small">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" />
            </ConfigurationSettings>
        </WebRole>
    ...
    </ServiceDefinition>

Em seguida, especifique valores no arquivo de configuração do serviço (.cscfg).

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

Use o nome da chave da SAS (Assinatura de Acesso Compartilhado) e os valores de chave recuperados do portal do Azure, conforme descrito na seção anterior.

### Configurando a cadeia de conexão ao usar sites ou Máquinas Virtuais do Azure

Ao usar os sites ou Máquinas Virtuais, é recomendável usar o sistema de configuração .NET (por exemplo, Web.config). Você pode armazenar a cadeia de conexão usando o elemento `<appSettings>`.

    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </appSettings>
    </configuration>

Use o nome da SAS e os valores de chave recuperados no portal do Azure, conforme descrito na seção anterior.

## Como criar um tópico

Você pode executar operações de gerenciamento de tópicos e assinaturas do Barramento de Serviço usando a classe [`NamespaceManager`](https://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.aspx). Essa classe fornece métodos para criar, enumerar e excluir tópicos.

O exemplo a seguir constrói um objeto `NamespaceManager` usando a classe `CloudConfigurationManager` do Azure com uma cadeia de conexão que consiste no endereço básico de um namespace de serviço do Barramento de Serviço e nas credenciais apropriadas da SAS com permissões para gerenciá-lo. Essa cadeia de conexão está na forma a seguir.

    Endpoint=sb://<yourServiceNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey

Use o exemplo a seguir, considerando as definições de configuração da seção anterior.

    // Create the topic if it does not exist already.
    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.TopicExists("TestTopic"))
    {
        namespaceManager.CreateTopic("TestTopic");
    }

Há sobrecargas do método [`CreateTopic`](https://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.createtopic.aspx) que permitem ajustar as propriedades do tópico, por exemplo, para definir o valor de "vida útil" padrão a ser aplicado às mensagens enviadas para o tópico. Essas configurações são aplicadas usando a classe [`TopicDescription`](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.topicdescription.aspx). O exemplo a seguir mostra como criar um tópico chamado TestTopic com um tamanho máximo de 5 GB e uma mensagem padrão do tempo de vida de 1 minuto.

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

> [AZURE.NOTE]Você pode usar o método [`TopicExists`](https://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.topicexists.aspx) em objetos [`NamespaceManager`](https://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.aspx) para verificar se já existe um tópico com um nome especificado em um namespace de serviço.

## Como criar uma assinatura

Você também pode criar assinaturas de tópico com a classe [`NamespaceManager`](https://msdn.microsoft.com/library/microsoft.servicebus.namespacemanager.aspx). As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens passadas para a fila virtual da assinatura.

### Criar uma assinatura com o filtro padrão (MatchAll)

**MatchAll** será o filtro padrão usado se nenhum filtro for especificado quando uma nova assinatura for criada. Quando você usa o filtro **MatchAll**, todas as mensagens publicadas no tópico são colocadas na fila virtual da assinatura. O exemplo a seguir cria uma assinatura denominada 'AllMessages' e usa o filtro padrão **MatchAll**.

    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
    {
        namespaceManager.CreateSubscription("TestTopic", "AllMessages");
    }

### Criar assinaturas com os filtros

Você também pode configurar filtros que permitem especificar quais mensagens enviadas a um tópico devem aparecer dentro de uma assinatura específica do tópico.

O tipo de filtro mais flexível com suporte das assinaturas é a classe [SqlFilter], que implementa um subconjunto do SQL92. Os filtros SQL operam nas propriedades das mensagens que são publicadas no tópico. Para obter mais informações sobre as expressões que podem ser usadas com um filtro SQL, consulte a sintaxe [SqlFilter.SqlExpression][].

O exemplo a seguir cria uma assinatura denominada **HighMessages** com um objeto [SqlFilter] que seleciona apenas as mensagens que tenham uma propriedade **MessageNumber** personalizada maior do que 3.

     // Create a "HighMessages" filtered subscription.
     SqlFilter highMessagesFilter =
        new SqlFilter("MessageNumber > 3");

     namespaceManager.CreateSubscription("TestTopic",
        "HighMessages",
        highMessagesFilter);

De maneira semelhante, o exemplo a seguir cria uma assinatura denominada **LowMessages** com um [SqlFilter] que seleciona apenas as mensagens que tenham uma propriedade **MessageNumber** menor ou igual a 3.

     // Create a "LowMessages" filtered subscription.
     SqlFilter lowMessagesFilter =
        new SqlFilter("MessageNumber <= 3");

     namespaceManager.CreateSubscription("TestTopic",
        "LowMessages",
        lowMessagesFilter);

Agora, quando uma mensagem for enviada para `TestTopic`, ela sempre será entregue aos destinatários inscritos na assinatura do tópico **AllMessages** e será entregue de forma seletiva aos destinatários inscritos nas assinaturas dos tópicos **HighMessages** e **LowMessages** (dependendo do conteúdo da mensagem).

## Como enviar mensagens a um tópico

Para enviar uma mensagem a um tópico do Barramento de Serviço, seu aplicativo cria um objeto [`TopicClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) usando a cadeia de conexão.

O código a seguir demonstra como criar um objeto [`TopicClient`](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.topicclient.aspx) para o tópico **TestTopic** criado anteriormente usando a chamada à API [`CreateFromConnectionString`](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.topicclient.createfromconnectionstring.aspx).

    string connectionString =
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    TopicClient Client =
        TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

    Client.Send(new BrokeredMessage());


As mensagens enviadas aos tópicos do Barramento de Serviço são instâncias da classe [`BrokeredMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Os objetos **BrokeredMessage** têm um conjunto de propriedades padrão (como [`Label`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) e [`TimeToLive`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), um dicionário usado para manter as propriedades personalizadas específicas do aplicativo e um corpo de dados de aplicativo arbitrários. Um aplicativo pode definir o corpo da mensagem, passando qualquer objeto serializável para o construtor do objeto [`BrokeredMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) e o **DataContractSerializer** adequado será usado para serializar o objeto. Como alternativa, é possível fornecer um **System.IO.Stream**.

O exemplo a seguir demonstra como enviar cinco mensagens de teste ao objeto **TestTopic** [`TopicClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) obtido no exemplo de código anterior. Observe que o valor da propriedade [`MessageNumber`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.properties.aspx) de cada mensagem varia de acordo com a iteração do loop (isso determinará qual assinatura o receberá).

     for (int i=0; i<5; i++)
     {
       // Create message, passing a string message for the body.
       BrokeredMessage message =
        new BrokeredMessage("Test message " + i);

       // Set additional custom app-specific property.
       message.Properties["MessageNumber"] = i;

       // Send message to the topic.
       Client.Send(message);
     }

Os tópicos do Barramento de Serviço oferecem suporte a um [tamanho máximo de mensagem de 256 KB](service-bus-quotas.md) (o cabeçalho, que inclui as propriedades do aplicativo padrão e personalizadas, pode ter um tamanho máximo de 64 KB). Não há nenhum limite no número de mensagens mantidas em um tópico mas há uma capacidade do tamanho total das mensagens mantidas por um tópico. O tamanho do tópico é definido no momento da criação, com um limite máximo de 5 GB. Se o particionamento estiver habilitado, o limite superior será maior. Para obter mais informações, consulte [Particionando entidades de mensagens](https://msdn.microsoft.com/library/azure/dn520246.aspx).

## Como receber mensagens de uma assinatura

A maneira recomendada para receber mensagens de uma assinatura é usar um objeto [`SubscriptionClient`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx). Os objetos **SubscriptionClient** podem funcionar de dois modos diferentes: [`ReceiveAndDelete` e `PeekLock`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

Quando o modo **ReceiveAndDelete** for usado, o recebimento será uma operação única, isto é, quando o Service Bus receber uma solicitação de leitura de uma mensagem em uma assinatura, ele marcará a mensagem como sendo consumida e a retornará ao aplicativo. O modo **ReceiveAndDelete** é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo pode tolerar o não processamento de uma mensagem em caso de falha. Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la. Como o Barramento de Serviço terá marcado a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

No modo **PeekLock** (que é o modo padrão), o processo de recebimento se torna uma operação de duas etapas, o que torna possível o suporte a aplicativos que não toleram mensagens ausentes. Quando o Barramento de Serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, a bloqueia para evitar que outros clientes a recebam e a retorna para o aplicativo. Depois que o aplicativo conclui o processamento da mensagem (ou a armazena de forma segura para processamento futuro), ele conclui a segunda etapa do processo de recebimento chamando [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) na mensagem recebida. Quando o Barramento de Serviço vê a chamada `Complete`, ele marca a mensagem como sendo consumida e a remove da assinatura.

O exemplo a seguir demonstra como as mensagens podem ser recebidas e processadas usando o modo **PeekLock**. Para especificar um valor [`ReceiveMode`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) diferente, você pode usar outra sobrecarga para [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.createfromconnectionstring.aspx). Este exemplo usa o retorno de chamada [`OnMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) para processar mensagens assim que chegam à assinatura **HighMessages**.

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

Este exemplo configura o retorno de chamada [`OnMessage`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) usando um objeto [`OnMessageOptions`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx). [`AutoComplete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) é definido como **false** para habilitar o controle manual do momento de chamar [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) na mensagem recebida. [`AutoRenewTimeout`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) é definido para 1 minuto, o que faz com que o cliente aguarde até um minuto para uma mensagem antes que a chamada expire e o cliente faça uma nova chamada para verificar se há mensagens. Esse valor de propriedade reduz o número de vezes que o cliente faz chamadas passíveis de cobrança que não recuperam mensagens.

## Como tratar falhas do aplicativo e mensagens ilegíveis

O Barramento de Serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele chamará o método [`Abandon`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) na mensagem recebida (em vez do método [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx)). Isso fará com que o Barramento de Serviço desbloqueie a mensagem na assinatura e disponibilize-a para ser recebida novamente, pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite associado a uma mensagem bloqueada na assinatura e, se houver falha no processamento da mensagem pelo aplicativo antes da expiração do tempo limite de bloqueio (por exemplo, se o aplicativo travar), o Barramento de Serviço desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Se houver falha do aplicativo após o processamento da mensagem, mas antes que a solicitação [`Complete`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) seja gerada, a mensagem será entregue novamente ao aplicativo quando ele reiniciar. Isso geralmente é chamado de **Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada pelo menos uma vez, mas, em algumas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não tolerar o processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega de mensagem duplicada. Isso geralmente é realizado com a propriedade [`MessageId`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) da mensagem, que permanecerá constante nas tentativas da entrega.

## Como excluir tópicos e assinaturas

O exemplo a seguir demonstra como excluir o tópico **TestTopic** do namespace de serviço **HowToSample**.

     // Delete Topic.
     namespaceManager.DeleteTopic("TestTopic");

A exclusão de um tópico também exclui todas as assinaturas registradas com o tópico. As assinaturas também podem ser excluídas de forma independente. O código a seguir demonstra como excluir uma assinatura denominada **HighMessages** do tópico **TestTopic**.

      namespaceManager.DeleteSubscription("TestTopic", "HighMessages");

## Próximas etapas

Agora que você já sabe os princípios dos tópicos do Barramento de Serviço, acesse estes links para saber mais.

-   Consulte a Referência do MSDN: [Filas, Tópicos e Assinaturas][].
-   Referência da API para [SqlFilter][].
-   Compile um aplicativo de trabalho que envia e recebe mensagens de e para uma fila do Barramento de Serviço: [Tutorial do .NET do sistema de mensagens agenciado do Barramento do Serviço][].
-   Exemplos de Barramento de Serviço: baixe em [Exemplos do Azure][] ou consulte a visão geral no [MSDN][].

  [Azure portal]: http://manage.windowsazure.com

  [7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png

  [Filas, Tópicos e Assinaturas]: http://msdn.microsoft.com/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Tutorial do .NET do sistema de mensagens agenciado do Barramento do Serviço]: http://msdn.microsoft.com/library/azure/hh367512.aspx
  [Exemplos do Azure]: https://code.msdn.microsoft.com/windowsazure/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [MSDN]: https://msdn.microsoft.com/library/azure/dn194201.aspx

<!---HONumber=August15_HO9-->