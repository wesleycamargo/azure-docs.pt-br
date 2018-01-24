---
title: 'Azure Cosmos DB: Desenvolver com a API de Tabela no .NET | Microsoft Docs'
description: Aprenda a desenvolver com a API de Tabela do Azure Cosmos DB usando o .NET
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 4b22cb49-8ea2-483d-bc95-1172cd009498
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/18/2017
ms.author: arramac
ms.custom: mvc
ms.openlocfilehash: 41d7e42f203170e4fa3b8e3a8c973e23808f941b
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2017
---
# <a name="azure-cosmos-db-develop-with-the-table-api-in-net"></a>Azure Cosmos DB: Desenvolver com a API de Tabela no .NET

O Azure Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. É possível criar e consultar rapidamente documentos, chave/valor e bancos de dados do grafo. Todos se beneficiam de recursos de escala horizontal e distribuição global no núcleo do Azure Cosmos DB.

Este tutorial cobre as seguintes tarefas: 

> [!div class="checklist"] 
> * Criar uma conta do Azure Cosmos DB 
> * Habilitar a funcionalidade no arquivo app. config 
> * Criar uma tabela usando o [API de Tabela](table-introduction.md)
> * Adicionar uma entidade a uma tabela 
> * Inserir um lote de entidades 
> * Recuperar uma única entidade 
> * Consultar entidades usando índices secundários automáticos 
> * Substituir uma entidade 
> * Excluir uma entidade 
> * Excluir uma tabela
 
## <a name="tables-in-azure-cosmos-db"></a>Tabelas no Azure Cosmos DB 

O Azure Cosmos DB fornece a [API de Tabela](table-introduction.md) para aplicativos que precisam de um repositório de chave-valor com um design menos esquemático. Ambos os API de Tabela do Azure Cosmos DB e [armazenamento de Tabela do Azure](../storage/common/storage-introduction.md) agora oferecem suporte a mesma SDKs e APIs REST. Você pode usar o Azure Cosmos DB para criar tabelas com requisitos de alta taxa de transferência.

Este tutorial é para desenvolvedores que estão familiarizados com o SDK de armazenamento de Tabela do Azure e desejam usar os recursos premium disponíveis com o Azure Cosmos DB. Ele se baseia na [Introdução ao armazenamento de Tabelas do Azure usando o .NET](table-storage-how-to-use-dotnet.md) e mostra como aproveitar os recursos adicionais, como índices secundários, taxa de transferência provisionada e hospedagem múltipla. Este tutorial descreve como usar o portal do Azure para criar uma conta do Azure Cosmos DB e então criar e implantar um aplicativo de API de Tabela. Também explicaremos detalhadamente os exemplos de .NET para criar e excluir uma tabela e inserir, atualizar, excluir e consultar dados de tabela. 

Caso utilize o Armazenamento de Tabelas do Azure neste momento, você receberá os seguintes benefícios ao mudar para a API de Tabela do Azure Cosmos DB:

