<properties 
	pageTitle="Introdução ao armazenamento de fila do Azure e serviços conectados do Visual Studio" 
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
	ms.date="08/04/2015" 
	ms.author="patshea123"/>

# Introdução ao armazenamento de fila do Azure e serviços conectados do Visual Studio

> [AZURE.SELECTOR]
> - [Getting started](vs-storage-cloud-services-getting-started-queues.md)
> - [What happened](vs-storage-cloud-services-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-cloud-services-getting-started-blobs.md)
> - [Queues](vs-storage-cloud-services-getting-started-queues.md)
> - [Tables](vs-storage-cloud-services-getting-started-tables.md)

Este artigo descreve como começar a usar o armazenamento de tabela do Azure no Visual Studio depois de ter criado ou referenciado uma conta de armazenamento do Azure em um projeto de serviços de nuvem usando a caixa de diálogo **Adicionar Serviços Conectados** do Visual Studio.

Mostraremos como criar uma fila em código. Também mostraremos como realizar operações básicas de fila, como adicionar, modificar, ler e remover entidades de fila. Os exemplos são escritos em C# e usam a [biblioteca do cliente de armazenamento do Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

 - Confira [Como usar o armazenamento de blob do .NET](storage-dotnet-how-to-use-blobs.md "Como usar o Armazenamento de Blob no .NET") para obter mais informações sobre como manipular blobs em código.
 - Consulte a [documentação de armazenamento](https://azure.microsoft.com/documentation/services/storage/) para obter informações gerais sobre o armazenamento do Azure.
 - Consulte a [documentação de serviços de nuvem](http://azure.microsoft.com/documentation/services/cloud-services/) para obter informações gerais sobre os serviços de nuvem do Azure.
 - Consulte [ASP.NET](http://www.asp.net) para obter mais informações sobre como programar aplicativos ASP.NET.


O armazenamento de filas do Azure é um serviço para armazenamento de um grande número de mensagens que podem ser acessadas de qualquer lugar do mundo por meio de chamadas autenticadas usando HTTP ou HTTPS. Uma única mensagem de fila pode ter até 64 KB de tamanho e uma fila pode conter milhões de mensagens, até o limite de capacidade total de uma conta de armazenamento.

A operação **Adicionar Serviços Conectados** instala os pacotes NuGet apropriados para acessar o armazenamento do Azure no seu projeto e adiciona a cadeia de conexão para a conta de armazenamento aos arquivos de configuração do projeto.

Para começar, primeiramente, você precisa criar uma fila do Azure em sua conta de armazenamento.

##Acessar filas em código

Para acessar filas em projetos do ASP.NET, você precisa incluir os itens a seguir para qualquer arquivo de origem C# que acesse o armazenamento de fila do Azure.

1. Verifique se as declarações de namespace na parte superior do arquivo de C# incluem estas instruções `using`.

		using Microsoft.Framework.Configuration;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Queue;

2. Obtenha um objeto `CloudStorageAccount` que represente as informações da conta de armazenamento. Use o seguinte código para obter a sua cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure.

		 CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		   CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));

3. Obtenha um objeto `CloudQueueClient` para fazer referência aos objetos de fila em sua conta de armazenamento.

	    // Create the table client.
    	CloudQuecClient queueClient = storageAccount.CreateCloudTableClient();

4. Obtenha um objeto `CloudQueue` para fazer referência a uma fila específica.

    	// Get a reference to a table named "messageQueue"
	    CloudTable messageQueue = queueClient.GetQueueReference("messageQueue");


**OBSERVAÇÃO:** use todo esse código antes do código nos exemplos a seguir.

###Criar uma fila em código

Para criar a fila do Azure no código, basta adicionar uma chamada para `CreateIfNotExists`.

	// Get a reference to a CloudQueue object with the variable name 'messageQueue' as described in 
    // the "Access blob containers in code" section.

	// Create the CloudTable if it does not exist
	messageQueue.CreateIfNotExists();

##Adicionar uma mensagem a uma fila

Para inserir uma mensagem em uma fila existente, crie primeiramente um novo objeto `CloudQueueMessage` e chame o método AddMessage().

Um objeto `CloudQueueMessage` pode ser criado por meio de uma cadeia de caracteres (em formato UTF-8) ou de uma matriz de byte.

Aqui está um exemplo que insere a mensagem “Hello, World”.

    // Get a reference to a CloudQueue object with the variable name 'messageQueue' as described in 
    // the "Access blob containers in code" section.

	// Create a message and add it to the queue.
	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
	messageQueue.AddMessage(message);

##Ler uma mensagem em uma fila

Você pode espiar a mensagem na frente de uma fila sem removê-la da fila, chamando o método PeekMessage().

    // Get a reference to a CloudQueue object with the variable name 'messageQueue' as described in 
    // the "Access blob containers in code" section.

	// Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

##Ler e remover uma mensagem em uma fila

Seu código pode remover uma mensagem de uma fila em duas etapas. 1. Chame GetMessage() para obter a próxima mensagem em uma fila. As mensagens retornadas de GetMessage() tornam-se invisíveis para todas as outras mensagens de leitura de código da fila. Por padrão, essa mensagem permanece invisível por 30 segundos. 2. Para concluir a remoção da mensagem da fila, chame DeleteMessage1().

Este processo de duas etapas de remover uma mensagem garante que quando o código não processa uma mensagem devido à falhas de hardware ou de software, outra instância do seu código pode receber a mesma mensagem e tentar novamente. Seu código chamará DeleteMessage() logo depois que a mensagem for processada.

    // Get a reference to a CloudQueue object with the variable name 'messageQueue' as described in 
    // the "Access blob containers in code" section.

	// Get the next message in the queue.
	CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

	// Process the message in less than 30 seconds

  	// Then delete the message.
	await messageQueue.DeleteMessage(retrievedMessage);


## Utilizar opções adicionais para processamento e remover mensagens da fila

Há duas maneiras de personalizar a recuperação da mensagem de uma fila. Primeiro, você pode obter um lote de mensagens (até 32). Segundo, você pode definir um tempo limite de invisibilidade mais longo ou mais curto, permitindo mais ou menos tempo para seu código processar totalmente cada mensagem. O exemplo de código a seguir usa o método `GetMessages` para receber 20 mensagens em uma chamada. Em seguida, ele processa cada mensagem usando um loop `foreach`. Ele também define o tempo limite de invisibilidade de cinco minutos para cada mensagem. Observe que os 5 minutos começam para todas as mensagens ao mesmo tempo; portanto, depois de 5 minutos desde a chamada para `GetMessages`, todas as mensagens que não tenham sido excluídas se tornarão visíveis novamente.

    // Get a reference to a CloudQueue object with the variable name 'messageQueue' as described in 
    // the "Access blob containers in code" section.

    foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.

        messageQueue.DeleteMessage(message);
    }

