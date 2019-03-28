---
title: Exemplos de script da CLI do Azure para o Banco de Dados SQL | Microsoft Docs
description: Exemplos de script da CLI do Azure para criar e gerenciar servidores do Banco de Dados SQL, pools elásticos, bancos de dados e firewalls.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc
ms.devlang: azurecli
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/03/2019
ms.openlocfilehash: e3a75acb6d591a9fdf1e632369cc2e9ae6b6099d
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58447829"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Amostras da CLI do Azure para o Banco de Dados SQL do Azure

Banco de dados SQL do Azure podem ser configurado usando a <a href="/cli/azure">CLI do Azure</a>.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá que você esteja executando a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="single-database--elastic-pools"></a>Banco de dados individual e pools elásticos

A tabela a seguir inclui links para exemplos de scripts da CLI do Azure para o Banco de Dados SQL.

| |  |
|---|---|
|**Criar um banco de dados individual e um pool elástico**||
| [Criar um Banco de Dados individual e configurar uma regra de firewall](scripts/sql-database-create-and-configure-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Este exemplo de script da CLI cria um banco de dados SQL do Azure e configura uma regra de firewall no nível do servidor. |
| [Criar pools Elásticos e mover bancos de dados em pools](scripts/sql-database-move-database-between-pools-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Este exemplo de script da CLI cria pools elásticos SQL, move os bancos de dados SQL do Azure em pools e altera os tamanhos de computação.|
|**Dimensionar um banco de dados individual e um pool elástico**||
| [Dimensionar um banco de dados individual](scripts/sql-database-monitor-and-scale-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Este exemplo de script da CLI dimensiona um banco de dados SQL do Azure individual para um tamanho de computação diferente depois de consultar as informações de tamanho do banco de dados. |
| [Dimensionar um pool elástico](scripts/sql-database-scale-pool-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Este exemplo de script da CLI dimensiona um pool elástico do SQL para um tamanho de computação diferente.  |
|||

Saiba mais sobre a [API da CLI do Azure do Banco de Dados Individual](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases).

## <a name="managed-instance"></a>Instância Gerenciada

A tabela a seguir inclui links para exemplos de scripts da CLI do Azure para o Banco de Dados SQL do Azure – Instância Gerenciada.

| |  |
|---|---|
| [Criar uma Instância Gerenciada](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/) | Esse script de CLI mostra como criar uma Instância Gerenciada. |
| [Atualizar uma Instância Gerenciada](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/) | Esse script de CLI mostra como atualizar uma Instância Gerenciada. |
| [Mover um banco de dados para outra instância gerenciada](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/) | Esse script de CLI mostra como restaurar um backup de um banco de dados de uma instância para outra. |
|||

Saiba mais sobre o a [API da CLI do Azure de Instância Gerenciada](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances) e localize [exemplos adicionais aqui](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).
