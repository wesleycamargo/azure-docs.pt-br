---
title: Gerenciar réplicas de leitura para o banco de dados do Azure para PostgreSQL da CLI do Azure
description: Saiba como gerenciar o banco de dados do Azure para PostgreSQL ler réplicas da CLI do Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: b5e0336a290090ed6bd7f5af508e691677780a80
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59789420"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli"></a>Criar e gerenciar réplicas de leitura da CLI do Azure

Neste artigo, você aprenderá como criar e gerenciar réplicas de leitura no banco de dados do Azure para PostgreSQL da CLI do Azure. Para saber mais sobre réplicas de leitura, confira [Visão Geral](concepts-read-replicas.md).

## <a name="prerequisites"></a>Pré-requisitos
- Um [servidor do Banco de Dados do Azure para PostgreSQL](quickstart-create-server-up-azure-cli.md) que será o servidor mestre.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que seja executada a CLI do Azure versão 2.0 ou posterior. Para ver a versão instalada, execute o comando `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli). 


## <a name="prepare-the-master-server"></a>Preparar o servidor mestre
Essas etapas devem ser usadas para preparar um servidor mestre nas camadas de uso geral ou otimizada para memória.

O parâmetro `azure.replication_support` deve ser definido como **REPLICA** no servidor mestre. Quando esse parâmetro estático é alterado, uma reinicialização do servidor é necessária para que a alteração tenha efeito.

1. Definir `azure.replication_support` à réplica.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

2. Reinicialização para aplicar a alteração para o servidor.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

## <a name="create-a-read-replica"></a>Criar uma réplica de leitura

O [criar réplica do az postgres server](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) comando requer os seguintes parâmetros:

| Configuração | Valor de exemplo | DESCRIÇÃO  |
| --- | --- | --- |
| resource-group | myresourcegroup |  O grupo de recursos onde o servidor de réplica será criado.  |
| Nome | mydemoserver-replica | O nome do novo servidor de réplica criado. |
| source-server | mydemoserver | A nome de identificação do recurso ou do servidor mestre existente para replicar de. |

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Se você não tiver definido o `azure.replication_support` parâmetro para **réplica** em um uso geral ou com otimização de memória de servidor mestre e reiniciou o servidor, você receberá um erro. Conclua essas duas etapas antes de criar uma réplica.

Uma réplica é criada usando a mesma configuração de servidor que o mestre. Depois que uma réplica é criada, várias configurações podem ser alteradas independentemente do servidor mestre: período de retenção de backup, armazenamento, vCores e geração da computação. O tipo de preço também pode ser alterado de forma independente, exceto de ou para a camada básica.

> [!IMPORTANT]
> Antes de uma configuração de servidor mestre ser atualizada com novos valores, atualize a configuração de réplica para valores iguais ou maiores. Esta ação garante que a réplica pode acompanhar as alterações feitas ao mestre.

## <a name="list-replicas"></a>Lista de réplicas
Você pode exibir a lista de réplicas de um servidor mestre usando [lista de réplicas do az postgres server](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) comando.

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

## <a name="stop-replication-to-a-replica-server"></a>Parar a replicação para um servidor de réplica
Você pode parar a replicação entre um servidor mestre e uma réplica de leitura por meio [parar de réplica do az postgres server](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) comando.

Depois de interromper a replicação para um servidor mestre e uma réplica de leitura, isso não poderá ser desfeito. A réplica de leitura se torna um servidor autônomo que dá suporte a leituras e gravações. O servidor autônomo não pode se tornar uma réplica novamente.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

## <a name="delete-a-master-or-replica-server"></a>Excluir um servidor de réplica ou mestre
Para excluir um servidor mestre ou réplica, você deve usar o [excluir do az postgres server](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) comando.

Ao excluir um servidor mestre, a replicação para todas as réplicas de leitura será interrompida. As réplicas de leitura tornam-se servidores independentes que agora têm suporte para leitura e gravação.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre [réplicas de leitura no Banco de Dados do Azure para PostgreSQL](concepts-read-replicas.md).