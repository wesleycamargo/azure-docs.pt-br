<properties linkid="dev-net-2-how-to-table-services" urlDisplayName="Serviço Tabela (2.0)" pageTitle="Como usar o armazenamento de tabela - Guia de recursos do Windows Azure" metaKeywords="Introdução à Tabela no Azure   nosql no Azure   Repositório de dados estruturados grandes do Azure   Tabela do Azure   Armazenamento de tabela do Azure   Tabela .Net no Azure   Armazenamento de tabela .NET no Azure   Tabela C# no Azure   Armazenamento de tabela C# no Azure" description="Saiba como usar o armazenamento de tabela para criar e excluir tabelas e inserir e consultar entidades em uma tabela." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Como usar o Serviço de Armazenamento de Tabela"/>



# Como usar o Serviço de Armazenamento de Tabela

<div class="dev-center-tutorial-selector">
<a href="/pt-br/develop/net/how-to-guides/table-services-v17/" title="versão 1.7">versão 1.7</a>
<a href="/pt-br/develop/net/how-to-guides/table-services/" title="versão 2.0" class="current">versão 2.0</a></div>


Este guia mostra como executar cenários comuns usando o Serviço de Armazenamento de Tabela no Windows Azure. As amostras são escritas em código C\# e usam a Biblioteca de Cliente de Armazenamento Windows Azure para .NET (versão 2.0). Os cenários abrangidos incluem a **criação e a exclusão de uma tabela** e o **trabalho com entidades de tabela**. Para obter mais informações sobre tabelas, consulte a seção [Próximas etapas][].

## Sumário

-   [O que é o Serviço Tabela?][]
-   [Conceitos][]
-   [Criar uma conta de Armazenamento do Windows Azure][]
-   [Configurar uma cadeia de conexão de armazenamento][]
-   [Como acessar programaticamente o armazenamento de tabela][]
-   [Como criar uma tabela][]
-   [Como adicionar uma entidade a uma tabela][]
-   [Como inserir um lote de entidades][]
-   [Como recuperar todas as entidades em uma partição][]
-   [Como recuperar um intervalo de entidades em uma partição][]
-   [Como recuperar uma única entidade][]
-   [Como substituir uma entidade][]
-   [Como inserir ou substituir uma entidade][]
-   [Como consultar um subconjunto de propriedades de entidade][]
-   [Como excluir uma entidade][]
-   [Como excluir uma tabela][]
-   [Próximas etapas][]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

<h2><a name="create-account"></a><span class="short-header">Criar uma conta</span>Criar uma conta de Armazenamento do Windows Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a name="setup-connection-string"></a><span class="short-header">Configurar uma cadeia de conexão</span>Configurar uma cadeia de conexão de armazenamento</h2>

A Biblioteca de Cliente de Armazenamento Windows Azure para .NET oferece suporte ao uso de uma cadeia de conexão de armazenamento para configurar pontos de extremidade e credenciais para acesso a serviços de armazenamento. Você pode colocar a cadeia de conexão de armazenamento em um arquivo de configuração, em vez de embuti-la no código:

- Ao usar os Serviços de Nuvem do Windows Azure, é recomendável armazenar a cadeia de conexão usando o sistema de configuração de serviço do Windows Azure (arquivos '*.csdef' e '*.cscfg').
- Ao usar Sites e Máquinas Virtuais do Windows Azure ou ao criar aplicativos .NET destinados para serem executados fora do Windows Azure, é recomendável armazenar sua cadeia de conexão usando o sistema de configuração do .NET (por exemplo, arquivo 'web. config' ou 'app. config').

Nos dois casos, você pode recuperar sua cadeia de conexão usando o método 'CloudConfigurationManager.GetSetting', conforme mostrado neste manual.

### Configurando a cadeia de conexão ao usar os Serviços de Nuvem

O mecanismo de configuração de serviço é exclusivo para projetos de Serviços de Nuvem do Windows Azure e permite que você altere dinamicamente os parâmetros de configuração no Portal de Gerenciamento do Windows Azure sem reimplantar
o aplicativo.

