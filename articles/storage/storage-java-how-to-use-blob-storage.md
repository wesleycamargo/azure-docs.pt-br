<properties 
	pageTitle="Como usar o Armazenamento de Blob do Java | Microsoft Azure" 
	description="Saiba como usar o serviço Blob do Azure para carregar, baixar, listar e excluir o conteúdo de blob. Amostras escritas em Java." 
	services="storage" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="robmcm"/>

# Como usar o Armazenamento de Blob do Java

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Visão geral

Este guia mostra como executar cenários comuns usando o serviço de armazenamento de Blob do Microsoft Azure. As amostras são escritas em Java e usam o [SDK de Armazenamento do Azure para Java][]. Os cenários cobertos incluem **carregamento**, **listagem**, **download** e **exclusão** de blobs. Para obter mais informações sobre blobs, consulte a seção [Próximas etapas](#NextSteps).

> [AZURE.NOTE]Um SDK está disponível para os desenvolvedores que usam o Armazenamento do Azure em dispositivos Android. Para obter mais informações, consulte [SDK de Armazenamento do Azure para Android][].

[AZURE.INCLUDE [armazenamento-blob-conceitos-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Criar um aplicativo Java

Neste guia, você usará os recursos de armazenamento que podem ser executados em um aplicativo Java localmente ou no código em execução em uma função web ou de trabalho do Azure.

Para isso, você vai precisar instalar o JDK (Java Development Kit) e criar uma conta de armazenamento do Azure na sua assinatura do Azure. Depois disso, você terá de verificar se o seu sistema de desenvolvimento atende aos requisitos mínimos e às dependências que estão listadas no repositório [SDK de Armazenamento do Azure para Java][] no GitHub. Se o seu sistema atender a esses requisitos, você poderá seguir as instruções para baixar e instalar as Bibliotecas de Armazenamento do Azure para Java em seu sistema por meio desse repositório. Depois de concluir essas tarefas, você poderá criar um aplicativo Java que usa os exemplos neste artigo.

## Configurar seu aplicativo para acessar o Armazenamento de Blob

Adicione as seguintes instruções de importação à parte superior do arquivo Java no qual deseja usar as APIs de armazenamento do Azure para acessar blobs:

    // Include the following imports to use blob APIs.
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;

## Configurar uma cadeia de conexão de armazenamento do Azure

Um cliente de armazenamento do Azure usa uma cadeia de conexão para armazenar pontos de extremidade e credenciais para acessar serviços de gerenciamento de dados. Ao ser executado em um aplicativo cliente, é necessário fornecer a cadeia de conexão de armazenamento no formato a seguir, usando o nome da sua conta de armazenamento e a chave de Acesso primário da conta de armazenamento listada no Portal de Gerenciamento para os valores *AccountName* e *AccountKey*. Este exemplo mostra como você pode declarar um campo estático para armazenar a cadeia de conexão:

    // Define the connection-string with your values
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

Em um aplicativo que esteja sendo executado em uma função no Microsoft Azure, essa cadeia pode ser armazenada no arquivo de configuração do serviço, *ServiceConfiguration.cscfg*, podendo ser acessada com uma chamada para o método **RoleEnvironment.getConfigurationSettings**. Segue um exemplo de como obter a cadeia de conexão de um elemento **Setting** denominado *StorageConnectionString* no arquivo de configuração de serviço:

    // Retrieve storage account from connection-string.
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

Os exemplos abaixo pressupõem que você usou um desses dois métodos para obter a cadeia de conexão do armazenamento.

## Como criar um contêiner

Um objeto CloudBlobClient permite que você obtenha os objetos de referência para os contêineres e blobs. O código a seguir cria um objeto **CloudBlobClient**. (Observação: existem outras maneiras de criar objetos **CloudStorageAccount**. Para obter mais informações, consulte **CloudStorageAccount** na [Referência de SDK do cliente de armazenamento do Azure].)

[AZURE.INCLUDE [armazenamento-contêiner-nomeando-regras-include](../../includes/storage-container-naming-rules-include.md)]

Use o objeto **CloudBlobClient** para obter uma referência ao contêiner que deseja usar. Se o contêiner não existir, será possível criá-lo com o método **createIfNotExists**; caso contrário, ele retornará o contêiner existente. Por padrão, o novo contêiner é particular; portanto, você deve especificar sua chave de acesso de armazenamento (como anteriormente) para baixar blobs desse contêiner.

	try
    {
        // Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    	// Create the blob client.
	   CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

	   // Get a reference to a container.
	   // The container name must be lower case
	   CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

	   // Create the container if it does not exist.
    	container.createIfNotExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

### Opcional: configurar um contêiner para acesso público

Por padrão, as permissões de um contêiner são configuradas para acesso privado, mas você pode configurar facilmente as permissões de um contêiner para permitir acesso público somente leitura a todos os usuários na Internet:

    // Create a permissions object.
    BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

    // Include public access in the permissions object.
    containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

    // Set the permissions on the container.
    container.uploadPermissions(containerPermissions);

## Como: carregar um blob em um contêiner

Para carregar um arquivo em um blob, obtenha uma referência de contêiner e use-a para obter uma referência de blob. Depois de obter uma referência do blob, é possível carregar qualquer fluxo chamando o upload na referência de blob. Essa operação criará o blob, se ele não existir, ou o substituirá, se ele já existir. Este exemplo de código mostra isso e pressupõe que o contêiner já tenha sido criado.

	try
    {
        // Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    	// Create the blob client.
    	CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

	   // Retrieve reference to a previously created container.
    	CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
			
        // Define the path to a local file.
        final String filePath = "C:\\myimages\\myimage.jpg";

    	// Create or overwrite the "myimage.jpg" blob with contents from a local file.
    	CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");
    	File source = new File(filePath);
    	blob.upload(new FileInputStream(source), source.length());
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Como: listar os blobs em um contêiner

Para listar os blobs em um contêiner, primeiro obtenha uma referência do contêiner como a que você obteve para carregar um blob. É possível usar o método **listBlobs** do contêiner com um loop **for**. O código a seguir gera a saída do Uri de cada blob em um contêiner para o console.

	try
    {
        // Retrieve storage account from connection-string.
    	CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    	// Create the blob client.
    	CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

    	// Retrieve reference to a previously created container.
    	CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
			
    	// Loop over blobs within the container and output the URI to each of them.
    	for (ListBlobItem blobItem : container.listBlobs()) {
	       System.out.println(blobItem.getUri());
	   }
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

O serviço Blob também tem o conceito de diretórios dentro de contêineres. Isso é para que você possa organizar seus blobs em uma estrutura mais semelhante a uma pasta.

Por exemplo, você pode ter um contêiner denominado “photos”, no qual poderia carregar os blobs “rootphoto1”, “2010/photo1”, “2010/photo2” e “2011/photo1”. Isso criaria os diretórios virtuais "2010" e "2011" no contêiner “photos". Quando você chamar **listBlobs** no contêiner “photos”, a coleção retornada conterá os objetos **CloudBlobDirectory** e **CloudBlob** que representam os diretórios e os blobs contidos no nível superior. Nesse caso, os diretórios “2010” e “2011”, bem como “rootphoto1” de photo, seriam retornados. É possível usar o operador **instanceof** para distinguir esses objetos.

Como alternativa, você pode transmitir parâmetros para o método **listBlobs** com o parâmetro **useFlatBlobListing** definido como true. Isso resultará no retorno de cada blob, independentemente do diretório. Para obter mais informações, consulte **CloudBlobContainer.listBlobs** na [Referência de SDK do cliente de armazenamento do Azure].

## Como baixar um blob

Para baixar blobs, siga as mesmas etapas utilizadas para carregar um blob, a fim de obter uma referência do blob. No exemplo de carregamento, você chamou o carregamento no objeto do blob. No exemplo a seguir, chame o download para transferir o conteúdo do blob para um objeto do fluxo, como um **FileOutputStream** que pode ser usado para persistir o blob ao arquivo local.

    try
    {
    	// Retrieve storage account from connection-string.
	   CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

	   // Create the blob client.
	   CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

	   // Retrieve reference to a previously created container.
	   CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
			
	   // Loop through each blob item in the container.
	   for (ListBlobItem blobItem : container.listBlobs()) {
	       // If the item is a blob, not a virtual directory.
	       if (blobItem instanceof CloudBlob) {
	           // Download the item and save it to a file with the same name.
    	        CloudBlob blob = (CloudBlob) blobItem;
    	        blob.download(new FileOutputStream("C:\\mydownloads\" + blob.getName()));
    	    }
    	}
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Como excluir um blob

Para excluir um blob, obtenha uma referência do blob e chame **deleteIfExists**.

    try
    {
	   // Retrieve storage account from connection-string.
	   CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

	   // Create the blob client.
	   CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

	   // Retrieve reference to a previously created container.
	   CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
			
	   // Retrieve reference to a blob named "myimage.jpg".
	   CloudBlockBlob blob = container.getBlockBlobReference("myimage.jpg");

	   // Delete the blob.
	   blob.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Como excluir um contêiner de blob

Por fim, para excluir um contêiner de blob, obtenha uma referência do contêiner de blob e chame **deleteIfExists**.

    try
    {
	   // Retrieve storage account from connection-string.
	   CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

	   // Create the blob client.
	   CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

	   // Retrieve reference to a previously created container.
	   CloudBlobContainer container = blobClient.getContainerReference("mycontainer");
			
	   // Delete the blob container.
	   container.deleteIfExists();
    }
    catch (Exception e)
    {
        // Output the stack trace.
        e.printStackTrace();
    }

## Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de blob, siga estes links para saber mais sobre tarefas de armazenamento mais complexas.

- [SDK de Armazenamento do Azure para Java]
- [Referência de SDK do Cliente de Armazenamento do Azure]
- [API REST de Armazenamento do Azure]
- [Blog da equipe de Armazenamento do Azure]

[Azure SDK for Java]: http://azure.microsoft.com/develop/java/
[SDK de Armazenamento do Azure para Java]: https://github.com/azure/azure-storage-java
[SDK de Armazenamento do Azure para Android]: https://github.com/azure/azure-storage-android
[Referência de SDK do cliente de armazenamento do Azure]: http://dl.windowsazure.com/storage/javadoc/
[API REST de Armazenamento do Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Blog da equipe de Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/
 

<!---HONumber=July15_HO4-->