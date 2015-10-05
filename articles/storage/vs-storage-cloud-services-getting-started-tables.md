<properties
    pageTitle="Introdução ao armazenamento de tabela e aos serviços conectados do Visual Studio (serviços de nuvem) | Microsoft Azure"
	description="Como começar a usar o armazenamento de Tabela do Azure em um projeto de serviço de nuvem no Visual Studio após a conexão a uma conta de armazenamento usando os serviços conectados do Visual Studio"
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
	ms.date="09/03/2015"
	ms.author="patshea"/>

# Introdução ao armazenamento de tabela do Azure e aos serviços conectados do Visual Studio (projetos de serviços de nuvem)

> [AZURE.SELECTOR]
> - [Getting started](vs-storage-cloud-services-getting-started-tables.md)
> - [What happened](vs-storage-cloud-services-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-cloud-services-getting-started-blobs.md)
> - [Queues](vs-storage-cloud-services-getting-started-queues.md)
> - [Tables](vs-storage-cloud-services-getting-started-tables.md)

##Visão geral

Este artigo descreve como começar a usar o armazenamento de tabela do Azure no Visual Studio depois de ter criado ou referenciado uma conta de armazenamento do Azure em um projeto de serviços de nuvem usando a caixa de diálogo **Adicionar Serviços Conectados** do Visual Studio. A operação **Adicionar Serviços Conectados** instala os pacotes NuGet apropriados para acessar o armazenamento do Azure no seu projeto e adiciona a cadeia de conexão para a conta de armazenamento aos arquivos de configuração do projeto.

O serviço de armazenamento de Tabela do Azure armazena grandes quantidades de dados estruturados. O serviço é um repositório de dados NoSQL que aceita chamadas autenticadas de dentro e de fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais.

Para começar, primeiramente, você precisa criar uma tabela em sua conta de armazenamento. Mostraremos como criar uma tabela do Azure com código e também como realizar operações básicas de tabela e entidade, como adicionar, modificar, ler e ler entidades de tabela. Os exemplos são escritos em C# e usam a [biblioteca do cliente de armazenamento do Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

