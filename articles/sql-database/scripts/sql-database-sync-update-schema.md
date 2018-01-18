---
title: "Exemplo de PowerShell - Atualizar esquema de sincronização para Sincronização de Dados SQL | Microsoft Docs"
description: "Script de exemplo do Azure PowerShell para atualizar o esquema de sincronização para Sincronização de Dados SQL"
services: sql-database
documentationcenter: sql-database
author: jognanay
manager: craigg
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: load & move data, mvc
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 01/10/2018
ms.author: jognanay
ms.reviewer: douglasl
ms.openlocfilehash: 66bf084f585b86979e6521321daf466c571de10c
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2018
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>Usar o PowerShell para atualizar o esquema de sincronização em um grupo de sincronização existente

Este exemplo de PowerShell atualiza o esquema de sincronização em um grupo de sincronização existente. Quando você estiver sincronizando várias tabelas, este script ajuda a atualizar o esquema de sincronização com eficiência.

Este exemplo demonstra o uso do script **UpdateSyncSchema**, que está disponível no GitHub como [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1).

Para obter uma visão geral da Sincronização de Dados SQL, consulte [Sincronizar dados entre vários bancos de dados locais e de nuvem com a Sincronização de Dados SQL do Azure (versão prévia)](../sql-database-sync-data.md).
## <a name="prerequisites"></a>Pré-requisitos

Este exemplo exige o módulo do Azure PowerShell, versão 4.2 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para localizar a versão instalada. Se você precisa instalar ou atualizar, confira [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).
 
Execute `Login-AzureRmAccount` para criar uma conexão com o Azure.

## <a name="examples"></a>Exemplos

### <a name="example-1---add-all-tables-to-the-sync-schema"></a>Exemplo 1: adicionar todas as tabelas no esquema de sincronização

O exemplo a seguir atualiza o esquema de banco de dados e adiciona todas as tabelas válidas no banco de dados hub ao esquema de sincronização.

```powershell
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="example-2---add-and-remove-tables-and-columns"></a>Exemplo 2: adicionar e remover tabelas e colunas

O exemplo a seguir adiciona `[dbo].[Table1]` e `[dbo].[Table2].[Column1]` ao esquema de sincronização e remove `[dbo].[Table3]`.

```powershell
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>Parâmetros de script

O script **UpdateSyncSchema** tem os seguintes parâmetros:

| Parâmetro | Observações |
|---|---|
| $SubscriptionId | A assinatura na qual o grupo de sincronização é criado. |
| $ResourceGroupName | O grupo de recursos no qual o grupo de sincronização é criado.|
| $ServerName | O nome do servidor do banco de dados hub.|
| $DatabaseName | O nome do banco de dados hub. |
| $SyncGroupName | O nome do grupo de sincronização. |
| $MemberName | Se você deseja carregar o esquema de banco de dados do membro em vez de sincronização em vez do banco de dados hub, especifique o nome do membro. Se você deseja carregar o esquema de banco de dados do hub, deixe esse parâmetro em branco. |
| $TimeoutInSeconds | Tempo limite quando o script atualiza o esquema de banco de dados. O padrão é 900 segundos. |
| $RefreshDatabaseSchema | Especifique se o script precisa atualizar o esquema de banco de dados. Se o esquema de banco de dados mudou em relação à configuração anterior (por exemplo, se você adicionou uma nova tabela ou uma nova coluna), você precisa atualizar o esquema antes de você reconfigurá-lo. O padrão é falso. |
| $AddAllTables | Se esse valor for true, todas as colunas e tabelas válidas são adicionadas ao esquema de sincronização. Os valores de $TablesAndColumnsToAdd e $TablesAndColumnsToRemove são ignorados. |
| $TablesAndColumnsToAdd | Especifique tabelas ou colunas a serem adicionadas ao esquema de sincronização. Cada nome de tabela ou coluna deve ser totalmente delimitado com o nome do esquema. Por exemplo, `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Vários nomes de tabela ou coluna podem ser especificados e separados por vírgulas (,). |
| $TablesAndColumnsToRemove | Especifique tabelas ou colunas a serem removidas do esquema de sincronização. Cada nome de tabela ou coluna deve ser totalmente delimitado com o nome do esquema. Por exemplo, `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Vários nomes de tabela ou coluna podem ser especificados e separados por vírgulas (,). |
|||

## <a name="script-explanation"></a>Explicação sobre o script

O script **UpdateSyncSchema** usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| Get-Help | Observações |
|---|---|
| [Get-AzureRmSqlSyncGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncgroup) | Retorna informações sobre um grupo de sincronização. |
| [Update-AzureRmSqlSyncGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/update-azurermsqlsyncgroup) | Atualiza um grupo de sincronização. |
| [Get-AzureRmSqlSyncMember](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncmember) | Retorna informações sobre um membro de sincronização. |
| [Get-AzureRmSqlSyncSchema](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncschema) | Retorna informações sobre um esquema de sincronização. |
| [Update-AzureRmSqlSyncSchema](https://docs.microsoft.com/powershell/module/azurerm.sql/update-azurermsqlsyncschema) | Atualiza um esquema de sincronização. |
|||

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Os exemplos de script do PowerShell do Banco de Dados SQL adicionais podem ser encontrados nos [scripts do PowerShell do Banco de Dados SQL do Azure](../sql-database-powershell-samples.md).

Para saber mais sobre a Sincronização de Dados SQL, veja:

-   [Sincronizar dados entre vários bancos de dados locais e de nuvem com a Sincronização de Dados SQL do Azure](../sql-database-sync-data.md)
-   [Configurar a Sincronização de Dados SQL do Azure](../sql-database-get-started-sql-data-sync.md)
-   [Melhores práticas para a Sincronização de Dados SQL do Azure](../sql-database-best-practices-data-sync.md)
-   [Monitorar Sincronização de Dados SQL do Azure com o Log Analytics do OMS](../sql-database-sync-monitor-oms.md)
-   [Solucionar problemas com a Sincronização de Dados SQL do Azure](../sql-database-troubleshoot-data-sync.md)

-   Conclua os exemplos do PowerShell que mostram como configurar a Sincronização de Dados SQL:
    -   [Usar o PowerShell para sincronização entre vários banco de dados SQL do Azure](sql-database-sync-data-between-sql-databases.md)
    -   [Usar o PowerShell para sincronizar entre um Banco de Dados SQL do Azure e um banco de dados local do SQL Server](sql-database-sync-data-between-azure-onprem.md)

-   [Baixe a documentação da API REST de Sincronização de Dados SQL](https://github.com/Microsoft/sql-server-samples/raw/master/samples/features/sql-data-sync/Data_Sync_Preview_REST_API.pdf?raw=true)

Para saber mais sobre o Banco de Dados SQL, veja:

-   [Visão geral do Banco de Dados SQL](../sql-database-technical-overview.md)
-   [Gerenciamento de ciclo de vida do banco de dados](https://msdn.microsoft.com/library/jj907294.aspx)
