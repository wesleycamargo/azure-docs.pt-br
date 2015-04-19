<properties 
	pageTitle="Introdução ao Armazenamento do Azure" 
	description="" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Introdução](vs-storage-aspnet5-getting-started-blobs.md)
> - [O que aconteceu](vs-storage-aspnet5-what-happened.md)

## Introdução ao Armazenamento do Azure (Projetos ASP.NET 5)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet5-getting-started-blobs.md)
> - [Filas](vs-storage-aspnet5-getting-started-queues.md)
> - [Tabelas](vs-storage-aspnet5-getting-started-tables.md)

Armazenamento de Blob do Azure é um serviço para armazenar grandes quantidades de dados não estruturados que podem ser acessados de qualquer lugar do mundo por meio de HTTP ou HTTPS. Um único blob pode ter qualquer tamanho. Blobs podem ser coisas como imagens, arquivos de áudio e vídeo, dados brutos e arquivos de documentos.

Para iniciar, você precisará criar uma conta do Armazenamento do Azure e um ou mais contêineres no armazenamento. Por exemplo, você poderia criar um armazenamento chamado "Scrapbook" e criar contêineres no armazenamento chamados "imagens" para armazenar fotos e "áudio" para armazenar arquivos de áudio. Depois de criar os contêineres, você poderá carregar arquivos de blob individuais para eles. Consulte [Como usar o Armazenamento de Blob do .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/ "How to use Blob Storage from .NET") para obter mais informações sobre como manipular blobs programaticamente.

Para acessar programaticamente blobs em projetos do ASP.NET 5, você precisa adicionar os itens a seguir, se eles ainda não existirem.

1. Adicione as seguintes declarações de namespace de código à parte superior de qualquer arquivo C# no qual você deseja acessar o Armazenamento do Azure por meio de programação.

		using Microsoft.Framework.ConfigurationModel;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
		using System.Threading.Tasks;

2. Use o seguinte código para obter a definição da configuração.

		 Configuration = new Configuration()
                .AddJsonFile("config.json")
                .AddEnvironmentVariables();

##### Obter a cadeia de conexão do armazenamento
Antes de poder realizar qualquer coisa com um blob, será necessário obter a cadeia de conexão para a conta de armazenamento onde os blobs residirão. Você pode usar o tipo **CloudStorageAccount** para representar suas informações de conta de armazenamento. Se estiver usando um projeto ASP.NET 5, você poderá chamar o método do objeto Configuration para obter sua cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração de serviço do Azure, como mostrado no código a seguir.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

##### Criar um contêiner
Assim como arquivos residem em pastas, blobs de armazenamento residem em contêineres. É possível usar um objeto **CloudBlobClient** para referência de um contêiner existente ou chamar o método CreateCloudBlobClient() para criar um novo contêiner.

O código a seguir mostra como criar um novo contêiner de armazenamento de blob. Primeiro o código cria um objeto **BlobClient**, assim você pode acessar as funções do objeto, como criar um contêiner de armazenamento. Em seguida, o código tenta fazer referência a um contêiner de armazenamento denominado "mycontainer". Se um contêiner com este nome não puder ser encontrado, ele será criado.

**OBSERVAÇÃO:** os APIs que executam chamadas em saída para o armazenamento do Azure em ASP.NET 5 são assíncronos. Consulte [Programação assíncrona com Async e Await](http://msdn.microsoft.com/library/hh191443.aspx) para obter mais informações. O código a seguir pressupõe que métodos de programação assíncrona estão sendo usados.

	// Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // If "mycontainer" doesn't exist, create it.
    await container.CreateIfNotExistsAsync();    

Por padrão, o novo contêiner é privado e você deve especificar sua chave de acesso de armazenamento para baixar blobs desse contêiner. Se desejar tornar os arquivos dentro do contêiner disponíveis a todos, pode definir o contêiner como público usando o seguinte código.

	await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

**OBSERVAÇÃO:** use todo esse código antes do código nas seções a seguir.

##### Carregar um blob em um contêiner
Para carregar um arquivo de blob para um contêiner, obtenha uma referência de contêiner e use-a para obter uma referência de blob. Quando tiver uma referência de blob, poderá transferir qualquer fluxo de dados para ele, chamando o método **UploadFromStreamAsync()**. Essa operação criará o blob, caso ele não exista, ou o substituirá, caso ele já exista. O exemplo a seguir mostra como carregar um blob em um contêiner e pressupõe que o contêiner já tenha sido criado.

	// Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");            

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named "myfile".
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

##### Listar os blobs em um contêiner
Para listar blobs em um contêiner, primeiro obtenha uma referência ao contêiner. Você pode usar o método **ListBlobsSegmentedAsync()** do contêiner para recuperar os blobs e/ou diretórios presentes dentro dele. Para acessar o avançado conjunto de propriedades e métodos para um **IListBlobItem** retornado, você deve convertê-lo em um objeto **CloudBlockBlob**, **CloudPageBlob** ou **CloudBlobDirectory**. Se o tipo de blob for desconhecido, você poderá usar uma verificação de tipo para determinar em qual convertê-lo. O código a seguir demonstra como recuperar e apresentar a saída do URI de cada item no contêiner.

	BlobContinuationToken token = null;
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
            token = resultSegment.ContinuationToken;

            foreach (IListBlobItem item in resultSegment.Results)
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
        } while (token != null);

Existem outras maneiras de listar o conteúdo de um contêiner de blob. Consulte [Como usar o Armazenamento de Blob do .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#list-blob) para obter mais informações.

##### Baixar um blob
Para baixar um blob, primeiramente obtenha uma referência para o blob e chame o método **DownloadToStreamAsync()**. O exemplo a seguir usa o método **DownloadToStreamAsync()** para transferir o conteúdo do blob para um objeto de fluxo pode ser salvo como arquivo local.

	// Get a reference to a blob named "photo1.jpg".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

	// Save the blob contents to a file named "myfile".
	using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
	{
    	await blockBlob.DownloadToStreamAsync(fileStream);
	}

Existem outras maneiras de salvar blobs como arquivos. Consulte [Como usar o Armazenamento de Blob do .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#download-blobs) para obter mais informações.

##### Excluir um blob
Para excluir um blob, primeiramente obtenha uma referência para o blob e chame o método **DeleteAsync()** nele.

	// Get a reference to a blob named "myblob.txt".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

	// Delete the blob.
	await blockBlob.DeleteAsync();

[Saiba mais sobre o armazenamento do Azure](http://azure.microsoft.com/documentation/services/storage/)
Consulte também [Procurar recursos de armazenamento no Gerenciador de Servidores](http://msdn.microsoft.com/library/azure/ff683677.aspx) e [ASP.NET 5](http://www.asp.net/vnext).
\<!--HONumber=42-->
