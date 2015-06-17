<properties 
	pageTitle="Como usar o armazenamento de Fila do .NET | Microsoft Azure" 
	description="Saiba como usar o armazenamento de Fila do Microsoft Azure para criar e excluir filas e inserir, espiar, obter e excluir mensagens de fila." 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="tamram"/>

# Como usar o armazenamento de Fila do .NET

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

## Visão geral

Este guia mostra como executar cenários comuns usando o
Serviço de armazenamento de Fila do Azure. Os exemplos são escritos em C# e usam o cliente de armazenamento do Azure para .NET. Os cenários abordados incluem **inserção**,
**inspeção**, **obtenção** e **exclusão** de mensagens das filas, bem como
**criação e exclusão de filas**. 

> [AZURE.NOTE] Este guia tem como alvo a Biblioteca do Cliente de Armazenamento .NET do Azure, versão 2.x e superior. A versão recomendada é a Biblioteca do Cliente de Armazenamento 4.x que está disponível via [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) ou como parte do [SDK do Azure para .NET](/downloads/). Consulte [Acessar o armazenamento de Fila programaticamente](#programmatically-access-queue-storage) abaixo para obter mais detalhes sobre como obter a Biblioteca de Cliente de Armazenamento.

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-configure-connection-string-include](../../includes/storage-configure-connection-string-include.md)]

## Acessar programaticamente o armazenamento de fila

### Obtendo o assembly
Você pode usar NuGet para obter o assembly  `Microsoft.WindowsAzure.Storage.dll`. Clique com o botão direito do mouse no **Gerenciador de Soluções** e escolha **Gerenciar Pacotes NuGet**.  Pesquise online por "WindowsAzure.Storage" e clique em **Instalar** para instalar o pacote Armazenamento do Azure e as dependências.

`Microsoft.WindowsAzure.Storage.dll` também está incluído no SDK do Azure para .NET, que pode ser baixado na <a href="http://azure.microsoft.com/develop/net/#">Central de desenvolvedores do .NET</a>. O assembly está instalado no diretório  `%Program Files%\Microsoft SDKs\Azure.NET SDK<sdk-version>\ref`.

### Declarações de namespace
Adicione as seguintes declarações de namespace de código à parte superior de qualquer arquivo C# no qual você deseja acessar o Armazenamento do Azure por meio de programação:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

Certifique-se de fazer referência ao assembly  `Microsoft.WindowsAzure.Storage.dll`.

### Recuperando sua cadeia de conexão
Você pode usar o tipo **CloudStorageAccount** para representaras informações de sua Conta de Armazenamento. Se você estiver usando o Windows 
modelo de projeto do Azure e/ou tiver uma referência para 
Microsoft.WindowsAzure.CloudConfigurationManager, você poderá usar o tipo **CloudConfigurationManager** para recuperar a cadeia de conexão de armazenamento e as informações de conta de armazenamento da configuração de serviço do Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Se estiver criando um aplicativo sem nenhuma referência ao Microsoft.WindowsAzure.CloudConfigurationManager, e sua cadeia de conexão estiver localizada no `web.config` ou `app.config`, como mostrado acima, você poderá usar o **ConfigurationManager** para recuperar a cadeia de conexão.  Você precisará adicionar uma referência ao System.Configuration.dll a seu projeto e adicionar outra declaração de namespace para ele:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

### Dependências do ODataLib
As dependências do ODataLib na Biblioteca de Cliente de Armazenamento para .NET são resolvidas por meio de pacotes do ODataLib (versão 5.0.2) disponíveis por meio do NuGet e não do WCF Data Services.  As bibliotecas do ODataLib podem ser baixadas diretamente ou referenciadas por seu projeto de código por meio do NuGet.  Os pacotes ODataLib específicos são [OData], [Edm] e [Spatial].

## Criar uma fila

Um objeto **CloudQueueClient** permite que você obtenha objetos de referência para filas.
O código a seguir cria um objeto **CloudQueueClient**. Todos os códigos neste guia usam uma cadeia de conexão de armazenamento armazenada na configuração dos serviços do aplicativo do Azure. Também existem outras maneiras de criar um objeto **CloudStorageAccount**. Consulte a documentação de [CloudStorageAccount][] para obter detalhes.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Use o objeto **queueClient** para obter uma referência à fila que deseja usar. Você poderá criar a fila se ela não existir.

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExist();

## Inserir uma mensagem em uma fila

Para inserir uma mensagem em uma fila existente, primeiro crie um
**CloudQueueMessage**. Em seguida, chame o método **AddMessage**. Um
**CloudQueueMessage** pode ser criado por meio de uma cadeia de caracteres (em formato UTF-8) ou de uma matriz de **byte**. Este é o código que cria uma fila (se ela não existir) e insere a mensagem  'Hello, World':

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.CreateIfNotExist();

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

Seu código remove uma mensagem de um fila em duas etapas. Quando você chamar
**GetMessage**, receberá a próxima mensagem em uma fila. Uma mensagem retornada de **GetMessage** torna-se invisível para todas as outras mensagens de leitura de código da fila. Por padrão, essa mensagem permanece invisível por 30 segundos. Para terminar de remover a mensagem da fila, você também deve chamar **DeleteMessage**. Este processo de duas etapas de remover uma mensagem garante que se o código não processar uma mensagem devido a falhas de hardware ou de software, outra instância do seu código poderá receber a mesma mensagem e tentar novamente. Seu código chama **deleteMessage** logo depois que a mensagem é processada.

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

## Aproveitar opções adicionais para remover mensagens da fila

Há duas maneiras de personalizar a recuperação da mensagem de uma fila.
Primeiro, você pode obter um lote de mensagens (até 32). Segundo, você pode definir um tempo limite de invisibilidade mais longo ou mais curto, permitindo mais ou menos tempo para seu código processar totalmente cada mensagem. O seguinte exemplo de código usa o método
**GetMessages** para obter 20 mensagens em uma chamada. Em seguida, ele processa cada mensagem usando um loop **foreach**. Ele também define o tempo limite de invisibilidade de cinco minutos para cada mensagem. Observe que os 5 minutos começam para todas as mensagens ao mesmo tempo; portanto, depois de 5 minutos desde a chamada para **GetMessages**, todas as mensagens que não tenham sido excluídas se tornarão visíveis novamente.

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

Você pode obter uma estimativa do número de mensagens em uma fila. O
O método **FetchAttributes** solicita que o serviço de fila recupere os atributos da fila, incluindo a contagem de mensagens. A propriedade **ApproximateMethodCount** retorna o último valor recuperado pelo método
**FetchAttributes**, sem chamar o serviço de fila.

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

Para excluir uma fila e todas as mensagens contidas nela, chame o método
**Delete** no objeto de fila.

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
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Referência à Biblioteca de Cliente de Armazenamento para .NET</a>
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

  [Baixar e instalar o SDK do Azure para .NET] /develop/net/
  [Referência à Biblioteca cliente do .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Criando um projeto do Azure no Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx 
  [CloudStorageAccount]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Blog da equipe do Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configurando cadeias de conexão]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2

<!--HONumber=49--> 