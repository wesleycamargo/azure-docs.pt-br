---
title: "Script da CLI do Azure – Criar um firewall para o Banco de Dados Cosmos do Azure | Microsoft Docs"
description: "Exemplo de script da CLI do Azure – Criar um firewall para o Banco de Dados Cosmos do Azure"
services: cosmos-db
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: sammvcple
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 06/02/2017
ms.author: mimig
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: 51f61901e1b1615e48582690dea701a01a56ebca
ms.contentlocale: pt-br
ms.lasthandoff: 07/18/2017

---

# <a name="azure-cosmos-db-create-a-firewall-using-the-azure-cli"></a>Banco de Dados Cosmos do Azure: Criar um firewall usando a CLI do Azure

Este exemplo de script da CLI cria uma política de firewall para qualquer tipo de conta do Banco de Dados Cosmos do Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá que você esteja executando a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/secure-cosmosdb-create-firewall/secure-cosmosdb-create-firewall.sh?highlight=38-42 "Criar um firewall do Banco de Dados Cosmos do Azure")]

## <a name="clean-up-deployment"></a>Limpar implantação

Após executar o exemplo de script, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az cosmosdb create](https://docs.microsoft.com/cli/azure/cosmosdb#create) | Cria uma conta do Banco de Dados Cosmos do Azure. |
| [az cosmosdb update](https://docs.microsoft.com/cli/azure/cosmosdb#update) | Atualiza uma conta do Banco de Dados Cosmos do Azure para incluir as configurações do firewall. |
| [az group delete](https://docs.microsoft.com/cli/azure/group#delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos adicionais de scripts da CLI do Banco de Dados Cosmos do Azure podem ser encontrados na [Documentação da CLI do Banco de Dados Cosmos do Azure](../cli-samples.md).

