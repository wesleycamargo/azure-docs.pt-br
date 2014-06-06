<properties linkid="dev-net-2-how-to-table-services" urlDisplayName="Serviço Tabela (2.0)" pageTitle="Como usar o armazenamento de tabela - Guia de recursos do Microsoft Azure" metaKeywords="Introdução à tabela do Azure, nosql do Azure, grande repositório de dados estruturados do Azure, tabela do Azure, armazenamento de tabela do Azure, .NET de tabela do Azure, .NET do armazenamento de tabela do Azure, C# de tabela do Azure, C# de armazenamento de tabela do Azure" description="Saiba como usar o armazenamento de tabela para criar e excluir tabelas e inserir e consultar entidades em uma tabela." metaCanonical="" services="storage" documentationCenter=".NET" title="Como usar o Serviço de Armazenamento de Tabela" authors="" solutions="" manager="paulettm" editor="cgronlun" />








# Como usar o Serviço de Armazenamento de Tabela

<div class="dev-center-tutorial-selector">
<a href="/pt-br/develop/net/how-to-guides/table-services-v17/" title="versão 1.7" class="current">versão 1.7</a>
<a href="/pt-br/develop/net/how-to-guides/table-services/" title="versão 2.0">versão 2.0</a> 
</div>


Este guia mostra como executar cenários comuns usando o serviço de Armazenamento de tabelas no Azure. Os exemplos são escritos em código C\#
e usam a API do .NET. Os cenários abordados incluem **criação e
exclusão de uma tabela, inserção e consulta de entidades em uma tabela**. Para obter mais informações sobre tabelas, consulte a seção [Próximas etapas][].

## Sumário

-   [O que é o serviço Tabela][]
-   [Conceitos][]
-   [Criar uma conta de Armazenamento do Azure][]
-   [Configurar uma cadeia de conexão de armazenamento][]
-   [Como acessar programaticamente o armazenamento de tabela][]
-   [Como: criar uma tabela][]
-   [Como: adicionar uma entidade a uma tabela][]
-   [Como: inserir um lote de entidades][]
-   [Como: recuperar todas as entidades em uma partição][]
-   [Como: recuperar um intervalo de entidades em uma partição][]
-   [Como: recuperar uma única entidade][]
-   [Como: atualizar uma entidade][]
-   [Como: consultar um subconjunto de propriedades de entidade][]
-   [Como: inserir ou substituir uma entidade][]
-   [Como excluir uma entidade][]
-   [Como excluir uma tabela][]
-   [Próximas etapas][]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

<h2><a name="create-account"></a><span class="short-header">Criar uma conta</span>Criar uma Conta de armazenamento do Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a name="setup-connection-string"></a><span class="short-header">Configurar uma cadeia de conexão</span>Configurar uma cadeia de conexão de armazenamento</h2>

A API de armazenamento do .NET no Azure oferece suporte ao uso de uma cadeia de conexão
de armazenamento para configurar pontos de extremidade e credenciais para acessar serviços
de armazenamento. Você pode colocar a cadeia de conexão de armazenamento em um arquivo de configuração, em vez de embuti-la no código:

- Ao usar os Serviços de Nuvem do Azure, é recomendável armazenar a cadeia de conexão usando o sistema de configuração de serviço do Azure (arquivos `*.csdef` e `*.cscfg`).
- Ao usar Sites do Azure ou Máquinas Virtuais do Azure, é recomendável armazenar a cadeia de conexão usando o sistema de configuração do .NET (por exemplo, arquivos `web.config`).

Nos dois casos, você pode recuperar a cadeia de conexão usando o método `CloudConfigurationManager.GetSetting`, conforme mostrado neste guia.

### Configurando a cadeia de conexão ao usar os Serviços de Nuvem

O mecanismo de configuração de serviço é exclusivo para projetos de
Serviços de Nuvem do Azure e permite que você altere dinamicamente
os parâmetros de configuração no Portal de Gerenciamento do Azure sem
reimplantar o aplicativo.

Para configurar a cadeia de conexão na configuração de serviço
do Azure:

1.  No Gerenciador de Soluções do Visual Studio, na pasta **Funções**
de seu Projeto de Implantação do Azure, clique com o botão direito do mouse
na função web ou função de trabalho e clique em **Propriedades**.  
    ![Blob5][Blob5]

2.  Clique na guia **Configurações** e pressione o botão **Adicionar Configuração**.  
    ![Blob6][Blob6]

    Uma nova entrada **Setting1** será mostrada na grade de configurações.

