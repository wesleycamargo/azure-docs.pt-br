---
title: "Início Rápido do Azure – Transferir objetos de/para o Armazenamento de Blobs do Azure usando o Go | Microsoft Docs"
description: Aprenda rapidamente a transferir objetos de/para o Armazenamento de Blobs do Azure usando a linguagem Go
services: storage
author: seguler
manager: jahogg
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: go
ms.topic: quickstart
ms.date: 01/29/2018
ms.author: seguler
ms.openlocfilehash: 4ba9721dc12bc50b20ad85019b1df51a56b52ebc
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2018
---
#  <a name="transfer-objects-tofrom-azure-blob-storage-using-go"></a>Transferir objetos de/para o Armazenamento de Blobs do Azure usando o Go
Neste início rápido, você aprenderá a usar a linguagem de programação Go para carregar, baixar e listar blobs de blocos em um contêiner no Armazenamento de Blobs do Azure. 

## <a name="prerequisites"></a>pré-requisitos

Para concluir este guia de início rápido: 
* Instale o [Go 1.8 ou superior](https://golang.org/dl/)
* Baixe e instale o [SDK do Azure Storage Blob para Go](https://github.com/azure/azure-storage-blob-go/) usando `go get -u github.com/azure/azure-storage-blob-go/2016-05-31/azblob`. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Baixar o aplicativo de exemplo
O [aplicativo de exemplo](https://github.com/Azure-Samples/storage-blobs-go-quickstart.git) usado neste início rápido é um aplicativo Go básico.  

Use o [git](https://git-scm.com/) para baixar uma cópia do aplicativo para seu ambiente de desenvolvimento. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-go-quickstart 
```

Este comando clona o repositório para sua pasta do git local. Para abrir o exemplo do Go para armazenamento de Blob, procure o arquivo storage-quickstart.go.  

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de conexão de armazenamento
Essa solução requer que o nome e a chave da conta de armazenamento sejam armazenados com segurança em variáveis de ambiente locais no computador que está executando o exemplo. Siga um dos exemplos abaixo, dependendo do seu sistema operacional para criar as variáveis de ambiente.

# <a name="linuxtablinux"></a>[Linux](#tab/Linux)

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

# <a name="windowstabwindows"></a>[Windows](#tab/Windows)

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

---

## <a name="run-the-sample"></a>Execute o exemplo
Esse exemplo cria um arquivo de teste na pasta atual, carrega o arquivo de teste no Armazenamento de blobs, lista os blobs no contêiner e baixa o arquivo em um buffer. 

Para executar o exemplo, emita o seguinte comando: 

```go run storage-quickstart.go```

A saída a seguir é um exemplo da saída retornada ao executar o aplicativo:
  
```
Azure Blob storage quick start sample
Creating a container named quickstart-5568059279520899415
Creating a dummy file to test the upload and download
Uploading the file with blob name: 630910657703031215
Blob name: 630910657703031215
Downloaded the blob: hello world
this is a blob
Press the enter key to delete the sample files, example container, and exit the application.
```
Quando você pressiona a tecla para continuar, o programa de exemplo exclui o contêiner de armazenamento e os arquivos. 

> [!TIP]
> Você também pode usar uma ferramenta como o [Gerenciador de Armazenamento do Azure](http://storageexplorer.com) para exibir os arquivos no Armazenamento de Blobs. O Gerenciador de Armazenamento do Azure é uma ferramenta gratuita de multiplataforma que permite que você acesse as informações da sua conta de armazenamento. 
>

## <a name="understand-the-sample-code"></a>Entender o código de exemplo

Em seguida, vamos percorrer o código de exemplo para que você possa entender como ele funciona.

### <a name="create-containerurl-and-bloburl-objects"></a>Criar objetos ContainerURL e BlobURL
A primeira coisa a fazer é criar as referências aos objetos ContainerURL e BlobURL usados para acessar e gerenciar o Armazenamento de blobs. Esses objetos oferecem APIs de baixo nível, como Criar, PutBlob e GetBlob para emitir APIs REST.

* Use o struct **SharedKeyCredential** para armazenar suas credenciais. 

* Crie um **Pipeline** usando as credenciais e as opções. O pipeline especifica itens como políticas de repetição, registro em log, desserialização de payloads de resposta HTTP e muito mais.  

* Crie uma instância de um novo ContainerURL e um novo objeto BlobURL para executar operações em contêiner (Criar) e blobs (PutBlob e GetBlob).


Depois que tiver o ContainerURL, você pode criar uma instância do objeto **BlobURL**, que aponta para um blob, e executar operações como carregar, baixar e copiar.

> [!IMPORTANT]
> Os nomes de contêiner devem estar em minúsculas. Consulte [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Nomenclatura e referência de contêineres, blobs e metadados) para obter mais informações sobre nomes de contêiner e de blobs.

Nesta seção, você criará um novo contêiner. O contêiner é chamado de **quickstartblobs-[random string]**. 

```go 
// From the Azure portal, get your storage account name and key and set environment variables.
accountName, accountKey := os.Getenv("AZURE_STORAGE_ACCOUNT"), os.Getenv("AZURE_STORAGE_ACCESS_KEY")
if len(accountName) == 0 || len(accountKey) == 0 {
    log.Fatal("Either the AZURE_STORAGE_ACCOUNT or AZURE_STORAGE_ACCESS_KEY environment variable is not set")
}

// Create a default request pipeline using your storage account name and account key.
credential := azblob.NewSharedKeyCredential(accountName, accountKey)
p := azblob.NewPipeline(credential, azblob.PipelineOptions{})

// Create a random string for the quick start container
containerName := fmt.Sprintf("quickstart-%s", randomString())

// From the Azure portal, get your storage account blob service URL endpoint.
URL, _ := url.Parse(
    fmt.Sprintf("https://%s.blob.core.windows.net/%s", accountName, containerName))

// Create a ContainerURL object that wraps the container URL and a request
// pipeline to make requests.
containerURL := azblob.NewContainerURL(*URL, p)

// Create the container
fmt.Printf("Creating a container named %s\n", containerName)
ctx := context.Background() // This example uses a never-expiring context
_, err := containerURL.Create(ctx, azblob.Metadata{}, azblob.PublicAccessNone)
handleErrors(err)
```
### <a name="upload-blobs-to-the-container"></a>Carregar blobs para o contêiner

O Armazenamento de Blobs dá suporte a blobs de blocos, blobs de acréscimo e blobs de páginas. Os blobs de blocos são utilizados com mais frequência e são usados nesse guia de início rápido.  

Para carregar um arquivo em um blob, abra o arquivo usando **os.Open**. Depois você pode carregar o arquivo no caminho especificado usando uma das APIs REST: PutBlob, PutBlock/PutBlockList. 

Como alternativa, o SDK oferece [APIs de alto nível](https://github.com/Azure/azure-storage-blob-go/blob/master/2016-05-31/azblob/highlevel.go) que são criadas sobre as APIs REST de nível inferior. Por exemplo, a função ***UploadFileToBlockBlob*** usa operações PutBlock para carregar um arquivo em partes simultaneamente para otimizar a taxa de transferência. Se o arquivo for menor que 256 MB, ele usa o PutBlob para concluir a transferência em uma única transação.

O exemplo a seguir carrega o arquivo para seu contêiner chamado **quickstartblobs-[randomstring]**.

```go
// Here's how to upload a blob.
blobURL := containerURL.NewBlockBlobURL(fileName)
file, err := os.Open(fileName)
handleErrors(err)

// You can use the low-level PutBlob API to upload files. Low-level APIs are simple wrappers for the Azure Storage REST APIs.
// Note that PutBlob can upload up to 256MB data in one shot. Details: https://docs.microsoft.com/en-us/rest/api/storageservices/put-blob
// Following is commented out intentionally because we will instead use UploadFileToBlockBlob API to upload the blob
// _, err = blobURL.PutBlob(ctx, file, azblob.BlobHTTPHeaders{}, azblob.Metadata{}, azblob.BlobAccessConditions{})
// handleErrors(err)

// The high-level API UploadFileToBlockBlob function uploads blocks in parallel for optimal performance, and can handle large files as well.
// This function calls PutBlock/PutBlockList for files larger 256 MBs, and calls PutBlob for any file smaller
fmt.Printf("Uploading the file with blob name: %s\n", fileName)
_, err = azblob.UploadFileToBlockBlob(ctx, file, blobURL, azblob.UploadToBlockBlobOptions{
    BlockSize:   4 * 1024 * 1024,
    Parallelism: 16})
handleErrors(err)
```

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Obtenha uma lista de arquivos no contêiner usando o método **ListBlobs** em um **ContainerURL**. ListBlobs retorna um único segmento de blobs (até 5.000), começando pelo **Marcador** especificado. Use um Marcador vazio para começar a enumeração desde o início. Nomes de blob são retornados em ordem lexicográfica. Depois de obter um segmento, processe-o e depois chame o ListBlobs novamente passando o Marcador retornado anteriormente.  

```go
// List the blobs in the container
for marker := (azblob.Marker{}); marker.NotDone(); {
    // Get a result segment starting with the blob indicated by the current Marker.
    listBlob, err := containerURL.ListBlobs(ctx, marker, azblob.ListBlobsOptions{})
    handleErrors(err)

    // ListBlobs returns the start of the next segment; you MUST use this to get
    // the next segment (after processing the current result segment).
    marker = listBlob.NextMarker

    // Process the blobs returned in this result segment (if the segment is empty, the loop body won't execute)
    for _, blobInfo := range listBlob.Blobs.Blob {
        fmt.Print("Blob name: " + blobInfo.Name + "\n")
    }
}
```

### <a name="download-the-blob"></a>Baixar o blob

Baixe blobs usando o método **GetBlob** de nível inferior em um BlobURL. Como alternativa, você pode criar um Fluxo e ler os intervalos dele usando uma API **NewDownloadStream** de alto nível fornecida no [highlevel.go](https://github.com/Azure/azure-storage-blob-go/blob/master/2016-05-31/azblob/highlevel.go). A função NewDownloadStream tenta novamente em caso de falha de conexão, enquanto a API Get Blob só tenta novamente em códigos de status HTTP como o 503 (Servidor Ocupado). O código a seguir baixa o blob usando a função **NewDownloadStream**. O conteúdo do blob é gravado em um buffer e mostrado no console.

```go
// Here's how to download the blob. NOTE: This method automatically retries if the connection fails
// during download (the low-level GetBlob function does NOT retry errors when reading from its stream).
stream := azblob.NewDownloadStream(ctx, blobURL.GetBlob, azblob.DownloadStreamOptions{})
downloadedData := &bytes.Buffer{}
_, err = downloadedData.ReadFrom(stream)
handleErrors(err)
```

### <a name="clean-up-resources"></a>Limpar recursos
Se você não precisar mais dos blobs carregados neste início rápido, poderá excluir todo o contêiner usando o método **Delete**. 

```go
// Cleaning up the quick start by deleting the container and the file created locally
fmt.Printf("Press the enter key to delete the sample files, example container, and exit the application.\n")
bufio.NewReader(os.Stdin).ReadBytes('\n')
fmt.Printf("Cleaning up.\n")
containerURL.Delete(ctx, azblob.ContainerAccessConditions{})
file.Close()
os.Remove(fileName)
```

## <a name="next-steps"></a>Próximas etapas
 
Neste início rápido, você aprendeu a transferir arquivos entre o disco local e o armazenamento de blobs do Azure usando o Go. Para obter mais informações sobre o SDK do Azure Storage Blob, consulte o [Código-Fonte](https://github.com/Azure/azure-storage-blob-go/) e a [Referência da API](https://godoc.org/github.com/Azure/azure-storage-blob-go/2016-05-31/azblob).
