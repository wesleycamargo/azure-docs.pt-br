<properties 
    pageTitle="Como usar os tópicos do Barramento de Serviço (.NET) - Azure" 
    description="Aprenda a usar assinaturas e tópicos do barramento de serviço no Azure. Os códigos de exemplo são escritos para aplicativos .NET." 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor="mattshel"/>

<tags 
    ms.service="service-bus" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="02/26/2015" 
    ms.author="sethm"/>





# Como usar os tópicos/assinaturas do Barramento de Serviço

Este guia mostra como usar os tópicos e as assinaturas do Barramento de Serviço. Os exemplos são escritos em código C# e utilizam a API .NET. Os cenários abordados incluem a **criação de tópicos e assinaturas, a criação de filtros de assinatura, o envio de mensagens** para um tópico, o **recebimento de mensagens de uma assinatura** e a **exclusão de tópicos e assinaturas**. Para obter mais informações sobre tópicos e assinaturas, consulte a seção [Próximas etapas][].

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## Configurar o aplicativo para usar o Barramento de Serviço

Quando você cria um aplicativo que usa o Barramento de Serviço, você precisa adicionar uma referência ao assembly do Barramento de Serviço e incluir os namespaces correspondentes.

## Obtenha o pacote do NuGet do Barramento de Serviço

O pacote **NuGet** do Barramento de Serviço é a maneira mais fácil de obter a
API do Barramento de Serviço e configurar o aplicativo com todas as
dependências do Barramento de Serviço. A extensão do Visual Studio do NuGet facilita a instalação e a atualização de bibliotecas e ferramentas no Visual Studio e
Visual Studio Express, que é gratuito. O pacote NuGet de Barramento de serviço é a maneira mais fácil de obter a API do Barramento de serviço e configurar seu aplicativo com todas as dependências de Barramento de serviço.

Para instalar o pacote do NuGet em seu aplicativo, proceda da seguinte maneira:

1.  No Gerenciador de Soluções, clique com o botão direito em **Referências** e, em seguida, clique em
    **Gerenciar Pacotes NuGet**.
2.  Pesquise por "Barramento de Serviço" e selecione o item **Barramento de Serviço do
    Microsoft Azure**. Clique em **Instalar** para concluir a instalação,
    então feche essa caixa de diálogo.

    ![][7]

Agora você está pronto para escrever código para o Barramento de Serviço.

## Como configurar uma cadeia de conexão do Barramento de Serviço

O Barramento de Serviço usa uma cadeia de conexão para armazenar pontos de extremidade e credenciais. Você pode colocar a cadeia de conexão em um arquivo de configuração, em vez de embuti-la no código:

- Ao usar os Serviços de Nuvem do Azure, é recomendável armazenar a cadeia de conexão usando o sistema de configuração de serviço do Azure (arquivos `*.csdef` e `*.cscfg`).
- Ao usar Sites do Azure ou Máquinas Virtuais do Azure, é recomendável armazenar a cadeia de conexão usando o sistema de configuração do .NET (por exemplo, arquivo  `web.config`).

Nos dois casos, você pode recuperar a cadeia de conexão usando o método  `CloudConfigurationManager.GetSetting`, conforme mostrado neste guia.

### Configurando a cadeia de conexão ao usar Serviços de Nuvem

O mecanismo de configuração de serviço é exclusivo para projetos de Serviços de Nuvem do Azure e permite que você altere dinamicamente os parâmetros de configuração no Portal de Gerenciamento do Azure sem reimplantar o aplicativo.  Por exemplo, adicione uma Configuração para seu arquivo de definição de serviço (`*.csdef`), conforme mostrado abaixo:

    <ServiceDefinition name="Azure1">
    ...
        <WebRole name="MyRole" vmsize="Small">
            <ConfigurationSettings>
                <Setting name="Microsoft.ServiceBus.ConnectionString" />
            </ConfigurationSettings>
        </WebRole>
    ...
    </ServiceDefinition>

Em seguida, você especifica valores no arquivo de configuração do serviço (`*.cscfg`):

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

Use os valores de chave recuperados do portal de gerenciamento, conforme descrito na seção anterior.

### Configurando a cadeia de conexão ao usar Sites ou Máquinas Virtuais

Ao usar Sites ou Máquinas Virtuais, é recomendável usar o sistema de configuração do .NET (por exemplo,  `web.config`).  Você pode armazenar a cadeia de conexão usando o elemento `<appSettings>`:

    <configuration>
        <appSettings>
            <add key="Microsoft.ServiceBus.ConnectionString"
                 value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </appSettings>
    </configuration>

Use os valores de emissor e chave recuperados do portal de gerenciamento, conforme descrito na seção anterior.

## Como criar um tópico

Você pode executar operações de gerenciamento de tópicos e assinaturas do Barramento de Serviço por meio da classe **NamespaceManager**. A classe**NamespaceManager** fornece métodos para criar, enumerar e excluir filas. 

Este exemplo constrói um objeto **NamespaceManager** usando a classe **CloudConfigurationManager** do Azure com uma cadeia de conexão que consiste no endereço básico de um namespace de serviço do Barramento de Serviço e as credenciais apropriadas com permissões para gerenciá-lo. Essa cadeia de conexão está no formato

    Endpoint=sb://<yourServiceNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey

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

Há sobrecargas do método **CreateTopic** que permitem ajustar as propriedades do tópico, por exemplo, definir o valor de "time-to-live" padrão a ser aplicado às mensagens enviadas ao tópico. Essas configurações são aplicadas usando a classe **TopicDescription**. O exemplo a seguir mostra como criar um tópico chamado "TestTopic" com um tamanho máximo de 5 GB e uma mensagem padrão do tempo de vida de um minuto.

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

**Observação:** Você pode usar o método **TopicExists** em objetos **NamespaceManager**para verificar se uma fila com um nome especificado já existe no namespace de serviço.

<h2>Como criar assinaturas</h2>

Você também pode criar assinaturas de tópico com a classe **NamespaceManager**. As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens passadas para a fila virtual da assinatura.

### Criar uma assinatura com o filtro padrão (MatchAll)

O filtro **MatchAll** é o filtro padrão usado se nenhum filtro for especificado quando uma nova assinatura for criada. Quando o filtro **MatchAll** é usado, todas as mensagens publicadas no tópico são colocadas na fila virtual da assinatura. O exemplo a seguir cria uma assinatura denominada "AllMessages" e usa o filtro padrão **MatchAll**.

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

O tipo mais flexível de filtro suportado por assinaturas é o
**SqlFilter**, que implementa um subconjunto do SQL92. Os filtros SQL operam nas propriedades das mensagens que são publicadas no tópico. Para obter mais detalhes sobre as expressões que podem ser usadas com um filtro SQL, examine a sintaxe [SqlFilter.SqlExpression][].

O exemplo a seguir cria uma assinatura chamada "HighMessages" com um
**SqlFilter** que seleciona somente mensagens com uma propriedade
**MessageNumber** personalizada maior que 3:

     // Create a "HighMessages" filtered subscription
     SqlFilter highMessagesFilter = 
        new SqlFilter("MessageNumber > 3");

     namespaceManager.CreateSubscription("TestTopic", 
        "HighMessages", 
        highMessagesFilter);

Da mesma forma, o exemplo a seguir cria uma assinatura denominada
'LowMessages' com um **SqlFilter** que seleciona apenas as mensagens que tenham uma propriedade **MessageNumber** menor ou igual a 3:

     // Create a "LowMessages" filtered subscription
     SqlFilter lowMessagesFilter = 
        new SqlFilter("MessageNumber <= 3");

     namespaceManager.CreateSubscription("TestTopic", 
        "LowMessages", 
        lowMessagesFilter);

Agora, quando uma mensagem for enviada para "TestTopic", ela sempre será fornecida aos destinatários inscritos na assinatura do tópico "AllMessages" e será fornecida de forma seletiva para os destinatários inscritos nas
assinaturas do tópico "HighMessages" e "LowMessages" (dependendo do conteúdo da mensagem).

## Como enviar mensagens a um tópico

Para enviar uma mensagem a um tópico do Service Bus, seu aplicativo cria um
objeto **TopicClient** usando a cadeia de conexão.

