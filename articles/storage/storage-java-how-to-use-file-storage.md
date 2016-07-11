<properties
	pageTitle="Como usar o Armazenamento de Arquivo do Java | Microsoft Azure"
	description="Saiba como usar o serviço de arquivos do Azure para carregar, baixar, listar e excluir arquivos. Amostras escritas em Java."
	services="storage"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor="" />

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="06/24/2016"
	ms.author="robmcm"/>

# Como usar o Armazenamento de Arquivo no Java

[AZURE.INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

## Visão geral

Neste guia você aprenderá a executar operações básicas no serviço de armazenamento de arquivos do Microsoft Azure. Por meio de exemplos escritos em Java, você aprenderá a criar compartilhamentos e diretórios, carregar, listar e excluir arquivos. Se você for novo no serviço de armazenamento de arquivos do Microsoft Azure, será muito útil percorrer os conceitos das seções a seguir para compreender os exemplos.

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Criar um aplicativo Java

Para criar os exemplos, você precisará do JDK (Java Development Kit) e do [SDK do Armazenamento do Azure para Java][]. Você também deverá ter criado uma conta de armazenamento do Azure.

## Configurar seu aplicativo para usar o armazenamento de arquivo

Para usar as APIs de armazenamento do Azure, adicione a instrução a seguir à parte superior do arquivo Java do qual você pretende acessar o serviço de armazenamento.

	// Include the following imports to use blob APIs.
    import com.microsoft.azure.storage.*;
	import com.microsoft.azure.storage.file.*;

## Configurar uma cadeia de conexão de armazenamento do Azure

Para usar o armazenamento de arquivos, será necessário se conectar à sua conta de armazenamento do Azure. A primeira etapa seria configurar uma cadeia de conexão que usaremos para acessar a sua conta de armazenamento. Vamos definir uma variável estática para fazer isso.

	// Configure the connection-string with your values
	public static final String storageConnectionString =
	    "DefaultEndpointsProtocol=http;" +
	    "AccountName=your_storage_account_name;" +
	    "AccountKey=your_storage_account_key";

> [AZURE.NOTE] Substitua nome\_da\_sua\_conta\_de\_armazenamento e chave\_da\_sua\_conta\_de\_armazenamento pelos valores reais da sua conta de armazenamento.

## Conectar-se a uma conta de armazenamento do Azure

Para se conectar à sua conta de armazenamento, você precisa usar o objeto **CloudStorageAccount**, passando uma cadeia de conexão para seu método de **análise**.

	// Use the CloudStorageAccount object to connect to your storage account
	try {
		CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
	} catch (InvalidKeyException invalidKey) {
		// Handle the exception
	}

O **CloudStorageAccount.parse** gera uma InvalidKeyException; portanto, você precisará colocá-lo dentro de um bloco try/catch.

## Como criar um compartilhamento

Todos os arquivos e diretórios do armazenamento de arquivos residem em um contêiner chamado **Compartilhamento**. Sua conta de armazenamento pode ter tantos compartilhamentos quantos a capacidade da conta permitir. Para obter acesso a um compartilhamento e seu conteúdo, você precisa usar um cliente de armazenamento de arquivos.

	// Create the file storage client.
	CloudFileClient fileClient = storageAccount.createCloudFileClient();

Usando o cliente de armazenamento de arquivos, será possível obter uma referência a um compartilhamento.

	// Get a reference to the file share
	CloudFileShare share = fileClient.getShareReference("sampleshare");

Para realmente criar o compartilhamento, use o método **createIfNotExists** do objeto CloudFileShare.

	if (share.createIfNotExists()) {
		System.out.println("New share created");
	}

Neste ponto, **compartilhar** contém uma referência a um compartilhamento chamado **sampleshare**.

## Como carregar um arquivo

O compartilhamento do armazenamento de arquivos do Azure contém, pelo menos, um diretório raiz onde os arquivos podem residir. Nesta seção, você aprenderá a carregar um arquivo do armazenamento local para o diretório raiz de um compartilhamento.

A primeira etapa do carregamento de um arquivo é obter uma referência para o diretório onde ele deve residir. Para isso, você deve chamar o método **getRootDirectoryReference** do objeto de compartilhamento.

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

Agora que você tem uma referência para o diretório raiz do compartilhamento, pode carregar um arquivo usando o código a seguir.

	// Define the path to a local file.
	final String filePath = "C:\\temp\\Readme.txt";

	CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
	cloudFile.uploadFromFile(filePath);

## Coco criar um diretório

Você também pode organizar o armazenamento colocando arquivos em subdiretórios em vez de manter todos eles no diretório raiz. O serviço de armazenamento de arquivos do Azure permite que você crie tantos diretórios quanto a sua conta permitir. O código a seguir criará um subdiretório chamado **sampledir** no diretório raiz.

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	//Get a reference to the sampledir directory
	CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

	if (sampleDir.createIfNotExists()) {
		System.out.println("sampledir created");
	} else {
		System.out.println("sampledir already exists");
	}

## Como listar arquivos e diretórios em um compartilhamento

A lista de arquivos e diretórios em um compartilhamento pode ser obtida facilmente chamando **listFilesAndDirectories** em uma referência CloudFileDirectory. O método retorna uma lista de objetos ListFileItem nos quais você pode iterar. Como exemplo, o código a seguir lista os arquivos e diretórios contidos no diretório raiz.

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
		System.out.println(fileItem.getUri());
	}


