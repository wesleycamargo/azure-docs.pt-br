<properties urlDisplayName="Service Bus Queues" pageTitle="Como usar filas do Barramento de Serviço (PHP) - Azure" metaKeywords="filas do Barramento de Serviço do Azure, filas do Azure, sistema de mensagens do Azure, filas do Azure em PHP" description="Learn how to use Service Bus queues in Azure. Code samples written in PHP." metaCanonical="" services="service-bus" documentationCenter="PHP" title="How to Use Service Bus Queues" authors="sethm" solutions="" manager="timlt" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/13/2014" ms.author="sethm" />

# Como usar as filas do Service Bus

Este guia mostra como usar as filas do Barramento de Serviço com o PHP. As amostras são escritas em PHP e usam o [SDK do Azure para PHP][download-sdk]. Os cenários abrangidos incluem **criar filas**, **enviar e receber mensagens** e **excluir filas**.

## Sumário

-   [O que são as filas do Barramento de Serviço?](#what-are-service-bus-queues)
-   [Criar um namespace de serviço](#create-a-service-namespace)
-   [Obter as credenciais de gerenciamento padrão do namespace](#obtain-default-credentials)
- 	[Criar um aplicativo PHP](#CreateApplication)
-	[Obter as bibliotecas de cliente do Azure](#GetClientLibrary)
-   [Configurar seu aplicativo para usar o Barramento de Serviço](#ConfigureApp)
-   [Como: Criar uma fila](#CreateQueue)
-   [Como: Enviar mensagens a uma fila](#SendMessages)
-   [Como: Receber mensagens de uma fila](#ReceiveMessages)
-   [Como: Tratar falhas do aplicativo e mensagens ilegíveis](#HandleCrashes)
-   [Próximas etapas](#NextSteps)

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

##<a id="CreateApplication"></a>Criar um aplicativo PHP

O único requisito para a criação de um aplicativo PHP que acessa o serviço Blob do Azure é a referência de classes no [SDK do Azure para PHP][download-sdk] dentro do seu código. Você pode usar as ferramentas de desenvolvimento para criar seu aplicativo, incluindo o bloco de notas.

> [WACOM.NOTE]
> A instalação do PHP também deve ter a <a href="http://php.net/openssl">extensão OpenSSL</a> instalada e habilitada.

Neste guia, você usará os recursos de serviços que podem ser chamados em um aplicativo PHP localmente ou no código em execução dentro de uma função web do Azure, uma função de trabalho ou um site.

##<a id="GetClientLibrary"></a>Obter as bibliotecas de cliente do Azure

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

##<a id="ConfigureApp"></a>Configurar seu aplicativo para usar o Barramento de Serviço

Para usar as APIs do Barramento de Serviço do Azure, você precisa:

1. Fazer referência ao arquivo de carregador automático usando a instrução [require_once][require_once] e
2. Fazer referência a qualquer classe que você possa usar.

O exemplo a seguir mostra como incluir o arquivo de carregador automático e fazer referência à classe **ServicesBuilder**.

> [WACOM.NOTE] 
> Esse exemplo (e outros exemplos deste artigo) pressupõe que você tenha instalado as Bibliotecas de Cliente do PHP para o Azure por meio do Compositor. Se você instalou as bibliotecas manualmente ou como um pacote PEAR, você precisará fazer referência ao arquivo de carregador automático <code>WindowsAzure.php</code>.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


Nos exemplos abaixo, a instrução 'require_once' será mostrada sempre, mas somente as classes necessárias para executar o exemplo serão referenciadas.

##<a id="ConnectionString"></a>Configurar uma conexão do Barramento de Serviço do Azure

Para criar uma instância de cliente do Barramento de Serviço do Azure, você deve primeiramente ter uma cadeia de conexão válida de acordo com este formato:

	Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]

Em que o ponto de extremidade normalmente está no formato `https://[yourNamespace].servicebus.windows.net`.

Para criar qualquer cliente de serviço do Azure é necessário usar a classe **ServicesBuilder**. Você pode:

* passar a cadeia de conexão diretamente para ele ou
*  usar o **CloudConfigurationManager (CCM)** para verificar várias origens externas para a cadeia de conexão:
	* por padrão, ele vem com suporte para uma origem externa - variáveis de ambiente
	* você pode adicionar novas origens ao estender a classe **ConnectionStringSource**

Para os exemplos descritos aqui, a cadeia de conexão será passada diretamente.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);


##<a id="CreateQueue"></a>Como: Criar uma fila

Operações de gerenciamento para as filas do Barramento de Serviço podem ser realizadas pela classe**ServiceBusRestProxy**. Um objeto **ServiceBusRestProxy** é construído por meio do método de fábrica **ServicesBuilder::createServiceBusService** com uma cadeia de conexão apropriada que encapsula as permissões de token para gerenciá-lo.

O exemplo a seguir mostra como criar uma instância de um **ServiceBusRestProxy** e chamar **ServiceBusRestProxy->createQueue** para criar um tópico denominado 'myqueue' em um namespace de serviço 'MySBNamespace':

    require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\QueueInfo;

	// Create Service Bus REST proxy.
		$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
	
	try	{
		$queueInfo = new QueueInfo("myqueue");
		
		// Create queue.
		$serviceBusRestProxy->createQueue($queueInfo);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/pt-br/library/windowsazure/dd179357
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

> [WACOM.NOTE]
> Você pode usar o método <b>listQueues</b> em objetos <b>ServiceBusRestProxy</b> para verificar se uma fila com um nome especificado já existe dentro de um namespace de serviço.

##<a id="SendMessages"></a>Como: Enviar mensagens a uma fila

Para enviar uma mensagem a uma fila do Barramento de Serviço, seu aplicativo chamará o método **ServiceBusRestProxy->sendQueueMessage**. O código abaixo demonstra como enviar uma mensagem à fila 'myqueue' que criamos acima no
namespace de serviço `MySBNamespace`.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\models\BrokeredMessage;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
	try	{
		// Create message.
		$message = new BrokeredMessage();
		$message->setBody("my message");
	
		// Send message.
		$serviceBusRestProxy->sendQueueMessage("myqueue", $message);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/pt-br/library/windowsazure/hh780775
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

As mensagens enviadas para (e recebidas das) filas de Barramento de Serviço são instâncias da classe **BrokeredMessage**. Os objetos **BrokeredMessage** possuem um conjunto de métodos padrão (como **getLabel**, **getTimeToLive**, **setLabel** e **setTimeToLive**) e propriedades que são usadas para manter propriedade específicas de aplicativo personalizadas e um corpo de dados de aplicativo arbitrários.

As filas do Barramento de Serviço dão suporte a um tamanho máximo de mensagem de 256 KB (o cabeçalho, incluindo as propriedades padrão e personalizadas do aplicativo podem ter um tamanho máximo de 64 KB). Não há nenhum limite no número de mensagens mantidas em uma fila mas há uma capacidade do tamanho total das mensagens mantidas por uma fila. Este limite superior do tamanho da fila é 5 GB.

##<a id="ReceiveMessages"></a>Como: Receber mensagens de uma fila

A principal maneira de receber mensagens de uma fila é usar um método **ServiceBusRestProxy->receiveQueueMessage**. As mensagens podem ser recebidas em dois modos diferentes: **ReceiveAndDelete** (o padrão) e **PeekLock**.

Quando o modo **ReceiveAndDelete** for usado, o recebimento será uma operação única, isto é, quando o Barramento de Serviço receber uma solicitação de leitura de uma mensagem em uma fila, ele marcará a mensagem como sendo consumida e a retornará ao aplicativo. O modo **ReceiveAndDelete** é um modelo mais simples e funciona melhor para cenários em um
aplicativo pode tolerar o não processamento de uma mensagem em caso de falha. Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la. Como o Service Bus marcou a mensagem como consumida e o aplicativo reiniciou e começou a consumir mensagens novamente, ela perdeu a mensagem que foi consumida antes da falha.

No modo **PeekLock**, o recebimento de uma mensagem se torna uma operação de dois estágios, o possibilita o suporte a aplicativos que não podem tolerar mensagens ausentes. Quando o Barramento de Serviço recebe uma solicitação, ele localiza a próxima mensagem a ser consumida, bloqueia-a para evitar que outros consumidores a recebam e, em seguida, retorna-a para o aplicativo. Depois que o aplicativo conclui o processamento da mensagem (ou a armazena de forma segura para processamento futuro), ele conclui a segunda etapa do processo de recebimento passando a mensagem recebida para **ServiceBusRestProxy->deleteMessage**. Quando o Barramento de Serviço vê a chamada **deleteMessage**, ele marca a mensagem como sendo consumida e remove-a da fila.

O exemplo a seguir demonstra como uma mensagem pode ser recebida e processada usando o modo **PeekLock** (não o modo padrão).

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\models\ReceiveMessageOptions;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
	try	{
		// Set the receive mode to PeekLock (default is ReceiveAndDelete).
		$options = new ReceiveMessageOptions();
		$options->setPeekLock();
		
		// Receive message.
		$message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
		echo "Body: ".$message->getBody()."<br />";
		echo "MessageID: ".$message->getMessageId()."<br />";
		
		/*---------------------------
			Process message here.
		----------------------------*/
		
		// Delete message. Not necessary if peek lock is not set.
		echo "Message deleted.<br />";
		$serviceBusRestProxy->deleteMessage($message);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/pt-br/library/windowsazure/hh780735
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

##<a id="HandleCrashes"></a>Como: Tratar falhas do aplicativo e mensagens ilegíveis

O Barramento de Serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não for capaz de processar a mensagem por algum motivo, ele chamará o método **unlockMessage** na mensagem recebida (em vez do método **deleteMessage**). Isso fará com que o Barramento de Serviço desbloqueie a mensagem na fila e disponibilize-a para que possa ser recebida novamente, tanto pelo mesmo aplicativo de consumo quanto por algum outro.

Também há um tempo limite associado a uma mensagem bloqueada na fila e, se o aplicativo não conseguir processar a mensagem antes da expiração do tempo limite do bloqueio (por exemplo, em caso de falha do aplicativo), o Service Bus desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Se houver falha do aplicativo após o processamento da mensagem, mas antes de a solicitação **deleteMessage** ser emitida, a mensagem será entregue novamente ao aplicativo quando ele reiniciar. Isso é frequentemente chamado de **Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada pelo menos uma vez mas, em algumas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não puder tolerar o processamento duplicado, é recomendável adicionar lógica extra ao aplicativo para tratar a entrega de mensagens duplicadas. Isso geralmente é feito com o método **getMessageId** da mensagem, que permanecerá constante nas tentativas da entrega.

##<a id="NextSteps"></a>Próximas etapas

Agora que você aprendeu as noções básicas sobre Filas do Barramento de Serviço, consulte o tópico do MSDN [Filas, tópicos e assinaturas][] para obter mais informações.

[download-sdk]: http://go.microsoft.com/fwlink/?LinkId=252473
[Diagrama de fila do Barramento de Serviço]: ../../../DevCenter/Java/Media/SvcBusQueues_01_FlowDiagram.jpg
[Portal de Gerenciamento do Azure]: http://manage.windowsazure.com/
[Captura de tela do nó do Service Bus]: ../../../DevCenter/Java/Media/SvcBusQueues_02_SvcBusNode.jpg
[Criar uma captura de tela do novo Namespace]: ../../../DevCenter/Java/Media/SvcBusQueues_03_CreateNewSvcNamespace.jpg
[Captura de tela de namespaces disponíveis]: ../../../DevCenter/Java/Media/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[Captura de tela da lista de namespaces]: ../../../DevCenter/Java/Media/SvcBusQueues_05_NamespaceList.jpg
[Captura de tela do painel Propriedades]: ../../../DevCenter/Java/Media/SvcBusQueues_06_PropertiesPane.jpg
[Captura de tela da chave padrão]: ../../../DevCenter/Java/Media/SvcBusQueues_07_DefaultKey.jpg
[Filas, tópicos e assinaturas]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh367516.aspx
[require_once]: http://php.net/require_once

