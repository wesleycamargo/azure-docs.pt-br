---
title: Início rápido do Azure - Carregar, baixar e listar blobs no Armazenamento do Azure usando Node.js | Microsoft Docs
description: Neste guia de início rápido, você criará uma conta de armazenamento e um contêiner. Em seguida, você deve usar a biblioteca de clientes de armazenamento para Node.js a fim de carregar um blob no Armazenamento do Azure, baixar um blob e listar os blobs em um contêiner.
services: storage
author: craigshoemaker
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 03/15/2018
ms.author: cshoe
ms.openlocfilehash: 8783b83a1a94caf4a49f9da7a2dd30c9cb52df22
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-nodejs"></a>Início rápido: Carregar, baixar e listar blobs usando Node.js

Neste início rápido, você aprenderá a usar o Node.js para carregar, baixar e listar blobs de blocos em um contêiner usando o Armazenamento de Blobs do Azure.

Para concluir este início rápido, você precisa de uma [assinatura do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Baixar o aplicativo de exemplo

O [aplicativo de exemplo](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git) deste início rápido é um aplicativo de console Node.js básico. Para começar, clone o repositório para seu computador usando o comando a seguir:

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

Para abrir o aplicativo, procure a pasta *storage-blobs-node-quickstart*, e abra-a no seu ambiente de edição de código favorito.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de conexão de armazenamento

Antes de executar o aplicativo, você deve fornecer a cadeia de conexão para sua conta de armazenamento. O repositório de exemplo inclui um arquivo chamado *.env.example*. É possível renomear esse arquivo, removendo a extensão *.example*, resultando em um arquivo chamado *.env*. Dentro do arquivo *.env*, adicione o valor da cadeia de conexão após a chave *AZURE_STORAGE_CONNECTION_STRING*.

## <a name="install-required-packages"></a>Instalar os pacotes necessários

No diretório de aplicativo, execute *npm install* para instalar os pacotes necessários para o aplicativo.

```bash
npm install
```

## <a name="run-the-sample"></a>Execute o exemplo
Agora que as dependências estão instaladas, você pode executar o exemplo passando comandos para o script. Por exemplo, para criar um contêiner de blob, execute o comando a seguir:

```bash
node index.js --command createContainer
```

Os comandos disponíveis incluem:

| Comando | DESCRIÇÃO |
|---------|---------|
|*createContainer* | Cria um contêiner chamado *test-container* (terá êxito mesmo se já existir um contêiner) |
|*upload*          | Carrega o arquivo *example.txt* para o contêiner *test-container* |
|*download*        | Baixa o conteúdo do blob *example* para *example.downloaded.txt* |
|*delete*          | Exclui o blob *example* |
|*list*            | Lista o conteúdo do contêiner *test-container* para o console |


## <a name="understanding-the-sample-code"></a>Entendendo o código de exemplo
Este exemplo de código usa alguns módulos para fazer interface com o sistema de arquivos e a linha de comando. 

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').load();
}
const path = require('path');
const args = require('yargs').argv;
const storage = require('azure-storage');
```

O propósito desses módulos é o seguinte: 

- *dotenv* carrega variáveis de ambiente definidas em um arquivo chamado *.env* no contexto de execução atual
- *path* é necessário para determinar o caminho absoluto de arquivo do arquivo a ser carregado no armazenamento de blobs
- *yargs* expõe uma interface simples para acessar argumentos de linha de comando
- *azure-storage* é o módulo do [SDK do Armazenamento do Azure](/nodejs/api/azure-storage) para Node.js

Em seguida, uma série de variáveis é inicializada:

```javascript
const blobService = storage.createBlobService();
const containerName = 'test-container';
const sourceFilePath = path.resolve('./example.txt');
const blobName = path.basename(sourceFilePath, path.extname(sourceFilePath));
```

As variáveis são definidas com os seguintes valores:

- *blobService* é definido como uma nova instância do serviço Blob do Azure
- *containerName* é definido como o nome do contêiner
- *sourceFilePath* é definido como o caminho absoluto do arquivo a ser carregado
- *blobName* é criado utilizando o nome do arquivo e removendo a extensão do arquivo

Na implementação a seguir, cada uma das funções *blobService* é encapsulada em uma *Promise*, que permite o acesso à função *async* do JavaScript e ao operador *await* para simplificar a natureza de retorno de chamada da [API do Armazenamento do Azure](/nodejs/api/azure-storage/blobservice). Quando uma resposta bem-sucedida é retornada para cada função, a promessa é resolvida com dados relevantes, juntamente com uma mensagem específica para a ação.

### <a name="create-a-blob-container"></a>Criar um contêiner de blob

A função *createContainer* chama [createContainerIfNotExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists) e define o nível de acesso apropriado para o blob.

```javascript
const createContainer = () => {
    return new Promise((resolve, reject) => {
        blobService.createContainerIfNotExists(containerName, { publicAccessLevel: 'blob' }, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' created` });
            }
        });
    });
};
```

O segundo parâmetro (*options*) para **createContainerIfNotExists** aceita um valor para [publicAccessLevel](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createContainerIfNotExists). O valor *blob* para *publicAccessLevel* especifica que dados de blob específicos são expostos ao público. Essa configuração está em oposição ao nível de acesso de *container*, que oferece a capacidade de listar o conteúdo do contêiner.

O uso de **createContainerIfNotExists** permite que o aplicativo execute o comando *createContainer* várias vezes sem retornar erros quando o contêiner já existir. Em um ambiente de produção, geralmente você só chama **createContainerIfNotExists** uma vez, isso porque o mesmo contêiner é usado em todo o aplicativo. Nesses casos, é possível criar o contêiner antecipadamente por meio do portal ou através da CLI do Azure.

### <a name="upload-a-blob-to-the-container"></a>Carregar um blob para o contêiner

A função *upload* usa a função [createBlockBlobFromLocalFile](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromLocalFile) para carregar e gravar ou substituir um arquivo do sistema de arquivos no armazenamento de blobs. 

```javascript
const upload = () => {
    return new Promise((resolve, reject) => {
        blobService.createBlockBlobFromLocalFile(containerName, blobName, sourceFilePath, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Upload of '${blobName}' complete` });
            }
        });
    });
};
```
No contexto do aplicativo de exemplo, o arquivo chamado *example.txt* é carregado para um blob chamado de *example* dentro de um contêiner chamado de *test-container*. Outras abordagens disponíveis para carregar conteúdo em blobs incluem trabalhar com [textos](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText) e [transmissões](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromStream).

Para verificar se o arquivo foi carregado ao seu armazenamento de blobs, você pode usar o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/en-us/features/storage-explorer/) para exibir os dados em sua conta.

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

A função *list* chama o método [listBlobsSegmented](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_createBlockBlobFromText) para retornar uma lista de metadados de blob em um contêiner. 

```javascript
const list = () => {
    return new Promise((resolve, reject) => {
        blobService.listBlobsSegmented(containerName, null, (err, data) => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Items in container '${containerName}':`, data: data });
            }
        });
    });
};
```

Chamar *listBlobsSegmented* retorna metadados de blob como uma matriz de instâncias [BlobResult](/nodejs/api/azure-storage/blobresult). Os resultados são retornados em 5.000 lotes de incremento (segmentos). Se houver mais de 5.000 blobs em um contêiner, os resultados incluirão um valor para **continuationToken**. Para listar segmentos subsequentes do contêiner de blob, você pode passar o token de continuação de volta para **listBlobSegmented** como o segundo argumento.

### <a name="download-a-blob-from-the-container"></a>Baixar um blob do contêiner

A função *download* usa [getBlobToLocalFile](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_getBlobToLocalFile) para baixar o conteúdo do blob para o caminho de arquivo absoluto fornecido.

```javascript
const download = () => {
    const dowloadFilePath = sourceFilePath.replace('.txt', '.downloaded.txt');
    return new Promise((resolve, reject) => {
        blobService.getBlobToLocalFile(containerName, blobName, dowloadFilePath, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Download of '${blobName}' complete` });
            }
        });
    });
};
```
A implementação exibida aqui altera o caminho do arquivo de origem para acrescentar *.downloaded.txt* ao nome do arquivo. Em contextos do mundo real, é possível alterar o local, bem como o nome do arquivo ao selecionar um destino de download.

### <a name="delete-blobs-in-the-container"></a>Excluir blobs no contêiner

A função *deleteBlock* (com o alias de comando de console *delete*) chama a função [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice#azure_storage_BlobService_deleteBlobIfExists). Como o nome implica, essa função não retorna um erro se o blob já tiver sido excluído.

```javascript
const deleteBlock = () => {
    return new Promise((resolve, reject) => {
        blobService.deleteBlobIfExists(containerName, blobName, err => {
            if(err) {
                reject(err);
            } else {
                resolve({ message: `Block blob '${blobName}' deleted` });
            }
        });
    });
};
```

### <a name="upload-and-list"></a>Carregar e listar

Um dos benefícios de usar promessas é a possibilidade de unir comandos. A função **uploadAndList** demonstra como é fácil listar o conteúdo de um Blob diretamente depois de carregar um arquivo.

```javascript
const uploadAndList = () => {
    return _module.upload().then(_module.list);
};
```

### <a name="calling-code"></a>Código de chamada

Para expor as funções implementadas para a linha de comando, cada uma das funções é mapeada até um literal de objeto.

```javascript
const _module = {
    "createContainer": createContainer,
    "upload": upload,
    "download": download,
    "delete": deleteBlock,
    "list": list,
    "uploadAndList": uploadAndList
};
```

Com *_module* agora em vigor, cada um dos comandos está disponível na linha de comando.

```javascript
const commandExists = () => exists = !!_module[args.command];
```

Se um determinado comando não existir, as propriedades de *_module* são renderizadas para o console como texto de ajuda ao usuário. 

A função *executeCommand* é uma função *async*, que chama o comando especificado usando o operador *await* e registra quaisquer mensagens de dados no console.

```javascript
const executeCommand = async () => {
    const response = await _module[args.command]();

    console.log(response.message);

    if (response.data) {
        response.data.entries.forEach(entry => {
            console.log('Name:', entry.name, ' Type:', entry.blobType)
        });
    }
};
```

Por fim, primeiro o código de execução chama *commandExists* para verificar se um comando conhecido foi passado pelo script. Se um comando existente estiver selecionado, o comando será executado, e os erros serão registrados no console.

```javascript
try {
    const cmd = args.command;

    console.log(`Executing '${cmd}'...`);

    if (commandExists()) {
        executeCommand();
    } else {
        console.log(`The '${cmd}' command does not exist. Try one of these:`);
        Object.keys(_module).forEach(key => console.log(` - ${key}`));
    }
} catch (e) {
    console.log(e);
}
```

## <a name="clean-up-resources"></a>Limpar recursos
Se você não planeja usar os dados ou as contas criadas neste artigo, convém excluí-los para evitar cobranças indesejadas. Para excluir o blob e os contêineres, é possível usar os métodos [deleteBlobIfExists](/nodejs/api/azure-storage/blobservice?view=azure-node-latest#deleteBlobIfExists_container__blob__options__callback_) e [deleteContainerIfExists](/nodejs/api/azure-storage/blobservice?view=azure-node-latest#deleteContainerIfExists_container__options__callback_). Também é possível excluir a conta de armazenamento [por meio do portal](../common/storage-create-storage-account.md).

## <a name="resources-for-developing-nodejs-applications-with-blobs"></a>Recursos para desenvolvimento de aplicativos Node.js com blobs

Confira estes recursos adicionais para o desenvolvimento em Node.js com armazenamento de Blobs:

### <a name="binaries-and-source-code"></a>Binários e código-fonte

- Exiba e instale o [código-fonte da biblioteca do cliente Node.js](https://github.com/Azure/azure-storage-node) para o Armazenamento do Azure no GitHub.

### <a name="client-library-reference-and-samples"></a>Exemplos e referência da biblioteca de clientes

- Confira a [referência da API Node.js](https://docs.microsoft.com/en-us/javascript/api/overview/azure/storage) para obter mais informações sobre a biblioteca de clientes do Node.js.
- Explore [exemplos de armazenamento de Blobs](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=nodejs&term=blob) gravados usando a biblioteca de clientes do Node.js.

## <a name="next-steps"></a>Próximas etapas

Este início rápido demonstra como carregar um arquivo entre um disco local e o Armazenamento de Blobs do Azure usando o Node.js. Para saber mais sobre como trabalhar com o Armazenamento de Blobs, prossiga para as instruções do Armazenamento de Blobs.

> [!div class="nextstepaction"]
> [Instruções de operações do Armazenamento de Blobs](storage-nodejs-how-to-use-blob-storage.md)

Para obter a referência Node.js para Armazenamento do Azure, consulte [Pacote do Armazenamento do Azure](https://docs.microsoft.com/javascript/api/azure-storage/?view=azure-node-latest).