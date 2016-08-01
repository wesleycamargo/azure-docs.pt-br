<properties
	pageTitle="Introdução ao armazenamento de blob e aos serviços conectados do Visual Studio (ASP.NET) | Microsoft Azure"
	description="Como começar a usar o armazenamento de Blob do Azure em um projeto ASP.NET no Visual Studio após a conexão a uma conta de armazenamento usando os serviços conectados do Visual Studio"
	services="storage"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-getting-started"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/18/2016"
	ms.author="tarcher"/>

# Introdução ao armazenamento de blob e aos serviços conectados do Visual Studio (ASP.NET)

[AZURE.INCLUDE [storage-try-azure-tools](../../includes/storage-try-azure-tools.md)]

## Visão geral

Este artigo descreve como começar a usar o armazenamento de Blobs do Azure depois de criar ou fazer referência a uma conta de armazenamento do Azure em um aplicativo ASP.NET usando a caixa de diálogo do Visual Studio **Adicionar Serviços Conectados**. O artigo mostra como criar contêineres de blob e executar outras tarefas comuns, como carregar, listar, baixar e excluir blobs. Os exemplos são escritos em C# e usam a [Biblioteca de Cliente do Armazenamento do Microsoft Azure para .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

 - Para obter mais informações sobre como usar o Armazenamento de Blobs do Azure, consulte [Introdução ao Armazenamento de Blobs do Azure usando .NET](storage-dotnet-how-to-use-blobs.md).
 - Para obter mais informações sobre projetos ASP.NET, consulte [ASP.NET](http://www.asp.net).


Armazenamento de Blob do Azure é um serviço para armazenar grandes quantidades de dados não estruturados que podem ser acessados de qualquer lugar do mundo por meio de HTTP ou HTTPS. Um único blob pode ter qualquer tamanho. Blobs podem ser coisas como imagens, arquivos de áudio e vídeo, dados brutos e arquivos de documentos.

Assim como arquivos residem em pastas, blobs de armazenamento residem em contêineres. Depois de criar uma conta de armazenamento, crie um ou mais contêineres no armazenamento. Por exemplo, em um armazenamento chamado "Scrapbook", você poderia criar contêineres de blob no armazenamento chamados "imagens" para armazenar fotos e "áudio" para armazenar arquivos de áudio. Depois de criar os contêineres, você poderá carregar arquivos de blob individuais para eles.




## Acessar contêineres de blob em código

Para acessar programaticamente blobs em projetos do ASP.NET, você precisa adicionar os itens a seguir, se eles ainda não existirem.

1. Adicione as seguintes declarações de namespace de código à parte superior de qualquer arquivo C# no qual você deseja acessar o Armazenamento do Azure por meio de programação.

		using Microsoft.Azure;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;


2. Obtenha um objeto **CloudStorageAccount** que represente as informações da conta de armazenamento. Use o seguinte código para obter a sua cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure.

		CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		   CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    > [AZURE.NOTE] Use todo o código anterior antes do código nas seções a seguir.

3. Obtenha um objeto **CloudBlobClient** para fazer referência a um contêiner existente em sua conta de armazenamento.

		// Create a blob client.
		CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [AZURE.NOTE] Algumas APIs que executam chamadas para o armazenamento do Azure no ASP.NET 5 são assíncronas. Confira [Programação assíncrona com Async e Await](http://msdn.microsoft.com/library/hh191443.aspx) para saber mais.


## Criar um contêiner de blob no código

Você também pode usar o objeto **CloudBlobClient** para criar um contêiner na sua conta de armazenamento. Tudo o que você precisa fazer é adicionar uma chamada para **CreateIfNotExistsAsync** ao código acima, como mostrado no exemplo a seguir.

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();

## Carregar um blob em um contêiner

O Armazenamento de Blob do Azure oferece suporte a blobs de blocos e a blobs de páginas. Na maioria dos casos, o blob de blocos é o tipo recomendado a ser usado.

Para carregar um arquivo em um blob de blocos, obtenha uma referência de contêiner e use-a para obter uma referência de blob de blocos. Quando tiver uma referência de blob, poderá transferir qualquer fluxo de dados para ele, chamando o método **UploadFromStream**. Essa operação criará o blob, caso ele não exista, ou o substituirá, caso ele já exista. O exemplo a seguir mostra como carregar um blob em um contêiner e pressupõe que o contêiner já tenha sido criado.

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## Listar os blobs em um contêiner

Para listar os blobs em um contêiner, use o método **ListBlobs** para recuperar os blobs e/ou diretórios dentro dele. Para acessar o avançado conjunto de propriedades e métodos para um **IListBlobItem** retornado, você deve convertê-lo em um objeto **CloudBlockBlob**, **CloudPageBlob** ou **CloudBlobDirectory**. Se o tipo for desconhecido, você poderá usar uma verificação de tipo para determinar no qual convertê-lo. O código a seguir demonstra como recuperar e apresentar a saída do URI de cada item no contêiner **photos**.

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

Como mostrado no exemplo anterior, o serviço Blob também tem o conceito de diretórios dentro de contêineres. Isso é para que você possa organizar seus blobs em uma estrutura mais semelhante a uma pasta. Por exemplo, considere o seguinte conjunto de blobs de blocos em um contêiner chamado **photos**.

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

Quando você chama **ListBlobs** no contêiner 'photos' (como mostrado no exemplo anterior), a coleção retornada conterá os objetos **CloudBlobDirectory** e **CloudBlockBlob**, que representam os diretórios e os blobs contidos no nível superior. O exemplo a seguir mostra a saída resultante.

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Opcionalmente, você pode definir o parâmetro **UseFlatBlobListing** do método **ListBlobs** como** true**. Isso resultaria em cada blob sendo retornado como um **CloudBlockBlob**, independentemente do diretório. O exemplo a seguir mostra a chamada para **ListBlobs**.

    // Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

E o exemplo a seguir mostra os resultados.

	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg



## Baixar blobs

Para baixar blobs, use o método **DownloadToStream**. O exemplo a seguir usa o método **DownloadToStream** para transferir o conteúdo do blob para um objeto de fluxo que você pode persistir em um arquivo local.

    // Retrieve a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

Você também pode usar o método **DownloadToStream** para baixar o conteúdo de um blob como uma cadeia de texto.

	// Retrieve a reference to a blob named "myblob.txt"
	CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

	string text;
	using (var memoryStream = new MemoryStream())
	{
		blockBlob2.DownloadToStream(memoryStream);
		text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
	}

## Excluir blobs

Para excluir um blob, use o método **Delete**.

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## Listar blobs em páginas de maneira assíncrona

Se você está listando uma grande quantidade de blobs ou se deseja controlar o número de resultados retornados em uma operação de listagem, pode listar os blobs em páginas de resultados. O exemplo a seguir mostra como retornar resultados em páginas de forma assíncrona, para que a execução não fique bloqueada enquanto espera para retornar um grande conjunto de resultados.

Este exemplo mostra uma listagem de blob simples, mas você também pode realizar uma listagem hierárquica, configurando o parâmetro **useFlatBlobListing** do método **ListBlobsSegmentedAsync** como **false**.

Como o exemplo de método chama um método assíncrono, ele deve ser precedido pela palavra-chave **async** e retornar um objeto **Task**. A palavra-chave await especificada para o método **ListBlobsSegmentedAsync** suspende a execução do método de exemplo até que a tarefa de listagem seja concluída.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        //List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        //When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## Próximas etapas

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]

<!---HONumber=AcomDC_0720_2016-->