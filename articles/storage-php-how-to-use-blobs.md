<properties title="Como usar o armazenamento de blob (PHP) &mdash; guia de recursos do Azure" pageTitle="Como usar o armazenamento de blob (PHP) | Microsoft Azure" metaKeywords="Azure blob service PHP, Azure blobs PHP" description="Saiba como usar o servi&ccedil;o Blob do Azure para carregar, baixar, listar e excluir blobs. Os exemplos de c&oacute;digo s&atilde;o escritos em PHP." documentationCenter="PHP" services="storage" videoId="" scriptId="" solutions="" authors="robmcm" manager="adinah" editor="mollybos" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

# Como usar o serviço Blob do PHP

Este guia mostra como executar cenários comuns usando o serviço Blob do Azure. As amostras são escritas em PHP e usam o [SDK do Azure para PHP][SDK do Azure para PHP]. Os cenários cobertos incluem **carregamento**, **listagem**, **download** e **exclusão** de blobs. Para obter mais informações sobre blobs, consulte a seção [Próximas etapas][Próximas etapas].

## Sumário

-   [O que é Blob Storage][O que é Blob Storage]
-   [Conceitos][Conceitos]
-   [Criar uma conta de armazenamento do Azure][Criar uma conta de armazenamento do Azure]
-   [Criar um aplicativo PHP][Criar um aplicativo PHP]
-   [Configurar seu aplicativo para acessar o Serviço Blob][Configurar seu aplicativo para acessar o Serviço Blob]
-   [Configurar uma conexão de armazenamento do Azure][Configurar uma conexão de armazenamento do Azure]
-   [Como: Criar um contêiner][Como: Criar um contêiner]
-   [Como: Carregar um blob em um contêiner][Como: Carregar um blob em um contêiner]
-   [Como: Listar os blobs em um contêiner][Como: Listar os blobs em um contêiner]
-   [Como: Baixar um blob][Como: Baixar um blob]
-   [Como: Excluir um blob][Como: Excluir um blob]
-   [Como: Excluir um contêiner de blob][Como: Excluir um contêiner de blob]
-   [Próximas etapas][Próximas etapas]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <span id="CreateAccount"></span></a>Criar uma conta de armazenamento do Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <span id="CreateApplication"></span></a>Criar um aplicativo PHP

O único requisito para a criação de um aplicativo PHP que acessa o serviço Blob do Azure é a referência das classes no SDK do Azure para PHP de dentro de seu código. Você pode usar as ferramentas de desenvolvimento para criar seu aplicativo, incluindo o bloco de notas.

Neste guia, você usará os recursos de serviços que podem ser chamados em um aplicativo PHP localmente ou no código em execução dentro de uma função web do Azure, uma função de trabalho ou um site.

## <span id="GetClientLibrary"></span></a>Obter as bibliotecas de cliente do Azure

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

## <span id="ConfigureStorage"></span></a>Configurar seu aplicativo para acessar o Serviço Blob

Para usar as APIs do serviço Blob do Azure, você precisa:

1.  Fazer referência ao arquivo de carregador automático usando a instrução [require\_once][require\_once], e
2.  Fazer referência a qualquer classe que você possa usar.

O exemplo a seguir mostra como incluir o arquivo de carregador automático e fazer referência à classe **ServicesBuilder**.

> [WACOM.NOTE]
> Este exemplo (e outros exemplos deste artigo) pressupõe que você instalou as Bibliotecas de Cliente do PHP para Azure por meio do Compositor. Se você instalou as bibliotecas manualmente ou como um pacote PEAR, você precisará fazer referência ao arquivo de carregador automático `WindowsAzure.php`.

    require_once 'vendor\autoload.php';
    use WindowsAzure\Common\ServicesBuilder;

Nos exemplos abaixo, a instrução '`require_once`' será mostrada sempre, mas somente as classes necessárias para executar o exemplo serão referenciadas.

## <span id="ConnectionString"></span></a>Configurar uma conexão de armazenamento do Azure

Para criar uma instância de um cliente de serviço Blob do Azure, você deve primeiramente ter uma cadeia de conexão válida. O formato da cadeia de conexão do serviço blob é:

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

    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

