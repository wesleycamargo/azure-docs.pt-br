---
title: Introdução às ferramentas de desenvolvimento de armazenamento do Azure Stack | Microsoft Docs
description: Diretrizes para obter uma introdução usando ferramentas de desenvolvimento de armazenamento do Azure Stack
services: azure-stack
author: mattbriggs
ms.author: mabrigg
ms.date: 12/03/2018
ms.topic: get-started-article
ms.service: azure-stack
manager: femila
ms.reviewer: xiaofmao
ms.openlocfilehash: 56814cc44874fe0c169e5fb51dc6cbff5a225231
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53727846"
---
# <a name="get-started-with-azure-stack-storage-development-tools"></a>Introdução às ferramentas de desenvolvimento de armazenamento do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

O Microsoft Azure Stack fornece um conjunto de serviços de armazenamento que inclua o blob, tabela e o armazenamento de filas.

Use este artigo como um guia para começar a usar as ferramentas de desenvolvimento de armazenamento do Azure Stack. Você pode encontrar informações mais detalhadas e código de exemplo nos tutoriais de armazenamento do Azure correspondente.

> [!NOTE]
> Há diferenças entre o armazenamento do Azure Stack e o armazenamento do Azure, incluindo requisitos específicos para cada plataforma conhecidos. Por exemplo, há bibliotecas de cliente específicas e requisitos de sufixo de ponto de extremidade específico para o Azure Stack. Para obter mais informações, consulte [armazenamento do Azure Stack: Diferenças e considerações](azure-stack-acs-differences.md).

## <a name="azure-client-libraries"></a>Bibliotecas de cliente do Azure

Para as bibliotecas de cliente de armazenamento, lembre-se da versão que é compatível com a API REST. Você também deve especificar o ponto de extremidade do Azure Stack em seu código.

### <a name="1811-update-or-newer-versions"></a>1811 atualização ou versões mais recentes