O código abaixo demonstra como criar um objeto **TopicClient** para o tópico "TestTopic" criado acima usando a chamada à API **CreateFromConnectionString**:

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    TopicClient Client = 
        TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

    Client.Send(new BrokeredMessage());
 

As mensagens enviadas aos tópicos do Service Bus são instâncias da
classe **BrokeredMessage**. Os objetos **BrokeredMessage** têm um conjunto de propriedades padrão (como **Label** e **TimeToLive**), um dicionário usado para manter as propriedades personalizadas específicas do aplicativo e um corpo de dados de aplicativo arbitrários. Um aplicativo pode definir o corpo da mensagem passando qualquer objeto serializável para o construtor de
**BrokeredMessage**e o **DataContractSerializer** adequado será usado para serializar o objeto. Como alternativa, um
**System.IO.Stream** pode ser fornecido.

O exemplo a seguir demonstra como enviar cinco mensagens de teste para
"TestTopic" **TopicClient** obtido no trecho de código acimas.
Observe como o valor da propriedade **MessageNumber** de cada mensagem varia de acordo com a iteração do loop (isso determinará qual assinatura o receberá):

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

Os tópicos do Barramento de Serviço dão suporte a um tamanho máximo de mensagem de 256 KB (o cabeçalho, que inclui as propriedades do aplicativo padrão e personalizadas, pode ter um tamanho máximo de 64 Kb). Não há nenhum limite no número de mensagens mantidas em um tópico mas há uma capacidade do tamanho total das mensagens mantidas por um tópico. O tamanho da fila é definido no momento da criação, com um limite superior de 5 GB.

## Como receber mensagens de uma assinatura

A maneira recomendada para receber mensagens de uma assinatura é usar um
objeto **SubscriptionClient**. Os objetos **SubscriptionClient** podem funcionar de dois modos diferentes: **ReceiveAndDelete** e **PeekLock**.

Quando o modo **ReceiveAndDelete** for usado, o recebimento será uma operação única, isto é, quando o Barramento de Serviço receber uma solicitação de leitura de uma mensagem em uma assinatura, ele marcará a mensagem como sendo consumida e a retornará ao aplicativo. **O modo ReceiveAndDelete** é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo pode tolerar o não processamento de uma mensagem em caso de falha. Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la. Como o Barramento de Serviço terá marcado a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

No modo **PeekLock** (que é o modo padrão), o processo de recebimento se torna uma operação de duas etapas, o que torna possível o suporte a aplicativos que não toleram mensagens ausentes. Quando o Barramento de Serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, a bloqueia para evitar que outros clientes a recebam e a retorna para o aplicativo. Depois que o aplicativo conclui o processamento da mensagem (ou a armazena de forma segura para processamento futuro), ele conclui a segunda etapa do processo de recebimento chamando **Complete** na mensagem recebida. Quando o Barramento
de Serviço vê a chamada **Concluída**, ele marca a mensagem como sendo consumida e remove-a da assinatura.

O exemplo abaixo demonstra como as mensagens podem ser recebidas e processadas 
usando o modo padrão **PeekLock**. Para especificar um outro valor **ReceiveMode**, 
você pode usar outra sobrecarga para **CreateFromConnectionString**. Este 
exemplo usa o retorno de chamada **OnMessage** para processar mensagens assim que chegarem 
na assinatura "HighMessages".

    string connectionString = 
        CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    SubscriptionClient Client = 
        SubscriptionClient.CreateFromConnectionString
                (connectionString, "TestTopic", "HighMessages");

    // Configure the callback options
    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    subscriptionClientHigh.OnMessage((message) =>
    {
        try
        {
            // Process message from subscription
            Console.WriteLine("\n**High Messages**");
            Console.WriteLine("Body: " + message.GetBody<string>());
            Console.WriteLine("MessageID: " + message.MessageId);
            Console.WriteLine("Message Number: " +
                message.Properties["MessageNumber"]);

            // Remove message from subscription
            message.Complete();
        }
        catch (Exception)
        {
            // Indicates a problem, unlock message in subscription
            message.Abandon();
        }
    }, options);

