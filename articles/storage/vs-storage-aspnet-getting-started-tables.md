---
title: "Introdução ao Armazenamento de Tabelas do Azure e aos Serviços Conectados do Visual Studio (ASP.NET) | Microsoft Docs"
description: "Como começar a usar o Armazenamento de Tabelas do Azure em um projeto do ASP.NET no Visual Studio após a conexão a uma conta de armazenamento usando os Serviços Conectados do Visual Studio"
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 07f827e13e5f01a373e69b90e8a5a0c72081acd0
ms.openlocfilehash: 24cfb3217dbadba3a086a8b0251efe5ab0173d1f


---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Introdução ao Armazenamento de Tabelas do Azure e aos Serviços Conectados do Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Visão geral

O serviço de armazenamento de Tabela do Azure permite que você armazene grandes quantidades de dados estruturados. O serviço é um repositório de dados NoSQL que aceita chamadas autenticadas de dentro e de fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais.

Este artigo descreve como gerenciar programaticamente entidades de Armazenamento de Tabelas do Azure, executar tarefas comuns, como criar e excluir uma tabela, bem como trabalhar com entidades de tabela. 

> [!NOTE]
> 
> As seções de código neste artigo pressupõem que você já se conectou a uma conta de armazenamento do Azure usando os Serviços Conectados. Os Serviços Conectados são configurados abrindo o Gerenciador de Soluções do Visual Studio, clicando no projeto e, no menu de contexto, selecionando a opção **Adicionar->Serviço Conectado**. A partir daí, siga as instruções da caixa de diálogo para conectar-se à conta de armazenamento desejada do Azure.      

## <a name="create-a-table-in-code"></a>Criar uma tabela em código

As etapas a seguir ilustram como criar programaticamente uma tabela. Em um aplicativo ASP.NET MVC, o código iria em um controlador.

