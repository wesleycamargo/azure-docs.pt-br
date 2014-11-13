<properties urlDisplayName="Table Service" pageTitle="Como usar o armazenamento de tabela (PHP) | Microsoft Azure" metaKeywords="Azure Table service PHP, Azure creating table, Azure deleting table, Azure insert table, Azure query table" description="Saiba como usar o servi&ccedil;o Tabela do PHP para criar e excluir tabelas e inserir, excluir e consultar a tabela." metaCanonical="" services="storage" documentationCenter="PHP" title="Como usar o servi&ccedil;o Tabela do PHP" authors="tamram" solutions="" manager="adinah" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Como usar o serviço Tabela do PHP

Este guia mostrará como executar cenários comuns usando o serviço Tabela do Azure. As amostras são escritas em PHP e usam o [SDK do Azure para PHP][SDK do Azure para PHP]. Os cenários abrangidos incluem **criar e excluir uma tabela, e inserir, excluir e consultar entidades em uma tabela**. Para obter mais informações sobre o serviço Tabela do Azure, consulte a seção [Próximas etapas][Próximas etapas].

## Sumário

-   [O que é o serviço Tabela][O que é o serviço Tabela]
-   [Conceitos][Conceitos]
-   [Criar uma conta de armazenamento do Azure][Criar uma conta de armazenamento do Azure]
-   [Criar um aplicativo PHP][Criar um aplicativo PHP]
-   [Configurar seu aplicativo para acessar o serviço Tabela][Configurar seu aplicativo para acessar o serviço Tabela]
-   [Configurar uma conexão de armazenamento do Azure][Configurar uma conexão de armazenamento do Azure]
-   [Como: Criar uma tabela][Como: Criar uma tabela]
-   [Como: Adicionar uma entidade a uma tabela][Como: Adicionar uma entidade a uma tabela]
-   [Como: Recuperar uma única entidade][Como: Recuperar uma única entidade]
-   [Como: Recuperar todas as entidades em uma partição][Como: Recuperar todas as entidades em uma partição]
-   [Como: Recuperar um subconjunto de entidades em uma partição][Como: Recuperar um subconjunto de entidades em uma partição]
-   [Como: Recuperar um subconjunto de propriedades da entidade][Como: Recuperar um subconjunto de propriedades da entidade]
-   [Como: Atualizar uma entidade][Como: Atualizar uma entidade]
-   [Como: Operações de tabela em lote][Como: Operações de tabela em lote]
-   [Como: Excluir uma tabela][Como: Excluir uma tabela]
-   [Próximas etapas][Próximas etapas]

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

## <span id="CreateAccount"></span></a>Criar uma conta de armazenamento do Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <span id="CreateApplication"></span></a>Criar um aplicativo PHP

O único requisito para a criação de um aplicativo PHP que acessa o serviço Tabela do Azure é a referência das classes no SDK do Azure para PHP de dentro de seu código. Você pode usar as ferramentas de desenvolvimento para criar seu aplicativo, incluindo o bloco de notas.

Neste guia, você usará os recursos de serviço Tabela que podem ser chamados de dentro de um aplicativo PHP localmente ou no código em execução dentro de uma função Web do Azure, função de trabalho ou no site.

## <span id="GetClientLibrary"></span></a>Obter as bibliotecas de cliente do Azure

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

## <span id="ConfigureStorage"></span></a>Configurar seu aplicativo para acessar o serviço Tabela

Para usar as APIs do serviço Tabela do Azure, você precisa:

1.  Fazer referência ao arquivo de carregador automático usando a instrução [require\_once][require\_once], e
2.  Fazer referência a qualquer classe que você possa usar.

O exemplo a seguir mostra como incluir o arquivo de carregador automático e fazer referência à classe **ServicesBuilder**.

> [WACOM.NOTE]
> Este exemplo (e outros exemplos deste artigo) pressupõe que você instalou as Bibliotecas de Cliente do PHP para Azure por meio do Compositor. Se você instalou as bibliotecas manualmente ou como um pacote PEAR, você precisará fazer referência ao arquivo de carregador automático `WindowsAzure.php`.

    require_once 'vendor\autoload.php';
    use WindowsAzure\Common\ServicesBuilder;

