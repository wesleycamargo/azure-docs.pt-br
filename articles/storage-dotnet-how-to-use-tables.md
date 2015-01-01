<properties urlDisplayName="Table Service" pageTitle="Como usar o armazenamento de tabela do .NET | Microsoft Azure" metaKeywords="Get started Azure table   Azure nosql   Azure large structured data store   Azure table   Azure table storage   Azure table .NET   Azure table storage .NET   Azure table C#   Azure table storage C#" description="Learn how to use Microsoft Azure Table storage to create and delete tables and insert and query entities in a table." services="storage" documentationCenter=".NET" metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="How to use Microsoft Azure Table storage" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/10/2014" ms.author="tamram" />


# Como usar o Armazenamento de Tabela no .NET


Este guia mostra como executar cenários comuns usando o serviço de Armazenamento de tabelas no Azure. Os exemplos são escritos em C# e usam a biblioteca do cliente de armazenamento do Azure para .NET. Os cenários abrangidos incluem a **criação e a exclusão de uma tabela** e o **trabalho com entidades de tabela**. Para obter mais informações sobre tabelas, consulte a seção [Próximas etapas][].

> [WACOM.NOTE] Este guia tem como alvo a Biblioteca do Cliente de Armazenamento .NET do Azure, versão 2.x e superior. A versão recomendada é a Biblioteca de Cliente de Armazenamento 4.x, que está disponível via [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) ou como parte do [SDK do Azure para .NET](/pt-br/downloads/). Consulte [Como: Acessar programaticamente o armazenamento de tabela][] abaixo para mais detalhes sobre como obter a biblioteca do cliente de armazenamento.

## Sumário

-   [O que é o serviço Tabela][]
-   [Conceitos][]
-   [Criar uma conta de Armazenamento do Azure][]
-   [Configurar uma cadeia de conexão de armazenamento][]
-   [Como: Acessar programaticamente o armazenamento de tabela][]
-   [Como: Criar uma tabela][]
-   [Como: Adicionar uma entidade a uma tabela][]
-   [Como: Inserir um lote de entidades][]
-   [Como: Recuperar todas as entidades em uma partição][]
-   [Como: Recuperar um intervalo de entidades em uma partição][]
-   [Como: Recuperar uma única entidade][]
-   [Como: Substituir uma entidade][]
-   [Como: Inserir ou substituir uma entidade][]
-   [Como: consultar um subconjunto de propriedades da entidade][]
-   [Como: Excluir uma entidade][]
-   [Como: Excluir uma tabela][]
-   [Próximas etapas][]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

## <h2><a name="create-account"></a>Criar uma conta de Armazenamento do Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <h2><a name="setup-connection-string"></a>Configurar uma cadeia de conexão de armazenamento</h2>

