<properties urlDisplayName="Queue Service" pageTitle="Como usar o armazenamento de fila do .NET | Microsoft Azure" metaKeywords="Get started Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage   Azure queue .NET   Azure queue storage .NET   Azure queue C#   Azure queue storage C#" description="Learn how to use Microsoft Azure Queue storage to create and delete queues and insert, peek, get, and delete queue messages." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Queue Storage" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Como usar o Armazenamento de Fila no .NET

Este guia mostra como executar cenários comuns usando o
Serviço de armazenamento de fila do Azure. As amostras são gravadas em código C\#
e usam o Cliente de Armazenamento do Azure para .NET. Os cenários abordados incluem a **inserção**,
a **inspeção**, a **obtenção** e a **exclusão** de mensagens das filas, bem como
a **criação e exclusão de filas**. Para obter mais informações sobre filas, consulte
para a seção [Próximas etapas][] .

> [WACOM.NOTE] Este guia tem como alvo a Biblioteca do Cliente de Armazenamento .NET do Azure, versão 2.x e superior. A versão recomendada é a Biblioteca de Cliente de Armazenamento 4.x, que está disponível via [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) ou como parte do [SDK do Azure para .NET](/pt-br/downloads/). Consulte [Como: Acessar programaticamente o armazenamento de fila][] abaixo para obter mais detalhes sobre como obter a Biblioteca de Cliente de Armazenamento.

<h2>Sumário</h2>

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
-   [Como: Aproveitar opções adicionais para remover mensagens da fila][]
-   [Como: Obter o tamanho da fila][]
-   [Como: Excluir uma fila][]
-   [Próximas etapas][]

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## <h2><a name="create-account"></a><span  class="short-header">Criar uma conta</span>Criar uma conta de Armazenamento do Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <h2><a name="setup-connection-string"></a><span  class="short-header">Configurar uma cadeia de conexão</span>Configurar uma cadeia de conexão de armazenamento do Azure</h2>

