<properties title="Como usar o armazenamento de Blob (PHP) - Guia de recursos do Azure" pageTitle="Como usar o armazenamento de Blob (PHP) | Microsoft Azure" metaKeywords="serviço Blob do Azure PHP, blobs do Azure PHP" description="Saiba como usar o serviço Blob do Azure para carregar, listar, baixar e excluir blobs. Os exemplos de código são escritos em PHP." documentationCenter="PHP" services="storage" videoId="" scriptId="" solutions="" authors="waltpo" manager="bjsmith" editor="mollybos" />

#Como usar o serviço Blob do PHP

Este guia mostra como executar cenários comuns usando o serviço Blob do Azure. Os exemplos são escritos em PHP e usam o [SDK do Azure para PHP] [download]. Os cenários cobertos incluem **carregamento**, **listagem**, **download** e **exclusão** de blobs. Para obter mais informações sobre blobs, consulte a seção [Próximas etapas](#NextSteps).

##Sumário

* [O que é Blob Storage](#what-is)
* [Conceitos](#concepts)
* [Criar uma conta de armazenamento do Azure](#CreateAccount)
* [Criar um aplicativo PHP](#CreateApplication)
* [Configurar seu aplicativo para acessar o Serviço Blob](#ConfigureStorage)
* [Configurar uma conexão de armazenamento do Azure](#ConnectionString)
* [Como criar um contêiner](#CreateContainer)
* [Como: carregar um blob em um contêiner](#UploadBlob)
* [Como: listar os blobs em um contêiner](#ListBlobs)
* [Como: baixar um blob](#DownloadBlob)
* [Como: excluir um blob](#DeleteBlob)
* [Como: excluir um contêiner de blob](#DeleteContainer)
* [Próximas etapas](#NextSteps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

<h2><a id="CreateAccount"></a>Criar uma conta de armazenamento do Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a id="CreateApplication"></a>Criar um aplicativo PHP</h2>

O único requisito para a criação de um aplicativo PHP que acessa o serviço Blob do Azure é a referência das classes no SDK do Azure para PHP de dentro de seu código. Você pode usar as ferramentas de desenvolvimento para criar seu aplicativo, incluindo o bloco de notas.

Neste guia, você usará os recursos de serviços que podem ser chamados em um aplicativo PHP localmente ou no código em execução dentro de uma função web do Azure, uma função de trabalho ou um site.

<h2><a id="GetClientLibrary"></a>Obter as bibliotecas de cliente do Azure</h2>

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

<h2><a id="ConfigureStorage"></a>Configurar seu aplicativo para acessar o Serviço Blob</h2>

Para usar as APIs do serviço Blob do Azure, você precisa:

1. Fazer referência ao arquivo de carregador automático usando a instrução [require_once][require_once] e
2. Fazer referência a qualquer classe que você possa usar.

O exemplo a seguir mostra como incluir o arquivo de carregador automático e fazer referência à classe **ServicesBuilder**.

> [WACOM.NOTE]
> Esse exemplo (e outros exemplos deste artigo) pressupõe que você tenha instalado as Bibliotecas de Cliente do PHP para o Azure por meio do Compositor. Se você instalou as bibliotecas manualmente ou como um pacote PEAR, você precisará fazer referência ao arquivo de carregador automático `WindowsAzure.php`.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


Nos exemplos abaixo, a instrução `exigir_uma vez` será mostrada sempre, mas somente as classes necessárias para executar o exemplo serão referenciadas.

<h2><a id="ConnectionString"></a>Configurar uma conexão de armazenamento do Azure</h2>

Para criar uma instância de um cliente de serviço Blob do Azure, você deve primeiramente ter uma cadeia de conexão válida. O formato da cadeia de conexão do serviço blob é:

Para acessar um serviço ao vivo:

	DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Para acessar o armazenamento do emulador:

	UseDevelopmentStorage=true


Para criar qualquer cliente de serviço do Azure é necessário usar a classe **ServicesBuilder**. Você pode:

* passar a cadeia de conexão diretamente para ele ou
* usar o **CloudConfigurationManager (CCM)** para verificar várias origens externas para a cadeia de conexão:
	* por padrão, ele é fornecido com suporte para uma origem externa - variáveis de ambiente
	* você pode adicionar novas origens ao estender a classe **ConnectionStringSource**

Para os exemplos descritos aqui, a cadeia de conexão será passada diretamente.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

<h2><a id="CreateContainer"></a>Como criar um contêiner</h2>

O objeto **BlobRestProxy** permite que você crie um contêiner de blob com o método **createContainer**. Ao criar um contêiner, você pode definir opções no contêiner, mas fazer isso não é necessário. (O exemplo a seguir mostra como definir os metadados do contêiner e ACL do contêiner.)

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Blob\Models\CreateContainerOptions;
	use WindowsAzure\Blob\Models\PublicAccessType;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


	// OPTIONAL: Set public access policy and metadata.
	// Create container options object.
	$createContainerOptions = new CreateContainerOptions();	

	// Set public access policy. Possible values are 
	// PublicAccessType::CONTAINER_AND_BLOBS and PublicAccessType::BLOBS_ONLY.
	// CONTAINER_AND_BLOBS: 	
	// Specifies full public read access for container and blob data.
    // proxys can enumerate blobs within the container via anonymous 
	// request, but cannot enumerate containers within the storage account.
	//
	// BLOBS_ONLY:
	// Specifies public read access for blobs. Blob data within this 
    // container can be read via anonymous request, but container data is not 
    // available. proxys cannot enumerate blobs within the container via 
	// anonymous request.
	// If this value is not specified in the request, container data is 
	// private to the account owner.
	$createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);
	
	// Set container metadata
	$createContainerOptions->addMetaData("key1", "value1");
	$createContainerOptions->addMetaData("key2", "value2");
	
	try	{
		// Create container.
		$blobRestProxy->createContainer("mycontainer", $createContainerOptions);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/pt-br/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

A chamada de **setPublicAccess(PublicAccessType::CONTAINER\_AND\_BLOBS)** faz com que os dados do contêiner e do blob sejam acessados por meio de solicitações anônimas. A chamada de **setPublicAccess(PublicAccessType::BLOBS_ONLY)** faz com que apenas os dados do blob sejam acessados por meio de solicitações anônimas. Para obter mais informações sobre ACLs de contêiner, consulte [Configurar ACL do Contêiner (REST API)][container-acl].

Para obter mais informações sobre códigos de erro de serviço Blob, consulte [Códigos de erro de serviço Blob][error-codes].

<h2><a id="UploadBlob"></a>Como: carregar um blob em um contêiner</h2>

Para carregar um arquivo como um blob, use o método **BlobRestProxy->createBlockBlob**. Essa operação criará o blob, caso ele não exista, ou o substituirá, caso ele já exista. O exemplo de código a seguir pressupõe que o contêiner já foi criado e usa [fopen][fopen] para abrir o arquivo como um fluxo.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	$content = fopen("c:\myfile.txt", "r");
	$blob_name = "myblob";
	
	try	{
		//Upload blob
		$blobRestProxy->createBlockBlob("mycontainer", $blob_name, $content);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/pt-br/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Observe que o exemplo acima carrega um blob como um fluxo. No entanto, um blob também pode ser carregado como uma cadeia usando, por exemplo, a função [file\_get\_contents][file_get_contents]. Para fazer isso, altere `$content = fopen ("c:\myfile.txt", "r");` no exemplo acima para `$content = file_get_contents("c:\myfile.txt");`.

<h2><a id="ListBlobs"></a>Como: listar os blobs em um contêiner</h2>

Para listar os blobs em um contêiner, use o método **BlobRestProxy->listBlobs** com um loop **foreach** para executar um loop pelo resultado. O código a seguir exibe o nome de cada blob em um contêiner e seu URI para o navegador.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// List blobs.
		$blob_list = $blobRestProxy->listBlobs("mycontainer");
		$blobs = $blob_list->getBlobs();
		
		foreach($blobs as $blob)
		{
			echo $blob->getName().": ".$blob->getUrl()."<br />";
		}
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/pt-br/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}


<h2><a id="DownloadBlob"></a>Como: baixar um blob</h2>

Para baixar um blob, chame o método **BlobRestProxy->getBlob** e, em seguida, chame o método **getContentStream** no objeto **GetBlobResult** resultante.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// Get blob.
		$blob = $blobRestProxy->getBlob("mycontainer", "myblob");
		fpassthru($blob->getContentStream());
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/pt-br/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Observe que o exemplo acima obtém um blob como um recurso de fluxo (o comportamento padrão). No entanto, você pode usar a função [stream\_get\_contents][stream-get-contents] para converter o fluxo de retorno em uma cadeia.

<h2><a id="DeleteBlob"></a>Como: excluir um blob</h2>

Para excluir um blob, passe o nome do contêiner e o nome do blob para **BlobRestProxy -> deleteBlob**. 

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// Delete container.
		$blobRestProxy->deleteBlob("mycontainer", "myblob");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/pt-br/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="DeleteContainer"></a>Como: excluir um contêiner de blob</h2>

Finalmente, para excluir um contêiner de blob, passe o nome do contêiner para **BlobRestProxy->deleteContainer**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// Delete container.
		$blobRestProxy->deleteContainer("mycontainer");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/pt-br/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="NextSteps"></a>Próximas etapas</h2>

Agora que você aprendeu os conceitos básicos do serviço Blob do Azure, siga estes links para saber como executar tarefas de armazenamento mais complexas.

- Consulte a referência do MSDN: [Armazenando e acessando dados no Azure] []
- Visite o Blog da Equipe de Armazenamento do Azure: <http://blogs.msdn.com/b/windowsazurestorage/>
- Consulte o exemplo de blob de bloco PHP em <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php>.
- Consulte o exemplo de blob de página PHP em <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php>

[baixar]: http://go.microsoft.com/fwlink/?LinkID=252473
[Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx
[container-acl]: http://msdn.microsoft.com/pt-br/library/windowsazure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/pt-br/library/windowsazure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents

