---
title: "Introdução às ferramentas de desenvolvimento de armazenamento de pilha do Azure"
description: "Diretrizes para começar a usar as ferramentas de desenvolvimento de armazenamento de pilha do Azure"
services: azure-stack
author: mabriggs
ms.author: mabrigg
ms.date: 02/21/2018
ms.topic: get-started-article
ms.service: azure-stack
manager: femila
ms.reviewer: xiaofmao
ms.openlocfilehash: 81c62fc569e9f758d08bfca0bdfc5bcc9ed5860f
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2018
---
# <a name="get-started-with-azure-stack-storage-development-tools"></a>Introdução às ferramentas de desenvolvimento de armazenamento de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

A pilha do Microsoft Azure fornece um conjunto de serviços de armazenamento, incluindo o armazenamento de BLOBs do Azure, tabela e fila.

Este artigo fornece orientações rápida sobre como começar a usar as ferramentas de desenvolvimento de armazenamento de pilha do Azure. Você pode encontrar informações mais detalhadas e código de exemplo nos tutoriais de armazenamento do Azure correspondentes.

Há diferenças entre o armazenamento do Azure e armazenamento de pilha do Azure, incluindo alguns requisitos específicos para cada plataforma conhecidos. Por exemplo, há bibliotecas de cliente específico e requisitos de sufixo de ponto de extremidade específico para a pilha do Azure. Para obter mais informações, consulte [Azure pilha de armazenamento: diferenças e considerações](azure-stack-acs-differences.md).

## <a name="azure-client-libraries"></a>Bibliotecas de cliente do Azure

As versões com suporte da API REST para o armazenamento do Azure pilha são 2017-04-17, 2016-05-31, 2015-12-11, 2015-07-08, 2015-04-05 para a atualização de 1802 ou versões mais recentes e 2015-04-05 para versões anteriores. Os pontos de extremidade de pilha do Azure não tem paridade completa com a versão mais recente da API de REST do armazenamento do Azure. Para as bibliotecas de cliente de armazenamento, você precisa estar ciente da versão que seja compatível com a API REST.

### <a name="1802-update-or-newer-versions"></a>1802 atualização ou versões mais recentes

