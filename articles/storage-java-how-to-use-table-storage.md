<properties linkid="dev-java-how-to-use-table-storage" urlDisplayName="Serviço de tabelas" pageTitle="Como usar o armazenamento de tabelas (Java) | Microsoft Azure" metaKeywords="Serviço de armazenamento de tabelas no Azure, serviço Tabelas do Java no Azure, armazenamento de tabelas do Java" description="Saiba como usar o serviço de armazenamento de tabelas no Azure. Os exemplos de código são escritos no código Java." metaCanonical="" services="storage" documentationCenter="Java" title="Como usar o serviço de Armazenamento de tabelas do Java" authors="" solutions="" manager="" editor="" />


# Como usar o serviço de Armazenamento de tabelas do Java

Este guia mostra como executar cenários comuns usando o serviço de Armazenamento de tabelas no Azure. As amostras são escritas no código Java. Os cenários abordados incluem **criar e excluir uma
tabela e inserir e consultar entidades em uma tabela**. Para obter mais informações sobre tabelas, consulte a seção [Próximas etapas](#NextSteps).

## <a name="Contents"> </a>Sumário

* [O que é um Armazenamento de tabelas?](#what-is)
* [Conceitos](#Concepts)
* [Criar uma conta de armazenamento do Azure](#CreateAccount)
* [Criar um aplicativo Java](#CreateApplication)
* [Configurar seu aplicativo para acessar o Armazenamento de tabelas](#ConfigureStorage)
* [Configurar uma cadeia de conexão de armazenamento do Azure](#ConnectionString)
* [Como criar uma tabela](#CreateTable)
* [Como adicionar uma entidade a uma tabela](#AddEntity)
* [Como inserir um lote de entidades](#InsertBatch)
* [Como recuperar todas as entidades em uma partição](#RetrieveEntities)
* [Como recuperar um intervalo de entidades em uma partição](#RetrieveRange)
* [Como recuperar uma única entidade](#RetriveSingle)
* [Como modificar uma entidade](#ModifyEntity)
* [Como consultar um subconjunto de propriedades de entidade](#QueryProperties)
* [Como inserir ou substituir uma entidade](#InsertOrReplace)
* [Como excluir uma entidade](#DeleteEntity)
* [Como excluir uma tabela](#DeleteTable)
* [Próximas etapas](#NextSteps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

##<a name="CreateAccount"></a>Criar uma conta de armazenamento do Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="CreateApplication"></a>Criar um aplicativo Java

Neste guia, você usará os recursos de armazenamento que podem ser executados em um aplicativo Java localmente ou no código em execução em uma função web ou função de trabalho do Azure. Supomos que você baixou e instalou o Java Development Kit (JDK), seguiu as instruções em [Baixe o SDK do Azure para Java] para instalar as Bibliotecas do Azure para Java e o SDK do Azure e criou uma conta de armazenamento do Azure em sua assinatura do Azure.

É possível usar as ferramentas de desenvolvimento para criar seu aplicativo, incluindo o Bloco de Notas. Tudo o que você precisa é a capacidade de compilar um projeto do Java e fazer a referência das bibliotecas do Azure para o Java.

## <a name="ConfigureStorage"> </a>Configurar seu aplicativo para acessar o Armazenamento de tabelas

Adicione as seguintes instruções de importação na parte superior do arquivo do Java em que deseje usar as APIs de armazenamento do Azure para acessar as tabelas:

    // Include the following imports to use table APIs
    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.table.client.*;
    import com.microsoft.windowsazure.services.table.client.TableQuery.*;

## <a name="ConnectionString"> </a>Configurar uma cadeia de conexão de armazenamento do Azure

Um cliente de armazenamento do Azure usa uma cadeia de conexão para armazenar pontos de extremidade e credenciais para acesso aos serviços de gerenciamento de dados. Ao ser executado em um aplicativo cliente, é necessário fornecer a cadeia de conexão de armazenamento no formato a seguir, usando o nome da sua conta de armazenamento e a Chave de acesso primária da conta de armazenamento listada no Portal de Gerenciamento para os valores *AccountName* e *AccountKey*. Este exemplo mostra como declarar um campo estático para armazenar a cadeia de conexão:

    // Define the connection-string with your values
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

Em um aplicativo que esteja sendo executado em uma função no Azure, essa cadeia pode ser armazenada no arquivo de configuração do serviço, ServiceConfiguration.cscfg, e pode ser acessada com uma chamada para o
método **RoleEnvironment.getConfigurationSettings**. Segue um exemplo de como obter a cadeia de conexão de um elemento denominado **Setting**
*StorageConnectionString* no arquivo de configuração do serviço::

    // Retrieve storage account from connection-string
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

Os exemplos abaixo pressupõem que você usou uma dessas duas definições para obter a cadeia de conexão do armazenamento.

## <a name="CreateTable"> </a>Como criar uma tabela

Um objeto **CloudTableClient** permite que você obtenha os objetos de referência para tabelas e entidades. O código a seguir cria um objeto **CloudTableClient** e usa-o para criar uma nova tabela. Todos os códigos deste guia usam uma cadeia de conexão de armazenamento
armazenada na configuração de serviço do aplicativo do Azure. Também há outras maneiras de criar
um objeto **CloudStorageAccount**.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Create the table if it doesn't exist.
    String tableName = "people";
    tableClient.createTableIfNotExists(tableName);

## <a name="AddEntity"> </a>Como adicionar uma entidade a uma tabela

As entidades mapeiam para os objetos do Java que usam uma classe personalizada, implementando
**TableEntity**. Para sua conveniência, a classe **TableServiceEntity** implementa **TableEntity** e usa a reflexão para mapear as propriedades para os métodos getter e setter nomeados para as propriedades. Para adicionar uma entidade a uma tabela, primeiro crie uma
classe que defina as propriedades da sua entidade. O código a seguir
define uma classe de entidade que usa o nome do cliente como a chave de linha
e o sobrenome como a chave de partição. Juntas, uma chave de partição e uma chave de linha identificam exclusivamente a entidade na tabela. As entidades com a
mesma chave de partição podem ser consultadas mais rápido do que as com chaves de partição
diferentes.

    public class CustomerEntity extends TableServiceEntity {
        public CustomerEntity(String lastName, String firstName) {
            this.partitionKey = lastName;
            this.rowKey = firstName;
        }

        public CustomerEntity() { }

        String email;
        String phoneNumber;
        
        public String getEmail() {
            return this.email;
        }
        
        public void setEmail(String email) {
            this.email = email;
        }
        
        public String getPhoneNumber() {
            return this.phoneNumber;
        }
        
        public void setPhoneNumber(String phoneNumber) {
            this.phoneNumber = phoneNumber;
        }
    }

As operações de tabela que envolvem entidades exigem um
objeto **TableOperation**.
Este objeto define a operação a ser executada em uma entidade, que pode ser executada com um objeto **CloudTableClient**. O código a seguir cria uma nova instância da classe **CustomerEntity** com alguns dados do cliente que serão armazenados. O código então chama
**TableOperation.insert** para criar um objeto **TableOperation** para inserir uma entidade em uma tabela e associa a nova **CustomerEntity**
a ele. Por fim, o código chama o método **execute** no
**CloudTableClient**, especificando a tabela "pessoas" e a nova
**TableOperation**, que envia uma solicitação para o serviço de armazenamento para inserir a nova entidade de cliente na tabela "pessoas".

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.setEmail("Walter@contoso.com");
    customer1.setPhoneNumber("425-555-0101");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer1 = TableOperation.insert(customer1);

    // Submit the operation to the table service.
    tableClient.execute("people", insertCustomer1);

## <a name="InsertBatch"> </a>Como inserir um lote de entidades

Você pode inserir um lote de entidades ao serviço Tabela em uma
operação de gravação. O código a seguir cria um objeto **TableBatchOperation** e adiciona três operações de inserção a ele. Cada operação de inserção é adicionada, criando um novo objeto de entidade, definindo seus valores e, em seguida, chamando o método **insert** no objeto **TableBatchOperation** para associar a entidade a uma nova operação de inserção. Em seguida, o código chama **execute**
no **CloudTableClient**, especificando a tabela "pessoas" e o
objeto **TableBatchOperation**, que envia o lote de operações da tabela para o serviço de armazenamento em uma única solicitação. Algumas outras observações sobre as operações em lote:

1.  Você pode executar até 100 operações de inserção, exclusão, mesclagem, substituição, inserção ou mesclagem e inserção ou substituição em qualquer combinação, em um único lote.
2.  Uma operação em lote pode ter uma operação de recuperação, se ela for a única operação no lote.
3.  Todas as entidades em uma única operação em lote devem ter a mesma chave de partição.
4.  Uma operação em lote está limitada a uma carga de dados de 4 MB.

<!-- -->

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Define a batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a customer entity to add to the table.
    CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    customer.setEmail("Jeff@contoso.com");
    customer.setPhoneNumber("425-555-0104");
    batchOperation.insert(customer);

    // Create another customer entity to add to the table.
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.setEmail("Ben@contoso.com");
    customer2.setPhoneNumber("425-555-0102");
    batchOperation.insert(customer2);

    // Create a third customer entity to add to the table.
    CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
    customer3.setEmail("Denise@contoso.com");
    customer3.setPhoneNumber("425-555-0103");
    batchOperation.insert(customer3);

    // Execute the batch of operations on the "people" table.
    tableClient.execute("people", batchOperation);

## <a name="RetrieveEntities"> </a>Como recuperar todas as entidades em uma partição

Para consultar uma tabela de todas as entidades em uma partição, use uma
**TableQuery**. Chame **TableQuery.from** para criar uma consulta em uma determinada tabela que retorna um tipo de resultado especificado. O código a seguir especifica um filtro para as entidades onde 'Smith' é a chave de partição.
**TableQuery.generateFilterCondition** é um método auxiliar para criar filtros para consultas. Chame **where** na referência retornada pelo
método **TableQuery.from** para aplicar o filtro à consulta. Quando a consulta é executada com uma chamada para **execute** no objeto **CloudTableClient**, ela retorna um **Iterator** com o tipo de resultado especificado da **CustomerEntity**. Você pode usar o **Iterator** retornado em cada loop para consumir os resultados. Esse código imprime os campos de
cada entidade nos resultados da consulta no console.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        TableConstants.PARTITION_KEY, 
        QueryComparisons.EQUAL,
        "Smith");

    // Specify a partition query, using "Smith" as the partition key filter.
    TableQuery<CustomerEntity> partitionQuery =
        TableQuery.from("people", CustomerEntity.class)
        .where(partitionFilter);

    // Loop through the results, displaying information about the entity.
    for (CustomerEntity entity : tableClient.execute(partitionQuery)) {
        System.out.println(entity.getPartitionKey() + " " + entity.getRowKey() + 
            "\t" + entity.getEmail() + "\t" + entity.getPhoneNumber());
    }

## <a name="RetrieveRange"> </a>Como recuperar um intervalo de entidades em uma partição

Se não desejar consultar todas as entidades em uma partição, você poderá especificar um intervalo, usando os operações de comparação em um filtro. O código a seguir combina dois filtros para obter todas as entidades na partição 'Smith', onde
a chave de linha (nome) começa com uma letra até a letra 'E' do alfabeto.
Em seguida, ele
imprime os resultados da consulta. Se você usar as entidades adicionadas à tabela
na seção de inserção do lote deste guia, apenas duas entidades serão retornadas
desta vez (Ben e Denise Smith); Jeff Smith não será incluído.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Create a filter condition where the partition key is "Smith".
    String partitionFilter = TableQuery.generateFilterCondition(
        TableConstants.PARTITION_KEY, 
        QueryComparisons.EQUAL,
        "Smith");

    // Create a filter condition where the row key is less than the letter "E".
    String rowFilter = TableQuery.generateFilterCondition(
        TableConstants.ROW_KEY, 
        QueryComparisons.LESS_THAN,
        "E");

    // Combine the two conditions into a filter expression.
    String combinedFilter = TableQuery.combineFilters(partitionFilter, 
            Operators.AND, rowFilter);

    // Specify a range query, using "Smith" as the partition key,
    // with the row key being up to the letter "E".
    TableQuery<CustomerEntity> rangeQuery =
        TableQuery.from("people", CustomerEntity.class)
        .where(combinedFilter);

    // Loop through the results, displaying information about the entity
    for (CustomerEntity entity : tableClient.execute(rangeQuery)) {
        System.out.println(entity.getPartitionKey() + " " + entity.getRowKey() + 
            "\t" + entity.getEmail() + "\t" + entity.getPhoneNumber());
    }

## <a name="RetriveSingle"> </a>Como recuperar uma única entidade

Você pode escrever uma consulta para recuperar uma entidade única e específica. O código a seguir chama **TableOperation.retrieve** com uma chave de partição e parâmetros da chave de linha para especificar o cliente 'Jeff Smith', em vez de criar uma **TableQuery** e usar filtros para executar a mesma tarefa. Quando executada, a operação de recuperação retorna apenas uma entidade, em vez de um conjunto. O método **getResultAsType** converte o resultado para o tipo de destino da atribuição, um objeto **CustomerEntity**. Se este tipo não for compatível com o tipo especificado na consulta, uma exceção será lançada. Um valor nulo será retornado se nenhuma entidade tiver uma
correspondência exata da chave de partição e da chave de linha. A especificação de chaves de partição e de linha
em uma consulta é a maneira mais rápida de recuperar uma única entidade do
serviço Tabelas.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Retrieve the entity with partition key of "Smith" and row key of "Jeff"
    TableOperation retrieveSmithJeff = 
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        tableClient.execute("people", retrieveSmithJeff).getResultAsType();

## <a name="ModifyEntity"> </a>Como modificar uma entidade

Para modificar uma entidade, recupere-a do serviço Tabelas, altere o objeto da entidade e, em seguida, salve as alterações novamente no serviço Tabelas com uma operação de substituição ou mesclagem. O código a seguir altera o número de telefone de um cliente existente. Em vez de chamar **TableOperation.insert**
como fizemos para inserir, esse código chama **TableOperation.replace**. O
método **CloudTableClient.execute** chamará o serviço Tabelas
e a entidade será atualizada, a menos que um outro aplicativo a tenha alterado desde que ela foi recuperada por esse aplicativo. Quando isso acontece,
uma exceção é lançada, e a entidade deve ser recuperada, modificada e
salva novamente.
Esse padrão de repetição de simultaneidade otimista é comum em um sistema de
armazenamento distribuído.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff = 
        TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Submit the operation to the table service and get the specific entity.
    CustomerEntity specificEntity =
        tableClient.execute("people", retrieveSmithJeff).getResultAsType();
        
    // Specify a new phone number.
    specificEntity.setPhoneNumber("425-555-0105");

    // Create an operation to replace the entity.
    TableOperation replaceEntity = TableOperation.replace(specificEntity);

    // Submit the operation to the table service.
    tableClient.execute("people", replaceEntity);

## <a name="QueryProperties"> </a>Como consultar um subconjunto de propriedades de entidade

Uma consulta a uma tabela pode recuperar apenas algumas propriedades de uma entidade.
Essa técnica, denominada projeção, reduz a largura de banda e pode melhorar
o desempenho da consulta, principalmente para grandes entidades. A consulta no código a seguir usa o método **select** para retornar apenas os endereços de e-mail das entidades na tabela. Os resultados são projetados em um conjunto de **String** com a ajuda de um **EntityResolver**, que faz a conversão de tipo nas entidades retornadas do servidor. Você pode saber mais sobre projeção nesta [postagem de blog][]. Observe
que essa projeção não tem suporte no emulador de armazenamento local; portanto, esse
código apenas será executado quando uma conta do serviço Tabelas for usada.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Define a projection query that retrieves only the Email property
    TableQuery<CustomerEntity> projectionQuery = 
        TableQuery.from("people", CustomerEntity.class)
        .select(new String[] {"Email"});

    // Define a Entity resolver to project the entity to the Email value.
    EntityResolver<String> emailResolver = new EntityResolver<String>() {
        @Override
        public String resolve(String PartitionKey, String RowKey, Date timeStamp,
                HashMap<String, EntityProperty> properties, String etag) {
            return properties.get("Email").getValueAsString();
        }
    };

    // Loop through the results, displaying the Email values.
    for (String projectedString : 
            tableClient.execute(projectionQuery, emailResolver)) {
        System.out.println(projectedString);
    }

## <a name="InsertOrReplace"> </a>Como inserir ou substituir uma entidade

Em geral, você deseja adicionar uma entidade a uma tabela sem saber se ela já
existe na tabela. Uma operação de inserção ou substituição permite que você faça uma
única solicitação que inserirá a entidade, se ela não existir, ou
substituirá a existente, se ela existir. Com base nos exemplos anteriores, o
código a seguir insere ou substitui a entidade para 'Walter Harp'. Depois de
criar uma nova entidade, esse código chamará o
método **TableOperation.insertOrReplace**. Em seguida, esse código chamará
**execute** no **CloudTableClient** com a tabela e a operação de inserção ou substituição de tabela como os parâmetros. Para atualizar somente a parte de uma entidade, o método **TableOperation.insertOrMerge** pode ser usado em vez disso.
Observe que a operação de inserção ou substituição não tem suporte no emulador de armazenamento local; portanto, esse código apenas será executado quando uma
conta do serviço Tabelas for usada. Você poderá saber mais sobre as operações de inserção ou substituição e de inserção ou mesclagem nesta [postagem de blog][].

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Create a new customer entity.
    CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
    customer5.setEmail("Walter@contoso.com");
    customer5.setPhoneNumber("425-555-0106");

    // Create an operation to add the new customer to the people table.
    TableOperation insertCustomer5 = TableOperation.insertOrReplace(customer5);

    // Submit the operation to the table service.
    tableClient.execute("people", insertCustomer5);

## <a name="DeleteEntity"> </a>Como excluir uma entidade

Você poderá excluir uma entidade facilmente depois que recuperá-la. Depois que a entidade for recuperada, chame **TableOperation.delete** com a entidade
para excluir. Em seguida, chame **execute** no **CloudTableClient**. O código a seguir recupera e exclui uma entidade do cliente.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
    TableOperation retrieveSmithJeff = TableOperation.retrieve("Smith", "Jeff", CustomerEntity.class);

    // Retrieve the entity with partition key of "Smith" and row key of "Jeff".
    CustomerEntity entitySmithJeff =
            tableClient.execute("people", retrieveSmithJeff).getResultAsType();

    // Create an operation to delete the entity.
    TableOperation deleteSmithJeff = TableOperation.delete(entitySmithJeff);

    // Submit the delete operation to the table service.
    tableClient.execute("people", deleteSmithJeff);

## <a name="DeleteTable"> </a>Como excluir uma tabela

Finalmente, o código a seguir excluirá uma tabela de uma conta de armazenamento. Uma tabela que tenha sido excluída não estará disponível para ser recriada por um período de tempo após a exclusão, geralmente menos de quarenta segundos.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);
     
    // Create the table client.
    CloudTableClient tableClient = storageAccount.createCloudTableClient();
     
    // Delete the table and all its data if it exists.
    tableClient.deleteTableIfExists("people");

## <a name="NextSteps"> </a>Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de tabela, siga estes links para saber como executar tarefas de armazenamento mais complexas.

-   Consulte a Referência do MSDN: [Armazenando e acessando dados no Windows
    Azure]
-   Acesse o [Blog da Equipe de Armazenamento do Azure][]


[Usando a API do REST]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh264518.aspx
[Postagem de blog]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
[Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx
[Blog da Equipe de Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
[Baixe o SDK do Azure para Java]: http://www.windowsazure.com/pt-br/develop/java/
[Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx

