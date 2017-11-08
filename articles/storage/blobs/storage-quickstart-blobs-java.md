---
title: "Guia de início rápido do Azure – Transferir objetos para/do armazenamento de Blobs do Azure usando Java | Microsoft Docs"
description: Aprenda rapidamente a transferir objetos de/para o Armazenamento de Blobs do Azure usando Java
author: roygara
manager: timlt
services: storage
ms.service: storage
ms.topic: quickstart
ms.date: 11/01/2017
ms.author: v-rogara
ms.custom: mvc
ms.openlocfilehash: 0ed27077f649df2d0673351cda708b9a358ff92b
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/02/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-java"></a>Transferir objetos de/para o Armazenamento de Blobs do Azure usando Java

Neste guia de início rápido, você aprenderá como usar Java para carregar, baixar e listar blobs de bloco em um contêiner no Armazenamento de Blobs do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* Instalar um IDE que tenha a integração Maven ou instalar e configurar o Maven para trabalhar da linha de comando
    * Este tutorial usa [Eclipse](http://www.eclipse.org/downloads/) com a configuração "Eclipse IDE para desenvolvedores de Java"
    


Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-storage-account-using-the-azure-portal"></a>Criar uma conta de armazenamento usando o Portal do Azure

Primeiro, crie uma nova conta de armazenamento de uso geral a ser usada para este guia de início rápido. 

1. Acesse o [Portal do Azure](https://portal.azure.com) e faça logon usando sua conta do Azure. 
2. No menu Hub, selecione **Novo** > **Armazenamento** > **Conta de armazenamento – blob, arquivo, tabela, fila**. 
3. Insira um nome para a conta de armazenamento. O nome deve ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas. Ele também deve ser exclusivo.
4. Configure `Deployment model` como **Resource Manager**.
5. Configure `Account kind` como **Propósito geral**.
6. Configure `Performance` como **Standard**. 
7. Configure `Replication` como **LRS (Armazenamento com redundância local)**.
8. Configure `Storage service encryption` como **Desabilitado**.
9. Configure `Secure transfer required` como **Desabilitado**.
10. Selecione sua assinatura. 
11. Para `resource group`, crie um novo e dê a ele um nome exclusivo. 
12. Selecione a `Location` a ser usada para sua conta de armazenamento.
13. Marque **Fixar no painel** e clique em **Criar** para criar sua conta de armazenamento. 

Depois que a conta de armazenamento é criada, ela é fixada ao painel. Clique nela para abri-la. Em CONFIGURAÇÕES, clique em **Chaves de acesso**. Selecione uma chave e copie-a para a área de transferência, em seguida, cole-a em um editor de texto para uso posterior. Copie o nome da sua conta de armazenamento e cole-o em um editor de texto para uso posterior.

## <a name="download-the-sample-application"></a>Baixar o aplicativo de exemplo

O [aplicativo de exemplo](https://github.com/Azure-Samples/storage-blobs-java-quickstart) usado neste guia de início rápido é um aplicativo de console básico. 

Use o [git](https://git-scm.com/) para baixar uma cópia do aplicativo para seu ambiente de desenvolvimento. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-quickstart.git
```

Este comando clona o repositório para sua pasta do git local. Para abrir o projeto, inicialize o Eclipse e feche a tela de boas-vinda. Selecione **Arquivo**, em seguida, **Abrir projetos do sistema de arquivos...** . Certifique-se de que **Detectar e configurar natureza do projeto** esteja marcado. Selecione **Diretório**, em seguida, vá até onde você armazenou o repositório clonado, dentro dele, selecione a pasta **javaBlobsQuickstart**. Verifique se o projeto **javaBlobsQuickstarts** aparece como um projeto do Eclipse, em seguida, selecione **Concluir**.

Depois que o projeto termina a importação, abra **AzureApp.java** (localizado em **blobQuickstart.blobAzureApp** dentro de **/src/main/java**) e substitua `accountname` e `accountkey` dentro da cadeia de caracteres `storageConnectionString`. Em seguida, execute o aplicativo.
     

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de conexão de armazenamento
    
No aplicativo, você deve fornecer a cadeia de conexão para sua conta de armazenamento. Abra o arquivo **AzureApp.Java**. Localize a variável `storageConnectionString`. Substitua os valores de `AccountName` e `AccountKey` na cadeia de conexão pelos valores que você salvou do Portal do Azure. Seu `storageConnectionString` deve ser semelhante ao seguinte:

```java
    public static final String storageConnectionString ="DefaultEndpointsProtocol=https;" +
     "AccountName=<Namehere>;" +
    "AccountKey=<Keyhere>";
```

## <a name="run-the-sample"></a>Execute o exemplo

Este exemplo cria um arquivo de teste em sua diretório padrão (Meus Documentos, para usuários do Windows), carrega-o para o armazenamento de Blobs, lista os blobs no contêiner e faz o download do arquivo com um novo nome para que você possa comparar o arquivo novo com antigo. 

Execute o exemplo pressionando **Ctrl+F11** no Eclipse.

Se você quiser executar o exemplo usando o Maven na linha de comando, abra um shell e vá até **blobAzureApp** dentro de seu diretório clonado. Em seguida, digite `mvn compile exec:java`.

Este é um exemplo de saída se você executar o aplicativo no Windows.

```
Location of file: C:\Users\<user>\Documents\results.txt
File has been written
URI of blob is: http://myexamplesacct.blob.core.windows.net/quickstartblobs/results.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.
```

 Antes de continuar, verifique o diretório padrão (Meus Documentos para usuários do Windows) para os dois arquivos. Você pode abri-los e ver que eles são idênticos. Copie a URL para o blob fora da janela do console e cole-a em um navegador para exibir o conteúdo do arquivo no Armazenamento de Blobs. Quando você pressiona a tecla Enter, ele exclui o contêiner de armazenamento e os arquivos.

Você também pode usar uma ferramenta como o [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para exibir os arquivos no Armazenamento de Blobs. O Gerenciador de Armazenamento do Azure é uma ferramenta gratuita de multiplataforma que permite que você acesse as informações da sua conta de armazenamento. 

Depois de verificar os arquivos, pressione a tecla Enter para concluir a demonstração e excluir os arquivos de teste. Agora que você sabe o que o exemplo faz, abra o arquivo **AzureApp.java** para examinar o código. 

## <a name="get-references-to-the-storage-objects"></a>Obter referências a objetos de armazenamento

A primeira coisa a fazer é criar as referências aos objetos usados para acessar e gerenciar o Armazenamento de Blobs. Esses objetos dependem uns dos outros, cada um é usado pelo próximo na lista.

* Crie uma instância do objeto **CloudStorageAccount** apontando para a [conta de armazenamento](/java/api/com.microsoft.azure.management.storage._storage_account). 

* Crie uma instância do objeto **CloudBlobClient**, que aponta para o [serviço Blob](/java/api/com.microsoft.azure.storage.blob._cloud_blob_client) na sua conta de armazenamento. 

* Crie uma instância do objeto **CloudBlobContainer** que representa o [contêiner](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container) que você está acessando. Os contêineres são usados para organizar seus blobs da mesma forma que você usa pastas no seu computador para organizar seus arquivos.

Assim que tiver o **CloudBlobContainer**, você poderá criar uma instância do objeto **CloudBlockBlob**, que aponta para o [blob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob) específico em que você está interessado e realizar a operação de upload, download, cópia etc.

Nesta seção, você cria uma instância de objetos, cria um novo contêiner e, em seguida, define as permissões no contêiner para que os blobs sejam públicos e possam ser acessados com apenas uma URL. O contêiner é chamado de **quickstartblobs**. 

Este exemplo usa **CreateIfNotExists** porque queremos criar um novo contêiner cada vez que a amostra for executada. Em um ambiente de produção em que você usa o mesmo contêiner em um aplicativo, é uma prática recomendada chamar **CreateIfNotExists** somente uma vez. Como alternativa, é possível criar o contêiner antecipadamente para não precisar criá-lo no código.

```java
CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
        
CloudBlobClient blobClient = storageAccount.createCloudBlobClient();

// Get a reference to a container.
// The container name must be lower case
CloudBlobContainer container = blobClient.getContainerReference("mycontainer");

// Create the container if it does not exist.
container.createIfNotExists();

// Create a permissions object.
BlobContainerPermissions containerPermissions = new BlobContainerPermissions();

// Include public access in the permissions object.
containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);

// Set the permissions on the container.
container.uploadPermissions(containerPermissions);
```

## <a name="upload-blobs-to-the-container"></a>Carregar blobs para o contêiner

O Armazenamento de Blobs dá suporte a blobs de blocos, blobs de acréscimo e blobs de páginas. Os blobs de bloco são utilizados com mais frequência e é o que é usado nesse guia de início rápido. 

Para carregar um arquivo em um blob, obtenha uma referência para o blob no contêiner de destino. Após obter a referência de blob, você pode carregar dados nele usando [CloudBlockBlob.Upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload#com_microsoft_azure_storage_blob__cloud_block_blob_upload_final_InputStream_final_long). Essa operação cria o blob, se ele ainda não existir, ou o substitui, se ele já existir.

O código de exemplo cria um arquivo local a ser usado para o upload e para o download, armazenando o arquivo a ser carregado como **origem** e o nome do blob no **blob**. O exemplo a seguir carrega o arquivo para seu contêiner chamado **quickstartblobs**.

```java
//Getting the path to user's myDocuments folder
String myDocs = FileSystemView.getFileSystemView().getDefaultDirectory().getPath();

//Creating a file in the myDocuments folder
File source = new File(myDocs + File.separator + "results.txt");
try( Writer output = new BufferedWriter(new FileWriter(source)))
{
    System.out.println("Location of file: " + source.toString());

    output.write("Hello Azure!");
    output.close();
    
    System.out.println("File has been written");
}
catch(IOException x) 
{
    System.err.println(x);
}

//Getting blob reference
CloudBlockBlob blob = container.getBlockBlobReference("results.txt");

//Creating a FileInputStream as it is necessary for the upload
FileInputStream myFile = new FileInputStream(source);

//Creating blob and uploading file to it
blob.upload( myFile, source.length());

//Closing FileInputStream
myFile.close();
```

Há vários métodos de upload que você pode usar com o Armazenamento de Blobs. Por exemplo, se você tiver um fluxo de memória, poderá usar o método UploadFromStreamAsync em vez de UploadFromFileAsync. 

Blobs de bloco podem ser qualquer tipo de arquivo binário ou de texto. Os blobs de páginas são usados principalmente para os arquivos VHD usados auxiliar as VMs IaaS. Os blobs de acréscimo são usados para registro em log, como quando você quer gravar em um arquivo e depois adicionar mais informações. A maioria dos objetos armazenados no Armazenamento de Blobs são blobs de blocos.

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Você pode obter uma lista de arquivos no contêiner usando [CloudBlobContainer.ListBlobs](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobs#com_microsoft_azure_storage_blob__cloud_blob_container_listBlobs). O código a seguir recupera a lista de blobs e a percorre, mostrando os URIs dos blobs encontrados. Você pode copiar o URI da janela de comando e colá-lo em um navegador para exibir o arquivo.

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

## <a name="download-blobs"></a>Baixar blobs

Faça download dos blobs para o disco local usando [CloudBlob.DownloadToFile](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.downloadtofile#com_microsoft_azure_storage_blob__cloud_blob_downloadToFile_final_String).

O código a seguir baixa o blob carregado em uma seção anterior, adicionando um sufixo de "_DOWNLOADED" ao nome do blob para que você possa ver ambos os arquivos no disco local. 

```java
// Download blob. In most cases, you would have to retrieve the reference
// to cloudBlockBlob here. However, we created that reference earlier, and 
// haven't changed the blob we're interested in, so we can reuse it. 
// First, add a _DOWNLOADED before the .txt so you can see both files in your default directory.
blob.downloadToFile(myDocs + File.separator + "results_DOWNLOADED.txt");
```

## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais dos blobs carregados neste guia de início rápido, poderá excluir o contêiner inteiro usando [CloudBlobContainer.DeleteIfExists](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.deleteifexists#com_microsoft_azure_storage_blob__cloud_blob_container_deleteIfExists). Isso também exclui os arquivos no contêiner.

```java
//Deletes container if it exists then deletes files created locally
container.deleteIfExists();
downloadedFile.deleteOnExit();
sourceFile.deleteOnExit();
```

## <a name="next-steps"></a>Próximas etapas

Nesse guia de início rápido, você aprendeu a transferir arquivos entre um disco local e o Armazenamento de Blobs do Azure usando Java. Para saber mais sobre como trabalhar com o Armazenamento de Blobs, prossiga para as instruções do Armazenamento de Blobs.

> [!div class="nextstepaction"]
> [Instruções de operações do Armazenamento de Blobs](storage-java-how-to-use-blob-storage.md)

Para obter mais informações sobre o Gerenciador de Armazenamento e os Blobs, consulte [Gerenciar os recursos de Armazenamento de Blobs do Azure com o Gerenciador de Armazenamento](../../vs-azure-tools-storage-explorer-blobs.md).