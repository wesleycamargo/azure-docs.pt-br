<properties
	pageTitle="Como usar o Armazenamento de Fila do PHP | Microsoft Azure"
	description="Saiba como usar o serviço de armazenamento de Filas do Azure para criar e excluir filas, bem como para inserir, obter e excluir mensagens. As amostras são escritas em PHP."
	documentationCenter="php"
	services="storage"
	authors="tfitzmac"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="tomfitz"/>

# Como usar o Armazenamento de Fila do PHP

[AZURE.INCLUDE [armazenamento-seletor-fila-include](../../includes/storage-selector-queue-include.md)]

## Visão geral

Este guia lhe mostrará como executar cenários comuns usando o serviço de Armazenamento de Fila do Azure. Os exemplos são gravados usando classes do SDK do Windows para PHP. Os cenários abrangidos incluem inserir, exibir, obter e excluir mensagens da fila, bem como criar e excluir filas.

[AZURE.INCLUDE [armazenamento-fila-conceitos-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Criar um aplicativo PHP

O único requisito para a criação de um aplicativo PHP que acessa o armazenamento de Filas do Azure é a referência das classes do SDK do Azure para PHP de dentro de seu código. Você pode usar as ferramentas de desenvolvimento para criar seu aplicativo, incluindo o bloco de notas.

Neste guia, você usará os recursos do armazenamento de Filas que podem ser chamados dentro de um aplicativo PHP localmente ou no código em execução dentro de uma função web do Azure, função de trabalho ou no site.

## Obter as bibliotecas de cliente do Azure

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## Configurar seu aplicativo para acessar o armazenamento de Filas

Para usar as APIs de armazenamento de Filas do Azure, você precisa:

1. Fazer referência ao arquivo do carregador automático usando a instrução [require\_once].
2. Fazer referência a qualquer classe que possa usar.

O exemplo a seguir mostra como incluir o arquivo de carregador automático e fazer referência à classe **ServicesBuilder**.

> [AZURE.NOTE]
Este exemplo (e outros exemplos neste artigo) pressupõe que você instalou as Bibliotecas de Cliente PHP para Azure por meio do Compositor. Se você instalou as bibliotecas manualmente ou como um pacote PEAR, você precisará fazer referência ao arquivo de carregador automático `WindowsAzure.php`.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


Nos exemplos abaixo, a instrução `require_once` será mostrada sempre, mas somente as classes que são necessárias para executar o exemplo serão referenciadas.

## Configurar uma conexão de armazenamento do Azure

Para criar uma instância de um cliente de armazenamento de Filas do Azure, você deve primeiramente ter uma cadeia de conexão válida. O formato da cadeia de conexão do serviço Fila é o seguinte.

Para acessar um serviço ao vivo:

	DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Para acessar o armazenamento do emulador:

	UseDevelopmentStorage=true


Para criar qualquer cliente de serviço do Azure, é necessário usar a classe **ServicesBuilder**. É possível usar qualquer uma das técnicas a seguir:

* Passar a cadeia de conexão diretamente para ele.
* Use **CloudConfigurationManager (CCM)** para verificar várias fontes externas da cadeia de conexão:
	* Por padrão, ele é fornecido com suporte para uma fonte externa – variáveis de ambiente.
	* É possível adicionar novas origens ao estender a classe **ConnectionStringSource**.

Para os exemplos descritos aqui, a cadeia de conexão será passada diretamente.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);


## Criar uma fila

O objeto **QueueRestProxy** permite que você crie uma fila com o método **createQueue**. Ao criar uma fila, você pode definir opções na fila, mas fazer isso não é necessário. (O exemplo abaixo mostra como definir metadados em uma fila.)

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Queue\Models\CreateQueueOptions;

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

	// OPTIONAL: Set queue metadata.
	$createQueueOptions = new CreateQueueOptions();
	$createQueueOptions->addMetaData("key1", "value1");
	$createQueueOptions->addMetaData("key2", "value2");

	try	{
		// Create queue.
		$queueRestProxy->createQueue("myqueue", $createQueueOptions);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/azure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

> [AZURE.NOTE] Você não deve depender de maiúsculas e minúsculas para as chaves de metadados. Todas as chaves são lidas do serviço em letras minúsculas.


## Adicionar uma mensagem a uma fila

Para adicionar uma mensagem para uma fila, use **QueueRestProxy->createMessage**. O método utiliza o nome da fila, o texto da mensagem e opções de mensagem (que são opcionais).

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Queue\Models\CreateMessageOptions;

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

	try	{
		// Create message.
		$builder = new ServicesBuilder();
		$queueRestProxy->createMessage("myqueue", "Hello World!");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/azure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## Espiar a próxima mensagem

Você pode exibir uma mensagem (ou mensagens) na frente de uma fila sem removê-la da fila chamando **QueueRestProxy->peekMessages**. Por padrão, o método **peekMessage** retorna uma única mensagem, mas você pode alterar esse valor com o método **PeekMessagesOptions->setNumberOfMessages**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Queue\Models\PeekMessagesOptions;

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

	// OPTIONAL: Set peek message options.
	$message_options = new PeekMessagesOptions();
	$message_options->setNumberOfMessages(1); // Default value is 1.

	try	{
		$peekMessagesResult = $queueRestProxy->peekMessages("myqueue", $message_options);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/azure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

	$messages = $peekMessagesResult->getQueueMessages();

	// View messages.
	$messageCount = count($messages);
	if($messageCount <= 0){
		echo "There are no messages.<br />";
	}
	else{
		foreach($messages as $message)	{
			echo "Peeked message:<br />";
			echo "Message Id: ".$message->getMessageId()."<br />";
			echo "Date: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
			echo "Message text: ".$message->getMessageText()."<br /><br />";
		}
	}

## Remover a próxima mensagem da fila

Seu código remove uma mensagem de uma fila em duas etapas. Primeiro, você chama **QueueRestProxy->listMessages**, que torna a mensagem invisível para qualquer outro código de leitura da fila. Por padrão, essa mensagem permanecerá invisível por 30 segundos. (Se a mensagem não for excluída neste período de tempo, ela se tornará visível na fila novamente.) Para terminar de remover a mensagem da fila, você deve chamar **QueueRestProxy->deleteMessage**. Este processo de duas etapas de remover uma mensagem garante que quando o código não processa uma mensagem devido à falhas de hardware ou de software, outra instância do seu código pode receber a mesma mensagem e tentar novamente. Seu código chama **deleteMessage** logo depois que a mensagem é processada.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

	// Get message.
	$listMessagesResult = $queueRestProxy->listMessages("myqueue");
	$messages = $listMessagesResult->getQueueMessages();
	$message = $messages[0];

	/* ---------------------
		Process message.
	   --------------------- */

	// Get message ID and pop receipt.
	$messageId = $message->getMessageId();
	$popReceipt = $message->getPopReceipt();

	try	{
		// Delete message.
		$queueRestProxy->deleteMessage("myqueue", $messageId, $popReceipt);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/azure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## Alterar o conteúdo de uma mensagem na fila

Você pode alterar o conteúdo de uma mensagem no local na fila chamando **QueueRestProxy->updateMessage**. Se a mensagem representar uma tarefa de trabalho, você poderá usar esse recurso para atualizar o status da tarefa de trabalho. O código a seguir atualiza a mensagem da fila com novo conteúdo e define o tempo limite de visibilidade para estender mais 60 segundos. Isso salva o estado do trabalho que está associado à mensagem e dá ao cliente mais um minuto para continuar trabalhando na mensagem. Você pode usar essa técnica para acompanhar fluxos de trabalho de várias etapas em mensagens em fila, sem a necessidade de começar desde o início, caso uma etapa de processamento falhar devido a uma falha de hardware ou de software. Normalmente, você mantém uma contagem de repetições e, se a mensagem for repetida mais de *n* vezes, você a exclui. Isso protege contra uma mensagem que dispara um erro do aplicativo sempre que for processada.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

	// Get message.
	$listMessagesResult = $queueRestProxy->listMessages("myqueue");
	$messages = $listMessagesResult->getQueueMessages();
	$message = $messages[0];

	// Define new message properties.
	$new_message_text = "New message text.";
	$new_visibility_timeout = 5; // Measured in seconds.

	// Get message ID and pop receipt.
	$messageId = $message->getMessageId();
	$popReceipt = $message->getPopReceipt();

	try	{
		// Update message.
		$queueRestProxy->updateMessage("myqueue",
									$messageId,
									$popReceipt,
									$new_message_text,
									$new_visibility_timeout);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/azure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## Opções adicionais para remover mensagens da fila

Há duas maneiras de personalizar a recuperação da mensagem de uma fila. Primeiro, você pode obter um lote de mensagens (até 32). Segundo, você pode definir um tempo limite de visibilidade mais longo ou mais curto, permitindo mais ou menos tempo para seu código processar totalmente cada mensagem. O seguinte exemplo de código usa o método **getMessages** para receber 16 mensagens em uma chamada. Em seguida, ele processa cada mensagem usando um loop **for**. Ele também define o tempo limite de invisibilidade de cinco minutos para cada mensagem.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Queue\Models\ListMessagesOptions;

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

	// Set list message options.
	$message_options = new ListMessagesOptions();
	$message_options->setVisibilityTimeoutInSeconds(300);
	$message_options->setNumberOfMessages(16);

	// Get messages.
	try{
		$listMessagesResult = $queueRestProxy->listMessages("myqueue",
														 $message_options);
		$messages = $listMessagesResult->getQueueMessages();

		foreach($messages as $message){

			/* ---------------------
				Process message.
			--------------------- */

			// Get message Id and pop receipt.
			$messageId = $message->getMessageId();
			$popReceipt = $message->getPopReceipt();

			// Delete message.
			$queueRestProxy->deleteMessage("myqueue", $messageId, $popReceipt);
		}
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/azure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## Obter o tamanho da fila

Você pode obter uma estimativa do número de mensagens em uma fila. O método **QueueRestProxy->getQueueMetadata** solicita que o serviço Fila retorne os metadados sobre a fila. Chamando o método **getApproximateMessageCount** no objeto retornado fornece uma contagem de quantas mensagens estão em uma fila. A contagem é aproximada apenas porque as mensagens podem ser adicionadas ou removidas depois que o serviço Fila responde à sua solicitação.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

	try	{
		// Get queue metadata.
		$queue_metadata = $queueRestProxy->getQueueMetadata("myqueue");
		$approx_msg_count = $queue_metadata->getApproximateMessageCount();
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/azure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

	echo $approx_msg_count;

## Excluir uma fila

Para excluir uma fila e todas as mensagens nela, chame o método **QueueRestProxy->deleteQueue**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create queue REST proxy.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);

	try	{
		// Delete queue.
		$queueRestProxy->deleteQueue("myqueue");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/azure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}


## Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de Filas do Azure, siga estes links para saber mais sobre tarefas de armazenamento mais complexas:

- Visite o [Blog da equipe de Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/).

Para saber mais, veja também a [Central de desenvolvedores do PHP](/develop/php/).

[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[require\_once]: http://www.php.net/manual/en/function.require-once.php
[Azure Portal]: https://portal.azure.com

<!----HONumber=AcomDC_0224_2016-->