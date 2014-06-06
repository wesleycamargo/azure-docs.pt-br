<properties linkid="dev-net-how-to-service-bus-queues" urlDisplayName="Filas do Service Bus" pageTitle="Como usar filas do Service Bus (.NET) - Azure" metaKeywords="Filas do Azure Service Bus, filas Azure, mensagens do Azure, C# de filas do Azure, filas do Azure .NET" description="Saiba como usar as filas do Service Bus no Azure. Amostras de código escritas em C# usando a API do .NET." metaCanonical="" services="service-bus" documentationCenter=".NET" title="Como usar filas do Service Bus" authors=""  solutions="" writer="sethm" manager="dwrede" editor="mattshel"  />





# Como usar as filas do Service Bus

<span>Este guia mostra como usar as filas do Service Bus. As amostras são escritas em C\# e usam a API do .NET. Os cenários abrangidos
incluem **criar filas, enviar e receber mensagens** e
**excluir filas**. Para obter mais informações sobre filas, consulte a seção [Próximas etapas]. </span>

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

<h2><span class="short-header">Configurar o aplicativo</span>Configurar o aplicativo para usar o Service Bus</h2>

Quando você cria um aplicativo que usa o Service Bus, você deve
adicionar uma referência ao assembly do Service Bus e incluir os
espaços para nome correspondentes.

<h2><span class="short-header">Obter o pacote do NuGet</span>Obter o pacote do NuGet do Service Bus</h2>

O pacote do **NuGet** do Service Bus é a maneira mais fácil de obter a API de Service Bus e configurar seu aplicativo com todas as dependências do Service Bus. A extensão do Visual Studio do NuGet facilita a instalação e a atualização de bibliotecas e ferramentas do Visual Studio e do Visual Studio Express 2012 para Web. O pacote do NuGet de Service Bus é a maneira mais fácil
de obter a API do Service Bus e configurar seu aplicativo com todas
as dependências do Service Bus.

Para instalar o pacote do NuGet em seu aplicativo, proceda da seguinte maneira:

1.  No Gerenciador de Soluções, clique com o botão direito do mouse em **Referências** e clique em
**Gerenciar Pacotes do NuGet**.
2.  Pesquise por WindowsAzure" e selecione o item **Service Bus
do Azure**. Clique em **Instalar** para concluir a instalação
e feche essa caixa de diálogo.

    ![][7]

Agora você está pronto para escrever código no Service Bus.

<h2><span class="short-header">Configurar a cadeia de conexão</span>Como configurar uma cadeia de conexão do Service Bus</h2>

O Service Bus usa uma cadeia de conexão para armazenar pontos de extremidade e credenciais. Você pode colocar a cadeia de conexão em um arquivo de configuração, em vez de embuti-la no código:

- Ao usar os Serviços de Nuvem do Azure, é recomendável armazenar a cadeia de conexão usando o sistema de configuração de serviço do Azure (arquivos `*.csdef` e `*.cscfg`).
- Ao usar os Sites do Azure ou Máquinas Virtuais do Azure, é recomendável armazenar a cadeia de conexão usando o sistema de configuração .NET (por exemplo arquivos `web.config`).

Nos dois casos, você pode recuperar sua cadeia de conexão usando o método `CloudConfigurationManager.GetSetting`, conforme mostrado neste guia.

### <a name="config-connstring"> </a>Configurando a cadeia de conexão ao usar os Serviços de Nuvem

O mecanismo de configuração de serviço é exclusivo para projetos de Serviços de Nuvem do Azure e permite que você altere dinamicamente os parâmetros de configuração no Portal de Gerenciamento do Azure sem reimplantar
o aplicativo.  Por exemplo, adicione uma Configuração para seu arquivo de definição do serviço (`*.csdef`), como mostrado abaixo:

	<ServiceDefinition name="WindowsAzure1">
	...
		<WebRole name="MyRole" vmsize="Small">
	    	<ConfigurationSettings>
	      		<Setting name="Microsoft.ServiceBus.ConnectionString" />
    		</ConfigurationSettings>
  		</WebRole>
	...
	</ServiceDefinition>

Especifique valores no arquivo de configuração do serviço (`*.cscfg`):

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

Use os valores de emissor e chave recuperados do Portal de Gerenciamento, conforme
descrito na seção anterior.

### Configurando a cadeia de conexão ao usar os Sites ou Máquinas Virtuais

Ao usar as Máquinas Virtuais ou Sites, é recomendável usar o sistema de configuração do .NET (por exemplo, `web. config`).  Você armazena a cadeia de conexão usando o elemento `<appSettings>`:

	<configuration>
	    <appSettings>
		    <add key="Microsoft.ServiceBus.ConnectionString"
			     value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
		</appSettings>
	</configuration>

Use os valores de emissor e chave recuperados do Portal de Gerenciamento, conforme
descrito na seção anterior.

<h2><span class="short-header">Como criar uma fila</span>Como criar uma fila</h2>

Você pode executar operações de gerenciamento de filas do Service Bus por meio da classe **NamespaceManager**. A classe **NamespaceManager** fornece métodos para criar, enumerar e excluir filas. 

Esse exemplo constrói um objeto **NamespaceManager** usando o a classe **CloudConfigurationManager** do Azure com uma cadeia de conexão que consiste do endereço base de um namespace de serviço do Service Bus e as credenciais apropriadas com permissões para gerenciá-lo. Essa cadeia de conexão é da forma 

	Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]

Por exemplo, considerando as definições de configuração na seção anterior:

	// Create the queue if it does not exist already
	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

	var namespaceManager = 
		NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue("TestQueue");
    }

Há sobrecargas do método **CreateQueue** que permitem que você ajuste as propriedades
da fila (por exemplo, para definir o valor de "time-to-live" padrão a ser aplicado às mensagens enviadas para a fila). Essas
configurações são aplicadas usando a classe **QueueDescription**. O
exemplo a seguir mostra como criar uma fila chamada "TestQueue" com um
tamanho máximo de 5GB e uma mensagem padrão do tempo de vida de um minuto:

	// Configure Queue Settings
    QueueDescription qd = new QueueDescription("TestQueue");
    qd.MaxSizeInMegabytes = 5120;
    qd.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

	// Create a new Queue with custom settings
	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

	var namespaceManager = 
		NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue(qd);
    }

**Observação:** você pode usar o método **QueueExists** em objetos **NamespaceManager**
para verificar se já existe uma fila com um nome especificado dentro de um
namespace de serviço.

<h2><span class="short-header">Enviar mensagens para uma fila</span>Como enviar mensagens para uma fila</h2>

Para enviar uma mensagem para uma fila do Service Bus, seu aplicativo cria um
objeto **QueueClient** usando a cadeia de conexão.

O código abaixo demonstra como criar um objeto **QueueClient**
para a fila "TestQueue" criada acima usando a chamada de API **CreateFromConnectionString**:

	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    QueueClient Client = 
		QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

	Client.Send(new BrokeredMessage());

Mensagens enviadas para (e recebidas de) filas do Service Bus são instâncias da
classe **BrokeredMessage**. Os objetos **BrokeredMessage** têm um conjunto de
propriedades padrão (como **Label** e **TimeToLive**), um dicionário
que é usado para manter as propriedades específicas do aplicativo personalizadas e um corpo
de dados arbitrários do aplicativo. Um aplicativo pode definir o corpo da
mensagem, passando qualquer objeto serializável para o construtor do
**BrokeredMessage** e o **DataContractSerializer** adequado será
usado para serializar o objeto. Como alternativa, um
**System.IO.Stream** pode ser fornecido.

O exemplo a seguir demonstra como enviar cinco mensagens de teste para o
"TestQueue" **QueueClient** obtido no snippet de código acima:

     for (int i=0; i<5; i++)
     {
       // Create message, passing a string message for the body
       BrokeredMessage message = new BrokeredMessage("Test message " + i);

       // Set some addtional custom app-specific properties
       message.Properties["TestProperty"] = "TestValue";
       message.Properties["Message number"] = i;   

       // Send message to the queue
       Client.Send(message);
     }

As filas do Service Bus suportam um tamanho máximo de mensagem de 256 KB (o cabeçalho,
incluindo as propriedades padrão e personalizadas do aplicativo podem ter um
tamanho máximo de 64 KB). Não há nenhum limite no número de mensagens
mantidas em uma fila mas há uma capacidade do tamanho total das mensagens
mantidas por uma fila. O tamanho da fila é definido no momento da criação, com um
limite superior de 5 GB.

<h2><span class="short-header">Receber mensagens de uma fila</span>Como receber mensagens de uma fila</h2>

A maneira mais fácil para receber mensagens de uma fila é usar um
objeto **QueueClient**. Esses objetos podem trabalhar em dois
modos diferentes: **ReceiveAndDelete** e **PeekLock**.

Ao usar o modo **ReceiveAndDelete**, o recebimento é uma operação
de única captura - isto é, quando o Service Bus recebe uma solicitação de leitura de
uma mensagem em uma fila, ele marca a mensagem como consumida e retorna
para o aplicativo. O modo **ReceiveAndDelete** é o modelo mais simples
e funciona melhor em cenários onde um aplicativo pode tolerar o não
processamento de uma mensagem em caso de falha. Para compreender isso,
considere um cenário no qual o consumidor emite a solicitação de recebimento e
falha antes de processá-la. Como o Service Bus marcou a mensagem
como consumida e o aplicativo reiniciou e começou a consumir
mensagens novamente, ela perdeu a mensagem que
foi consumida antes da falha.

