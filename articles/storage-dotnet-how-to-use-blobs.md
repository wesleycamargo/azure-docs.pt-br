<properties urlDisplayName="Blob Service" pageTitle="Como usar o armazenamento de blob do .NET | Azure" metaKeywords="Get started Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure blob .NET   Azure blob C#   Azure blob C#" description="Learn how to use Microsoft Azure Blob storage to upload,  download, list, and delete blob content. Samples are written in C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Blob storage in .NET" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/10/2014" ms.author="tamram" />

# Como usar o Armazenamento de Blob no .NET

Este guia demonstra como executar cenários comuns usando o
Serviço de armazenamento de Blob do Azure. Os exemplos são escritos em C\# e
usam a Biblioteca do Cliente de Armazenamento do Azure para .NET. Os cenários abordados incluem
**carregamento, ****listagem, ****download** e **exclusão** de blobs.Por
para obter mais informações sobre blobs, consulte a seção [Próximas etapas][].

> [WACOM.NOTE] Este guia tem como alvo a Biblioteca do Cliente de Armazenamento .NET do Azure, versão 2.x e superior. A versão recomendada é a Biblioteca de Cliente de Armazenamento 4.x, que está disponível via [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) ou como parte do [SDK do Azure para .NET](/pt-br/downloads/). Consulte [Como: Acessar programaticamente o armazenamento de blobs][] abaixo para mais detalhes sobre como obter a biblioteca do cliente de armazenamento.

##Sumário

-   [O que é Armazenamento de Blob][]
-   [Conceitos][]
-   [Criar uma conta de Armazenamento do Azure][]
-   [Configurar uma cadeia de conexão de armazenamento][]
-   [Como: Acessar programaticamente o armazenamento de blobs][]
-   [Como: Criar um contêiner][]
-   [Como: Carregar um blob em um contêiner][]
-   [Como: Listar os blobs em um contêiner][]
-   [Como: Baixar blobs][]
-   [Como: Excluir blobs][]
-   [Como: Listar blobs em páginas de maneira assíncrona][]
-   [Próximas etapas][]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

##<a name="create-account"></a>Criar uma conta de Armazenamento do Azure
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

##<a name="setup-connection-string"></a>Configurar uma cadeia de conexão de armazenamento

[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a>Como: Acessar programaticamente o armazenamento de blobs

###Obtendo o assembly
Recomendamos que você use o NuGet para obter o assembly `Microsoft.WindowsAzure.Storage.dll`. Clique com o botão direito do mouse no **Gerenciador de Soluções** e escolha **Gerenciar Pacotes NuGet**.  Pesquise online por "MicrosoftAzure.Storage" e clique em **Instalar** para instalar o pacote Armazenamento do Azure e as dependências.

'Microsoft.WindowsAzure.Storage.dll' também está incluído no SDK do Azure para .NET, que pode ser baixado na <a href="http://www.windowsazure.com/pt-br/develop/net/#">Central de desenvolvedores do .NET</a>. O assembly é instalado no diretório `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`.

###Declarações de namespace
Adicione as seguintes declarações de namespace à parte superior de qualquer arquivo C\#
em que você deseja acessar o armazenamento do Azure programaticamente:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;

Faça referência ao assembly `Microsoft.WindowsAzure.Storage.dll`.

###Recuperando sua cadeia de conexão
Você pode usar o tipo **CloudStorageAccount** para representar 
as informações da conta de armazenamento. Se estiver usando um 
modelo de projeto do Azure e/ou tiver uma referência para 
Microsoft.WindowsAzure.CloudConfigurationManager, você 
você pode usar o tipo **CloudConfigurationManager**
para recuperar a cadeia de conexão de armazenamento e a conta de armazenamento
informações da configuração do serviço do Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Se estiver criando um aplicativo sem referência a Microsoft.WindowsAzure.CloudConfigurationManager e a cadeia de conexão estiver localizada no web.config ou no app.config conforme mostrado acima, você poderá usar **ConfigurationManager** para recuperar a cadeia de conexão.Você precisará adicionar uma referência ao System.Configuration.dll a seu projeto e adicionar outra declaração de namespace para ele:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

Um tipo **CloudBlobClient** permite que você recupere objetos que representam
contêineres e blobs armazenados no serviço de armazenamento de Blob. O
código a seguir cria um objeto **CloudBlobClient** usando o objeto
da conta de armazenamento que recuperamos acima:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

###Dependências do ODataLib
As dependências do ODataLib na Biblioteca de Cliente de Armazenamento para .NET são resolvidas por meio de pacotes do ODataLib (versão 5.0.2) disponíveis por meio do NuGet e não do WCF Data Services.  As bibliotecas do ODataLib podem ser baixadas diretamente ou referenciadas por seu projeto de código por meio do NuGet.  Os pacotes ODataLib específicos são [OData], [Edm] e [Spatial].

## <a name="create-container"> </a>Como: Criar um contêiner

Todos os blobs no armazenamento do Azure devem residir em um contêiner. Este exemplo mostra como criar um contêiner se ele ainda não existir:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

Por padrão, o novo contêiner é privado e você deve especificar sua chave de acesso de armazenamento para baixar blobs desse contêiner. Para disponibilizar arquivos dentro do contêiner a todas as pessoas, você pode definir o contêiner como público usando o seguinte código:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
 	    BlobContainerPublicAccessType.Blob }); 

