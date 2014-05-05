<properties linkid="dev-net-how-to-use-blog-storage-service-java" urlDisplayName="Serviço de blobs" pageTitle="Como usar o armazenamento de blobs (Java) | Microsoft Azure" metaKeywords="Introdução aos blobs no Azure, Dados não estruturados no Azure, Armazenamento não estruturado no Azure, Blobs no Azure, Armazenamento de blobs no Azure, Java para blobs no Azure" description="Saiba como usar o serviços de blobs no Azure para carregar, baixar, listar e excluir o conteúdo de blobs. Amostras escritas em Java." metaCanonical="" services="storage" documentationCenter="Java" title="Como usar o Armazenamento de blobs do Java" authors="" solutions="" manager="" editor="" />




# Como usar o Armazenamento de blobs do Java

Este guia mostra como realizar cenários comuns usando o serviço de Armazenamento de blobs do Azure. As amostras são escritas em Java e usam o [SDK do Azure para Java][]. Os cenários cobertos incluem **carregamento**, **listagem**, **download** e **exclusão** de blobs. Para obter mais informações sobre blobs, consulte a seção [Próximas etapas](#NextSteps).

## <a name="Contents"> </a>Sumário

* [O que é Blob Storage](#what-is)
* [Conceitos](#Concepts)
* [Criar uma conta de armazenamento do Azure](#CreateAccount)
* [Criar um aplicativo do Java](#CreateApplication)
* [Configurar seu aplicativo para acessar o Armazenamento de blobs](#ConfigureStorage)
* [Configurar uma cadeia de conexão de armazenamento do Azure](#ConnectionString)
* [Como criar um contêiner](#CreateContainer)
* [Como carregar um blob em um contêiner](#UploadBlob)
* [Como listar os blobs em um contêiner](#ListBlobs)
* [Como baixar um blob](#DownloadBlob)
* [Como excluir um blob](#DeleteBlob)
* [Como excluir um contêiner de blob](#DeleteContainer)
* [Próximas etapas](#NextSteps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

<h2><a id="CreateAccount"></a>Criar uma conta de armazenamento do Azure</h2>

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="CreateApplication"> </a>Criar um aplicativo do Java

Neste guia, você usará os recursos de armazenamento que podem ser executados em um aplicativo do Java localmente ou no código em execução em uma função web ou função de trabalho do Azure. Supomos que você baixou e instalou o Java Development Kit (JDK), seguiu as instruções em [Baixe o SDK do Azure para Java][Azure SDK for Java] para instalar as Bibliotecas do Azure para Java e o SDK do Azure e criou uma conta de armazenamento do Azure em sua assinatura do Azure.

É possível usar as ferramentas de desenvolvimento para criar seu aplicativo, incluindo o Bloco de Notas. Tudo o que você precisa é a capacidade de compilar um projeto do Java e fazer a referência das bibliotecas do Azure para o Java.

## <a name="ConfigureStorage"> </a>Configurar seu aplicativo para acessar o Armazenamento de blobs

Adicione as seguintes instruções de importação na parte superior do arquivo do Java em que deseje usar as APIs de armazenamento do Azure para acessar os blobs:

    // Include the following imports to use blob APIs
    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.blob.client.*;

## <a name="ConnectionString"> </a>Configurar uma cadeia de conexão de armazenamento do Azure

Um cliente de armazenamento do Azure usa uma cadeia de conexão para armazenar pontos de extremidade e credenciais para acesso aos serviços de gerenciamento de dados. Ao ser executado em um aplicativo cliente, é necessário fornecer a cadeia de conexão de armazenamento no formato a seguir, usando o nome da sua conta de armazenamento e a chave de Acesso primário da conta de armazenamento listada no Portal de Gerenciamento para os valores *AccountName* e *AccountKey*. Este exemplo mostra como você pode declarar um campo estático para armazenar a cadeia de conexão:

    // Define the connection-string with your values
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

Em um aplicativo que esteja sendo executado em uma função no Azure, essa cadeia pode ser armazenada no arquivo de configuração do serviço, ServiceConfiguration.cscfg, e pode ser acessada com uma chamada para o método **RoleEnvironment.getConfigurationSettings**. Segue um exemplo de como obter a cadeia de conexão de um elemento **Setting** denominado *StorageConnectionString* no arquivo de configuração do serviço:

    // Retrieve storage account from connection-string
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

## <a name="CreateContainer"> </a>Como criar um contêiner

Um objeto CloudBlobClient permite que você obtenha os objetos de referência para os contêineres e blobs. O código a seguir cria um objeto **CloudBlobClient**.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

Todos os blobs residem em um contêiner. Use the **CloudBlobClient** para obter uma referência ao contêiner que deseja usar. Se o contêiner não existir, é possível criá-lo com o método **createIfNotExist**; caso contrário, ele retornará o contêiner existente. Por padrão, o novo contêiner é particular; portanto, você deve especificar sua
chave de acesso de armazenamento (como anteriormente) para baixar blobs desse
contêiner.

    // Get a reference to a container
    // The container name must be lower case
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Create the container if it does not exist
    container.createIfNotExist();

Se desejar tornar os arquivos públicos, você pode definir as permissões do contêiner.

    // Create a permissions object
    BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

    // Include public access in the permissions object
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

    // Set the permissions on the container
    container.uploadPermissions(containerPermissions);

Qualquer pessoa com acesso à Internet pode visualizar os blobs em um contêiner público, mas o acesso público é limitado a somente leitura.

## <a name="UploadBlob"> </a>Como carregar um blob em um contêiner

Para carregar um arquivo para um blob, obtenha uma referência de contêiner e use-a para obter
uma referência de blob. Depois de obter uma referência do blob, é possível carregar qualquer fluxo chamando o upload na referência do blob. Essa operação criará o blob, caso ele não exista, ou o substituirá, caso ele já exista. Esta amostra de código abaixo mostra isso e pressupõe
que o contêiner já tenha sido criado.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Create or overwrite the "myimage.jpg" blob with contents from a local file
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");
    File source = new File("c:\\myimages\\myimage.jpg");
    blob.upload(new FileInputStream(source), source.length());

## <a name="ListBlobs"> </a>Como listar os blobs em um contêiner

Para listar os blobs em um contêiner, primeiro obtenha uma referência como a que você obteve para carregar o blob. É possível usar o método **listBlobs**
do contêiner com um loop. O
código a seguir gera a saída do Uri de cada blob em um contêiner para o
console.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Loop over blobs within the container and output the URI to each of them
    for (ListBlobItem blobItem : container.listBlobs()) {
        System.out.println(blobItem.getUri());
    }

O serviço de blob também tem o conceito de diretórios dentro de
contêineres. Isso é para que você possa organizar seus blobs em uma estrutura mais semelhante a uma pasta.

Por exemplo, você poderia ter um contêiner denominado "fotos", no
qual você poderia carregar os blobs denominados "rootphoto1", "2010/photo1", "2010/photo2" e "2011/photo1". Isso criaria os
diretórios virtuais "2010" e "2011" no contêiner "fotos". Ao chamar **listBlobs** no contêiner "fotos", o conjunto retornou conterá
os objetos **CloudBlobDirectory** e **CloudBlob**, que representam os diretórios e blobs contidos no nível superior. Nesse
caso, os diretórios "2010" and "2011", bem como "rootphoto1",
seriam retornados. É possível usar o operador **instanceof** para distinguir esses objetos.

Como alternativa, você pode transmitir parâmetros para o método **listBlobs** com o parâmetro **useFlatBlobListing** definido como true. Isso resultará no retorno de cada blob, independentemente do
diretório. Para obter mais informações, consulte CloudBlobContainer.listBlobs na documentação do Javadocs.

## <a name="DownloadBlob"> </a>Como baixar um blob

Para baixar blobs, siga as mesmas etapas utilizadas para carregar um blob, a fim de obter uma referência do blob. No exemplo de carregamento, o carregamento no objeto do blob foi chamado. No exemplo a seguir, chame o download para transferir o conteúdo do blob para um objeto do fluxo, como um **FileOutputStream** que pode ser usado para persistir o blob ao arquivo local.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // For each item in the container
    for (ListBlobItem blobItem : container.listBlobs()) {
        // If the item is a blob, not a virtual directory
        if (blobItem instanceof CloudBlob) {
            // Download the item and save it to a file with the same name
            CloudBlob blob = (CloudBlob) blobItem;
            blob.download(new FileOutputStream(blob.getName()));
        }
    }

## <a name="DeleteBlob"> </a>Como excluir um blob

Para excluir um blob, obtenha uma referência do blob e chame **delete**.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Retrieve reference to a blob named "myimage.jpg"
    CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");

    // Delete the blob
    blob.delete();

## <a name="DeleteContainer"> </a>Como excluir um contêiner de blob

Por fim, para excluir um contêiner de blob, obtenha uma referência deo blob e, em seguida, chame delete.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the blob client
    CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    // Retrieve reference to a previously created container
    CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

    // Delete the blob container
    container.delete();

## <a name="NextSteps"> </a>Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de blob, siga estes links para saber como executar tarefas de armazenamento mais complexas.

-   Consulte a Referência do MSDN: [Armazenando e acessando dados no Windows
    Azure]
-   Acesse o Blog da Equipe de Armazenamento do Azure <http://blogs.msdn.com/b/windowsazurestorage/>


[SDK do Azure para Java]: http://www.windowsazure.com/pt-br/develop/java/
[Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx

