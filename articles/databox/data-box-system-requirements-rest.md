---
title: Requisitos de armazenamento de Blobs do Microsoft Azure Data Box | Microsoft Docs
description: Saiba mais sobre as versões com suporte para APIs, SDKs e bibliotecas de clientes para o armazenamento de Blobs do Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/11/2018
ms.author: alkohli
ms.openlocfilehash: e7c2cc0c0ffaae11bd7bf5113c942cdb98397201
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53550642"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Requisitos de armazenamento de Blobs do Azure Data Box

Este artigo lista as versões das APIs do Azure, SDKs e ferramentas compatíveis com o armazenamento de Blobs do Azure Data Box. O armazenamento de Blobs do Azure Data Box fornece a funcionalidade de gerenciamento de Blobs com a semântica consistente do Azure. Além disso, este artigo resume as diferenças de armazenamento de Blobs do Azure Data Box dos serviços de Armazenamento do Microsoft Azure.

Recomendamos que você leia as informações com atenção antes de se conectar ao armazenamento de Blobs do Azure Data Box e consulte-as quando precisar.


## <a name="storage-differences"></a>Diferenças do armazenamento

|     Recurso                                             |     Armazenamento do Azure                                     |     Armazenamento de Blobs do Azure Data Box |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Armazenamento de arquivos do Azure                                   |    Compartilhamentos de arquivos SMB baseado em nuvem com suporte              |    Sem suporte      |
|    Criptografia do Serviço para dados inativos                  |    Criptografia AES de 256 bits                             |    Criptografia AES de 256 bits |
|    Tipo de conta de armazenamento                                 |    Contas de armazenamento de Blobs do Azure e de uso geral    |    Uso geral v1 apenas|
|    Nome de blob                                            |    1.024 caracteres (2.048 bytes)                     |    880 caracteres (1.760 bytes)|
|    Tamanho máximo do blob de blocos                              |    4,75 TB (100 MB X 50.000 blocos)                   |    4,75 TB (100 MB x 50.000 blocos) para o Azure Data Box v 1.7 em diante.|
|    Tamanho máximo de blob de páginas                               |    8 TB                                               |    1 TB                   |
|    Tamanho da página do blob de páginas                                  |    512 bytes                                          |    4 KB                   |

## <a name="supported-api-versions"></a>Versões de API com suporte

As seguintes versões das APIs de serviço de Armazenamento do Microsoft Azure são compatíveis com o armazenamento de Blob do Azure Data Box:

Versão de visualização pública (Azure Data Box 1.7 em diante)

- [17-04-2017](/rest/api/storageservices/version-2017-04-17)
- [31-05-2016](/rest/api/storageservices/version-2016-05-31)
- [11-12-2015](/rest/api/storageservices/version-2015-12-11)
- [08-07-2015](/rest/api/storageservices/version-2015-07-08)
- [05-04-2015](/rest/api/storageservices/version-2015-04-05)

## <a name="supported-sdk-versions"></a>Versões com suporte SDK

|     Biblioteca do cliente     |     Versão com suporte de armazenamento de Blob do Azure Data Box     |     Link             |     Especificação do ponto de extremidade         |
|------------------------|-------------------------------------------------|---------------------------------------------|------------------------------------|
|    .NET                |    De 6.2.0 a 8.7.0.                         |    Pacote NuGet:   https://www.nuget.org/packages/WindowsAzure.Storage/ <br>Versão do GitHub:   https://github.com/Azure/azure-storage-net/releases                                                                      |    app.config file                 |
|    Java                |    De 4.1.0 a 6.1.0                          |    Maven Package:   http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage   <br>Versão do GitHub:   https://github.com/Azure/azure-storage-java/releases                                                      |    Configuração de cadeia de conexão         |
|    Node.js             |    De 1.1.0 a 2.7.0                          |    Link do NPM:   https://www.npmjs.com/package/azure-storage   (Por exemplo: execute "instalar npm azure-storage@2.7.0")   <br>Versão do GitHub:   https://github.com/Azure/azure-storage-node/releases                            |    Declaração de instância de serviço    |
|    C++                 |    De 2.4.0 a 3.1.0                          |    Pacote NuGet:   https://www.nuget.org/packages/wastorage.v140/   <br>Versão do GitHub:   https://github.com/Azure/azure-storage-cpp/releases                                                                            |    Configuração de cadeia de conexão         |
|    PHP                 |    De 0.15.0 a 1.0.0                         |    Versão do GitHub:   https://github.com/Azure/azure-storage-php/releases   <br>Instalar por meio do Composer (veja os detalhes abaixo)                                                                                                   |    Configuração de cadeia de conexão         |
|    Python              |    De 0.30.0 a 1.0.0                         |    Versão do GitHub:   https://github.com/Azure/azure-storage-python/releases                                                                                                                                              |    Declaração de instância de serviço    |
|    Ruby                |    De 0.12.1 a 1.0.1                         |    Pacote do RubyGems:<br>Comum:   https://rubygems.org/gems/azure-storage-common/   <br>Blob: https://rubygems.org/gems/azure-storage-blob/      <br>Versão do GitHub:   https://github.com/Azure/azure-storage-ruby/releases    |                                   |

## <a name="supported-azure-client-libraries"></a>Bibliotecas de cliente do Azure compatíveis

