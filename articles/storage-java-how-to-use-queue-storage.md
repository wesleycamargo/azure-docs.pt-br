<properties linkid="dev-net-how-to-use-queue-storage-service-java" urlDisplayName="Serviço Fila" pageTitle="Como usar o serviço de fila (Java) | Microsoft Azure" metaKeywords="Serviço Fila do Azure, serviço de armazenamento de filas do Azure, inspeção de filas, mensagens de inserção de filas, mensagens de obtenção de filas, mensagens de exclusão de filas, criar filas, excluir filas, serviço Fila para Java" description="Saiba como usar o serviço Fila do Azure para criar e excluir filas e inserir, obter e excluir mensagens. Exemplos escritos em Java." metaCanonical="" services="storage" documentationCenter="Java" title="Como usar o serviço de armazenamento Fila com Java" authors="" solutions="" manager="" editor="" />





# Como usar o serviço de armazenamento Fila com Java

Este guia mostrará a você como executar cenários comuns usando o
serviço de armazenamento Fila do Azure. As amostras são escritas em Java e usam o [SDK do Azure para Java][]. Os cenários abordados incluem a
inserção, inspeção, obtenção e exclusão de mensagens da fila, bem como,
a criação e a exclusão de filas. Para obter mais informações sobre filas, consulte
a seção [Próximas etapas](#NextSteps).

## <a name="Contents"> </a>Sumário

* [O que é Armazenamento de Filas](#what-is)
* [Conceitos](#Concepts)
* [Criar uma conta de armazenamento do Azure](#CreateAccount)
* [Criar um aplicativo do Java](#CreateApplication)
* [Configurar seu aplicativo para acessar o armazenamento de filas](#ConfigureStorage)
* [Configurar uma cadeia de conexão de armazenamento do Azure](#ConnectionString)
* [Como criar uma fila](#create-queue)
* [Como adicionar uma mensagem a uma fila](#add-message)
* [Como inspecionar a próxima mensagem](#peek-message)
* [Como remover a próxima mensagem da fila](#dequeue-message)
* [Como alterar o conteúdo de uma mensagem em fila](#change-message)
* [Opções adicionais para remover mensagens da fila](#additional-options)
* [Como obter o comprimento da fila](#get-queue-length)
* [Como excluir uma fila](#delete-queue)
* [Próximas etapas](#NextSteps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

<h2><a id="CreateAccount"></a>Criar uma conta de armazenamento do Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="CreateApplication"> </a>Criar um aplicativo do Java

Neste guia, você usará os recursos de armazenamento que podem ser executados em um aplicativo do Java localmente ou no código em execução em uma função web ou função de trabalho do Azure. Supomos que você baixou e instalou o Java Development Kit (JDK), seguiu as instruções em [Baixe o SDK do Azure para Java][SDK do Azure para Java] para instalar as Bibliotecas do Azure para Java e o SDK do Azure e criou uma conta de armazenamento do Azure em sua assinatura do Azure. É possível usar as ferramentas de desenvolvimento para criar seu
aplicativo, incluindo o Bloco de Notas. Tudo o que você precisa é a capacidade de compilar um
projeto do Java e fazer referência às bibliotecas do Azure para o Java.

## <a name="ConfigureStorage"> </a>Configurar seu aplicativo para acessar o armazenamento de filas

Adicione as seguintes instruções de importação na parte superior do arquivo Java em que
deseja usar as APIs de armazenamento do Azure para acessar as filas:

    // Include the following imports to use queue APIs
    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.queue.client.*;

## <a name="ConnectionString"> </a>Configurar uma cadeia de conexão de armazenamento do Azure

Um cliente de armazenamento do Azure usa uma cadeia de conexão para armazenar
pontos de extremidade e credenciais para acessar serviços de gerenciamento de dados. Ao ser executado
em um aplicativo cliente, é necessário fornecer a cadeia de conexão de armazenamento
no formato a seguir, usando o nome da sua conta de armazenamento e a
Chave de acesso primária da conta de armazenamento listada no Portal
de Gerenciamento para os valores *AccountName* e *AccountKey*. Este exemplo mostra
como declarar um campo estático para armazenar a cadeia de conexão:

    // Define the connection-string with your values
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

Em um aplicativo que esteja sendo executado em uma função no Azure, essa cadeia pode ser armazenada no arquivo de configuração do serviço, ServiceConfiguration.cscfg, e pode ser acessada com uma chamada para o método RoleEnvironment.getConfigurationSettings. Este é um exemplo de como obter a cadeia de conexão a partir de um elemento **Setting** chamado
*StorageConnectionString* no arquivo de configuração do serviço:

    // Retrieve storage account from connection-string
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

## <a name="create-queue"> </a>Como criar uma fila

Um objeto CloudQueueClient permite que você obtenha objetos de referência para filas. O código a seguir cria um objeto CloudQueueClient.

Todos os códigos neste guia usam uma cadeia de conexão de armazenamento declarada como uma das duas maneiras mostradas acima. Também existem outras maneiras de criar objetos CloudStorageAccount. Consulte a documentação de Javadocs sobre CloudStorageAccount para obter detalhes.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

Use o objeto CloudQueueClient para obter uma referência à fila que deseja usar. Você poderá criar a fila se ela não existir.

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.createIfNotExist();

## <a name="add-message"> </a>Como adicionar uma mensagem a uma fila

Para inserir uma mensagem em uma fila existente, primeiro crie um novo CloudQueueMessage. Em seguida, chame o método addMessage. Um CloudQueueMessage pode ser criado a partir de uma cadeia de caracteres (em formato UTF-8) ou de uma matriz de bytes.
Este é o código que cria uma fila (se ela não existir) e insere a mensagem "Hello, World".

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.createIfNotExist();

    // Create a message and add it to the queue
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);

## <a name="peek-message"> </a>Como inspecionar a próxima mensagem

Você pode inspecionar a mensagem na frente de uma fila sem removê-la da fila chamando o método peekMessage.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.peekMessage();

## <a name="dequeue-message"> </a>Como remover a próxima mensagem da fila

Seu código remove uma mensagem de um fila em duas etapas. Quando chama retrieveMessage, você obtém a próxima mensagem em uma fila. A mensagem retornada de retrieveMessage se torna invisível para todas as outras mensagens de leitura de código da fila. Por padrão, essa mensagem permanece invisível por 30 segundos. Para concluir a remoção da mensagem da fila, você também deve chamar deleteMessage. Esse processo de duas etapas de remoção de uma mensagem garante que, se o seu código não processar uma mensagem devido a falhas de hardware ou software, outra instância de seu código poderá receber a mesma mensagem e tentar novamente. Seu código chama deleteMessage logo depois que a mensagem é processada.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    // Process the message in less than 30 seconds, and then delete the message.
    queue.deleteMessage(retrievedMessage);

## <a name="change-message"> </a>Como alterar o conteúdo de uma mensagem em fila

Você pode alterar o conteúdo de uma mensagem in-loco na fila. Se a mensagem representar uma tarefa de trabalho, você poderá usar esse recurso para atualizar o status da tarefa de trabalho. O código a seguir atualiza a mensagem da fila com novo conteúdo e define o tempo limite de visibilidade para estender mais 60 segundos. Isso salva o estado do trabalho associado à mensagem e dá ao cliente mais um minuto para continuar trabalhando na mensagem. Você pode usar essa técnica para acompanhar fluxos de trabalho de várias etapas em mensagens em fila, sem a necessidade de começar desde o início, caso uma etapa de processamento falhar devido a uma falha de hardware ou de software. Normalmente, você mantém uma contagem de repetições e, se a mensagem for repetida mais de n vezes, você a exclui. Isso protege contra uma mensagem que dispara um erro do aplicativo sempre que é processada.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue
    CloudQueueMessage message = queue.retrieveMessage();

    // Modify the message content and set it to be visible in 60 seconds
    message.setMessageContent("Updated contents.");
    EnumSet<MessageUpdateFields> updateFields = 
        EnumSet.of(MessageUpdateFields.CONTENT, MessageUpdateFields.VISIBILITY);
    queue.updateMessage(message, 60, updateFields, null, null);

## <a name="additional-options"> </a>Opções adicionais para remover mensagens da fila

Há duas maneiras de personalizar a recuperação da mensagem de uma fila.
Primeiro, você pode obter um lote de mensagens (até 32). Em segundo lugar, você pode definir um tempo limite de invisibilidade mais longo ou mais curto, permitindo mais ou menos tempo para o código processar completamente cada mensagem.

O exemplo de código a seguir usa o método retrieveMessages para obter 20 mensagens em uma chamada. Em seguida, ele processa cada mensagem usando um loop **for**. Ele também define o tempo limite de invisibilidade como cinco minutos (300 segundos) para cada mensagem. Observe que os cinco minutos começam para todas as mensagens ao mesmo tempo; portanto, depois de cinco minutos desde a chamada para retrieveMessages, todas as mensagens que não tenham sido excluídas se tornarão visíveis novamente.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds
    for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
        // Do processing for all messages in less than 5 minutes, 
        // deleting each message after processing.
        queue.deleteMessage(message);
    }

## <a name="get-queue-length"> </a>Como obter o comprimento da fila

Você pode obter uma estimativa do número de mensagens em uma fila. O método downloadAttributes solicita vários valores atuais ao serviço Fila, incluindo uma contagem de quantas mensagens estão em uma fila. A contagem é aproximada porque as mensagens podem ser adicionadas ou removidas depois que o serviço Fila responde à sua solicitação. O método getApproximateMethodCount retorna o último valor recuperado pela chamada para downloadAttributes, sem chamar o serviço Fila.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Download the approximate message count from the server
    queue.downloadAttributes();

    // Retrieve the newly cached approximate message count
    long cachedMessageCount = queue.getApproximateMessageCount();

## <a name="delete-queue"> </a>Como excluir uma fila

Para excluir uma fila e todas as mensagens contidas nela, chame o método delete no objeto queue.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Delete the queue
    queue.delete();

## <a name="NextSteps"> </a>Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de fila, siga estes links para saber como executar tarefas de armazenamento mais complexas.

-   Consulte a Referência do MSDN: [Armazenando e acessando dados no Windows
    Azure]
-   Acesse o Blog da Equipe de Armazenamento do Azure <http://blogs.msdn.com/b/windowsazurestorage/>

[SDK do Azure para Java]: http://www.windowsazure.com/pt-br/develop/java/
[Armazenando e acessando dados no Azure (a página pode estar em inglês)]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx

