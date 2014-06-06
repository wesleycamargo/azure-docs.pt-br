<properties linkid="dev-net-how-to-service-bus-topics" urlDisplayName="Tópicos do Service Bus" pageTitle="Como usar os tópicos do Service Bus (.NET) - Azure" metaKeywords="Introdução aos tópicos do Service Bus do Azure, publicação assinatura mensagens Azure, tópicos de mensagens do Azure e assinaturas C# " description="Saiba como usar assinaturas e tópicos do Service Bus no Azure. Os códigos de exemplo são escritos para aplicativos .NET. " metaCanonical="" services="service-bus" documentationCenter=".NET" title="Como usar os tópicos e as assinaturas do Service Bus" authors=""  solutions="" writer="sethm" manager="dwrede" editor="mattshel"  />





#Como usar os tópicos/assinaturas do Service Bus

<span>Este guia mostrará como usar os tópicos e as assinaturas do Service Bus. As amostras são escritas em C\# e usam a API do .NET. Os cenários cobertos incluem **como criar tópicos e assinaturas, criar filtros de assinatura, enviar mensagens** a um tópico, **receber mensagens de uma assinatura** e **excluir tópicos e assinaturas**. Para obter mais informações sobre tópicos e assinaturas, consulte a seção [Próximas etapas][]. </span>

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

<h2><span class="short-header">Configurar o aplicativo</span>Configurar o aplicativo para usar o Service Bus</h2>

Quando você cria um aplicativo que usa o Service Bus, você precisa adicionar uma referência ao assembly do Service Bus e incluir os namespaces correspondentes.

<h2><span class="short-header">Obter o pacote do NuGet</span>Obter o pacote do NuGet do Service Bus</h2>

O pacote do **NuGet** do Service Bus é a maneira mais fácil de obter a API do Service Bus e configurar seu aplicativo com todas as dependências do Service Bus. A extensão do Visual Studio do NuGet facilita a instalação e a atualização de bibliotecas e ferramentas do Visual Studio e do Visual Studio Express 2012 para Web. O pacote do NuGet do Service Bus é a maneira mais fácil de obter a API do Service Bus e configurar seu aplicativo com todas as dependências do Service Bus.

Para instalar o pacote do NuGet em seu aplicativo, proceda da seguinte maneira:

1.  No Gerenciador de Soluções, clique com o botão direito do mouse em **Referências** e clique em **Gerenciar Pacotes do NuGet**.
2.  Pesquise por WindowsAzure" e selecione o item **Service Bus do Azure**. Clique em **Instalar** para concluir a instalação e fechar essa caixa de diálogo.

    ![][7]

Agora você está pronto para escrever código no Service Bus.

<h2><span class="short-header">Como configurar a cadeia de conexão</span>Como configurar uma cadeia de conexão do Service Bus</h2>

O Service Bus usa uma cadeia de conexão para armazenar pontos de extremidade e credenciais. Você pode colocar a cadeia de conexão em um arquivo de configuração, em vez de embuti-la no código:

- Ao usar os Serviços de Nuvem do Azure, é recomendável armazenar a cadeia de conexão usando o sistema de configuração de serviço do Azure (arquivos `*.csdef` e `*.cscfg`).
- Ao usar os Sites do Azure ou Máquinas Virtuais do Azure, é recomendável armazenar a cadeia de conexão usando o sistema de configuração .NET (por exemplo arquivos `web.config`).

Nos dois casos, você pode recuperar sua cadeia de conexão usando o método `CloudConfigurationManager.GetSetting`, conforme mostrado neste manual.

### <a name="config-connstring"> </a>Configurando a cadeia de conexão ao usar os Serviços de Nuvem

O mecanismo de configuração de serviço é exclusivo para projetos de Serviços de Nuvem do Azure e permite que você altere dinamicamente os parâmetros da configuração no Portal de Gerenciamento do Azure sem reimplantar o aplicativo.  Por exemplo, adicione uma Configuração para seu arquivo de definição de serviço (`*.csdef`), conforme mostrado abaixo:

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