Para o armazenamento de Blob do Azure Data Box, há bibliotecas específicas de clientes e requisitos de sufixo de ponto de extremidade específicos.

As versões com suporte para API REST para o armazenamento de Blob do Azure Data Box são 2017-04-17, 2016-05-31, 2015-12-11, 2015-07-08 e 2015-04-05 para a versão do Azure Data Box 1.7 em diante. Os pontos de extremidade de armazenamento de Blobs do Azure Data Box não têm paridade completa com a versão mais recente da API REST do Armazenamento de Blobs do Azure. Para as bibliotecas de cliente de armazenamento, é necessário estar ciente da versão que é compatível com a API REST.

### <a name="azure-data-box-17-onwards"></a>Azure Data Box 1.7 em diante

| Biblioteca do cliente     |Versão com suporte de armazenamento de Blob do Azure Data Box     | Link   |     Especificação do ponto de extremidade      |
|--------------------|--------------------------------------------|--------|---------------------------------|
|    .NET                |    8.7.0                                           |    Pacote NuGet:  https://www.nuget.org/packages/WindowsAzure.Storage/8.7.0    <br>Versão do GitHub:  https://github.com/Azure/azure-storage-net/releases/tag/v8.7.0                                                                                                                                                                                               |    app.config file                 |
|    Java                |    6.1.0                                           |    Maven Package:   http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0   <br>Versão do GitHub:  https://github.com/Azure/azure-storage-java/releases/tag/v6.1.0                                                                                                                                                                              |    Configuração de cadeia de conexão         |
|    Node.js             |    2.7.0                                           |    Link do NPM:   https://www.npmjs.com/package/azure-storage   (Executar: instalar npm azure-storage@2.7.0)   <br>Versão do GitHub:  https://github.com/Azure/azure-storage-node/releases/tag/v2.7.0                                                                                                                                                                        |    Declaração de instância de serviço    |
|    C++                 |    3.1.0                                           |    Pacote Nuget:   https://www.nuget.org/packages/wastorage.v140/3.1.0   <br>Versão do GitHub:  https://github.com/Azure/azure-storage-cpp/releases/tag/v3.1.0                                                                                                                                                                                                     |    Configuração de cadeia de conexão         |
|    PHP                 |    1.0.0                                           |    Versão do GitHub:<br>Comum: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-common   <br>Blob: https://github.com/Azure/azure-storage-php/releases/tag/v1.0.0-blob      <br>Instalar por meio do Composer (para saber mais, veja os detalhes abaixo).                                                                                                             |    Configuração de cadeia de conexão         |
|    Python              |    1.0.0                                           |    Versão do GitHub:<br>Comum:   https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common <br>Blob:   https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-blob                                                                                                                                                                          |    Declaração de instância de serviço    |
|    Ruby                |    1.0.1                                           |    Pacote do RubyGems:<br>Comum:   https://rubygems.org/gems/azure-storage-common/versions/1.0.1   <br>Blob: https://rubygems.org/gems/azure-storage-blob/versions/1.0.1         <br>Versão do GitHub:<br>Comum: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common   <br>Blob: https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob          |    Configuração de cadeia de conexão         |



### <a name="install-php-client-via-composer---current"></a>Instalar o cliente PHP por meio do Composer - atual

Para instalar por meio do Compoer: (tome blob como exemplo).
Crie um arquivo chamado composer.json na raiz do projeto e adicione o seguinte código a ele:

```
 {
   "require": {
   "Microsoft/azure-storage-blob":"1.0.0"
   }
```

Fazer o download `composer.phar` à raiz do projeto.

Execução: instalar o php composer.phar.

### <a name="endpoint-declaration"></a>Declaração de ponto de extremidade

Um ponto de extremidade de armazenamento de Blob do Azure Data Box inclui duas partes: nome de uma região e o domínio do Data Box. No SDK de armazenamento do Azure Data Box, o ponto de extremidade padrão é <serial no. of the device>. microsoftdatabox.com.  Para obter mais informações sobre o ponto de extremidade de serviço blob, acesse [Conectar por meio do armazenamento de blobs do Azure Data Box](data-box-deploy-copy-data-via-rest.md).
 
## <a name="examples"></a>Exemplos

### <a name="net"></a>.NET

Para o armazenamento de Blob do Azure Data Box, o sufixo de ponto de extremidade é especificado no arquivo `app.config`:

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Para o armazenamento de Blob do Azure Data Box, o sufixo de ponto de extremidade é especificado na configuração da cadeia de conexão:

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Para o armazenamento de Blob do Azure Data Box, o sufixo é especificado na instância de declaração:

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Para o armazenamento de Blob do Azure Data Box, o sufixo de ponto de extremidade é especificado na configuração da cadeia de conexão:

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Para o armazenamento de Blob do Azure Data Box, o sufixo de ponto de extremidade é especificado na configuração da cadeia de conexão:

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Para o armazenamento de Blob do Azure Data Box, o sufixo é especificado na instância de declaração:

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Para o armazenamento de Blob do Azure Data Box, o sufixo de ponto de extremidade é especificado na configuração da cadeia de conexão:

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Próximas etapas

* [Implante seu Azure Data Box](data-box-deploy-ordered.md)