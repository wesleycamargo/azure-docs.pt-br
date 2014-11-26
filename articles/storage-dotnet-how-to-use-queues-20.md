<properties linkid="dev-net-2-how-to-queue-service" urlDisplayName="Serviço Fila" pageTitle="Como usar o armazenamento de fila do .NET | Microsoft Azure" metaKeywords="Introdução à fila do Azure   Processamento assíncrono do Azure   Fila do Azure   Armazenamento de fila do Azure   .NET da fila do Azure   Armazenamento de fila do Azure   C# da fila do Azure   C# do armazenamento de fila do Azure" description="Aprenda a usar o serviço de armazenamento de fila do Windows Azure para criar e excluir filas, além de inserir, inspecionar, obter e excluir mensagens de fila." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="Como usar o serviço de armazenamento de fila" authors="tamram" />

# Como usar o armazenamento de fila do .NET


Este guia mostrará a você como executar cenários comuns usando o
serviço de armazenamento Fila do Azure. As amostras estão escritas em código C#
e usam o cliente de armazenamento do Azure para .NET. Os cenários abrangidos incluem **inserir**, **exibir**, **obter** e **excluir** mensagens da fila, bem como **criar e excluir filas**. Para obter mais informações sobre filas, consulte a seção [Próximas etapas][Próximas etapas].

<h2>Sumário</h2>

-   [O que é Armazenamento de Filas][O que é Armazenamento de Filas]
-   [Conceitos][Conceitos]
-   [Criar uma conta de armazenamento do Azure][Criar uma conta de armazenamento do Azure]
-   [Configurar uma cadeia de conexão de armazenamento do Azure][Configurar uma cadeia de conexão de armazenamento do Azure]
-   [Como acessar filas de forma programática usando o .NET][Como acessar filas de forma programática usando o .NET]
-   [Como criar uma fila][Como criar uma fila]
-   [Como inserir uma mensagem em uma fila][Como inserir uma mensagem em uma fila]
-   [Como inspecionar a próxima mensagem][Como inspecionar a próxima mensagem]
-   [Como alterar o conteúdo de uma mensagem em fila][Como alterar o conteúdo de uma mensagem em fila]
-   [Como remover a próxima mensagem da fila][Como remover a próxima mensagem da fila]
-   [Como utilizar opções adicionais para remover mensagens da fila][Como utilizar opções adicionais para remover mensagens da fila]
-   [Como obter o comprimento da fila][Como obter o comprimento da fila]
-   [Como excluir uma fila][Como excluir uma fila]
-   [Próximas etapas][Próximas etapas]

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

<h2><a name="create-account"></a><span  class="short-header">Criar uma conta</span>Criar uma conta de armazenamento do Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a name="setup-connection-string"></a><span  class="short-header">Configurar uma cadeia de conexão</span>Configurar uma cadeia de conexão de armazenamento do Azure</h2>

A Biblioteca de Cliente de Armazenamento do Azure para .NET oferece suporte ao uso de uma
cadeia de conexão de armazenamento para configurar pontos de extremidade e credenciais
para acesso a serviços de armazenamento. Você pode colocar a cadeia de conexão de armazenamento em um arquivo de configuração, em vez de embuti-la no código:

- Ao usar os Serviços de Nuvem do Azure, é recomendável armazenar a cadeia de conexão usando o sistema de configuração de serviço do Azure (arquivos `*.csdef` e `*.cscfg`).
- Durante o uso de sites e máquinas virtuais do Azure ou a compilação de aplicativos .NET destinados a serem executados fora do Azure, é recomendável armazenar a cadeia de conexão usando-se o sistema de configuração do .NET (por exemplo, arquivo `web. config` ou `app. config`).

Nos dois casos, você pode recuperar a cadeia de conexão usando o método `CloudConfigurationManager.GetSetting`, conforme mostrado neste guia.

### Configurando a cadeia de conexão ao usar os Serviços de Nuvem

O mecanismo de configuração de serviço é exclusivo para projetos de
Serviços de Nuvem do Azure e permite que você altere dinamicamente
os parâmetros de configuração no Portal de Gerenciamento do Azure sem
reimplantar o aplicativo.

