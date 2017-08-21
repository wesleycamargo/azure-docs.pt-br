---
title: Desenvolvimento para o Armazenamento de Arquivos do Azure com Java | Microsoft Docs
description: "Saiba como desenvolver aplicativos e serviços Java que usam o Armazenamento de Arquivos do Azure para armazenar dados de arquivo."
services: storage
documentationcenter: java
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 3bfbfa7f-d378-4fb4-8df3-e0b6fcea5b27
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 05/27/2017
ms.author: robinsh
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 16924599e49990265e07f7a58613756d93c46942
ms.contentlocale: pt-br
ms.lasthandoff: 07/12/2017

---

# <a name="develop-for-azure-file-storage-with-java"></a>Desenvolvimento para o Armazenamento de Arquivos do Azure com Java
[!INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>Sobre este tutorial
Este tutorial demonstrará as noções básicas de usar Java para desenvolver aplicativos ou serviços que usam o Armazenamento de Arquivos do Azure para armazenar dados de arquivo. Neste tutorial, criaremos um aplicativo de console simples e mostraremos como executar ações básicas com Java e o Armazenamento de Arquivos do Azure:

* Criar e excluir Compartilhamentos de Arquivos do Azure
* Criar e excluir diretórios
* Enumerar arquivos e diretórios em um Compartilhamento de Arquivos do Azure
* Carregar, baixar e excluir um arquivo

> [!Note]  
> Como o Armazenamento de Arquivos do Azure pode ser acessado via SMB, é possível criar aplicativos simples que acessam o Compartilhamento de Arquivos do Azure usando as classes padrão de E/S do Java. Este artigo descreverá como criar aplicativos que usam o SDK do Java do Armazenamento do Azure, que usa a [API REST do Armazenamento de Arquivos do Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/file-service-rest-api) para se comunicar com o Armazenamento de Arquivos do Azure.

## <a name="create-a-java-application"></a>Criar um aplicativo Java
Para criar os exemplos, você precisará do JDK (Java Development Kit) e do [SDK do Armazenamento do Azure para Java][]. Você também deverá ter criado uma conta de armazenamento do Azure.

## <a name="setup-your-application-to-use-azure-file-storage"></a>Configurar seu aplicativo para usar o Armazenamento de Arquivos do Azure
Para usar as APIs de armazenamento do Azure, adicione a instrução a seguir à parte superior do arquivo Java do qual você pretende acessar o serviço de armazenamento.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="setup-an-azure-storage-connection-string"></a>Configurar uma cadeia de conexão de armazenamento do Azure
Para usar o Armazenamento de Arquivos do Azure, será necessário se conectar à sua conta de armazenamento do Azure. A primeira etapa seria configurar uma cadeia de conexão que usaremos para acessar a sua conta de armazenamento. Vamos definir uma variável estática para fazer isso.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

> [!NOTE]
> Substitua nome_da_sua_conta_de_armazenamento e chave_da_sua_conta_de_armazenamento pelos valores reais da sua conta de armazenamento.
> 
> 

## <a name="connecting-to-an-azure-storage-account"></a>Conectar-se a uma conta de armazenamento do Azure
Para se conectar à sua conta de armazenamento, você precisa usar o objeto **CloudStorageAccount**, passando uma cadeia de conexão para seu método de **análise**.

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** gera uma InvalidKeyException; portanto, você precisará colocá-lo dentro de um bloco try/catch.

## <a name="create-an-azure-file-share"></a>Criar um Compartilhamento de Arquivos do Azure
Todos os arquivos e diretórios do Armazenamento de Arquivos do Azure residem em um contêiner chamado **Compartilhamento**. Sua conta de armazenamento pode ter tantos compartilhamentos quantos a capacidade da conta permitir. Para obter acesso a um compartilhamento e seu conteúdo, é necessário usar um cliente de Armazenamento de Arquivos do Azure.

```java
// Create the Azure File storage client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Usando o cliente do Armazenamento de Arquivos do Azure, será possível obter uma referência a um compartilhamento.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

Para realmente criar o compartilhamento, use o método **createIfNotExists** do objeto CloudFileShare.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

Neste ponto, **compartilhar** contém uma referência a um compartilhamento chamado **sampleshare**.

## <a name="delete-an-azure-file-share"></a>Excluir um Compartilhamento de Arquivos do Azure
Para excluir um compartilhamento deve-se chamar o método **deleteIfExists** em um objeto CloudFileShare. O código fornecido a seguir é um exemplo de como fazer isso.

```java
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
```

## <a name="create-a-directory"></a>Criar um diretório
Você também pode organizar o armazenamento colocando arquivos em subdiretórios em vez de manter todos eles no diretório raiz. O Armazenamento de Arquivos do Azure permite criar que você crie quantos diretórios a conta permitir. O código a seguir criará um subdiretório chamado **sampledir** no diretório raiz.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the sampledir directory
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

if (sampleDir.createIfNotExists()) {
    System.out.println("sampledir created");
} else {
    System.out.println("sampledir already exists");
}
```

## <a name="delete-a-directory"></a>Excluir um diretório
Excluir um diretório é uma tarefa bem simples, embora deva-se observar que não é possível excluir um diretório que ainda contenha arquivos ou outros diretórios.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory you want to delete
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

// Delete the directory
if ( containerDir.deleteIfExists() ) {
    System.out.println("Directory deleted");
}
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Enumerar arquivos e diretórios em um Compartilhamento de Arquivos do Azure
A lista de arquivos e diretórios em um compartilhamento pode ser obtida facilmente chamando **listFilesAndDirectories** em uma referência CloudFileDirectory. O método retorna uma lista de objetos ListFileItem nos quais você pode iterar. Como exemplo, o código a seguir lista os arquivos e diretórios contidos no diretório raiz.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>Carregar um arquivo
Um compartilhamento de Arquivos do Azure contém, no mínimo, um diretório raiz em que os arquivos podem residir. Nesta seção, você aprenderá a carregar um arquivo do armazenamento local para o diretório raiz de um compartilhamento.

A primeira etapa do carregamento de um arquivo é obter uma referência para o diretório onde ele deve residir. Para isso, você deve chamar o método **getRootDirectoryReference** do objeto de compartilhamento.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

Agora que você tem uma referência para o diretório raiz do compartilhamento, pode carregar um arquivo usando o código a seguir.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="download-a-file"></a>Baixar um arquivo
Uma das operações mais frequentes executadas no Armazenamento de Arquivos do Azure é baixar arquivos. No exemplo a seguir, o código baixa o arquivo SampleFile.txt e exibe seu conteúdo.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the directory that contains the file
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

//Get a reference to the file you want to download
CloudFile file = sampleDir.getFileReference("SampleFile.txt");

//Write the contents of the file to the console.
System.out.println(file.downloadText());
```

## <a name="delete-a-file"></a>Excluir um arquivo
Outra operação comum do Armazenamento de Arquivos do Azure é a exclusão de arquivos. O código a seguir exclui um arquivo chamado SampleFile.txt armazenado em um diretório chamado **sampledir**.

```java
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
```

## <a name="next-steps"></a>Próximas etapas
Se você quiser saber mais sobre outras APIs de armazenamento do Azure, siga estes links.

* [Central de Desenvolvedores do Java](http://azure.microsoft.com/develop/java/)
* [SDK de Armazenamento do Azure para Java](https://github.com/azure/azure-storage-java)
* [SDK de Armazenamento do Azure para Android](https://github.com/azure/azure-storage-android)
* [Referência de SDK do Cliente de Armazenamento do Azure](http://dl.windowsazure.com/storage/javadoc/)
* [API REST de serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog da equipe de Armazenamento do Azure](http://blogs.msdn.com/b/windowsazurestorage/)
* [Transferir dados com o Utilitário de Linha de Comando AzCopy](storage-use-azcopy.md)