3.  No menu suspenso **Tipo** da nova entrada **Setting1**, escolha
**Cadeia de Conexão**.  
    ![Blob7][Blob7]

4.  Clique no botão **...** na extremidade direita da entrada **Setting1**.
    A caixa de diálogo **Cadeia de Conexão da Conta de Armazenamento** será aberta.

5. Escolha se deseja direcionar o emulador de armazenamento (o Armazenamento
do Azure simulado em sua máquina local) ou uma conta de armazenamento
real na nuvem. O código deste guia funciona com qualquer uma dessas opções. Digite o valor de **Chave de Acesso Primária** copiado na
etapa anterior deste tutorial se desejar armazenar dados de blob na
conta de armazenamento criada anteriormente no Azure.   
    ![Blob8][Blob8]

6.  Altere a entrada **Nome** de **Setting1** para um nome mais amigável,
como **StorageConnectionString**. Você fará referência a essa
cadeia de conexão mais tarde no código deste guia.  
    ![Blob9][Blob9]
	
### Configurando a cadeia de conexão ao usar Sites ou Máquinas Virtuais

Ao usar Sites ou Máquinas Virtuais, é recomendável usar o sistema de configuração do .NET (por exemplo, `web.config`).  Você pode armazenar a cadeia de conexão usando o elemento `<appSettings>`:

	<configuration>
	    <appSettings>
		    <add key="StorageConnectionString"
			     value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
		</appSettings>
	</configuration>

Leia [Configurando cadeias de conexão][] para obter mais informações sobre cadeias de conexão de armazenamento.
	
Agora você está pronto para executar as tarefas das instruções deste guia.


<h2> <a name="configure-access"> </a><span  class="short-header">Acessar programaticamente</span>Como acessar o armazenamento de tabela programaticamente</h2>

Adicione as seguintes declarações de namespace de código à parte superior de qualquer arquivo C\#
no qual você deseja acessar o Armazenamento do Azure programaticamente:

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.StorageClient;

Você pode usar o tipo **CloudStorageAccount** e
o tipo **CloudConfigurationManager**
para recuperar sua cadeia de conexão de armazenamento e as informações
da conta de armazenamento da configuração de serviço do Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));


<h2><a name="create-table"></a><span class="short-header">Criar uma tabela</span>Como criar uma tabela</h2>

Um objeto **CloudTableClient** permite que você obtenha os objetos de referência para tabelas e entidades. O código a seguir cria um objeto **CloudTableClient** e usa-o para criar uma nova tabela. Todos os códigos deste guia usam uma cadeia de conexão de armazenamento
armazenada na configuração de serviço do aplicativo do Azure. Também há outras maneiras de criar
um objeto **CloudStorageAccount**.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the table if it doesn't exist
    string tableName = "people";
    tableClient.CreateTableIfNotExist(tableName);

<h2><a name="add-entity"></a><span class="short-header">Adicionar uma entidade a uma tabela</span>Como adicionar uma entidade a uma tabela</h2>

As entidades mapeiam para objetos C\# usando uma classe personalizada derivada de
**TableServiceEntity**. Para adicionar uma entidade a uma tabela, primeiro crie uma
classe que defina as propriedades da sua entidade. O código a seguir
define uma classe de entidade que usa o nome do cliente como a chave de linha
e o sobrenome como a chave de partição. Juntas, uma chave de partição e uma chave de linha identificam exclusivamente a entidade na tabela. As entidades com a
mesma chave de partição podem ser consultadas mais rápido do que as com chaves de partição
diferentes.

    public class CustomerEntity : TableServiceEntity
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

As operações de tabela que envolvem entidades exigem um objeto **TableServiceContext**. Esse objeto acompanha o estado do lado do cliente de todas as entidades de tabela
criadas e acessadas no código cliente. A manutenção de um objeto do lado do cliente
que representa cada entidade torna mais eficiente a gravação de operações, pois
atualizados apenas os objetos com alterações são atualizados no serviço Tabela quando
as operações de salvamento são executados. O código a seguir cria um objeto
**TableServiceContext** chamando o método **GetDataServiceContext**. Em seguida, o código cria uma instância da classe **CustomerEntity**. O código chama o **serviceContext.AddObject** para inserir a nova
entidade na tabela. Isso adiciona o objeto de entidade ao
**serviceContext**, mas não ocorre nenhuma operação de serviço. Finalmente, o código envia a nova entidade ao serviço Tabela quando o
método **SaveChangesWithRetries** é chamado.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Create a new customer entity
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Add the new customer to the people table
    serviceContext.AddObject("people", customer1);

    // Submit the operation to the table service
    serviceContext.SaveChangesWithRetries();

