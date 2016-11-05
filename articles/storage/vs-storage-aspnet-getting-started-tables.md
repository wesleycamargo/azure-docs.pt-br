---
title: Introdução ao armazenamento de tabela e aos serviços conectados do Visual Studio (ASP.NET) | Microsoft Docs
description: Como começar a usar o armazenamento de Tabela do Azure em um projeto do ASP.NET no Visual Studio após a conexão a uma conta de armazenamento usando os serviços conectados do Visual Studio
services: storage
documentationcenter: ''
author: TomArcher
manager: douge
editor: ''

ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 07/18/2016
ms.author: tarcher

---
# Introdução ao armazenamento de tabela e aos serviços conectados do Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## Visão geral
Este artigo descreve como começar a usar o armazenamento de Tabelas do Azure no Visual Studio depois de ter criado ou referenciado uma conta de armazenamento do Azure em um projeto ASP.NET, usando a caixa de diálogo **Adicionar Serviços Conectados** do Visual Studio. Este artigo mostra como executar tarefas comuns nas tabelas do Azure, incluindo a criação e exclusão de uma tabela, bem como o trabalho com entidades de tabela. Os exemplos são escritos em código C# e usam a [Biblioteca de Cliente do Armazenamento do Microsoft Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx). Para obter mais informações sobre como usar o Armazenamento de Tabelas do Azure, consulte [Introdução ao Armazenamento de Tabelas do Azure usando o .NET](storage-dotnet-how-to-use-tables.md).

O serviço de armazenamento de Tabela do Azure permite que você armazene grandes quantidades de dados estruturados. O serviço é um repositório de dados NoSQL que aceita chamadas autenticadas de dentro e de fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais.

## Acessar tabelas em código
1. Verifique se as declarações de namespace na parte superior do arquivo C# incluem estas instruções de **uso**.
   
         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;
2. Obtenha um objeto **CloudStorageAccount** que represente as informações da conta de armazenamento. Use o seguinte código para obter a sua cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
   
    **OBSERVAÇÃO** - use todos os códigos acima antes do código nos exemplos a seguir.
3. Obtenha um objeto **CloudTableClient** para fazer referência aos objetos de tabela em sua conta de armazenamento.
   
        // Create the table client.
        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
4. Obtenha um objeto de referência **CloudTable** para fazer referência a entidades e a uma tabela específica.
   
        // Get a reference to a table named "peopleTable"
        CloudTable table = tableClient.GetTableReference("peopleTable");

## Criar uma tabela em código
Para criar a tabela do Azure, basta adicionar uma chamada para **CreateIfNotExistsAsync()** ao código anterior.

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

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity>
        resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity entity in resultSegment.Results)
        {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
        }
        } while (token != null);

        return View();


## Obter uma única entidade
Você pode escrever uma consulta para obter uma entidade única e específica. O código a seguir usa um objeto **TableOperation** para especificar o cliente chamado 'Ben Smith'. Este método retorna uma única entidade, em vez de uma coleção, e o valor retornado no **TableResult.Result** é um objeto **CustomerEntity**. Especificar chaves de partição e de linha em uma consulta é a maneira mais rápida de recuperar uma única entidade de serviço Tabela.

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
Você poderá excluir uma entidade facilmente depois de encontrá-la. O código a seguir busca uma entidade de cliente chamada "Ben Smith", excluindo-a caso a encontre.

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
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]

<!---HONumber=AcomDC_0727_2016-->