Qualquer pessoa na Internet pode ver blobs em um contêiner público, mas você só pode modificar ou excluí-los se tiver a chave de acesso apropriada.

## <a name="upload-blob"> </a>Como: Carregar um blob em um contêiner

O Armazenamento de Blob do Azure dá suporte a blobs de blocos e a blobs de páginas.  Na maioria dos casos, o blob de blocos é o tipo recomendado a ser usado.

Para carregar um arquivo em um blob de blocos, obtenha uma referência de contêiner e use-a para obter uma referência de blob de blocos.  Depois de ter uma referência do blob, você pode carregar qualquer fluxo de dados nele chamando o método **UploadFromStream**.Essa operação criará o blob, caso ele não exista, ou o substituirá, caso ele já exista.O exemplo a seguir mostra como carregar um blob em um contêiner e pressupõe que o contêiner já tenha sido criado.

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

##<a name="list-blob"> </a>Como: Listar os blobs em um contêiner

Para listar blobs em um contêiner, primeiro obtenha uma referência ao contêiner.   Você pode usar o método **ListBlobs** do contêiner para recuperar os blobs e/ou diretórios dentro dele.  Para acessar o conjunto avançado de propriedades e métodos de um**IListBlobItem** retornado, você deve convertê-lo em um objeto **CloudBlockBlob, ****CloudPageBlob**, ou **CloudBlobDirectory**.Se o tipo for desconhecido, você poderá usar uma verificação de tipo para determinar em qual convertê-lo.O código a seguir demonstra como recuperar e apresentar a saída do URI de cada item no 
contêiner 'fotos':

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

Como mostrado acima, o serviço Blob também tem o conceito de diretórios dentro de contêineres. Isso é para que você possa organizar seus blobs em uma estrutura mais semelhante a uma pasta. Por exemplo, considere o seguinte conjunto de blobs de blocos em um contêiner chamado 'fotos':

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

Ao chamar **ListBlobs** no contêiner 'fotos' (como na amostra acima), a coleção retornada conterá os objetos **CloudBlobDirectory** e **CloudBlockBlob**
representando os diretórios e os blobs contidos no nível superior. Esta seria a saída resultante:

	Diretório: https://<accountname>.blob.core.windows.net/photos/2010/
	Diretório: https://<accountname>.blob.core.windows.net/photos/2011/
	Blob de blocos de tamanho 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Opcionalmente, você pode definir o parâmetro **UseFlatBlobListing** do método **ListBlobs** como**true**. Isso resultaria em cada blob sendo retornado como um **CloudBlockBlob**, independentemente do diretório.  Esta seria a chamada para **ListBlobs**:

    // Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

e estes seriam os resultados:

	Blob de blocos de tamanho 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Blob de blocos de tamanho 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Blob de blocos de tamanho 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Blob de blocos de tamanho 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Blob de blocos de tamanho 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Blob de blocos de tamanho 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Blob de blocos de tamanho 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Blob de blocos de tamanho 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Para obter mais informações, consulte [CloudBlobContainer.ListBlobs][].

## <a name="download-blobs"> </a>Como: Baixar blobs

Para baixar blobs, primeiro recupere uma referência a um blob e, em seguida, chame o método **DownloadToStream**.O seguinte
exemplo usa o método **DownloadToStream** para transferir o conteúdo
do blob para um objeto de fluxo que você pode persistir para um arquivo local.

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

##<a name="delete-blobs"> </a>Como: Excluir blobs

Para excluir um blob, primeiro obtenha uma referência a um blob e, em seguida, chame o
Método **Delete** sobre ele.

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


##<a name="list-blobs-async"> </a>Como: Listar blobs em páginas de maneira assíncrona