Para configurar a cadeia de conexão na configuração de serviço do Windows
Azure:

1.  No Gerenciador de Soluções do Visual Studio, na pasta **Funções** do Projeto de Implantação do Windows Azure, clique com o botão direito do mouse na sua função web ou função de trabalho e clique em **Propriedades**  
    ![Blob5][Blob5]

2.  Clique na guia **Configurações** e pressione o botão **Adicionar Configuração**.  
    ![Blob6][Blob6]

    Uma nova entrada **Setting1** será mostrada na grade de configurações.

3.  No menu suspenso **Tipo** da nova entrada **Setting1**, escolha **Connection String**  
    ![Blob7][Blob7]

4.  Clique no botão **...** na extremidade direita da entrada **Setting1**.
    A caixa de diálogo **Cadeia de Conexão da Conta de Armazenamento** será aberta.

5.  Escolha se deseja direcionar o emulador de armazenamento (o armazenamento do Windows Azure simulado em sua máquina local) ou uma conta de armazenamento real na nuvem. O código deste guia funciona com qualquer uma dessas opções. Digite o valor da **Chave de Acesso Primária** copiado na etapa anterior deste tutorial, se desejar armazenar dados blob na conta de armazenamento que criamos anteriormente no Windows Azure.   
    ![Blob8][Blob8]

6.  Altere a entrada **Nome** em **Setting1** para um nome mais amigável, como **StorageConnectionString**. Você fará referência a essa cadeia de conexão mais tarde no código deste guia.  
    ![Blob9][Blob9]
	
### Configurando sua cadeia de conexão usando a configuração do .NET

Se você estiver escrevendo um aplicativo que não seja um serviço de nuvem do Windows Azure (consulte a seção anterior), é recomendável usar o sistema de configuração do .NET (por exemplo, 'Web. config' ou 'App. config').  Isso inclui os Sites ou as Máquinas Virtuais do Windows Azure, bem como aplicativos desenvolvidos para serem executados fora do Windows Azure.  Você armazena a cadeia de conexão usando o elemento '<appSettings>' da seguinte maneira:

	<configuration>
  		<appSettings>
    		<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
  		</appSettings>
	</configuration>

Leia [Configurando cadeias de conexão][] para obter mais informações sobre cadeias de conexão de armazenamento.
	
Agora você está pronto para executar as tarefas das instruções deste guia.

<h2> <a name="configure-access"> </a><span  class="short-header">Acessar programaticamente</span>Como acessar o armazenamento de tabela programaticamente</h2>

<h3>Obtendo o assembly</h3>
Você pode usar o NuGet para obter o assembly 'Microsoft.WindowsAzure.Storage.dll'. Clique com o botão direito do mouse no **Gerenciador de Soluções** e escolha **Gerenciar Pacotes NuGet**  Pesquise online por "WindowsAzure.Storage" e clique em **Instalar** para instalar o pacote Armazenamento do Windows Azure e as dependências.

O `Microsoft.WindowsAzure.Storage.dll` também é incluído no SDK do Windows Azure para .NET 2.0, que pode ser baixado na <a href="http://www.windowsazure.com/pt-br/develop/net/#">Central de desenvolvedores do .NET</a> (a página pode estar em inglês). O assembly é instalado no diretório '%Arquivos de Programas%\Microsoft SDKs\Windows Azure\ .NET SDK\v2.0\ref\'.

<h3>Declarações de namespace</h3>
Adicione as seguintes declarações de namespace à parte superior de qualquer arquivo C\# no qual você deseja acessar o Armazenamento do Windows Azure programaticamente:

    using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;

Faça referência ao assembly 'Microsoft.WindowsAzure.Storage.dll'.