Ao usar Sites ou Máquinas Virtuais, é recomendável usar o sistema de configuração do .NET (por exemplo, `web.config`).  Você armazena a cadeia de conexão usando o elemento `<appSettings>`:

	<configuration>
	    <appSettings>
		    <add key="Microsoft.ServiceBus.ConnectionString"
			     value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
		</appSettings>
	</configuration>

Use os valores de emissor e chave recuperados do Portal de Gerenciamento, conforme descrito na seção anterior.

<h2><span class="short-header">Como criar um tópico</span>Como criar um tópico</h2>

Você pode executar operações de gerenciamento de tópicos e assinaturas do Service Bus por meio da classe **NamespaceManager**. A classe **NamespaceManager** fornece métodos para criar, enumerar e excluir filas. 

Este exemplo constrói um objeto **NamespaceManager** usando a classe **CloudConfigurationManager** do Azure com uma cadeia de conexão que consiste do endereço base de um namespace de serviço do Service Bus e as credenciais apropriadas com permissões para gerenciá-lo. Essa cadeia de conexão está no formato

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

Há sobrecargas do método **CreateTopic** que permitem que você ajuste as propriedades do tópico (por exemplo, para definir o valor de "time-to-live" padrão a ser aplicado às mensagens enviadas para o tópico. Essas configurações são aplicadas usando a classe **TopicDescription**. O exemplo a seguir mostra como criar um tópico chamado "TestTopic" com um tamanho máximo de 5 GB e uma mensagem padrão com vida útil de um minuto:

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

**Observação:** você pode usar o método **TopicExists** em objetos **NamespaceManager** para verificar se já existe um tópico com um nome especificado em um namespace de serviço.

<h2><span class="short-header">Como criar assinaturas</span>Como criar assinaturas</h2>

Você também pode criar assinaturas de tópico com a classe **NamespaceManager**. As assinaturas são nomeadas e podem ter um filtro opcional que restringe o conjunto de mensagens passadas para a fila virtual de assinaturas.

### Criar uma assinatura com o filtro padrão (MatchAll)

O filtro **MatchAll** é o filtro padrão usado se nenhum filtro for especificado quando uma nova assinatura é criada. Quando o filtro **MatchAll** é usado, todas as mensagens publicadas no tópico são colocadas na fila virtual de assinaturas. O seguinte exemplo cria uma assinatura chamada "AllMessages" e usa o filtro padrão **MatchAll**.

	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

	var namespaceManager = 
		NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
    {
        namespaceManager.CreateSubscription("TestTopic", "AllMessages");
    }

### Criar assinaturas com filtros

Você também pode configurar filtros que permitem especificar quais mensagens enviadas a um tópico devem aparecer dentro de uma assinatura específica do tópico.

O tipo mais flexível de filtro suportado por assinaturas é o
**SqlFilter** que implementa um subconjunto de SQL92. Os filtros SQL operam nas propriedades das mensagens que são publicadas no tópico. Para obter mais detalhes sobre as expressões que podem ser usadas com um filtro SQL, examine a sintaxe [SqlFilter.SqlExpression][].

O exemplo a seguir cria uma assinatura denominada "HighMessages" com um
**SqlFilter** que seleciona somente mensagens com uma propriedade
**MessageNumber** personalizada maior que três:

     // Create a "HighMessages" filtered subscription
     SqlFilter highMessagesFilter = 
		new SqlFilter("MessageNumber > 3");

     namespaceManager.CreateSubscription("TestTopic", 
		"HighMessages", 
		highMessagesFilter);

Da mesma forma, o seguinte exemplo cria uma assinatura denominada "LowMessages" com um **SqlFilter** que seleciona apenas mensagens que têm uma propriedade **MessageNumber** menor ou igual a três:

     // Create a "LowMessages" filtered subscription
     SqlFilter lowMessagesFilter = 
		new SqlFilter("MessageNumber <= 3");

     namespaceManager.CreateSubscription("TestTopic", 
		"LowMessages", 
		lowMessagesFilter);

Agora, quando uma mensagem é enviada para "TestTopic", ela sempre será fornecida aos destinatários inscritos na assinatura do tópico "AllMessages" e será fornecida de forma seletiva aos destinatários inscritos nas assinaturas dos tópicos "HighMessages" e "LowMessages" (dependendo do conteúdo de mensagem).

<h2><span class="short-header">Enviar mensagens a um tópico</span>Como enviar mensagens a um tópico</h2>

Para enviar uma mensagem a um tópico do Service Bus, seu aplicativo cria um
objeto **TopicClient** usando a cadeia de conexão.

O código abaixo demonstra como criar um objeto **TopicClient** para o tópico "TestTopic" criado acima usando a chamada da API **CreateFromConnectionString**:

	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

   	TopicClient Client = 
		TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

	Client.Send(new BrokeredMessage());
 

As mensagens enviadas aos tópicos do Service Bus são instâncias da
classe **BrokeredMessage**. Os objetos **BrokeredMessage** têm um conjunto de propriedades padrão (como **Label** e **TimeToLive**), um dicionário que é usado para manter propriedades personalizadas específicas ao aplicativo e um corpo de dados arbitrários do aplicativo. Um aplicativo pode definir o corpo da mensagem passando qualquer objeto serializável para o construtor da
**BrokeredMessage**e o **DataContractSerializer** apropriados serão usados para serializar o objeto. Como alternativa, um
**System.IO.Stream** pode ser fornecido.

O exemplo a seguir demonstra como enviar cinco mensagens de teste para o "TestTopic" **TopicClient** obtido no trecho de código acima:
Observe como o valor da propriedade **MessageNumber** de cada mensagem varia de acordo com a iteração do loop (isso determinará quais assinaturas as recebem):

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

Os tópicos do Service Bus oferecem suporte a um tamanho máximo de mensagem de 256 Kb (o cabeçalho, incluindo as propriedades padrão e personalizadas do aplicativo, podem ter um tamanho máximo de 64 Kb. Não há nenhum limite sobre o número de mensagens mantidas em um tópico, mas há um limite de utilização para o tamanho total das mensagens mantidas por um tópico. O tamanho da fila é definido no momento da criação, com um limite superior de 5 GB.

<h2><span class="short-header">Receber mensagens de uma assinatura</span>Como receber mensagens de uma assinatura</h2>

A maneira mais fácil de receber mensagens de uma assinatura é usar um
objeto **SubscriptionClient**. Os objetos **SubscriptionClient** podem funcionar em dois modos diferentes: **ReceiveAndDelete** e **PeekLock**.

Ao usar o modo **ReceiveAndDelete**, receber é uma operação única, isto é, quando o Service Bus recebe uma solicitação de leitura de uma mensagem em uma assinatura, ela marca a mensagem como sendo consumida e retorna para o aplicativo. O modo **ReceiveAndDelete** é o modelo mais simples e funciona melhor em cenários nos quais o aplicativo pode tolerar o não processamento de uma mensagem em caso de falha. Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e há uma falha antes de processá-la. Como o Service Bus marcou a mensagem como consumida, quando o aplicativo reiniciar e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

No modo **PeekLock** (que é o modo padrão), o processo de recebimento se torna uma operação de duas etapas, o que torna possível o suporte a aplicativos que não toleram mensagens ausentes. Quando o Service Bus recebe uma solicitação, ele localiza a próxima mensagem a ser consumida, bloqueia-a para evitar que outros consumidores a recebam e a retorna para o aplicativo. Depois que o aplicativo conclui o processamento da mensagem (ou a armazena de forma segura para processamento futuro), ele conclui a segunda etapa do processo de recebimento chamando **Complete** na mensagem recebida. Quando o Service Bus vê a chamada de **Complete**, ele marca a mensagem como sendo consumida e remove-a da assinatura.

O exemplo a seguir demonstra como as mensagens podem ser recebidas e processadas usando o modo **PeekLock**. Para especificar outro valor de **ReceiveMode**, você pode usar outra sobrecarga para **CreateFromConnectionString**. Este exemplo cria um loop infinito e processa mensagens assim que chegam na assinatura "HighMessages". Observe que o caminho para a assinatura "HighMessages" é fornecido no formato "<*topicpath*\>/subscriptions/<*subscription name*\>".

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

<h2><span class="short-header">Falhas no aplicativo e mensagens ilegíveis</span>Como lidar com falhas no aplicativo e mensagens ilegíveis</h2>

O Service Bus fornece funcionalidade para ajudá-lo a se recuperar normalmente dos erros em seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele chamará o método **Abandon** na mensagem recebida (em vez do método **Complete**). Isso fará com que o Service Bus desbloqueie a mensagem na assinatura e disponibilize-a para ser recebida novamente, pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite associado a uma mensagem bloqueada na assinatura e, se houver falha no processamento da mensagem pelo aplicativo antes da expiração do tempo limite de bloqueio (por exemplo, se o aplicativo travar), o Service Bus desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Se houver falha no aplicativo após o processamento da mensagem, mas antes que o método **Complete** seja emitido, a mensagem será fornecida novamente ao aplicativo quando ele reiniciar. Isso normalmente é chamado de
**Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada pelo menos uma vez, mas, em determinadas situações, a mesma mensagem pode ser fornecida novamente. Se o cenário não tolerar processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega da mensagem duplicada. Isso geralmente é feito usando a
propriedade **MessageId** da mensagem, que permanecerá constante entre as tentativas de entrega.

<h2><span class="short-header">Excluir tópicos e assinaturas</span>Como excluir tópicos e assinaturas</h2>

O exemplo a seguir demonstra como excluir o tópico denominado
**TestTopic** do namespace do serviço **HowToSample**:

     // Delete Topic
     namespaceManager.DeleteTopic("TestTopic");

Excluir um tópico também excluirá todas as assinaturas que estiverem registradas com o tópico. As assinaturas também podem ser excluídas de forma independente. O código a seguir demonstra como excluir uma assinatura denominada
**HighMessages** do tópico **TestTopic**:

      namespaceManager.DeleteSubscription("TestTopic", "HighMessages");

<h2><span class="short-header">Próximas etapas</span>Próximas etapas</h2>

Agora que você já aprendeu os conceitos básicos dos tópicos e assinaturas do Service Bus, acesse estes links para obter mais informações.

-   Consulte a Referência do MSDN: [Filas, tópicos e assinaturas][].
-   Referência da API para [SqlFilter][].
-   Criar um aplicativo de trabalho que envia e recebe mensagens de e para uma fila do Service Bus: [Tutorial do Service Bus orientado para mensagens do .NET][].

  [Próximas etapas]: #nextsteps
  [O que são os tópicos e as assinaturas do Service Bus]: #what-is
  [Criar um namespace de serviço]: #create-namespace
  [Obter as credenciais de gerenciamento padrão do namespace]: #obtain-creds
  [Configurar seu aplicativo para usar o Service Bus]: #configure-app
  [Como configurar uma cadeia de conexão do Service Bus]: #set-up-connstring
  [Como configurar sua cadeia de conexão]: #config-connstring
  [Como criar um tópico]: #create-topic
  [Como criar assinaturas]: #create-subscriptions
  [Como enviar mensagens a um tópico]: #send-messages
  [Como receber mensagens de uma assinatura]: #receive-messages
  [Como tratar falhas do aplicativo e mensagens ilegíveis]: #handle-crashes
  [Como excluir tópicos e assinaturas]: #delete-topics
  
  [Conceitos do tópico]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/sb-topics-01.png
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  
  
  
  
  
  
  [7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png
  
  [Filas, tópicos e assinaturas]: http://msdn.microsoft.com/pt-br/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/pt-br/library/microsoft.servicebus.messaging.sqlfilter.aspx
  [Tutorial do Service Bus orientado para mensagens do .NET]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh367512.aspx

