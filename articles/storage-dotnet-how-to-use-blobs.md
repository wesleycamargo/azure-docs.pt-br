<properties linkid="dev-net-how-to-blob-storage" urlDisplayName="Blob Service" pageTitle="How to use blob storage from .NET | Azure" metaKeywords="Get started Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure blob .NET   Azure blob C#   Azure blob C#" description="Learn how to use Microsoft Azure Blob storage to upload,  download, list, and delete blob content. Samples are written in C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Blob storage in .NET" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Como usar o Armazenamento de Blob no .NET

Este guia demonstra como executar cenários comuns usando o
Serviço de armazenamento de blobs do Azure. Os exemplos são escritos em C# e
usam a biblioteca do cliente de armazenamento do Azure para .NET. Os cenários cobertos incluem
**carregamento**, **listagem**, **download** e **exclusão** de blobs. Para
obter mais informações sobre blobs, consulte a seção [Próximas etapas][Próximas etapas].

> [WACOM.NOTE] Este guia tem como alvo a Biblioteca do Cliente de Armazenamento .NET do Azure, versão 2.x e superior. A versão recomendada é a Biblioteca do Cliente de Armazenamento 4.x que está disponível via [NuGet][NuGet] ou como parte do [SDK do Azure para .NET][SDK do Azure para .NET]. Consulte [Como: Acessar programaticamente o armazenamento de blobs][Como: Acessar programaticamente o armazenamento de blobs] abaixo para mais detalhes sobre como obter a biblioteca do cliente de armazenamento.

## Sumário

-   [O que é Blob Storage][O que é Blob Storage]
-   [Conceitos][Conceitos]
-   [Criar uma conta de Armazenamento do Azure][Criar uma conta de Armazenamento do Azure]
-   [Configurar uma cadeia de conexão de armazenamento][Configurar uma cadeia de conexão de armazenamento]
-   [Como: Acessar programaticamente o armazenamento de blobs][Como: Acessar programaticamente o armazenamento de blobs]
-   [Como: Criar um contêiner][Como: Criar um contêiner]
-   [Como: Carregar um blob em um contêiner][Como: Carregar um blob em um contêiner]
-   [Como: Listar os blobs em um contêiner][Como: Listar os blobs em um contêiner]
-   [Como: Baixar blobs][Como: Baixar blobs]
-   [Como: Excluir blobs][Como: Excluir blobs]
-   [Próximas etapas][Próximas etapas]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <a name="create-account"></a><span class="short-header">Criar uma conta</span>Criar uma conta de armazenamento do Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="setup-connection-string"></a><span class="short-header">Configurar uma cadeia de conexão</span>Configurar uma cadeia de conexão de armazenamento

