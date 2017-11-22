---
title: "Guia de início rápido do Azure – Transferir objetos para/do Armazenamento de Blobs do Azure usando o Node.js | Microsoft Docs"
description: Aprenda rapidamente a transferir objetos de/para o armazenamento de Blobs do Azure usando o Node.js
services: storage
documentationcenter: storage
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/30/2017
ms.author: gwallace
ms.openlocfilehash: dd4d3abf082767c40760d020c0997b365452e769
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-nodejs"></a>Transferir objetos de/para o armazenamento de Blobs do Azure usando o Node.js

Neste guia de início rápido, você aprenderá a usar o Node.js para carregar, baixar e listar blobs de bloco em um contêiner no Armazenamento de Blobs do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* Instale o [Node.js](https://nodejs.org/en/)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Baixar o aplicativo de exemplo

O [aplicativo de exemplo](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git) usado neste guia de início rápido é um aplicativo de console básico. 

Use o [git](https://git-scm.com/) para baixar uma cópia do aplicativo para seu ambiente de desenvolvimento.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

Este comando clona o repositório para sua pasta do git local. Para abrir a aparência de aplicativo para a pasta storage-blobs-node-quickstart, abra-o e clique duas vezes em index.js.

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de conexão de armazenamento

No aplicativo, você deve fornecer a cadeia de conexão para sua conta de armazenamento. Abra o arquivo `index.js`, localize a variável `connectionString`. Substitua seu valor com todo o valor da cadeia de conexão pelo valor salvo do Portal do Azure. Sua cadeia de conexão de armazenamento deve ser semelhante à seguinte:

```javascript
// Create a blob client for interacting with the blob service from connection string
// How to create a storage connection string - http://msdn.microsoft.com/library/azure/ee758697.aspx
var connectionString = '<Your connection string here>';
var blobService = storage.createBlobService(connectionString);
```

## <a name="install-required-packages"></a>Instalar os pacotes necessários

No diretório de aplicativo, execute `npm install` para instalar qualquer pacote necessário listado no arquivo `package.json`.

```javascript
npm install
```

## <a name="run-the-sample"></a>Execute o exemplo

Este exemplo cria um arquivo de teste em Meus Documentos, carrega-o para o Armazenamento de Blobs, lista os blobs no contêiner e baixa o arquivo com um novo nome para que você possa comparar o arquivo novo com antigo.

Execute o exemplo digitando `node index.js`. A saída a seguir é de um sistema Windows.  Uma saída semelhante com caminhos de arquivo apropriados poderá ser esperada se você estiver usando o Linux.

```
Azure Storage Node.js Client Library Blobs Quick Start

1. Creating a container with public access: quickstartcontainer-79a3eea0-bec9-11e7-9a36-614cd00ca63d

2. Creating a file in ~/Documents folder to test the upload and download

   Local File: C:\Users\admin\Documents\HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

3. Uploading BlockBlob: quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

   Uploaded Blob URL: https://mystorageaccount.blob.core.windows.net/quickstartcontainer-79a3eea0-bec9-11e7-9a36-614cd00ca63d/quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt

4. Listing blobs in container

   - quickstartblob-HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d.txt (type: BlockBlob)


5. Downloading blob

   Downloaded File: C:\Users\admin\Documents\HelloWorld-79a3c790-bec9-11e7-9a36-614cd00ca63d_DOWNLOADED.txt

Sample finished running. When you hit <ENTER> key, the temporary files will be deleted and the sample application will exit.
```

Antes de continuar, verifique se os dois documentos estão em MyDocuments. Você pode abri-los e ver que eles são idênticos.

Você também pode usar uma ferramenta como o [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para exibir os arquivos no Armazenamento de Blobs. O Gerenciador de Armazenamento do Azure é uma ferramenta gratuita de multiplataforma que permite que você acesse as informações da sua conta de armazenamento.

Depois de verificar os arquivos, pressione qualquer tecla para concluir a demonstração e excluir os arquivos de teste. Agora que você sabe o que o exemplo faz, abra o arquivo index.js para examinar o código. 

## <a name="understand-the-sample-code"></a>Entender o código de exemplo

Em seguida, vamos percorrer o código de exemplo para que você possa entender como ele funciona.

### <a name="get-references-to-the-storage-objects"></a>Obter referências a objetos de armazenamento

A primeira medida a adotar é criar a referência ao `BlobService` usado para acessar e gerenciar o armazenamento de Blobs. Esses objetos dependem uns dos outros, cada um é usado pelo próximo na lista.

* Crie uma instância do objeto **[BlobService](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService__ctor)**, que aponta para o serviço Blob na sua conta de armazenamento.

* Crie um novo contêiner e, em seguida, defina permissões no contêiner para que os blobs sejam públicos e possam ser acessados com apenas uma URL. O contêiner inicia com **quickstartcontainer-**.

Este exemplo usa [createContainerCreateIfNotExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists) porque queremos criar um novo contêiner cada vez que a amostra for executada. Em um ambiente de produção em que você usa o mesmo contêiner em um aplicativo, é uma prática recomendada chamar CreateIfNotExists somente uma vez. Como alternativa, é possível criar o contêiner antecipadamente para não precisar criá-lo no código.

```javascript
// Create a container for organizing blobs within the storage account.
console.log('1. Creating a Container with Public Access:', blockBlobContainerName, '\n');
blobService.createContainerIfNotExists(blockBlobContainerName, { 'publicAccessLevel': 'blob' }, function (error) {
    if (error) return callback(error);
```

### <a name="upload-blobs-to-the-container"></a>Carregar blobs para o contêiner

O Armazenamento de Blobs dá suporte a blobs de blocos, blobs de acréscimo e blobs de páginas. Blobs de bloco são os mais usados. Eles são ideais para armazenar texto e dados binários, o motivo pelo qual são usados neste guia de início rápido.

Para carregar um arquivo para um blob, use o método [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromLocalFile). Essa operação cria o blob, se ele ainda não existir, ou o substitui, se ele já existir.

O código de exemplo cria um arquivo local a ser usado para o upload e o download, armazenando o arquivo a ser carregado como **localPath** e o nome do blob em **localFileToUpload**. O exemplo a seguir carrega o arquivo para o contêiner que começa com **quickstartcontainer-**.

```javascript
console.log('2. Creating a file in ~/Documents folder to test the upload and download\n');
console.log('   Local File:', LOCAL_FILE_PATH, '\n');
fs.writeFileSync(LOCAL_FILE_PATH, 'Greetings from Microsoft!');

console.log('3. Uploading BlockBlob:', BLOCK_BLOB_NAME, '\n');
blobService.createBlockBlobFromLocalFile(CONTAINER_NAME, BLOCK_BLOB_NAME, LOCAL_FILE_PATH, function (error) {
handleError(error);
console.log('   Uploaded Blob URL:', blobService.getUrl(CONTAINER_NAME, BLOCK_BLOB_NAME), '\n');
```

Há vários métodos de upload que você pode usar com o Armazenamento de Blobs. Por exemplo, se você tiver um fluxo de memória, poderá usar o método [createBlockBlobFromStream](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromStream), em vez de [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_createBlockBlobFromLocalFile).

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Em seguida, o aplicativo obtém uma lista de arquivos no contêiner usando [listBlobsSegmented](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_listBlobsSegmented). O código a seguir recupera a lista de blobs e a percorre, mostrando os URIs dos blobs encontrados. Você pode copiar o URI da janela de comando e colá-lo em um navegador para exibir o arquivo.

Se você tiver 5 mil blobs ou menos no contêiner, todos os nomes de blob serão recuperados em uma chamada para [listBlobsSegmented](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_listBlobsSegmented). Se você tiver mais de 5.000 blobs no contêiner, o serviço recuperará a lista em conjuntos de 5.000 até que todos os nomes de blob tenham sido recuperados. Portanto, na primeira vez que essa API é chamada, ela retorna os primeiros 5.000 nomes de blobs e um token de continuação. Na segunda vez, você fornece o token e o serviço recupera o próximo conjunto de nomes de blobs e assim por diante até que o token de continuação seja nulo, o que indica que todos os nomes de blobs foram recuperados.

```javascript
console.log('4. Listing blobs in container\n');
blobService.listBlobsSegmented(CONTAINER_NAME, null, function (error, data) {
    handleError(error);

    for (var i = 0; i < data.entries.length; i++) {
    console.log(util.format('   - %s (type: %s)'), data.entries[i].name, data.entries[i].blobType);
    }
    console.log('\n');
```

### <a name="download-blobs"></a>Baixar blobs

Baixar blobs para seu disco local usando [getBlobToLocalFile](/nodejs/api/azure-storage/blobservice?view=azure-node-2.2.0#azure_storage_BlobService_getBlobToLocalFile).

O código a seguir baixa o blob carregado em uma seção anterior, adicionando um sufixo de "_DOWNLOADED" ao nome do blob para que você possa ver ambos os arquivos no disco local. 

```javascript
console.log('5. Downloading blob\n');
blobService.getBlobToLocalFile(CONTAINER_NAME, BLOCK_BLOB_NAME, DOWNLOADED_FILE_PATH, function (error) {
handleError(error);
console.log('   Downloaded File:', DOWNLOADED_FILE_PATH, '\n');
```

### <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais dos blobs carregados neste guia de início rápido, poderá excluir o contêiner inteiro usando [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists) e [deleteContainerIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteContainerIfExists). Também exclua os arquivos criados se eles não forem mais necessários. Isso é resolvido no aplicativo quando você pressiona Enter para sair do aplicativo.

```javascript
console.log('6. Deleting block Blob\n');
    blobService.deleteBlobIfExists(CONTAINER_NAME, BLOCK_BLOB_NAME, function (error) {
        handleError(error);

    console.log('7. Deleting container\n');
    blobService.deleteContainerIfExists(CONTAINER_NAME, function (error) {
        handleError(error);
            
        fs.unlinkSync(LOCAL_FILE_PATH);
        fs.unlinkSync(DOWNLOADED_FILE_PATH);
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a transferir arquivos entre um disco local e o armazenamento de Blobs do Azure usando o Node.js. Para saber mais sobre como trabalhar com o Armazenamento de Blobs, prossiga para as instruções do Armazenamento de Blobs.

> [!div class="nextstepaction"]
> [Instruções de operações do Armazenamento de Blobs](storage-nodejs-how-to-use-blob-storage.md)

Para obter mais informações sobre o Gerenciador de Armazenamento e os Blobs, consulte [Gerenciar os recursos de Armazenamento de Blobs do Azure com o Gerenciador de Armazenamento](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
