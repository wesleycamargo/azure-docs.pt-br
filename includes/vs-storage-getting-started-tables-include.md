#####Criar uma tabela
Um objeto **CloudTableClient** permite obter os objetos de referência para tabelas e entidades. O código a seguir cria um objeto **CloudTableClient** e o utiliza para criar uma nova tabela. O código tenta fazer referência a uma tabela denominada “people”. Se uma tabela com esse nome não for localizada, ela será criada.

**OBSERVAÇÃO**: todos os códigos deste guia pressupõe que o aplicativo que está sendo criado é um projeto do Serviço de Nuvem do Azure e usa uma cadeia de conexão de armazenamento armazenada na configuração de serviço do aplicativo do Azure.

	// Create the table client.
	CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	// Create the table if it doesn't exist.
	CloudTable table = tableClient.GetTableReference("people");
	table.CreateIfNotExists();

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

Operações de tabela que envolvem entidades são executadas usando o objeto **CloudTable** criado anteriormente em "Criar uma tabela". O objeto **TableOperation** representa a operação a ser realizada. O exemplo de código a seguir mostra como criar um objeto **CloudTable** e um objeto **CustomerEntity**. Para preparar a operação, um **TableOperation** é criado para inserir a entidade de cliente na tabela. Finalmente, a operação é executada chamando CloudTable.Execute.

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

#####Inserir um lote de entidades
Você pode inserir várias entidades em uma tabela em uma única operação de gravação. O exemplo de código a seguir cria dois objetos de entidade ("Jeff Smith" e "Ben Smith") e adiciona cada um a um objeto **TableBatchOperation** usando o método Insert, iniciando então a operação chamando CloudTable.Execute.

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

#####Obter todas as entidades em uma partição
Para consultar uma tabela de todas as entidades em uma partição, use um objeto **TableQuery**. O exemplo de código a seguir especifica um filtro para entidades onde 'Smith' é a chave da partição. Esse exemplo imprime os campos de cada entidade nos resultados da consulta no console.

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

#####Obter uma única entidade
Você pode escrever uma consulta para obter uma entidade única e específica. O código a seguir usa um objeto **TableOperation** para especificar o cliente chamado 'Ben Smith'. Esse método retorna uma única entidade, em vez de uma coleção, e o valor retornado no TableResult.Result é um objeto **CustomerEntity**. Especificar chaves de partição e de linha em uma consulta é a maneira mais rápida de recuperar uma única entidade de serviço **Table**.

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
	   table.Execute(deleteOperation);
	
	   Console.WriteLine("Entity deleted.");
	}
	
	else
	   Console.WriteLine("Couldn't delete the entity.");

[Saiba mais sobre o armazenamento do Azure](http://azure.microsoft.com/documentation/services/storage/) Veja também [Procurando recursos de armazenamento no Gerenciador de servidores](http://msdn.microsoft.com/library/azure/ff683677.aspx).

<!---HONumber=62-->