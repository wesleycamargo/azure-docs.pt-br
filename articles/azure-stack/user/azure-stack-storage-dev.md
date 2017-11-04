---
title: "Introdução às ferramentas de desenvolvimento de armazenamento de pilha do Azure"
description: "Diretrizes para começar a usar as ferramentas de desenvolvimento de armazenamento de pilha do Azure"
services: azure-stack
author: xiaofmao
ms.author: xiaofmao
ms.date: 9/25/2017
ms.topic: get-started-article
ms.service: azure-stack
ms.openlocfilehash: 5b2898c64c0f1b5d804e63fa4e4e1218fa7a672c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-stack-storage-development-tools"></a>Introdução às ferramentas de desenvolvimento de armazenamento de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*


A pilha do Microsoft Azure fornece um conjunto de serviços de armazenamento, incluindo o armazenamento de BLOBs do Azure, tabela e fila.

Este artigo fornece orientações rápida sobre como começar a usar as ferramentas de desenvolvimento de armazenamento de pilha do Azure. Você pode encontrar informações mais detalhadas e código de exemplo nos tutoriais de armazenamento do Azure correspondentes.

Há diferenças entre o armazenamento do Azure e armazenamento de pilha do Azure, incluindo alguns requisitos específicos para cada plataforma conhecidos. Por exemplo, há bibliotecas de cliente específico e requisitos de sufixo de ponto de extremidade específico para a pilha do Azure. Para obter mais informações, consulte [Azure pilha de armazenamento: diferenças e considerações](azure-stack-acs-differences.md).

## <a name="azure-client-libraries"></a>Bibliotecas de cliente do Azure
A versão com suporte da API REST para o armazenamento do Azure pilha é 2015-04-05. Ele não tem paridade completa com a versão mais recente da API de REST do armazenamento do Azure. Portanto para as bibliotecas de cliente de armazenamento, você precisa estar ciente da versão que seja compatível com REST API 2015-04-05.


