<properties
	pageTitle="Introdução ao armazenamento de blob e aos serviços conectados do Visual Studio (ASP.NET 5) | Microsoft Azure"
	description="Como começar a usar o armazenamento de Blob do Azure em um projeto do ASP.NET 5 no Visual Studio após a criação de uma conta de armazenamento usando os serviços conectados do Visual Studio"
	services="storage"
	documentationCenter=""
	authors="patshea123"
	manager="douge"
	editor="tglee"/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-getting-started"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/03/2015"
	ms.author="patshea123"/>

# Introdução ao Armazenamento de Blob do Azure e aos serviços conectados do Visual Studio (ASP.NET 5)

> [AZURE.SELECTOR]
> - [Getting started](vs-storage-aspnet5-getting-started-blobs.md)
> - [What happened](vs-storage-aspnet5-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet5-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet5-getting-started-queues.md)
> - [Tables](vs-storage-aspnet5-getting-started-tables.md)

##Visão geral

Este artigo descreve como começar a usar o armazenamento de Blob do Azure no Visual Studio após a criação ou referência de uma conta de armazenamento do Azure em um projeto do ASP.NET 5 usando o diálogo Adicionar Serviços Conectados do Visual Studio.

Armazenamento de Blob do Azure é um serviço para armazenar grandes quantidades de dados não estruturados que podem ser acessados de qualquer lugar do mundo por meio de HTTP ou HTTPS. Um único blob pode ter qualquer tamanho. Blobs podem ser coisas como imagens, arquivos de áudio e vídeo, dados brutos e arquivos de documentos. Este artigo descreve como começar a usar o armazenamento de blob depois de criar uma conta de armazenamento do Azure usando a caixa de diálogo **Adicionar Serviços Conectados** do Visual Studio em um projeto do ASP.NET 5.

Assim como arquivos residem em pastas, blobs de armazenamento residem em contêineres. Após ter criado um armazenamento, crie um ou mais contêineres no armazenamento. Por exemplo, em um armazenamento chamado "Scrapbook", você pode criar contêineres no armazenamento chamados "imagens" para armazenar fotos e "áudio" para armazenar arquivos de áudio. Depois de criar os contêineres, você poderá carregar arquivos de blob individuais para eles. Confira [Como usar o armazenamento de blob do .NET](storage-dotnet-how-to-use-blobs.md "Como usar o armazenamento de blob no .NET") para obter mais informações sobre como manipular blobs programaticamente.



##Acessar contêineres de blob em código

Para acessar programaticamente blobs em projetos do ASP.NET 5, você precisa adicionar os itens a seguir, se eles ainda não existirem.

1. Adicione as seguintes declarações de namespace de código à parte superior de qualquer arquivo C# no qual você queira acessar o armazenamento do Azure por meio de programação.

		using Microsoft.Framework.Configuration;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
		using System.Threading.Tasks;
		using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. Obtenha um objeto **CloudStorageAccount**que represente as informações de sua conta de armazenamento. Use o seguinte código para obter a sua cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure.

		 CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		   CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    **OBSERVAÇÃO:** use todo esse código acima antes do código nas seções a seguir.


3. Use um objeto **CloudBlobClient** para obter uma referência **CloudBlobContainer** a um contêiner existente em sua conta de armazenamento.

		// Create a blob client.
		CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");



##Criar um contêiner no código

Você também pode usar o **CloudBlobClient** para criar um contêiner na sua conta de armazenamento. Basta adicionar uma chamada para **CreateIfNotExistsAsync**, como no seguinte código:

	// Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named “my-new-container.”
    CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();


**OBSERVAÇÃO:** as APIs que executam chamadas para o armazenamento do Azure no ASP.NET 5 são assíncronas. Confira [Programação assíncrona com Async e Await](http://msdn.microsoft.com/library/hh191443.aspx) para saber mais. O código a seguir pressupõe que os métodos de programação assíncrona estão sendo usados.

Se quiser tornar os arquivos dentro do contêiner disponíveis a todos, basta definir o contêiner como público usando o seguinte código.

	await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

##Carregar um blob em um contêiner

Para carregar um arquivo de blob para um contêiner, obtenha uma referência de contêiner e use-a para obter uma referência de blob. Depois que tiver uma referência de blob, você poderá carregar qualquer fluxo de dados nele chamando o método **UploadFromStreamAsync**. Essa operação criará o blob, caso ele não exista, ou o substituirá, caso ele já exista. O exemplo a seguir mostra como carregar um blob em um contêiner e pressupõe que o contêiner já tenha sido criado.

	// Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named “myfile”.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

##Listar os blobs em um contêiner
Para listar blobs em um contêiner, primeiro obtenha uma referência ao contêiner. Depois, você pode usar o método **ListBlobsSegmentedAsync** do contêiner para recuperar os blobs e/ou diretórios nele contidos. Para acessar o avançado conjunto de propriedades e métodos para um **IListBlobItem** retornado, você deve convertê-lo em um objeto **CloudBlockBlob**, **CloudPageBlob** ou **CloudBlobDirectory**. Se o tipo de blob for desconhecido, você poderá usar uma verificação de tipo para determinar em qual convertê-lo. O código a seguir demonstra como recuperar e apresentar a saída do URI de cada item no contêiner.

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

Existem outras maneiras de listar o conteúdo de um contêiner de blob. Consulte [Como usar o armazenamento de blob do .NET](storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container) para obter mais informações.

##Baixar um blob
Para baixar um blob, obtenha primeiro uma referência ao blob e depois chame o método **DownloadToStreamAsync**. O exemplo a seguir usa o método **DownloadToStreamAsync** para transferir o conteúdo do blob para um objeto de fluxo que pode então ser salvo como um arquivo local.

	// Get a reference to a blob named "photo1.jpg".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

	// Save the blob contents to a file named “myfile”.
	using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
	{
    	await blockBlob.DownloadToStreamAsync(fileStream);
	}

Existem outras maneiras de salvar blobs como arquivos. Consulte [Como usar o armazenamento de blob do .NET](storage-dotnet-how-to-use-blobs.md/#download-blobs) para obter mais informações.

##Excluir um blob
Para excluir um blob, obtenha primeiro uma referência ao blob e depois chame o método **DeleteAsync** nele.

	// Get a reference to a blob named "myblob.txt".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

	// Delete the blob.
	await blockBlob.DeleteAsync();

## Próximas etapas

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]

<!---HONumber=Sept15_HO2-->