<h3>Recuperando sua cadeia de conexão</h3>
Você pode usar o tipo **CloudStorageAccount** para representar as informações de sua Conta de Armazenamento. Se estiver usando um modelo de projeto do Windows Azure e/ou tiver uma referência ao namespace Microsoft.WindowsAzure.CloudConfigurationManager, você poderá usar o tipo **CloudConfigurationManager** para recuperar a cadeia de conexão do armazenamento e as informações da conta de armazenamento na configuração de serviço do Windows Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Se estiver criando um aplicativo sem nenhuma referência ao Microsoft.WindowsAzure.CloudConfigurationManager, e sua cadeia de conexão estiver localizada no 'web.config' ou no 'app.config' como mostrado acima, você poderá usar o **ConfigurationManager** para recuperar a cadeia de conexão.  Você precisará adicionar uma referência ao System.Configuration.dll a seu projeto e adicionar outra declaração de namespace para ele:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

<h3>Dependências do ODataLib</h3>
As dependências do ODataLib na Biblioteca de Cliente de Armazenamento para .NET são resolvidas por meio de pacotes do ODataLib (versão 5.0.2) disponíveis por meio do NuGet e não do WCF Data Services.  As bibliotecas do ODataLib podem ser baixadas diretamente ou referenciadas por seu projeto de código por meio do NuGet.  Os pacotes ODataLib específicos são [OData], [Edm] e [Spatial].

<h2><a name="create-table"></a><span class="short-header">Criar uma tabela</span>Como criar uma tabela</h2>

Um objeto **CloudTableClient** permite que você obtenha os objetos de referência para tabelas e entidades. O código a seguir cria um objeto **CloudTableClient** e usa-o para criar uma nova tabela. Todos os códigos deste guia pressupõe que o aplicativo que está sendo criado é um projeto do Serviço de Nuvem do Windows Azure e usa uma cadeia de conexão de armazenamento armazenada na configuração de serviço do aplicativo do Windows Azure.

    // Recuperar a conta de armazenamento da cadeia de conexão.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de tabela.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Criar a tabela se ela não existir.
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();

<h2><a name="add-entity"></a><span class="short-header">Adicionar uma entidade a uma tabela</span>Como adicionar uma entidade a uma tabela</h2>

As entidades mapeiam para objetos C\# usando uma classe personalizada derivada de
**TableEntity**. Para adicionar uma entidade a uma tabela, crie uma classe que defina as propriedades da entidade. O código a seguir define uma classe de entidade que usa o nome do cliente como a chave de linha e o sobrenome como a chave de partição. Juntas, uma chave de partição e uma chave de linha identificam exclusivamente a entidade na tabela. Entidades com a mesma chave de partição podem ser consultadas mais rapidamente do que as que têm chaves de partição diferentes, mas usar chaves de partição diferentes permite maior escalabilidade de operação em paralelo.  Em qualquer propriedade que deva ser armazenada no serviço tabela, a propriedade deve ser uma propriedade pública de um tipo com suporte que exponha 'get' e 'set'.
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

Operações de tabela que envolvem entidades são executadas usando o objeto **CloudTable** que você criou em "Como criar uma tabela".  A operação a ser executada é representada por um objeto **TableOperation**.  O exemplo de código a seguir mostra a criação do objeto **CloudTable** e, em seguida, de um objeto **CustomerEntity**.  Para preparar a operação, um **TableOperation** é criado para inserir a entidade customer na tabela.  Finalmente, a operação é executada chamando **CloudTable.Execute**.

    // Recuperar a conta de armazenamento da cadeia de conexão.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de tabela.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	/ / Criar o objeto CloudTable que representa a tabela "people".
	CloudTable table = tableClient.GetTableReference("people");

    // Criar uma nova entidade de cliente.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Criar o TableOperation que insere a entidade customer.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Executar a operação de inserção.
    table.Execute(insertOperation);

<h2><a name="insert-batch"></a><span class="short-header">Inserir um lote de entidades</span>Como inserir um lote de entidades</h2>

Você pode inserir um lote de entidades em uma tabela em uma única operação de gravação. Algumas outras observações sobre operações em lote:

1.  Você pode executar atualizações, exclusões e inserções em uma única operação em lote.
2.  Uma única operação em lote pode incluir até 100 entidades.
3.  Todas as entidades em uma única operação em lote devem ter a mesma chave de partição.
4.  Embora seja possível executar uma consulta como uma operação em lotes, ele deve ser a única operação em lote.

