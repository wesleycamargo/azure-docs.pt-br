<properties linkid="dev-net-how-to-queue-service" urlDisplayName="Queue Service" pageTitle="How to use queue storage from .NET | Microsoft Azure" metaKeywords="Get started Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage   Azure queue .NET   Azure queue storage .NET   Azure queue C#   Azure queue storage C#" description="Learn how to use Microsoft Azure Queue storage to create and delete queues and insert, peek, get, and delete queue messages." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Queue Storage" authors="tamram" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"></tags>

# Como usar o Armazenamento de Fila no .NET

Este guia mostrará como executar cenários comuns usando o
serviço de armazenamento de Fila do Azure. Os exemplos são escritos em C#
e usam o Cliente de Armazenamento do Azure para .NET. Os cenários abrangidos incluem **inserir**,
**exibir**, **obter** e **excluir** mensagens da fila, bem como
**criar e excluir filas**. Para obter mais informações sobre filas, consulte
a seção [Próximas etapas][].

> [WACOM.NOTE] Este guia tem como alvo a Biblioteca do Cliente de Armazenamento .NET do Azure, versão 2.x e superior. A versão recomendada é a Biblioteca do Cliente de Armazenamento 4.x que está disponível via [NuGet][] ou como parte do [SDK do Azure para .NET][]. Consulte [Como: Acessar programaticamente o armazenamento de fila][] abaixo para mais detalhes sobre como obter a Biblioteca do Cliente de Armazenamento.

## Sumário

-   [O que é Armazenamento de Filas][]
-   [Conceitos][]
-   [Criar uma conta de armazenamento do Azure][]
-   [Configurar uma cadeia de conexão de armazenamento do Azure][]
-   [Como: Acessar programaticamente o armazenamento de fila][]
-   [Como: Criar uma fila][]
-   [Como: Inserir uma mensagem em uma fila][]
-   [Como: Espiar a próxima mensagem][]
-   [Como: Alterar o conteúdo de uma mensagem na fila][]
-   [Como: Remover a próxima mensagem da fila][]
-   [Como: Como aproveitar opções adicionais para remover mensagens da fila][]
-   [Como: Obter o tamanho da fila][]
-   [Como: Excluir uma fila][]
-   [Próximas etapas][]

[WACOM.INCLUDE [howto-queue-storage][]]

## 

## <a name="create-account"></a><span class="short-header">Criar uma conta</span>Criar uma conta de armazenamento do Azure

</h2>
[WACOM.INCLUDE [create-storage-account][]]

## 

## <a name="setup-connection-string"></a><span class="short-header">Configurar uma cadeia de conexão</span>Configurar uma cadeia de conexão de armazenamento do Azure

</h2>
[WACOM.INCLUDE [storage-configure-connection-string][]]

## <a name="configure-access"> </a><span class="short-header">Acessar programaticamente</span>Como: Acessar programaticamente o armazenamento de fila

### Obtendo o assembly

Você pode usar NuGet para obter o assembly `Microsoft.WindowsAzure.Storage.dll`. Clique com o botão direito do mouse no **Gerenciador de Soluções** e escolha **Gerenciar Pacotes NuGet**. Pesquise online por "WindowsAzure.Storage" e clique em **Instalar** para instalar o pacote Armazenamento do Azure e as dependências.

