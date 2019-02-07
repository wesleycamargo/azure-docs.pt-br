---
title: Script da CLI do Azure - Dimensionar o Banco de Dados do Azure para PostgreSQL
description: Exemplo de script da CLI do Azure – dimensionar o Banco de Dados do Azure para o servidor PostgreSQL para um nível de desempenho diferente após consultar a métrica.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.custom: mvc
ms.topic: sample
ms.date: 04/05/2018
ms.openlocfilehash: 65958eb1e2d7cc4492e0437b24db77f00ebc7eba
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55728326"
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Monitore e dimensione um único servidor PostgreSQL usando a CLI do Azure
Este exemplo de script da CLI dimensiona um único Banco de Dados do Azure para o servidor PostgreSQL para um nível de desempenho diferente após consultar a métrica. 

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se você optar por executar a CLI localmente, este artigo exigirá a CLI do Azure versão 2.0 ou posterior. Verifique a versão executando `az --version`. Confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli) para instalar ou atualizar sua versão da CLI do Azure.

## <a name="sample-script"></a>Script de exemplo
Neste script de exemplo, edite as linhas destacadas para atualizar o nome de usuário administrador e a senha com os seus próprios. Substitua a ID de assinatura usada nos comandos `az monitor` pela sua ID de assinatura.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh?highlight=15-16 "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Limpar a implantação
Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele após executar o exemplo de script. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação sobre o script
Esse script usa os comandos descritos na tabela abaixo:

| **Comando** | **Observações** |
|---|---|
| [az group create](/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az postgres server create](/cli/azure/postgres/server) | Cria um servidor PostgreSQL que hospeda os bancos de dados. |
| [az monitor metrics list](/cli/azure/monitor/metrics) | Lista o valor de métrica para os recursos. |
| [az group delete](/cli/azure/group) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas
- Leia mais sobre a CLI do Azure: [Documentação da CLI do Azure](/cli/azure)
- Experimente scripts adicionais: [Exemplos da CLI do Azure para o Banco de Dados do Azure para PostgreSQL](../sample-scripts-azure-cli.md)
- Leia mais sobre escala: [Camadas de Serviço](../concepts-service-tiers.md) e [Unidades de Computação e Unidades de Armazenamento](../concepts-compute-unit-and-storage.md)