|Biblioteca do cliente|Versão com suporte a pilha do Azure|Link|Especificação de ponto de extremidade|
|---------|---------|---------|---------|
|.NET     |6.2.0|Pacote do NuGet:<br>[https://www.NuGet.org/Packages/windowsazure.Storage/6.2.0](https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)<br><br>Versão do GitHub:<br>[https://GitHub.com/Azure/Azure-Storage-NET/releases/tag/v6.2.1](https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1)|arquivo App. config|
|Java|4.1.0|Pacote de Maven:<br>[http://mvnrepository.com/artifact/com.microsoft.Azure/Azure-Storage/4.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0)<br><br>Versão do GitHub:<br> [https://GitHub.com/Azure/Azure-Storage-Java/releases/tag/V4.1.0](https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0)|Instalação de cadeia de caracteres de Conexão|
|Node.js     |1.1.0|Link NPM:<br>[https://www.npmjs.com/Package/Azure-Storage](https://www.npmjs.com/package/azure-storage)<br>(executar:`npm install azure-storage@1.1.0)`<br><br>Versão do GitHub:<br>[https://GitHub.com/Azure/Azure-Storage-Node/releases/tag/1.1.0](https://github.com/Azure/azure-storage-node/releases/tag/1.1.0)|Declaração da instância de serviço||C++|2.4.0|Pacote do NuGet:<br>[https://www.NuGet.org/Packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>Versão do GitHub:<br>[https://GitHub.com/Azure/Azure-Storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Instalação de cadeia de caracteres de Conexão|
|C++|2.4.0|Pacote do NuGet:<br>[https://www.NuGet.org/Packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>Versão do GitHub:<br>[https://GitHub.com/Azure/Azure-Storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Instalação de cadeia de caracteres de Conexão|
|PHP|0.15.0|Versão do GitHub:<br>[https://GitHub.com/Azure/Azure-Storage-PHP/releases/tag/v0.15.0](https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0)<br><br>Instalar por meio do criador (veja os detalhes abaixo)|Instalação de cadeia de caracteres de Conexão|
|Python     |0.30.0|Pacote PIP:<br> [https://pypi.Python.org/pypi/Azure-Storage/0.30.0](https://pypi.python.org/pypi/azure-storage/0.30.0)<br>(Executar:`pip install -v azure-storage==0.30.0)`<br><br>Versão do GitHub:<br> [https://GitHub.com/Azure/Azure-Storage-Python/releases/tag/v0.30.0](https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0)|Declaração da instância de serviço|
|Ruby|0.12.1<br>Visualização|Pacote de RubyGems:<br> [https://RubyGems.org/gems/Azure-Storage/Versions/0.12.1.Preview](https://rubygems.org/gems/azure-storage/versions/0.12.1.preview)<br><br>Versão do GitHub:<br> [https://GitHub.com/Azure/Azure-Storage-Ruby/releases/tag/v0.12.1](https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1)|Instalação de cadeia de caracteres de Conexão|

> [!NOTE]
> Detalhes do PHP<br><br>
>Para instalar por meio do criador:
>1. Crie um arquivo chamado `composer.json` na raiz do projeto com o código a seguir:<br>
>
>   ```
>   {
>       "require":{
>           "Microsoft/azure-storage":"0.15.0"
>        }
>    }
>   ```
>
>2. Baixar [composer.phar](http://getcomposer.org/composer.phar) na raiz do projeto.
>3. Execute: `php composer.phar install`.
>


## <a name="endpoint-declaration"></a>Declaração de ponto de extremidade
Um ponto de extremidade de pilha do Azure inclui duas partes: o nome de uma região e o domínio de pilha do Azure.
O Kit de desenvolvimento de pilha do Azure, o ponto de extremidade padrão é **local.azurestack.external**.
Se você não tiver certeza sobre o ponto de extremidade, entre em contato com seu administrador de nuvem.

## <a name="examples"></a>Exemplos


### <a name="net"></a>.NET

Para a pilha do Azure, o sufixo de ponto de extremidade é especificado no arquivo App. config:

```
<add key="StorageConnectionString" 
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```
### <a name="java"></a>Java

Para a pilha do Azure, o sufixo de ponto de extremidade é especificado na configuração de cadeia de caracteres de conexão:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=local.azurestack.external";
```

### <a name="nodejs"></a>Node.js

Para a pilha do Azure, o sufixo de ponto de extremidade é especificado na instância de declaração:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob.local.azurestack.external');
```
### <a name="c"></a>C++

Para a pilha do Azure, o sufixo de ponto de extremidade é especificado na configuração de cadeia de caracteres de conexão:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=local.azurestack.external"));
```

### <a name="php"></a>PHP

Para a pilha do Azure, o sufixo de ponto de extremidade é especificado na configuração de cadeia de caracteres de conexão:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.local.azurestack.external/;
QueueEndpoint=http:// <storage account name>.queue.local.azurestack.external/;
TableEndpoint=http:// <storage account name>.table.local.azurestack.external/;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Para a pilha do Azure, o sufixo de ponto de extremidade é especificado na instância de declaração:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```
### <a name="ruby"></a>Ruby

Para a pilha do Azure, o sufixo de ponto de extremidade é especificado na configuração de cadeia de caracteres de conexão:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=local.azurestack.external
```

## <a name="blob-storage"></a>Armazenamento de blob

Os seguintes tutoriais do armazenamento de BLOBs do Azure são aplicáveis a pilha do Azure. Observe o requisito de sufixo de ponto de extremidade específico descrito anteriormente na pilha do Azure [exemplos](#examples) seção.

* [Introdução ao armazenamento de Blobs do Azure usando o .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Como usar o Armazenamento de Blob do Java](../../storage/blobs/storage-java-how-to-use-blob-storage.md)
* [Como usar o armazenamento de Blob do Node. js]. /.. / storage/blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Como usar o armazenamento de Blob por meio do C++](../../storage/blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Como usar o Armazenamento de Blob do PHP](../../storage/blobs/storage-php-how-to-use-blobs.md)
* [Como usar o armazenamento de BLOBs do Azure do Python](../../storage/blobs/storage-python-how-to-use-blob-storage.md)
* [Como usar o Armazenamento de blob no Ruby](../../storage/blobs/storage-ruby-how-to-use-blob-storage.md)

## <a name="queue-storage"></a>Armazenamento de filas

Os seguintes tutoriais do armazenamento de fila do Azure são aplicáveis a pilha do Azure. Observe o requisito de sufixo de ponto de extremidade específico descrito anteriormente na pilha do Azure [exemplos](#examples) seção.

* [Introdução ao armazenamento de Fila do Azure usando o .NET](../../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Como usar o Armazenamento de Fila no Java](../../storage/queues/storage-java-how-to-use-queue-storage.md)
* [Como usar o Armazenamento de Fila do Node.js](../../storage/queues/storage-nodejs-how-to-use-queues.md)
* [Como usar o armazenamento de Fila por meio do C++](../../storage/queues/storage-c-plus-plus-how-to-use-queues.md)
* [Como usar o Armazenamento de Fila do PHP](../../storage/queues/storage-php-how-to-use-queues.md)
* [Como usar o Armazenamento de fila do Python](../../storage/queues/storage-python-how-to-use-queue-storage.md)
* [Como usar o Armazenamento de fila do Ruby](../../storage/queues/storage-ruby-how-to-use-queue-storage.md)


## <a name="table-storage"></a>Armazenamento de tabela

Os seguintes tutoriais do armazenamento de tabela do Azure são aplicáveis a pilha do Azure. Observe o requisito de sufixo de ponto de extremidade específico descrito anteriormente na pilha do Azure [exemplos](#examples) seção.

* [Introdução ao armazenamento de Tabelas do Azure usando o .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Como usar o Armazenamento de Tabela do Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [Como usar o armazenamento de tabela do Azure do Node. js](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [Como usar o armazenamento de tabela do C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Como usar o Armazenamento de Tabela do PHP](../../cosmos-db/table-storage-how-to-use-php.md)
* [Como usar o armazenamento de tabela em Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Como usar o Armazenamento de Tabela do Ruby](../../cosmos-db/table-storage-how-to-use-ruby.md)

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Armazenamento do Microsoft Azure](../../storage/common/storage-introduction.md)