<properties urlDisplayName="Queue Service" pageTitle="Como usar o serviço Fila (Java) | Microsoft Azure" metaKeywords="Azure Queue Service, Azure Queue storage service, queues peeking, queues insert messages, queues get messages, queues delete messages, create queues, delete queues, Queue service Java" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in Java." metaCanonical="" services="storage" documentationCenter="Java" title="How to use the Queue storage service from Java" authors="tamram" solutions="" manager="adinah" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Como usar o Armazenamento de Fila no Java

Este guia irá lhe mostrar como executar cenários comuns usando o armazenamento de fila do Azure. Os exemplos são escritos em Java e usam o [SDK de Armazenamento do Azure para Java][]. Os cenários abrangidos incluem **inserir**, **espiar**, **obter** e **excluir** mensagens da fila, bem como **criar** e **excluir** filas. Para obter mais informações sobre filas, consulte a seção [Próximas etapas](#NextSteps) .

Observação: Um SDK está disponível para os desenvolvedores que usam o Armazenamento do Azure em dispositivos Android. Para obter mais informações, consulte [SDK de Armazenamento do Azure para Android][]. 

## <a name="Contents"> </a>Sumário

* [O que é Armazenamento de Filas](#what-is)
* [Conceitos](#Concepts)
* [Criar uma conta de armazenamento do Azure](#CreateAccount)
* [Criar um aplicativo do Java](#CreateApplication)
* [Configurar seu aplicativo para acessar o armazenamento de filas](#ConfigureStorage)
* [Configurar uma cadeia de conexão de armazenamento do Azure](#ConnectionString)
* [Como: Criar uma fila](#create-queue)
* [Como: Adicionar uma mensagem a uma fila](#add-message)
* [Como: Espiar a próxima mensagem](#peek-message)
* [Como: Alterar o conteúdo de uma mensagem na fila](#change-message)
* [Como: Obter o tamanho da fila](#get-queue-length)
* [Como: Remover a próxima mensagem da fila](#dequeue-message)
* [Opções adicionais para remover mensagens da fila](#additional-options)
* [Como: Listar filas](#list-queues)
* [Como: Excluir uma fila](#delete-queue)
* [Próximas etapas](#NextSteps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

<h2><a id="CreateAccount"></a>Criar uma conta de armazenamento do Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="CreateApplication"> </a>Criar um aplicativo do Java

Neste guia, você usará os recursos de armazenamento que podem ser executados em um aplicativo Java localmente ou no código em execução em uma função web ou de trabalho do Azure.

Para isso, você vai precisar instalar o JDK (Java Development Kit) e criar uma conta de armazenamento do Azure na sua assinatura do Azure. Assim que tiver feito isso, você precisará verificar se o sistema de desenvolvimento atende aos requisitos mínimos e às dependências que estão listadas no repositório [SDK de Armazenamento do Azure para Java][] no GitHub. Se o seu sistema atender a esses requisitos, você poderá seguir as instruções para baixar e instalar as Bibliotecas de Armazenamento do Azure para Java em seu sistema por meio desse repositório. Depois de concluir essas tarefas, você poderá criar um aplicativo Java que usa os exemplos neste artigo.

## <a name="ConfigureStorage"> </a>Configurar seu aplicativo para acessar o armazenamento de filas

Adicione as seguintes instruções de importação na parte superior do arquivo Java em que deseja usar as APIs de armazenamento do Azure para acessar as filas:

    // Inclua a instrução imports a seguir para usar APIs da fila.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.queue.*;

## <a name="ConnectionString"> </a>Configurar uma cadeia de conexão de armazenamento do Azure

Um cliente de armazenamento do Azure usa uma cadeia de conexão para armazenar pontos de extremidade e credenciais para acessar serviços de gerenciamento de dados. Ao ser executado em um aplicativo cliente, é necessário fornecer a cadeia de conexão de armazenamento no formato a seguir, usando o nome da sua conta de armazenamento e a chave de Acesso primário da conta de armazenamento listada no Portal de Gerenciamento para os valores *AccountName* e *AccountKey*. Este exemplo mostra como você pode declarar um campo estático para armazenar a cadeia de conexão:

    // Definir a cadeia de conexão com seus valores.
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

Em um aplicativo que esteja sendo executado em uma função no Microsoft Azure, essa cadeia pode ser armazenada no arquivo de configuração do serviço, *ServiceConfiguration.cscfg*, podendo ser acessada com uma chamada para o método **RoleEnvironment.getConfigurationSettings**. Segue um exemplo de como obter a cadeia de conexão de um elemento **Setting** denominado *StorageConnectionString* no arquivo de configuração de serviço:

    // Recuperar a conta de armazenamento da cadeia de conexão.
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

Os exemplos abaixo pressupõem que você usou um desses dois métodos para obter a cadeia de conexão do armazenamento.

## <a name="create-queue"> </a>Como: Criar uma fila

Um objeto **CloudQueueClient** permite que você obtenha objetos de referência para filas. O código a seguir cria um objeto **CloudQueueClient**. (Observação: Existem outras maneiras de criar objetos **CloudStorageAccount**; Para obter mais informações, consulte **CloudStorageAccount** na [Referência de SDK do Cliente de Armazenamento do Azure].)

Use o objeto **CloudQueueClient** para obter uma referência à fila que deseja usar. Você poderá criar a fila se ela não existir.

    try
    {
    	// Recuperar a conta de armazenamento da cadeia de conexão.
    	CloudStorageAccount storageAccount = 
	       CloudStorageAccount.parse(storageConnectionString);

	   // Criar o cliente de fila.
	   CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

	   // Recuperar uma referência a uma fila.
	   CloudQueue queue = queueClient.getQueueReference("myqueue");

	   // Criar a fila se ela ainda não existir.
	   queue.createIfNotExists();
    }
    catch (Exception e)
    {
        // Saída do rastreamento de pilha.
        e.printStackTrace();
    }

## <a name="add-message"> </a>Como: Adicionar uma mensagem a uma fila

Para inserir uma mensagem em uma fila existente, primeiro crie uma nova **CloudQueueMessage**. Em seguida, chame o método **addMessage**. Um **CloudQueueMessage** pode ser criado por meio de uma cadeia de caracteres (em formato UTF-8) ou de uma matriz de bytes. Este é o código que cria uma fila (se ela não existir) e insere a mensagem 'Hello, World'.

    try
    {
    	// Recuperar a conta de armazenamento da cadeia de conexão.
    	CloudStorageAccount storageAccount = 
	       CloudStorageAccount.parse(storageConnectionString);

    	// Criar o cliente de fila.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Recuperar uma referência a uma fila.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

    	// Criar a fila se ela ainda não existir.
    	queue.createIfNotExists();

    	// Criar uma mensagem e adicioná-la à fila.
    	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    	queue.addMessage(message);
    }
    catch (Exception e)
    {
        // Saída do rastreamento de pilha.
        e.printStackTrace();
    }

## <a name="peek-message"> </a>Como: Espiar a próxima mensagem

Você pode inspecionar a mensagem na frente de uma fila sem removê-la da fila chamando **peekMessages**.

    try
    {
    	// Recuperar a conta de armazenamento da cadeia de conexão.
    	CloudStorageAccount storageAccount = 
	       CloudStorageAccount.parse(storageConnectionString);

    	// Criar o cliente de fila.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Recuperar uma referência a uma fila.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");
			
    	// Inspecionar a próxima mensagem.
    	CloudQueueMessage peekedMessage = queue.peekMessage();
			
    	// O valor da mensagem de saída.
    	if (peekedMessage != null)
    	{
		  System.out.println(peekedMessage.getMessageContentAsString());
	   }
    }
    catch (Exception e)
    {
        // Saída do rastreamento de pilha.
        e.printStackTrace();
    }

## <a name="change-message"> </a>Como: Alterar o conteúdo de uma mensagem na fila

Você pode alterar o conteúdo de uma mensagem in-loco na fila. Se a mensagem representar uma tarefa de trabalho, você poderá usar esse recurso para atualizar o status da tarefa de trabalho. O código a seguir atualiza a mensagem da fila com novo conteúdo e define o tempo limite de visibilidade para estender mais 60 segundos. Isso salva o estado do trabalho associado à mensagem e dá ao cliente mais um minuto para continuar trabalhando na mensagem. Você pode usar essa técnica para acompanhar fluxos de trabalho de várias etapas em mensagens em fila, sem a necessidade de começar desde o início, caso uma etapa de processamento falhar devido a uma falha de hardware ou de software. Normalmente, você mantém uma contagem de repetições e, se a mensagem for repetida mais de *n* vezes, você a exclui. Isso protege contra uma mensagem que dispara um erro do aplicativo sempre que for processada.

O exemplo de código a seguir pesquisa através da fila de mensagens, localiza a primeira mensagem que corresponde a "Hello, World" do conteúdo, em seguida, modifica o conteúdo da mensagem e sai. 

    try
    {
        // Recuperar a conta de armazenamento da cadeia de conexão.
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.parse(storageConnectionString);

    	// Criar o cliente de fila.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Recuperar uma referência a uma fila.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

        // O número máximo de mensagens que podem ser recuperadas é 32. 
        final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

        // Um loop de mensagens na fila.
        for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
        {
            // Verifique se há uma cadeia de caracteres específica.
            if (message.getMessageContentAsString().equals("Hello, World"))
            {
                // Modifica o conteúdo da primeira mensagem correspondente.
                message.setMessageContent("Updated contents.");
                // Defini-lo para ser visível em 30 segundos.
                EnumSet<MessageUpdateFields> updateFields = 
                    EnumSet.of(MessageUpdateFields.CONTENT,
                    MessageUpdateFields.VISIBILITY);
                // Atualize a mensagem.
                queue.updateMessage(message, 30, updateFields, null, null);
                break;
            }
        }
    }
    catch (Exception e)
    {
        // Saída do rastreamento de pilha.
        e.printStackTrace();
    }

Como alternativa, o seguinte exemplo de código atualiza apenas a primeira mensagem visível na fila.

    try
    {
    	// Recuperar a conta de armazenamento da cadeia de conexão.
    	CloudStorageAccount storageAccount = 
	       CloudStorageAccount.parse(storageConnectionString);

    	// Criar o cliente de fila.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Recuperar uma referência a uma fila.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

    	// Recupere a primeira mensagem visível na fila.
    	CloudQueueMessage message = queue.retrieveMessage();
			
    	if (message != null)
    	{
            // Modifica o conteúdo da mensagem.
            message.setMessageContent("Updated contents.");
            // Defini-lo para ser visível em 60 segundos.
            EnumSet<MessageUpdateFields> updateFields = 
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Atualize a mensagem.
            queue.updateMessage(message, 60, updateFields, null, null);
    	}
    }
    catch (Exception e)
    {
        // Saída do rastreamento de pilha.
        e.printStackTrace();
    }

## <a name="get-queue-length"> </a>Como: Obter o tamanho da fila

Você pode obter uma estimativa do número de mensagens em uma fila. O método **downloadAttributes** solicita vários valores atuais ao serviço Fila, incluindo uma contagem de quantas mensagens estão em uma fila. A contagem é aproximada apenas porque as mensagens podem ser adicionadas ou removidas depois que o serviço Fila responde à sua solicitação. O método **getApproximateMessageCount** retorna o último valor recuperado pela chamada a **downloadAttributes** sem chamar o serviço Fila.

    try
    {
    	// Recuperar a conta de armazenamento da cadeia de conexão.
    	CloudStorageAccount storageAccount = 
	       CloudStorageAccount.parse(storageConnectionString);

    	// Criar o cliente de fila.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Recuperar uma referência a uma fila.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

	   // Baixe a contagem aproximada das mensagens do servidor.
    	queue.downloadAttributes();

    	// Recuperar a contagem aproximada das mensagens recém-armazenadas em cache.
    	long cachedMessageCount = queue.getApproximateMessageCount();
			
    	// Exibe o comprimento da fila.
    	System.out.println(String.format("Queue length: %d", cachedMessageCount));
    }
    catch (Exception e)
    {
        // Saída do rastreamento de pilha.
        e.printStackTrace();
    }

## <a name="dequeue-message"> </a>Como: Remover a próxima mensagem da fila

Seu código remove uma mensagem de um fila em duas etapas. Quando você chama **retrieveMessage**, obtém a próxima mensagem em uma fila. As mensagens retornadas de **retrieveMessage** tornam-se invisíveis para todas as outras mensagens de leitura de código da fila. Por padrão, essa mensagem permanece invisível por 30 segundos. Para terminar de remover a mensagem da fila, você também deve chamar **deleteMessage**. Este processo de duas etapas de remover uma mensagem garante que quando o código não processa uma mensagem devido à falhas de hardware ou de software, outra instância do seu código pode receber a mesma mensagem e tentar novamente. Seu código chama **deleteMessage** logo depois que a mensagem é processada.

    try
    {
    	// Recuperar a conta de armazenamento da cadeia de conexão.
    	CloudStorageAccount storageAccount = 
    	    CloudStorageAccount.parse(storageConnectionString);

    	// Criar o cliente de fila.
    	CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    	// Recuperar uma referência a uma fila.
    	CloudQueue queue = queueClient.getQueueReference("myqueue");

    	// Recupere a primeira mensagem visível na fila.
    	CloudQueueMessage retrievedMessage = queue.retrieveMessage();
			
    	if (retrievedMessage != null)
    	{
    		// Processar a mensagem em menos de 30 segundos e, em seguida, excluir a mensagem.
    		queue.deleteMessage(retrievedMessage);
    	}
    }
    catch (Exception e)
    {
        // Saída do rastreamento de pilha.
        e.printStackTrace();
    }


## <a name="additional-options"> </a>Opções adicionais para remover mensagens da fila

Há duas maneiras de personalizar a recuperação da mensagem de uma fila. Primeiro, você pode obter um lote de mensagens (até 32). Segundo, você pode definir um tempo limite de invisibilidade mais longo ou mais curto, permitindo mais ou menos tempo para seu código processar totalmente cada mensagem.

O exemplo de código a seguir usa o método **retrieveMessages** para obter 20 mensagens em uma chamada. Em seguida, ele processa cada mensagem usando um loop **for**. Ele também define o tempo limite de invisibilidade como cinco minutos (300 segundos) para cada mensagem. Observe que os cinco minutos começam para todas as mensagens ao mesmo tempo; portanto, depois de cinco minutos desde a chamada para **retrieveMessages**, todas as mensagens que não tenham sido excluídas se tornarão visíveis novamente.

    try
    {
        // Recuperar a conta de armazenamento da cadeia de conexão.
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.parse(storageConnectionString);

        // Criar o cliente de fila.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Recuperar uma referência a uma fila.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Recupere 20 mensagens da fila com um limite de visibilidade de 300 segundos.
        for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
            // Fazer o processamento de todas as mensagens em menos de 5 minutos. 
            // excluindo cada mensagem após o processamento.
            queue.deleteMessage(message);
        }
    }
    catch (Exception e)
    {
        // Saída do rastreamento de pilha.
        e.printStackTrace();
    }

## <a name="list-queues"> </a>Como: Listar filas

Para obter uma lista das filas atuais, chame o método **CloudQueueClient.listQueues()**, que retornará uma coleção de objetos **CloudQueue**. 

    try
    {
        // Recuperar a conta de armazenamento da cadeia de conexão.
        CloudStorageAccount storageAccount =
            CloudStorageAccount.parse(storageConnectionString);

        // Criar o cliente de fila.
        CloudQueueClient queueClient =
            storageAccount.createCloudQueueClient();

        // Um loop através da coleção de filas.
        for (CloudQueue queue : queueClient.listQueues())
        {
            // Gerar saída de cada nome de fila.
            System.out.println(queue.getName());
        }
    }
    catch (Exception e)
    {
        // Saída do rastreamento de pilha.
        e.printStackTrace();
    }

## <a name="delete-queue"> </a>Como: Excluir uma fila

Para excluir uma fila e todas as mensagens contidas nela, chame o método **deleteIfExists** no objeto **CloudQueue**.

    try
    {
        // Recuperar a conta de armazenamento da cadeia de conexão.
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.parse(storageConnectionString);

        // Criar o cliente de fila.
        CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

        // Recuperar uma referência a uma fila.
        CloudQueue queue = queueClient.getQueueReference("myqueue");

        // Excluir a fila, se ela existir.
        queue.deleteIfExists();
    }
    catch (Exception e)
    {
        // Saída do rastreamento de pilha.
        e.printStackTrace();
    }

## <a name="NextSteps"> </a>Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de filas, siga estes links para saber como fazer tarefas mais complexas de armazenamento.

- [SDK de Armazenamento do Azure para Java]
- [Referência de SDK do Cliente de Armazenamento do Azure]
- [API REST de Armazenamento do Azure]
- [Blog da equipe do Armazenamento do Azure]

[SDK do Azure para Java]: http://www.windowsazure.com/pt-br/develop/java/
[SDK de Armazenamento do Azure para Java]: https://github.com/azure/azure-storage-java
[SDK de Armazenamento do Azure para Android]: https://github.com/azure/azure-storage-android
[Referência de SDK do Cliente de Armazenamento do Azure]: http://dl.windowsazure.com/storage/javadoc/
[API REST de Armazenamento do Azure]: http://msdn.microsoft.com/pt-br/library/azure/gg433040.aspx
[Blog da equipe do Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
