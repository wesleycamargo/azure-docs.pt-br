<properties 
	pageTitle="Como usar o Armazenamento de Tabela do PHP | Microsoft Azure" 
	description="Saiba como usar o serviço de Tabela do PHP para criar e excluir tabelas, assim como inserir, excluir e consultar a tabela." 
	services="storage" 
	documentationCenter="php" 
	authors="tfitzmac,tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="tomfitz"/>


# Como usar o Armazenamento de Tabela do PHP

[AZURE.INCLUDE [storage-selector-table-include](../includes/storage-selector-table-include.md)]

## Visão geral

Este guia mostrará como executar cenários comuns usando o serviço de Tabela do Azure. Os exemplos são escritos em PHP e usam o [SDK do Azure para PHP][download]. Os cenários abrangidos incluem **criar e excluir uma tabela, e inserir, excluir e consultar entidades em uma tabela**. Para obter mais informações sobre o serviço de Tabela do Azure, consulte a seção [Próximas etapas](#NextSteps) .

[AZURE.INCLUDE [storage-table-concepts-include](../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

## Criar um aplicativo PHP

O único requisito para a criação de um aplicativo PHP que acessa o serviço de Tabela do Azure é a referência das classes no SDK do Azure para PHP de dentro de seu código. Você pode usar as ferramentas de desenvolvimento para criar seu aplicativo, incluindo o bloco de notas.

Neste guia, você usará os recursos de serviço de Tabela que podem ser chamados de dentro de um aplicativo PHP localmente ou no código em execução dentro de uma função web do Azure, função de trabalho ou no site.

## Obter as Bibliotecas de Cliente do Azure

[AZURE.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

## Configurar seu aplicativo para acessar o serviço de Tabela

Para usar as APIs do serviço de Tabela do Azure, você precisa:

1. Fazer referência ao arquivo de carregador automático usando a instrução [require_once][require_once], e
2. Fazer referência a qualquer classe que você possa usar.

O exemplo a seguir mostra como incluir o arquivo de carregador automático e fazer referência à classe **ServicesBuilder**.

> [AZURE.NOTE] Esse exemplo (e outros exemplos deste artigo) pressupõe que você tenha instalado as Bibliotecas de Cliente do PHP para o Azure por meio do Compositor. Se você instalou as bibliotecas manualmente ou como um pacote PEAR, você precisará fazer referência ao arquivo de carregador automático <code>WindowsAzure.php</code>.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


Nos exemplos abaixo, a instrução `require_once` será mostrada sempre, mas somente as classes necessárias para executar o exemplo serão referenciadas.

## Configurar uma conexão de armazenamento do Azure

Para criar uma instância de um cliente de serviço de Tabela do Azure, você deve primeiramente ter uma cadeia de conexão válida. O formato da cadeia de conexão do serviço de Tabela é:

Para acessar um serviço ao vivo:

	DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Para acessar o armazenamento do emulador:

	UseDevelopmentStorage=true


Para criar qualquer cliente de serviço do Azure é necessário usar a classe **ServicesBuilder**. Você pode:

* passar a cadeia de conexão diretamente para ele ou
* usar o **CloudConfigurationManager (CCM)** para verificar várias origens externas para a cadeia de conexão:
	* por padrão, ele vem com suporte para uma origem externa - variáveis de ambiente
	* você pode adicionar novas origens ao estender a classe **ConnectionStringSource**

Para os exemplos descritos aqui, a cadeia de conexão será passada diretamente.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);


## Como criar uma tabela

O objeto **TableRestProxy** permite que você crie uma tabela com o método **createTable**. Ao criar uma tabela, você pode definir o tempo limite do serviço de Tabela. (Para obter mais informações sobre o tempo de limite do serviço de Tabela, consulte [Tempos limite de configuração para as operações do serviço de Tabela][table-service-timeouts].)

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

	try	{
		// Create table.
		$tableRestProxy->createTable("mytable");
	}
	catch(ServiceException $e){
		$code = $e->getCode();
		$error_message = $e->getMessage();
		// Handle exception based on error codes and messages.
		// Error codes and messages can be found here: 
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
	}

Para obter informações sobre restrições em nomes de tabelas, consulte [Noções básicas sobre o modelo de dados do serviço de Tabela][table-data-model].

## Como: Adicionar uma entidade a uma tabela

Para adicionar uma entidade a uma tabela, crie um novo objeto **Entidade** e passe-o para **TableRestProxy->insertEntity**. Observe que quando você criar uma entidade, você deve especificar um `PartitionKey` e `RowKey`. Estes são os identificadores exclusivos para uma entidade e são os valores que podem ser consultados muito mais rápido que as outras propriedades da entidade. O sistema usa `PartitionKey` para distribuir automaticamente as entidades das tabelas por vários nós de armazenamento. As entidades com a mesma `PartitionKey` são armazenadas no mesmo nó. (Executarão operações em várias entidades armazenadas no mesmo nó melhor do que em entidades armazenadas em nós diferentes.)  `RowKey` é a ID exclusiva de uma entidade dentro de uma partição.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\Entity;
	use WindowsAzure\Table\Models\EdmType;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$entity = new Entity();
	$entity->setPartitionKey("tasksSeattle");
	$entity->setRowKey("1");
	$entity->addProperty("Description", null, "Take out the trash.");
	$entity->addProperty("DueDate", 
						 EdmType::DATETIME, 
						 new DateTime("2012-11-05T08:15:00-08:00"));
	$entity->addProperty("Location", EdmType::STRING, "Home");
	
	try{
		$tableRestProxy->insertEntity("mytable", $entity);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
	}

Para obter informações sobre tipos e propriedades de tabelas, consulte [Noções básicas sobre o modelo de dados do serviço de Tabela][table-data-model].

A classe **TableRestProxy** oferece dois métodos alternativos para inserir entidades: **insertOrMergeEntity** e **insertOrReplaceEntity**. Para usar esses métodos, crie uma nova **Entidade** e passe-a como um parâmetro para qualquer método. Cada método vai inserir a entidade se ela não existir. Se a entidade já existe, **insertOrMergeEntity** irá atualizar valores de propriedade, se as propriedades já existem e adicionar novas propriedades se elas não existirem, enquanto **insertOrReplaceEntity** substitui completamente uma entidade existente. O exemplo a seguir mostra como usar o **insertOrMergeEntity**. Se a entidade com `PartitionKey` "tasksSeattle" e `RowKey` "1" ainda não existir, ela será inserida. No entanto, se ela tiver sido inserida anteriormente (conforme mostrado no exemplo acima), a propriedade `DueDate` será atualizada e a propriedade `Status` será adicionada. As propriedades  `Description` e  `Location` também são atualizadas, mas com valores que efetivamente as deixam inalteradas. Se essas duas últimas propriedades não foram adicionadas conforme mostrado no exemplo, mas existiam na entidade de destino, seus valores existentes permaneceriam inalterados.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\Entity;
	use WindowsAzure\Table\Models\EdmType;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	//Create new entity.
	$entity = new Entity();
	
	// PartitionKey and RowKey are required.
	$entity->setPartitionKey("tasksSeattle");
	$entity->setRowKey("1");
	
	// If entity exists, existing properties are updated with new values and
	// new properties are added. Missing properties are unchanged.
	$entity->addProperty("Description", null, "Take out the trash.");
	$entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
	$entity->addProperty("Location", EdmType::STRING, "Home");
	$entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.
	
	try	{
		// Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
		// would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
		$tableRestProxy->insertOrMergeEntity("mytable", $entity);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	   

## Como: Recuperar uma única entidade

O método **TableRestProxy->getEntity** permite que você recupere uma única entidade consultando seu `PartitionKey` e `RowKey`. No exemplo abaixo, a chave da partição `tasksSeattle` e a chave de linha '1' são passadas para o método **getEntity**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	try	{
		$result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	$entity = $result->getEntity();

	echo $entity->getPartitionKey().":".$entity->getRowKey();

## Como: Recuperar todas as entidades em uma partição

As consultas de entidades são construídas usando filtros (para obter mais informações, consulte [Consultando tabelas e entidades][filtros]). Para recuperar todas as entidades na partição, use o filtro "PartitionKey eq *partition_name*". O exemplo a seguir mostra como recuperar todas as entidades na partição `tasksSeattle` passando um filtro para o método **queryEntities**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$filter = "PartitionKey eq 'tasksSeattle'";
	
	try	{
		$result = $tableRestProxy->queryEntities("mytable", $filter);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	$entities = $result->getEntities();
	
	foreach($entities as $entity){
		echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
	}

## Como: Recuperar um subconjunto de entidades em uma partição

O mesmo padrão usado no exemplo anterior pode ser usado para recuperar qualquer subconjunto de entidades em uma partição. O subconjunto de entidades que você recuperar será determinado pelo filtro que você usar (para obter mais informações, consulte [Consultando tabelas e entidades][filtros]). O exemplo a seguir mostra como usar um filtro para recuperar todas as entidades em um determinado `Location` e uma `DueDate` menor do que uma data especificada.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";
	
	try	{
		$result = $tableRestProxy->queryEntities("mytable", $filter);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	$entities = $result->getEntities();
	
	foreach($entities as $entity){
		echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
	}

## Como: Recuperar um subconjunto de propriedades da entidade

Uma consulta pode recuperar um subconjunto de propriedades da entidade. Essa técnica, chamada *projection*, reduz a largura de banda e pode melhorar o desempenho da consulta, principalmente para grandes entidades. Para especificar uma propriedade a ser recuperada, passe o nome da propriedade para o método **Consulta->addSelectField**. Você pode chamar esse método várias vezes para adicionar mais propriedades. Depois da execução de **TableRestProxy->queryEntities**, as entidades retornadas somente terão as propriedades selecionadas. (Se você desejar retornar um subconjunto de entidades de tabela, use um filtro conforme as consultas acima.)

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\QueryEntitiesOptions;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$options = new QueryEntitiesOptions();
	$options->addSelectField("Description");
	
	try	{
		$result = $tableRestProxy->queryEntities("mytable", $options);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}
	
	// All entities in the table are returned, regardless of whether 
	// they have the Description field.
	// To limit the results returned, use a filter.
	$entities = $result->getEntities();

	foreach($entities as $entity){
		$description = $entity->getProperty("Description")->getValue();
		echo $description."<br />";
	}

## Como: Atualizar uma entidade

Uma entidade existente pode ser atualizada usando os métodos **Entity->setProperty** e **Entity->addProperty** na entidade e, em seguida, chamando **TableRestProxy->updateEntity**. O exemplo a seguir recupera uma entidade, modifica uma propriedade, remove outra propriedade e adiciona uma nova propriedade. Observe que a remoção de uma propriedade é feita definindo seu valor como **nulo**. 

	require_once 'vendor\autoload.php';
	
	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\Entity;
	use WindowsAzure\Table\Models\EdmType;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	$result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
	
	$entity = $result->getEntity();
	
	$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.
	
	$entity->setPropertyValue("Location", null); //Removed Location.
	
	$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

	try	{
		$tableRestProxy->updateEntity("mytable", $entity);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## Como: Excluir uma entidade

Para excluir uma entidade, passe o nome da tabela e a `PartitionKey` e `RowKey` da entidade para o método **TableRestProxy->deleteEntity**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	try	{
		// Delete entity.
		$tableRestProxy->deleteEntity("mytable", "tasksSeattle", "2");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Observe que, para verificações de simultaneidade, você pode definir o Etag para uma entidade a ser excluída, usando o método **DeleteEntityOptions->setEtag** e passando o objeto **DeleteEntityOptions** para **deleteEntity** como um quarto parâmetro.

## Como: Operações de tabela em lote

O método **TableRestProxy->lote** permite que você execute várias operações em uma única solicitação. O padrão aqui envolve a adição de operações para o objeto **BatchRequest** e, em seguida, passa o objeto **BatchRequest** para o método **TableRestProxy->lote**. Para adicionar uma operação a um objeto **BatchRequest**, você pode chamar várias vezes para qualquer um dos seguintes métodos:

* **addInsertEntity** (adds an insertEntity operation)
* **addUpdateEntity** (adds an updateEntity operation)
* **addMergeEntity** (adds a mergeEntity operation)
* **addInsertOrReplaceEntity** (adds an insertOrReplaceEntity operation)
* **addInsertOrMergeEntity** (adds an insertOrMergeEntity operation)
* **addDeleteEntity** (adds a deleteEntity operation)

O exemplo a seguir mostra como executar as operações **insertEntity** e **deleteEntity** em uma única solicitação:

	require_once 'vendor\autoload.php';
	
	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\Table\Models\Entity;
	use WindowsAzure\Table\Models\EdmType;
	use WindowsAzure\Table\Models\BatchOperations;

 	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	// Create list of batch operation.
	$operations = new BatchOperations();
	
	$entity1 = new Entity();
	$entity1->setPartitionKey("tasksSeattle");
	$entity1->setRowKey("2");
	$entity1->addProperty("Description", null, "Clean roof gutters.");
	$entity1->addProperty("DueDate", 
						  EdmType::DATETIME, 
						  new DateTime("2012-11-05T08:15:00-08:00"));
	$entity1->addProperty("Location", EdmType::STRING, "Home");
	
	// Add operation to list of batch operations.
    $operations->addInsertEntity("mytable", $entity1);

	// Add operation to list of batch operations.
	$operations->addDeleteEntity("mytable", "tasksSeattle", "1");
	
	try	{
		$tableRestProxy->batch($operations);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Para obter mais informações sobre operações de tabela em lotes, consulte [Executando transações do grupo de entidade][entity-group-transactions].

## Como: Excluir uma tabela

Finalmente, para excluir uma tabela, passe o nome da tabela para o método **TableRestProxy->deleteTable**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create table REST proxy.
	$tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);
	
	try	{
		// Delete table.
		$tableRestProxy->deleteTable("mytable");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179438.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## Próximas etapas

Agora que você aprendeu os conceitos básicos do Serviço de Tabela do Azure, siga estes links para saber mais sobre tarefas de armazenamento mais complexas.

- Consulte a referência de MSDN: [Armazenamento do Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx)
- Visite o Blog da equipe do [Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/)

[baixar]: http://go.microsoft.com/fwlink/?LinkID=252473
[Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[require_once]: http://php.net/require_once
[table-service-timeouts]: http://msdn.microsoft.com/library/azure/dd894042.aspx

[table-data-model]: http://msdn.microsoft.com/library/azure/dd179338.aspx
[filtros]: http://msdn.microsoft.com/library/azure/dd894031.aspx
[entity-group-transactions]: http://msdn.microsoft.com/library/azure/dd894038.aspx

<!--HONumber=49-->