**OBSERVAÇÃO:** algumas APIs que executam chamadas para o armazenamento do Azure são assíncronas. Confira [Programação assíncrona com Async e Await](http://msdn.microsoft.com/library/hh191443.aspx) para saber mais. O código a seguir pressupõe que os métodos de programação assíncrona estão sendo usados.

- Confira [Como usar o armazenamento de tabela do .NET](storage-dotnet-how-to-use-tables.md) para obter mais informações sobre como manipular tabelas programaticamente.
- Consulte a [documentação de armazenamento](https://azure.microsoft.com/documentation/services/storage/) para obter informações gerais sobre o armazenamento do Azure.
- Consulte a [documentação de serviços de nuvem](http://azure.microsoft.com/documentation/services/cloud-services/) para obter informações gerais sobre os serviços de nuvem do Azure.
- Consulte [ASP.NET](http://www.asp.net) para obter mais informações sobre como programar aplicativos ASP.NET.

## Acessar tabelas em código

Para acessar tabelas em projetos de serviço de nuvem, você precisa incluir os itens a seguir para quaisquer arquivos de origem de C# que acessam o armazenamento de tabela do Azure.

1. Verifique se as declarações de namespace na parte superior do arquivo C# incluem estas instruções de **uso**.

		using Microsoft.Framework.Configuration;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Table;
		using System.Threading.Tasks;
		using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. Obtenha um objeto **CloudStorageAccount** que represente as informações da conta de armazenamento. Use o seguinte código para obter a cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure.

		 CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		   CloudConfigurationManager.GetSetting("<storage account name>
         _AzureStorageConnectionString"));
> [AZURE.NOTE]Use todo o código acima antes do código dos exemplos a seguir.

3. Obtenha um objeto **CloudTableClient** para fazer referência aos objetos de tabela em sua conta de armazenamento.

         // Create the table client.
         CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

4. Obtenha um objeto de referência **CloudTable** para fazer referência a entidades e a uma tabela específica.

    	// Get a reference to a table named "peopleTable".
	    CloudTable table = tableClient.GetTableReference("peopleTable");

## Criar uma tabela em código

Para criar a tabela do Azure, basta adicionar uma chamada para **CreateIfNotExistsAsync** após obter um objeto **CloudTable**, como descrito na seção “Acessar tabelas no código”.

	// Create the CloudTable if it does not exist.
	await table.CreateIfNotExistsAsync();

## Adicionar uma entidade a uma tabela

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

As operações de tabela que envolvem entidades são executadas usando o objeto **CloudTable**, criado anteriormente em "Acessar tabelas no código". O objeto **TableOperation** representa a operação a ser realizada. O exemplo de código a seguir mostra como criar um objeto **CloudTable** e um objeto **CustomerEntity**. Para preparar a operação, um **TableOperation** é criado para inserir a entidade de cliente na tabela. Finalmente, a operação é executada chamando **CloudTable.ExecuteAsync**.

	// Get a reference to the **CloudTable** object named 'peopleTable' as described in "Access a table in code".

	// Create a new customer entity.
	CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
	customer1.Email = "Walter@contoso.com";
	customer1.PhoneNumber = "425-555-0101";

	// Create the TableOperation that inserts the customer entity.
	TableOperation insertOperation = TableOperation.Insert(customer1);

	// Execute the insert operation.
	await peopleTable.ExecuteAsync(insertOperation);

## Inserir um lote de entidades

Você pode inserir várias entidades em uma tabela em uma única operação de gravação. O exemplo de código a seguir cria dois objetos de entidade ("Mateus Rodrigues" e "Alberto Rodrigues") e os adiciona a um objeto **TableBatchOperation** usando o método Insert e iniciando a operação ao chamar **CloudTable.ExecuteBatchAsync**.

	// Get a reference to a **CloudTable** object named 'peopleTable' as described in "Access a table in code".

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
	await peopleTable.ExecuteBatchAsync(batchOperation);

    // Create the CloudTable if it does not exist
    await table.CreateIfNotExistsAsync();

## Adicionar uma entidade a uma tabela

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

As operações de tabela que envolvem entidades são executadas usando o objeto **CloudTable** criado anteriormente em “Acessar tabelas no código”. O objeto **TableOperation** representa a operação a ser realizada. O exemplo de código a seguir mostra como criar um objeto **CloudTable** e um objeto **CustomerEntity**. Para preparar a operação, um **TableOperation** é criado para inserir a entidade de cliente na tabela. Finalmente, a operação é executada chamando CloudTable.ExecuteAsync.

    // Get a reference to the CloudTable object named 'peopleTable' as described in "Access a table in code".

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);

## Inserir um lote de entidades

Você pode inserir várias entidades em uma tabela em uma única operação de gravação. O exemplo de código a seguir cria dois objetos de entidade (“Mateus Rodrigues” e “Alberto Rodrigues”) e os adiciona a um objeto **TableBatchOperation** usando o método Insert e depois inicia a operação chamando **CloudTable.ExecuteBatchAsync**.

    // Get a reference to a CloudTable object named 'peopleTable' as described in "Access a table in code".

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
    await peopleTable.ExecuteBatchAsync(batchOperation);

## Obter todas as entidades em uma partição

Para consultar uma tabela de todas as entidades em uma partição, use um objeto **TableQuery**. O exemplo de código a seguir especifica um filtro para entidades onde 'Smith' é a chave da partição. Esse exemplo imprime os campos de cada entidade nos resultados da consulta no console.

    // Get a reference to a CloudTable object named 'peopleTable' as described in "Access a table in code".

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
    	TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
		token = resultSegment.ContinuationToken;

		foreach (CustomerEntity entity in resultSegment.Results)
    	{
    		Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
    		entity.Email, entity.PhoneNumber);
        }
    } while (token != null);

    return View();


## Obter uma única entidade

Você pode escrever uma consulta para obter uma entidade única e específica. O código a seguir usa um objeto **TableOperation** para especificar o cliente chamado 'Ben Smith'. Esse método retorna uma única entidade, em vez de uma coleção, e o valor retornado no **TableResult.Result** é um objeto **CustomerEntity**. Especificar chaves de partição e de linha em uma consulta é a maneira mais rápida de recuperar uma única entidade de serviço **Table**.

	// Get a reference to a **CloudTable** object named 'peopleTable' as described in "Access a table in code".

	// Create a retrieve operation that takes a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

	// Execute the retrieve operation.
	TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

	// Print the phone number of the result.
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("The phone number could not be retrieved.");

## Excluir uma entidade
Você poderá excluir uma entidade facilmente depois de encontrá-la. O código a seguir busca uma entidade customer chamada "Mateus Rodrigues", excluindo-a caso a encontre.

	// Get a reference to a **CloudTable** object named 'peopleTable' as described in "Access a table in code".

	// Create a retrieve operation that expects a customer entity.
	TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

	// Execute the operation.
	TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

	// Assign the result to a CustomerEntity object.
	CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

	// Create the Delete TableOperation and then execute it.
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

	   // Execute the operation.
	   await peopleTable.ExecuteAsync(deleteOperation);

	   Console.WriteLine("Entity deleted.");
	}

	else
	   Console.WriteLine("Couldn't delete the entity.");

## Próximas etapas

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]

<!---HONumber=Sept15_HO4-->