## Obter o tamanho da fila

Você pode obter uma estimativa do número de mensagens em uma fila. O método `FetchAttributes` solicita que o serviço Fila recupere os atributos da fila, incluindo a contagem de mensagens. A propriedade `ApproximateMethodCount` retorna o último valor recuperado pelo método `FetchAttributes`, sem chamar o serviço Fila.

    // Get a reference to a CloudQueue object with the variable name 'messageQueue' as described in 
    // the "Access blob containers in code" section.

	// Fetch the queue attributes.
	messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

	// Display number of messages.
	Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## Usar padrão Async-Await com APIs comuns de fila

Este exemplo mostra como usar o padrão Async-Await com APIs de fila comuns. O exemplo chama a versão assíncrona de cada um dos métodos determinados, o que pode ser visto pela pós-correção Async de cada método. Quando um método assíncrono é usado, o padrão async-await suspende a execução local até que a chamada seja concluída. Esse comportamento permite que o thread atual faça outro trabalho que ajude a evitar gargalos de desempenho e melhora a capacidade de resposta geral do aplicativo. Para obter mais detalhes sobre como usar o padrão Async-Await no .NET, confira [Async e Await (C# e Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

    // Get a reference to a CloudQueue object with the variable name 'messageQueue' as described in 
    // the "Access blob containers in code" section.

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Add the message asynchronously
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Delete the message asynchronously
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");
## Excluir uma fila

Para excluir uma fila e todas as mensagens que ela contém, chame o método `Delete` no objeto queue.

    // Get a reference to a CloudQueue object with the variable name 'messageQueue' as described in 
    // the "Access blob containers in code" section.

    // Delete the queue.
    messageQueue.Delete();



##Próximas etapas

[AZURE.INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
			

<!---HONumber=06-->