[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <h2> <a name="configure-access"> </a>Como: Acessar programaticamente o armazenamento de tabela</h2>

<h3>Obtendo o assembly</h3>
Você pode usar o NuGet para obter o assembly `Microsoft.WindowsAzure.Storage.dll`. Clique com o botão direito do mouse no **Gerenciador de Soluções** e escolha **Gerenciar Pacotes NuGet**.  Pesquise online por "MicrosoftAzure.Storage" e clique em **Instalar** para instalar o pacote Armazenamento do Azure e as dependências.

'Microsoft.WindowsAzure.Storage.dll' também está incluído no SDK do Azure para .NET, que pode ser baixado na <a href="http://www.windowsazure.com/pt-br/develop/net/#">Central de desenvolvedores do .NET</a>. O assembly é instalado no diretório `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`.

<h3>Declarações de namespace</h3>
Adicione as seguintes declarações de namespace do código à parte superior de qualquer arquivo C\#
em que você deseja acessar o armazenamento do Azure programaticamente:

    using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;

Faça referência ao assembly `Microsoft.WindowsAzure.Storage.dll`.

<h3>Recuperando sua cadeia de conexão</h3>
Você pode usar o tipo **CloudStorageAccount** para representaras informações de sua Conta de Armazenamento. Se você estiver usando um modelo de projeto do WindowsAzure e/ou tiver uma referência ao namespace Microsoft.WindowsAzure.CloudConfigurationManager, poderá usar o tipo **CloudConfigurationManager** para recuperar a cadeia de conexão do armazenamento e as informações de conta de armazenamento da configuração de serviços do Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Se estiver criando um aplicativo sem referência a Microsoft.WindowsAzure.CloudConfigurationManager e a cadeia de conexão estiver localizada no web.config ou no app.config conforme mostrado acima, você poderá usar **ConfigurationManager** para recuperar a cadeia de conexão.Você precisará adicionar uma referência ao System.Configuration.dll a seu projeto e adicionar outra declaração de namespace para ele:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

<h3>Dependências do ODataLib</h3>
As dependências do ODataLib na Biblioteca de Cliente de Armazenamento para .NET são resolvidas por meio de pacotes do ODataLib (versão 5.0.2) disponíveis por meio do NuGet e não do WCF Data Services.  As bibliotecas do ODataLib podem ser baixadas diretamente ou referenciadas por seu projeto de código por meio do NuGet.  Os pacotes ODataLib específicos são [OData], [Edm] e [Spatial].

<h2><a name="create-table"></a>Como: Criar uma tabela</h2>

Um objeto **CloudTableClient** permite obter os objetos de referência para tabelas e entidades. O código a seguir cria um objeto **CloudTableClient** e o utiliza para criar uma nova tabela.Todos os códigos deste guia pressupõe que o aplicativo que está sendo criado é um projeto do Serviço de Nuvem do Azure e usa uma cadeia de conexão de armazenamento armazenada na configuração de serviço do aplicativo do Azure.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the table if it doesn't exist.
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();

<h2><a name="add-entity"></a>Como: Adicionar uma entidade a uma tabela</h2>

As entidades mapeiam para objetos C\# usando uma classe personalizada derivada de**TableEntity**. Para adicionar uma entidade a uma tabela, crie uma classe que defina as propriedades da sua entidade. O código a seguir define uma classe de entidade que usa o nome do cliente como a chave de linha e o sobrenome como a chave de partição. Juntas, uma chave de partição e uma chave de linha identificam exclusivamente a entidade na tabela.  As entidades com a mesma chave de partição podem ser consultadas mais rápido do que aquelas com chaves de partição diferentes, mas usar chaves de partição diferentes possibilita uma maior escalabilidade de operação paralela.Em qualquer propriedade que deva ser armazenada no serviço tabela, a propriedade deve ser uma propriedade pública de um tipo com suporte que exponha 'get' e 'set'.
Além disso, o tipo de entidade *deve* expor um construtor sem parâmetros.

    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }
		
        public CustomerEntity() { }

        public string Email { get; set; }

        public string PhoneNumber { get; set; }
    }

Operações de tabela que envolvem entidades são executadas usando o objeto **CloudTable** que você criou em "Como:  Criar uma tabela".A operação a ser executada é representada por um objeto **TableOperation**.  O exemplo de código a seguir mostra a criação do objeto **CloudTable** e, em seguida, de um objeto **CustomerEntity**.  Para preparar a operação, um **TableOperation** é criado para inserir a entidade de cliente na tabela.  Finalmente, a operação é executada chamando **CloudTable.Execute**.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	// Create the CloudTable object that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    table.Execute(insertOperation);

<h2><a name="insert-batch"></a>Como: Inserir um lote de entidades</h2>

Você pode inserir um lote de entidades em uma tabela em uma única operação de gravação. Algumas outras observações sobre operações em lote:

1.  Você pode executar atualizações, exclusões e inserções em uma única operação em lote.
2.  Uma única operação em lote pode incluir até 100 entidades.
3.  Todas as entidades em uma única operação em lote devem ter a mesma chave de partição.
4.  Embora seja possível executar uma consulta como uma operação em lotes, ele deve ser a única operação em lote.

<!-- -->
O exemplo de código a seguir cria dois objetos de entidade e adiciona cada uma um **TableBatchOperation** usando o método **Insert**. Em seguida, **CloudTable.Execute** é chamado para executar a operação.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
	// Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a customer entity and add it to the table.
	CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
	customer1.Email = "Jeff@contoso.com";
	customer1.PhoneNumber = "425-555-0104";
            
	// Create another customer entity and add it to the table.
	CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
	customer2.Email = "Ben@contoso.com";
	customer2.PhoneNumber = "425-555-0102";
            
	// Add both customer entities to the batch insert operation.
	batchOperation.Insert(customer1);
	batchOperation.Insert(customer2);

	// Execute the batch operation.
	table.ExecuteBatch(batchOperation);

