<properties 
    pageTitle="Como usar o armazenamento de tabelas (C++) | Microsoft Azure" 
    description="Saiba como usar o serviço de armazenamento de tabela no Azure. As amostras são escritas em C++." 
    services="storage" 
    documentationCenter=".net" 
    authors="tamram" 
    manager="adinah" 
    editor=""/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
	ms.date="07/19/2015" 
    ms.author="tamram"/>

# Como usar o armazenamento de tabelas em C++

[AZURE.INCLUDE [armazenamento-seletor-tabela-include](../../includes/storage-selector-table-include.md)]

## Visão geral  
Este guia mostra como executar cenários comuns usando o serviço de Armazenamento de tabelas no Azure. Os exemplos são escritos em C++ e usam a [Biblioteca do Cliente de Armazenamento do Azure para C++](https://github.com/Azure/azure-storage-cpp/blob/v1.0.0/README.md). Os cenários cobertos incluem a **criação e a exclusão de uma tabela**, e o **trabalho com entidades de tabela**.

>[AZURE.NOTE]Este guia tem como alvo a Biblioteca do Cliente de Armazenamento do Azure para C++, versão 1.0.0 e superior. A versão recomendada é a Biblioteca do Cliente de Armazenamento 1.0.0, que está disponível via [NuGet](http://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/).

[AZURE.INCLUDE [armazenamento-tabela-conceitos-include](../../includes/storage-table-concepts-include.md)]
[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


## Criar um aplicativo em C++  
Neste guia, você usará os recursos de armazenamento que podem ser executados em um aplicativo C++. Para isso, você precisará instalar a Biblioteca do Cliente de Armazenamento do Azure para C++ e criar uma conta de armazenamento do Azure em sua assinatura do Azure.

Para instalar a Biblioteca do Cliente de Armazenamento do Azure para C++, você pode usar os seguintes métodos:

-	**Linux:** siga as instruções dadas na página README da [Biblioteca do Cliente de Armazenamento do Azure para C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).  
-	**Windows:** no Visual Studio, clique em **Ferramentas > Gerenciador de Pacotes do NuGet > Console do Gerenciador de Pacotes**. Digite o seguinte comando no console do [Gerenciador de Pacotes do NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) e pressione **ENTER**.  

		Install-Package wastorage

## Configurar seu aplicativo para acessar o Armazenamento de tabelas  
Adicione as seguintes instruções include à parte superior do arquivo C++ no qual deseja usar as APIs de armazenamento do Azure para acessar as tabelas:

	#include "was/storage_account.h"
	#include "was/table.h"

## Configurar uma cadeia de conexão de armazenamento do Azure  
Um cliente de armazenamento do Azure usa uma cadeia de conexão para armazenar pontos de extremidade e credenciais para acessar serviços de gerenciamento de dados. Ao executar um aplicativo cliente, é necessário fornecer a cadeia de conexão de armazenamento no formato a seguir, usando o nome da conta de armazenamento e a chave de acesso de armazenamento da conta de armazenamento listada no Portal de Gerenciamento para os valores *AccountName* e *AccountKey*. Para obter informações sobre as contas de armazenamento e as chaves de acesso, consulte [Sobre as Contas de Armazenamento do Azure](storage-create-storage-account.md). Este exemplo mostra como você pode declarar um campo estático para armazenar a cadeia de conexão:

	// Define the connection-string with your values.
	const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));

Para testar seu aplicativo no computador local do Windows, você pode usar o emulador de armazenamento do [Microsoft Azure](https://msdn.microsoft.com/library/azure/hh403989.aspx) que é instalado com o [SDK do Azure](http://azure.microsoft.com/downloads/). O emulador de armazenamento é um utilitário que simula os serviços Blob, fila e tabela disponíveis no Azure em sua máquina de desenvolvimento local. Este exemplo mostra como você pode declarar um campo estático para manter a cadeia de conexão em seu emulador de armazenamento local:

	// Define the connection-string with Azure Storage Emulator.
	const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  

Para iniciar o emulador de armazenamento do Azure, selecione o botão **Iniciar** ou pressione a tecla **Windows**. Comece digitando **Emulador de Armazenamento do Azure** e selecione **Emulador de Armazenamento do Microsoft Azure** na lista de aplicativos.

Os exemplos abaixo pressupõem que você usou um desses dois métodos para obter a cadeia de conexão do armazenamento.

## Recuperar sua cadeia de conexão  
Você pode usar a classe **cloud\_storage\_account** para representar as informações da Conta de Armazenamento. Para recuperar as informações da conta de armazenamento na cadeia de conexão de armazenamento, você pode usar o método de analisar.

	// Retrieve the storage account from the connection string. 
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

Em seguida, obtenha uma referência para uma classe **cloud\_table\_client** que permita recuperar os objetos de referência para as tabelas e as entidades armazenadas no Serviço de Armazenamento de Tabelas. O código a seguir cria um objeto **cloud\_table\_client** usando o objeto da conta de armazenamento que recuperamos acima:

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

## Como: criar uma tabela
Um objeto **cloud\_table\_client** permite obter os objetos de referência para tabelas e entidades. O código a seguir cria um objeto **cloud\_table\_client** e utiliza-o para criar uma nova tabela.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Retrieve a reference to a table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create the table if it doesn't exist.
	table.create_if_not_exists();  

## Como: adicionar uma entidade a uma tabela
Para adicionar uma entidade a uma tabela, crie um novo objeto **table\_entity** e passe-o para **table\_operation::insert\_entity**. O código a seguir usa o primeiro nome do cliente como a chave de linha e o último nome como a chave de partição. Juntas, uma chave de partição e uma chave de linha identificam exclusivamente a entidade na tabela. As entidades com a mesma chave de partição podem ser consultadas mais rápido do que aquelas com chaves de partição diferentes, mas usar chaves de partição diferentes possibilita uma maior escalabilidade de operação paralela. Para obter mais informações, consulte [Lista de Verificação de Escalabilidade e Desempenho de Armazenamento do Microsoft Azure](storage-performance-checklist.md/).

O código a seguir cria uma nova instância de **table\_entity** com alguns dados do cliente que serão armazenados. O próximo código chama **table\_operation::insert\_entity** para criar um objeto **table\_operation** para inserir uma entidade em uma tabela e associa a nova entidade da tabela a ele. Por fim, o código chama o método execute no objeto **cloud\_table**. E o novo **table\_operation** envia uma solicitação ao serviço de armazenamento para inserir a nova entidade de cliente na tabela "pessoas".

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Retrieve a reference to a table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create the table if it doesn't exist.
	table.create_if_not_exists();

	// Create a new customer entity.
	azure::storage::table_entity customer1(U("Harp"), U("Walter"));

	azure::storage::table_entity::properties_type& properties = customer1.properties();
	properties.reserve(2);
	properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

	properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

	// Create the table operation that inserts the customer entity.
	azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

	// Execute the insert operation.
	azure::storage::table_result insert_result = table.execute(insert_operation);

## Como: inserir um lote de entidades
Você pode inserir um lote de entidades ao serviço Tabela em uma operação de gravação. O código a seguir cria um objeto **table\_batch\_operation** e adiciona três operações de inserção a ele. Cada operação de inserção é adicionada criando um novo objeto de entidade, definindo seus valores, em seguida, chamando o método insert no objeto **table\_batch\_operation** para associar a entidade a uma nova operação de inserção. Em seguida, **cloud\_table.execute** é chamado para executar a operação.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Define a batch operation.
	azure::storage::table_batch_operation batch_operation;

	// Create a customer entity and add it to the table.
	azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

	azure::storage::table_entity::properties_type& properties1 = customer1.properties();
	properties1.reserve(2);
	properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
	properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

	// Create another customer entity and add it to the table.
	azure::storage::table_entity customer2(U("Smith"), U("Ben"));

	azure::storage::table_entity::properties_type& properties2 = customer2.properties();
	properties2.reserve(2);
	properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
	properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

	// Create a third customer entity to add to the table.
	azure::storage::table_entity customer3(U("Smith"), U("Denise"));

	azure::storage::table_entity::properties_type& properties3 = customer3.properties();
	properties3.reserve(2);
	properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
	properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));
		
	// Add customer entities to the batch insert operation.
	batch_operation.insert_or_replace_entity(customer1);
	batch_operation.insert_or_replace_entity(customer2);
	batch_operation.insert_or_replace_entity(customer3);

	// Execute the batch operation.
	std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);

