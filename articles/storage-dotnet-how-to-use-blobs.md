<properties urlDisplayName="Blob Service" pageTitle="Como usar o armazenamento de blob do .NET | Azure" metaKeywords="Get started Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure blob .NET   Azure blob C#   Azure blob C#" description="Learn how to use Microsoft Azure Blob storage to upload,  download, list, and delete blob content. Samples are written in C#." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use Microsoft Azure Blob storage in .NET" authors="tamram" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Como usar o Armazenamento de Blob no .NET

Este guia demonstra como executar cenários comuns usando o
Serviço de armazenamento de Blob do Azure. As amostras são gravadas em C\# e
usam a Biblioteca do Cliente de Armazenamento do Azure para .NET. Os cenários abordados incluem
**carregamento**, **listagem**, **download** e **exclusão** de blobs. Para
obter mais informações sobre blobs, consulte a seção [Próximas etapas][] .

> [WACOM.NOTE] Este guia tem como alvo a Biblioteca do Cliente de Armazenamento .NET do Azure, versão 2.x e superior. A versão recomendada é a Biblioteca de Cliente de Armazenamento 4.x, que está disponível via [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) ou como parte do [SDK do Azure para .NET](/pt-br/downloads/). Consulte [Como: Acessar programaticamente o armazenamento de blobs][] abaixo para obter mais detalhes sobre como obter a Biblioteca de Cliente de Armazenamento.

##Sumário

-   [O que é Blob Storage][]
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

##<a name="create-account"></a><span  class="short-header">Criar uma conta de Armazenamento do Azure</span>
[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

##<a name="setup-connection-string"></a><span  class="short-header">Configurar uma cadeia de conexão de armazenamento</span>

[WACOM.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <a name="configure-access"> </a><span  class="short-header">Como: Acessar programaticamente o armazenamento de blobs</span>

###Obtendo o assembly
Recomendamos que você use o NuGet para obter o assembly `Microsoft.WindowsAzure.Storage.dll`. Clique com o botão direito do mouse no **Gerenciador de Soluções** e escolha **Gerenciar Pacotes NuGet**.  Pesquise online por "WindowsAzure.Storage" e clique em **Instalar** para instalar o pacote Armazenamento do Azure e as dependências.

`Microsoft.WindowsAzure.Storage.dll` também está incluído no SDK do Azure para .NET, que pode ser baixado na <a href="http://www.windowsazure.com/pt-br/develop/net/#">Central de desenvolvedores do .NET</a>. O assembly é instalado no diretório `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`.

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
Você pode usar o tipo **CloudConfigurationManager**
para recuperar a cadeia de conexão de armazenamento e a conta de armazenamento
informações da configuração do serviço do Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Se estiver criando um aplicativo sem nenhuma referência ao Microsoft.WindowsAzure.CloudConfigurationManager, e sua cadeia de conexão estiver localizada no `web.config` ou no `app.config` como mostrado acima, você poderá usar o **ConfigurationManager** para recuperar a cadeia de conexão.  Você precisará adicionar uma referência ao System.Configuration.dll a seu projeto e adicionar outra declaração de namespace para ele:

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
As dependências do ODataLib na Biblioteca de Cliente de Armazenamento para .NET são resolvidas por meio de pacotes do ODataLib (versão 5.0.2) disponíveis por meio do NuGet e não do WCF Data Services.  As bibliotecas do ODataLib podem ser baixadas diretamente ou referenciadas por seu projeto de código por meio do NuGet.  Os pacotes ODataLib específicos são [OData], [Edm]e [Espacial].

## <a name="create-container"> </a><span  class="short-header">Como: Criar um contêiner</span>

Todos os blobs no armazenamento do Azure devem residir em um contêiner. Este exemplo mostra como criar um contêiner se ele ainda não existir:

    // Recuperar a conta de armazenamento da cadeia de conexão.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de blob.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Recuperar uma referência a um contêiner. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Criar o contêiner se ele ainda não existir.
    container.CreateIfNotExists();

Por padrão, o novo contêiner é particular, e você deve especificar sua
chave de acesso de armazenamento para baixar blobs desse
contêiner: Se você quiser disponibilizar os arquivos dentro do contêiner
para qualquer pessoa, poderá definir o contêiner como público usando o seguinte
Código:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
 	    BlobContainerPublicAccessType.Blob }); 

