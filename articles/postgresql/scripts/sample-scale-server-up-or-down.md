---
title: "Script da CLI do Azure – dimensionar o Banco de Dados do Azure para PostgreSQL | Microsoft Docs"
description: "Exemplo de script da CLI do Azure – dimensionar o Banco de Dados do Azure para o servidor PostgreSQL para um nível de desempenho diferente após consultar a métrica."
services: postgresql
author: salonisonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.devlang: azure-cli
ms.custom: mvc
ms.topic: sample
ms.date: 05/31/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 75efaa7dd6165fe0a3d3e35928107cae71e23d5a
ms.contentlocale: pt-br
ms.lasthandoff: 06/20/2017

---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Monitore e dimensione um único servidor PostgreSQL usando a CLI do Azure
Este exemplo de script da CLI dimensiona um único Banco de Dados do Azure para o servidor PostgreSQL para um nível de desempenho diferente após consultar a métrica. 

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá que você esteja executando a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo
Neste script de exemplo, altere as linhas destacadas para personalizar o nome de usuário administrador e a senha. Substitua a ID de assinatura usada nos comandos do monitor AZ pela sua ID de assinatura.
[!code-azurecli-interactive[principal](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh?highlight=15-16 "Criar e escalar o banco de dados do Azure para PostgreSQL.")]

## <a name="clean-up-deployment"></a>Limpar implantação
Após executar o exemplo de script, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.
[!code-azurecli-interactive[principal](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Excluir o grupo de recursos.")]

## <a name="script-explanation"></a>Explicação sobre o script
Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| **Comando** | **Observações** |
|---|---|
| [az group create](/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az postgres server create](/cli/azure/postgres/server#create) | Cria um servidor PostgreSQL que hospeda os bancos de dados. |
| [az monitor metrics list](/cli/azure/monitor/metrics#list) | Lista o valor de métrica para os recursos. |
| [az group delete](/cli/azure/group#delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas
- Leia mais sobre a CLI do Azure: [Documentação da CLI do Azure](/cli/azure/overview)
- Experimente scripts adicionais: [Exemplos da CLI do Azure para o Banco de Dados do Azure para PostgreSQL](../sample-scripts-azure-cli.md)
- Leia mais informações sobre dimensionamento: [Camadas de serviço](../concepts-service-tiers.md) e [Unidades de computação e unidades de armazenamento](../concepts-compute-unit-and-storage.md)