Algumas outras observações sobre as operações em lote:

-	Você pode executar até 100 operações de inserção, exclusão, mesclagem, substituição, inserção ou mesclagem e inserção ou substituição em qualquer combinação em um único lote.  
-	Uma operação em lote pode ter uma operação de recuperação se ela for a única operação no lote.  
-	Todas as entidades em uma única operação em lote devem ter a mesma chave de partição.  
-	Uma operação em lote está limitada a uma carga de dados de 4 MB.  

## Como recuperar todas as entidades em uma partição
Para consultar uma tabela de todas as entidades em uma partição, use um objeto **table\_query**. O exemplo de código a seguir especifica um filtro para entidades onde 'Smith' é a chave da partição. Esse exemplo imprime os campos de cada entidade nos resultados da consulta no console.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Construct the query operation for all customer entities where PartitionKey="Smith".
	azure::storage::table_query query;

	query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

	// Execute the query.
	azure::storage::table_query_iterator it = table.execute_query(query);

	// Print the fields for each customer.
	azure::storage::table_query_iterator end_of_results;
	for (; it != end_of_results; ++it)
	{
		const azure::storage::table_entity::properties_type& properties = it->properties();

		std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()		
			<< U(", Property1: ") << properties.at(U("Email")).string_value()
			<< U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
	}  