<!-- -->
O exemplo de código a seguir cria dois objetos de entidade e adiciona cada um a um **TableBatchOperation** usando o método **insert**. Em seguida, **CloudTable.Execute** é chamado para executar a operação.

    // Recuperar a conta de armazenamento da cadeia de conexão.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de tabela.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
	/ / Criar o objeto CloudTable que representa a tabela "people".
    CloudTable table = tableClient.GetTableReference("people");

    // Criar a operação em lote.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Criar uma entidade de cliente e adicioná-la à tabela.
	CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
	customer1.Email = "Jeff@contoso.com";
	customer1.PhoneNumber = "425-555-0104";
            
	// Criar outra entidade de cliente e adicioná-la à tabela.
	CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
	customer2.Email = "Ben@contoso.com";
	customer2.PhoneNumber = "425-555-0102";
            
	// Adiciona duas entidades de cliente à operação de inserção em lote.
	batchOperation.Insert(customer1);
	batchOperation.Insert(customer2);

	// Executar a operação em lote.
	table.ExecuteBatch(batchOperation);

<h2><a name="retrieve-all-entities"></a><span class="short-header">Recuperar todas as entidades</span>Como recuperar todas as entidades em uma partição</h2>

Para consultar uma tabela de todas as entidades em uma partição, use um objeto **TableQuery**.
O exemplo de código a seguir especifica um filtro para entidades onde 'Smith' é a chave da partição. Este exemplo imprime os campos de cada entidade dos resultados da consulta no console.

    // Recuperar a conta de armazenamento da cadeia de conexão.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de tabela.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    / / Criar o objeto CloudTable que representa a tabela "people".
    CloudTable table = tableClient.GetTableReference("people");

    // Construir a operação de consulta para todas as entidades de cliente onde PartitionKey = "Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Imprimir os campos de cada cliente.
    foreach (CustomerEntity entity in table.ExecuteQuery(query))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

<h2><a name="retrieve-range-entities"></a><span class="short-header">Recuperar um intervalo de entidades</span>Como recuperar um intervalo de entidades em uma partição</h2>

Se não desejar consultar todas as entidades em uma partição, você poderá especificar um intervalo combinando o filtro de chave de partição com um filtro de chave de linha. O exemplo de código a seguir usa dois filtros para obter todas as entidades na partição 'Smith' onde a chave de linha (nome) começa com uma letra anterior a 'E' do alfabeto e, em seguida, imprime os resultados da consulta.

    // Recuperar a conta de armazenamento da cadeia de conexão.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de tabela.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Criar o objeto CloudTable que representa a tabela "people".
    CloudTable table = tableClient.GetTableReference("people");

	// Criar a consulta de tabela.
    TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

    // Executar um loop pelos resultados exibindo informações sobre a entidade.
    foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

<h2><a name="retrieve-single-entity"></a><span class="short-header">Recuperar uma única entidade</span>Como recuperar uma única entidade</h2>

Você pode escrever uma consulta para recuperar uma entidade única e específica. O código a seguir usa um **TableOperation** para especificar o cliente 'Ben Smith'.
Esse método retorna uma única entidade, em vez de uma coleção, e o valor retornado no **TableResult.Result** é um **CustomerEntity**
Especificar chaves de partição e de linha em uma consulta é a maneira mais rápida de recuperar uma única entidade do serviço Tabela.

    // Recuperar a conta de armazenamento da cadeia de conexão.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de tabela.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    / / Criar o objeto CloudTable que representa a tabela "people".
    CloudTable table = tableClient.GetTableReference("people");

    // Criar uma operação de recuperação que utiliza uma entidade customer.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Executar a operação de recuperação.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Imprimir o número do telefone do resultado.
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("The phone number could not be retrieved.");

<h2><a name="replace-entity"></a><span class="short-header">Substituir uma entidade</span>Como substituir uma entidade</h2>