`Microsoft.WindowsAzure.Storage.dll` também está incluído no SDK do Azure para .NET, que pode ser baixado na [Central de desenvolvedores do .NET][]. O assembly está instalado no diretório `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\<sdk-version>\ref\`.

### Declarações de namespace

Adicione as seguintes declarações de namespace de código à parte superior de qualquer arquivo C#
no qual você deseja acessar o armazenamento do Azure programaticamente:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;

Certifique-se de fazer referência ao assembly `Microsoft.WindowsAzure.Storage.dll`.

### Recuperando sua cadeia de conexão

Você pode usar o tipo **CloudStorageAccount** para representar
as informações de sua Conta de Armazenamento. Se você estiver usando um modelo de projeto do Windows
Azure e/ou tiver uma referência ao
Microsoft.WindowsAzure.CloudConfigurationManager,
poderá usar o tipo **CloudConfigurationManager** para
recuperar a cadeia de conexão do armazenamento e as informações
da conta de armazenamento da configuração dos serviços do Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Se estiver criando um aplicativo sem nenhuma referência ao Microsoft.WindowsAzure.CloudConfigurationManager, e sua cadeia de conexão estiver localizada no `web.config` ou `app.config`, como mostrado acima, você poderá usar o **ConfigurationManager** para recuperar a cadeia de conexão. Você precisará adicionar uma referência ao System.Configuration.dll a seu projeto e adicionar outra declaração de namespace para ele:

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

### Dependências do ODataLib

As dependências do ODataLib na Biblioteca de Cliente de Armazenamento para .NET são resolvidas por meio de pacotes do ODataLib (versão 5.0.2) disponíveis por meio do NuGet e não do WCF Data Services. As bibliotecas do ODataLib podem ser baixadas diretamente ou referenciadas por seu projeto de código por meio do NuGet. Os pacotes ODataLib específicos são [OData][], [Edm][] e [Spatial][].

## <a name="create-queue"></a><span class="short-header">Criar uma fila</span>Como: Criar uma fila

Um objeto **CloudQueueClient** permite que você obtenha objetos de referência para filas.
O código a seguir cria um objeto **CloudQueueClient**. Todos os códigos
neste guia usam uma cadeia de conexão de armazenamento
armazenada na configuração dos serviços do aplicativo do Azure. Há também outras maneiras de criar
um objeto **CloudStorageAccount**. Consulte a [CloudStorageAccount][]
documentação para ver detalhes.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Use o objeto **queueClient** para obter uma referência à fila que deseja
usar. Você poderá criar a fila se ela não existir.

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

## <a name="insert-message"> </a><span class="short-header">Inserir uma mensagem</span>Como: Inserir uma mensagem em uma fila

Para inserir uma mensagem em uma fila existente, primeiro crie uma nova
**CloudQueueMessage**. Em seguida, chame o método **AddMessage**. Um
**CloudQueueMessage** pode ser criado por meio de uma cadeia de caracteres (em formato
UTF-8) ou de uma matriz de **bytes**. Este é o código que cria uma fila (se ela
não existir) e insere a mensagem “Hello, World”:

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

## <a name="peek-message"></a><span class="short-header">Inspecionar a próxima mensagem</span>Como: Espiar a próxima mensagem

Você pode inspecionar a mensagem na frente de uma fila sem removê-la
da fila chamando o método **PeekMessage**.

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

## <a name="change-contents"></a><span class="short-header">Alterar o conteúdo da mensagem</span>Como: Alterar o conteúdo de uma mensagem na fila

Você pode alterar o conteúdo de uma mensagem in-loco na fila. Se a
mensagem representar uma tarefa de trabalho, você poderá usar esse recurso para atualizar o
status da tarefa de trabalho. O código a seguir atualiza a mensagem da fila
com novo conteúdo e define o tempo limite de visibilidade para se estender por mais 60
segundos. Isso salva o estado do trabalho associado à mensagem e
dá ao cliente mais um minuto para continuar trabalhando na mensagem. Você
pode usar essa técnica para acompanhar fluxos de trabalho de várias etapas em mensagens
em fila, sem a necessidade de começar desde o início caso uma
etapa do processamento falhar em função de uma falha de hardware ou de software. Normalmente,
você mantém uma contagem de repetições e, se a mensagem for repetida mais
de *n* vezes, você a exclui. Isso protege contra uma mensagem
que dispara um erro do aplicativo sempre que for processada.

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

## <a name="get-message"></a><span class="short-header">Remover a próxima mensagem da fila</span>Como: Remover a próxima mensagem da fila

Seu código remove uma mensagem de um fila em duas etapas. Ao chamar
**GetMessage**, você recebe a próxima mensagem em uma fila. Uma mensagem retornada
de **GetMessage** torna-se invisível para todas as outras mensagens de leitura de código
da fila. Por padrão, essa mensagem permanece invisível por 30
segundos. Para terminar de remover a mensagem da fila, você também deve
chamar **DeleteMessage**. Este processo de duas etapas para remover uma mensagem
garante que, quando seu código não processar uma mensagem em função de falhas de hardware ou
de software, outra instância do código possa receber a mesma mensagem
e repetir a tentativa. Seu código chama **DeleteMessage** logo depois que a mensagem
é processada.

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

## <a name="advanced-get"></a><span class="short-header">Mais opções de remoção da fila</span>Como: Aproveitar opções adicionais para remover mensagens da fila

Há duas maneiras de personalizar a recuperação da mensagem de uma fila.
Primeiro, você pode obter um lote de mensagens (até 32). Segundo, você pode definir um
tempo limite de invisibilidade mais longo ou mais curto, permitindo mais ou menos
tempo para seu código processar totalmente cada mensagem. O exemplo de código a seguir usa o método
**GetMessages** para receber 20 mensagens em uma chamada. Em seguida, ele processa
cada mensagem usando um loop **foreach**. Ele também define o tempo limite
de invisibilidade de cinco minutos para cada mensagem. Observe que os cinco minutos começam
para todas as mensagens ao mesmo tempo; portanto, depois de cinco minutos desde
a chamada para **GetMessages**, todas as mensagens que não tenham sido excluídas
se tornarão visíveis novamente.

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

## <a name="get-queue-length"></a><span class="short-header">Obter o tamanho da fila</span>Como: Obter o tamanho da fila

Você pode obter uma estimativa do número de mensagens em uma fila. O método
**FetchAttributes** solicita que o serviço de fila
recupere os atributos da fila, incluindo a contagem de mensagens. A propriedade **ApproximateMethodCount**
retorna o último valor recuperado pelo método
**FetchAttributes**, sem chamar o serviço Fila.

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

## <a name="delete-queue"></a><span class="short-header">Excluir uma fila</span>Como: Excluir uma fila

Para excluir uma fila e todas as mensagens que ela contém, chame o método
**Delete** no objeto queue.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de filas, siga estes links
para saber como executar tarefas de armazenamento mais complexas.

-   Consulte a documentação de referência do serviço Fila para obter detalhes completos sobre as APIs disponíveis:
    -   [Referência à Biblioteca de Cliente de Armazenamento para .NET][]
    -   [Referência da API REST][]
-   Conheça as tarefas mais avançadas que você pode executar com o Armazenamento do Azure em [Armazenando e acessando dados no Azure][].
-   Aprenda a trabalhar com o Armazenamento do Azure em processos de back-end para sites do Azure em [Introdução ao SDK de WebJobs do Azure][].
-   Consulte outros guias de recursos para obter informações sobre opções adicionais para armazenar dados no Azure.
    -   Usar o [Armazenamento de Tabela][] para armazenar dados estruturados.
    -   Usar o [Armazenamento de Blob][] para armazenar dados não estruturados.
    -   Usar o [Banco de Dados SQL][] para armazenar dados relacionais.

  [Próximas etapas]: #next-steps
  [NuGet]: https://www.nuget.org/packages/WindowsAzure.Storage/
  [SDK do Azure para .NET]: /en-us/downloads/
  [Como: Acessar programaticamente o armazenamento de fila]: #configure-access
  [O que é Armazenamento de Filas]: #what-is
  [Conceitos]: #concepts
  [Criar uma conta de armazenamento do Azure]: #create-account
  [Configurar uma cadeia de conexão de armazenamento do Azure]: #setup-connection-string
  [Como: Criar uma fila]: #create-queue
  [Como: Inserir uma mensagem em uma fila]: #insert-message
  [Como: Espiar a próxima mensagem]: #peek-message
  [Como: Alterar o conteúdo de uma mensagem na fila]: #change-contents
  [Como: Remover a próxima mensagem da fila]: #get-message
  [Como: Como aproveitar opções adicionais para remover mensagens da fila]: #advanced-get
  [Como: Obter o tamanho da fila]: #get-queue-length
  [Como: Excluir uma fila]: #delete-queue
  [howto-queue-storage]: ../includes/howto-queue-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [storage-configure-connection-string]: ../includes/storage-configure-connection-string.md
  [Central de desenvolvedores do .NET]: http://www.windowsazure.com/en-us/develop/net/#
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [CloudStorageAccount]: http://msdn.microsoft.com/en-us/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [Referência à Biblioteca de Cliente de Armazenamento para .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Referência da API REST]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179355
  [Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
  [Introdução ao SDK de WebJobs do Azure]: /pt-br/documentation/articles/websites-dotnet-webjobs-sdk-get-started/
  [Armazenamento de Tabela]: /pt-br/documentation/articles/storage-dotnet-how-to-use-tables/
  [Armazenamento de Blob]: /pt-br/documentation/articles/storage-dotnet-how-to-use-blobs/
  [Banco de Dados SQL]: /pt-br/documentation/articles/sql-database-dotnet-how-to-use/