## <span id="CreateContainer"></span></a>Como: Criar um contêiner

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

    try {
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

A chamada a **setPublicAccess(PublicAccessType::CONTAINER\_AND\_BLOBS)** faz com que os dados do contêiner e do blob fiquem acessíveis por meio de solicitações anônimas. A chamada a **setPublicAccess(PublicAccessType::BLOBS\_ONLY)** faz com que apenas os dados do blob fiquem acessíveis por meio de solicitações anônimas. Para obter mais informações sobre ACLs de contêiner, consulte [Configurar ACL do Contêiner (REST API)][Configurar ACL do Contêiner (REST API)].

Para obter mais informações sobre códigos de erro de serviço Blob, consulte [Códigos de erro de serviço Blob][Códigos de erro de serviço Blob].

## <span id="UploadBlob"></span></a>Como: Carregar um blob em um contêiner

Para carregar um arquivo como um blob, use o método **BlobRestProxy-\>createBlockBlob**. Essa operação criará o blob, se ele não existir, ou o substituirá, se ele já existir. O exemplo de código a seguir pressupõe que o contêiner já foi criado e usa [fopen][fopen] para abrir o arquivo como um fluxo.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    $content = fopen("c:\myfile.txt", "r");
    $blob_name = "myblob";

    try {
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

Observe que o exemplo acima carrega um blob como um fluxo. No entanto, um blob também pode ser carregado como uma cadeia usando, por exemplo, a função [file\_get\_contents][file\_get\_contents]. Para isso, modifique `$content = fopen("c:\myfile.txt", "r");` no exemplo acima para `$content = file_get_contents("c:\myfile.txt");`.

## <span id="ListBlobs"></span></a>Como: Listar os blobs em um contêiner

Para listar os blobs em um contêiner, use o método **BlobRestProxy-\>listBlobs** com um loop **foreach** para executar um loop pelo resultado. O código a seguir exibe o nome de cada blob em um contêiner e seu URI para o navegador.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
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

## <span id="DownloadBlob"></span></a>Como: Baixar um blob

Para baixar um blob, chame o método **BlobRestProxy-\>getBlob** e, em seguida, chame o método **getContentStream** no objeto **GetBlobResult** resultante.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
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

Observe que o exemplo acima obtém um blob como um recurso de fluxo (o comportamento padrão). No entanto, você pode usar a função [stream\_get\_contents][stream\_get\_contents] para converter o fluxo de retorno em uma cadeia.

## <span id="DeleteBlob"></span></a>Como: Excluir um blob

Para excluir um blob, passe o nome do contêiner e o nome do blob para **BlobRestProxy -\> deleteBlob**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
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

## <span id="DeleteContainer"></span></a>Como: Excluir um contêiner de blob

Finalmente, para excluir um contêiner de blob, passe o nome do contêiner para **BlobRestProxy-\>deleteContainer**.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;

    // Create blob REST proxy.
    $blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


    try {
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

## <span id="NextSteps"></span></a>Próximas etapas

Agora que você aprendeu os conceitos básicos do serviço Blob do Azure, siga estes links para saber como executar tarefas de armazenamento mais complexas.

-   Consulte a referência de MSDN: [Armazenando e acessando dados no Azure][Armazenando e acessando dados no Azure]
-   Visite o Blog da Equipe de Armazenamento do Azure: <http://blogs.msdn.com/b/windowsazurestorage/>
-   Consulte o exemplo de blob de bloco PHP em <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php>.
-   Consulte o exemplo de blob de página PHP em <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php>

  [SDK do Azure para PHP]: http://go.microsoft.com/fwlink/?LinkID=252473
  [Próximas etapas]: #NextSteps
  [O que é Blob Storage]: #what-is
  [Conceitos]: #concepts
  [Criar uma conta de armazenamento do Azure]: #CreateAccount
  [Criar um aplicativo PHP]: #CreateApplication
  [Configurar seu aplicativo para acessar o Serviço Blob]: #ConfigureStorage
  [Configurar uma conexão de armazenamento do Azure]: #ConnectionString
  [Como: Criar um contêiner]: #CreateContainer
  [Como: Carregar um blob em um contêiner]: #UploadBlob
  [Como: Listar os blobs em um contêiner]: #ListBlobs
  [Como: Baixar um blob]: #DownloadBlob
  [Como: Excluir um blob]: #DeleteBlob
  [Como: Excluir um contêiner de blob]: #DeleteContainer
  [require\_once]: http://php.net/require_once
  [Configurar ACL do Contêiner (REST API)]: http://msdn.microsoft.com/pt-br/library/windowsazure/dd179391.aspx
  [Códigos de erro de serviço Blob]: http://msdn.microsoft.com/pt-br/library/windowsazure/dd179439.aspx
  [fopen]: http://www.php.net/fopen
  [file\_get\_contents]: http://php.net/file_get_contents
  [stream\_get\_contents]: http://www.php.net/stream_get_contents
  [Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx
