<properties urlDisplayName="Service Bus Queues" pageTitle="Como usar filas do Barramento de Serviço (.NET) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues C#, Azure queues .NET" description="Aprenda a usar as filas do barramento de serviço no Azure. Exemplos de códigos escritos em c# usando a API do .NET." metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use Service Bus Queues" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />





# Como usar filas do Barramento de Serviço

<span>Este guia mostra como usar filas do Barramento de Serviço. Os exemplos são escritos em C\# e usam a API .NET. Os cenários abrangidos incluem **criar filas, enviar e receber mensagens** e **excluir filas**. Para mais informações sobre filas, consulte a seção[Próximas etapas]. </span>

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

##Configurar o aplicativo para usar o Barramento de Serviço

Quando você cria um aplicativo que usa Barramento de Serviço, você deve
adicionar uma referência ao assembly Barramento de Serviço e incluir os
namespaces correspondentes.

##Obtenha o pacote do NuGet do Barramento de Serviço

O pacote **NuGet** de Barramento de Serviço é a maneira mais fácil de obter a API de Barramento de Serviço e configurar seu aplicativo com todas as dependências de Barramento de Serviço.A extensão do Visual Studio do NuGet facilita a instalação e a atualização de bibliotecas e ferramentas no Visual Studio e no Visual Studio Express 2012 para Web.

Para instalar o pacote do NuGet em seu aplicativo, proceda da seguinte maneira:

1.  No Gerenciador de Soluções, clique com o botão direito do mouse em **Referências**, depois clique em **Gerenciar Pacotes NuGet**.
2.  Pesquise por MicrosoftAzure" e selecione o item **Barramento de Serviço do Azure**. Clique em **Instalar** para concluir a instalação e, em seguida, feche essa caixa de diálogo.

    ![][7]

Agora você está pronto para escrever código no Barramento de Serviço.


##Como configurar uma cadeia de conexão do Barramento de Serviço

O Barramento de Serviço usa uma cadeia de conexão para armazenar pontos de extremidade e credenciais. Você pode colocar a cadeia de conexão em um arquivo de configuração, em vez de embuti-la no código:

- Ao usar os Serviços de Nuvem do Azure, é recomendável armazenar a cadeia de conexão usando o sistema de configuração de serviço do Azure (arquivos `*.csdef` e`*.cscfg`).
- Ao usar Sites do Azure ou Máquinas Virtuais do Azure, é recomendável armazenar a cadeia de conexão usando o sistema de configuração do .NET (por exemplo, arquivo `web.config`).

Nos dois casos, você pode recuperar sua cadeia de conexão usando o método `CloudConfigurationManager.GetSetting`, conforme mostrado neste manual.

### <a name="config-connstring"> </a>Configurando a cadeia de conexão ao usar Serviços de Nuvem

O mecanismo de configuração de serviço é exclusivo para projetos de Serviços de Nuvem do Azure e permite que você altere dinamicamente os parâmetros de configuração no Portal de Gerenciamento do Azure sem reimplantar o aplicativo.  Por exemplo, adicione uma Configuração ao seu arquivo de definição de serviço (`*.csdef`), conforme mostrado abaixo:

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

Ao usar Sites ou Máquinas Virtuais, é recomendável usar o sistema de configuração do .NET (por exemplo,`web.config`).  Você armazena a cadeia de conexão usando o elemento `<appSettings>`:

	<configuration>
	    <appSettings>
		    <add key="Microsoft.ServiceBus.ConnectionString"
			     value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
		</appSettings>
	</configuration>

Use os valores de emissor e chave recuperados do Portal de Gerenciamento, conforme descrito na seção anterior.

##Como criar uma fila

Você pode executar operações de gerenciamento de filas do Barramento de Serviço por meio da classe **NamespaceManager**. A classe**NamespaceManager** fornece métodos para criar, enumerar e excluir filas. 

Este exemplo constrói um objeto **NamespaceManager** usando a classe **CloudConfigurationManager** do Azure com uma cadeia de conexão que consiste no endereço básico de um namespace de serviço do Barramento de Serviço e as credenciais apropriadas com permissões para gerenciá-lo. Essa cadeia de conexão está no formato 

	Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]

Por exemplo, considerando os parâmetros de configuração da seção anterior:

	// Create the queue if it does not exist already
	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

	var namespaceManager = 
		NamespaceManager.CreateFromConnectionString(connectionString);

    if (!namespaceManager.QueueExists("TestQueue"))
    {
        namespaceManager.CreateQueue("TestQueue");
    }

Há sobrecargas do método **CreateQueue** que permitem que você ajuste as propriedades da fila (por exemplo, para definir o valor de "time-to-live" padrão a ser aplicado às mensagens enviadas para a fila). Essas configurações são aplicadas usando a classe **QueueDescription**.O exemplo a seguir mostra como criar uma fila chamada "TestQueue" com um tamanho máximo de 5 GB e uma mensagem padrão do tempo de vida de 1 minuto:

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

**Observação**:  Você pode usar o método **QueueExists** em objetos **NamespaceManager**para verificar se uma fila com um nome especificado já existe no namespace de serviço.

##Como enviar mensagens para uma fila

Para enviar uma mensagem para uma fila do Barramento de Serviço, seu aplicativo cria um objeto**QueueClient** usando a cadeia de conexão.

O código a seguir demonstra como criar um objeto**QueueClient** 
para a fila "TestQueue" criado acima utilizando a chamada à API **CreateFromConnectionString**:

	string connectionString = 
	    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    QueueClient Client = 
		QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

	Client.Send(new BrokeredMessage());

