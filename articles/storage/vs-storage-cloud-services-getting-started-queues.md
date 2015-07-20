<properties 
	pageTitle="Introdução ao Armazenamento do Azure" 
	description="Como começar a usar o armazenamento de fila do Azure em um projeto de serviço de nuvem no Visual Studio" 
	services="storage" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/24/2015" 
	ms.author="patshea123"/>

# Introdução ao armazenamento do Azure (Projetos de serviço de nuvem)

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-cloud-services-getting-started-queues.md)
> - [What Happened](vs-storage-cloud-services-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-cloud-services-getting-started-blobs.md)
> - [Queues](vs-storage-cloud-services-getting-started-queues.md)
> - [Tables](vs-storage-cloud-services-getting-started-tables.md)

O armazenamento de filas do Azure é um serviço para armazenamento de um grande número de mensagens que podem ser acessadas de qualquer lugar do mundo por meio de chamadas autenticadas usando HTTP ou HTTPS. Uma única mensagem de fila pode ter até 64 KB de tamanho e uma fila pode conter milhões de mensagens, até o limite de capacidade total de uma conta de armazenamento. Consulte [Como usar o Armazenamento de Filas do .NET](storage-dotnet-how-to-use-queues.md/ "Como usar o Armazenamento de Fila no .NET") para obter mais informações.

Este artigo lhe mostrará como executar cenários comuns usando o serviço de Armazenamento de Fila do Azure. Os exemplos são escritos em C# e usam o Cliente de Armazenamento do Azure para .NET. Os cenários abrangidos incluem **inserir**, **exibir**, **obter** e **excluir** mensagens da fila, bem como **criar e excluir filas**.

## Acessar programaticamente o armazenamento de fila

[AZURE.INCLUDE [storage-dotnet-obtain-assembly](../../includes/storage-dotnet-obtain-assembly.md)]

### Declarações de namespace
Adicione as seguintes declarações de namespace de código à parte superior de qualquer arquivo C# no qual você deseje acessar o Armazenamento do Azure por meio de programação:

    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;

Certifique-se de fazer referência ao assembly `Microsoft.WindowsAzure.Storage.dll`.

[AZURE.INCLUDE [storage-dotnet-retrieve-conn-string](../../includes/storage-dotnet-retrieve-conn-string.md)]

[AZURE.INCLUDE [storage-dotnet-odatalib-dependencies](../../includes/storage-dotnet-odatalib-dependencies.md)]

## Criar uma fila

Um objeto **CloudQueueClient** permite que você obtenha objetos de referência para filas. O código a seguir cria um objeto **CloudQueueClient**. Todos os códigos neste guia usam uma cadeia de conexão de armazenamento armazenada na configuração dos serviços do aplicativo do Azure. Também existem outras maneiras de criar um objeto **CloudStorageAccount**. Confira a documentação da [CloudStorageAccount][] para obter detalhes.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Use o objeto **queueClient** para obter uma referência à fila que deseja usar. Você poderá criar a fila se ela não existir.

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

## Inserir uma mensagem em uma fila

Para inserir uma mensagem em uma fila existente, primeiro crie uma nova **CloudQueueMessage**. Em seguida, chame o método **AddMessage**. Um **CloudQueueMessage** pode ser criado por meio de uma cadeia de caracteres (em formato UTF-8) ou de uma matriz de **bytes**. Este é o código que cria uma fila (se ela não existir) e insere a mensagem 'Hello, World':

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.CreateIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

## Espiar a próxima mensagem

Você pode inspecionar a mensagem na frente de uma fila sem removê-la da fila chamando o método **PeekMessage**.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Display message.
	Console.WriteLine(peekedMessage.AsString);

## Alterar o conteúdo de uma mensagem na fila

Você pode alterar o conteúdo de uma mensagem in-loco na fila. Se a mensagem representar uma tarefa de trabalho, você poderá usar esse recurso para atualizar o status da tarefa de trabalho. O código a seguir atualiza a mensagem da fila com novo conteúdo e define o tempo limite de visibilidade para estender mais 60 segundos. Isso salva o estado do trabalho associado à mensagem e dá ao cliente mais um minuto para continuar trabalhando na mensagem. Você pode usar essa técnica para acompanhar fluxos de trabalho de várias etapas em mensagens em fila, sem a necessidade de começar desde o início, caso uma etapa de processamento falhar devido a uma falha de hardware ou de software. Normalmente, você mantém uma contagem de repetições e, se a mensagem for repetida mais de *n* vezes, você a exclui. Isso protege contra uma mensagem que dispara um erro do aplicativo sempre que for processada.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// Get the message from the queue and update the message contents.
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.") ;
    queue.UpdateMessage(message, 
        TimeSpan.FromSeconds(0.0),  // Make it visible immediately.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

## Remover a próxima mensagem da fila

Seu código remove uma mensagem de um fila em duas etapas. Ao chamar **GetMessage**, você recebe a próxima mensagem em uma fila. Uma mensagem retornada de **GetMessage** torna-se invisível para todas as outras mensagens de leitura de código da fila. Por padrão, essa mensagem permanece invisível por 30 segundos. Para terminar de remover a mensagem da fila, você também deve chamar **DeleteMessage**. Este processo de duas etapas para remover uma mensagem garante que quando o código não processa uma mensagem devido à falhas de hardware ou de software, outra instância do seu código pode receber a mesma mensagem e tentar novamente. Seu código chama **DeleteMessage** logo depois que a mensagem é processada.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the next message
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    //Process the message in less than 30 seconds, and then delete the message
    queue.DeleteMessage(retrievedMessage);

## Usar padrão Async-Await com APIs comuns de fila

Este exemplo mostra como usar o padrão Async-Await com APIs de fila comuns. O exemplo chama a versão assíncrona de cada um dos métodos determinados, o que pode ser visto pela pós-correção Async de cada método. Quando um método assíncrono é usado, o padrão async-await suspende a execução local até que a chamada seja concluída. Esse comportamento permite que o thread atual faça outro trabalho que ajude a evitar gargalos de desempenho e melhora a capacidade de resposta geral do aplicativo. Para obter mais detalhes sobre como usar o padrão Async-Await no .NET, confira [Async e Await (C# e Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

    // Create the queue if it doesn't already exist
    if(await queue.CreateIfNotExistsAsync())
    {
        Console.WriteLine("Queue '{0}' Created", queue.Name);
    }
    else
    {
        Console.WriteLine("Queue '{0}' Exists", queue.Name);
    }

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message
    await queue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message
    await queue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## Aproveitar opções adicionais para remover mensagens da fila

Há duas maneiras de personalizar a recuperação da mensagem de uma fila. Primeiro, você pode obter um lote de mensagens (até 32). Segundo, você pode definir um tempo limite de invisibilidade mais longo ou mais curto, permitindo mais ou menos tempo para seu código processar totalmente cada mensagem. O exemplo de código a seguir usa o método **GetMessages** para receber 20 mensagens em uma chamada. Em seguida, ele processa cada mensagem usando um loop **foreach**. Ele também define o tempo limite de invisibilidade de cinco minutos para cada mensagem. Observe que os 5 minutos começam para todas as mensagens ao mesmo tempo; portanto, depois de 5 minutos desde a chamada para **GetMessages**, todas as mensagens que não tenham sido excluídas se tornarão visíveis novamente.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## Obter o tamanho da fila

Você pode obter uma estimativa do número de mensagens em uma fila. O método **FetchAttributes** solicita que o serviço Fila recupere os atributos da fila, incluindo a contagem de mensagens. A propriedade **ApproximateMethodCount** retorna o último valor recuperado pelo método **FetchAttributes**, sem chamar o serviço Fila.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// Fetch the queue attributes.
	queue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = queue.ApproximateMessageCount;

	// Display number of messages.
	Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## Excluir uma fila

Para excluir uma fila e todas as mensagens que ela contém, chame o método **Delete** no objeto fila.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

## Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de Fila, siga estes links para saber mais sobre tarefas de armazenamento mais complexas.

<ul>
<li>Consulte a documentação de referência do serviço Fila para obter detalhes completos sobre as APIs disponíveis:
  <ul>
    <li>Referência à <a href="http://go.microsoft.com/fwlink/?LinkID=390731">Biblioteca de Cliente de Armazenamento para .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/library/azure/dd179355">Referência da API REST</a></li>
  </ul>
</li>
<li>Conheça as tarefas mais avançadas que você pode executar com o Armazenamento do Azure em <a href="http://msdn.microsoft.com/library/azure/gg433040.aspx">Armazenando e acessando dados no Azure</a>.</li>
<li>Saiba como simplificar o código que você escreve para trabalhar com o armazenamento do Azure usando o <a href="../websites-dotnet-webjobs-sdk/">SDK WebJobs do Azure.</li>
<li>Consulte outros guias de recursos para obter informações sobre opções adicionais para armazenar dados no Azure.
  <ul>
    <li>Usar o <a href="/documentation/articles/storage-dotnet-how-to-use-tables/">Armazenamento de Tabela</a> para armazenar dados estruturados.</li>
    <li>Usar o <a href="/documentation/articles/storage-dotnet-how-to-use-blobs/">Armazenamento de Blob</a> para armazenar dados não estruturados.</li>
    <li>Usar o <a href="/documentation/articles/sql-database-dotnet-how-to-use/">Banco de Dados SQL</a> para armazenar dados relacionais.</li>
  </ul>
</li>
</ul>

  [Download and install the Azure SDK for .NET]: /develop/net/
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731
  [Creating a Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx
  [CloudStorageAccount]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [Storing and Accessing Data in Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
 

<!---HONumber=July15_HO2-->