[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a><span class="short-header">Acessar programaticamente</span>Como: Acessar programaticamente o armazenamento de blobs

### Obtendo o assembly

Você pode usar NuGet para obter o assembly `Microsoft.WindowsAzure.Storage.dll`. Clique com o botão direito do mouse no **Gerenciador de Soluções** e escolha **Gerenciar Pacotes NuGet**. Pesquise online por "WindowsAzure.Storage" e clique em **Instalar** para instalar o pacote Armazenamento do Azure e as dependências.

`Microsoft.WindowsAzure.Storage.dll` também está incluído no SDK do Azure para .NET, que pode ser baixado na [Central de desenvolvedores do .NET][Central de desenvolvedores do .NET]. O assembly está instalado no diretório `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`lt;sdk-version\>\\ref\\</code>.

### Declarações de namespace

Adicione as seguintes declarações de namespace à parte superior de qualquer arquivo C\\
no qual você deseja acessar o armazenamento do Azure programaticamente:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;

Certifique-se de fazer referência ao assembly `Microsoft.WindowsAzure.Storage.dll`.

### Recuperando sua cadeia de conexão

Você pode usar o tipo **CloudStorageAccount** para representar
as informações de sua Conta de Armazenamento. Se você estiver usando um
modelo de projeto do Azure e/ou tiver uma referência ao
Microsoft.WindowsAzure.CloudConfigurationManager,
poderá usar o tipo **CloudConfigurationManager** para
recuperar a cadeia de conexão do armazenamento e as informações
de conta da configuração de serviços do Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Se estiver criando um aplicativo sem nenhuma referência ao Microsoft.WindowsAzure.CloudConfigurationManager, e sua cadeia de conexão estiver localizada no `web.config` ou `app.config`, como mostrado acima, você poderá usar o **ConfigurationManager** para recuperar a cadeia de conexão. Você precisará adicionar uma referência ao System.Configuration.dll a seu projeto e adicionar outra declaração de namespace para ele:

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

Um tipo **CloudBlobClient** permite que você recupere objetos que representam
contêineres e blobs armazenados no Serviço de Armazenamento de Blobs. O
código a seguir cria um objeto **CloudBlobClient** usando o objeto da conta de armazenamento
que recuperamos acima:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

### Dependências do ODataLib

As dependências do ODataLib na Biblioteca de Cliente de Armazenamento para .NET são resolvidas por meio de pacotes do ODataLib (versão 5.0.2) disponíveis por meio do NuGet e não do WCF Data Services. As bibliotecas do ODataLib podem ser baixadas diretamente ou referenciadas por seu projeto de código por meio do NuGet. Os pacotes ODataLib específicos são [OData][OData], [Edm][Edm] e [Spatial][Spatial].

## <a name="create-container"> </a><span class="short-header">Criar um contêiner</span>Como: Criar um contêiner

Todos os blobs de armazenamento residem em um contêiner. Você pode usar um objeto
**CloudBlobClient** para obter uma referência ao contêiner que deseja
usar. Você pode criar o contêiner se ele não existir:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

Por padrão, o novo contêiner é particular e você deve especificar sua
chave de acesso de armazenamento para baixar os blobs desse
contêiner. Para disponibilizar arquivos dentro do contêiner a
todas as pessoas, você pode definir o contêiner como público usando o seguinte
código:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
        BlobContainerPublicAccessType.Blob }); 

Qualquer pessoa na internet pode ver blobs em um contêiner público, mas você só pode
modificar ou excluí-los se tiver a chave de acesso apropriada.

## <a name="upload-blob"> </a><span class="short-header">Carregar um contêiner</span>Como: Carregar um blob em um contêiner

O Armazenamento de Blob do Azure oferece suporte a blobs de blocos e a blobs de páginas. Na maioria dos casos, o blob de blocos é o tipo recomendado a ser usado.

Para carregar um arquivo em um blob de blocos, obtenha uma referência de contêiner e use-a para obter
uma referência de blob de blocos. Depois de ter uma referência do blob, você pode carregar qualquer
fluxo de dados nele chamando o método **UploadFromStream**. Essa operação criará o blob, caso ele não exista,
ou o substituirá, caso ele já exista. O exemplo a seguir mostra como carregar um blob em um contêiner e pressupõe que o contêiner já tenha sido criado.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    } 

## <a name="list-blob"> </a><span class="short-header">Listar blobs em um contêiner</span>Como: Listar os blobs em um contêiner

Para listar blobs em um contêiner, primeiro obtenha uma referência ao contêiner. Você
pode usar o método **ListBlobs** do contêiner para recuperar os blobs e/ou diretórios
dentro dele. Para acessar o conjunto avançado de propriedades e métodos de um
**IListBlobItem** retornado, você deve convertê-lo em um objeto **CloudBlockBlob**,
**CloudPageBlob**, ou **CloudBlobDirectory**. Se o tipo for desconhecido, você poderá usar uma
verificação de tipo para determinar em qual convertê-lo. O código a seguir
demonstra como recuperar e apresentar a saída do URI de cada item
no contêiner `photos`:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client. 
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("photos");

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;

            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
                                        
        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;
            
            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }

Como mostrado acima, o serviço Blob também tem o conceito de diretórios dentro de
contêineres. Isso é para que você possa organizar seus blobs em uma estrutura
mais semelhante a uma pasta. Por exemplo, considere o seguinte conjunto de blobs de blocos em um contêiner
chamado `photos`:

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