Qualquer pessoa na Internet pode ver blobs em um contêiner público, mas você pode
modificar ou excluí-los somente se você tiver a chave de acesso apropriada.

## <a name="upload-blob"> </a><span  class="short-header">Como: Carregar um blob em um contêiner</span>

O Armazenamento de Blob do Azure oferece suporte a blobs de blocos e a blobs de páginas.  Na maioria dos casos, o blob de blocos é o tipo recomendado a ser usado.

Para carregar um arquivo em um blob, obtenha uma referência de contêiner e use-a para obter
uma referência de blob de bloco. Depois que tiver uma referência de blob, você poderá carregar qualquer
fluxo de dados nele chamando o método **UploadFromStream**. Essa operação criará o blob se ele não existia anteriormente,
ou substituí-lo, se ele existir. O exemplo a seguir mostra como carregar um blob em um contêiner e pressupõe que o contêiner já tenha sido criado.

    // Recuperar a conta de armazenamento da cadeia de conexão.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de blob.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Recuperar uma referência a um contêiner criado anteriormente.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Recuperar uma referência a um blob denominado "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Criar ou substituir o blob "myblob" com o conteúdo de um arquivo local.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    } 

##<a name="list-blob"> </a><span  class="short-header">Como: Listar os blobs em um contêiner</span>

Para listar blobs em um contêiner, primeiro obtenha uma referência ao contêiner. Você
pode usar o método **ListBlobs** do contêiner para recuperar os blobs e/ou diretórios
dentro dele. Para acessar o avançado conjunto de propriedades e métodos para um 
**IListBlobItem** retornado, você deve convertê-lo em um objeto **CloudBlockBlob**, 
**CloudPageBlob** ou **CloudBlobDirectory**.  Se o tipo for desconhecido, você pode usar um 
tipo de verificação para determinar qual convertê-lo.  O código a seguir 
Demonstra como recuperar e gerar a saída do URI de cada item no 
contêiner `fotos`:

    // Recuperar a conta de armazenamento da cadeia de conexão.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de blob. 
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Recuperar uma referência a um contêiner criado anteriormente.
	CloudBlobContainer container = blobClient.GetContainerReference("photos");

	// Executar um loop sobre os itens no contêiner e fornecer a saída do comprimento e do URI.
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

Como mostrado acima, o serviço de blob tem o conceito de diretórios dentro de contêineres
. Isso é para que você possa organizar seus blobs em uma estrutura mais semelhante a uma pasta
. Por exemplo, considere o seguinte conjunto de blobs de blocos em um contêiner
chamado 'fotos':

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

Quando você chama **ListBlobs** no contêiner 'fotos' (como no exemplo acima), a coleção retornada
conterá objetos **CloudBlobDirectory** e **CloudBlockBlob**
representando os diretórios e os blobs contidos no nível superior. Esta seria a saída resultante:

	Diretório: https://<accountname>.blob.core.windows.net/photos/2010/
	Diretório: https://<accountname>.blob.core.windows.net/photos/2011/
	Blob de blocos de comprimento 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Opcionalmente, você pode definir o parâmetro **UseFlatBlobListing** do método **ListBlobs** como 
**verdadeiro**. Isso resultaria em cada blob sendo retornado como um **CloudBlockBlob**
, independentemente do diretório.  Esta seria a chamada para **ListBlobs**:

    // Executar um loop sobre os itens no contêiner e fornecer a saída do comprimento e do URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

e estes seriam os resultados:

	Blob de blocos de comprimento 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Blob de blocos de comprimento 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Blob de blocos de comprimento 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Blob de blocos de comprimento 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Blob de blocos de comprimento 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Blob de blocos de comprimento 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Blob de blocos de comprimento 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Blob de blocos de comprimento 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Para obter mais informações, consulte [CloudBlobContainer.ListBlobs][].

## <a name="download-blobs"> </a><span  class="short-header">Como: Baixar blobs</span>

Para baixar blobs, primeiro recupere uma referência a um blob e, em seguida, chame o método **DownloadToStream**. O seguinte
exemplo usa o método **DownloadToStream** para transferir o conteúdo
do blob para um objeto de fluxo que você pode persistir para um arquivo local.

    // Recuperar a conta de armazenamento da cadeia de conexão.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de blob.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Recuperar uma referência a um contêiner criado anteriormente.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Recuperar uma referência a um blob denominado "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Salvar o conteúdo do blob em um arquivo.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    } 