<h2><a name="retrieve-all-entities"></a>Como: Recuperar todas as entidades em uma partição</h2>

Para consultar uma tabela de todas as entidades em uma partição, use um objeto **TableQuery**.
O exemplo de código a seguir especifica um filtro para as entidades em que 'Smith' é a chave de partição. Esse exemplo imprime os campos de cada entidade nos resultados da consulta no console.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    foreach (CustomerEntity entity in table.ExecuteQuery(query))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

<h2><a name="retrieve-range-entities"></a>Como: Recuperar um intervalo de entidades em uma partição</h2>

Se não desejar consultar todas as entidades em uma partição, você poderá especificar um intervalo combinando o filtro de chave de partição com um filtro de chave de linha. O exemplo de código a seguir usa dois filtros para obter todas as entidades na partição 'Smith' onde a chave de linha (nome) começa com uma letra anterior a 'E' do alfabeto e, em seguida, imprime os resultados da consulta.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

	// Create the table query.
    TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

    // Loop through the results, displaying information about the entity.
    foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

<h2><a name="retrieve-single-entity"></a>Como: Recuperar uma única entidade</h2>

Você pode escrever uma consulta para recuperar uma entidade única e específica. Código a seguir usa um **TableOperation** para especificar o cliente 'Ben Smith'.
Esse método retorna uma única entidade, em vez de uma coleção, e o valor retornado no**TableResult.Result** é um objeto **CustomerEntity**.
A especificação de chaves de partição e de linha em uma consulta é a maneira mais rápida de 
recuperar uma única entidade do serviço Tabela.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Print the phone number of the result.
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("The phone number could not be retrieved.");

<h2><a name="replace-entity"></a>Como: Substituir uma entidade</h2>

Para atualizar uma entidade, recupere-a do serviço Tabela, modifique o objeto de entidade e, em seguida, salve as alterações novamente no serviço tabela.   O código a seguir altera o número de telefone de um cliente existente.   Em vez de chamar **Insert**, esse código chama**Replace**.Isso faz com que a entidade seja totalmente substituída no servidor, a menos que a entidade no servidor tenha sido alterada desde que foi recuperada, caso em que haverá falha na operação.Essa falha é para impedir que seu aplicativo substitua inadvertidamente uma alteração feita entre a recuperação e a atualização por outro componente de seu aplicativo.A manipulação correta dessa falha é recuperar a entidade novamente, fazer as alterações (se ainda válidas) executar outra operação **Replace**.A próxima seção mostrará como substituir esse comportamento.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
	{
	   // Change the phone number.
	   updateEntity.PhoneNumber = "425-555-0105";

	   // Create the InsertOrReplace TableOperation
	   TableOperation updateOperation = TableOperation.Replace(updateEntity);

	   // Execute the operation.
	   table.Execute(updateOperation);

	   Console.WriteLine("Entity updated.");
	}

	else
	   Console.WriteLine("Entity could not be retrieved.");

<h2><a name="insert-or-replace-entity"></a>Como: Inserir ou substituir uma entidade</h2>

Haverá falha nas operações **Replace** se a entidade tiver sido alterada desde que foi recuperada do servidor.      Além disso, você deve primeiro recuperar a entidade do servidor para que o **Replace** seja bem-sucedido.No entanto, às vezes você não sabe se a entidade existe no servidor e se os valores atuais armazenados nela são irrelevantes sua atualização deve substituir todos eles.Para fazer isso, você usa uma operação **InsertOrReplace**.Essa operação insere a entidade, se ela não existir, ou a substitui, se ela existir, independentemente de quando foi feita a última atualização.No exemplo de código a seguir, a entidade customer de Ben Smith ainda é recuperada, mas ela é salva novamente no servidor usando **InsertOrReplace**.Todas as atualizações feitas na entidade entre a operação de recuperação e de atualização serão substituídas.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
	{
	   // Change the phone number.
	   updateEntity.PhoneNumber = "425-555-1234";

	   // Create the InsertOrReplace TableOperation
	   TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);

	   // Execute the operation.
	   table.Execute(insertOrReplaceOperation);

	   Console.WriteLine("Entity was updated.");
	}

	else
	   Console.WriteLine("Entity could not be retrieved.");

<h2><a name="query-entity-properties"></a>Como: consultar um subconjunto de propriedades da entidade</h2>