<h2><a name="insert-batch"></a><span class="short-header">Inserir um lote de entidades</span>Como inserir um lote de entidades</h2>

Você pode inserir um lote de entidades ao serviço Tabela em uma
operação de gravação. O código a seguir cria três objetos de entidade e adiciona cada um deles ao contexto de serviço usando o método **AddObject**. Em seguida, o código chama **SaveChangesWithRetries** com o parâmetro **SaveChangesOptions.Batch**. Se você omitir **SaveChangesOptions.Batch**, ocorrerão três chamadas separadas para o serviço Tabela. Algumas outras observações sobre operações em lote:

1.  Você pode executar atualizações, exclusões ou inserções em lotes.
2.  Uma única operação em lotes pode incluir até 100 entidades.
3.  Todas as entidades em uma única operação em lote devem ter a mesma chave de partição.

<!-- -->

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    string tableName = "people";

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Create a customer entity and add to the table
    CustomerEntity customer = new CustomerEntity("Smith", "Jeff");
    customer.Email = "Jeff@contoso.com";
    customer.PhoneNumber = "425-555-0104";
    serviceContext.AddObject(tableName, customer);

    // Create another customer entity and add to the table
    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    customer2.PhoneNumber = "425-555-0102";
    serviceContext.AddObject(tableName, customer2);

    // Create a customer entity and add to the table
    CustomerEntity customer3 = new CustomerEntity("Smith", "Denise");
    customer3.Email = "Denise@contoso.com";
    customer3.PhoneNumber = "425-555-0103";
    serviceContext.AddObject(tableName, customer3);

    // Submit the operation to the table service
    serviceContext.SaveChangesWithRetries(SaveChangesOptions.Batch);

<h2><a name="retrieve-all-entities"></a><span class="short-header">Recuperar todas as entidades</span>Como recuperar todas as entidades em uma partição</h2>

Para consultar em uma tabela as entidades de uma partição, você pode usar uma consulta LINQ.
Chame **serviceContext.CreateQuery** para criar uma consulta da sua fonte de dados. O código a seguir especifica um filtro para entidades onde 'Smith' é a chave da partição. Chame **AsTableServiceQuery&lt;CustomerEntity&gt;** no resultado da consulta LINQ para concluir a criação do objeto **CloudTableQuery**. Você pode usar o objeto **partitionQuery** que você criou em um
loop **foreach** para consumir os resultados. Esse código imprime os campos de
cada entidade nos resultados da consulta no console.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Specify a partition query, using "Smith" as the partition key
    CloudTableQuery<CustomerEntity> partitionQuery =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith"
         select e).AsTableServiceQuery<CustomerEntity>();

    // Loop through the results, displaying information about the entity
    foreach (CustomerEntity entity in partitionQuery)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

<h2><a name="retrieve-range-entities"></a><span class="short-header">Recuperar um intervalo de entidades</span>Como recuperar um intervalo de entidades em uma partição</h2>

Se não quiser consultar todas as entidades de uma partição, você poderá especificar um intervalo usando o método **CompareTo** em vez de usar os operadores normais maior que (&gt;) e menor que (&lt;). Isso é necessário, pois o uso desses operadores
resultará na construção de uma consulta incorreta. O código a seguir usa dois filtros para obter todas as entidades na partição 'Smith' onde
a chave de linha (nome) começa com uma letra até a letra 'E' do alfabeto. Em seguida, ele
imprime os resultados da consulta. Se você usar as entidades adicionadas à tabela
na seção de inserção do lote deste guia, apenas duas entidades serão retornadas
desta vez (Ben e Denise Smith); Jeff Smith não será incluído.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Specify a partition query, using "Smith" as the partition key,
    // with the row key being up to the letter "E"
    CloudTableQuery<CustomerEntity> entityRangeQuery =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith" && e.RowKey.CompareTo("E") < 0
         select e).AsTableServiceQuery<CustomerEntity>();

    // Loop through the results, displaying information about the entity
    foreach (CustomerEntity entity in entityRangeQuery)
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

<h2><a name="retrieve-single-entity"></a><span class="short-header">Recuperar uma única entidade</span>Como recuperar uma única entidade</h2>