Quando você chama **ListBlobs** no contêiner 'photos' (como no exemplo acima), a coleção retornada
conterá os objetos **CloudBlobDirectory** e **CloudBlockBlob**
que representam os diretórios e os blobs contidos no nível superior. Esta seria a saída resultante:

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Opcionalmente, você pode definir o parâmetro **UseFlatBlobListing** do método **ListBlobs** como
**true**. Isso resultaria em cada blob sendo retornado como um **CloudBlockBlob**
,independentemente do diretório. Esta seria a chamada para **ListBlobs**:

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

e estes seriam os resultados:

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Para obter mais informações, consulte [CloudBlobContainer.ListBlobs][CloudBlobContainer.ListBlobs].

## <a name="download-blobs"> </a>Baixar blobs<span class="short-header"></span>Como: Baixar blobs

Para baixar blobs, primeiro recupere uma referência a um blob e, em seguida, chame o método **DownloadToStream**. O exemplo a
seguir usa o método **DownloadToStream** para transferir o conteúdo
do blob para um objeto de fluxo que você pode persistir em um arquivo local.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    } 

Você também pode usar o método **DownloadToStream** para baixar o conteúdo de um blob como uma cadeia de texto.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## <a name="delete-blobs"> </a><span class="short-header">Excluir blobs</span>Como: Excluir blobs

Para excluir um blob, primeiro obtenha uma referência de blob e, em seguida, chame o método
**Delete**.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete(); 

## <a name="next-steps"></a> <span class="short-header">Próximas etapas</span>Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de blob, siga estes links
para saber como executar tarefas de armazenamento mais complexas.

-   Consulte a documentação de referência do serviço Blob para obter detalhes completos sobre as APIs disponíveis:
    -   [Referência à Biblioteca de Cliente de Armazenamento para .NET][Referência à Biblioteca de Cliente de Armazenamento para .NET]
    -   [Referência da API REST][Referência da API REST]
-   Conheça as tarefas mais avançadas que você pode executar com o Armazenamento do Azure em [Armazenando e acessando dados no Azure][Armazenando e acessando dados no Azure].
-   Aprenda a trabalhar com o Armazenamento do Azure em processos de back-end para sites do Azure em [Introdução ao SDK de WebJobs do Azure][Introdução ao SDK de WebJobs do Azure].
-   Consulte outros guias de recursos para obter informações sobre opções adicionais para armazenar dados no Azure.
    -   Usar o [Armazenamento de Tabela][Armazenamento de Tabela] para armazenar dados estruturados.
    -   Usar o [Armazenamento de blobs][Armazenamento de blobs] para armazenar dados não estruturados.
    -   Usar o [Banco de Dados SQL][Banco de Dados SQL] para armazenar dados relacionais.

</p>

  [Próximas etapas]: #next-steps
  [NuGet]: https://www.nuget.org/packages/WindowsAzure.Storage/
  [SDK do Azure para .NET]: /pt-br/downloads/
  [Como: Acessar programaticamente o armazenamento de blobs]: #configure-access
  [O que é Blob Storage]: #what-is
  [Conceitos]: #concepts
  [Criar uma conta de Armazenamento do Azure]: #create-account
  [Configurar uma cadeia de conexão de armazenamento]: #setup-connection-string
  [Como: Criar um contêiner]: #create-container
  [Como: Carregar um blob em um contêiner]: #upload-blob
  [Como: Listar os blobs em um contêiner]: #list-blob
  [Como: Baixar blobs]: #download-blobs
  [Como: Excluir blobs]: #delete-blobs
  [howto-blob-storage]: ../includes/howto-blob-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [storage-configure-connection-string]: ../includes/storage-configure-connection-string.md
  [Central de desenvolvedores do .NET]: http://www.windowsazure.com/pt-br/develop/net/#
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
  [Referência à Biblioteca de Cliente de Armazenamento para .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Referência da API REST]: http://msdn.microsoft.com/pt-br/library/windowsazure/dd179355
  [Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx
  [Introdução ao SDK de WebJobs do Azure]: /pt-br/documentation/articles/websites-dotnet-webjobs-sdk-get-started/
  [Armazenamento de Tabela]: /pt-br/documentation/articles/storage-dotnet-how-to-use-tables/
  [Armazenamento de blobs]: /pt-br/documentation/articles/storage-dotnet-how-to-use-queues/
  [Banco de Dados SQL]: /pt-br/documentation/articles/sql-database-dotnet-how-to-use/