Para atualizar uma entidade, recupere-a do serviço Tabela, modifique o objeto da entidade e, em seguida, salve as alterações novamente no serviço tabela. O código a seguir altera o número de telefone de um cliente existente. Em vez de chamar **Insert**, esse código usa 
**Replace**. Isso faz com que a entidade seja totalmente substituída no servidor, a menos que a entidade no servidor tenha sido alterada desde que foi recuperada, caso em que haverá falha na operação.  Essa falha é para impedir que seu aplicativo substitua inadvertidamente uma alteração feita entre a recuperação e a atualização por outro componente de seu aplicativo.  A manipulação correta dessa falha é recuperar a entidade novamente, fazer as alterações (se ainda válidas) e executar outra operação **Replace**.  A próxima seção mostrará como substituir esse comportamento.

    // Recuperar a conta de armazenamento da cadeia de conexão.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de tabela    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Criar o objeto CloudTable que representa a tabela "people".
    CloudTable table = tableClient.GetTableReference("people");

    // Criar uma operação de recuperação que utiliza uma entidade customer.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Executar a operação.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Atribuir o resultado a um objeto CustomerEntity.
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

<h2><a name="insert-or-replace-entity"></a><span class="short-header">Inserir ou substituir uma entidade</span>Como inserir ou substituir uma entidade</h2>

Haverá falha nas operações **Replace** se a entidade tiver sido alterada desde que foi recuperada do servidor.  Além disso, você deve primeiro recuperar a entidade do servidor para que o **Replace** seja bem-sucedido.
No entanto, às vezes você não sabe se a entidade existe no servidor e se os valores atuais armazenados nela são irrelevantes - sua atualização deve substituir todos eles.  Para fazer isso, você usa uma operação **InsertOrReplace**.  Essa operação insere a entidade, se ela não existir, ou a substitui, se ela existir, independentemente de quando foi feita a última atualização.  No exemplo de código a seguir, a entidade customer de Ben Smith ainda é recuperada, mas ela é salva novamente no servidor usando **InsertOrReplace**  Todas as atualizações feitas na entidade entre a operação de recuperação e de atualização serão substituídas.

    // Recuperar a conta de armazenamento da cadeia de conexão.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de tabela.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Criar o objeto CloudTable que representa a tabela "people".
    CloudTable table = tableClient.GetTableReference("people");

    // Criar uma operação de recuperação que utiliza uma entidade customer.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Executar a operação.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Atribuir o resultado a um objeto CustomerEntity.
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

<h2><a name="query-entity-properties"></a><span class="short-header">Consultar um subconjunto das propriedades</span>Como consultar um subconjunto das propriedades da entidade</h2>

Uma consulta de tabela pode recuperar apenas algumas propriedades de uma entidade em vez de todas as propriedades da entidade. Essa técnica, chamada projeção, reduz a largura de banda e pode melhorar o desempenho da consulta, principalmente para grandes entidades. A consulta no código a seguir retorna apenas os endereços de e-mail das entidades na tabela. Isso é feito por meio de uma consulta de **DynamicTableEntity** e também de **EntityResolver**. Você pode aprender mais sobre projeção nesta [postagem de blog][]. Observe que a projeção não é compatível com o emulador de armazenamento local, portanto, esse código é executado somente ao usar uma conta no serviço tabela.

    // Recuperar a conta de armazenamento da cadeia de conexão
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de tabela
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Criar o objeto CloudTable que representa a tabela "people".
    CloudTable table = tableClient.GetTableReference("people");

    // Definir a consulta e selecionar apenas a propriedade Email
    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

    // Definir um resolvedor de entidade para trabalhar com a entidade depois da recuperação.
    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
    {
        Console.WriteLine(projectedEmail);
    }

<h2><a name="delete-entity"></a><span class="short-header">Excluir uma entidade</span>Como excluir uma entidade</h2>

Você pode excluir facilmente uma entidade após a recuperação usando o mesmo padrão mostrado para a atualização de uma entidade.  O código a seguir recupera e exclui uma entidade customer.

    // Recuperar a conta de armazenamento da cadeia de conexão
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de tabela
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Criar o objeto CloudTable que representa a tabela "people".
    CloudTable table = tableClient.GetTableReference("people");

    // Criar uma operação de recuperação que espera uma entidade customer.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Executar a operação.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Atribuir o resultado a um CustomerEntity.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    / / Criar o TableOperation de exclusão.
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

	   // Executar a operação.
	   table.Execute(deleteOperation);

	   Console.WriteLine("Entity deleted.");
	}

	else
	   Console.WriteLine("Could not retrieve the entity.");