No modo **PeekLock** (que é o modo padrão), o recebimento se torna uma operação de dois estágios, o que torna possível o suporte a aplicativos que não podem tolerar mensagens ausentes. Quando o Service Bus recebe uma solicitação,
ele encontra a próxima mensagem a ser consumida, ela a bloqueia para evitar que outros
consumidores a recebam e a retorna para o aplicativo. Depois
que o aplicativo termina de processar a mensagem (ou a armazena de forma segura
para um processamento futuro), ele conclui o segundo estágio processo de recebimento
chamando **Concluído** na mensagem recebida. Quando o Service
Bus vê a chamada **Concluída**, ele marca a mensagem como sendo
consumida e remove-a da fila.

O exemplo a seguir demonstra como as mensagens podem ser recebidas e
processadas usando o modo **PeekLock** padrão. Para especificar um outro valor **ReceiveMode**, você pode usar outra sobrecarga para **CreateFromConnectionString**. Este exemplo cria um loop infinito e processa mensagens assim que chegarem no "TestQueue":

    Client.Receive();
     
    // Continuously process messages sent to the "TestQueue" 
    while (true) 
    {  
       BrokeredMessage message = Client.Receive();

       if (message != null)
       {
          try 
          {
             Console.WriteLine("Body: " + message.GetBody<string>());
             Console.WriteLine("MessageID: " + message.MessageId);
             Console.WriteLine("Test Property: " + 
				message.Properties["TestProperty"]);

             // Remove message from queue
             message.Complete();
          }
          catch (Exception)
          {
             // Indicate a problem, unlock message in queue
             message.Abandon();
          }
       }
    } 

<h2><span class="short-header">Falhas do aplicativos e mensagens ilegíveis</span>Como lidar com falhas do aplicativo e mensagens ilegíveis</h2>

O Service Bus proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos
erros em seu aplicativo ou das dificuldades do processamento de uma mensagem. Se um
aplicativo receptor não puder processar a mensagem por algum motivo,
ele chamará o método **Abandon** na mensagem recebida (em vez do
método **Complete**). Isso fará com que o Service Bus desbloqueie a
mensagem na fila e disponibilize-a para que possa ser recebida novamente,
tanto pelo mesmo aplicativo de consumo quanto por algum outro
aplicativo.

Também há um tempo limite associado a uma mensagem bloqueada na
fila, e se o aplicativo falhar em processar a mensagem antes da
expiração do tempo limite do bloqueio (por exemplo, se o aplicativo falhar), o Service
Bus desbloqueará a mensagem automaticamente e a disponibilizará para ser
recebida novamente.

Caso o aplicativo falhe após o processamento da mensagem,
mas antes que o método **Concluir** seja emitido, a mensagem será
fornecida novamente ao aplicativo quando reiniciar. Isso é frequentemente chamado
**Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada
pelo menos uma vez mas, em algumas situações, a mesma mensagem pode ser
fornecida novamente. Se o cenário não tolerar o processamento duplicado, os
desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo
para tratar a entrega de mensagem duplicada. Isso geralmente é obtido com a
propriedade **MessageId** da mensagem, que permanecerá constante nas
tentativas da entrega.

<h2><span class="short-header">Próximas etapas</span>Próximas etapas</h2>

Agora que você já sabe os princípios das filas do Service Bus, acesse
estes links para saber mais.

-   Consulte a Referência do MSDN: [Filas, tópicos e assinaturas.][]
-   Criar um aplicativo de trabalho que envia e recebe mensagens em
    uma fila do Service Bus: [Tutorial do Service Bus orientado para mensagens
    .NET].

  [Próximas etapas]: #next-steps
  [O que são as filas do Service Bus]: #what-queues
  [Criar um namespace de serviço]: #create-namespace
  [Obter as credenciais de gerenciamento padrão do namespace]: #obtain-creds
  [Configurar seu aplicativo para usar o Service Bus]: #configure-app
  [Tutorial: Configurar uma cadeia de conexão do Service Bus]: #set-up-connstring
  [Tutorial: Configurar a cadeia de conexão]: #config-connstring
  [Tutorial: Criar uma fila]: #create-queue
  [Tutoria: Enviar mensagens a uma fila]: #send-messages
  [Tutorial: Receber mensagens de uma fila]: #receive-messages
  [Tutorial: Tratar falhas do aplicativo e mensagens ilegíveis]: #handle-crashes
  [Conceitos de fila]: ./media/service-bus-dotnet-how-to-use-queues/sb-queues-08.png
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  
  
  
  
  
  
  [7]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [Filas, tópicos e assinaturas.]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh367516.aspx
  [Tutorial do Service Bus orientado para mensagens .NET]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh367512.aspx

