<properties 
	pageTitle="Como usar o Armazenamento de Fila do Java | Microsoft Azure" 
	description="Saiba como usar o serviço Fila do Azure para criar e excluir filas, bem como para inserir, obter e excluir mensagens. Amostras escritas em Java." 
	services="storage" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="robmcm"/>

# Como usar o Armazenamento de Fila no Java

[AZURE.INCLUDE [armazenamento-seletor-fila-include](../../includes/storage-selector-queue-include.md)]

## Visão geral

Este guia lhe mostrará como executar cenários comuns usando o serviço de Armazenamento de Fila do Azure. As amostras são escritas em Java e usam o [SDK de Armazenamento do Azure para Java][]. Os cenários abrangidos incluem **inserir**, **espiar**, **obter** e **excluir** mensagens da fila, bem como **criar** e **excluir** filas. Para obter mais informações sobre filas, consulte a seção [Próximas etapas](#NextSteps).

Observação: um SDK está disponível para os desenvolvedores que usam o Armazenamento do Azure em dispositivos Android. Para obter mais informações, consulte [SDK de Armazenamento do Azure para Android][].

[AZURE.INCLUDE [armazenamento-fila-conceitos-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Criar um aplicativo Java

Neste guia, você usará os recursos de armazenamento que podem ser executados em um aplicativo Java localmente ou no código em execução em uma função web ou de trabalho do Azure.

Para isso, você vai precisar instalar o JDK (Java Development Kit) e criar uma conta de armazenamento do Azure na sua assinatura do Azure. Depois disso, você terá de verificar se o seu sistema de desenvolvimento atende aos requisitos mínimos e às dependências que estão listadas no repositório [SDK de Armazenamento do Azure para Java][] no GitHub. Se o seu sistema atender a esses requisitos, você poderá seguir as instruções para baixar e instalar as Bibliotecas de Armazenamento do Azure para Java em seu sistema por meio desse repositório. Depois de concluir essas tarefas, você poderá criar um aplicativo Java que usa os exemplos neste artigo.

## Configurar seu aplicativo para acessar o armazenamento de filas

Adicione as seguintes instruções de importação na parte superior do arquivo Java em que deseja usar as APIs de armazenamento do Azure para acessar as filas:

    // Include the following imports to use queue APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.queue.*;

## Configurar uma cadeia de conexão de armazenamento do Azure

Um cliente de armazenamento do Azure usa uma cadeia de conexão para armazenar pontos de extremidade e credenciais para acessar serviços de gerenciamento de dados. Ao ser executado em um aplicativo cliente, é necessário fornecer a cadeia de conexão de armazenamento no formato a seguir, usando o nome da sua conta de armazenamento e a chave de Acesso primário da conta de armazenamento listada no Portal de Gerenciamento para os valores *AccountName* e *AccountKey*. Este exemplo mostra como você pode declarar um campo estático para armazenar a cadeia de conexão:

    // Define the connection-string with your values.
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

Em um aplicativo que esteja sendo executado em uma função no Microsoft Azure, essa cadeia pode ser armazenada no arquivo de configuração do serviço, *ServiceConfiguration.cscfg*, podendo ser acessada com uma chamada para o método **RoleEnvironment.getConfigurationSettings**. Segue um exemplo de como obter a cadeia de conexão de um elemento **Setting** denominado *StorageConnectionString* no arquivo de configuração de serviço:

    // Retrieve storage account from connection-string.
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

Os exemplos abaixo pressupõem que você usou um desses dois métodos para obter a cadeia de conexão do armazenamento.

## Como criar uma fila

Um objeto **CloudQueueClient** permite que você obtenha objetos de referência para filas. O código a seguir cria um objeto **CloudQueueClient**. (Observação: existem outras maneiras de criar objetos **CloudStorageAccount**. Para obter mais informações, consulte **CloudStorageAccount** na [Referência de SDK do cliente de armazenamento do Azure].)

Use o objeto **CloudQueueClient** para obter uma referência à fila que deseja usar. Você poderá criar a fila se ela não existir.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount = 
	       CloudStorageAccount.parse(storageConnectionString);

	   // Create the queue client.
	   CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

	   // Retrieve a reference to a queue.
	   CloudQueue queue = queueClient.getQueueReference("myqueue");

	   // Create the queue if it doesn't already exist.
	   queue.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Como adicionar uma mensagem a uma fila

Para inserir uma mensagem em uma fila existente, primeiro crie uma nova **CloudQueueMessage**. Em seguida, chame o método **addMessage**. Um **CloudQueueMessage** pode ser criado por meio de uma cadeia de caracteres (em formato UTF-8) ou de uma matriz de bytes. Este é o código que cria uma fila (se ela não existir) e insere a mensagem 'Hello, World'.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount = 
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

    	// Create the queue if it doesn't already exist.
    	queue.createIfNotExists();

    	// Create a message and add it to the queue.
    	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    	queue.addMessage(message);
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Como inspecionar a próxima mensagem

Você pode inspecionar a mensagem na frente de uma fila sem removê-la da fila chamando **peekMessages**.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount = 
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");
			
    	// Peek at the next message.
    	CloudQueueMessage peekedMessage = queue.peekMessage();
			
    	// Output the message value.
    	if (peekedMessage != null)
    	{
		  System.out.println(peekedMessage.getMessageContentAsString());
	   }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Como alterar o conteúdo de uma mensagem em fila

Você pode alterar o conteúdo de uma mensagem in-loco na fila. Se a mensagem representar uma tarefa de trabalho, você poderá usar esse recurso para atualizar o status da tarefa de trabalho. O código a seguir atualiza a mensagem da fila com novo conteúdo e define o tempo limite de visibilidade para estender mais 60 segundos. Isso salva o estado do trabalho associado à mensagem e dá ao cliente mais um minuto para continuar trabalhando na mensagem. Você pode usar essa técnica para acompanhar fluxos de trabalho de várias etapas em mensagens em fila, sem a necessidade de começar desde o início, caso uma etapa de processamento falhar devido a uma falha de hardware ou de software. Normalmente, você mantém uma contagem de repetições e, se a mensagem for repetida mais de *n* vezes, você a exclui. Isso protege contra uma mensagem que dispara um erro do aplicativo sempre que for processada.

O exemplo de código a seguir pesquisa através da fila de mensagens, localiza a primeira mensagem que corresponde a "Hello, World” do conteúdo, em seguida, modifica o conteúdo da mensagem e sai.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

        // The maximum number of messages that can be retrieved is 32. 
        final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

        // Loop through the messages in the queue.
        for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
        {
            // Check for a specific string.
            if (message.getMessageContentAsString().equals("Hello, World"))
            {
                // Modify the content of the first matching message.
                message.setMessageContent("Updated contents.");
                // Set it to be visible in 30 seconds.
                EnumSet<MessageUpdateFields> updateFields = 
                    EnumSet.of(MessageUpdateFields.CONTENT,
                    MessageUpdateFields.VISIBILITY);
                // Update the message.
                queue.updateMessage(message, 30, updateFields, null, null);
                break;
            }
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

Como alternativa, o seguinte exemplo de código atualiza apenas a primeira mensagem visível na fila.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount = 
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

    	// Retrieve the first visible message in the queue.
    	CloudQueueMessage message = queue.retrieveMessage();
			
    	if (message != null)
    	{
            // Modify the message content.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 60 seconds.
            EnumSet<MessageUpdateFields> updateFields = 
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 60, updateFields, null, null);
    	}
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Como obter o comprimento da fila

Você pode obter uma estimativa do número de mensagens em uma fila. O método **downloadAttributes** solicita vários valores atuais ao serviço Fila, incluindo uma contagem de quantas mensagens estão em uma fila. A contagem é aproximada apenas porque as mensagens podem ser adicionadas ou removidas depois que o serviço Fila responde à sua solicitação. O método **getApproximateMessageCount** retorna o último valor recuperado pela chamada a **downloadAttributes** sem chamar o serviço Fila.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount = 
	       CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

	   // Download the approximate message count from the server.
    	queue.downloadAttributes();

    	// Retrieve the newly cached approximate message count.
    	long cachedMessageCount = queue.getApproximateMessageCount();
			
    	// Display the queue length.
    	System.out.println(String.format("Queue length: %d", cachedMessageCount));
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Como remover a próxima mensagem da fila

Seu código remove uma mensagem de um fila em duas etapas. Quando você chama **retrieveMessage**, obtém a próxima mensagem em uma fila. As mensagens retornadas de **retrieveMessage** tornam-se invisíveis para todas as outras mensagens de leitura de código da fila. Por padrão, essa mensagem permanece invisível por 30 segundos. Para terminar de remover a mensagem da fila, você também deve chamar **deleteMessage**. Este processo de duas etapas de remover uma mensagem garante que quando o código não processa uma mensagem devido à falhas de hardware ou de software, outra instância do seu código pode receber a mesma mensagem e tentar novamente. Seu código chama **deleteMessage** logo depois que a mensagem é processada.

    try
    {
    	// Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount = 
    	    CloudStorageAccount.parse(storageConnectionString);

    	// Create the queue client.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Retrieve a reference to a queue.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

    	// Retrieve the first visible message in the queue.
    	CloudQueueMessage retrievedMessage = queue.retrieveMessage();
			
    	if (retrievedMessage != null)
    	{
    		// Process the message in less than 30 seconds, and then delete the message.
    		queue.deleteMessage(retrievedMessage);
    	}
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }


## Opções adicionais para remover mensagens da fila

Há duas maneiras de personalizar a recuperação da mensagem de uma fila. Primeiro, você pode obter um lote de mensagens (até 32). Segundo, você pode definir um tempo limite de invisibilidade mais longo ou mais curto, permitindo mais ou menos tempo para seu código processar totalmente cada mensagem.

O exemplo de código a seguir usa o método **retrieveMessages** para obter 20 mensagens em uma chamada. Em seguida, ele processa cada mensagem usando um loop **for**. Ele também define o tempo limite de invisibilidade como cinco minutos (300 segundos) para cada mensagem. Observe que os cinco minutos começam para todas as mensagens ao mesmo tempo; portanto, depois de cinco minutos desde a chamada para **retrieveMessages**, todas as mensagens que não tenham sido excluídas se tornarão visíveis novamente.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
        for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
            // Do processing for all messages in less than 5 minutes, 
            // deleting each message after processing.
            queue.deleteMessage(message);
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Como: listar as filas

Para obter uma lista das filas atuais, chame o método **CloudQueueClient.listQueues()**, que retornará uma coleção de objetos **CloudQueue**.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient =
            storageAccount.createCloudQueueClient();

        // Loop through the collection of queues.
        for (CloudQueue queue : queueClient.listQueues())
        {
            // Output each queue name.
            System.out.println(queue.getName());
        }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Como excluir uma fila

Para excluir uma fila e todas as mensagens contidas nela, chame o método **deleteIfExists** no objeto **CloudQueue**.

    try
    {
        // Retrieve storage account from connection-string.
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.parse(storageConnectionString);

        // Create the queue client.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Retrieve a reference to a queue.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Delete the queue if it exists.
        queue.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de fila, siga estes links para saber mais sobre tarefas de armazenamento mais complexas.

- [SDK de Armazenamento do Azure para Java]
- [Referência de SDK do Cliente de Armazenamento do Azure]
- [API REST de Armazenamento do Azure]
- [Blog da equipe de Armazenamento do Azure]

[Azure SDK for Java]: http://azure.microsoft.com/develop/java/
[SDK de Armazenamento do Azure para Java]: https://github.com/azure/azure-storage-java
[SDK de Armazenamento do Azure para Android]: https://github.com/azure/azure-storage-android
[Referência de SDK do cliente de armazenamento do Azure]: http://dl.windowsazure.com/storage/javadoc/
[API REST de Armazenamento do Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Blog da equipe de Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
 

<!---HONumber=July15_HO2-->