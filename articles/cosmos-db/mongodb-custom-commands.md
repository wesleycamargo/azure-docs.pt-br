---
title: Comandos personalizados para gerenciar dados armazenados na API do Azure Cosmos DB para MongoDB
description: Este artigo descreve como usar comandos personalizados para gerenciar dados armazenados na API do Azure Cosmos DB para MongoDB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: 238ba2722fef52d4607a7832113c03c097ef90b3
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58806746"
---
# <a name="use-custom-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Use os comandos personalizados para gerenciar dados armazenados na API do Azure Cosmos DB para MongoDB 

O Azure Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. Você pode se comunicar com a API do Azure Cosmos DB para MongoDB usando qualquer um dos software livre [drivers de cliente do MongoDB](https://docs.mongodb.org/ecosystem/drivers). A API do Azure Cosmos DB para MongoDB permite o uso de drivers de cliente existentes ao aderir à [protocolo de fios MongoDB](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Usando a API do Azure Cosmos DB para MongoDB, você pode aproveitar os benefícios do Cosmos DB como distribuição global, fragmentação automática, alta disponibilidade, garantias de latência, automático, a criptografia em repouso, backups, e muitos mais, enquanto preserva seus investimentos em seu aplicativo MongoDB.

## <a name="mongodb-protocol-support"></a>Suporte de protocolo do MongoDB

Por padrão, API do Azure Cosmos DB para MongoDB é compatível com o MongoDB versão 3.2, do servidor para obter mais detalhes, consulte [suporte para recursos e sintaxe](mongodb-feature-support.md). Os recursos ou os operadores de consulta adicionadas no MongoDB versão 3.4 estão disponíveis atualmente como uma visualização na API do Azure Cosmos DB para MongoDB. Os seguintes comandos personalizados dão suporte a funcionalidade específica do Azure Cosmos DB ao executar operações CRUD nos dados armazenados na API do Azure Cosmos DB para MongoDB:

* [Criar banco de dados](#create-database)
* [Atualizar banco de dados](#update-database)
* [Obter banco de dados](#get-database)
* [Criar coleção](#create-collection)
* [Atualizar a coleção](#update-collection)
* [Obter coleção](#get-collection)

## <a id="create-database"></a> Criar banco de dados

O comando personalizado do banco de dados de create Cria um novo banco de dados do MongoDB. O nome do banco de dados é usado do contexto de bancos de dados em relação ao qual o comando é executado. O formato do comando CreateDatabase é da seguinte maneira:

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

A tabela a seguir descreve os parâmetros no comando:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
| customAction   |  cadeia de caracteres  |   Nome do comando personalizado, ele deve ser "CreateDatabase".      |
| offerThroughput | int  | Taxa de transferência provisionada que podem ser definidas no banco de dados. Esse parâmetro é opcional. |

### <a name="output"></a>Saída

Retorna uma resposta de comando personalizada padrão. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

**Criar um banco de dados**

Para criar um banco de dados chamado "test", use o seguinte comando:

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**Criar um banco de dados com taxa de transferência**

Para criar um banco de dados chamado "teste" e taxa de transferência provisionada de 1000 RUs, use o seguinte comando:

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a id="update-database"></a> Atualizar banco de dados

O comando personalizado do banco de dados de atualização atualiza as propriedades associadas ao banco de dados especificado. Atualmente, só é possível atualizar a propriedade "offerThroughput".

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

A tabela a seguir descreve os parâmetros no comando:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
| customAction    |    cadeia de caracteres     |   Nome do comando personalizado. Deve ser "UpdateDatabase".      |
|  offerThroughput   |  int       |     Nova taxa de transferência provisionada que você deseja definir no banco de dados.    |

### <a name="output"></a>Saída

Retorna uma resposta de comando personalizada padrão. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

**Atualizar a taxa de transferência provisionada associada a um banco de dados**

Para atualizar a taxa de transferência provisionada de um banco de dados com o nome "test" para 1200 RUs, use o seguinte comando:

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a id="get-database"></a> Obter banco de dados

O comando personalizado do banco de dados de get retorna o objeto de banco de dados. O nome do banco de dados é usado do contexto de banco de dados em relação ao qual o comando é executado.

```
{
  customAction: "GetDatabase"
}
```

A tabela a seguir descreve os parâmetros no comando:


|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  customAction   |   cadeia de caracteres      |   Nome do comando personalizado. Deve ser "GetDatabase"|
        
### <a name="output"></a>Saída

Se o comando for bem-sucedido, a resposta contém um documento com os seguintes campos:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  `ok`   |   `int`     |   Status da resposta. 1 = = êxito. 0 = = falha.      |
| `database`    |    `string`        |   Nome do banco de dados.      |
|   `provisionedThroughput`  |    `int`      |    Taxa de transferência provisionada que é definida no banco de dados. Este é um parâmetro de resposta opcional.     |

Se o comando falhar, uma resposta de comando personalizado padrão será retornada. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

**Obter o banco de dados**

Para obter o objeto de banco de dados para um banco de dados chamado "test", use o seguinte comando:

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a id="create-collection"></a> Criar coleção

Comando Criar coleção personalizada cria uma nova coleção MongoDB. O nome do banco de dados é usado do contexto de bancos de dados em relação ao qual o comando é executado. O formato do comando CreateCollection é da seguinte maneira:

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

A tabela a seguir descreve os parâmetros no comando:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
| customAction    | cadeia de caracteres | Nome do comando personalizado. Deve ser "CreateDatabase"     |
| collection      | cadeia de caracteres | Nome da coleção                                   |
| offerThroughput | int    | Taxa de transferência provisionada para definir o banco de dados. Ele é um parâmetro opcional |
| shardKey        | cadeia de caracteres | Caminho da chave de fragmento para criar uma coleção fragmentada. Ele é um parâmetro opcional |

### <a name="output"></a>Saída

Retorna uma resposta de comando personalizada padrão. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

**Criar uma coleção de unsharded**

Para criar uma coleção de unsharded com nome "testCollection" e taxa de transferência provisionada de 1000 RUs, use o seguinte comando: 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**Criar uma coleção fragmentada**

Para criar uma coleção fragmentada com o nome "testCollection" e taxa de transferência provisionada de 1000 RUs, use o seguinte comando:

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a id="update-collection"></a> Atualizar a coleção

O comando personalizado de coleção de atualização atualiza as propriedades associadas com a coleção especificada.

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

A tabela a seguir descreve os parâmetros no comando:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  customAction   |   cadeia de caracteres      |   Nome do comando personalizado. Deve ser "UpdateCollection".      |
|  collection   |   cadeia de caracteres      |   Nome da coleção.       |
| offerThroughput   |int|   Taxa de transferência provisionada para definir na coleção.|

## <a name="output"></a>Saída

Retorna uma resposta de comando personalizada padrão. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

**Atualizar a taxa de transferência provisionada associada a uma coleção**

Para atualizar a taxa de transferência provisionada de uma coleção com o nome "testCollection" para 1200 RUs, use o seguinte comando:

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a id="get-collection"></a> Obter coleção

O comando personalizado de coleção de get retorna o objeto da coleção.

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

A tabela a seguir descreve os parâmetros no comando:


|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
| customAction    |   cadeia de caracteres      |   Nome do comando personalizado. Deve ser "GetCollection".      |
| collection    |    cadeia de caracteres     |    Nome da coleção.     |

### <a name="output"></a>Saída

Se o comando for bem-sucedido, a resposta contém um documento com os seguintes campos


|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Status da resposta. 1 = = êxito. 0 = = falha.      |
| `database`    |    `string`     |   Nome do banco de dados.      |
| `collection`    |    `string`     |    Nome da coleção.     |
|  `shardKeyDefinition`   |   `document`      |  Documento de especificação de índice usado como uma chave de fragmentação. Este é um parâmetro de resposta opcional.       |
|  `provisionedThroughput`   |   `int`      |    Taxa de transferência provisionada para definir na coleção. Este é um parâmetro de resposta opcional.     |

Se o comando falhar, uma resposta de comando personalizado padrão será retornada. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

**Obtenha a coleção**

Para obter o objeto de coleção para uma coleção denominada "testCollection", use o seguinte comando:

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a id="default-output"></a> Saída padrão de um comando personalizado

Se não for especificado, uma resposta personalizada contém um documento com os seguintes campos:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Status da resposta. 1 = = êxito. 0 = = falha.      |
| `code`    |   `int`      |   Retornado somente quando o comando falhou (ou seja, okey = = 0). Contém o código de erro do MongoDB. Este é um parâmetro de resposta opcional.      |
|  `errMsg`   |  `string`      |    Retornado somente quando o comando falhou (ou seja, okey = = 0). Contém uma mensagem de erro amigável. Este é um parâmetro de resposta opcional.      |

## <a name="next-steps"></a>Próximas etapas

Em seguida, você pode continuar a aprender os conceitos do Azure Cosmos DB a seguir: 

* [Indexação no Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expirar os dados no Azure Cosmos DB automaticamente com a vida útil](../cosmos-db/time-to-live.md)