As mensagens enviadas para (e recebidas das) filas de Barramento de Serviço são instâncias da classe **BrokeredMessage**.   Objetos**BrokeredMessage** têm um conjunto de propriedades padrão (como **Label** e**TimeToLive**), um dicionário usado para manter as propriedades personalizadas específicas do aplicativo e um corpo de dados de aplicativo arbitrários. Um aplicativo pode definir o corpo da mensagem, passando qualquer objeto serializável para o construtor da**BrokeredMessage**e o**DataContractSerializer** adequado então será usado para serializar o objeto.Como alternativa, um **System.IO.Stream* pode ser fornecido.O exemplo a seguir demonstra como enviar cinco mensagens de teste para o "TestQueue" **QueueClient** obtido no trecho de código acima:

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

As filas de Barramento de Serviço dão suporte a um tamanho máximo de mensagem de 256 KB (o cabeçalho, que inclui as propriedades do aplicativo padrão e personalizadas, pode te rum tamanho máximo de 64 KB). Não há nenhum limite no número de mensagens mantidas em uma fila mas há uma capacidade do tamanho total das mensagens mantidas por uma fila. O tamanho da fila é definido no momento da criação, com um limite superior de 5 GB.

##Como receber mensagens de uma fila

A maneira mais fácil para receber mensagens de uma fila é usar um objeto **QueueClient**. Esses objetos podem trabalhar em dois modos diferentes:**ReceiveAndDelete** e **PeekLock**.

Ao usar o modo **ReceiveAndDelete**, o recebimento é uma operação única, isto é, quando o Barramento de Serviço recebe uma solicitação de leitura de uma mensagem em uma fila, ele marca a mensagem como sendo consumida e a retorna ao aplicativo.  O modo **ReceiveAndDelete** é o modelo mais simples e funciona melhor em cenários nos quais um aplicativo pode tolerar o não processamento de uma mensagem em caso de falha.Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la.Como o Barramento de Serviço terá marcado a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

No modo **PeekLock** (que é o modo padrão), o processo de recebimento torna-se uma operação de duas etapas, o que torna possível o suporte a aplicativos que não toleram mensagens ausentes. Quando o Barramento de Serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, ele a bloqueia para evitar que outros consumidores a recebam e a retorna para o aplicativo. Depois que o aplicativo conclui o processamento da mensagem (ou a armazena de forma segura para processamento futuro), ele conclui a segunda etapa do processo de recebimento chamando **Complete** na mensagem recebida.Quando o Barramento de Serviço vê a chamada **Complete**, ele marca a mensagem como sendo consumida e a remove da fila.

O exemplo a seguir demonstra como as mensagens podem ser recebidas e processadas usando o modo **PeekLock**. Para especificar outro valor de **ReceiveMode**, você pode usar outra sobrecarga para **CreateFromConnectionString**.Este exemplo cria um loop infinito e processa mensagens assim que chegarem no "TestQueue":

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

##Como tratar falhas do aplicativo e mensagens ilegíveis

O Barramento de Serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não for capaz de processar a mensagem por algum motivo, ele chamará o método **Abandon** na mensagem recebida (em vez do método **Complete**).Isso fará com que o Barramento de Serviço desbloqueie a mensagem na fila e disponibilize-a para que ela possa ser recebida novamente pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite associado a uma mensagem bloqueada na fila e, se houver falha no processamento da mensagem pelo aplicativo da expiração do tempo limite de bloqueio (por exemplo, se o aplicativo travar), o Barramento de Serviço desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Se houver falha do aplicativo após o processamento da mensagem, mas antes que a solicitação **Complete** seja gerada, a mensagem será entregue novamente ao aplicativo quando ele reiniciar.  Isso é frequentemente chamado de **Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada pelo menos uma vez mas, em algumas situações, a mesma mensagem poderá ser entregue novamente.Se o cenário não tolerar o processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega de mensagem duplicada.Isso geralmente é obtido com a propriedade **MessageId** da mensagem, que permanecerá constante nas tentativas da entrega.

##Próximas etapas

Agora que você já sabe as noções básicas das filas de Barramento de Serviço, siga estes
links para saber mais.

-   Consulte a referência de MSDN: [Filas, tópicos e assinaturas.][]
-   Compilar um aplicativo de trabalho que envia e recebe mensagens para e
    uma fila do Barramento de Serviço: [Sistema de mensagens agenciado .NET do Barramento de Serviço
    Tutorial].

  [Próximas etapas]: #next-steps
  [O que são as filas do Barramento de Serviço]: #what-queues
  [Criar um namespace de serviço]: #create-namespace
  [Obter as credenciais de gerenciamento padrão do namespace]: #obtain-creds
  [Configurar seu aplicativo para usar o Barramento de Serviço]: #configure-app
  [Como: Configurar uma cadeia de conexão do Barramento de Serviço]: #set-up-connstring
  [Como: Configurar sua cadeia de conexão]: #config-connstring
  [Como: Criar uma fila]: #create-queue
  [Como: Enviar mensagens para uma fila]: #send-messages
  [Como: Receber mensagens de uma fila]: #receive-messages
  [Como: Tratar falhas do aplicativo e mensagens ilegíveis]: #handle-crashes
  [Conceitos de fila]: ./media/service-bus-dotnet-how-to-use-queues/sb-queues-08.png
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  
  
  
  
  
  
  [7]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [Filas, tópicos e assinaturas.]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh367516.aspx
  [Tutorial do Sistema de mensagens agenciado .NET do Barramento de Serviço]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh367512.aspx

<!--HONumber=35.1-->