Você também pode usar o método **DownloadToStream** para baixar o conteúdo de um blob como uma cadeia de texto.

	// Recuperar a conta de armazenamento da cadeia de conexão.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de blob.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Recuperar uma referência a um contêiner criado anteriormente.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

	// Recuperar uma referência a um blob denominado "myblob.txt"
	CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

	texto da cadeia de caracteres;
	using (var memoryStream = new MemoryStream())
	{
		blockBlob2.DownloadToStream(memoryStream);
		text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
	}

##<a name="delete-blobs"> </a><span  class="short-header">Como: Excluir blobs</span>

Para excluir um blob, primeiro obtenha uma referência a um blob e, em seguida, chame o
método **Delete** sobre ele.

    // Recuperar a conta de armazenamento da cadeia de conexão.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Criar o cliente de blob.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Recuperar uma referência a um contêiner criado anteriormente.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Recuperar uma referência a um blob denominado "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Excluir um blob.
    blockBlob.Delete(); 


##<a name="list-blobs-async"> </a><span  class="short-header">Como: Listar blobs em páginas de maneira assíncrona</span> 

Se você está listando uma grande quantidade de blobs ou se deseja controlar o número de resultados retornados em uma operação de listagem, pode listar os blobs em páginas de resultados. Este exemplo mostra como retornar resultados em páginas de forma assíncrona, para que a execução não fique bloqueada enquanto espera para retornar um grande conjunto de resultados.

Este exemplo mostra uma listagem de blob simples, mas você também pode realizar uma listagem hierárquica, configurando o parâmetro `useFlatBlobListing` do método **ListBlobsSegmentedAsync** como `falso`.

Como o método de amostra chama um método assíncrono, ele deve ser precedido pela palavra-chave `async`  e deve retornar um objeto **Task**. A palavra-chave await especificada para o método **ListBlobsSegmentedAsync** suspende a execução do método de amostra até que a tarefa de listagem seja concluída.

    async public static Task ListBlobsSegmentedInFlatListing()
    {
        // Recuperar a conta de armazenamento da cadeia de conexão.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
            CloudConfigurationManager.GetSetting("StorageConnectionString"));

        // Criar o cliente de blob.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Recuperar uma referência a um contêiner criado anteriormente.
        CloudBlobContainer container = blobClient.GetContainerReference("myblobs");

        //Listar blobs em páginas.
        Console.WriteLine("Listar blobs em páginas:");

        //Listar blobs com um tamanho de paginação de 10, para fins de exemplo. 
		//A primeira chamada não inclui o token de continuação.
        BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(
                "", true, BlobListingDetails.All, 10, null, null, null);

        //Enumere o segmento de resultado retornado.
        int i = 0;
        if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
        foreach (var blobItem em resultSegment.Results)
        {
            Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
        }
        Console.WriteLine();

        //Obter o token de continuação, se houver mais páginas de resultados.
        BlobContinuationToken continuationToken = resultSegment.ContinuationToken;

        //Verificar se há mais resultados e listá-los em páginas de 10, enquanto um token de continuação será retornado.
        while (continuationToken != null)
        {
            //Esta sobrecarga permite o controle do tamanho da página. 
			//Você pode retornar todos os resultados restantes, passando nulo para o parâmetro maxResults, 
            //ou chamando outra sobrecarga.
            resultSegment = await container.ListBlobsSegmentedAsync(
					"", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem em resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Obter o próximo token de continuação.
            continuationToken = resultSegment.ContinuationToken;
        }
    }

## <a name="next-steps"></a><span  class="short-header">Próximas etapas</span>

Agora que você aprendeu os conceitos básicos do armazenamento de blob, siga estes links
para saber como fazer tarefas mais complexas de armazenamento.
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
  [O que é Blob Storage]: #what-is
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
  [Referência à biblioteca cliente do .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Referência da API REST]: http://msdn.microsoft.com/pt-br/library/windowsazure/dd179355
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Espacial]: http://nuget.org/packages/System.Spatial/5.0.2