[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a><span  class="short-header">Acessar de forma programática</span>Como: Acessar programaticamente o armazenamento de fila

<h3>Obtendo o assembly</h3>
Você pode usar o NuGet para obter o assembly `Microsoft.WindowsAzure.Storage.dll`. Clique com o botão direito do mouse no **Gerenciador de Soluções** e escolha **Gerenciar Pacotes NuGet**.  Pesquise online por "WindowsAzure.Storage" e clique em **Instalar** para instalar o pacote Armazenamento do Azure e as dependências.

`Microsoft.WindowsAzure.Storage.dll` também está incluído no SDK do Azure para .NET, que pode ser baixado na <a href="http://www.windowsazure.com/pt-br/develop/net/#">Central de desenvolvedores do .NET</a>. O assembly é instalado no diretório `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`.

<h3>Declarações de namespace</h3>
Adicione as seguintes declarações de namespace do código à parte superior de qualquer arquivo C\#
em que você deseja acessar o armazenamento do Azure programaticamente:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

Faça referência ao assembly `Microsoft.WindowsAzure.Storage.dll`.

<h3>Recuperando sua cadeia de conexão</h3>
Você pode usar o tipo **CloudStorageAccount** para representar 
as informações da conta de armazenamento. Se você estiver usando um 
modelo de projeto do Azure e/ou tiver uma referência para 
Microsoft.WindowsAzure.CloudConfigurationManager, você 
Você pode usar o tipo **CloudConfigurationManager**
para recuperar a cadeia de conexão de armazenamento e a conta de armazenamento
informações da configuração do serviço do Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Se estiver criando um aplicativo sem referência a Microsoft.WindowsAzure.CloudConfigurationManager e a cadeia de conexão estiver localizada no `web.config` ou no `app.config` conforme mostrado acima, você poderá usar **ConfigurationManager** para recuperar a cadeia de conexão.  Você precisará adicionar uma referência ao System.Configuration.dll a seu projeto e adicionar outra declaração de namespace para ele:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

<h3>Dependências do ODataLib</h3>
As dependências do ODataLib na Biblioteca de Cliente de Armazenamento para .NET são resolvidas por meio de pacotes do ODataLib (versão 5.0.2) disponíveis por meio do NuGet e não do WCF Data Services.  As bibliotecas do ODataLib podem ser baixadas diretamente ou referenciadas por seu projeto de código por meio do NuGet.  Os pacotes ODataLib específicos são [OData], [Edm]e [Espacial].

<h2><a name="create-queue"></a><span  class="short-header">Criar uma fila</span>Como: Criar uma fila</h2>

Um objeto **CloudQueueClient** permite que você obtenha objetos de referência para filas.
O código a seguir cria um objeto **CloudQueueClient**. Todo código neste
guia usa uma cadeia de conexão armazenada na configuração de serviço do
do aplicativo do Azure. Também há outras maneiras de criar
um objeto **CloudStorageAccount**. Consulte [CloudStorageAccount][]
para obter detalhes.

    // Recuperar a conta de armazenamento da cadeia de conexão
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de fila
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Use o objeto **queueClient** para obter uma referência à fila que você deseja
usar. Você poderá criar a fila se ela não existir.

    // Recuperar uma referência a uma fila
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Criar a fila se ela ainda não existir
    queue.CreateIfNotExists();

<h2><a name="insert-message"> </a><span  class="short-header">Inserir uma mensagem</span>Como: Inserir uma mensagem em uma fila</h2>

Para inserir uma mensagem em uma fila existente, primeiro crie um
**CloudQueueMessage**. Em seguida, chame o método **AddMessage**. Um
**CloudQueueMessage** pode ser criada a partir de uma cadeia de caracteres (em formato UTF-8
) ou uma matriz **bytes**. Aqui está o código que cria uma fila (se ele
não existe) e insere a mensagem 'Hello, World':

    // Recuperar a conta de armazenamento da cadeia de conexão.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de fila.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Recuperar uma referência a uma fila.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Criar a fila se ela ainda não existir.
    queue.CreateIfNotExists();

    // Criar uma mensagem e adicioná-la à fila.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

<h2><a name="peek-message"></a><span  class="short-header">Espiar a próxima mensagem</span>Como: Espiar a próxima mensagem</h2>

Você pode inspecionar a mensagem na frente de uma fila sem removê-la
da fila chamando o método **PeekMessage**.

    // Recuperar a conta de armazenamento da cadeia de conexão
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de fila
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Recuperar uma referência a uma fila
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Inspecionar a próxima mensagem
    CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Exibir mensagem.
	Console.WriteLine(peekedMessage.AsString);

<h2><a name="change-contents"></a><span  class="short-header">Alterar o conteúdo da mensagem</span>Como: Alterar o conteúdo de uma mensagem na fila</h2>

Você pode alterar o conteúdo de uma mensagem in-loco na fila. Se a
mensagem representa uma tarefa de trabalho, você poderá usar esse recurso para atualizar o
status da tarefa. O código a seguir atualiza a mensagem da fila
com novo conteúdo e define o tempo limite de visibilidade para estender outros 60
Segundos. Isso salva o estado do trabalho associado à mensagem e
dá ao cliente mais um minuto para continuar trabalhando na mensagem. Você
pode usar essa técnica para acompanhar fluxos de trabalho de várias etapas em mensagens
em fila, sem ter que recomeçar desde o início, se uma
etapa de processamento falhar devido a uma falha de hardware ou software. Em geral,
você mantém uma contagem de repetições, e se a mensagem for repetida mais
que *n* vezes, você poderá exclui-la. Isso protege contra uma mensagem
que dispara um erro de aplicativo sempre que ela é processada.

    // Recuperar a conta de armazenamento da cadeia de conexão.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de fila.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Recuperar uma referência a uma fila.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// Obter a mensagem da fila e atualizar o conteúdo da mensagem.
    Mensagem CloudQueueMessage = fila.GetMessage ();
    message.SetMessageContent("Updated contents.") ;
    queue.UpdateMessage(message, 
        TimeSpan.FromSeconds(0.0),  //Torná-lo visível imediatamente.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

<h2><a name="get-message"></a><span  class="short-header">Remover a próxima mensagem da fila</span>Como: Remover a próxima mensagem da fila</h2>

Seu código remove uma mensagem de um fila em duas etapas. Quando você chamar
**GetMessage**, receberá a próxima mensagem em uma fila. Uma mensagem retornada
de **GetMessage** se torna invisível para qualquer outro código de leitura de mensagens
dessa fila. Por padrão, essa mensagem permanece invisível por 30
segundos. Para concluir a remoção da mensagem da fila, você também deve
chamar **DeleteMessage**. Esse processo de duas etapas de remover uma mensagem
garante que, se seu código falha ao processar uma mensagem devido a uma falha de hardware ou
software, outra instância do seu código pode receber a mesma mensagem
e tentar novamente. Seu código chama **DeleteMessage** logo depois que a mensagem
foi processada.

    // Recuperar a conta de armazenamento da cadeia de conexão
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de fila
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Recuperar uma referência a uma fila
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Obter a próxima mensagem
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    //Processar a mensagem em menos de 30 segundos e, em seguida, excluir a mensagem
    queue.DeleteMessage(retrievedMessage);

<h2><a name="advanced-get"></a><span  class="short-header">Mais opções de remoção da fila</span>Como: Aproveitar opções adicionais para remover mensagens da fila</h2>

Há duas maneiras de personalizar a recuperação da mensagem de uma fila.
Primeiro, você pode obter um lote de mensagens (até 32). Em segundo lugar, você pode definir um
tempo limite de invisibilidade mais longo ou mais curto, permitindo que seu código tenha mais ou menos
tempo para processar totalmente cada mensagem. O seguinte exemplo de código usa o
método **GetMessages** para obter 20 mensagens em uma chamada. Em seguida, ele processa
cada mensagem usando um loop **foreach**. Ele também define a invisibilidade
do tempo limite como cinco minutos para cada mensagem. Observe que o tempo de 5 minutos inicia
para todas as mensagens ao mesmo tempo, após 5 minutos desde
a chamada para **GetMessages**, todas as mensagens que não foram excluídas
se tornarão visíveis novamente.

    // Recuperar a conta de armazenamento da cadeia de conexão.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de fila.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Recuperar uma referência a uma fila.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        //Processe todas as mensagens em menos de 5 minutos, excluindo cada mensagem após o processamento.
        queue.DeleteMessage(message);
    }

<h2><a name="get-queue-length"></a><span  class="short-header">Obter o tamanho da fila</span>Como: Obter o tamanho da fila</h2>

Você pode obter uma estimativa do número de mensagens em uma fila. O
O método **FetchAttributes** solicita o serviço de fila
recuperar os atributos da fila, incluindo a contagem de mensagens. A propriedade **ApproximateMethodCount**
retorna o último valor recuperado pelo método
**FetchAttributes**, sem chamar o serviço Fila.

    // Recuperar a conta de armazenamento da cadeia de conexão.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de fila.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Recuperar uma referência a uma fila.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// Buscar os atributos da fila.
	queue.FetchAttributes();

    // Recuperar a contagem aproximada das mensagens armazenadas em cache.
    int? cachedMessageCount = queue.ApproximateMessageCount;

	// Exibir o número de mensagens.
	Console.WriteLine ("Número de mensagens na fila: " + cachedMessageCount);

<h2><a name="delete-queue"></a><span  class="short-header">Excluir uma fila</span>Como: Excluir uma fila</h2>

Para excluir uma fila e todas as mensagens contidas nela, chame o
método **Delete** no objeto queue.

    // Recuperar a conta de armazenamento da cadeia de conexão.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de fila.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Recuperar uma referência a uma fila.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Excluir a fila.
    queue.Delete();

<h2><a name="next-steps"></a>Próximas etapas</h2>

Agora que você aprendeu os conceitos básicos do armazenamento de fila, siga estes links
para saber como fazer tarefas mais complexas de armazenamento.

<ul>
<li>Consulte a documentação de referência do serviço Fila para obter detalhes completos sobre as APIs disponíveis:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Referência à Biblioteca de Cliente de Armazenamento para .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/pt-br/library/windowsazure/dd179355">Referência da API REST</a></li>
  </ul>
</li>
<li>Conheça tarefas mais avançadas, que você pode executar com o armazenamento do Azure <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx">Armazenando e acessando dados no Azure</a>.</li>
<li>Aprenda a trabalhar com o armazenamento do Azure nos processos de back-end para os sites do Azure em <a href="/pt-br/documentation/articles/websites-dotnet-webjobs-sdk-get-started/">Introdução ao SDK de Trabalhos Web do Azure</a>.</li>
<li>Consulte outros guias de recursos para obter informações sobre opções adicionais para armazenar dados no Azure.
  <ul>
    <li>Use <a href="/pt-br/documentation/articles/storage-dotnet-how-to-use-tables/">Armazenamento de tabela</a> para armazenar dados estruturados.</li>
    <li>Use <a href="/pt-br/documentation/articles/storage-dotnet-how-to-use-blobs/">Armazenamento de Blob</a> para armazenar dados não estruturados.</li>
    <li>Use <a href="/pt-br/documentation/articles/sql-database-dotnet-how-to-use/">Banco de dados SQL</a> para armazenar dados relacionais.</li>
  </ul>
</li>
</ul>



  [Próximas etapas]: #next-steps
  [O que é Armazenamento de Filas]: #what-is
  [Conceitos]: #concepts
  [Criar uma conta de armazenamento do Azure]: #create-account
  [Configurar uma cadeia de conexão de armazenamento do Azure]: #setup-connection-string
  [Como: Acessar programaticamente o armazenamento de fila]: #configure-access
  [Como: Criar uma fila]: #create-queue
  [Como: Inserir uma mensagem em uma fila]: #insert-message
  [Como: Espiar a próxima mensagem]: #peek-message
  [Como: Alterar o conteúdo de uma mensagem na fila]: #change-contents
  [Como: Remover a próxima mensagem da fila]: #get-message
  [Como: Aproveitar opções adicionais para remover mensagens da fila]: #advanced-get
  [Como: Obter o tamanho da fila]: #get-queue-length
  [Como: Excluir uma fila]: #delete-queue
  [Baixar e instalar o SDK do Azure para .NET]: /pt-br/develop/net/
  [Referência à biblioteca cliente do .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Criando um projeto do Azure no Visual Studio]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee405487.aspx 
  [CloudStorageAccount]: http://msdn.microsoft.com/pt-br/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx
  [Blog da equipe do Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configurando cadeias de conexão]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Espacial]: http://nuget.org/packages/System.Spatial/5.0.2