1. Adicione as seguintes diretivas *using*:

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. Obtenha um objeto **CloudStorageAccount** que represente as informações da conta de armazenamento. Use o seguinte código para obter a cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure. (Mude *<storage-account-name>* para o nome da conta de armazenamento do Azure que você está acessando.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenha um objeto **CloudTableClient** que representa um cliente do serviço de tabela.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. Obtenha um objeto **CloudTable** que representa uma referência ao nome da tabela desejada. (Mude *<table-name>* para o nome da tabela que você deseja criar.)

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. Chame o método **CloudTable.CreateIfNotExists** para criar a tabela se ela ainda não existir.   
   
        table.CreateIfNotExists();

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

As etapas a seguir ilustram como adicionar programaticamente uma entidade a uma tabela. Em um aplicativo ASP.NET MVC, o código iria em um controlador. 

1. Adicione as seguintes diretivas *using*:

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. Obtenha um objeto **CloudStorageAccount** que represente as informações da conta de armazenamento. Use o seguinte código para obter a cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure. (Mude *<storage-account-name>* para o nome da conta de armazenamento do Azure que você está acessando.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenha um objeto **CloudTableClient** que representa um cliente do serviço de tabela.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. Obtenha um objeto **CloudTable** que representa uma referência ao nome da tabela desejada. (Mude *<table-name>* para o nome da tabela a qual você deseja adicionar a entidade.)

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. Para adicionar uma entidade a uma tabela, defina uma classe derivada de **TableEntity**. O código a seguir define uma classe chamada **CustomerEntity** que usa o nome do cliente como a chave de linha e o sobrenome como a chave de partição.

        public class CustomerEntity : TableEntity
        {
            public CustomerEntity(string lastName, string firstName)
            {
                this.PartitionKey = lastName;
                this.RowKey = firstName;
            }
    
            public CustomerEntity() { }
    
            public string Email { get; set; }
        }

6. Crie uma instância da entidade.

        CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
        customer1.Email = "Walter@contoso.com";

7. Crie o objeto **TableOperation** que insere a entidade do cliente.

        TableOperation insertOperation = TableOperation.Insert(customer1);

8. Execute a operação de inserção chamando o método **Cloudtable.Execute**. Você pode verificar o resultado da operação inspecionando a propriedade **TableResult.HttpStatusCode**. Um código de status de 2xx indica que a ação solicitada pelo cliente foi processada com êxito. Por exemplo, inserções bem-sucedidas de novas entidades resultam em um código de status HTTP 204, indicando que a operação foi processada com êxito, e o servidor não retornou qualquer conteúdo.

        TableResult result = table.Execute(insertOperation);

        // Inspect result.HttpStatusCode for success/failure.

## <a name="add-a-batch-of-entities-to-a-table"></a>Adicionar um lote de entidades a uma tabela

Além de poder adicionar uma entidade por vez a uma tabela, você também pode adicionar entidades em lote. Isso reduz o número de viagens de ida e volta entre o código e o serviço Tabela do Azure. As etapas a seguir ilustram como adicionar programaticamente várias entidades a uma tabela usando uma única operação. Em um aplicativo ASP.NET MVC, o código iria em um controlador.

1. Adicione as seguintes diretivas *using*:

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. Obtenha um objeto **CloudStorageAccount** que represente as informações da conta de armazenamento. Use o seguinte código para obter a cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure. (Mude *<storage-account-name>* para o nome da conta de armazenamento do Azure que você está acessando.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenha um objeto **CloudTableClient** que representa um cliente do serviço de tabela.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. Obtenha um objeto **CloudTable** que representa uma referência ao nome da tabela desejada. (Mude *<table-name>* para o nome da tabela a qual você deseja adicionar as entidades.)

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. Para adicionar uma entidade a uma tabela, defina uma classe derivada de **TableEntity**. O código a seguir define uma classe chamada **CustomerEntity** que usa o nome do cliente como a chave de linha e o sobrenome como a chave de partição.

        public class CustomerEntity : TableEntity
        {
            public CustomerEntity(string lastName, string firstName)
            {
                this.PartitionKey = lastName;
                this.RowKey = firstName;
            }
    
            public CustomerEntity() { }
    
            public string Email { get; set; }
        }

6. Crie uma instância das entidades.

        CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
        customer1.Email = "Jeff@contoso.com";
    
        CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
        customer2.Email = "Ben@contoso.com";

7. Obtenha um objeto **TableBatchOperation**.

        TableBatchOperation batchOperation = new TableBatchOperation();

8. Adicione entidades à operação de inserção em lote.

        batchOperation.Insert(customer1);
        batchOperation.Insert(customer2);

9. Execute a operação de inserção em lote chamando o método **CloudTable.ExecuteBatch**. O método **CloudTable.ExecuteBatch** retorna uma lista de objetos **TableResult**. Você pode verificar o resultado da operação de inserção em lote inspecionando a propriedade **TableResult.HttpStatusCode** de cada objeto **TableResult** na lista. Um código de status de 2xx indica que a ação solicitada pelo cliente foi processada com êxito. Por exemplo, inserções bem-sucedidas de novas entidades resultam em um código de status HTTP 204, indicando que a operação foi processada com êxito, e o servidor não retornou qualquer conteúdo.
    
        IList<TableResult> results = table.ExecuteBatch(batchOperation);

        // Inspect the HttpStatusCode property of each TableResult object
        // in the results list for success/failure.

## <a name="get-a-single-entity"></a>Obter uma única entidade

As etapas a seguir ilustram como obter programaticamente uma entidade de uma tabela. Em um aplicativo ASP.NET MVC, o código iria em um controlador. 

> [!NOTE]
> 
> O código nesta seção faz referência à classe **CustomerEntity** e aos dados apresentados na seção, [Adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table). 

1. Adicione as seguintes diretivas *using*:

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. Obtenha um objeto **CloudStorageAccount** que represente as informações da conta de armazenamento. Use o seguinte código para obter a cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure. (Mude *<storage-account-name>* para o nome da conta de armazenamento do Azure que você está acessando.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenha um objeto **CloudTableClient** que representa um cliente do serviço de tabela.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. Obtenha um objeto **CloudTable** que representa uma referência ao nome da tabela desejada. (Mude *<table-name>* para o nome da tabela a qual você deseja adicionar as entidades.)

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. Crie um objeto de operação de recuperação que usa um objeto de entidade derivado de **TableEntity**. O primeiro parâmetro é *partitionKey*, e o segundo parâmetro é *rowKey*. Usando a classe **CustomerEntity** e os dados apresentados na seção [Adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table), o trecho de código a seguir consulta a tabela em busca de uma entidade **CustomerEntity** com um valor de *partitionKey* de "Mateus" e um valor de *rowKey* de "Rodrigues".  

        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

6. Execute a operação de recuperação.   

        TableResult retrievedResult = table.Execute(retrieveOperation);

7. Verifique o resultado da operação inspecionando a propriedade **TableOperation.HttpStatusCode**, na qual um código de status 200 indica que a ação solicitada pelo cliente foi processada com êxito. Você também pode inspecionar a propriedade **TableResult.Result** que conterá a entidade retornada (se a operação for bem-sucedida).

        CustomerEntity customer = null;

        if (retrievedResult.HttpStatusCode == 200 && retrievedResult.Result != null)
        {
            // Process the customer entity.
            customer = retrievedResult.Result as CustomerEntity;
        }

## <a name="get-all-entities-in-a-partition"></a>Obter todas as entidades em uma partição

As etapas a seguir ilustram como obter programaticamente todas as entidades de uma partição. Em um aplicativo ASP.NET MVC, o código iria em um controlador. 

> [!NOTE]
> 
> O código nesta seção faz referência à classe **CustomerEntity** e aos dados apresentados na seção, [Adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table). 

1. Adicione as seguintes diretivas *using*:

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. Obtenha um objeto **CloudStorageAccount** que represente as informações da conta de armazenamento. Use o seguinte código para obter a cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure. (Mude *<storage-account-name>* para o nome da conta de armazenamento do Azure que você está acessando.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenha um objeto **CloudTableClient** que representa um cliente do serviço de tabela.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. Obtenha um objeto **CloudTable** que representa uma referência ao nome da tabela desejada. (Mude *<table-name>* para o nome da tabela a qual você deseja adicionar as entidades.)

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. Crie uma instância de um objeto **TableQuery** que especifica a consulta na cláusula **Where**. Usando a classe **CustomerEntity** e os dados apresentados na seção [Adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table), o trecho de código a seguir consulta a tabela em busca de uma entidade na qual **PartitionKey** tem um valor de "Rodrigues".

        TableQuery<CustomerEntity> query = 
            new TableQuery<CustomerEntity>()
            .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

6. Dentro de um loop, chame o método **CloudTable.ExecuteQuerySegmented** passando o objeto de consulta para o qual você criou uma instância na etapa anterior.  O método **CloudTable.ExecuteQuerySegmented** retorna um objeto **TableContinuationToken** que - quando é **nulo** -indica que não há mais entidades para recuperação. Dentro do loop, use outro loop para iterar sobre as entidades retornadas.

        TableContinuationToken token = null;
        do
        {
            TableQuerySegment<CustomerEntity>resultSegment = table.ExecuteQuerySegmented(query, token);
            token = resultSegment.ContinuationToken;

            foreach (CustomerEntity customer in resultSegment.Results)
            {
                // Process customer entity.
            }
        } while (token != null);

## <a name="delete-an-entity"></a>Excluir uma entidade

As etapas a seguir ilustram como procurar e excluir uma entidade.

1. Adicione as seguintes diretivas *using*:

         using Microsoft.Azure;
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Auth;
         using Microsoft.WindowsAzure.Storage.Table;

2. Obtenha um objeto **CloudStorageAccount** que represente as informações da conta de armazenamento. Use o seguinte código para obter a cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure. (Mude *<storage-account-name>* para o nome da conta de armazenamento do Azure que você está acessando.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenha um objeto **CloudTableClient** que representa um cliente do serviço de tabela.

        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();


4. Obtenha um objeto **CloudTable** que representa uma referência ao nome da tabela desejada. (Mude *<table-name>* para o nome da tabela a qual você deseja adicionar as entidades.)

        CloudTable table = tableClient.GetTableReference(<table-name>);

5. Crie um objeto de operação de recuperação que usa um objeto de entidade derivado de **TableEntity**. O primeiro parâmetro é *partitionKey*, e o segundo parâmetro é *rowKey*. Usando a classe **CustomerEntity** e os dados apresentados na seção [Adicionar um lote de entidades a uma tabela](#add-a-batch-of-entities-to-a-table), o trecho de código a seguir consulta a tabela em busca de uma entidade **CustomerEntity** com um valor de *partitionKey* de "Mateus" e um valor de *rowKey* de "Rodrigues".  

        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

6. Execute a operação de recuperação.   

        TableResult retrievedResult = table.Execute(retrieveOperation);

7. Verifique o resultado da operação inspecionando a propriedade **TableOperation.HttpStatusCode**, na qual um código de status 200 indica que a ação solicitada pelo cliente foi processada com êxito. Você também pode inspecionar a propriedade **TableResult.Result** que conterá a entidade retornada (se a operação for bem-sucedida). Para verificar se a operação foi bem-sucedida dentro da instrução condicional, crie uma operação de exclusão (passando a entidade retornada da consulta) e execute a operação de exclusão.

        if (retrievedResult.HttpStatusCode == 200 && retrievedResult.Result != null)
        {
            CustomerEntity customer = retrievedResult.Result as CustomerEntity;

            // Create the delete operation.
            TableOperation deleteOperation = TableOperation.Delete(customer);

            // Execute the delete operation.
            table.Execute(deleteOperation);
        }

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]




<!--HONumber=Dec16_HO2-->