<h2><a name="delete-table"></a><span class="short-header">Excluir uma tabela</span>Como excluir uma tabela</h2>

Finalmente, o exemplo de código a seguir exclui uma tabela de uma conta de armazenamento. Uma tabela que foi excluída não estará disponível para ser recriada por um período de tempo após a exclusão.

    // Recuperar a conta de armazenamento da cadeia de conexão.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de tabela.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Criar o objeto CloudTable que representa a tabela "people".
    CloudTable table = tableClient.GetTableReference("people");

    // Excluir a tabela, se ela existir.
    table.DeleteIfExists();

<h2><a name="next-steps"></a><span class="short-header">Próximas etapas</span>Próximas etapas</h2>

Agora que você aprendeu os conceitos básicos do armazenamento de tabela, siga estes links para saber como executar tarefas de armazenamento mais complexas.

<ul>
<li>Consulte a documentação de referência do serviço blob para obter detalhes completos sobre as APIs disponíveis:
  <ul>
    <li><a href="http://msdn.microsoft.com/pt-br/library/windowsazure/wa_storage_api_ref_reference_home.aspx">Referência à Biblioteca de Cliente de Armazenamento para .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/pt-br/library/windowsazure/dd179355">Referência da API REST</a></li>
  </ul>
</li>
<li>Conheça tarefas mais avançadas que você pode executar com o Armazenamento do Windows Azure em <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx">Armazenando e acessando dados no Windows Azure</a> (a página pode estar em inglês).</li>
<li>Exibir mais guias de recursos para obter informações sobre opções adicionais para armazenar dados no Windows Azure.
  <ul>
    <li>Usar o <a href="/pt-br/develop/net/how-to-guides/blob-storage/">Blob Storage</a> para armazenar dados não estruturados.</li>
    <li>Usar o <a href="/pt-br/develop/net/how-to-guides/sql-database/">SQL Database</a> para armazenar dados relacionais.</li>
  </ul>
</li>
</ul>

  [Próximas etapas]: #next-steps
  [O que é o serviço Tabela]: #what-is
  [Conceitos]: #concepts
  [Criar uma conta de armazenamento do Windows Azure]: #create-account
  [Criar um projeto do Windows Azure no Visual Studio]: #create-project
  [Configurar seu aplicativo para acessar o armazenamento]: #configure-access
  [Configurar uma cadeia de conexão de armazenamento]: #setup-connection-string
  [Como acessar programaticamente o armazenamento de tabela]: #configure-access
  [Como criar uma tabela]: #create-table
  [Como adicionar uma entidade a uma tabela]: #add-entity
  [Como inserir um lote de entidades]: #insert-batch
  [Como recuperar todas as entidades em uma partição]: #retrieve-all-entities
  [Como recuperar um intervalo de entidades em uma partição]: #retrieve-range-entities
  [Como recuperar uma única entidade]: #retrieve-single-entity
  [Como substituir uma entidade]: #replace-entity
  [Como inserir ou substituir uma entidade]: #insert-or-replace-entity
  [Como consultar um subconjunto de propriedades de entidade]: #query-entity-properties
  [Como excluir uma entidade]: #delete-entity
  [Como excluir uma tabela]: #delete-table
  [Baixar e instalar o SDK do Windows Azure para .NET]: / en-us/desenvolver/net /
  [Criando um projeto do Windows Azure no Visual Studio]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee405487.aspx
  
  [Blob5]: ./media/storage-dotnet-how-to-use-table-storage-20/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-table-storage-20/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-table-storage-20/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-table-storage-20/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-table-storage-20/blob9.png
  
  [postagem no blog]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [Acessar e armazenar dados no Windows Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx
  [Blog da equipe do Armazenamento do Windows Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configurando cadeias de conexão]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2