Você pode escrever uma consulta para recuperar uma entidade única e específica. O código a seguir usa dois filtros para especificar o cliente 'Jeff Smith'.
Em vez de chamar **AsTableServiceQuery**, esse código chama
**FirstOrDefault**. Esse método retorna apenas uma entidade, em vez de uma coleção, portanto, o código atribui o valor de retorno diretamente a um
objeto **CustomerEntity**. Um valor nulo será retornado se nenhuma entidade tiver uma
correspondência exata de chave de partição e chave de linha. A especificação de chaves de partição e de linha
em uma consulta é a maneira mais rápida de recuperar uma única entidade do
serviço Tabela.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Return the entity with partition key of "Smith" and row key of "Jeff"
    CustomerEntity specificEntity =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith" && e.RowKey == "Jeff"
         select e).FirstOrDefault();

<h2><a name="update-entity"></a><span class="short-header">Atualizar uma entidade</span>Como: atualizar uma entidade</h2>

Para atualizar uma entidade, recupere-a do serviço Tabela, modifique o
objeto de entidade e salve as alterações novamente no serviço Tabela. O código a seguir altera o número de telefone de um cliente existente. Em vez de chamar **AddObject**, como fizemos para inserir, esse código chama
**UpdateObject**. O método **SaveChangesWithRetries** chama o serviço Tabela, e a entidade é atualizada, a menos que um outro aplicativo a tenha alterado desde que ela foi recuperada por esse aplicativo. Quando isso acontece,
uma exceção é gerada, e a entidade deve ser recuperada, modificada e
salva novamente. Esse padrão de repetição é comum em um sistema de
armazenamento distribuído.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Return the entity with partition key of "Smith" and row key of "Jeff"
    CustomerEntity specificEntity =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith" && e.RowKey == "Jeff"
         select e).FirstOrDefault();

    // Specify a new phone number
    specificEntity.PhoneNumber = "425-555-0105";

    // Update the entity
    serviceContext.UpdateObject(specificEntity);

    // Submit the operation to the table service
    serviceContext.SaveChangesWithRetries();

<h2><a name="query-entity-properties"></a><span class="short-header">Consultar um subconjunto das propriedades</span>Como consultar um subconjunto das propriedades da entidade</h2>

Uma consulta a uma tabela pode recuperar apenas algumas propriedades de uma entidade.
Essa técnica, denominada projeção, reduz a largura de banda e pode melhorar
o desempenho da consulta, principalmente para grandes entidades. A consulta no código a seguir retorna apenas os endereços de e-mail das entidades na tabela. Você pode aprender mais sobre projeção nesta [postagem de blog][]. Observe
que essa projeção não tem suporte no emulador de armazenamento local, portanto, esse
código somente será executado quando uma conta for usada no serviço Tabela.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Define a projection query that retrieves only the Email property
    var projectionQuery = 
        from e in serviceContext.CreateQuery<CustomerEntity>("people")
        select new
        {
            Email = e.Email
            // You can specify additional fields here
        };

    // Loop through the results, displaying the Email value
    foreach (var person in projectionQuery)
    {
        Console.WriteLine(person.Email);
    }

<h2><a name="insert-entity"></a><span class="short-header">Inserir ou substituir uma entidade</span>Como: inserir ou substituir uma entidade</h2>

Em geral, você deseja adicionar uma entidade a uma tabela sem saber se ela já
existe na tabela. Uma operação de inserção ou substituição permite que você faça uma
única solicitação que inserirá a entidade, se ela não existir, ou
substituirá a existente, se ela existir. Com base nos exemplos anteriores, o
código a seguir insere ou substitui a entidade para 'Walter Harp'. Depois de criar uma nova entidade, esse código chama o método **serviceContext.AttachTo**. Em seguida, esse código chama **UpdateObject** e, finalmente, chama
**SaveChangesWithRetries** com o
parâmetro **SaveChangesOptions.ReplaceOnUpdate**. A omissão do
parâmetro **SaveChangesOptions.ReplaceOnUpdate** provoca uma operação de inserção ou mesclagem. Observe que a operação de inserção ou substituição não tem suporte
no emulador de armazenamento local, portanto, esse código somente será executado quando uma
conta no serviço Tabela for usada. Você poderá saber mais sobre as operações de inserção ou substituição e de inserção ou mesclagem nesta [postagem de blog][].

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    // Create a new customer entity
    CustomerEntity customer5 = new CustomerEntity("Harp", "Walter");
    customer5.Email = "Walter@contoso.com";
    customer5.PhoneNumber = "425-555-0106";

    // Attach this customer to the people table
    serviceContext.AttachTo("people", customer5);

    // Insert this customer if new, or replace if exists
    serviceContext.UpdateObject(customer5);

    // Submit the operation the table service, using the ReplaceOnUpdate option
    serviceContext.SaveChangesWithRetries(SaveChangesOptions.ReplaceOnUpdate);