| Biblioteca do cliente | Versão com suporte do Azure Stack | Link | Especificação de ponto de extremidade |
|----------------|-------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|
| .NET | 8.7.0 | Pacote do NuGet:<br>https://www.nuget.org/packages/WindowsAzure.Storage/8.7.0<br> <br>Versão do GitHub:<br>https://github.com/Azure/azure-storage-net/releases/tag/v8.7.0 | arquivo App. config |
| Java | 6.1.0 | Pacote do Maven:<br>http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0<br> <br>Versão do GitHub:<br>https://github.com/Azure/azure-storage-java/releases/tag/v6.1.0 | Configuração de cadeia de conexão |
| Node.js | 2.7.0 | Link do NPM:<br>https://www.npmjs.com/package/azure-storage<br>(Executar: `npm install azure-storage@2.7.0`)<br> <br>Versão do GitHub:<br>https://github.com/Azure/azure-storage-node/releases/tag/v2.7.0 | Declaração de instância de serviço |
| C++ | 3.1.0 | Pacote do NuGet:<br>https://www.nuget.org/packages/wastorage.v140/3.1.0<br> <br>Versão do GitHub:<br>https://github.com/Azure/azure-storage-cpp/releases/tag/v3.1.0 | Configuração de cadeia de conexão |
| PHP | 1.0.0 | Versão do GitHub:<br>Comuns: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-common<br>Blob: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-blob<br>Fila:<br>https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-queue<br>Tabela: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-table<br> <br>Instalar por meio do compositor (para saber mais, [consulte os detalhes abaixo](#install-php-client-via-composer---current).) | Configuração de cadeia de conexão |
| Python | 1.0.0 | Versão do GitHub:<br>Comuns:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common<br>Blob:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-blob<br>Fila:<br>https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-queue | Declaração de instância de serviço |
| Ruby | 1.0.1 | Pacote do RubyGems:<br>Comuns:<br>https://rubygems.org/gems/azure-storage-common/versions/1.0.1<br>Blob: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1<br>Fila: https://rubygems.org/gems/azure-storage-queue/versions/1.0.1<br>Tabela: https://rubygems.org/gems/azure-storage-table/versions/1.0.1<br> <br>Versão do GitHub:<br>Comuns: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common<br>Blob: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob<br>Fila: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-queue<br>Tabela: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-table | Configuração de cadeia de conexão |

#### <a name="install-php-client-via-composer---current"></a>Instalar o cliente PHP por meio do compositor - atual

Para instalar por meio do compositor: (levar o blob como um exemplo).

1. Crie um arquivo chamado **Composer. JSON** na raiz do projeto com o código a seguir:

    ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.2.0"
      }
    }
    ```

2. Baixe [Phar](http://getcomposer.org/composer.phar) à raiz do projeto.
3. Execute: `php composer.phar install`.

### <a name="previous-versions-1802-to-1809-update"></a>Versões anteriores (atualização 1802 para 1809)

|Biblioteca do cliente|Versão com suporte do Azure Stack|Link|Especificação de ponto de extremidade|
|---------|---------|---------|---------|
|.NET     |6.2.0|Pacote do NuGet:<br>[https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0](https://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)<br><br>Versão do GitHub:<br>[https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1](https://github.com/Azure/azure-storage-net/releases/tag/v6.2.1)|arquivo App. config|
|Java|4.1.0|Pacote do Maven:<br>[http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/4.1.0)<br><br>Versão do GitHub:<br> [https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0](https://github.com/Azure/azure-storage-java/releases/tag/v4.1.0)|Configuração de cadeia de conexão|
|Node.js     |1.1.0|Link do NPM:<br>[https://www.npmjs.com/package/azure-storage](https://www.npmjs.com/package/azure-storage)<br>(execute: `npm install azure-storage@1.1.0)`<br><br>Versão do GitHub:<br>[https://github.com/Azure/azure-storage-node/releases/tag/1.1.0](https://github.com/Azure/azure-storage-node/releases/tag/1.1.0)|Declaração de instância de serviço||C++|2.4.0|Pacote do NuGet:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>Versão do GitHub:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Configuração de cadeia de conexão|
|C++|2.4.0|Pacote do NuGet:<br>[https://www.nuget.org/packages/wastorage.v140/2.4.0](https://www.nuget.org/packages/wastorage.v140/2.4.0)<br><br>Versão do GitHub:<br>[https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0](https://github.com/Azure/azure-storage-cpp/releases/tag/v2.4.0)|Configuração de cadeia de conexão|
|PHP|0.15.0|Versão do GitHub:<br>[https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0](https://github.com/Azure/azure-storage-php/releases/tag/v0.15.0)<br><br>Instalar por meio do compositor (veja os detalhes abaixo)|Configuração de cadeia de conexão|
|Python     |0.30.0|Pacote PIP:<br> [https://pypi.python.org/pypi/azure-storage/0.30.0](https://pypi.python.org/pypi/azure-storage/0.30.0)<br>(Execute: `pip install -v azure-storage==0.30.0)`<br><br>Versão do GitHub:<br> [https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0](https://github.com/Azure/azure-storage-python/releases/tag/v0.30.0)|Declaração de instância de serviço|
|Ruby|0.12.1<br>Visualização|Pacote do RubyGems:<br> [https://rubygems.org/gems/azure-storage/versions/0.12.1.preview](https://rubygems.org/gems/azure-storage/versions/0.12.1.preview)<br><br>Versão do GitHub:<br> [https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1](https://github.com/Azure/azure-storage-ruby/releases/tag/v0.12.1)|Configuração de cadeia de conexão|

#### <a name="install-php-client-via-composer---previous"></a>Instalar o cliente PHP por meio do compositor - anterior

Para instalar por meio do compositor: (take blob como exemplo).

1. Crie um arquivo chamado **Composer. JSON** na raiz do projeto com o código a seguir:

  ```json
    {
      "require": {
      "Microsoft/azure-storage-blob":"1.0.0"
      }
    }
  ```

2. Baixe [Phar](http://getcomposer.org/composer.phar) à raiz do projeto.
3. Execute: `php composer.phar install`.

## <a name="endpoint-declaration"></a>Declaração de ponto de extremidade

Um ponto de extremidade do Azure Stack inclui duas partes: o nome de uma região e domínio do Azure Stack.
O Kit de desenvolvimento do Azure Stack, o ponto de extremidade padrão é **local.azurestack.external**.
Se você não tiver certeza sobre o ponto de extremidade, entre em contato com seu administrador de nuvem.

## <a name="examples"></a>Exemplos

### <a name="net"></a>.NET

Para o Azure Stack, o sufixo de ponto de extremidade é especificado no arquivo App. config:

```xml
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=local.azurestack.external;" />
```

### <a name="java"></a>Java

Para o Azure Stack, o sufixo de ponto de extremidade é especificado na configuração de cadeia de caracteres de conexão:

```java
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=local.azurestack.external";
```

### <a name="nodejs"></a>Node.js

Para o Azure Stack, o sufixo de ponto de extremidade é especificado na instância de declaração:

```nodejs
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob.local.azurestack.external');
```

### <a name="c"></a>C++

Para o Azure Stack, o sufixo de ponto de extremidade é especificado na configuração de cadeia de caracteres de conexão:

```cpp
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=local.azurestack.external"));
```

### <a name="php"></a>PHP

Para o Azure Stack, o sufixo de ponto de extremidade é especificado na configuração de cadeia de caracteres de conexão:

```php
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.local.azurestack.external/;
QueueEndpoint=http:// <storage account name>.queue.local.azurestack.external/;
TableEndpoint=http:// <storage account name>.table.local.azurestack.external/;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Para o Azure Stack, o sufixo de ponto de extremidade é especificado na instância de declaração:

```python
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix='local.azurestack.external')
```

### <a name="ruby"></a>Ruby

Para o Azure Stack, o sufixo de ponto de extremidade é especificado na configuração de cadeia de caracteres de conexão:

```ruby
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=local.azurestack.external
```

## <a name="blob-storage"></a>Armazenamento de blob

Os tutoriais de armazenamento de BLOBs do Azure a seguir são aplicáveis para o Azure Stack. Observe o requisito de sufixo de ponto de extremidade específico para o Azure Stack descrito anteriormente na [exemplos](#examples) seção.

* [Introdução ao armazenamento de Blobs do Azure usando o .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Como usar o Armazenamento de Blob do Java](../../storage/blobs/storage-java-how-to-use-blob-storage.md)
* [Como usar o armazenamento de Blob do Node.js](../../storage/blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Como usar o armazenamento de Blob por meio do C++](../../storage/blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Como usar o Armazenamento de Blob do PHP](../../storage/blobs/storage-php-how-to-use-blobs.md)
* [Como usar o armazenamento de BLOBs do Azure do Python](../../storage/blobs/storage-python-how-to-use-blob-storage.md)
* [Como usar o Armazenamento de blob no Ruby](../../storage/blobs/storage-ruby-how-to-use-blob-storage.md)

## <a name="queue-storage"></a>Armazenamento de filas

Os tutoriais de armazenamento de filas do Azure a seguir são aplicáveis para o Azure Stack. Observe o requisito de sufixo de ponto de extremidade específico para o Azure Stack descrito anteriormente na [exemplos](#examples) seção.

* [Introdução ao armazenamento de Fila do Azure usando o .NET](../../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Como usar o Armazenamento de Fila no Java](../../storage/queues/storage-java-how-to-use-queue-storage.md)
* [Como usar o Armazenamento de Fila do Node.js](../../storage/queues/storage-nodejs-how-to-use-queues.md)
* [Como usar o armazenamento de Fila por meio do C++](../../storage/queues/storage-c-plus-plus-how-to-use-queues.md)
* [Como usar o Armazenamento de Fila do PHP](../../storage/queues/storage-php-how-to-use-queues.md)
* [Como usar o Armazenamento de fila do Python](../../storage/queues/storage-python-how-to-use-queue-storage.md)
* [Como usar o Armazenamento de fila do Ruby](../../storage/queues/storage-ruby-how-to-use-queue-storage.md)

## <a name="table-storage"></a>Armazenamento de tabela

Os tutoriais de armazenamento de tabelas do Azure a seguir são aplicáveis para o Azure Stack. Observe o requisito de sufixo de ponto de extremidade específico para o Azure Stack descrito anteriormente na [exemplos](#examples) seção.

* [Introdução ao armazenamento de Tabelas do Azure usando o .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Como usar o Armazenamento de Tabela do Java](../../cosmos-db/table-storage-how-to-use-java.md)
* [Como usar o armazenamento de Tabela do Azure com Node.js](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [Como usar o armazenamento de tabela do C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Como usar o Armazenamento de Tabela do PHP](../../cosmos-db/table-storage-how-to-use-php.md)
* [Como usar o armazenamento de tabelas no Python](../../cosmos-db/table-storage-how-to-use-python.md)
* [Como usar o Armazenamento de Tabela do Ruby](../../cosmos-db/table-storage-how-to-use-ruby.md)

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao armazenamento do Microsoft Azure](../../storage/common/storage-introduction.md)
