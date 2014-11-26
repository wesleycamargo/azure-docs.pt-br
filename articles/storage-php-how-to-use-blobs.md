<properties title="How to use blob storage (PHP) - Azure feature guide" pageTitle="Como usar o armazenamento de blob (PHP) | Microsoft Azure" metaKeywords="Azure blob service PHP, Azure blobs PHP" description="Learn how to use the Azure Blob service to upload, list, download, and delete blobs. Code samples are written in PHP." documentationCenter="PHP" services="storage" videoId="" scriptId="" solutions="" authors="robmcm" manager="adinah" editor="mollybos" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

#Como usar o serviço Blob do PHP

Este guia mostra como executar cenários comuns usando o serviço Blob do Azure. Os exemplos são escritos em PHP e usam o [SDK do Azure para PHP] [baixar]. Os cenários cobertos incluem **carregamento**, **listagem**, **download** e **exclusão** de blobs. Para obter mais informações sobre blobs, consulte a seção [Próximas etapas](#NextSteps) .

##Sumário

* [O que é Blob Storage](#what-is)
* [Conceitos](#concepts)
* [Criar uma conta de armazenamento do Azure](#CreateAccount)
* [Criar um aplicativo PHP](#CreateApplication)
* [Configurar seu aplicativo para acessar o Serviço Blob](#ConfigureStorage)
* [Configurar uma conexão de armazenamento do Azure](#ConnectionString)
* [Como: Criar um contêiner](#CreateContainer)
* [Como: Carregar um blob em um contêiner](#UploadBlob)
* [Como: Listar os blobs em um contêiner](#ListBlobs)
* [Como: Baixar um blob](#DownloadBlob)
* [Como: Excluir um blob](#DeleteBlob)
* [Como: Excluir um contêiner de blob](#DeleteContainer)
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


Nos exemplos abaixo, a instrução `require_once` será mostrada sempre, mas somente as classes necessárias para executar o exemplo serão referenciadas.

<h2><a id="ConnectionString"></a>Configurar uma conexão de armazenamento do Azure</h2>

Para criar uma instância de um cliente de serviço Blob do Azure, você deve primeiramente ter uma cadeia de conexão válida. O formato da cadeia de conexão do serviço blob é:

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

	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

<h2><a id="CreateContainer"></a>Como: Criar um contêiner</h2>

O objeto **BlobRestProxy** permite que você crie um contêiner de blob com o método **createContainer**. Ao criar um contêiner, você pode definir opções no contêiner, mas fazer isso não é necessário. (O exemplo a seguir mostra como definir os metadados do contêiner e ACL do contêiner.)

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Blob\Models\CreateContainerOptions;
	use WindowsAzure\Blob\Models\PublicAccessType;
	use o WindowsAzure\Common\ServiceException;

	// Criar blob proxy REST.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


	// OPCIONAL: Definir metadados e política de acesso público.
	// Criar objeto de opções do contêiner.
	$createContainerOptions = new CreateContainerOptions();	

	// Definir a política de acesso público. Os valores possíveis são 
	// PublicAccessType::CONTAINER_AND_BLOBS e PublicAccessType::BLOBS_ONLY.
	// CONTAINER_AND_BLOBS: 	
	// Especifica o acesso de leitura público completo dos dados do contêiner e de blob.
    // os proxys podem enumerar blobs dentro do contêiner por meio de uma solicitação anônima, 
	// mas não é possível enumerar contêineres dentro da conta de armazenamento.
	//
	// BLOBS_ONLY:
	// Especifica o acesso de leitura público de blobs. Dados de blob dentro desse 
    // contêiner podem ser lidos por solicitação anônima, mas os dados de contêiner não estão 
    // disponíveis. proxys não podem enumerar blobs dentro do contêiner por meio de 
	// solicitação anônima.
	// Se este valor não for especificado na solicitação, dados do contêiner serão 
	// privada para o proprietário da conta.
	$createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);
	
	// Definir metadados de contêiner
	$createContainerOptions->addMetaData("key1", "value1");
	$createContainerOptions->addMetaData("key2", "value2");
	
	try	{
		// Criar contêiner.
		$blobRestProxy->createContainer("mycontainer", $createContainerOptions);
	}
	catch(ServiceException $e){
		// Tratar exceções com base nos códigos de erro e mensagens.
		// Os códigos de erro e mensagem estão aqui: 
		// http://msdn.microsoft.com/pt-br/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

A chamada a **setPublicAccess(PublicAccessType::CONTAINER\_AND\_BLOBS)** faz com que os dados do contêiner e do blob fiquem acessíveis por meio de solicitações anônimas. A chamada a **setPublicAccess(PublicAccessType::BLOBS_ONLY)** faz com que apenas os dados do blob fiquem acessíveis por meio de solicitações anônimas. Para obter mais informações sobre as ACls do contêiner, consulte [Conjunto de ACL do contêiner (REST API)][container-acl].

Para obter mais informações sobre códigos de erro do serviço de Blob, consulte [Códigos de erro do serviço blob][error-codes].

<h2><a id="UploadBlob"></a>Como: Carregar um blob em um contêiner</h2>

Para carregar um arquivo como um blob, use o método **BlobRestProxy->createBlockBlob**. Essa operação criará o blob, se ele não existir, ou o substituirá, se ele já existir. O exemplo de código a seguir pressupõe que o contêiner já foi criado e usa [fopen][fopen] para abrir o arquivo como um fluxo.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use o WindowsAzure\Common\ServiceException;

	// Criar blob proxy REST.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	$content = fopen("c:\myfile.txt", "r");
	$blob_name = "myblob";
	
	try	{
		//Carregar blob
		$blobRestProxy->createBlockBlob("mycontainer", $blob_name, $content);
	}
	catch(ServiceException $e){
		// Tratar exceções com base nos códigos de erro e mensagens.
		// Os códigos de erro e mensagem estão aqui: 
		// http://msdn.microsoft.com/pt-br/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Observe que o exemplo acima carrega um blob como um fluxo. No entanto, um blob também pode ser carregado como uma cadeia usando, por exemplo, a função [file\_get\_contents][file_get_contents] . Para fazer isso, altere content = fopen("c:\myfile.txt", "r");` no exemplo acima para `$content = file_get_contents("c:\myfile.txt");`.

<h2><a id="ListBlobs"></a>Como: Listar os blobs em um contêiner</h2>

Para listar os blobs em um contêiner, use o método **BlobRestProxy->listBlobs** com um loop **foreach** para executar um loop pelo resultado. O código a seguir exibe o nome de cada blob em um contêiner e seu URI para o navegador.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use o WindowsAzure\Common\ServiceException;

	// Criar blob proxy REST.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// Listar blobs.
		$blob_list = $blobRestProxy->listBlobs("mycontainer");
		$blobs = $blob_list->getBlobs();
		
		foreach($blobs as $blob)
		{
			echo $blob->getName().": ".$blob->getUrl()."<br />";
		}
	}
	catch(ServiceException $e){
		// Tratar exceções com base nos códigos de erro e mensagens.
		// Os códigos de erro e mensagem estão aqui: 
		// http://msdn.microsoft.com/pt-br/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}


<h2><a id="DownloadBlob"></a>Como: Baixar um blob</h2>

Para baixar um blob, chame o método **BlobRestProxy->getBlob** e, em seguida, chame o método **getContentStream** no objeto **GetBlobResult** resultante.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use o WindowsAzure\Common\ServiceException;

	// Criar blob proxy REST.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// Obter blob.
		$blob = $blobRestProxy->getBlob("mycontainer", "myblob");
		fpassthru($blob->getContentStream());
	}
	catch(ServiceException $e){
		// Tratar exceções com base nos códigos de erro e mensagens.
		// Os códigos de erro e mensagem estão aqui: 
		// http://msdn.microsoft.com/pt-br/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

Observe que o exemplo acima obtém um blob como um recurso de fluxo (o comportamento padrão). No entanto, você pode usar a função [stream\_get\_contents][stream-get-contents] para converter o fluxo retornado em uma cadeia de caracteres.

<h2><a id="DeleteBlob"></a>Como: Excluir um blob</h2>

Para excluir um blob, passe o nome do contêiner e o nome do blob para **BlobRestProxy -> deleteBlob**. 

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use o WindowsAzure\Common\ServiceException;

	// Criar blob proxy REST.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// Excluir contêiner.
		$blobRestProxy->deleteBlob("mycontainer", "myblob");
	}
	catch(ServiceException $e){
		// Tratar exceções com base nos códigos de erro e mensagens.
		// Os códigos de erro e mensagem estão aqui: 
		// http://msdn.microsoft.com/pt-br/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="DeleteContainer"></a>Como: Excluir um contêiner de blob</h2>

Finalmente, para excluir um contêiner de blob, passe o nome do contêiner para **BlobRestProxy->deleteContainer**.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use o WindowsAzure\Common\ServiceException;

	// Criar blob proxy REST.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// Excluir contêiner.
		$blobRestProxy->deleteContainer("mycontainer");
	}
	catch(ServiceException $e){
		// Tratar exceções com base nos códigos de erro e mensagens.
		// Os códigos de erro e mensagem estão aqui: 
		// http://msdn.microsoft.com/pt-br/library/windowsazure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

<h2><a id="NextSteps"></a>Próximas etapas</h2>

Agora que você aprendeu os conceitos básicos do serviço Blob do Azure, siga estes links para saber como executar tarefas de armazenamento mais complexas.

- Consulte a referência de MSDN: [Armazenando e acessando dados no Azure] []
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