<h2><a name="delete-entity"></a><span class="short-header">Excluir uma entidade</span>Como excluir uma entidade</h2>

Você poderá excluir uma entidade facilmente depois que recuperá-la. Você também pode usar o método **AttachTo** para iniciar seu acompanhamento sem recuperá-lo do servidor (consulte a operação de inserção ou substituição acima). Depois que a entidade for acompanhada com **serviceContext**, chame **DeleteObject** com a entidade a ser excluída. Em seguida, chame **SaveChangesWithRetries**. O código a seguir recupera e exclui uma entidade customer.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Get the data service context
    TableServiceContext serviceContext = tableClient.GetDataServiceContext();

    CustomerEntity specificEntity =
        (from e in serviceContext.CreateQuery<CustomerEntity>("people")
         where e.PartitionKey == "Smith" && e.RowKey == "Jeff"
         select e).FirstOrDefault();

    // Delete the entity
    serviceContext.DeleteObject(specificEntity);

    // Submit the operation to the table service
    serviceContext.SaveChangesWithRetries();

<h2><a name="delete-table"></a><span class="short-header">Excluir uma tabela</span>Como excluir uma tabela</h2>

Finalmente, o código a seguir excluirá uma tabela de uma conta de armazenamento. Uma tabela que foi excluída não estará disponível para ser recriada por um período de tempo após a exclusão.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Delete the table it if exists
    tableClient.DeleteTableIfExist("people");

<h2><a name="next-steps"></a><span class="short-header">Próximas etapas</span>Próximas etapas</h2>

Agora que você aprendeu os conceitos básicos do armazenamento de tabela, siga estes links para saber como executar tarefas de armazenamento mais complexas.

<ul>
<li>Consulte a documentação de referência do serviço Blob para obter detalhes completos sobre as APIs disponíveis:
  <ul>
    <li><a href="http://msdn.microsoft.com/pt-br/library/windowsazure/wl_svchosting_mref_reference_home">Referência à biblioteca cliente do .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/pt-br/library/windowsazure/dd179355">Referência da API REST</a></li>
  </ul>
</li>
<li>Conheça tarefas mais avançadas que você pode executar com o Armazenamento do Azure em <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx">Armazenando e acessando dados no Azure</a> (a página pode estar em inglês).</li>
<li>Exibir mais guias de recursos para obter informações sobre opções adicionais para armazenar dados no Azure.
  <ul>
    <li>Usar o <a href="/pt-br/develop/net/how-to-guides/blob-storage/">Armazenamento de Blob</a> para armazenar dados não estruturados.</li>
    <li>Usar o <a href="/pt-br/develop/net/how-to-guides/sql-database/">Banco de Dados SQL</a> para armazenar dados relacionais.</li>
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
  [Como acessar programaticamente o armazenamento de tabela]: #configure-access
  [Como: criar uma tabela]: #create-table
  [Como: adicionar uma entidade a uma tabela]: #add-entity
  [Como: inserir um lote de entidades]: #insert-batch
  [Como: recuperar todas as entidades em uma partição]: #retrieve-all-entities
  [Como: recuperar um intervalo de entidades em uma partição]: #retrieve-range-entities
  [Como: recuperar uma única entidade]: #retrieve-single-entity
  [Como: atualizar uma entidade]: #update-entity
  [Como: consultar um subconjunto de propriedades de entidade]: #query-entity-properties
  [Como: inserir ou substituir uma entidade]: #insert-entity
  [Como excluir uma entidade]: #delete-entity
  [Como excluir uma tabela]: #delete-table
  [Baixar e instalar o SDK do Azure para .NET]: /pt-br/develop/net/
  [Criando um projeto do Azure no Visual Studio]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee405487.aspx
  
  [Blob5]: ./media/storage-dotnet-how-to-use-table-storage-17/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-table-storage-17/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-table-storage-17/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-table-storage-17/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-table-storage-17/blob9.png
  
  [postagem de blog]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx
  [Blog da equipe do Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configurando cadeias de conexão]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee758697.aspx

