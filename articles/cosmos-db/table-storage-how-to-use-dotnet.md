---
title: "Introdução ao armazenamento de Tabelas do Azure usando o .NET | Microsoft Docs"
description: "Armazene dados estruturados na nuvem usando o Armazenamento de Tabelas do Azure, um repositório de dados NoSQL."
services: cosmos-db
documentationcenter: .net
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: fe46d883-7bed-49dd-980e-5c71df36adb3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/30/2018
ms.author: mimig
ms.openlocfilehash: f95d66950feb8729a7edcad3e02ea9a932123e16
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="get-started-with-azure-table-storage-using-net"></a>Introdução ao armazenamento de Tabelas do Azure usando o .NET
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

O Armazenamento de Tabelas do Azure é um serviço que armazena dados NoSQL estruturados na nuvem, fornecendo um repositório chave/atributo com um design sem esquema. Como o armazenamento de Tabelas não tem um esquema, é fácil adaptar seus dados à medida que as necessidades de seu aplicativo evoluem. O acesso aos dados do Armazenamento de Tabelas é rápido e econômico para muitos tipos de aplicativos e normalmente tem um custo mais baixo que o SQL tradicional para volumes de dados semelhantes.

Você pode usar o armazenamento de tabelas para armazenar conjuntos de dados flexíveis, como dados de usuário para aplicativos web, catálogos de endereços, informações sobre dispositivos ou outros tipos de metadados exigidos pelo serviço. Você pode armazenar qualquer número de entidades em uma tabela e uma conta de armazenamento pode conter um número ilimitado de tabelas, até o limite de capacidade da conta de armazenamento.

### <a name="about-this-tutorial"></a>Sobre este tutorial
Este tutorial mostra como usar a [Biblioteca de Tabelas do Cliente do Microsoft Azure Cosmos DB para .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) em alguns cenários comuns do Armazenamento de Tabelas do Azure. O nome do pacote indica que é para uso com o Azure Cosmos DB, mas o pacote trabalha com o Azure Cosmos DB e o Armazenamento de Tabelas do Azure, cada serviço possui apenas um ponto de extremidade exclusivo. Esses cenários são explorados usando exemplos em C# que ilustram como:
* Criar e excluir tabelas
* Inserir, atualizar e excluir linhas
* Consultar tabelas

## <a name="prerequisites"></a>pré-requisitos

