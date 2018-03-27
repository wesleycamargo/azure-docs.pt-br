---
title: Início rápido do Azure - Carregar, baixar e listar blobs no Armazenamento do Azure usando .NET | Microsoft Docs
description: Neste guia de início rápido, você criará uma conta de armazenamento e um contêiner. Em seguida, você deve usar a biblioteca de clientes de armazenamento para .NET a fim de carregar um blob no Armazenamento do Azure, baixar um blob e listar os blobs em um contêiner.
services: storage
author: tamram
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 03/15/2018
ms.author: tamram
ms.openlocfilehash: 716e61840f4bfb5a68a995683e67dae0b43d3854
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-net"></a>Início rápido: Carregar, baixar e listar blobs usando .NET

Neste guia de início rápido, você aprende a usar a biblioteca de clientes do .NET para o Armazenamento do Azure para carregar, baixar e listar blobs de bloco em um contêiner.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>pré-requisitos

Para concluir este início rápido, primeiro crie uma conta de armazenamento do Azure no [portal do Azure](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM). Para obter ajuda sobre como criar a conta, confira [Criar uma conta de armazenamento](../common/storage-quickstart-create-account.md).

Em seguida, baixe e instale o .NET Core 2.0 para seu sistema operacional. Você também pode optar por instalar um editor para usar com o sistema operacional.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