- [Distribuição global](distribute-data-globally.md) turnkey com hospedagem múltipla e [failovers automáticos e manuais](regional-failover.md)
- Suporte para indexação agnóstica de esquema automática em relação a todas as propriedades ("índices secundários") e consultas rápidas 
- Suporte para [dimensionamento independente de armazenamento e taxa de transferência](partition-data.md), em qualquer número de regiões
- Suporte para [taxa de transferência dedicada por tabela](request-units.md) que podem ser dimensionados para centenas a milhões de solicitações por segundo
- Suporte para [cinco níveis de consistência bem definidos](consistency-levels.md) para compensar a disponibilidade, latência e consistência com base nas necessidades do seu aplicativo
- 99,99% de disponibilidade dentro de uma única região e a capacidade de adicionar mais regiões para maior disponibilidade, e [SLAs abrangentes líderes do setor](https://azure.microsoft.com/support/legal/sla/cosmos-db/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) em disponibilidade geral
- Trabalhar com o SDK do .NET de armazenamento do Azure existente e nenhuma alteração de código no seu aplicativo

Este tutorial aborda a API de tabela do Azure Cosmos DB usando o SDK do .NET. Você pode fazer o download do [SDK de visualização do Armazenamento do Azure](https://aka.ms/tableapinuget) do NuGet.

Para saber mais sobre tarefas complexas de armazenamento de Tabelas do Azure, consulte:

* [Introdução ao Azure Cosmos DB API de Tabela](table-introduction.md)
* A documentação de referência do serviço Tabela para obter detalhes completos sobre as APIs disponíveis [API de Tabela do Azure Cosmos DB .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cosmosdb/client?view=azure-dotnet)

### <a name="about-this-tutorial"></a>Sobre este tutorial
Este tutorial é para desenvolvedores que estão familiarizados com o SDK de armazenamento de Tabelas do Azure e desejam usar os recursos premium disponíveis usando o Azure Cosmos DB. Ele se baseia na [Introdução ao armazenamento de Tabelas do Azure usando o .NET](table-storage-how-to-use-dotnet.md) e mostra como aproveitar os recursos adicionais, como índices secundários, taxa de transferência provisionada e hospedagem múltipla. Abordaremos como usar o portal do Azure para criar uma conta do Azure Cosmos DB e, em seguida, criar e implantar um aplicativo de Tabela. Também explicaremos detalhadamente os exemplos de .NET para criar e excluir uma tabela e inserir, atualizar, excluir e consultar dados de tabela. 

Se você ainda não tem o Visual 2017 Studio instalado, poderá baixar e usar o **Visual Studio 2017 Community Edition** [gratuito](https://www.visualstudio.com/downloads/). Verifique se você habilitou o **desenvolvimento do Azure** durante a instalação do Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Criar uma conta de banco de dados

Vamos começar criando uma conta do Azure Cosmos DB no portal do Azure.  
 
> [!IMPORTANT]  
> Você precisa criar uma nova conta de API de tabela para trabalhar com os SDKs de API de tabela disponíveis. Não há suporte para contas de API de tabela criadas durante a versão prévia pelos SDKs disponíveis. 
>

[!INCLUDE [cosmosdb-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)] 

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora, clonaremos um aplicativo de Tabela do github, definiremos a cadeia de conexão e o executaremos. Você verá como é fácil trabalhar usando dados de forma programática. 

1. Abra uma janela de terminal de git, como git bash, e use o comando `cd` para alterar para uma pasta para instalar o aplicativo de exemplo. 

    ```bash
    cd "C:\git-samples"
    ```

2. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador. 

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-dotnet-getting-started.git
    ```

3. Em seguida, abra o arquivo da solução no Visual Studio. 

## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

Agora, volte ao portal do Azure para obter informações sobre a cadeia de conexão e copiá-las para o aplicativo. Isso permite que seu aplicativo se comunique com o banco de dados hospedado. 

1. No [Portal do Azure](http://portal.azure.com/), clique em **Cadeia de Conexão**. 

    Use os botões de cópia no lado direito da tela para copiar a CADEIA DE CONEXÃO PRIMÁRIA.

    ![Exiba e copie a CADEIA DE CONEXÃO no painel Cadeia de Conexão](./media/create-table-dotnet/connection-string.png)

2. No Visual Studio, abra o arquivo app.config. 

3. Remova a marca de comentário da StorageConnectionString na linha 8 e comente a StorageConnectionString na linha 7, pois este tutorial não usa o Emulador de Armazenamento. As linhas 7 e 8 agora devem ser assim:

    ```
    <!--key="StorageConnectionString" value="UseDevelopmentStorage=true;" />-->
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
    ```

4. Cole o valor de CADEIA DE CONEXÃO PRIMÁRIA do portal no valor da StorageConnectionString na linha 8. Cole a cadeia de caracteres entre aspas.
   
    > [!IMPORTANT]
    > Se o ponto de extremidade usa documents.azure.com, isso significa que você tem uma conta de versão prévia, e você precisa criar um [nova conta de API de tabela](#create-a-database-account) para trabalhar com o SDK de API de tabela geralmente disponível. 
    >

    Agora, a linha 8 deve ser semelhante a:

    ```
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=txZACN9f...==;TableEndpoint=https://<account name>.table.cosmosdb.azure.com;" />
    ```

5. Salve o arquivo app.config.

Agora, você atualizou o aplicativo com todas as informações necessárias para se comunicar com o Azure Cosmos DB. 

## <a name="azure-cosmos-db-capabilities"></a>Recursos Azure Cosmos DB
O Azure Cosmos DB oferece suporte a uma série de recursos que não estão disponíveis na API de armazenamento de Tabelas do Azure API. 

Algumas funcionalidades são acessadas por meio de novas sobrecargas para CreateCloudTableClient que permitem que um especifique o nível de política e a consistência de conexão.

| Configurações de Conexão de Tabela | DESCRIÇÃO |
| --- | --- |
| Modo da conexão  | O Azure Cosmos DB oferece suporte a dois modos de conectividade. No modo `Gateway`, as solicitações são sempre feitas no gateway do Azure Cosmos DB, que encaminha para as partições de dados correspondentes. No modo de conectividade `Direct`, o cliente busca o mapeamento de tabelas de partições e as solicitações são feitas diretamente em partições de dados. Recomendamos `Direct`, o padrão.  |
| Protocolo de Conexão | O Azure Cosmos DB oferece suporte a dois protocolos de conexão - `Https` e `Tcp`. `Tcp` é o padrão e recomendado porque é mais leve. |
| Locais preferenciais | A lista separada por vírgulas de locais (hospedagem múltipla) preferenciais para leituras. Cada conta do Azure Cosmos DB pode ser associada a 1 ou 30 ou mais regiões. Cada instância do cliente pode especificar um subconjunto dessas regiões na ordem preferida para leituras de baixa latência. As regiões devem ser nomeadas usando seus [nomes de exibição](https://msdn.microsoft.com/library/azure/gg441293.aspx), por exemplo, `West US`. Consulte também [APIs de hospedagem múltipla](tutorial-global-distribution-table.md). |
| Nível de Consistência | Você pode alternar entre disponibilidade, consistência e latência escolhendo entre cinco níveis de consistência bem definidos: `Strong`, `Session`, `Bounded-Staleness`, `ConsistentPrefix` e `Eventual`. O padrão é `Session`. A escolha do nível de consistência faz uma diferença significativa de desempenho em configurações de várias regiões. Consulte [Níveis de consistência](consistency-levels.md) para saber detalhes. |

Outra funcionalidade pode ser habilitada através dos seguintes valores de configuração `appSettings`.

| Chave | DESCRIÇÃO |
| --- | --- |
| TableQueryMaxItemCount | Configure o número máximo de itens retornados por consultas de tabela em uma única viagem de ida e volta. O padrão é `-1`, que permite ao Azure Cosmos DB determinar dinamicamente o valor em tempo de execução. |
| TableQueryEnableScan | Se a consulta não pode usar o índice para qualquer filtro, em seguida, execute assim mesmo por meio de uma varredura. O padrão é `false`.|
| TableQueryMaxDegreeOfParallelism | O grau de paralelismo para execução de uma consulta entre partições. `0` é serial sem nenhuma busca prévia, `1` é serial com busca prévia e valores mais altos aumentam a taxa de paralelismo. O padrão é `-1`, que permite ao Azure Cosmos DB determinar dinamicamente o valor em tempo de execução. |

Para alterar o valor padrão, abra o arquivo `app.config` do Gerenciador de Soluções no Visual Studio. Adicionar o conteúdo do elemento `<appSettings>` mostrado abaixo. Substitua `account-name` pelo nome da sua conta de armazenamento e `account-key` pela chave de acesso da conta. 

```xml
<configuration>
    ...
    <appSettings>
      <!-- Client options -->
      <add key="CosmosDBStorageConnectionString" 
        value="DefaultEndpointsProtocol=https;AccountName=MYSTORAGEACCOUNT;AccountKey=AUTHKEY;TableEndpoint=https://account-name.table.cosmosdb.azure.com" />
      <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key; TableEndpoint=https://account-name.documents.azure.com" />

      <!-- Table query options -->
      <add key="TableQueryMaxItemCount" value="-1"/>
      <add key="TableQueryEnableScan" value="false"/>
      <add key="TableQueryMaxDegreeOfParallelism" value="-1"/>
      <add key="TableQueryContinuationTokenLimitInKb" value="16"/>
            
    </appSettings>
</configuration>
```

Façamos uma rápida revisão do que está acontecendo no aplicativo. Abra o arquivo `Program.cs` e veja que essas linhas de código criam os recursos da Tabela. 

## <a name="create-the-table-client"></a>Criar o cliente da tabela
Você deve inicializar um `CloudTableClient` para conectar-se à conta de tabela.

```csharp
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```
Esse cliente é inicializado usando os valores de configuração `TableConnectionMode`, `TableConnectionProtocol`, `TableConsistencyLevel` e `TablePreferredLocations` se especificados nas configurações do aplicativo.

## <a name="create-a-table"></a>Criar uma tabela
Em seguida, você cria uma tabela usando `CloudTable`. As tabelas no Azure Cosmos DB pode ser dimensionada independentemente em termos de armazenamento e taxa de transferência e o particionamento é tratado automaticamente pelo serviço. O Azure Cosmos DB oferece suporte a tabelas ilimitadas e tamanho fixo. Consulte [Particionamento no Azure Cosmos DB](partition-data.md) para saber detalhes. 

```csharp
CloudTable table = tableClient.GetTableReference("people");
400
table.CreateIfNotExists(throughput: 800);
```

Há uma diferença importante em como as tabelas são criadas. O Azure Cosmos DB reserva a taxa de transferência, ao contrário do modelo baseado em consumo do armazenamento do Azure para transações. A taxa de transferência é dedicada/reservada, para que você nunca sofra restrições se a taxa de solicitação estiver no limite ou abaixo da sua taxa de transferência provisionada.

Você pode configurar a taxa de transferência padrão incluindo-a como um parâmetro de CreateIfNotExists.

Uma leitura de uma entidade de 1 KB é normalizada como 1 RU e outras operações são normalizados para um valor fixo de RU com base em seu consumo de CPU, memória e IOPS. Saiba mais sobre [unidades no Azure Cosmos DB de solicitação](request-units.md) e especificamente para [repositórios de valor de chave](key-value-store-cost.md).

Em seguida, percorremos as operações simples de leitura e gravação (CRUD) usando o SDK de armazenamento de Tabelas do Azure. Este tutorial demonstra latências baixas de milissegundos de dígito único previsíveis e consultas rápidas fornecidas pelo Azure Cosmos DB.

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela
As entidades no armazenamento de Tabelas do Azure estendem-se a partir da classe `TableEntity` e deve ter as propriedades `PartitionKey` e `RowKey`. Aqui está um exemplo de definição de uma entidade de cliente.

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

O trecho a seguir mostra como inserir uma entidade com o SDK do armazenamento do Azure. O Azure Cosmos DB destina-se à garantia de baixa latência em qualquer escala, em todo o mundo.

As gravações são concluídas em menos de 15 ms em p99 e ~6 ms em p50 para aplicativos executados na mesma região que a conta do Azure Cosmos DB. E essa duração de considera o fato de que as gravações são confirmadas para o cliente somente depois que eles são replicados de maneira síncrona, com confirmação permanentemente, e com todo o conteúdo indexado.


```csharp
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
O armazenamento de tabela do Azure suporta uma API de operação em lote, que permite que você combine atualizações, exclusões e inserções na mesma operação de lote.

```csharp
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
## <a name="retrieve-a-single-entity"></a>Recuperar uma única entidade
As recuperações (GETs) no Azure Cosmos DB são concluídas em menos de 10 ms em p99 e ~1 ms em p50 na mesma região do Azure. Você pode adicionar a mesma quantidade de regiões à sua conta para leituras de baixa latência e implantar aplicativos para leitura da sua região local (com hospedagem múltipla), definindo `TablePreferredLocations`. 

Você pode recuperar uma única entidade usando o trecho a seguir:

```csharp
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = table.Execute(retrieveOperation);
```
> [!TIP]
> Saiba mais sobre as APIs de hospedagem múltipla em [Desenvolvendo com várias regiões](tutorial-global-distribution-table.md)
>

## <a name="query-entities-using-automatic-secondary-indexes"></a>Consultar entidades usando índices secundários automáticos
Tabelas podem ser consultadas usando a classe `TableQuery`. O Azure Cosmos DB tem um mecanismo de banco de dados otimizado para gravação que indexa automaticamente todas as colunas em sua tabela. A indexação no Azure Cosmos DB é agnóstica em relação ao esquema. Portanto, mesmo que seu esquema seja diferente entre as linhas, ou se o esquema evoluir ao longo do tempo, ele será indexado automaticamente. Como o Azure Cosmos DB oferece suporte a índices secundários automáticos, as consultas em relação a qualquer propriedade podem usar o índice e serem apresentadas de forma eficiente.

```csharp
CloudTable table = tableClient.GetTableReference("people");

// Filter against a property that's not partition key or row key
TableQuery<CustomerEntity> emailQuery = new TableQuery<CustomerEntity>().Where(
    TableQuery.GenerateFilterCondition("Email", QueryComparisons.Equal, "Ben@contoso.com"));

foreach (CustomerEntity entity in table.ExecuteQuery(emailQuery))
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
}
```

O Azure Cosmos DB oferece suporte à mesma funcionalidade de consulta que o armazenamento de tabela do Azure para a API de Tabela. O Azure Cosmos DB também oferece suporte à classificação, agregações, consulta geoespacial, hierarquia e uma ampla variedade de funções internas. Os recursos adicionais serão fornecidos na API de Tabela em uma atualização futura do serviço. Consulte [Consulta do Azure Cosmos DB](sql-api-sql-query.md) para uma visão geral desses recursos. 

## <a name="replace-an-entity"></a>Substituir uma entidade
Para atualizar uma entidade, recupere-a do serviço Tabela, modifique o objeto de entidade e, em seguida, salve as alterações novamente no serviço Tabela. O código a seguir altera o número de telefone de um cliente existente. 

```csharp
TableOperation updateOperation = TableOperation.Replace(updateEntity);
table.Execute(updateOperation);
```
Da mesma forma, você pode executar as operações `InsertOrMerge` ou `Merge`.  

## <a name="delete-an-entity"></a>Excluir uma entidade
Você pode excluir facilmente uma entidade após a recuperação usando o mesmo padrão mostrado para a atualização de uma entidade. O código a seguir recupera e exclui uma entidade de cliente.

```csharp
TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
table.Execute(deleteOperation);
```

## <a name="delete-a-table"></a>Excluir uma tabela
Finalmente, o exemplo de código a seguir exclui uma tabela de uma conta de armazenamento. Você pode excluir e recriar uma tabela imediatamente com o Azure Cosmos DB.

```csharp
CloudTable table = tableClient.GetTableReference("people");
table.DeleteIfExists();
```

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, abordamos como começar a usar o Azure Cosmos DB com a API de Tabela e você tiver feito o seguinte: 

> [!div class="checklist"] 
> * Criou uma conta do Azure Cosmos DB 
> * Habilitou a funcionalidade no arquivo app. config 
> * Criou uma tabela 
> * Adicionou uma entidade a uma tabela 
> * Inseriu um lote de entidades 
> * Recuperou uma única entidade 
> * Consultou entidades usando índices secundários automáticos 
> * Substituiu uma entidade 
> * Excluiu uma entidade 
> * Excluiu uma tabela  

Agora você pode prosseguir para o próximo tutorial e saber mais sobre como consultar dados da tabela. 

> [!div class="nextstepaction"]
> [Consulta com a API de Tabela](tutorial-query-table.md)
