#####Criar uma fila
Um objeto **CloudQueueClient** permite que você obtenha objetos de referência para filas. O código a seguir cria um objeto **CloudQueueClient**. Todos os códigos neste tópico usam uma cadeia de conexão de armazenamento armazenada na configuração dos serviços do aplicativo do Azure. Também existem outras maneiras de criar um objeto **CloudStorageAccount**. Consulte a documentação [CloudStorageAccount](http://msdn.microsoft.com/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx "CloudStorageAccount") para obter detalhes.

	// Create the queue client.
	CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Use o objeto **queueClient** para obter uma referência à fila que deseja usar. O código tenta fazer referência a uma fila denominada "myqueue". Se não localizar uma fila com esse nome, ele criará uma.

	// Get a reference to a queue named "myqueue".
	CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// If the queue isn't already there, then create it.
	queue.CreateIfNotExists();

**OBSERVAÇÃO:** Use este bloco de código antes do código nas seções a seguir.

#####Inserir uma mensagem em uma fila
Para inserir uma mensagem em uma fila existente, crie primeiramente um novo objeto **CloudQueueMessage**. Em seguida, chame o método AddMessage(). Um objeto **CloudQueueMessage** pode ser criado por meio de uma cadeia de caracteres (em formato UTF-8) ou de uma matriz de bytes. Este é o código que cria uma fila (se ela não existir) e insere a mensagem 'Hello, World'.

	// Create a message and add it to the queue.
	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
	queue.AddMessage(message);

#####Espiar a próxima mensagem
Você pode espiar a mensagem na frente de uma fila sem removê-la da fila, chamando o método PeekMessage().

	// Peek at the next message in the queue.
	CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Display the message.
	Console.WriteLine(peekedMessage.AsString);

#####Remover a próxima mensagem
Seu código pode remover uma mensagem de uma fila em duas etapas. 


1. Chame GetMessage() para obter a próxima mensagem em uma fila. As mensagens retornadas de GetMessage() tornam-se invisíveis para todas as outras mensagens de leitura de código da fila. Por padrão, essa mensagem permanece invisível por 30 segundos. 
2.	Para concluir a remoção da mensagem da fila, chame DeleteMessage(). 

Este processo de duas etapas de remover uma mensagem garante que se o código não processar uma mensagem devido a falhas de hardware ou de software, outra instância do seu código poderá receber a mesma mensagem e tentar novamente. Seu código chamará DeleteMessage() logo depois que a mensagem for processada.

	// Get the next message in the queue.
	CloudQueueMessage retrievedMessage = queue.GetMessage();

	// Process the message in less than 30 seconds, and then delete the message.
	queue.DeleteMessage(retrievedMessage);

[Saiba mais sobre o armazenamento do Azure](http://azure.microsoft.com/documentation/services/storage/)
Consulte também [Procurando recursos de armazenamento no Gerenciador de servidores](http://msdn.microsoft.com/library/azure/ff683677.aspx).

<!--HONumber=42-->
