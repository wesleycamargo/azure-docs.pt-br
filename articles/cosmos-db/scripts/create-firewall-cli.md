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
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: c37d4ed3353d302ecfb2f673e890ffc3869f1844
ms.contentlocale: pt-br
ms.lasthandoff: 06/20/2017

---

<a id="azure-cosmos-db-create-a-firewall-using-the-azure-cli" class="xliff"></a>

# Banco de Dados Cosmos do Azure: Criar um firewall usando a CLI do Azure

Este exemplo de script da CLI cria uma política de firewall para qualquer tipo de conta do Banco de Dados Cosmos do Azure. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá que você esteja executando a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

<a id="sample-script" class="xliff"></a>

## Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/secure-cosmosdb-create-firewall/secure-cosmosdb-create-firewall.sh?highlight=38-42 "Criar um firewall do Banco de Dados Cosmos do Azure")]

<a id="clean-up-deployment" class="xliff"></a>

## Limpar implantação

Após executar o exemplo de script, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli-interactive
az group delete --name myResourceGroup
```

<a id="script-explanation" class="xliff"></a>

## Explicação sobre o script

Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [az group create](/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az cosmosdb create](/cli/azure/cosmosdb/name#create) | Cria uma conta do Banco de Dados Cosmos do Azure. |
| [az cosmosdb update](/cli/azure/sql/server#create) | Atualiza uma conta do Banco de Dados Cosmos do Azure para incluir as configurações do firewall. |
| [az group delete](/cli/azure/resource#delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

<a id="next-steps" class="xliff"></a>

## Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos adicionais de scripts da CLI do Banco de Dados Cosmos do Azure podem ser encontrados na [Documentação da CLI do Banco de Dados Cosmos do Azure](../cli-samples.md).