Este exemplo configura o retorno de mensagem **OnMessage** usando um 
objeto **OnMessageOptions**. O **Preenchimento automático** é definido como **false** para habilitar  o controle manual de quando chamar **Concluída** na mensagem recebida.
O **AutoRenewTimeout** é definido como um minuto que faz com que o cliente aguarde durante um minuto por uma mensagem antes que a chamada expire e o cliente faça uma nova chamada para verificar se há mensagens - isso reduz o número de vezes que o cliente faz chamadas passíveis de cobrança que não recupera as mensagens.

## Como tratar falhas do aplicativo e mensagens ilegíveis

O Barramento de Serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não for capaz de processar a mensagem por algum motivo, ele chamará o método **Abandon** na mensagem recebida (em vez do método **Complete**). Isso fará com que o Barramento de Serviço desbloqueie a mensagem na assinatura e disponibilize-a para ser recebida novamente, pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite associado a uma mensagem bloqueada na assinatura e, se houver falha no processamento da mensagem pelo aplicativo antes da expiração do tempo limite de bloqueio (por exemplo, se o aplicativo travar), o Barramento de Serviço desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Se houver falha do aplicativo após o processamento da mensagem, mas antes que a solicitação **Complete** seja gerada, a mensagem será entregue novamente ao aplicativo quando ele reiniciar. Isso é frequentemente chamado de **Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada pelo menos uma vez mas, em algumas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não tolerar o processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega de mensagem duplicada. Isso geralmente é obtido com a propriedade **Message_id** da mensagem, que permanecerá constante nas tentativas da entrega.

## Como excluir tópicos e assinaturas

O exemplo a seguir demonstra como excluir o tópico denominado**TestTopic** do namespace de serviço **HowToSample**:

     // Delete Topic
     namespaceManager.DeleteTopic("TestTopic");

A exclusão de um tópico também excluirá todas as assinaturas registradas com o tópico. As assinaturas também podem ser excluídas de forma independente. O código a seguir demonstra como excluir uma assinatura denominada
**HighMessages** do tópico **TestTopic**:

      namespaceManager.DeleteSubscription("TestTopic", "HighMessages");

## Próximas etapas

Agora que você já sabe os princípios dos tópicos do Barramento de Serviço, acesse estes links para saber mais.

-   Consulte a referência de MSDN: [Filas, tópicos e assinaturas][].
-   Referência da API para [SqlFilter][].
-   Compilar um aplicativo de trabalho que envia e recebe mensagens para e uma fila do Barramento de Serviço: [Sistema de mensagens agenciado do Barramento de Serviço .NET Tutorial][].

  [Próximas etapas]: #nextsteps
  [O que são tópicos e assinaturas do Barramento de Serviço]: #what-is
  [Criar um namespace de serviço]: #create-namespace
  [Obter as Credenciais de Gerenciamento Padrão para o Namespace]: #obtain-creds
  [Configurar seu aplicativo para usar o Barramento de serviço]: #configure-app
  [Como: Configurar uma cadeia de conexão do Barramento de Serviço]: #set-up-connstring
  [Como: Configurar sua cadeia de conexão]: #config-connstring
  [Como: Criar um tópico]: #create-topic
  [Como: Criar assinaturas]: #create-subscriptions
  [Como: Enviar mensagens para um tópico]: #send-messages
  [Como: Receber mensagens de uma assinatura]: #receive-messages
  [Como: Tratar falhas do aplicativo e mensagens ilegíveis]: #handle-crashes
  [Como: Excluir tópicos e assinaturas]: #delete-topics
  
  [Conceitos de tópico]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/sb-topics-01.png
  [Portal de gerenciamento do Azure]: http://manage.windowsazure.com
  
  
  
  
  
  
  [7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png
  
  [Filas, tópicos e assinaturas]: http://msdn.microsoft.com/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/microsoft.servicebus.messaging.sqlfilter.aspx
  [Tutorial do Sistema de mensagens agenciado .NET do Barramento de Serviço]: http://msdn.microsoft.com/library/hh367512.aspx

<!--HONumber=47-->
 