## Como baixar um arquivo

Uma das operações mais frequentes a serem executadas no armazenamento de arquivos é o download de arquivos. No exemplo a seguir, o código baixa o arquivo SampleFile.txt e exibe seu conteúdo.

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	//Get a reference to the directory that contains the file
	CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

	//Get a reference to the file you want to download
	CloudFile file = sampleDir.getFileReference("SampleFile.txt");

	//Write the contents of the file to the console.
	System.out.println(file.downloadText());

## Como excluir um arquivo

Outra operação de armazenamento de arquivo comum é a exclusão de arquivos. O código a seguir exclui um arquivo chamado SampleFile.txt armazenado em um diretório chamado **sampledir**.


	// Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	// Get a reference to the directory where the file to be deleted is in
	CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

	String filename = "SampleFile.txt"
	CloudFile file;

	file = containerDir.getFileReference(filename)
	if ( file.deleteIfExists() ) {
		System.out.println(filename + " was deleted");
	}


## Como excluir um diretório

Excluir um diretório é uma tarefa bem simples, embora deva-se observar que não é possível excluir um diretório que ainda contenha arquivos ou outros diretórios.

	// Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	// Get a reference to the directory you want to delete
	CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

	// Delete the directory
	if ( containerDir.deleteIfExists() ) {
		System.out.println("Directory deleted");
	}


## Como excluir um compartilhamento

Para excluir um compartilhamento deve-se chamar o método **deleteIfExists** em um objeto CloudFileShare. O código fornecido a seguir é um exemplo de como fazer isso.

	try
	{
	    // Retrieve storage account from connection-string.
	    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

	    // Create the file client.
	   CloudFileClient fileClient = storageAccount.createCloudFileClient();

	   // Get a reference to the file share
	   CloudFileShare share = fileClient.getShareReference("sampleshare");

	   if (share.deleteIfExists()) {
		   System.out.println("sampleshare deleted");
	   }
	} catch (Exception e) {
		e.printStackTrace();
	}

## Próximas etapas

Se você quiser saber mais sobre outras APIs de armazenamento do Azure, siga estes links.

- [Central de Desenvolvedores do Java](http://azure.microsoft.com/develop/java/)
- [SDK de Armazenamento do Azure para Java](https://github.com/azure/azure-storage-java)
- [SDK de Armazenamento do Azure para Android](https://github.com/azure/azure-storage-android)
- [Referência de SDK do Cliente de Armazenamento do Azure](http://dl.windowsazure.com/storage/javadoc/)
- [API REST de serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blog da equipe de Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- [Transferir dados com o Utilitário de Linha de Comando AzCopy](storage-use-azcopy.md)

<!---HONumber=AcomDC_0629_2016-->