Para configurar a cadeia de conexão na configuração de serviço
do Azure:

1.  No Gerenciador de Soluções do Visual Studio, na pasta **Funções** de seu Projeto de Implantação do Azure, clique com o botão direito do mouse na sua função web ou função de trabalho e clique em **Propriedades**.  

	![Blob5](./media/storage-dotnet-how-to-use-queues-20/blob5.png)

2.  Clique na guia **Configurações** e pressione o botão **Adicionar Configuração**. 
 
	![Blob6](./media/storage-dotnet-how-to-use-queues-20/blob6.png)

	Uma nova entrada **Setting1** será mostrada na grade de configurações.

3.  No menu suspenso **Tipo** da nova entrada **Setting1**, escolha **Cadeia de Conexão**.  

	![Blob7](./media/storage-dotnet-how-to-use-queues-20/blob7.png)

4.  Clique no botão **...** na extremidade direita da entrada **Setting1**. A caixa de diálogo **Cadeia de Conexão da Conta de Armazenamento** será aberta.

5.  Escolha se deseja direcionar o emulador de armazenamento (o armazenamento do Azure simulado em sua máquina local) ou uma conta de armazenamento real na nuvem. O código deste guia funciona com qualquer uma dessas opções. Insira o valor **Chave de Acesso Primária** copiado na etapa anterior deste tutorial se você quiser armazenar dados da fila na conta de armazenamento que criamos anteriormente no Azure.   

	![Blob8](./media/storage-dotnet-how-to-use-queues-20/blob8.png)

6.  Altere a entrada **Nome** de **Setting1** para um nome mais amigável, como **StorageConnectionString**. Você fará referência a essa cadeia de conexão mais tarde no código deste guia.  

	![Blob9](./media/storage-dotnet-how-to-use-queues-20/blob9.png)
	
### Configurando sua cadeia de conexão usando a configuração do .NET

Se você estiver escrevendo um aplicativo que não seja um serviço de nuvem do Azure (consulte a seção anterior), será recomendável usar o sistema de configuração do .NET (por exemplo, `web.config` ou `app.config`).  Isso inclui sites ou máquinas virtuais do Azure, bem como aplicativos projetados para serem executados fora do Azure.  Você armazena a cadeia de conexão usando o elemento `<appSettings>` da seguinte maneira:

	<configuration>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey" />
  		</appSettings>
	</configuration>