- Instalar o [.NET Core para Windows](https://www.microsoft.com/net/download/windows/build) 
- Opcionalmente, instalar o [Visual Studio para Windows](https://www.visualstudio.com/) 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

- Instalar o [.NET Core para Linux](https://www.microsoft.com/net/download/linux/build)
- Opcionalmente, instalar o [Visual Studio Code](https://www.visualstudio.com/) e a [extensão C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp&dotnetid=963890049.1518206068)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

- Instalar o [.NET Core para macOS](https://www.microsoft.com/net/download/macos/build).
- Opcionalmente, instalar o [Visual Studio para Mac](https://www.visualstudio.com/vs/visual-studio-mac/)

---

## <a name="download-the-sample-application"></a>Baixar o aplicativo de exemplo

O aplicativo de exemplo usado neste guia de início rápido é um aplicativo de console básico. Você pode explorar o aplicativo de exemplo no [GitHub](https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart).

Use o [git](https://git-scm.com/) para baixar uma cópia do aplicativo para seu ambiente de desenvolvimento. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

Este comando clona o repositório para sua pasta do git local. Para abrir a solução do Visual Studio, procure a pasta storage-blobs-dotnet-quickstart, abra-a e clique duas vezes em storage-blobs-dotnet-quickstart.sln. 

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de conexão de armazenamento

Para executar o aplicativo, você deve fornecer a cadeia de conexão para sua conta de armazenamento. Você pode armazenar essa cadeia de conexão dentro de uma variável de ambiente no computador local que está executando o aplicativo. Crie a variável de ambiente usando um dos exemplos a seguir, dependendo do seu sistema operacional. Substitua `<yourconnectionstring>` pela cadeia de conexão real.

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export storageconnectionstring=<yourconnectionstring>
```

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Edite seu .bash_profile e adicione a variável de ambiente:

```
export STORAGE_CONNECTION_STRING=
```

Depois de adicionar a variável de ambiente, saia e entre novamente para que as alterações entrem em vigor. Como alternativa, você pode digitar "source .bash_profile" no terminal.

---

## <a name="run-the-sample"></a>Execute o exemplo

Este exemplo cria um arquivo de teste na sua pasta local **Meus documentos** e carrega-o no armazenamento de blobs. Em seguida, o exemplo lista os blobs no contêiner e baixa o arquivo com um novo nome para que você possa comparar os arquivos novos e antigos. 

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

Quando você pressiona a tecla **Enter**, o aplicativo exclui o contêiner de armazenamento e os arquivos. Antes de excluí-los, verifique os dois arquivos na pasta **Meus documentos**. Você pode abri-los e ver se eles são idênticos. Copie a URL do blob da janela do console e cole-a em um navegador para exibir o conteúdo do blob.

Depois de verificar os arquivos, pressione qualquer tecla para concluir a demonstração e excluir os arquivos de teste. Agora que você sabe o que o exemplo faz, abra o arquivo Program.cs para examinar o código. 

## <a name="understand-the-sample-code"></a>Entender o código de exemplo

Em seguida, vamos percorrer o código de exemplo para que você possa entender como ele funciona.

### <a name="try-parsing-the-connection-string"></a>Tentar analisar a cadeia de conexão

A primeira coisa que o exemplo faz é verificar se a variável de ambiente contém uma cadeia de conexão que pode ser analisada para criar um objeto [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) apontando para a conta de armazenamento. Para verificar se a cadeia de conexão é válida, use o método [TryParse](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.tryparse). Se **TryParse** for bem-sucedido, ele inicializará a variável *storageAccount* e retornará **true**.

```csharp
// Retrieve the connection string for use with the application. The storage connection string is stored
// in an environment variable on the machine running the application called storageconnectionstring.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring", EnvironmentVariableTarget.User);

// Check whether the connection string can be parsed.
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with operations against Blob storage here.
    ...
}
else
{
    // Otherwise, let the user know that they need to define the environment variable.
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add a environment variable named 'storageconnectionstring' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the sample application.");
    Console.ReadLine();
}
```

### <a name="create-the-container-and-set-permissions"></a>Criar um contêiner e definir permissões

Em seguida, o exemplo cria um contêiner e define suas permissões para que todos os blobs no contêiner fiquem públicos. Se um blob é público, ele pode ser acessado anonimamente por qualquer cliente.

Para criar o contêiner, primeiro crie uma instância do objeto [CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient), que aponta para o armazenamento de blobs na sua conta de armazenamento. Em seguida, crie uma instância do objeto [CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer) e crie o contêiner. 

Nesse caso, o exemplo chama o método [CreateAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createasync) para criar o contêiner. Um valor de GUID é acrescentado ao nome do contêiner para garantir que ele seja exclusivo. Em um ambiente de produção, geralmente é preferível só usar o método [CreateIfNotExistsAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createifnotexistsasync) para criar um contêiner se ele ainda não existir para evitar conflitos de nomenclatura.

> [!IMPORTANT]
> Os nomes de contêiner devem estar em minúsculas. Para saber mais sobre como nomear contêineres e blobs, veja [Nomenclatura e referência de contêineres, blobs e metadados](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).


```csharp
// Create the CloudBlobClient that represents the Blob storage endpoint for the storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs' and append a GUID value to it to make the name unique. 
cloudBlobContainer = cloudBlobClient.GetContainerReference("quickstartblobs" + Guid.NewGuid().ToString());
await cloudBlobContainer.CreateAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-the-container"></a>Carregar blobs para o contêiner

Em seguida, o exemplo carrega um arquivo local para um blob de blocos. O exemplo de código obtém uma referência a um objeto **CloudBlockBlob** chamando o método [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference) no contêiner criado na seção anterior. Em seguida, carrega o arquivo selecionado para o blob chamando o método [UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync). Esse método criará o blob se ele ainda não existir e o substituirá se já existir. 

```csharp
// Create a file in your local MyDocuments folder to upload to a blob.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the blob address, then upload the file to the blob.
// Use the value of localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

O exemplo lista blobs no contêiner usando o método [ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync). No caso do exemplo, apenas um blob foi adicionado ao contêiner e, portanto, a operação de listagem retorna apenas um blob.

Se houver muitos blobs em uma chamada de retorno (por padrão, mais de 5.000), o método **ListBlobsSegmentedAsync** retornará um segmento do conjunto de resultados total e um token de continuação. Para recuperar o próximo segmento de blobs, forneça o token de continuação retornado pela chamada anterior e assim por diante, até que o token de continuação seja nulo. Um token de continuação nulo indica que todos os blobs foram recuperados. O código de exemplo mostra como usar a continuação do token para fins de melhores práticas.

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    // Get the value of the continuation token returned by the listing call.
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
    blobContinuationToken = results.ContinuationToken;
} while (blobContinuationToken != null); // Loop while the continuation token is not null. 

```

### <a name="download-blobs"></a>Baixar blobs

Em seguida, o exemplo baixa o blob criado anteriormente para o sistema de arquivos local usando o método [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync). O código de exemplo adiciona um sufixo "_DOWNLOADED" ao nome do blob para que você possa ver os dois arquivos no sistema de arquivos local.

```csharp
// Download the blob to a local file, using the reference created earlier. 
// Append the string "_DOWNLOADED" before the .txt extension so that you can see both files in MyDocuments.
destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);  
```

### <a name="clean-up-resources"></a>Limpar recursos

O exemplo limpa os recursos que ele criou ao excluir o contêiner inteiro usando [CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync). Você também pode excluir arquivos locais se desejar.

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

## <a name="resources-for-developing-net-applications-with-blobs"></a>Recursos para desenvolvimento de aplicativos .NET com blobs

Consulte estes recursos adicionais para o desenvolvimento em .NET com armazenamento de blobs:

### <a name="binaries-and-source-code"></a>Binários e código-fonte

- Baixe o pacote NuGet para a versão mais recente da [biblioteca de clientes .NET](https://www.nuget.org/packages/WindowsAzure.Storage/) para Armazenamento do Azure. 
- Exiba o [código-fonte da biblioteca de clientes .NET](https://github.com/Azure/azure-storage-net) no GitHub.

### <a name="client-library-reference-and-samples"></a>Exemplos e referência da biblioteca de clientes

- Confira a [referência da API .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage) para saber mais sobre a biblioteca de clientes .NET.
- Explore [exemplos de armazenamento de Blobs](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob) gravados usando a biblioteca de clientes de .NET.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido você aprendeu a carregar, baixar e listar blobs usando .NET. 

Para saber como criar um aplicativo Web que carrega uma imagem no armazenamento de blobs, continue para [Carregar dados de imagem na nuvem com o Armazenamento do Azure](storage-upload-process-images.md).

> [!div class="nextstepaction"]
> [Instruções de operações do Armazenamento de Blobs](storage-dotnet-how-to-use-blobs.md)

- Para saber mais sobre o núcleo do .NET, confira [Introdução ao .NET em 10 minutos](https://www.microsoft.com/net/learn/get-started/).
- Para explorar um aplicativo de exemplo que você pode implantar do Visual Studio para Windows, confira o [exemplo de aplicativo Web de galeria de fotos do .NET com o Armazenamento de Blobs do Azure](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/).
 