Se você está listando uma grande quantidade de blobs ou se deseja controlar o número de resultados retornados em uma operação de listagem, pode listar os blobs em páginas de resultados. Este exemplo mostra como retornar resultados em páginas de forma assíncrona, para que a execução não fique bloqueada enquanto espera para retornar um grande conjunto de resultados.

Este exemplo mostra uma listagem de blob simples, mas você também pode realizar uma listagem hierárquica, configurando o parâmetro useFlatBlobListing do método **ListBlobsSegmentedAsync** como 'false'.

Como o método de amostra chama um método assíncrono, ele deve ser precedido pela palavra-chave async e deve retornar um objeto **Task**. A palavra-chave await especificada para o método **ListBlobsSegmentedAsync** suspende a execução do método de amostra até que a tarefa de listagem seja concluída.

    async public static Task ListBlobsSegmentedInFlatListing()
    {
        // Retrieve storage account from connection string.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
            CloudConfigurationManager.GetSetting("StorageConnectionString"));

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Retrieve reference to a previously created container.
        CloudBlobContainer container = blobClient.GetContainerReference("myblobs");

        //List blobs in pages.
        Console.WriteLine("List blobs in pages:");

        //List blobs with a paging size of 10, for the purposes of the example. 
		//The first call does not include the continuation token.
        BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(
                "", true, BlobListingDetails.All, 10, null, null, null);

        //Enumerate the result segment returned.
        int i = 0;
        if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
        foreach (var blobItem in resultSegment.Results)
        {
            Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
        }
        Console.WriteLine();

        //Get the continuation token, if there are additional pages of results.
        BlobContinuationToken continuationToken = resultSegment.ContinuationToken;

        //Check whether there are more results and list them in pages of 10 while a continuation token is returned.
        while (continuationToken != null)
        {
            //This overload allows control of the page size. 
			//You can return all remaining results by passing null for the maxResults parameter, 
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync(
					"", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the next continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
    }

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de blob, siga estes links para saber como executar tarefas de armazenamento mais complexas.
<ul>
<li>Consulte a documentação de referência do serviço Blob para obter detalhes completos sobre as APIs disponíveis:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Referência à Biblioteca de Cliente de Armazenamento para .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/pt-br/library/windowsazure/dd179355">Referência da API REST</a></li>
  </ul>
</li>
<li>Conheça tarefas mais avançadas, que você pode executar com o armazenamento do Azure <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx">Armazenando e acessando dados no Azure</a>.</li>
<li>Aprenda a trabalhar com o armazenamento do Azure nos processos de back-end para os sites do Azure em <a href="/pt-br/documentation/articles/websites-dotnet-webjobs-sdk-get-started/">Introdução ao SDK de Trabalhos Web do Azure</a>.</li>
<li>Consulte outros guias de recursos para obter informações sobre opções adicionais para armazenar dados no Azure.
  <ul>
    <li>Use <a href="/pt-br/documentation/articles/storage-dotnet-how-to-use-tables/">Armazenamento de tabela</a> para armazenar dados estruturados.</li>
    <li>Use <a href="/pt-br/documentation/articles/storage-dotnet-how-to-use-queues/">Armazenamento de Fila</a> para armazenar dados não estruturados.</li>
    <li>Use <a href="/pt-br/documentation/articles/sql-database-dotnet-how-to-use/">Banco de dados SQL</a> para armazenar dados relacionais.</li>
  </ul>
</li>
</ul>

  [Próximas etapas]: #next-steps
  [O que é Armazenamento de Blob]: #what-is
  [Conceitos]: #concepts
  [Criar uma conta de armazenamento do Azure]: #create-account
  [Configurar uma cadeia de conexão de armazenamento]: #setup-connection-string
  [Como: Acessar programaticamente o armazenamento de blobs]: #configure-access
  [Como: Criar um contêiner]: #create-container
  [Como: Carregar um blob em um contêiner]: #upload-blob
  [Como: Listar os blobs em um contêiner]: #list-blob
  [Como: Baixar blobs]: #download-blobs
  [Como: Excluir blobs]: #delete-blobs
  [Como: Listar blobs em páginas de maneira assíncrona]: #list-blobs-async
  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png
  
  [Armazenando e acessando dados no Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433040.aspx
  [Blog da equipe do Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configurando cadeias de conexão]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee758697.aspx
  [Referência à Biblioteca cliente do .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Referência da API REST]: http://msdn.microsoft.com/pt-br/library/windowsazure/dd179355
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2

<!--HONumber=35.1-->
