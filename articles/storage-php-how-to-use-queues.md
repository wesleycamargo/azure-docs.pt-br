<properties title="How to use the queue service (PHP) - Azure feature guide" pageTitle="Como usar o serviço Fila (PHP) | Microsoft Azure" metaKeywords="Azure Queue Service messaging PHP" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in PHP." documentationCenter="PHP" services="storage" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Como usar o serviço Fila do PHP

Este guia mostra como executar cenários comuns usando o serviço Fila do Azure. Os exemplos são gravados usando classes do SDK do Windows para PHP. Os cenários abrangidos incluem **inserir**, **exibir**, **obter** e **excluir** mensagens da fila, bem como **criar e excluir filas**. Para obter mais informações sobre filas, consulte a seção [Próximas etapas](#NextSteps) .

##Sumário

* [O que é Armazenamento de Filas](#what-is)
* [Conceitos](#concepts)
* [Criar uma conta de armazenamento do Azure](#create-account)
* [Criar um aplicativo PHP](#create-app)
* [Configurar seu aplicativo para o service de fila](#configure-app)
* [Configurar uma conexão de armazenamento do Azure](#connection-string)
* [Como: Criar uma fila](#create-queue)
* [Como: Adicionar uma mensagem a uma fila](#add-message)
* [Como: Espiar a próxima mensagem](#peek-message)
* [Como: Remover a próxima mensagem da fila](#dequeue-message)
* [Como: Alterar o conteúdo de uma mensagem na fila](#change-message)
* [Opções adicionais para remover mensagens da fila](#additional-options)
* [Como: Obter o tamanho da fila](#get-queue-length)
* [Como: Excluir uma fila](#delete-queue)
* [Próximas etapas](#next-steps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

<h2><a id="create-account"></a>Criar uma conta de armazenamento do Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a id="create-app"></a>Criar um aplicativo PHP</h2>

O único requisito para a criação de um aplicativo PHP que acessa o serviço Fila do Azure é a referência das classes do SDK do Azure para PHP de dentro de seu código. Você pode usar as ferramentas de desenvolvimento para criar seu aplicativo, incluindo o bloco de notas.

Neste guia, você usará os recursos do serviço Fila que podem ser chamados dentro de um aplicativo PHP localmente ou no código em execução dentro de uma função Web do Azure, função de trabalho ou no site.

<h2><a id="GetClientLibrary"></a>Obter as bibliotecas de cliente do Azure</h2>

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

<h2><a id="configure-app"></a>Configurar seu aplicativo para acessar o serviço Fila</h2>

Para usar as APIs do serviço Fila do Azure, você precisa:

1. Fazer referência ao arquivo de carregador automático usando a instrução [require_once][require_once] e
2. Fazer referência a qualquer classe que você possa usar.

O exemplo a seguir mostra como incluir o arquivo de carregador automático e fazer referência à classe **ServicesBuilder**.

> [WACOM.NOTE]
> Esse exemplo (e outros exemplos deste artigo) pressupõe que você tenha instalado as Bibliotecas de Cliente do PHP para o Azure por meio do Compositor. Se você instalou as bibliotecas manualmente ou como um pacote PEAR, você precisará fazer referência ao arquivo de carregador automático `WindowsAzure.php`.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


Nos exemplos abaixo, a instrução `require_once` será mostrada sempre, mas somente as classes necessárias para executar o exemplo serão referenciadas.

<h2><a id="connection-string"></a>Configurar uma conexão de armazenamento do Azure</h2>

Para criar uma instância de um cliente de serviço Fila do Azure, você deve primeiramente ter uma cadeia de conexão válida. O formato da cadeia de conexão do serviço Fila é:

Para acessar um serviço ao vivo:

	DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Para acessar o armazenamento do emulador:

	UseDevelopmentStorage=true


Para criar qualquer cliente de serviço do Azure é necessário usar a classe **ServicesBuilder**. Você pode:

* passar a cadeia de conexão diretamente para ele ou
* usar o **CloudConfigurationManager (CCM)** para verificar várias origens externas para a cadeia de conexão:
	* por padrão, ele vem com suporte para uma origem externa - variáveis de ambiente
	* você pode adicionar novas origens ao estender a classe **ConnectionStringSource**

Para os exemplos descritos aqui, a cadeia de conexão será passada diretamente.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);


<h2><a id="create-queue"></a>Como: Criar uma fila</h2>

O objeto **QueueRestProxy** permite que você crie uma fila com o método **createQueue**. Ao criar uma fila, você pode definir opções na fila, mas fazer isso não é necessário. (O exemplo abaixo mostra como definir metadados em uma fila.)

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use o WindowsAzure\Common\ServiceException;
	use WindowsAzure\Queue\Models\CreateQueueOptions;
	
	// Criar fila proxy REST.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	// OPCIONAL: Definir metadados de fila.
	$createQueueOptions = new CreateQueueOptions();
	$createQueueOptions->addMetaData("key1", "value1");
	$createQueueOptions->addMetaData("key2", "value2");
	
	try	{
		// Criar fila.
		$queueRestProxy->createQueue("myqueue", $createQueueOptions);
	}
	catch(ServiceException $e){
		// Tratar exceções com base nos códigos de erro e mensagens.
		// Os códigos de erro e mensagem estão aqui: 
		// http://msdn.microsoft.com/pt-br/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

> [WACOM.NOTE]
> Você não deve depender de maiúsculas e minúsculas para as chaves de metadados. Todas as chaves são lidas do serviço em letras minúsculas.


<h2><a id="add-message"></a>Como: Adicionar uma mensagem a uma fila</h2>

Para adicionar uma mensagem para uma fila, use **QueueRestProxy->createMessage**. O método utiliza o nome da fila, o texto da mensagem e opções de mensagem (que são opcionais).

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use o WindowsAzure\Common\ServiceException;
	use WindowsAzure\Queue\Models\CreateMessageOptions;

	// Criar fila proxy REST.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	try	{
		// Criar a mensagem.
		$builder = novo ServicesBuilder();
		$queueRestProxy->createMessage("myqueue", "Hello World!");
	}
	catch(ServiceException $e){
		// Tratar exceções com base nos códigos de erro e mensagens.
		// Os códigos de erro e mensagem estão aqui: 
		// http://msdn.microsoft.com/pt-br/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="peek-message"></a>Como: Espiar a próxima mensagem</h2>

Você pode exibir uma mensagem (ou mensagens) na frente de uma fila sem removê-la da fila chamando **QueueRestProxy->peekMessages**. Por padrão, o método **peekMessage** retorna uma única mensagem, mas você pode alterar esse valor com o método **PeekMessagesOptions->setNumberOfMessages**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use o WindowsAzure\Common\ServiceException;
	use WindowsAzure\Queue\Models\PeekMessagesOptions;

	// Criar fila proxy REST.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	// OPCIONAL: Definir opções de mensagem de inspeção.
	$message_options = new PeekMessagesOptions();
	$message_options->setNumberOfMessages(1); // Default value is 1.
	
	try	{
		$peekMessagesResult = $queueRestProxy->peekMessages("myqueue", $message_options);
	}
	catch(ServiceException $e){
		// Tratar exceções com base nos códigos de erro e mensagens.
		// Os códigos de erro e mensagem estão aqui: 
		// http://msdn.microsoft.com/pt-br/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	$messages = $peekMessagesResult->getQueueMessages();

	// Exibir mensagens.
	$messageCount = count($messages);
	if($messageCount <= 0){
		echo "Não há nenhuma mensagem.<br />";
	}
	else {
		foreach($messages as $message)	{
			echo "Peeked message:<br />";
			echo "Id da mensagem: ".$message->getMessageId()."<br />";
			echo "Data: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
			echo "Texto da mensagem: ".$message->getMessageText()."<br /><br />";
		}
	}

<h2><a id="dequeue-message"></a>Como: Remover a próxima mensagem da fila</h2>

Seu código remove uma mensagem de uma fila em duas etapas. Primeiro, você chama **QueueRestProxy->listMessages**, que torna a mensagem invisível para qualquer outro código de leitura da fila. Por padrão, esta mensagem ficará invisível por 30 segundos (se a mensagem não for excluída neste período de tempo, ela se tornará visível na fila novamente). Para terminar de remover a mensagem da fila, você deve chamar **QueueRestProxy->deleteMessage**. Este processo de duas etapas de remover uma mensagem garante que quando o código não processa uma mensagem devido à falhas de hardware ou de software, outra instância do seu código pode receber a mesma mensagem e tentar novamente. Seu código chama **deleteMessage** logo depois que a mensagem é processada.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use o WindowsAzure\Common\ServiceException;

	// Criar fila proxy REST.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	// Obter mensagem.
	$listMessagesResult = $queueRestProxy->listMessages("myqueue");
	$messages = $listMessagesResult->getQueueMessages();
	$message = $messages[0];
	
	/* ---------------------
		Mensagem do processo.
	   --------------------- */
	
	// Obter Id e o recebimento pop da mensagem.
	$messageId = $message->getMessageId();
	$popReceipt = $message->getPopReceipt();
	
	try	{
		// Excluir a mensagem.
		$queueRestProxy->deleteMessage("myqueue", $messageId, $popReceipt);
	}
	catch(ServiceException $e){
		// Tratar exceções com base nos códigos de erro e mensagens.
		// Os códigos de erro e mensagem estão aqui: 
		// http://msdn.microsoft.com/pt-br/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="change-message"></a>Como: Alterar o conteúdo de uma mensagem na fila</h2>

Você pode alterar o conteúdo de uma mensagem no local na fila chamando **QueueRestProxy->updateMessage**. Se a mensagem representar uma tarefa de trabalho, você poderá usar esse recurso para atualizar o status da tarefa de trabalho. O código a seguir atualiza a mensagem da fila com novo conteúdo e define o tempo limite de visibilidade para estender mais 60 segundos. Isso salva o estado do trabalho associado à mensagem e dá ao cliente mais um minuto para continuar trabalhando na mensagem. Você pode usar essa técnica para acompanhar fluxos de trabalho de várias etapas em mensagens em fila, sem a necessidade de começar desde o início, caso uma etapa de processamento falhar devido a uma falha de hardware ou de software. Normalmente, você mantém uma contagem de repetições e, se a mensagem for repetida mais de n vezes, você a exclui. Isso protege contra uma mensagem que dispara um erro do aplicativo sempre que for processada.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use o WindowsAzure\Common\ServiceException;	

	// Criar fila proxy REST.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	// Obter mensagem.
	$listMessagesResult = $queueRestProxy->listMessages("myqueue");
	$messages = $listMessagesResult->getQueueMessages();
	$message = $messages[0];
	
	// Definir novas propriedades de mensagem.
	$new_message_text = "New message text.";
	$new_visibility_timeout = 5; // Medido em segundos. 
	
	// Obter Id e o recebimento pop da mensagem.
	$messageId = $message->getMessageId();
	$popReceipt = $message->getPopReceipt();
	
	try	{
		// Atualizar a mensagem.
		$queueRestProxy->updateMessage("myqueue", 
									$messageId, 
									$popReceipt, 
									$new_message_text, 
									$new_visibility_timeout);
	}
	catch(ServiceException $e){
		// Tratar exceções com base nos códigos de erro e mensagens.
		// Os códigos de erro e mensagem estão aqui: 
		// http://msdn.microsoft.com/pt-br/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="additional-options"></a>Opções adicionais para remover mensagens da fila</h2>

Há duas maneiras de personalizar a recuperação da mensagem de uma fila. Primeiro, você pode obter um lote de mensagens (até 32). Segundo, você pode definir um tempo limite de visibilidade mais longo ou mais curto, permitindo mais ou menos tempo para seu código processar totalmente cada mensagem. O seguinte exemplo de código usa o método **getMessages** para receber 16 mensagens em uma chamada. Em seguida, ele processa cada mensagem usando um loop **for**. Ele também define o tempo limite de invisibilidade de cinco minutos para cada mensagem.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use o WindowsAzure\Common\ServiceException;
	use WindowsAzure\Queue\Models\ListMessagesOptions;

	// Criar fila proxy REST.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	// Definir opções de mensagem de lista. 
	$message_options = new ListMessagesOptions();
	$message_options->setVisibilityTimeoutInSeconds(300); 
	$message_options->setNumberOfMessages(16);
	
	// Obter mensagens.
	try{
		$listMessagesResult = $queueRestProxy->listMessages("myqueue", 
														 $message_options); 
		$messages = $listMessagesResult->getQueueMessages(); 

		foreach($messages as $message){
			
			/* ---------------------
				Mensagem do processo.
			--------------------- */
		
			// Obter Id e o recebimento pop da mensagem.
			$messageId = $message->getMessageId();
			$popReceipt = $message->getPopReceipt();
			
			// Excluir a mensagem.
			$queueRestProxy->deleteMessage("myqueue", $messageId, $popReceipt);   
		}
	}
	catch(ServiceException $e){
		// Tratar exceções com base nos códigos de erro e mensagens.
		// Os códigos de erro e mensagem estão aqui: 
		// http://msdn.microsoft.com/pt-br/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="get-queue-length"></a>Como: Obter o tamanho da fila</h2>

Você pode obter uma estimativa do número de mensagens em uma fila. O método **QueueRestProxy->getQueueMetadata** solicita que o serviço Fila retorne os metadados sobre a fila. Chamando o método **getApproximateMessageCount** no objeto retornado fornece uma contagem de quantas mensagens estão em uma fila. A conta é aproximada apenas porque as mensagens podem ser adicionadas ou removidas depois que o serviço Fila responde à sua solicitação.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use o WindowsAzure\Common\ServiceException;

	// Criar fila proxy REST.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	try	{
		// Obter metadados de fila.
		$queue_metadata = $queueRestProxy->getQueueMetadata("myqueue");
		$approx_msg_count = $queue_metadata->getApproximateMessageCount();
	}
	catch(ServiceException $e){
		// Tratar exceções com base nos códigos de erro e mensagens.
		// Os códigos de erro e mensagem estão aqui: 
		// http://msdn.microsoft.com/pt-br/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	echo $approx_msg_count;

<h2><a id="delete-queue"></a>Como: Excluir uma fila</h2>

Para excluir uma fila e todas as mensagens contidas nela, chame o método **QueueRestProxy->deleteQueue**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use o WindowsAzure\Common\ServiceException;

	// Criar fila proxy REST.
	$queueRestProxy = ServicesBuilder::getInstance()->createQueueService($connectionString);
	
	try	{
		// Excluir a fila.
		$queueRestProxy->deleteQueue("myqueue");
	}
	catch(ServiceException $e){
		// Tratar exceções com base nos códigos de erro e mensagens.
		// Os códigos de erro e mensagem estão aqui: 
		// http://msdn.microsoft.com/pt-br/library/windowsazure/dd179446.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}


<h2><a id="next-steps"></a>Próximas etapas</h2>

Agora que você aprendeu os conceitos básicos do serviço Fila do Azure, siga estes links para saber como executar tarefas de armazenamento mais complexas.

- Consulte a referência de MSDN: [Armazenando e acessando dados no Azure] []
- Visite o Blog da Equipe de Armazenamento do Azure: <http://blogs.msdn.com/b/windowsazurestorage/>

[baixar]: http://go.microsoft.com/fwlink/?LinkID=252473
[require_once]: http://www.php.net/manual/en/function.require-once.php
[Portal de Gerenciamento do Azure]: http://manage.windowsazure.com/
[Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx
