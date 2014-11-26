<properties linkid="dev-net-how-to-service-bus-topics" urlDisplayName="Service Bus Topics" pageTitle="How to use Service Bus topics (.NET) - Azure" metaKeywords="Get started Azure Service Bus topics, Azure publish subscribe messaging, Azure messaging topics and subscriptions C# " description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for .NET applications. " metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use Service Bus Topics/Subscriptions" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />

# Como usar os tópicos e as assinaturas do Service Bus

<span>Este guia mostrará como usar os tópicos e as assinaturas do Barramento de Serviço. Os exemplos são escritos em código C# e utilizam a API .NET. Os cenários abordados incluem a **criação de tópicos e assinaturas, a criação de filtros de assinatura, o envio de mensagens** para um tópico, o **recebimento de mensagens de uma assinatura** e a **exclusão de tópicos e assinaturas**. Para obter mais informações sobre tópicos e assinaturas, consulte a seção [Próximas etapas][Próximas etapas]. </span>

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## <span class="short-header">Configurar o aplicativo</span>Configurar o aplicativo para usar o Barramento de Serviço

Quando você cria um aplicativo que usa o Barramento de Serviço, você precisa adicionar uma referência ao assembly do Barramento de Serviço e incluir os namespaces correspondentes.

## <span class="short-header">Obter o pacote do NuGet</span>Obter o pacote do NuGet do Barramento de Serviço

O pacote **NuGet** de Barramento de Serviço é a maneira mais fácil de obter a API de Barramento de Serviço e configurar seu aplicativo com todas as dependências de Barramento de Serviço. A extensão do Visual Studio do NuGet facilita a instalação e a atualização de bibliotecas e ferramentas no Visual Studio e no Visual Studio Express 2012 para Web. O pacote NuGet de Barramento de Serviço é a maneira mais fácil de obter a API de Barramento de Serviço e configurar seu aplicativo com todas as dependências de Barramento de Serviço.

Para instalar o pacote do NuGet em seu aplicativo, proceda da seguinte maneira:

1.  No Gerenciador de Soluções, clique com o botão direito do mouse em **Referências** e clique em
    **Gerenciar Pacotes NuGet**.
2.  Pesquise por WindowsAzure" e selecione o item **Barramento de Serviço do Azure**. Clique em **Instalar** para concluir a instalação e, em seguida, feche essa caixa de diálogo.

    ![][0]

Agora você está pronto para escrever código no Barramento de Serviço.

## <span class="short-header">Como configurar a cadeia de conexão</span>Como configurar uma cadeia de conexão do Barramento de Serviço

O Service Bus usa uma cadeia de conexão para armazenar pontos de extremidade e credenciais. Você pode colocar a cadeia de conexão em um arquivo de configuração, em vez de embuti-la no código:

-   Ao usar os Serviços de Nuvem do Azure, é recomendável armazenar a cadeia de conexão usando o sistema de configuração de serviço do Azure (arquivos `*.csdef` e `*.cscfg`).
-   Ao usar Sites do Azure ou Máquinas Virtuais do Azure, é recomendável armazenar a cadeia de conexão usando o sistema de configuração do .NET (por exemplo, arquivos `web.config`).

Nos dois casos, você pode recuperar a cadeia de conexão usando o método `CloudConfigurationManager.GetSetting`, conforme mostrado neste guia.

### <a name="config-connstring"> </a> Configurando a cadeia de conexão ao usar os Serviços de Nuvem

O mecanismo de configuração de serviço é exclusivo para projetos de Serviços de Nuvem do Azure e permite que você altere dinamicamente os parâmetros de configuração no Portal de Gerenciamento do Azure sem reimplantar o aplicativo. Por exemplo, adicione uma Configuração para seu arquivo de definição de serviço (`*.csdef`), conforme mostrado abaixo:

    <ServiceDefinition name="WindowsAzure1">
    ...
        <WebRole name="MyRole" vmsize="Small">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" />
            </ConfigurationSettings>
        </WebRole>
    ...
    </ServiceDefinition>

