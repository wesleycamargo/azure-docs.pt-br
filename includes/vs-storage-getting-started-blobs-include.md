##### Criar um contêiner

Assim como arquivos residem em pastas, blobs de armazenamento residem em contêineres. É possível usar um objeto **CloudBlobClient** para referência de um contêiner existente ou chamar o método CreateCloudBlobClient() para criar um novo contêiner.

O código a seguir mostra como criar um novo contêiner de armazenamento de blob. O primeiro código cria um objeto **BlobClient** assim você pode acessar as funções do objeto, como criar um contêiner de armazenamento. Em seguida, o código tenta fazer referência a um contêiner de armazenamento denominado “mycontainer”. Se um contêiner com este nome não puder ser encontrado, ele será criado.

    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named “mycontainer.”
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // If “mycontainer” doesn’t exist, create it.
    container.CreateIfNotExists();

Por padrão, o novo contêiner é privado e você deve especificar sua chave de acesso do armazenamento para baixar blobs desse contêiner. Se desejar tornar os arquivos dentro do contêiner disponíveis a todos, pode definir o contêiner como público usando o seguinte código.

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess = 
        BlobContainerPublicAccessType.Blob }); 

**OBSERVAÇÃO:** Use este bloco de código antes do código na seções a seguir.

##### Carregar um blob em um contêiner

Para carregar um arquivo de blob para um contêiner, obtenha uma referência de contêiner e use-a para obter uma referência de blob. Quando tiver uma referência de blob, poderá transferir qualquer fluxo de dados para ele, chamando o método **UploadFromStream()**. Essa operação criará o blob, caso ele não exista, ou o substituirá, caso ele já exista. O exemplo a seguir mostra como carregar um blob em um contêiner e pressupõe que o contêiner já tenha sido criado.

    // Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named “myfile”.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

##### Listar os blobs em um contêiner

Para listar blobs em um contêiner, primeiro obtenha uma referência ao contêiner. Em seguira, poderá chamar o método **ListBlobs()** do contêiner para recuperar os blobs e/ou diretórios dentro dele. Para acessar o avançado conjunto de propriedades e métodos para um **IListBlobItem** retornado, você deve convertê-lo em um objeto **CloudBlockBlob**, **CloudPageBlob** ou **CloudBlobDirectory**. Se o tipo de blob for desconhecido, você poderá usar uma verificação de tipo para determinar em qual convertê-lo. O código a seguir demonstra como recuperar e apresentar a saída do URI de cada item no contêiner chamado "photos".

    // Get a reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("photos");

    // Loop through items in the container and output the length and URI for each 
    // item.
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

Existem outras maneiras de listar o conteúdo de um contêiner de blob. Consulte [Como usar o Armazenamento de Blob do .NET][Como usar o Armazenamento de Blob do .NET] para obter mais informações.

##### Baixar um blob

Para baixar um blob, primeiramente obtenha uma referência para o blob e chame o método DownloadToStream(). O exemplo a seguir usa o método DownloadToStream() para transferir o conteúdo do blob para um objeto de fluxo pode ser salvo como arquivo local.

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save the blob contents to a file named “myfile”.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

Existem outras maneiras de salvar blobs como arquivos. Consulte [Como usar o Armazenamento de Blob do .NET][1] para obter mais informações.

##### Excluir um blob

Para excluir um blob, primeiramente obtenha uma referência para o blob e chame o método Delete() nele.

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();

[Saiba mais sobre o armazenamento do Azure][Saiba mais sobre o armazenamento do Azure]
Consulte também [Procurando recursos de armazenamento no Gerenciador de servidores][Procurando recursos de armazenamento no Gerenciador de servidores].

  [Como usar o Armazenamento de Blob do .NET]: http://azure.microsoft.com/pt-BR/documentation/articles/storage-dotnet-how-to-use-blobs/#list-blob
  [1]: http://azure.microsoft.com/pt-BR/documentation/articles/storage-dotnet-how-to-use-blobs/#download-blobs
  [Saiba mais sobre o armazenamento do Azure]: http://azure.microsoft.com/documentation/services/storage/
  [Procurando recursos de armazenamento no Gerenciador de servidores]: http://msdn.microsoft.com/pt-BR/library/azure/ff683677.aspx