Leia [Configurando cadeias de conexão][AccountKey" />
  		</appSettings>
	</configuration>

Leia [Configurando cadeias de conexão] para obter mais informações sobre cadeias de conexão de armazenamento.
	
Agora você está pronto para executar as tarefas das instruções deste guia.

<h2><a name="access"></a><span  class="short-header">Acessar de forma programática</span>Como acessar filas de forma programática usando o .NET</h2>

<h3>Obtendo o assembly</h3>
Você pode usar o NuGet para obter o assembly `Microsoft.WindowsAzure.Storage.dll`. Clique com o botão direito do mouse no **Gerenciador de Soluções** e escolha **Gerenciar Pacotes NuGet**  Pesquise online por "WindowsAzure.Storage" e clique em **Instalar** para instalar o pacote Armazenamento do Azure e as dependências.

`Microsoft.WindowsAzure.Storage.dll` também está incluído no SDK do Azure para .NET, que pode ser baixado na <a href="http://www.windowsazure.com/pt-br/develop/net/#">Central de desenvolvedores do .NET</a>. O assembly é instalado no diretório `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`.

<h3>Declarações de namespace</h3>
Adicione as seguintes declarações de namespace de código à parte superior de qualquer arquivo C\#
no qual você deseja acessar o Armazenamento do Azure programaticamente:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

Faça referência ao assembly `Microsoft.WindowsAzure.Storage.dll`.

<h3>Recuperando sua cadeia de conexão</h3>
Você pode usar o tipo **CloudStorageAccount** para representar
as informações da conta de armazenamento. Se estiver usando um modelo de projeto do Windows
Azure e/ou tiver uma referência a
Microsoft.WindowsAzure.CloudConfigurationManager, você
poderá usar o tipo **CloudConfigurationManager**
para recuperar a cadeia de conexão do armazenamento e as
informações da conta de armazenamento na configuração de serviço do Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Se estiver criando um aplicativo sem referência a Microsoft.WindowsAzure.CloudConfigurationManager e a cadeia de conexão estiver localizada no `web.config` ou no `app.config` conforme mostrado acima, você poderá usar **ConfigurationManager** para recuperar a cadeia de conexão.  Você precisará adicionar uma referência ao System.Configuration.dll a seu projeto e adicionar outra declaração de namespace para ele:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

<h3>Dependências do ODataLib</h3>
As dependências do ODataLib na Biblioteca de Cliente de Armazenamento para .NET são resolvidas por meio de pacotes do ODataLib (versão 5.0.2) disponíveis por meio do NuGet e não do WCF Data Services.  As bibliotecas do ODataLib podem ser baixadas diretamente ou referenciadas por seu projeto de código por meio do NuGet.  Os pacotes ODataLib específicos são [OData], [Edm] e [Spatial].

<h2><a name="create-queue"></a><span  class="short-header">Criar uma fila</span>Como criar uma fila</h2>

Um objeto **CloudQueueClient** permite que você obtenha objetos de referência para filas.
O código a seguir cria um objeto **CloudQueueClient**. Todos os códigos
neste guia usam uma cadeia de conexão de armazenamento
armazenada na configuração de serviço do aplicativo do Azure. Também existem outras maneiras de criar
um objeto **CloudStorageAccount**. Consulte a [CloudStorageAccount][CloudStorageAccount]
documentação para ver detalhes.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Use o objeto **queueClient** para obter uma referência à fila que você
deseja usar. Você poderá criar a fila se ela não existir.

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

<h2><a name="insert-message"> </a><span  class="short-header">Inserir uma mensagem</span>Como inserir uma mensagem em uma fila</h2>

Para inserir uma mensagem em uma fila existente, primeiro crie um
**CloudQueueMessage**. Em seguida, chame o método **AddMessage**. Um
**CloudQueueMessage** pode ser criado com base em uma cadeia de caracteres
(em formato UTF-8) ou em uma matriz de **bytes**. Este é o código que cria uma fila (se ela não existir) e insere a mensagem 'Hello, World':

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

<h2><a name="peek-message"></a><span  class="short-header">Inspecionar a próxima mensagem</span>Como inspecionar a próxima mensagem</h2>

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

<h2><a name="change-contents"></a><span  class="short-header">Alterar o conteúdo da mensagem</span>Como alterar o conteúdo de uma mensagem em fila</h2>

Você pode alterar o conteúdo de uma mensagem in-loco na fila. Se a mensagem representar uma tarefa de trabalho, você poderá usar esse recurso para atualizar o status da tarefa de trabalho. O código a seguir atualiza a mensagem da fila com novo conteúdo e define o tempo limite de visibilidade para estender mais 60 segundos. Isso salva o estado do trabalho associado à mensagem e dá ao cliente mais um minuto para continuar trabalhando na mensagem. Você pode usar essa técnica para acompanhar fluxos de trabalho de várias etapas em mensagens em fila, sem a necessidade de começar desde o início, caso uma etapa de processamento falhar devido a uma falha de hardware ou de software. Normalmente, você mantém uma contagem de repetições e, se a mensagem for repetida mais de *n* vezes, você a exclui. Isso protege contra uma mensagem que dispare um erro do aplicativo sempre que for processada.

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

<h2><a name="get-message"></a><span  class="short-header">Remover a próxima mensagem da fila</span>Como remover a próxima mensagem da fila</h2>

Seu código remove uma mensagem de um fila em duas etapas. Ao chamar
**GetMessage**, você recebe a próxima mensagem em uma fila. As mensagens retornadas
de **GetMessage** se tornam invisíveis para todas as outras mensagens de leitura
de código desta fila. Por padrão, essa mensagem permanece invisível por 30 segundos. Para concluir a remoção da mensagem da fila, você também deve
chamar **DeleteMessage**. Esse processo de duas etapas de remoção de uma mensagem garante que quando o código não processar uma mensagem devido a falhas de hardware ou de software, outra instância do código possa receber a mesma mensagem e tentar novamente. Seu código chama **DeleteMessage** logo depois que a
mensagem é processada.

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

<h2><a name="advanced-get"></a><span  class="short-header">Mais opções de remoção da fila</span>Como utilizar opções adicionais para remover mensagens da fila</h2>

Há duas maneiras de personalizar a recuperação da mensagem de uma fila.
Primeiro, você pode obter um lote de mensagens (até 32). Em segundo lugar, você pode definir um tempo limite de invisibilidade mais longo ou mais curto, permitindo mais ou menos tempo para o código processar completamente cada mensagem. O exemplo de código a seguir usa o método
**GetMessages** para receber 20 mensagens em uma chamada. Em seguida, processa
cada mensagem usando um loop **foreach**. Ele também define o tempo limite de invisibilidade para cinco minutos para cada mensagem. Observe que os cinco minutos começam
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

<h2><a name="get-queue-length"></a><span  class="short-header">Obter o comprimento da fila</span>Como obter o comprimento da fila</h2>

Você pode obter uma estimativa do número de mensagens em uma fila. O
método **FetchAttributes** pede para que o serviço Fila
recupere os atributos da fila, inclusive a contagem de mensagens. A propriedade **ApproximateMethodCount**
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

<h2><a name="delete-queue"></a><span  class="short-header">Excluir uma fila</span>Como excluir uma fila</h2>

Para excluir uma fila e todas as mensagens contidas nela, chame o
método **Delete** no objeto queue.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

<h2><a name="next-steps"></a>Próximas etapas</h2>

Agora que você aprendeu os conceitos básicos do armazenamento de fila, siga estes links para saber como executar tarefas de armazenamento mais complexas.

<ul>
<li>Consulte a documentação de referência do serviço Fila para obter detalhes completos sobre as APIs disponíveis:
  <ul>
    <li><a href="http://msdn.microsoft.com/pt-br/library/windowsazure/dn495001(v=azure.10).aspx">Referência à Biblioteca de Cliente de Armazenamento para .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/pt-br/library/windowsazure/dd179355">Referência da API REST</a></li>
  </ul>
</li>
<li>Conheça tarefas mais avançadas que você pode executar com o Armazenamento do Azure em <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx">Armazenando e acessando dados no Azure</a> (a página pode estar em inglês).</li>
<li>Exibir mais guias de recursos para obter informações sobre opções adicionais para armazenar dados no Azure.
  <ul>
    <li>Usar o <a href="/pt-br/develop/net/how-to-guides/table-services/">Armazenamento de Tabela</a> para armazenar dados estruturados.</li>
    <li>Usar o <a href="/pt-br/develop/net/how-to-guides/blob-storage/">Armazenamento do Blob</a> para armazenar dados não estruturados.</li>
    <li>Usar o <a href="/pt-br/develop/net/how-to-guides/sql-database/">Banco de Dados SQL</a> para armazenar dados relacionais.</li>
  </ul>
</li>
</ul>



  [Próximas etapas]: #next-steps
  [O que é Armazenamento de Filas]: #what-is
  [Conceitos]: #concepts
  [Criar uma conta de armazenamento do Azure]: #create-account
  [Configurar uma cadeia de conexão de armazenamento do Azure]: #setup-connection-string
  [Como acessar filas de forma programática usando o .NET]: #access
  [Como criar uma fila]: #create-queue
  [Como inserir uma mensagem em uma fila]: #insert-message
  [Como inspecionar a próxima mensagem]: #peek-message
  [Como alterar o conteúdo de uma mensagem em fila]: #change-contents
  [Como remover a próxima mensagem da fila]: #get-message
  [Como utilizar opções adicionais para remover mensagens da fila]: #advanced-get
  [Como obter o comprimento da fila]: #get-queue-length
  [Como excluir uma fila]: #delete-queue
  [CloudStorageAccount]: http://msdn.microsoft.com/pt-br/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [Configurando cadeias de conexão]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2