Em seguida, você especifica valores no arquivo de configuração do serviço (`*.cscfg`):

    <ServiceConfiguration serviceName="WindowsAzure1">
    ...
        <Role name="MyRole">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" 
                         value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
            </ConfigurationSettings>
        </Role>
    ...
    </ServiceConfiguration>

Use os valores de emissor e chave recuperados do Portal de Gerenciamento, conforme descrito na seção anterior.

### Configurando a cadeia de conexão ao usar Sites ou Máquinas Virtuais

Ao usar Sites ou Máquinas Virtuais, é recomendável usar o sistema de configuração do .NET (por exemplo, `web.config`). Você pode armazenar a cadeia de conexão usando o elemento `<appSettings>`:

    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
        </appSettings>
    </configuration>

Use os valores de emissor e chave recuperados do Portal de Gerenciamento, conforme descrito na seção anterior.

## <span class="short-header">Como criar um tópico</span>Como criar um tópico

Você pode executar operações de gerenciamento de tópicos e assinaturas do Barramento de Serviço por meio da classe **NamespaceManager**. A classe **NamespaceManager** fornece métodos para criar, enumerar e excluir filas.

Este exemplo constrói um objeto **NamespaceManager** usando a classe **CloudConfigurationManager** do Azure com uma cadeia de conexão que consiste do endereço base de um namespace de serviço do Barramento de Serviço e as credenciais apropriadas com permissões para gerenciá-lo. Essa cadeia de conexão está no formato

    Endpoint=sb://<yourServiceNamespace>.servicebus.windows.net/;SharedSecretIssuer=<issuerName>;SharedSecretValue=<yourDefaultKey>

Por exemplo, considerando os parâmetros de configuração da seção anterior:

    // Create the topic if it does not exist already
    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager = 
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.TopicExists("TestTopic"))
    {
        namespaceManager.CreateTopic("TestTopic");
    }

Há sobrecargas do método **CreateTopic** que permitem que você ajuste as propriedades da fila (por exemplo, para definir o valor de "time-to-live" padrão a ser aplicado às mensagens enviadas para a fila). Essas configurações são aplicadas usando a classe **TopicDescription**. O exemplo a seguir mostra como criar uma fila chamada "TestTopic" com umtamanho máximo de 5 GB e uma mensagem padrão do tempo de vida de um minuto.

    // Configure Topic Settings
    TopicDescription td = new TopicDescription("TestTopic");
    td.MaxSizeInMegabytes = 5120;
    td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

    // Create a new Topic with custom settings
    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager = 
        NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.TopicExists("TestTopic"))
    {
        namespaceManager.CreateTopic(td);
    }

**Observação:** Você pode usar o método **TopicExists** em objetos **NamespaceManager** para verificar se uma fila com um nome especificado já existe no namespace de serviço.

## <span class="short-header">Como criar assinaturas</span>Como criar assinaturas

Você também pode criar assinaturas de tópico com a classe **NamespaceManager**. As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens passadas para a fila virtual da assinatura.

### Criar uma assinatura com o filtro padrão (MatchAll)

**MatchAll** será o filtro padrão usado se nenhum filtro for especificado quando uma nova assinatura for criada. Quando o filtro **MatchAll** é usado, todas as mensagens publicadas no tópico são colocadas na fila virtual da assinatura. O exemplo a seguir cria uma assinatura denominada "AllMessages" e usa o filtro padrão **MatchAll**
.

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