Você precisará do seguinte para concluir este tutorial com sucesso:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Biblioteca Comum do Armazenamento do Azure para .NET (versão prévia)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/). Este é um pacote em versão prévia obrigatório com suporte para ambientes de produção. 
* [Biblioteca de Tabelas do Microsoft Azure Cosmos DB para .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)
* [Gerenciador de Configurações do Azure para .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
* [Conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Mais exemplos
Para obter exemplos adicionais usando o Armazenamento de Tabelas, confira [Introdução ao Armazenamento de Tabelas do Azure no .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/). Você pode baixar o aplicativo de exemplo e executá-lo ou procurar o código no GitHub.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

## <a name="create-an-azure-service-account"></a>Criar uma conta de serviço do Azure

Você pode trabalhar com tabelas usando o Armazenamento de Tabelas do Azure ou o Azure Cosmos DB. Você pode aprender mais sobre as diferenças entre os serviços lendo [Ofertas de tabela](table-introduction.md#table-offerings). Você precisará criar uma conta para o serviço que pretende usar. 

### <a name="create-an-azure-storage-account"></a>Criar uma conta de armazenamento do Azure
A maneira mais fácil de criar sua primeira conta de armazenamento do Azure é usando o [portal do Azure](https://portal.azure.com). Para saber mais, consulte [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account).

Você também pode criar uma conta de armazenamento do Azure usando o [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), a [CLI do Azure](../storage/common/storage-azure-cli.md) ou a [Biblioteca de cliente do provedor de recursos de armazenamento para .NET](/dotnet/api/microsoft.azure.management.storage).

Se você preferir não criar uma conta de armazenamento no momento, também poderá usar o emulador de armazenamento do Azure para executar e testar seu código em um ambiente local. Para saber mais, confira [Usar o Emulador de Armazenamento do Azure para desenvolvimento e teste](../storage/common/storage-use-emulator.md).

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Criar uma conta de API de Tabele do Azure Cosmos DB

Para obter instruções sobre como criar uma conta da API de Tabela do Azure Cosmos DB, consulte [Criar uma conta de API de Tabela](create-table-dotnet.md#create-a-database-account).

## <a name="set-up-your-development-environment"></a>Configurar seu ambiente de desenvolvimento
Em seguida, configure seu ambiente de desenvolvimento no Visual Studio para poder ficar pronto para experimentar os exemplos de código neste guia.

### <a name="create-a-windows-console-application-project"></a>Criar um projeto de aplicativo de console do Windows.
No Visual Studio, crie um novo aplicativo de console do Windows. As etapas a seguir mostram como criar um aplicativo de console no Visual Studio 2017. As etapas são semelhantes em outras versões do Visual Studio.

1. Selecione **Arquivo** > **Novo** > **Projeto**.
2. Selecione **Instalado** > **Visual C#** > **Área de Trabalho Clássica do Windows**.
3. Selecione **Aplicativo do Console (.NET Framework)**.
4. No campo **Nome**, insira um nome para seu aplicativo.
5. Selecione **OK**.

Todos os exemplos de código neste tutorial podem ser adicionados ao método `Main()` no arquivo`Program.cs` do aplicativo de console.

Você pode usar a Biblioteca de Tabelas do Azure Cosmos DB em qualquer tipo de aplicativo .NET, incluindo um serviço de nuvem do Azure, um aplicativo Web do Azure e aplicativos da área de trabalho ou móvel. Neste guia, usamos um aplicativo de console para simplificar.

### <a name="use-nuget-to-install-the-required-packages"></a>Use o NuGet para instalar os pacotes necessários
Há três pacotes que você precisará referenciar em seu projeto para concluir este tutorial:

* [Biblioteca Comum do Armazenamento do Azure para .NET (versão prévia)](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/). 
* [Biblioteca de Tabelas do Microsoft Azure Cosmos DB para .NET](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table). Este pacote fornece acesso programático aos recursos de dados em sua conta de Armazenamento de Tabelas do Azure ou em sua conta de API de Tabela do Azure Cosmos DB.
* [Biblioteca do Gerenciador de Configuração do Microsoft Azure para .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/): este pacote fornece uma classe para analisar uma cadeia de conexão em um arquivo de configuração, independentemente de onde o aplicativo está sendo executado.

Você pode usar NuGet para obter os dois pacotes. Siga estas etapas:

1. Clique com o botão direito do mouse no seu projeto no **Gerenciador de Soluções** e escolha **Gerenciar Pacotes NuGet**.
2. Pesquise online "Microsoft.Azure.Storage.Common" e selecione **Instalar** para instalar a Biblioteca Comum de Armazenamento do Azure para .NET (Versão prévia) e suas dependências. Verifique se a caixa **Incluir pré-lançamento** está marcada, já que este é um pacote de versão prévia.
3. Pesquise online "Microsoft.Azure.CosmosDB.Table" e selecione **Instalar** para instalar a Biblioteca de Tabelas do Microsoft Azure Cosmos DB.
4. Pesquise online por "WindowsAzure.ConfigurationManager" e clique em **Instalar** para instalar a Biblioteca do Gerenciador de Configurações do Microsoft Azure.

> [!NOTE]
> As dependências do ODataLib na Biblioteca Comum de Armazenamento para .NET são resolvidas por pacotes do ODataLib disponíveis no NuGet, não do WCF Data Services. As bibliotecas do ODataLib podem ser baixadas diretamente ou referenciadas por seu projeto de código por meio do NuGet. Os pacotes específicos do ODataLib usados pela Biblioteca de Cliente de Armazenamento são [OData](http://nuget.org/packages/Microsoft.Data.OData/), [Edm](http://nuget.org/packages/Microsoft.Data.Edm/) e [Spatial](http://nuget.org/packages/System.Spatial/). Embora essas bibliotecas sejam usadas pelas classes de armazenamento de tabela do Azure, elas são dependências necessárias para a programação com a Biblioteca Comum de Armazenamento.
> 
> 

### <a name="determine-your-target-environment"></a>Determinar o ambiente de destino
Você tem duas opções de ambiente para executar os exemplos neste guia:

* Você pode executar o código em uma conta de Armazenamento do Azure na nuvem. 
* Você pode executar o código em uma conta do Azure Cosmos DB.
* Você pode executar o código em um emulador de armazenamento do Azure. O emulador de armazenamento é um ambiente local que emula uma conta de Armazenamento do Azure na nuvem. O emulador é uma opção gratuita para testar e depurar seu código enquanto o aplicativo está em desenvolvimento. O emulador usa uma conta e chave bem conhecidas. Para saber mais, confira [Usar o emulador de armazenamento do Azure para desenvolvimento e teste](../storage/common/storage-use-emulator.md).

Se você estiver selecionando uma conta de armazenamento na nuvem, copie a chave de acesso primário para sua conta de armazenamento a partir do portal do Azure. Para saber mais, confira [Exibir e copiar chaves de acesso de armazenamento](../storage/common/storage-create-storage-account.md#view-and-copy-storage-access-keys).

> [!NOTE]
> Você pode utilizar o emulador de armazenamento como destino para evitar quaisquer custos associados ao Armazenamento do Azure. Todavia, se você escolher utilizar como destino uma conta de armazenamento do Azure na nuvem, os custos para realização das operações neste tutorial serão insignificantes.
> 
> 

Se você estiver selecionando uma conta do Azure Cosmos DB, copie a chave de acesso primária para sua conta de API de Tabela do portal do Azure. Para saber mais informações, consulte [Atualizar sua cadeia de conexão](create-table-dotnet.md#update-your-connection-string).

### <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de conexão de armazenamento
A Biblioteca de Comum de Armazenamento do Azure para .NET oferece suporte ao uso de uma cadeia de conexão de armazenamento para configurar pontos de extremidade e credenciais para acesso a serviços de armazenamento. A melhor maneira de manter a cadeia de conexão de armazenamento é em um arquivo de configuração. 

Para obter mais detalhes sobre as sequências de conexão, veja [Configurar uma cadeia de conexão para o Armazenamento do Azure](../storage/common/storage-configure-connection-string.md).

> [!NOTE]
> Sua chave de conta é semelhante para a senha raiz da sua conta de armazenamento. Sempre tenha cuidado para proteger a chave de sua conta de armazenamento. Evite distribuí-la a outros usuários, embuti-la no código ou salvá-lo em um arquivo de texto sem formatação que esteja acessível a outras pessoas. Regenere a chave usando o portal do Azure se você achar que ela pode ter sido comprometida.
> 
> 

Para configurar a cadeia de conexão, abra o arquivo `app.config` do Gerenciador de Soluções no Visual Studio. Adicionar o conteúdo do elemento `<appSettings>` mostrado abaixo. Substitua `account-name` pelo nome da sua conta e `account-key` pela chave de acesso da conta:

```xml
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.6.1" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

Por exemplo, se você estiver usando uma Conta de Armazenamento do Azure, as configurações serão semelhantes a:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==" />
```

Se você estiver usando uma conta do Azure Cosmos DB, as configurações serão semelhantes a:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=tableapiacct;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==;TableEndpoint=https://tableapiacct.table.cosmosdb.azure.com:443/;" />
```

Para direcionar o emulador de armazenamento, você pode usar um atalho que mapeia para o nome de conta e chave bem conhecidos. Nesse caso, a configuração da cadeia de conexão é:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

### <a name="add-using-directives"></a>Adicionar diretivas using
Adicione as seguintes diretivas **using** à parte superior do arquivo `Program.cs`:

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage.Common; // Namespace for StorageAccounts
using Microsoft.Azure.CosmosDB.Table; // Namespace for Table storage types
```

### <a name="parse-the-connection-string"></a>Analisar a cadeia de conexão
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-table-service-client"></a>Criar o cliente do serviço Tabela
A classe [CloudTableClient][dotnet_CloudTableClient] permite que você recupere as tabelas e entidades armazenadas no Armazenamento de tabelas. Veja uma maneira de criar o cliente do serviço Tabela:

```csharp
// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```

Agora você está pronto para escrever um código que lê e grava dados no Armazenamento de Tabelas.

## <a name="create-a-table"></a>Criar uma tabela
Este exemplo mostra como criar uma tabela se ela ainda não existe:

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Retrieve a reference to the table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table if it doesn't exist.
table.CreateIfNotExists();
```

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela
As entidades são mapeadas para objetos C# usando uma classe personalizada derivada de [TableEntity][dotnet_TableEntity]. Para adicionar uma entidade a uma tabela, crie uma classe que defina as propriedades da sua entidade. O código a seguir define uma classe de entidade que usa o nome do cliente como a chave de linha e o sobrenome como a chave de partição. Juntas, uma chave de partição e uma chave de linha identificam exclusivamente a entidade na tabela. As entidades com a mesma chave de partição podem ser consultadas mais rápido do que aquelas com chaves de partição diferentes, mas usar chaves de partição diferentes permite uma escalabilidade maior de operações paralelas. As entidades que serão armazenadas em tabelas deverão ser de um tipo com suporte, por exemplo, derivado da classe [TableEntity][dotnet_TableEntity]. As propriedades de entidade que você gostaria de armazenar em uma tabela precisam ser propriedades públicas do tipo e dar suporte à obtenção e à definição dos valores. Além disso, o tipo de entidade *deve* expor um construtor sem parâmetros.

```csharp
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
```

As operações de tabela que envolvam entidades são executadas usando o objeto [CloudTable][dotnet_CloudTable] que você criou na seção “Criar uma tabela”. A operação a ser executada é representada por um objeto [TableOperation][dotnet_TableOperation]. O exemplo de código a seguir mostra a criação do objeto [CloudTable][dotnet_CloudTable] e, em seguida, de um objeto **CustomerEntity**. Para preparar a operação, um objeto [TableOperation][dotnet_TableOperation] é criado para inserir a entidade customer na tabela. Finalmente, a operação é executada chamando [CloudTable][dotnet_CloudTable].[Execute][dotnet_CloudTable_Execute].

```csharp
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

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
table.Execute(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Inserir um lote de entidades
Você pode inserir um lote de entidades em uma tabela em uma única operação de gravação. Algumas outras observações sobre operações em lote:

* Você pode executar atualizações, exclusões e inserções em uma única operação em lote.
* Uma única operação em lotes pode incluir até 100 entidades.
* Todas as entidades em uma única operação em lote devem ter a mesma chave de partição.
* Embora seja possível executar uma consulta como uma operação em lotes, ele deve ser a única operação em lote.

O exemplo de código a seguir cria dois objetos de entidade e adiciona cada um a uma [TableBatchOperation][dotnet_TableBatchOperation] usando o método [Insert][dotnet_TableBatchOperation_Insert]. Em seguida, [CloudTable][dotnet_CloudTable].[ExecuteBatch][dotnet_CloudTable_ExecuteBatch] é chamado para executar a operação.

```csharp
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
```

## <a name="retrieve-all-entities-in-a-partition"></a>Recuperar todas as entidades em uma partição
Para consultar uma tabela de todas as entidades em uma partição, use um objeto [TableQuery][dotnet_TableQuery]. O exemplo de código a seguir especifica um filtro para as entidades em que 'Smith’ é a chave de partição. Esse exemplo imprime os campos de cada entidade nos resultados da consulta no console.

```csharp
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
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Recuperar um intervalo de entidades em uma partição
Se não desejar consultar todas as entidades em uma partição, você poderá especificar um intervalo combinando o filtro de chave de partição com um filtro de chave de linha. O exemplo de código a seguir usa dois filtros para obter todas as entidades na partição 'Smith' onde a chave de linha (nome) começa com uma letra anterior a 'E' do alfabeto e imprime os resultados da consulta.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
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
```

## <a name="retrieve-a-single-entity"></a>Recuperar uma única entidade
Você pode escrever uma consulta para recuperar uma entidade única e específica. O código a seguir usa [TableOperation][dotnet_TableOperation] para especificar o cliente 'Mateus Rodrigues'. Este método retorna uma única entidade, em vez de uma coleção, e o valor retornado no [TableResult][dotnet_TableResult].[Result][dotnet_TableResult_Result] é um objeto **CustomerEntity**. Especificar chaves de partição e de linha em uma consulta é a maneira mais rápida de recuperar uma única entidade de serviço Table.

```csharp
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
{
    Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
}
else
{
    Console.WriteLine("The phone number could not be retrieved.");
}
```

## <a name="replace-an-entity"></a>Substituir uma entidade
Para atualizar uma entidade, recupere-a do serviço Tabela, modifique o objeto de entidade e, em seguida, salve as alterações novamente no serviço Tabela. O código a seguir altera o número de telefone de um cliente existente. Em vez de chamar [Insert][dotnet_TableOperation_Insert], esse código usa [Replace][dotnet_TableOperation_Replace]. [Replace][dotnet_TableOperation_Replace] faz com que a entidade seja totalmente substituída no servidor, a menos que a entidade no servidor tenha sido alterada desde que foi recuperada, caso em que haverá falha na operação. Essa falha é para impedir que seu aplicativo substitua inadvertidamente uma alteração feita entre a recuperação e a atualização por outro componente de seu aplicativo. O tratamento correto dessa falha é recuperar a entidade novamente, fazer as alterações (se ainda forem válidas) e executar outra operação [Replace][dotnet_TableOperation_Replace]. A próxima seção mostrará como substituir esse comportamento.

```csharp
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
    updateEntity.PhoneNumber = "425-555-0105";

    // Create the Replace TableOperation.
    TableOperation updateOperation = TableOperation.Replace(updateEntity);

    // Execute the operation.
    table.Execute(updateOperation);

    Console.WriteLine("Entity updated.");
}
else
{
    Console.WriteLine("Entity could not be retrieved.");
}
```

## <a name="insert-or-replace-an-entity"></a>Inserir ou substituir uma entidade
A operação [Replace][dotnet_TableOperation_Replace] falhará se a entidade tiver sido alterada depois de ter sido recuperada do servidor. Além disso, primeiro você precisa recuperar a entidade do servidor para que a operação [Replace][dotnet_TableOperation_Replace] seja bem-sucedida. No entanto, às vezes você não sabe se a entidade existe no servidor, e se os valores atuais armazenados nela são irrelevantes. A atualização deve substituir todos eles. Para fazer isso, você deve usar uma operação [InsertOrReplace][dotnet_TableOperation_InsertOrReplace]. Essa operação insere a entidade, se ela não existir, ou a substitui, se ela existir, independentemente de quando foi feita a última atualização.

No exemplo de código a seguir, uma entidade de cliente 'Vinicius Monte' é criada e inserida na tabela 'pessoas'. Em seguida, usamos a operação [InsertOrReplace][dotnet_TableOperation_InsertOrReplace] para salvar uma entidade com a mesma chave de partição (Monte) e chave de linha (Vinicius) para o servidor, dessa vez com um valor diferente para a propriedade PhoneNumber. Como usamos [InsertOrReplace][dotnet_TableOperation_InsertOrReplace], todos os seus valores de propriedade serão substituídos. No entanto, se uma entidade 'Vinicius Monte' já não existia na tabela, ela foi inserida.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a customer entity.
CustomerEntity customer3 = new CustomerEntity("Jones", "Fred");
customer3.Email = "Fred@contoso.com";
customer3.PhoneNumber = "425-555-0106";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer3);

// Execute the operation.
table.Execute(insertOperation);

// Create another customer entity with the same partition key and row key.
// We've already created a 'Fred Jones' entity and saved it to the
// 'people' table, but here we're specifying a different value for the
// PhoneNumber property.
CustomerEntity customer4 = new CustomerEntity("Jones", "Fred");
customer4.Email = "Fred@contoso.com";
customer4.PhoneNumber = "425-555-0107";

// Create the InsertOrReplace TableOperation.
TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(customer4);

// Execute the operation. Because a 'Fred Jones' entity already exists in the
// 'people' table, its property values will be overwritten by those in this
// CustomerEntity. If 'Fred Jones' didn't already exist, the entity would be
// added to the table.
table.Execute(insertOrReplaceOperation);
```

## <a name="query-a-subset-of-entity-properties"></a>consultar um subconjunto de propriedades da entidade
Uma consulta de tabela pode recuperar apenas algumas propriedades de uma entidade em vez de todas as propriedades da entidade. Essa técnica, chamada projeção, reduz a largura de banda e pode melhorar o desempenho da consulta, principalmente para grandes entidades. A consulta no código a seguir retorna somente os endereços de email das entidades na tabela. Isso é feito por meio de uma consulta de [DynamicTableEntity][dotnet_DynamicTableEntity] e também de [EntityResolver][dotnet_EntityResolver]. Saiba mais sobre projeção na [postagem de blog Apresentando a projeção de upsert e de consulta][blog_post_upsert]. A projeção não tem suporte pelo emulador de armazenamento e, portanto, esse código é executado somente ao usar uma conta no serviço Tabela.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Define the query, and select only the Email property.
TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

// Define an entity resolver to work with the entity after retrieval.
EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
{
    Console.WriteLine(projectedEmail);
}
```

## <a name="delete-an-entity"></a>Excluir uma entidade
Você pode excluir facilmente uma entidade após a recuperação usando o mesmo padrão mostrado para a atualização de uma entidade. O código a seguir recupera e exclui uma entidade de cliente.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
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
{
    Console.WriteLine("Could not retrieve the entity.");
}
```

## <a name="delete-a-table"></a>Excluir uma tabela
Finalmente, o exemplo de código a seguir exclui uma tabela de uma conta de armazenamento. Uma tabela excluída não estará disponível para ser recriada durante um período após a exclusão.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Delete the table it if exists.
table.DeleteIfExists();
```

## <a name="retrieve-entities-in-pages-asynchronously"></a>Recuperar entidades nas páginas de forma assíncrona
Se estiver lendo um grande número de entidades e quiser processar/exibir as entidades quando elas forem recuperadas em vez de esperar que todas sejam retornadas, você pode recuperar as entidades usando uma consulta segmentada. Este exemplo mostra como retornar resultados nas páginas usando o padrão Async-Await para que a execução não fique bloqueada enquanto você espera o retorno de um grande conjunto de resultados. Para obter mais detalhes sobre como usar o padrão Async-Await no .NET, consulte [Programação assíncrona com Async e Await (C# e Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

```csharp
// Initialize a default TableQuery to retrieve all the entities in the table.
TableQuery<CustomerEntity> tableQuery = new TableQuery<CustomerEntity>();

// Initialize the continuation token to null to start from the beginning of the table.
TableContinuationToken continuationToken = null;

do
{
    // Retrieve a segment (up to 1,000 entities).
    TableQuerySegment<CustomerEntity> tableQueryResult =
        await table.ExecuteQuerySegmentedAsync(tableQuery, continuationToken);

    // Assign the new continuation token to tell the service where to
    // continue on the next iteration (or null if it has reached the end).
    continuationToken = tableQueryResult.ContinuationToken;

    // Print the number of rows retrieved.
    Console.WriteLine("Rows retrieved {0}", tableQueryResult.Results.Count);

// Loop until a null continuation token is received, indicating the end of the table.
} while(continuationToken != null);
```

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu os conceitos básicos do armazenamento de Tabela, siga estes links para saber mais sobre tarefas de armazenamento mais complexas:

* [O Gerenciador de Armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo e gratuito da Microsoft que possibilita o trabalho visual com os dados do Armazenamento do Azure no Windows, MacOS e Linux.
* Ver mais exemplos de Armazenamento de Tabelas na [Introdução ao Armazenamento de Tabelas do Azure no .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)
* Consulte a documentação de referência do serviço Tabela para obter detalhes completos sobre as APIs disponíveis:
* [Referência à Biblioteca de Cliente de Armazenamento para .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
* [Referência da API REST](http://msdn.microsoft.com/library/azure/dd179355)
* Saiba como simplificar o código que você escreve para  trabalhar com o Armazenamento do Azure usando o [SDK do Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki)
* Consulte outros guias de recursos para obter informações sobre opções adicionais para armazenar dados no Azure.
* [Introdução ao armazenamento de Blobs do Azure usando o .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) para armazenar dados não estruturados.
* [Conectar-se ao Banco de Dados SQL usando .NET (C#)](../sql-database/sql-database-develop-dotnet-simple.md) para armazenar dados relacionais.

[Download and install the Azure SDK for .NET]: /develop/net/
[Creating an Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx

[blog_post_upsert]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx

[dotnet_api_ref]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[dotnet_CloudTableClient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtableclient.aspx
[dotnet_CloudTable]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.aspx
[dotnet_CloudTable_Execute]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.execute.aspx
[dotnet_CloudTable_ExecuteBatch]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.executebatch.aspx
[dotnet_DynamicTableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.dynamictableentity.aspx
[dotnet_EntityResolver]: https://msdn.microsoft.com/library/jj733144.aspx
[dotnet_TableBatchOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx
[dotnet_TableBatchOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.insert.aspx
[dotnet_TableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableentity.aspx
[dotnet_TableOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.aspx
[dotnet_TableOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insert.aspx
[dotnet_TableOperation_InsertOrReplace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insertorreplace.aspx
[dotnet_TableOperation_Replace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.replace.aspx
[dotnet_TableQuery]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablequery.aspx
[dotnet_TableResult]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.aspx
[dotnet_TableResult_Result]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.result.aspx
