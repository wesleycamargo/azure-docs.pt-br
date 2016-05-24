<properties
	pageTitle="Como usar o armazenamento de blob do PHP | Microsoft Azure"
	description="Armazene dados não estruturados na nuvem com o Armazenamento de Blobs do Azure (armazenamento de objeto)."
	documentationCenter="php"
	services="storage"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
    ms.date="04/29/2016"
	ms.author="robmcm"/>

# Como usar o armazenamento de blob no PHP

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Visão geral

Este guia mostrará como executar cenários comuns usando o serviço Blob do Azure. As amostras são escritas em PHP e usam o [SDK do Azure para PHP][download]. Os cenários cobertos incluem **carregamento**, **listagem**, **download** e **exclusão** de blobs. Para saber mais sobre blobs, consulte a seção [Próximas etapas](#next-steps).

[AZURE.INCLUDE [armazenamento-blob-conceitos-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Criar um aplicativo PHP

O único requisito para a criação de um aplicativo PHP que acessa o serviço Blob do Azure é a referência de classes no SDK do Azure para PHP em seu código. Você pode usar as ferramentas de desenvolvimento para criar seu aplicativo, incluindo o bloco de notas.

Neste guia, você usará os recursos de serviços que podem ser chamados em um aplicativo PHP localmente ou no código em execução dentro de uma função web do Azure, uma função de trabalho ou um site.

## Obter as bibliotecas de cliente do Azure

[AZURE.INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## Configurar seu aplicativo para acessar o serviço Blob

Para usar as APIs do serviço Blob do Azure, você precisa:

1. Fazer referência ao arquivo de carregador automático usando a instrução [require\_once], e
2. Fazer referência a qualquer classe que você possa usar.

O exemplo a seguir mostra como incluir o arquivo de carregador automático e fazer referência à classe **ServicesBuilder**.

> [AZURE.NOTE] Esse exemplo (e outros exemplos deste artigo) pressupõe que você tenha instalado as Bibliotecas de Cliente do PHP para o Azure por meio do Compositor. Se você instalou as bibliotecas manualmente ou como um pacote PEAR, precisará fazer referência ao arquivo de carregador automático `WindowsAzure.php`.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


Nos exemplos abaixo, a instrução `require_once` será mostrada sempre, mas somente as classes necessárias para executar o exemplo serão referenciadas.

## Configurar uma conexão de armazenamento do Azure

Para criar uma instância de um cliente de serviço Blob do Azure, você deve primeiramente ter uma cadeia de conexão válida. O formato da cadeia de conexão do serviço blob é:

Para acessar um serviço ao vivo:

	DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

Para acessar o emulador de armazenamento:

	UseDevelopmentStorage=true


Para criar qualquer cliente de serviço do Azure, é necessário usar a classe **ServicesBuilder**. Você pode:

* Passar a cadeia de conexão diretamente para ele ou
* Usar **CloudConfigurationManager (CCM)** para verificar várias fontes externas da cadeia de conexão:
	* Por padrão, ele é fornecido com suporte para uma fonte externa – variáveis de ambiente.
	* É possível adicionar novas origens ao estender a classe **ConnectionStringSource**.

Para os exemplos descritos aqui, a cadeia de conexão será passada diretamente.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

## Criar um contêiner

[AZURE.INCLUDE [armazenamento-contêiner-nomeando-regras-include](../../includes/storage-container-naming-rules-include.md)]

O objeto **BlobRestProxy** permite que você crie um contêiner de blob com o método **createContainer**. Ao criar um contêiner, você pode definir opções no contêiner, mas fazer isso não é necessário. (O exemplo a seguir mostra como definir os metadados do contêiner e a ACL [lista de controle de acesso] do contêiner.)

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

	// Set container metadata.
	$createContainerOptions->addMetaData("key1", "value1");
	$createContainerOptions->addMetaData("key2", "value2");

	try	{
		// Create container.
		$blobRestProxy->createContainer("mycontainer", $createContainerOptions);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here:
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

A chamada a **setPublicAccess(PublicAccessType::CONTAINER\_AND\_BLOBS)** faz com que os dados do contêiner e do blob fiquem acessíveis por meio de solicitações anônimas. A chamada a **setPublicAccess(PublicAccessType::BLOBS\_ONLY)** faz com que apenas os dados do blob fiquem acessíveis por meio de solicitações anônimas. Para obter mais informações sobre ACLs de contêiner, consulte [Configurar ACL do contêiner (REST API)][container-acl].

Para obter mais informações sobre códigos de erro de serviço Blob, consulte [Códigos de erro de serviço Blob][error-codes].

## Carregar um blob em um contêiner

Para carregar um arquivo como um blob, use o método **BlobRestProxy->createBlockBlob**. Essa operação cria o blob, se ele não existir, ou o substitui, se ele já existir. O exemplo de código a seguir pressupõe que o contêiner já foi criado e usa [fopen][fopen] para abrir o arquivo como um fluxo.

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
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Observe que o exemplo anterior carrega um blob como um fluxo. No entanto, um blob também pode ser carregado como uma cadeia usando, por exemplo, a função [file\_get\_contents][file_get_contents]. Para fazer isso usando o exemplo anterior, altere `$content = fopen("c:\myfile.txt", "r");` para `$content = file_get_contents("c:\myfile.txt");`.

## Listar os blobs em um contêiner

Para listar os blobs em um contêiner, use o método **BlobRestProxy->listBlobs** com um loop **foreach** para executar um loop pelo resultado. O código a seguir mostra o nome de cada blob como resultado em um contêiner e exibe seu URI para o navegador.

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
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}


## Baixar um blob

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
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Observe que o exemplo acima obtém um blob como um recurso de fluxo (o comportamento padrão). No entanto, você pode usar a função [stream\_get\_contents][stream-get-contents] para converter o fluxo de retorno em uma cadeia.

## Excluir um blob

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
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## Excluir um contêiner de blob

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
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## Próximas etapas

Agora que você aprendeu os conceitos básicos do serviço Blob do Azure, siga estes links para saber mais sobre tarefas de armazenamento mais complexas.

- Visite o [Blog da Equipe de Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Consulte o [exemplo de blob de blocos PHP](https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php).
- Consulte o [exemplo de blob de páginas PHP](https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php).
- [Transferir dados com o Utilitário de Linha de Comando AzCopy](storage-use-azcopy.md)
 
Para saber mais, veja também a [Central de desenvolvedores do PHP](/develop/php/).


[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[container-acl]: http://msdn.microsoft.com/library/azure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/library/azure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require\_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents

<!---HONumber=AcomDC_0511_2016-->