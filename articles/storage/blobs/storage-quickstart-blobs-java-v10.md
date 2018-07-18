---
title: Início Rápido do Azure - Criar um blob no armazenamento de objetos usando o SDK de Armazenamento de Java V10 | Microsoft Docs
description: Neste início rápido você irá criar um contêiner no armazenamento de objetos (Blob), carregar um arquivo, listar objetos e baixar usando o SDK de Armazenamento de Java.
services: storage
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 07/02/2018
ms.author: rogarana
ms.openlocfilehash: a789269e73e1817f6a45e1e5948dbfaa21efd283
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38704463"
---
# <a name="quickstart-upload-download-and-list-blobs-using-the-java-sdk-v10-preview"></a>Início Rápido: carregar, baixar e listar blobs usando o SDK de Java V10 (Versão prévia)

Neste início rápido, você aprende a usar o novo SDK de Armazenamento de Java para carregar, baixar e listar blobs de blocos em um contêiner no Armazenamento de Blobs do Azure. O novo SDK de Java utiliza o modelo de programação reativa com RxJava fornecendo operações assíncronas. Saiba mais sobre o RxJava [aqui](https://github.com/ReactiveX/RxJava). 

## <a name="prerequisites"></a>pré-requisitos

Para concluir este guia de início rápido:

* Instale e configure o [Maven](http://maven.apache.org/download.cgi) para trabalhar usando a linha de comando ou qualquer Java IDE de sua preferência
* Instalar e configurar um [JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Baixar o aplicativo de exemplo

O [aplicativo de exemplo](https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart) usado neste guia de início rápido é um aplicativo de console básico. 

Use o [git](https://git-scm.com/) para baixar uma cópia do aplicativo para seu ambiente de desenvolvimento.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-v10-quickstart.git
```

Este comando clona o repositório para sua pasta do git local.

Após o projeto ser importado, abra **Quickstart.java** (localizado em **src/main/java/quickstart**).

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de conexão de armazenamento
Essa solução requer que o nome e a chave da conta de armazenamento sejam armazenados com segurança em variáveis de ambiente locais no computador que está executando o exemplo. Siga um dos exemplos abaixo, dependendo do seu sistema operacional para criar as variáveis de ambiente.

### <a name="for-linux"></a>Para Linux

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

### <a name="for-windows"></a>Para Windows

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

## <a name="run-the-sample"></a>Execute o exemplo

Esse exemplo cria um arquivo de teste em seu diretório padrão (AppData\Local\Temp, para usuários do Windows), em seguida solicita que você insira comandos para carregar o arquivo de teste no Armazenamento de Blobs, listar os blobs no contêiner e baixar o arquivo carregado com um novo nome para que você possa comparar os arquivos novos e antigos. 

Se você deseja executar o exemplo usando Maven na linha de comando, abra um shell e navegue até **storage-blobs-java-v10-quickstart** dentro do seu diretório clonado. Em seguida, digite `mvn compile exec:java`.

Este é um exemplo de saída se você executar o aplicativo no Windows.

```
Created quickstart container
Enter a command
(P)utBlob | (L)istBlobs | (G)etBlob | (D)eleteBlobs | (E)xitSample
# Enter a command :
P
Uploading the sample file into the container: https://<storageaccount>.blob.core.windows.net/quickstart
# Enter a command :
L
Listing blobs in the container: https://<storageaccount>.blob.core.windows.net/quickstart
Blob name: SampleBlob.txt
# Enter a command :
G
Get the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
The blob was downloaded to C:\Users\<useraccount>\AppData\Local\Temp\downloadedFile13097087873115855761.txt
# Enter a command :
D
Delete the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt

# Enter a command :
>> Blob deleted: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
E
Cleaning up the sample and exiting!
```

Você tem controle sobre o exemplo. Insira comandos para que ele execute o código. Lembre-se de que as entras são sensíveis a maiúsculas e minúsculas.

Você também pode usar uma ferramenta como o [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para exibir os arquivos no Armazenamento de Blobs. O Gerenciador de Armazenamento do Azure é uma ferramenta gratuita de multiplataforma que permite que você acesse as informações da sua conta de armazenamento. 

Após ter verificado os arquivos, **E** e pressione Enter para concluir a demonstração e excluir os arquivos de teste. Agora que você sabe o que o exemplo faz, abra o arquivo **Quickstart.java** para ver o código. 

## <a name="understand-the-sample-code"></a>Entender o código de exemplo

Em seguida, vamos percorrer o código de exemplo para que você entenda como ele funciona.

### <a name="get-references-to-the-storage-objects"></a>Obter referências a objetos de armazenamento

A primeira coisa a fazer é criar as referências aos objetos usados para acessar e gerenciar o Armazenamento de Blobs. Esses objetos dependem uns dos outros, cada um é usado pelo próximo na lista.

* Crie uma instância do objeto StorageURL apontando para a conta de armazenamento.

    O objeto [**StorageURL**](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._storage_u_r_l?view=azure-java-preview) é uma representação da sua conta de armazenamento que permite a geração de um novo pipeline. Um pipeline ([pipeline HTTP](https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview#url-types--http-pipeline)) é um conjunto de políticas que são usadas para manipular solicitações e respostas com mecanismos de autorização, registro em log e repetição. Com o pipeline é possível criar uma instância do objeto [**ServiceURL**](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._service_u_r_l?view=azure-java-preview), que permite a criação de uma instância de [**ContainerURL**](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-preview), necessária para executar operações nos contêineres de Blob.

* Crie uma instância do objeto ContainerURL, representando o contêiner que você está acessando. Os contêineres são usados para organizar seus blobs da mesma forma que você usa pastas no seu computador para organizar seus arquivos.

    O **ContainerURL** fornece um ponto de acesso ao serviço de contêiner. Com o **ContainerURL** é possível criar uma instância do objeto [**BlobURL**](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l?view=azure-java-preview), necessária para criar blobs.

* Crie uma instância do objeto [BlobURL](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._blob_u_r_l?view=azure-java-preview), que aponta para o blob específico no qual você está interessado.

> [!IMPORTANT]
> Os nomes de contêiner devem estar em minúsculas. Consulte [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Nomenclatura e referência de contêineres, blobs e metadados) para obter mais informações sobre nomes de contêiner e de blobs.

### <a name="create-a-container"></a>Criar um contêiner 

Nesta seção, você cria uma instância de ContainerURL, e cria um novo contêiner com ele. O contêiner do exemplo é chamado de **quickstart**. 

Este exemplo usa [containerURL.create](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l.create?view=azure-java-preview) porque queremos criar um novo contêiner cada vez que o exemplo for executado. Como alternativa, é possível criar o contêiner antecipadamente para não precisar criá-lo no código.

```java
// Create a ServiceURL to call the Blob service. We will also use this to construct the ContainerURL
SharedKeyCredentials creds = new SharedKeyCredentials(accountName, accountKey);
// We are using a default pipeline here, you can learn more about it at https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview
final ServiceURL serviceURL = new ServiceURL(new URL("http://" + accountName + ".blob.core.windows.net"), StorageURL.createPipeline(creds, new PipelineOptions()));

// Let's create a container using a blocking call to Azure Storage
// If container exists, we'll catch and continue
containerURL = serviceURL.createContainerURL("quickstart");

try {
    ContainersCreateResponse response = containerURL.create(null, null).blockingGet();
    System.out.println("Container Create Response was " + response.statusCode());
} catch (RestException e){
    if (e instanceof RestException && ((RestException)e).response().statusCode() != 409) {
        throw e;
    } else {
        System.out.println("quickstart container already exists, resuming...");
    }
}
```

### <a name="upload-blobs-to-the-container"></a>Carregar blobs para o contêiner

O Armazenamento de Blobs dá suporte a blobs de blocos, blobs de acréscimo e blobs de páginas. Os blobs de bloco são utilizados com mais frequência e é o que é usado nesse guia de início rápido. 

Para carregar um arquivo em um blob, obtenha uma referência para o blob no contêiner de destino. Após ter a referência do blob, você pode carregar um arquivo nele usando APIs de baixo nível como [Upload](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.upload?view=azure-java-preview), também conhecida como PutBlob, [StageBlock](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._block_blob_u_r_l.stageblock?view=azure-java-preview#com_microsoft_azure_storage_blob__block_blob_u_r_l_stageBlock_String_Flowable_ByteBuffer__long_LeaseAccessConditions_), também conhecida como PutBLock-, na instância do BlockBlobURL, ou APIs de alto nível fornecidas na classe [TransferManager](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._transfer_manager?view=azure-java-preview), como o método [TransferManager.uploadFileToBlockBlob](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._transfer_manager.uploadfiletoblockblob?view=azure-java-preview). Essa operação cria o blob, se ele ainda não existir, ou o substitui, se ele já existir.

O código de exemplo cria um arquivo local a ser usado para upload e download. Armazenando o arquivo a ser carregado como **sourceFile** e a URL do blob em **blob**. O exemplo a seguir carrega o arquivo no seu contêiner chamado **quickstart**.

```java
static void uploadFile(BlockBlobURL blob, File sourceFile) throws IOException {
    
    FileChannel fileChannel = FileChannel.open(sourceFile.toPath());
            
    // Uploading a file to the blobURL using the high-level methods available in TransferManager class
    // Alternatively call the Upload/StageBlock low-level methods from BlockBlobURL type
    TransferManager.uploadFileToBlockBlob(fileChannel, blob, 8*1024*1024, null)
        .subscribe(response-> {
            System.out.println("Completed upload request.");
            System.out.println(response.response().statusCode());
        });
}
```

Blobs de bloco podem ser qualquer tipo de arquivo binário ou de texto. Os blobs de páginas são usados principalmente para os arquivos VHD usados auxiliar as VMs IaaS. A opção Acrescentar blobs é usada para acrescentar dados no final e, muitas vezes, isso é usado para registro em log. A maioria dos objetos armazenados no Armazenamento de Blobs são blobs de blocos.

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

É possível obter uma lista de objetos em um contêiner usando [containerURL.listBlobsFlatSegment](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l.listblobsflatsegment?view=azure-java-preview). Esse método retorna até 5.000 objetos de uma só vez junto com um marcador (marcador de continuação) se houver mais objetos a serem listados no contêiner. Para fazer isso, criamos uma função auxiliar que chama ela mesma repetidamente enquanto houver um marcador de continuação na resposta de listBlobsFlatSegment anterior.

```java
static void listBlobs(ContainerURL containerURL) {
    // Each ContainerURL.listBlobsFlatSegment call return up to maxResults (maxResults=10 passed into ListBlobOptions below).
    // To list all Blobs, we are creating a helper static method called listAllBlobs, 
    // and calling it after the initial listBlobsFlatSegment call
    ListBlobsOptions options = new ListBlobsOptions(null, null, 10);

    containerURL.listBlobsFlatSegment(null, options)
        .flatMap(containersListBlobFlatSegmentResponse -> 
            listAllBlobs(containerURL, containersListBlobFlatSegmentResponse))    
                .subscribe(response-> {
                    System.out.println("Completed list blobs request.");
                    System.out.println(response.statusCode());
                });
}

private static Single <ContainersListBlobFlatSegmentResponse> listAllBlobs(ContainerURL url, ContainersListBlobFlatSegmentResponse response) {                
    // Process the blobs returned in this result segment (if the segment is empty, blobs() will be null.
    if (response.body().blobs() != null) {
        for (Blob b : response.body().blobs().blob()) {
            String output = "Blob name: " + b.name();
            if (b.snapshot() != null) {
                output += ", Snapshot: " + b.snapshot();
            }
            System.out.println(output);
        }
    }
    else {
        System.out.println("There are no more blobs to list off.");
    }
    
    // If there is not another segment, return this response as the final response.
    if (response.body().nextMarker() == null) {
        return Single.just(response);
    } else {
        /*
        IMPORTANT: ListBlobsFlatSegment returns the start of the next segment; you MUST use this to get the next
        segment (after processing the current result segment
        */
            
        String nextMarker = response.body().nextMarker();

        /*
        The presence of the marker indicates that there are more blobs to list, so we make another call to
        listBlobsFlatSegment and pass the result through this helper function.
        */
            
    return url.listBlobsFlatSegment(nextMarker, new ListBlobsOptions(null, null,1))
        .flatMap(containersListBlobFlatSegmentResponse ->
            listAllBlobs(url, containersListBlobFlatSegmentResponse));
    }
}
```

### <a name="download-blobs"></a>Baixar blobs

Baixe os blobs em seu disco local usando [blobURL.download](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._blob_u_r_l.download?view=azure-java-preview).

O código a seguir baixa o blob carregado em uma seção anterior, adicionando um sufixo de "_DOWNLOADED" ao nome do blob para que você possa ver ambos os arquivos no disco local. 

```java
static void getBlob(BlockBlobURL blobURL, File sourceFile) {
    try {
        // Get the blob using the low-level download method in BlockBlobURL type
        // com.microsoft.rest.v2.util.FlowableUtil is a static class that contains helpers to work with Flowable
        blobURL.download(new BlobRange(0, Long.MAX_VALUE), null, false)
            .flatMapCompletable(response -> {
                AsynchronousFileChannel channel = AsynchronousFileChannel.open(Paths
                    .get(sourceFile.getPath()), StandardOpenOption.CREATE,  StandardOpenOption.WRITE);
                        return FlowableUtil.writeFile(response.body(), channel);
            }).doOnComplete(()-> System.out.println("The blob was downloaded to " + sourceFile.getAbsolutePath()))
            // To call it synchronously add .blockingAwait()
            .subscribe();
    } catch (Exception ex){
        System.out.println(ex.toString());
    }
}
```

### <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais dos blobs carregados neste início rápido, é possível excluir o contêiner usando [containerURL.delete](https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.storage.blob._container_u_r_l.delete?view=azure-java-preview). Esse método também exclui os arquivos no contêiner.

```java
containerURL.delete(null).blockingGet();
```

## <a name="next-steps"></a>Próximas etapas

Nesse guia de início rápido, você aprendeu a transferir arquivos entre um disco local e o Armazenamento de Blobs do Azure usando Java. 

> [!div class="nextstepaction"]
> [SDK de Armazenamento V10 para código-fonte em Java](https://github.com/Azure/azure-storage-java/tree/New-Storage-SDK-V10-Preview)
> [Referência de API](https://docs.microsoft.com/en-us/java/api/storage/client?view=azure-java-preview)
> [Saiba mais sobre o RxJava](https://github.com/ReactiveX/RxJava)