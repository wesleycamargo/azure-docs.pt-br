---
title: "Guia de início rápido do Azure – Transferir objetos para/do Armazenamento de Blobs do Azure usando o .NET | Microsoft Docs"
description: Aprenda rapidamente a transferir objetos de/para o Armazenamento de Blobs do Azure usando o .NET
services: storage
documentationcenter: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 12/04/2017
ms.author: tamram
ms.openlocfilehash: 5020f070a8eb9215f175fc3ff3a905cff28ce37f
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-net"></a>Transferir objetos de/para o Armazenamento de Blobs do Azure usando o .NET

Neste guia de início rápido, você aprende a usar a biblioteca de clientes do .NET para o Armazenamento do Azure para carregar, baixar e listar blobs de bloco em um contêiner.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* Instale o núcleo do .NET 2.0 para [Linux](/dotnet/core/linux-prerequisites?tabs=netcore2x) ou [Windows](/dotnet/core/windows-prerequisites?tabs=netcore2x)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Baixar o aplicativo de exemplo

O aplicativo de exemplo usado neste guia de início rápido é um aplicativo de console básico. 

Use o [git](https://git-scm.com/) para baixar uma cópia do aplicativo para seu ambiente de desenvolvimento. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

Este comando clona o repositório para sua pasta do git local. Para abrir a solução do Visual Studio, procure a pasta storage-blobs-dotnet-quickstart, abra-a e clique duas vezes em storage-blobs-dotnet-quickstart.sln. 

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de conexão de armazenamento

No aplicativo, você deve fornecer a cadeia de conexão para sua conta de armazenamento. É recomendável armazenar essa cadeia de caracteres de conexão dentro de uma variável de ambiente no computador local que está executando o aplicativo. Siga um dos exemplos abaixo, dependendo do seu sistema operacional para criar a variável de ambiente.  Substitua \<yourconnectionstring\> pela sua cadeia de caracteres de conexão atual.

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```
### <a name="windows"></a>Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

## <a name="run-the-sample"></a>Execute o exemplo

Este exemplo cria um arquivo de teste em Meus Documentos, carrega-o para o Armazenamento de Blobs, lista os blobs no contêiner e baixa o arquivo com um novo nome para que você possa comparar o arquivo novo com antigo. 

Navegue até seu diretório de aplicativo e execute o aplicativo com o comando `dotnet run`.

```
dotnet run
```

A saída exibida é semelhante ao seguinte exemplo:

```
Azure Blob storage quick start sample
Temp file = /home/admin/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt
Uploading to Blob storage as blob 'QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt'
List blobs in container.
https://mystorageaccount.blob.core.windows.net/quickstartblobs/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374.txt
Downloading blob to /home/admin/QuickStart_b73f2550-bf20-4b3b-92ec-b9b31c56b374_DOWNLOADED.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.
```

Quando você pressiona qualquer tecla para continuar, ele exclui o contêiner de armazenamento e os arquivos. Antes de continuar, verifique se os dois documentos estão em MyDocuments. Você pode abri-los e ver que eles são idênticos. Copie a URL para o blob fora da janela do console e cole-a em um navegador para exibir o conteúdo do arquivo no Armazenamento de Blobs.

Você também pode usar uma ferramenta como o [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para exibir os arquivos no Armazenamento de Blobs. O Gerenciador de Armazenamento do Azure é uma ferramenta gratuita de multiplataforma que permite que você acesse as informações da sua conta de armazenamento. 

Depois de verificar os arquivos, pressione qualquer tecla para concluir a demonstração e excluir os arquivos de teste. Agora que você sabe o que o exemplo faz, abra o arquivo Program.cs para examinar o código. 

## <a name="understand-the-sample-code"></a>Entender o código de exemplo

Em seguida, vamos percorrer o código de exemplo para que você possa entender como ele funciona.

### <a name="get-references-to-the-storage-objects"></a>Obter referências a objetos de armazenamento

A primeira coisa a fazer é criar as referências aos objetos usados para acessar e gerenciar o Armazenamento de Blobs. Esses objetos dependem uns dos outros, cada um é usado pelo próximo na lista.

* Crie uma instância do objeto [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet) apontando para a conta de armazenamento.

* Crie uma instância do objeto [CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet), que aponta para o serviço Blob na sua conta de armazenamento.

* Crie uma instância do objeto [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet) que representa o contêiner que você está acessando. Os contêineres são usados para organizar seus blobs da mesma forma que você usa pastas no seu computador para organizar seus arquivos.

Assim que tiver o [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet), será possível criar uma instância do objeto [CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet), que aponta para o blob específico em que você está interessado e realizar a operação de upload, download, cópia etc.

> [!IMPORTANT]
> Os nomes de contêiner devem estar em minúsculas. Consulte [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Nomenclatura e referência de contêineres, blobs e metadados) para obter mais informações sobre nomes de contêiner e de blobs.

Nesta seção, você cria uma instância de objetos, cria um novo contêiner e, em seguida, define as permissões no contêiner para que os blobs sejam públicos e possam ser acessados com apenas uma URL. O contêiner é chamado de **quickstartblobs**.

Este exemplo usa [CreateIfNotExists](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createifnotexists?view=azure-dotnet) porque queremos criar um novo contêiner cada vez que a amostra for executada. Em um ambiente de produção em que você usa o mesmo contêiner em um aplicativo, é uma prática recomendada chamar **CreateIfNotExists** somente uma vez. Como alternativa, é possível criar o contêiner antecipadamente para não precisar criá-lo no código.

```csharp
// Load the connection string for use with the application. The storage connection string is stored
// in an environment variable on the machine running the application called storageconnectionstring.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");
if (storageConnectionString == null)
{
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add a environment variable name 'storageconnectionstring' with the actual storage " +
        "connection string as a value.");
}
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

// Create the CloudBlobClient that is used to call the Blob Service for that storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs'. 
cloudBlobContainer = cloudBlobClient.GetContainerReference("quickstartblobs" + Guid.NewGuid().ToString());
await cloudBlobContainer.CreateIfNotExistsAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-the-container"></a>Carregar blobs para o contêiner

O Armazenamento de Blobs dá suporte a blobs de blocos, blobs de acréscimo e blobs de páginas. Os blobs de bloco são utilizados com mais frequência e é o que é usado nesse guia de início rápido.

Para carregar um arquivo em um blob, obtenha uma referência para o blob no contêiner de destino. Após obter a referência de blob, você pode carregar dados nele usando [CloudBlockBlob.UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync). Essa operação cria o blob, se ele ainda não existir, ou o substitui, se ele já existir.

O código de exemplo cria um arquivo local a ser usado para o upload e para o download, armazenando o arquivo a ser carregado como **fileAndPath** e o nome do blob no **localFileName**. O exemplo a seguir carrega o arquivo para seu contêiner chamado **quickstartblobs**.

```csharp
// Create a file in MyDocuments to test the upload and download.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the location where the blob is going to go, then upload the file.
// Upload the file you created, use localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

Há vários métodos de upload que você pode usar com o Armazenamento de Blobs. Por exemplo, se você tiver um fluxo de memória, poderá usar o método UploadFromStreamAsync em vez de UploadFromFileAsync.

Blobs de bloco podem ser qualquer tipo de arquivo binário ou de texto. Os blobs de páginas são usados principalmente para os arquivos VHD usados auxiliar as VMs IaaS. Os blobs de acréscimo são usados para registro em log, como quando você quer gravar em um arquivo e depois adicionar mais informações. A maioria dos objetos armazenados no Armazenamento de Blobs são blobs de blocos.

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Obtenha uma lista de arquivos no contêiner usando [CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync). O código a seguir recupera a lista de blobs e a percorre, mostrando os URIs dos blobs encontrados. Você pode copiar o URI da janela de comando e colá-lo em um navegador para exibir o arquivo.

Se você tiver 5.000 blobs ou menos no contêiner, todos os nomes de blob serão recuperados em uma chamada para ListBlobsSegmentedAsync. Se você tiver mais de 5.000 blobs no contêiner, o serviço recuperará a lista em conjuntos de 5.000 até que todos os nomes de blob tenham sido recuperados. Portanto, na primeira vez que essa API é chamada, ela retorna os primeiros 5.000 nomes de blobs e um token de continuação. Na segunda vez, você fornece o token e o serviço recupera o próximo conjunto de nomes de blobs e assim por diante até que o token de continuação seja nulo, o que indica que todos os nomes de blobs foram recuperados.

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
    blobContinuationToken = results.ContinuationToken;
} while (blobContinuationToken != null);

```

### <a name="download-blobs"></a>Baixar blobs

Baixe os blobs para o disco local usando [CloudBlob.DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync).

O código a seguir baixa o blob carregado em uma seção anterior, adicionando um sufixo de "_DOWNLOADED" ao nome do blob para que você possa ver ambos os arquivos no disco local.

```csharp
// Download blob. In most cases, you would have to retrieve the reference
//   to cloudBlockBlob here. However, we created that reference earlier, and 
//   haven't changed the blob we're interested in, so we can reuse it. 
// First, add a _DOWNLOADED before the .txt so you can see both files in MyDocuments.
destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);  
```

### <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais dos blobs carregados neste guia de início rápido, poderá excluir o contêiner inteiro usando [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync). Também exclua os arquivos criados se eles não forem mais necessários.

```csharp
Console.WriteLine("Press the 'Enter' key to delete the sample files, example container, and exit the application.");
Console.ReadLine();
// Clean up resources. This includes the container and the two temp files.
Console.WriteLine("Deleting the container");
if (cloudBlobContainer != null)
{
    await cloudBlobContainer.DeleteIfExistsAsync();
}
Console.WriteLine("Deleting the source, and downloaded files");
File.Delete(sourceFile);
File.Delete(destinationFile);
```

## <a name="next-steps"></a>Próximas etapas

Nesse guia de início rápido, você aprendeu a transferir arquivos entre o disco local e Armazenamento de Blobs do Azure usando o .NET. Para saber mais sobre como trabalhar com o Armazenamento de Blobs, prossiga para as instruções do Armazenamento de Blobs.

> [!div class="nextstepaction"]
> [Instruções de operações do Armazenamento de Blobs](storage-dotnet-how-to-use-blobs.md)

Para obter exemplos de código adicionais do Armazenamento do Azure que podem ser baixados e executados, consulte a lista de [amostras do Armazenamento do Azure que usam o .NET](../common/storage-samples-dotnet.md).

Para obter mais informações sobre o Gerenciador de Armazenamento e os Blobs, consulte [Gerenciar os recursos de Armazenamento de Blobs do Azure com o Gerenciador de Armazenamento](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