A consulta neste exemplo traz todas as entidades que correspondem aos critérios do filtro. Se você tiver tabelas grandes e precisar baixar entidades de tabela com frequência, é recomendável que armazene seus dados nos Blobs de Armazenamento do Azure.

## Como: recuperar um intervalo de entidades em uma partição
Se não desejar consultar todas as entidades em uma partição, você poderá especificar um intervalo combinando o filtro de chave de partição com um filtro de chave de linha. O exemplo de código a seguir usa dois filtros para obter todas as entidades na partição 'Smith' onde a chave de linha (nome) começa com uma letra anterior a 'E' do alfabeto e, em seguida, imprime os resultados da consulta.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create the table query.
	azure::storage::table_query query;

	query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
		azure::storage::table_query::generate_filter_condition(U("PartitionKey"), 
		azure::storage::query_comparison_operator::equal, U("Smith")),
		azure::storage::query_logical_operator::op_and,
		azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

	// Execute the query.
	azure::storage::table_query_iterator it = table.execute_query(query);

	// Loop through the results, displaying information about the entity.
	azure::storage::table_query_iterator end_of_results;
	for (; it != end_of_results; ++it)
	{
		const azure::storage::table_entity::properties_type& properties = it->properties();

		std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
			<< U(", Property1: ") << properties.at(U("Email")).string_value()
			<< U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
	}  

## Como: recuperar uma única entidade
Você pode escrever uma consulta para recuperar uma entidade única e específica. O código a seguir usa uma **table\_operation::retrive\_entity** para especificar o cliente 'Jeff Smith'. Esse método retorna uma única entidade, em vez de uma coleção, e o valor retornado está em **table\_result**. Especificar chaves de partição e de linha em uma consulta é a maneira mais rápida de recuperar uma única entidade do serviço Tabela.

	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Retrieve the entity with partition key of "Smith" and row key of "Jeff".
	azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
	azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

	// Output the entity.
	azure::storage::table_entity entity = retrieve_result.entity();
	const azure::storage::table_entity::properties_type& properties = entity.properties();

	std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
		<< U(", Property1: ") << properties.at(U("Email")).string_value()
		<< U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;

## Como: substituir uma entidade
Para substituir uma entidade, recupere-a no serviço de tabela, modifique o objeto de entidade, em seguida, salve as alterações novamente no serviço de tabela. O código a seguir altera o número de telefone e o endereço de email de um cliente existente. Em vez de chamar **table\_operation:: insert\_entity**, esse código usa **table\_operation:: replace\_entity**. Isso faz com que a entidade seja totalmente substituída no servidor, a menos que a entidade no servidor tenha sido alterada desde que foi recuperada, caso em que haverá falha na operação. Essa falha é para impedir que seu aplicativo substitua inadvertidamente uma alteração feita entre a recuperação e a atualização por outro componente de seu aplicativo. O tratamento correto dessa falha é recuperar a entidade novamente, fazer as alterações (se ainda válidas) e executar outra operação **table\_operation::replace\_entity**. A próxima seção mostrará como substituir esse comportamento.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Replace an entity.
	azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
	azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
	properties_to_replace.reserve(2);

	// Specify a new phone number.
	properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

	// Specify a new email address.
	properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

	// Create an operation to replace the entity.
	azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

	// Submit the operation to the table service.
	azure::storage::table_result replace_result = table.execute(replace_operation);

