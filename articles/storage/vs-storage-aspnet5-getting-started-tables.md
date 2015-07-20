<properties 
	pageTitle="Introdução ao Armazenamento do Azure" 
	description="Como começar a usar o armazenamento de tabela do Azure em um projeto do ASP.NET 5 no Visual Studio" 
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
	ms.date="04/20/2015" 
	ms.author="patshea123"/>

# Introdução ao armazenamento do Azure (Projetos ASP.NET vNext)

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet5-getting-started-tables.md)
> - [What Happened](vs-storage-aspnet5-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet5-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet5-getting-started-queues.md)
> - [Tables](vs-storage-aspnet5-getting-started-tables.md)

O serviço de armazenamento de Tabela do Azure armazena grandes quantidades de dados estruturados. O serviço é um repositório de dados NoSQL que aceita chamadas autenticadas de dentro e de fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais. Confira [Como usar o Armazenamento de Tabelas do .NET](storage-dotnet-how-to-use-tables.md/#create-table "Como usar o Armazenamento de Tabela no .NET") para saber mais.

Para acessar programaticamente tabelas em projetos do ASP.NET 5, você precisa adicionar os itens a seguir, se eles ainda não existirem.

1. Adicione a seguinte declaração de namespace de código à parte superior de qualquer arquivo C# no qual você deseja acessar o Armazenamento do Azure por meio de programação.

		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Table;
		using Microsoft.Framework.Configuration;
		using System.Threading.Tasks;
		using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. Use o seguinte código para obter a definição da configuração.

		IConfigurationSourceRoot config = new Configuration()
                .AddJsonFile("config.json")
                .AddEnvironmentVariables();

#####Obter a cadeia de conexão do armazenamento
Antes de fazer qualquer coisa com uma tabela, é necessário obter a cadeia de conexão da conta de armazenamento na qual as tabelas residirão. Você pode usar o tipo **CloudStorageAccount** para representar as informações da sua conta de armazenamento. Se estiver usando um projeto ASP.NET vNext, você poderá chamar o método do objeto Configuration para obter sua cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração de serviço do Azure, como mostrado no código a seguir.

**OBSERVAÇÃO:** as APIs que executam chamadas para o armazenamento do Azure no ASP.NET 5 são assíncronas. Confira [Programação assíncrona com Async e Await](http://msdn.microsoft.com/library/hh191443.aspx) para saber mais. O código a seguir pressupõe que os métodos de programação assíncrona estão sendo usados.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

#####Criar uma tabela
Um objeto **CloudTableClient** permite obter os objetos de referência para tabelas e entidades. O código a seguir cria um objeto **CloudTableClient** e o utiliza para criar uma nova tabela. O código tenta fazer referência a uma tabela denominada “people”. Se uma tabela com esse nome não for localizada, ela será criada.

**OBSERVAÇÃO**: todos os códigos deste guia pressupõe que o aplicativo que está sendo criado é um projeto do Serviço de Nuvem do Azure e usa uma cadeia de conexão de armazenamento armazenada na configuração de serviço do aplicativo do Azure. Além disso, use todo esse código antes do código nas seções a seguir.

	// Create the table client.
	CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	// Create the table if it doesn't exist.
	CloudTable table = tableClient.GetTableReference("people");
	await table.CreateIfNotExistsAsync();

#####Adicionar uma entidade a uma tabela
Para adicionar uma entidade a uma tabela, crie uma classe que defina as propriedades da sua entidade. O código a seguir define uma classe chamada **CustomerEntity** que usa o nome do cliente como a chave de linha e o sobrenome como a chave de partição.

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

Operações de tabela que envolvem entidades são executadas usando o objeto **CloudTable** criado anteriormente em "Criar uma tabela". O objeto **TableOperation** representa a operação a ser realizada. O exemplo de código a seguir mostra como criar um objeto **CloudTable** e um objeto **CustomerEntity**. Para preparar a operação, um **TableOperation** é criado para inserir a entidade de cliente na tabela. Finalmente, a operação é executada chamando CloudTable.ExecuteAsync.

	// Create a new customer entity.
	CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
	customer1.Email = "Walter@contoso.com";
	customer1.PhoneNumber = "425-555-0101";
	
	// Create the TableOperation that inserts the customer entity.
	TableOperation insertOperation = TableOperation.Insert(customer1);
	
	// Execute the insert operation.
	await table.ExecuteAsync(insertOperation);

#####Inserir um lote de entidades
Você pode inserir várias entidades em uma tabela em uma única operação de gravação. O exemplo de código a seguir cria dois objetos de entidade ("Jeff Smith" e "Ben Smith") e adiciona cada um a um objeto **TableBatchOperation** usando o método Insert, iniciando então a operação chamando CloudTable.ExecuteBatchAsync.

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
	await table.ExecuteBatchAsync(batchOperation);

#####Obter todas as entidades em uma partição
Para consultar uma tabela de todas as entidades em uma partição, use um objeto **TableQuery**. O exemplo de código a seguir especifica um filtro para entidades onde 'Smith' é a chave da partição. Esse exemplo imprime os campos de cada entidade nos resultados da consulta no console.

	// Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
    	TableQuerySegment<CustomerEntity> resultSegment = await table.ExecuteQuerySegmentedAsync(query, token);
		token = resultSegment.ContinuationToken;

		foreach (CustomerEntity entity in resultSegment.Results)
    	{
    		Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
    		entity.Email, entity.PhoneNumber);
        }
    } while (token != null);

    return View();


#####Obter uma única entidade
Você pode escrever uma consulta para obter uma entidade única e específica. O código a seguir usa um objeto **TableOperation** para especificar o cliente chamado 'Ben Smith'. Esse método retorna uma única entidade, em vez de uma coleção, e o valor retornado no TableResult.Result é um objeto **CustomerEntity**. Especificar chaves de partição e de linha em uma consulta é a maneira mais rápida de recuperar uma única entidade de serviço **Table**.

	// Create the CloudTable object that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");
	
	// Create a retrieve operation that takes a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	
	// Execute the retrieve operation.
	TableResult retrievedResult = await table.ExecuteAsync(retrieveOperation);
	
	// Print the phone number of the result.
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("The phone number could not be retrieved.");

#####Excluir uma entidade
Você poderá excluir uma entidade facilmente depois de encontrá-la. O código a seguir busca uma entidade de cliente chamada "Ben Smith", excluindo-a caso a encontre.

	// Create the CloudTable that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");
	
	// Create a retrieve operation that expects a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	
	// Execute the operation.
	TableResult retrievedResult = table.Execute(retrieveOperation);
	
	// Assign the result to a CustomerEntity object.
	CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;
	
	// Create the Delete TableOperation and then execute it.
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
	
	   // Execute the operation.
	   await table.ExecuteAsync(deleteOperation);
	
	   Console.WriteLine("Entity deleted.");
	}
	
	else
	   Console.WriteLine("Couldn't delete the entity.");

[Saiba mais sobre o armazenamento do Azure](http://azure.microsoft.com/documentation/services/storage/) Confira também [Procurando recursos de armazenamento no Gerenciador de servidores](http://msdn.microsoft.com/library/azure/ff683677.aspx) e [ASP.NET 5](http://www.asp.net/vnext).
 

<!---HONumber=July15_HO2-->