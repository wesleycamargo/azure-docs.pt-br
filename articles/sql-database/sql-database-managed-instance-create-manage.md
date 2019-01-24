---
title: Gerenciamento da referência de API para Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre como criar e gerenciar Instâncias Gerenciadas do Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 01/17/2019
ms.openlocfilehash: 8d108df187e7d1fe549f284cffe3c5cbe4d56f7c
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54389933"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Gerenciamento da referência de API para Instâncias Gerenciadas do Banco de Dados SQL do Azure

É possível criar e gerenciar Instâncias Gerenciadas do Banco de Dados SQL do Azure usando o portal do Azure, o PowerShell, a CLI do Azure, a API REST e o Transact-SQL. Neste artigo, você encontra uma visão geral das funções e API que você pode usar para criar e configurar a instância gerenciada.

## <a name="azure-portal-create-a-managed-instance"></a>Portal do Azure: Criar uma Instância Gerenciada

Para ver um início rápido mostrando como criar uma nova Instância Gerenciada do Banco de Dados SQL do Azure, consulte [Início Rápido: Criar uma Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-get-started.md).

## <a name="powershell-create-and-manage-a-managed-instance"></a>PowerShell: Criar e gerenciar uma instância gerenciada

Para criar e gerenciar servidores, bancos de dados e firewalls do Azure SQL com o Azure PowerShell, use os cmdlets do PowerShell a seguir. Se você precisa instalar ou atualizar o PowerShell, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps).

> [!TIP]
> Para ver scripts de exemplo do PowerShell, consulte [Script de início rápido: Criar Instância Gerenciada do SQL do Azure usando a biblioteca do PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/).

| Cmdlet | DESCRIÇÃO |
| --- | --- |
|[New-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqlinstance)|Cria uma Instância Gerenciada do Banco de Dados SQL do Azure |
|[Get-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlinstance)|Retorna informações sobre a Instância Gerenciada do SQL do Azure|
|[Set-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqlinstance)|Define propriedades para uma Instância Gerenciada do Banco de Dados SQL do Azure|
|[Remove-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/remove-azurermsqlinstance)|Remove uma Instância Gerenciada do Banco de Dados SQL do Azure|
|[New-AzureRmSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqlinstancedatabase)|Cria um banco de dados da Instância Gerenciada do Banco de Dados SQL do Azure|
|[Get-AzureRmSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlinstancedatabase)|Retorna informações sobre o banco de dados da Instância Gerenciada do SQL do Azure|
|[Remove-AzureRmSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/azurerm.sql/remove-azurermsqlinstancedatabase)|Remove um banco de dados da Instância Gerenciada do Banco de Dados SQL do Azure|
|[Restore-AzureRmSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqlinstancedatabase)|Restaura um banco de dados da Instância Gerenciada do Banco de Dados SQL do Azure|

## <a name="azure-cli-manage-logical-servers-and-databases"></a>CLI do Azure: Gerenciar servidores lógicos e bancos de dados

Para criar e gerenciar servidores, bancos de dados e firewalls do SQL Azure com a [CLI do Azure](/cli/azure), use os seguintes comandos da [Instância Gerenciada do SQL da CLI do Azure](/cli/azure/sql/mi). Use o [Cloud Shell](/azure/cloud-shell/overview) para executar a CLI no seu navegador ou [instale-o](/cli/azure/install-azure-cli) no macOS, Linux ou Windows.

> [!TIP]
> Para ver um início rápido da CLI do Azure, consulte [Trabalhar com a Instância Gerenciada do SQL usando a CLI do Azure](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

| Cmdlet | DESCRIÇÃO |
| --- | --- |
|[az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Cria uma instância gerenciada|
|[az sql mi list](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|Lista as instâncias gerenciadas disponíveis|
|[az sql mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|Obtém os detalhes de uma instância gerenciada|
|[az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Atualiza uma instância gerenciada|
|[az sql mi delete](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Remove uma instância gerenciada|
|[az sql midb create](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |Cria um banco de dados gerenciado|
|[az sql midb list](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|Lista os bancos de dados gerenciados disponíveis|
|[az sql midb restore](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|Restaura um banco de dados gerenciado|
|[az sql midb delete](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|Remove um banco de dados gerenciado|

## <a name="transact-sql-manage-logical-servers-and-databases"></a>Transact-SQL: Gerenciar servidores lógicos e bancos de dados

Para criar e gerenciar a Instância Gerenciada do Banco de Dados SQL do Azure depois que a instância gerenciada for criada, use os seguintes comandos T-SQL. É possível emitir esses comandos usando o portal do Azure, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is). [Visual Studio Code](https://code.visualstudio.com/docs), ou qualquer outro programa que possa conectar a um servidor de Banco de Dados SQL do Azure e passar comandos Transact-SQL.

> [!TIP]
> Para ver inícios rápidos mostrando que você precisa configurar e conectar a uma instância gerenciada usando o SQL Server Management Studio no Microsoft Windows, consulte [Início Rápido: Configurar a VM do Azure para conectar a uma Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-configure-vm.md) e [Início Rápido: Configurar uma conexão ponto a site para uma Instância Gerenciada do Banco de Dados SQL do Azure do local](sql-database-managed-instance-configure-p2s.md).
> [!IMPORTANT]
> Não é possível criar ou excluir uma instância gerenciada usando o Transact-SQL.

| Comando | DESCRIÇÃO |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|Cria um novo banco de dados de instância gerenciada. Você deve estar conectado ao banco de dados mestre para criar um novo banco de dados.|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Modifica o banco de dados de uma Instância Gerenciada do SQL do Azure.|

## <a name="rest-api-manage-logical-servers-and-databases"></a>API REST: Gerenciar servidores lógicos e bancos de dados

Para criar e gerenciar a Instância Gerenciada do Banco de Dados SQL do Azure, use estas solicitações de API REST.

| Comando | DESCRIÇÃO |
| --- | --- |
|[Managed Instances - Create Or Update](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Cria ou atualiza uma instância gerenciada.|
|[Managed Instances - Delete](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Exclui uma instância gerenciada.|
|[Managed Instances - Get](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Obtém uma instância gerenciada.|
|[Managed Instances - List](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|Retorna uma lista de instâncias gerenciadas em uma assinatura.|
|[Managed Instances - List By Resource Group](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|Retorna uma lista de instâncias gerenciadas em um grupo de recursos.|
|[Managed Instances - Update](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Atualiza uma instância gerenciada.|

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como migrar um banco de dados do SQL Server para o Azure, confira [Migrar para o Banco de Dados SQL do Azure](sql-database-cloud-migrate.md).
- Para obter informações sobre os recursos com suporte, consulte [Recursos](sql-database-features.md).