## Como: inserir ou substituir uma entidade
As operações **table\_operation:: replace\_entity** falharão se a entidade tiver sido alterada desde que foi recuperada no servidor. Além disso, você deve primeiro recuperar a entidade no servidor para que **table\_operation::replace\_entity** seja bem-sucedido. No entanto, às vezes você não sabe se a entidade existe no servidor e se os valores atuais armazenados nela são irrelevantes sua atualização deve substituir todos eles. Para fazer isso, você usaria uma operação **table\_operation:: insert\_or\_replace\_entity**. Essa operação insere a entidade, se ela não existir, ou a substitui, se ela existir, independentemente de quando foi feita a última atualização. No exemplo de código a seguir, a entidade customer de Jeff Smith ainda é recuperada, mas é salva novamente no servidor usando **table\_operation::insert\_or\_replace\_entity**. Todas as atualizações feitas na entidade entre a operação de recuperação e de atualização serão substituídas.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Insert-or-replace an entity.
	azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
	azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

	properties_to_insert_or_replace.reserve(2);

	// Specify a phone number.
	properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

	// Specify an email address.
	properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

	// Create an operation to insert or replace the entity.
	azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

	// Submit the operation to the table service.
	azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);
 
## Como: consultar um subconjunto de propriedades de entidade  
Uma consulta a uma tabela pode recuperar apenas algumas propriedades de uma entidade. A consulta no código a seguir usa o método **table\_query::set\_select\_columns** para retornar apenas os endereços de email das entidades na tabela.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Define the query, and only select the Email property.
	azure::storage::table_query query;
	std::vector<utility::string_t> columns;

	columns.push_back(U("Email"));
	query.set_select_columns(columns);

	// Execute the query.
	azure::storage::table_query_iterator it = table.execute_query(query);

	// Display the results.
	azure::storage::table_query_iterator end_of_results;
	for (; it != end_of_results; ++it)
	{
		std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

		const azure::storage::table_entity::properties_type& properties = it->properties();
		for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
		{
			std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
		}

		std::wcout << std::endl;
	}

**Observação:** consultar algumas propriedades em uma entidade é uma operação mais eficiente do que recuperar todas as propriedades.

## Como excluir uma entidade
Você poderá excluir uma entidade facilmente depois que recuperá-la. Depois da entidade ser recuperada, chame **table\_operation::delete\_entity** com a entidade a excluir. Então, chame execute no método **cloud\_table.execute**. O código a seguir recupera e exclui uma entidade com chave de partição "Smith" e a chave de linha "Jeff".

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
	azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
	azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

	// Create an operation to delete the entity.
	azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

	// Submit the delete operation to the table service.
	azure::storage::table_result delete_result = table.execute(delete_operation);  
 
## Como excluir uma tabela
Finalmente, o exemplo de código a seguir exclui uma tabela de uma conta de armazenamento. Uma tabela que tenha sido excluída não estará disponível para ser recriada por um período de tempo após a exclusão.

	// Retrieve the storage account from the connection string.
	azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

	// Create the table client.
	azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

	// Create a cloud table object for the table.
	azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

	// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
	azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
	azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

	// Create an operation to delete the entity.
	azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

	// Submit the delete operation to the table service.
	azure::storage::table_result delete_result = table.execute(delete_operation);

## Próximas etapas
Agora que você aprendeu os conceitos básicos do armazenamento de tabelas, siga estes links para saber mais sobre o armazenamento do Azure.

-	[Como usar o Armazenamento de Blobs do C++](storage-c-plus-plus-how-to-use-blobs.md)
-	[Como usar o Armazenamento de Filas do C++](storage-c-plus-plus-how-to-use-queues.md)
-	[Listar recursos de Armazenamento do Azure em C++](storage-c-plus-plus-enumeration.md)
-	[Biblioteca do Cliente de Armazenamento para C++](https://msdn.microsoft.com/library/azure/gg433040.aspx) 
-	[Referência do MSDN de Armazenamento do Azure](https://msdn.microsoft.com/library/azure/gg433040.aspx)
-	[Documentação do Armazenamento do Azure](http://azure.microsoft.com/documentation/services/storage/)
 

<!---HONumber=August15_HO6-->