Nos exemplos abaixo, a instrução '`require_once`' será mostrada sempre, mas somente as classes necessárias para executar o exemplo serão referenciadas.

## <span id="ConnectionString"></span></a>Configurar uma conexão de armazenamento do Azure

Para criar uma instância de um cliente de serviço Tabela do Azure, você deve primeiramente ter uma cadeia de conexão válida. O formato da cadeia de conexão do serviço Tabela é:

Para acessar um serviço ao vivo:

    DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Para acessar o armazenamento do emulador:

    UseDevelopmentStorage=true

Para criar qualquer cliente de serviço do Azure é necessário usar a classe **ServicesBuilder**. Você pode:

-   passar a cadeia de conexão diretamente para ele ou
-   usar o **CloudConfigurationManager (CCM)** para verificar várias origens externas para a cadeia de conexão:

    -   por padrão, ele vem com suporte para uma origem externa - variáveis de ambiente
    -   você pode adicionar novas origens ao estender a classe **ConnectionStringSource**

Para os exemplos descritos aqui, a cadeia de conexão será passada diretamente.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

## <span id="CreateTable"></span></a>Como: criar uma tabela

O objeto **TableRestProxy** permite que você crie uma tabela com o método **createTable**. Ao criar uma tabela, você pode definir o tempo limite do serviço Tabela. (Para obter mais informações sobre o tempo de limite do serviço Tabela, consulte [Tempos limite de configuração para as operações do serviço Tabela][Tempos limite de configuração para as operações do serviço Tabela].)

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Create table.
        $tableRestProxy->createTable("mytable");
    }
    catch(ServiceException $e){
        $code = $e->getCode();
        $error_message = $e->getMessage();
        // Handle exception based on error codes and messages.
        // Error codes and messages can be found here: 
        // http://msdn.microsoft.com/pt-br/library/windowsazure/dd179438.aspx
    }

Para obter informações sobre restrições em nomes de tabelas, consulte [Noções básicas sobre o modelo de dados do serviço Tabela][Noções básicas sobre o modelo de dados do serviço Tabela].

## <span id="AddEntity"></span></a>Como: Adicionar uma entidade a uma tabela

Para adicionar uma entidade a uma tabela, crie um novo objeto **Entidade** e passá-lo para **TableRestProxy-\>insertEntity**. Observe que quando você criar uma entidade, você deve especificar um `PartitionKey` e `RowKey`. Estes são os identificadores exclusivos para uma entidade e são os valores que podem ser consultados muito mais rápido que as outras propriedades da entidade. O sistema usa a `PartitionKey` para distribuir automaticamente as entidades das tabelas por vários nós de armazenamento. As entidades com a mesma `PartitionKey` são armazenadas no mesmo nó. (Executarão operações em várias entidades armazenadas no mesmo nó melhor do que em entidades armazenadas em nós diferentes.) O `RowKey` é o ID exclusivo de uma entidade dentro de uma partição.

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
        // http://msdn.microsoft.com/pt-br/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
    }

Para obter informações sobre tipos e propriedades de tabelas, consulte [Noções básicas sobre o modelo de dados do serviço Tabela][Noções básicas sobre o modelo de dados do serviço Tabela].

A classe **TableRestProxy** oferece dois métodos alternativos para inserir entidades: **insertOrMergeEntity** e **insertOrReplaceEntity**. Para usar esses métodos, crie uma nova **Entidade** e passe-a como um parâmetro para qualquer método. Cada método irá inserir a entidade se ela não existir. Se a entidade já existe, **insertOrMergeEntity** irá atualizar valores de propriedade, se as propriedades já existem e adicionar novas propriedades se elas não existirem, enquanto **insertOrReplaceEntity** substitui completamente uma entidade existente. O exemplo a seguir mostra como usar o **insertOrMergeEntity**. Se a entidade com `PartitionKey` "tasksSeattle" e `RowKey` "1" ainda não existir, ela será inserida. No entanto, se ela tiver sido inserida anteriormente (conforme mostrado no exemplo acima), a propriedade `DueDate` será atualizada e a propriedade `Status` será adicionada. As propriedades `Description` e `Location` também são atualizadas, mas com valores que efetivamente as deixam inalteradas. Se essas duas últimas propriedades não foram adicionadas conforme mostrado no exemplo, mas existia na entidade de destino, seus valores existentes permaneceriam inalterados.

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

    try {
        // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
        // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
        $tableRestProxy->insertOrMergeEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/pt-br/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }
       