| Biblioteca do cliente | Versão com suporte a pilha do Azure | Link | Especificação de ponto de extremidade |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 8.7.0 | Pacote do NuGet:<br>https://www.nuget.org/packages/WindowsAzure.Storage/8.7.0<br> <br>Versão do GitHub:<br>https://github.com/Azure/azure-storage-net/releases/tag/v8.7.0 | arquivo App. config |
| Java | 6.1.0 | Pacote de Maven:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0<br> <br>Versão do GitHub:<br>https://github.com/Azure/azure-storage-java/releases/tag/v6.1.0 | Configuração de cadeia de conexão |
| Node.js | 2.7.0 | Link NPM:<br>https://www.npmjs.com/package/azure-storage<br>(Executar: `npm install azure-storage@2.7.0`)<br> <br>Versão do GitHub:<br>https://github.com/Azure/azure-storage-node/releases/tag/v2.7.0 | Declaração da instância de serviço |
| C++ | 3.1.0 | Pacote do NuGet:<br>https://www.nuget.org/packages/wastorage.v140/3.1.0<br> <br>Versão do GitHub:<br>https://github.com/Azure/azure-storage-cpp/releases/tag/v3.1.0 | Configuração de cadeia de conexão |
| PHP | 1.0.0 | Versão do GitHub:<br>Common: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-common<br>Blob: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-blob<br>Fila:<br>https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-queue<br>Table: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-table<br> <br>Instalar por meio do criador (para saber mais, [consulte os detalhes abaixo](#install-php-client-via-composer---current).) | Configuração de cadeia de conexão |
| Python | 1.0.0 | Versão do GitHub:<br>Comuns:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common<br>Blob:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-blob<br>Fila:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-queue | Declaração da instância de serviço |
| Ruby | 1.0.1 | Pacote de RubyGems:<br>Comuns:<br>https://rubygems.org/gems/azure-storage-common/versions/1.0.1<br>Blob: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1<br>Queue: https://rubygems.org/gems/azure-storage-queue/versions/1.0.1<br>Table: https://rubygems.org/gems/azure-storage-table/versions/1.0.1<br> <br>Versão do GitHub:<br>Common: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common<br>Blob: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob<br>Queue: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue<br>Table: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table | Configuração de cadeia de conexão |

#### <a name="install-php-client-via-composer---current"></a>Instalar o cliente PHP por meio de criador - atual

Para instalar por meio do criador: (take blob exemplo).

1. Crie um arquivo chamado **composer.json** na raiz do projeto com o código a seguir:
  ```php
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.0.0"
      }
    }
  ```
2. Baixar [composer.phar](http://getcomposer.org/composer.phar) para a raiz do projeto.
3. Execute: `php composer.phar install`.

### <a name="previous-versions"></a>Versões anteriores

|Biblioteca do cliente|Versão com suporte a pilha do Azure|Link|Especificação de ponto de extremidade|
|---------|---------|---------|---------|
|.NET     |6.2.0|Pacote do NuGet:<br>[https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0](https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)<br><br>Versão do GitHub:<br>[https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1](https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1)|arquivo App. config|
|Java|4.1.0|Pacote de Maven:<br>[http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0)<br><br>Versão do GitHub:<br> [https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0](https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0)|Configuração de cadeia de conexão|
|Node.js     |1.1.0|Link NPM:<br>[https://www.npmjs.com/package/azure-storage](https://www.npmjs.com/package/azure-storage)<br>(executar: `npm install azure-storage@1.1.0)`<br><br>Versão do GitHub:<br>[https://github.com/Azure/azure-storage-node/releases/tag/1.1.0](https://github.com/Azure/azure-storage-node/releases/tag/1.1.0)|Declaração da instância de serviço||C++|2.4.0|Pacote do NuGet:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>Versão do GitHub:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Configuração de cadeia de conexão|
|C++|2.4.0|Pacote do NuGet:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>Versão do GitHub:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Configuração de cadeia de conexão|
|PHP|0.15.0|Versão do GitHub:<br>[https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0](https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0)<br><br>Instalar por meio do criador (veja os detalhes abaixo)|Configuração de cadeia de conexão|
|Python     |0.30.0|Pacote PIP:<br> [https://pypi.python.org/pypi/azure-storage/0.30.0](https://pypi.python.org/pypi/azure-storage/0.30.0)<br>(Executar: `pip install -v azure-storage==0.30.0)`<br><br>Versão do GitHub:<br> [https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0](https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0)|Declaração da instância de serviço|
|Ruby|0.12.1<br>Visualização|Pacote de RubyGems:<br> [https://rubygems.org/gems/azure-storage/versions/0.12.1.preview](https://rubygems.org/gems/azure-storage/versions/0.12.1.preview)<br><br>Versão do GitHub:<br> [https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1](https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1)|Configuração de cadeia de conexão|

#### <a name="install-php-client-via-composer---previous"></a>Instalar o cliente PHP por meio de criador - anterior

Para instalar por meio do criador:

1. Crie um arquivo chamado **composer.json** na raiz do projeto com o código a seguir:
  ```php
    {
          "require":{
          "Microsoft/azure-storage":"0.15.0"
          }
    }
  ```
2. Baixar [composer.phar](http://getcomposer.org/composer.phar) na raiz do projeto.
3. Execute: `php composer.phar install`.

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
* [Como usar o armazenamento de Blob do Node.js](../../storage/blobs/storage-nodejs-how-to-use-blob-storage.md)
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
* [Como usar o armazenamento de Tabela do Azure com Node.js](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [Como usar o armazenamento de tabela do C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Como usar o Armazenamento de Tabela do PHP](../../cosmos-db/table-storage-how-to-use-php.md)
* [Como usar o armazenamento de tabela em Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Como usar o Armazenamento de Tabela do Ruby](../../cosmos-db/table-storage-how-to-use-ruby.md)

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Armazenamento do Microsoft Azure](../../storage/common/storage-introduction.md)