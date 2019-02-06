---
title: Gerenciar Microsoft Azure Cosmos DB usando a CLI do Azure
description: Use a CLI do Azure para gerenciar sua conta, banco de dados e contêineres do Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: c3028fd18bd9afefaa18f7f515a43a852ddef78a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55464391"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Gerenciar Microsoft Azure Cosmos usando a CLI do Azure

O guia a seguir descreve os comandos para automatizar o gerenciamento de contas, bancos de dados e contêineres do banco de dados do Microsoft Azure Cosmos DB usando a CLI do Azure. Também inclui comandos para dimensionar o rendimento do contêiner. As páginas de referência de todos os comandos do Azure Cosmos DB CLI estão disponíveis na [Referência de CLI do Azure](https://docs.microsoft.com/cli/azure/cosmosdb). Você também pode encontrar mais exemplos em [amostras de CLI do Azure para o Azure Cosmos DB](cli-samples.md), incluindo como criar e gerenciar contas, bancos de dados e contêineres do Cosmos DB para MongoDB, Gremlin, Cassandra e API de Tabela.

Este exemplo de script da CLI cria uma conta de API de SQL do Azure Cosmos DB, banco de dados e contêiner.  

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá que você esteja executando a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Para criar uma conta do Azure Cosmos DB com a API SQL, consistência de sessão, vários mestres habilitado no leste dos EUA e oeste dos EUA, abra o CLI do Azure ou o shell de nuvem e execute o seguinte comando:

```azurecli-interactive
az cosmosdb create \
   –-name "myCosmosDbAccount" \
   --resource-group "myResourceGroup" \
   --kind GlobalDocumentDB \
   --default-consistency-level "Session" \
   --locations "EastUS=0" "WestUS=1" \
   --enable-multiple-write-locations true \
```

## <a name="create-a-database"></a>Criar um banco de dados

Para criar um banco de dados do Cosmos DB, abra o CLI do Azure ou o shell da nuvem e execute o seguinte comando:

```azurecli-interactive
az cosmosdb database create \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup"
```

## <a name="create-a-container"></a>Criar um contêiner

Para criar um contêiner de banco de dados do Cosmos com RU / s de 1000 e uma chave de partição, abra o CLI do Azure ou o shell de nuvem e execute o seguinte comando:

```azurecli-interactive
# Create a container
az cosmosdb collection create \
   --collection-name "myContainer" \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup" \
   --partition-key-path = "/myPartitionKey" \
   --throughput 1000
```

## <a name="change-the-throughput-of-a-container"></a>Alterar o rendimento de um contêiner

Para alterar a taxa de transferência de um contêiner de banco de dados do Cosmos para RU / s de 400, abra o CLI do Azure ou o shell de nuvem e execute o seguinte comando:

```azurecli-interactive
# Update container throughput
az cosmosdb collection update \
   --collection-name "myContainer" \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup" \
   --throughput 400
```

## <a name="list-account-keys"></a>Listar chaves de conta

Quando você cria uma conta do Azure Cosmos DB, o serviço gera duas chaves de acesso mestras que podem ser usadas para autenticação quando a conta do Azure Cosmos DB é acessada. Ao fornecer duas chaves de acesso, o Azure Cosmos DB permite regenerar as chaves sem nenhuma interrupção na conta do Azure Cosmos DB. Também estão disponíveis chaves somente leitura para autenticação de operações somente leitura. Há duas chaves de leitura/gravação (primária e secundária) e duas chaves somente leitura (primária e secundária). Você pode obter as chaves da sua conta executando o seguinte comando:

```azurecli-interactive
# List account keys
az cosmosdb list-keys \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup"
```

## <a name="list-connection-strings"></a>Cadeias de caracteres de conexão de lista

A cadeia de conexão de conexão para conectar seu aplicativo à conta do Cosmos DB pode ser recuperada usando o seguinte comando.

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup"
```

## <a name="regenerate-account-key"></a>Regenerar a chave de conta

Você deve alterar as chaves de acesso de sua conta do Azure Cosmos DB periodicamente para ajudar a manter as conexões mais seguras. Duas chaves de acesso são atribuídas para permitir que você mantenha conexões com a conta do Azure Cosmos DB usando uma chave de acesso enquanto regenera a outra.

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup" \
   --key-kind primary
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a CLI do Azure, consulte:

- [Instalar a CLI do Azure.](/cli/azure/install-azure-cli)
- [Referência à CLI do Azure](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Amostras adicionais do CLI do Azure para o Azure Cosmos DB](cli-samples.md)