Uma consulta de tabela pode recuperar apenas algumas propriedades de uma entidade em vez de todas as propriedades da entidade. Essa técnica, chamada projeção, reduz a largura de banda e pode melhorar o desempenho da consulta, principalmente para grandes entidades. A consulta no código a seguir retorna somente os endereços de email das entidades na tabela. Isso é feito por meio de uma consulta de **DynamicTableEntity** e também **EntityResolver**. Você pode aprender mais sobre projeção nesta [postagem de blog][].Observe que a projeção não é compatível com o emulador de armazenamento local, portanto, esse código é executado somente ao usar uma conta no serviço tabela.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Define the query, and only select the Email property
    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

    // Define an entity resolver to work with the entity after retrieval.
    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
    {
        Console.WriteLine(projectedEmail);
    }

<h2><a name="delete-entity"></a>Como: Excluir uma entidade</h2>

Você pode excluir facilmente uma entidade após a recuperação usando o mesmo padrão mostrado para a atualização de uma entidade.  O código a seguir recupera e exclui uma entidade de cliente.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation.
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

	   // Execute the operation.
	   table.Execute(deleteOperation);

	   Console.WriteLine("Entity deleted.");
	}

	else
	   Console.WriteLine("Could not retrieve the entity.");

<h2><a name="delete-table"></a>Como: Excluir uma tabela</h2>

Finalmente, o exemplo de código a seguir exclui uma tabela de uma conta de armazenamento. Uma tabela que tenha sido excluída não estará disponível para ser recriada por um período de tempo após a exclusão.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Delete the table it if exists.
    table.DeleteIfExists();

<h2><a name="next-steps"></a>Próximas etapas</h2>

Agora que você aprendeu os conceitos básicos do armazenamento de tabela, siga estes links
para saber como fazer tarefas mais complexas de armazenamento.

<ul>
<li>Consulte a documentação de referência do serviço Tabela para obter detalhes completos sobre as APIs disponíveis:
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
    <li>Use <a href="/pt-br/documentation/articles/storage-dotnet-how-to-use-blobs/">Armazenamento de Blob</a> para armazenar dados não estruturados.</li>
    <li>Use <a href="/pt-br/documentation/articles/storage-dotnet-how-to-use-queues/">Armazenamento de Fila</a> para armazenar dados estruturados.</li>
    <li>Use <a href="/pt-br/documentation/articles/sql-database-dotnet-how-to-use/">Banco de dados SQL</a> para armazenar dados relacionais.</li>
  </ul>
</li>
</ul>

  [Próximas etapas]: #next-steps
  [O que é o serviço Tabela]: #what-is
  [Conceitos]: #concepts
  [Criar uma conta de armazenamento do Azure]: #create-account
  [Criar um projeto do Azure no Visual Studio]: #create-project
  [Configurar seu aplicativo para acessar o armazenamento]: #configure-access
  [Configurar uma cadeia de conexão de armazenamento]: #setup-connection-string
  [Como: Acessar programaticamente o armazenamento de tabela]: #configure-access
  [Como: Criar uma tabela]: #create-table
  [Como: Adicionar uma entidade a uma tabela]: #add-entity
  [Como: Inserir um lote de entidades]: #insert-batch
  [Como: Recuperar todas as entidades em uma partição]: #retrieve-all-entities
  [Como: Recuperar um intervalo de entidades em uma partição]: #retrieve-range-entities
  [Como: Recuperar uma única entidade]: #retrieve-single-entity
  [Como: Substituir uma entidade]: #replace-entity
  [Como: Inserir ou substituir uma entidade]: #insert-or-replace-entity
  [Como: Consultar um subconjunto de propriedades da entidade]: #query-entity-properties
  [Como: Excluir uma entidade]: #delete-entity
  [Como: Excluir uma tabela]: #delete-table
  [Baixar e instalar o SDK do Azure para .NET]: /pt-br/develop/net/
  [Criando um projeto do Azure no Visual Studio]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee405487.aspx
  
  [Blob5]: ./media/storage-dotnet-how-to-use-table-storage/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-table-storage/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-table-storage/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-table-storage/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-table-storage/blob9.png
  
  [postagem no blog]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [Referência à Biblioteca cliente do .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx
  [Blog da equipe do Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configurando cadeias de conexão]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2

<!--HONumber=35_1-->