O tipo de filtro mais flexível compatível com as assinaturas é o **SqlFilter**, que implementa um subconjunto do SQL92. Os filtros SQL operam nas propriedades das mensagens que são publicadas no tópico. Para obter mais detalhes sobre as expressões que podem ser usadas com um filtro SQL, examine a sintaxe [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

O exemplo abaixo cria uma assinatura denominada "HighMessages" com um **SqlFilter** que seleciona apenas as mensagens que tenham uma propriedade **MessageNumber** personalizada maior que 3:

     // Create a "HighMessages" filtered subscription
     SqlFilter highMessagesFilter = 
        new SqlFilter("MessageNumber > 3");

     namespaceManager.CreateSubscription("TestTopic", 
        "HighMessages", 
        highMessagesFilter);

De maneira semelhante, o exemplo a seguir cria uma assinatura denominada "LowMessages" com um **SqlFilter** que seleciona apenas as mensagens que tenham uma propriedade**MessageNumber** menor ou igual a 3:

     // Create a "LowMessages" filtered subscription
     SqlFilter lowMessagesFilter = 
        new SqlFilter("MessageNumber <= 3");

     namespaceManager.CreateSubscription("TestTopic", 
        "LowMessages", 
        lowMessagesFilter);

Agora, quando uma mensagem for enviada para "TestTopic", ela sempre será fornecida aos destinatários inscritos na assinatura do tópico "AllMessages" e será fornecida de forma seletiva para os destinatários inscritos nas assinaturas dos tópicos "HighMessages" e "LowMessages" (dependendo do conteúdo de mensagem).

## <span class="short-header">Enviar mensagens a um tópico</span>Como enviar mensagens a um tópico

Para enviar uma mensagem a um tópico do Barramento de Serviço, seu aplicativo cria um objeto **TopicClient** usando a cadeia de conexão.

O código abaixo demonstra como criar um objeto**TopicClient**para a fila "TestTopic" criada acima usando a chamada de API **CreateFromConnectionString**:

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    TopicClient Client = 
        TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

    Client.Send(new BrokeredMessage());

As mensagens enviadas aos tópicos de Service Bus são instâncias da classe **BrokeredMessage**. Os objetos **BrokeredMessage** têm um conjunto de propriedades padrão (como **Label** e **TimeToLive**), um dicionário usado para manter as propriedades personalizadas específicas do aplicativo e um corpo de dados da cadeia. Um aplicativo pode definir o corpo da mensagem, passando qualquer objeto serializável para o construtor do **BrokeredMessage** e o **DataContractSerializer** adequado será usado para serializar o objeto. Como alternativa, um **System.IO.Stream** pode ser fornecido.

O exemplo a seguir demonstra como enviar cinco mensagens de teste para o "TestTopic" **TopicClient** obtido no snippet de código acima. Observe que o valor de propriedade personalizado de **message\_number** de cada mensagem varia de acordo com a iteração do loop (isso determinará qual assinatura recebe isso):

     for (int i=0; i<5; i++)
     {
       // Create message, passing a string message for the body
       BrokeredMessage message = 
        new BrokeredMessage("Test message " + i);

       // Set additional custom app-specific property
       message.Properties["MessageNumber"] = i;

       // Send message to the topic
       Client.Send(message);
     }

Os tópicos do Barramento de Serviço suportam um tamanho máximo de mensagem de 256 KB (o cabeçalho, que inclui as propriedades do aplicativo padrão e personalizadas, pode ter um tamanho máximo de 64 KB). Não há nenhum limite no número de mensagens mantidas em um tópico mas há uma capacidade do tamanho total das mensagens mantidas por um tópico. O tamanho da fila é definido no momento da criação, com um limite superior de 5 GB.

## <span class="short-header">Receber mensagens de uma assinatura</span>Como receber mensagens de uma assinatura

A maneira mais fácil de receber mensagens de uma assinatura é usar um objeto **SubscriptionClient**. Os objetos **SubscriptionClient** podem trabalhar em dois modos diferentes: **ReceiveAndDelete** e **PeekLock**.

Quando o modo **ReceiveAndDelete** for usado, o recebimento será uma operação única, isto é, quando o Service Bus receber uma solicitação de leitura de uma mensagem em uma assinatura, ele marcará a mensagem como sendo consumida e a retornará ao aplicativo. O modo **ReceiveAndDelete** é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo pode tolerar o não processamento de uma mensagem em caso de falha. Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la. Como o Barramento de Serviço terá marcado a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

No modo **PeekLock** (que é o modo padrão), o processo de recebimento se torna uma operação de duas etapas, o que torna possível o suporte a aplicativos que não toleram mensagens ausentes. Quando o Barramento de Serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, ela a bloqueia para evitar que outros consumidores a recebam e a retorna para o aplicativo. Depois que o aplicativo conclui o processamento da mensagem (ou a armazena de forma segura para processamento futuro), ele conclui a segunda etapa do processo de recebimento pela chamada <strong>Concluída</strong> na mensagem recebida. Quando o Barramento de Serviço vê a chamada <strong>Concluída</strong>, ele marca a mensagem como sendo consumida e remove-a da assinatura.

O exemplo a seguir demonstra como as mensagens podem ser recebidas e processadas usando o modo **PeekLock**. Para especificar outro valor de **ReceiveMode**, você pode usar outra sobrecarga para **CreateFromConnectionString**. Este exemplo cria um loop infinito e processa mensagens assim que chegam na assinatura "HighMessages". Observe que o caminho para a assinatura "HighMessages" é fornecido no formato "\<*topic path*\>/subscriptions/\<*nome\_da\_assinatura*\>".

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    SubscriptionClient Client = 
        SubscriptionClient.CreateFromConnectionString
                (connectionString, "TestTopic", "HighMessages");

    Client.Receive();
     
    // Continuously process messages received from the HighMessages subscription 
    while (true) 
    {  
       BrokeredMessage message = Client.Receive();

       if (message != null)
       {
          try 
          {
             Console.WriteLine("Body: " + message.GetBody<string>());
             Console.WriteLine("MessageID: " + message.MessageId);
             Console.WriteLine("MessageNumber: " + 
                message.Properties["MessageNumber"]);

             // Remove message from subscription
             message.Complete();
          }
          catch (Exception)
          {
             // Indicate a problem, unlock message in subscription
             message.Abandon();
          }
       }
    } 

## <span class="short-header">Falhas no aplicativo e mensagens ilegíveis</span>Como lidar com falhas no aplicativo e mensagens ilegíveis

O Barramento de Serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não for capaz de processar a mensagem por algum motivo, ele chamará o método **Abandon** na mensagem recebida (em vez do método **Complete**). Isso fará com que o Service Bus desbloqueie a mensagem na assinatura e disponibilize-a para ser recebida novamente, pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite associado a uma mensagem bloqueada na assinatura e, se houver falha no processamento da mensagem pelo aplicativo antes da expiração do tempo limite de bloqueio (por exemplo, se o aplicativo travar), o Service Bus desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Se houver falha do aplicativo após o processamento da mensagem mas antes da solicitação **Complete** ser emitida, a mensagem será entregue novamente ao aplicativo quando ele reiniciar. Isso é frequentemente chamado de **Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada pelo menos uma vez mas, em algumas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não tolerar o processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega de mensagem duplicada. Isso geralmente é obtido com a propriedade **MessageId** da mensagem, que permanecerá constante nas tentativas da entrega.

## <span class="short-header">Excluir tópicos e assinaturas</span>Como excluir tópicos e assinaturas

O exemplo a seguir demonstra como excluir o tópico denominado **TestTopic** do namespace de serviço **HowToSample**:

     // Delete Topic
     namespaceManager.DeleteTopic("TestTopic");

A exclusão de um tópico também excluirá todas as assinaturas registradas com o tópico. As assinaturas também podem ser excluídas de forma independente. O código a seguir demonstra como excluir uma assinatura denominada <strong>HighMessages</strong> a partir do tópico <strong>TestTopic</strong>:

      namespaceManager.DeleteSubscription("TestTopic", "HighMessages");

## <span class="short-header">Próximas etapas</span>Próximas etapas

Agora que você já sabe os princípios dos tópicos do Barramento de Serviço, acesse estes links para saber mais.

-   Consulte a referência de MSDN: [Filas, tópicos e assinaturas][Filas, tópicos e assinaturas].
-   Referência de API para [SqlFilter][SqlFilter].
-   Compilar um aplicativo de trabalho que envia e recebe mensagens de e para uma fila do Barramento de Serviço: [Tutorial do Barramento de Serviço orientado a mensagens do.NET][Tutorial do Barramento de Serviço orientado a mensagens do.NET].

  [Próximas etapas]: #nextsteps
  [0]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png
  [Filas, tópicos e assinaturas]: http://msdn.microsoft.com/pt-br/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/pt-br/library/microsoft.servicebus.messaging.sqlfilter.aspx
  [Tutorial do Barramento de Serviço orientado a mensagens do.NET]: http://msdn.microsoft.com/pt-br/library/hh367512.aspx
