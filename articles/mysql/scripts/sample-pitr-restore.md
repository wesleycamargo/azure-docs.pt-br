---
title: Script CLI do Azure – Restaurar um único Banco de Dados do Azure para MySQL a um ponto anterior
description: O script de exemplo do CLI restaura um servidor de Banco de Dados do Azure para MySQL a um ponto anterior.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: bea3a8057e9b99dc5847e23541d41c5c68393d48
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266203"
---
# <a name="restore-an-azure-database-for-mysql-server-using-azure-cli"></a>Restaurar um servidor Banco de Dados do Azure para MySQL usando a CLI do Azure
Esse script de exemplo do CLI restaura um servidor de Banco de Dados individual do Azure para MySQL a um ponto anterior.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este exemplo exigirá que você esteja executando a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script de exemplo
Neste script de exemplo, altere as linhas destacadas para personalizar o nome de usuário administrador e a senha. Substitua a ID de assinatura usada nos comandos az monitor pela sua ID de assinatura.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/backup-restore-pitr/backup-restore.sh?highlight=18-19 "Restore Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Limpar a implantação
Após a execução do script de exemplo, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/backup-restore-pitr/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Explicação sobre o script
Este script usa os seguintes comandos. Cada comando da tabela é vinculado à documentação específica do comando.

| **Comando** | **Observações** |
|---|---|
| [az group create](/cli/azure/group#create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az mysql server create](/cli/azure/mysql/server#create) | Cria um servidor MySQL que hospeda os bancos de dados. |
| [az mysql server restore](/cli/azure/mysql/server#restore) | Restaura um servidor de backup. |
| [az group delete](/cli/azure/group#delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas
- Para ler mais sobre a CLI do Azure: [documentação da CLI do Azure](/cli/azure).
- Experimente scripts adicionais: [exemplos da CLI do Azure para o Banco de Dados do Azure para MySQL](../sample-scripts-azure-cli.md)