## <span id="RetrieveEntity"></span></a>Como: Recuperar uma única entidade

O método **TableRestProxy-\>getEntity** permite que você recupere uma única entidade consultando seu `PartitionKey` e `RowKey`. No exemplo abaixo, a chave da partição `tasksSeattle` e a chave de linha `1` são passadas para o método **getEntity**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        $result = $tableRestProxy->getEntity("mytable", "tasksSeattle", 1);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/pt-br/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entity = $result->getEntity();

    echo $entity->getPartitionKey().":".$entity->getRowKey();

## <span id="RetEntitiesInPartition"></span></a>Como: Recuperar todas as entidades em uma partição

As consultas de entidades são construídas usando filtros (para obter mais informações, consulte [Consultar tabelas e entidades][Consultar tabelas e entidades]). Para recuperar todas as entidades na partição, use o filtro "PartitionKey eq *nome\_da\_partição*". O exemplo a seguir mostra como recuperar todas as entidades na partição `tasksSeattle` passando um filtro para o método **queryEntities**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $filter = "PartitionKey eq 'tasksSeattle'";

    try {
        $result = $tableRestProxy->queryEntities("mytable", $filter);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/pt-br/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entities = $result->getEntities();

    foreach($entities as $entity){
        echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
    }

## <span id="RetrieveSubset"></span></a>Como: Recuperar um subconjunto de entidades em uma partição

O mesmo padrão usado no exemplo anterior pode ser usado para recuperar qualquer subconjunto de entidades em uma partição. O subconjunto de entidades que você recuperar será determinado pelo filtro que você usar (para obter mais informações, consulte [Consultar Tabelas e Entidades][Consultar tabelas e entidades]). O exemplo a seguir mostra como usar um filtro para recuperar todas as entidades com um determinado `Location` e `DueDate` menor do que uma data especificada.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

    try {
        $result = $tableRestProxy->queryEntities("mytable", $filter);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/pt-br/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    $entities = $result->getEntities();

    foreach($entities as $entity){
        echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
    }

## <span id="RetPropertiesSubset"></span></a>Como: Recuperar um subconjunto de propriedades da entidade

Uma consulta pode recuperar um subconjunto de propriedades da entidade. Essa técnica, chamada *projeção*, reduz a largura de banda e pode melhorar o desempenho da consulta, principalmente para grandes entidades. Para especificar uma propriedade a ser recuperada, passe o nome da propriedade para o método **Consulta-\>addSelectField**. Você pode chamar esse método várias vezes para adicionar mais propriedades. Depois da execução de **TableRestProxy-\>queryEntities**, as entidades retornadas somente terão as propriedades selecionadas. (Se você desejar retornar um subconjunto de entidades de tabela, use um filtro conforme as consultas acima.)

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\Table\Models\QueryEntitiesOptions;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    $options = new QueryEntitiesOptions();
    $options->addSelectField("Description");

    try {
        $result = $tableRestProxy->queryEntities("mytable", $options);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/pt-br/library/windowsazure/dd179438.aspx
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

## <span id="UpdateEntity"></span></a>Como: Atualizar uma entidade

Uma entidade existente pode ser atualizada usando os métodos **Entidade-\>setProperty** e **Entidade-\>addProperty** na entidade e, em seguida, chamando **TableRestProxy-\>updateEntity**. O exemplo a seguir recupera uma entidade, modifica uma propriedade, remove outra propriedade e adiciona uma nova propriedade. Observe que a remoção de uma propriedade é feita definindo seu valor como **nulo**.

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

    try {
        $tableRestProxy->updateEntity("mytable", $entity);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/pt-br/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="DeleteEntity"></span></a>Como: Excluir uma entidade

Para excluir uma entidade, passe o nome da tabela e a `PartitionKey` e `RowKey` da entidade para o método **TableRestProxy-\>deleteEntity**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Delete entity.
        $tableRestProxy->deleteEntity("mytable", "tasksSeattle", "2");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/pt-br/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Observe que, para verificações de simultaneidade, você pode definir o Etag para uma entidade a ser excluída, usando o método **DeleteEntityOptions-\>setEtag** e passando o objeto **DeleteEntityOptions** para **deleteEntity** como um quarto parâmetro.

## <span id="BatchOperations"></span></a>Como: Operações de tabela em lote

O método **TableRestProxy-\>lote** permite que você execute várias operações em uma única solicitação. O padrão aqui envolve a adição de operações para o objeto **BatchRequest** e, em seguida, passa o objeto **BatchRequest** para o método **TableRestProxy-\>lote**. Para adicionar uma operação a um objeto **BatchRequest**, você pode chamar várias vezes para qualquer um dos seguintes métodos:

-   **addInsertEntity** (adiciona uma operação insertEntity)
-   **addUpdateEntity** (adiciona uma operação updateEntity)
-   **addMergeEntity** (adiciona uma operação mergeEntity)
-   **addInsertOrReplaceEntity** (adiciona uma operação insertOrReplaceEntity)
-   **addInsertOrMergeEntity** (adiciona uma operação insertOrMergeEntity)
-   **addDeleteEntity** (adiciona uma operação deleteEntity)

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

    try {
        $tableRestProxy->batch($operations);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/pt-br/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

Para obter mais informações sobre operações de tabela em lotes, consulte [Executando transações do grupo de entidade][Executando transações do grupo de entidade].

## <span id="DeleteTable"></span></a>Como: Excluir uma tabela

Finalmente, para excluir uma tabela, passe o nome da tabela para o método **TableRestProxy-\>deleteTable**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create table REST proxy.
    $tableRestProxy = ServicesBuilder::getInstance()->createTableService($connectionString);

    try {
        // Delete table.
        $tableRestProxy->deleteTable("mytable");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/pt-br/library/windowsazure/dd179438.aspx
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

## <span id="NextSteps"></span></a>Próximas etapas

Agora que você aprendeu os conceitos básicos do Serviço de Tabela do Azure, siga estes links para saber como executar tarefas de armazenamento mais complexas.

-   Consulte a referência de MSDN: [Armazenando e acessando dados no Azure][Armazenando e acessando dados no Azure]
-   Visite o Blog da Equipe de Armazenamento do Azure: <http://blogs.msdn.com/b/windowsazurestorage/>

  [SDK do Azure para PHP]: http://go.microsoft.com/fwlink/?LinkID=252473
  [Próximas etapas]: #NextSteps
  [O que é o serviço Tabela]: #what-is
  [Conceitos]: #concepts
  [Criar uma conta de armazenamento do Azure]: #CreateAccount
  [Criar um aplicativo PHP]: #CreateApplication
  [Configurar seu aplicativo para acessar o serviço Tabela]: #ConfigureStorage
  [Configurar uma conexão de armazenamento do Azure]: #ConnectionString
  [Como: Criar uma tabela]: #CreateTable
  [Como: Adicionar uma entidade a uma tabela]: #AddEntity
  [Como: Recuperar uma única entidade]: #RetrieveEntity
  [Como: Recuperar todas as entidades em uma partição]: #RetEntitiesInPartition
  [Como: Recuperar um subconjunto de entidades em uma partição]: #RetrieveSubset
  [Como: Recuperar um subconjunto de propriedades da entidade]: #RetPropertiesSubset
  [Como: Atualizar uma entidade]: #UpdateEntity
  [Como: Operações de tabela em lote]: #BatchOperations
  [Como: Excluir uma tabela]: #DeleteTable
  [require\_once]: http://php.net/require_once
  [Tempos limite de configuração para as operações do serviço Tabela]: http://msdn.microsoft.com/pt-br/library/windowsazure/dd894042.aspx
  [Noções básicas sobre o modelo de dados do serviço Tabela]: http://msdn.microsoft.com/pt-br/library/windowsazure/dd179338.aspx
  [Consultar tabelas e entidades]: http://msdn.microsoft.com/pt-br/library/windowsazure/dd894031.aspx
  [Executando transações do grupo de entidade]: http://msdn.microsoft.com/pt-br/library/windowsazure/dd894038.aspx
  [Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx
