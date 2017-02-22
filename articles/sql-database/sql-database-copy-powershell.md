---
title: Copiar um banco de dados SQL do Azure usando o PowerShell | Microsoft Docs
description: "Criar cópia de um Banco de Dados SQL do Azure usando o PowerShell"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 6d9839d7-9303-48d2-be0f-21ce84f95a94
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 09/08/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: edfbc0d94d9d5b33a25788772a98173187e394b8
ms.openlocfilehash: 031a17d179cee8d6cceb9ed3aefd993add1958b8


---
# <a name="copy-an-azure-sql-database-using-powershell"></a>Copiar um banco de dados SQL do Azure usando o PowerShell

Este artigo mostra como copiar um banco de dados SQL com o PowerShell para o mesmo servidor ou um servidor diferente ou copiar um banco de dados para um [pool elástico](sql-database-elastic-pool.md). A operação de cópia do banco de dados usa o cmdlet [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/mt603644\(v=azure.300\).aspx) . 

> [!NOTE]
> Também é possível criar um Banco de Dados SQL usando o [Portal do Azure](sql-database-copy-portal.md) ou o [Transact-SQL](sql-database-copy-transact-sql.md).
>

Para concluir este artigo, você precisa do seguinte:

* Um banco de dados SQL do Azure (um banco de dados para cópia). Se você não tiver um banco de dados SQL, crie um executando as etapas neste artigo: [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md).
* Versão mais recente do Azure PowerShell. Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](/powershell/azureps-cmdlets-docs).

Muitos recursos novos do Banco de Dados SQL só terão suporte quando você estiver usando o [modelo de implantação do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md), portanto, os exemplos usam os [cmdlets do PowerShell do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx) para o Resource Manager. Os cmdlets do Banco de Dados SQL do Azure (clássico) do [modelo de implantação clássico existente](https://msdn.microsoft.com/library/azure/dn546723\(v=azure.300\).aspx) têm suporte para a compatibilidade com versões anteriores, mas é recomendável usar os cmdlets do Resource Manager.

> [!NOTE]
> Dependendo do tamanho do banco de dados, a operação de cópia poderá demorar a ser concluída.
> 
> 

## <a name="copy-a-sql-database-to-the-same-server"></a>Copiar um banco de dados SQL para o mesmo servidor
Para criar a cópia no mesmo servidor, omita o parâmetro `-CopyServerName` (ou defina-o para o mesmo servidor).

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyDatabaseName "database1_copy"

## <a name="copy-a-sql-database-to-a-different-server"></a>Copiar um banco de dados SQL para um servidor diferente
Para criar a cópia em um servidor diferente, inclua o parâmetro `-CopyServerName` e defina-o para um servidor diferente. O servidor de *cópia* já deve existir. Se ele estiver em um grupo de recursos diferente, você também deverá incluir o parâmetro `-CopyResourceGroupName` .

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyServerName "server2" -CopyDatabaseName "database1_copy"


## <a name="copy-a-sql-database-into-an-elastic-pool"></a>Copiar um banco de dados SQL para um pool elástico
Para criar uma cópia de um banco de dados SQL em um pool, defina o parâmetro `-ElasticPoolName` para um pool existente.

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegoup1" -ServerName "server1" -DatabaseName "database1" -CopyResourceGroupName "poolResourceGroup" -CopyServerName "poolServer1" -CopyDatabaseName "database1_copy" -ElasticPoolName "poolName"


## <a name="resolve-logins"></a>Resolver logons
Para resolver logons após a conclusão da operação de cópia, confira [Resolver logons](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)

## <a name="example-powershell-script"></a>Exemplo de script do PowerShell
O script a seguir pressupõe que todos os grupos de recursos, os servidores e o pool já existam (substitua os valores de variáveis por seus recursos existentes). Tudo deve existir, exceto a cópia do banco de dados.

    # Sign in to Azure and set the subscription to work with
    # ------------------------------------------------------
    $SubscriptionId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId


    # SQL database source (the existing database to copy)
    # ---------------------------------------------------
    $sourceDbName = "db1"
    $sourceDbServerName = "server1"
    $sourceDbResourceGroupName = "rg1"

    # SQL database copy (the new db to be created)
    # --------------------------------------------
    $copyDbName = "db1_copy"
    $copyDbServerName = "server2"
    $copyDbResourceGroupName = "rg2"

    # Copy a database to the same server
    # ----------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyDatabaseName $copyDbName

    # Copy a database to a different server
    # -------------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -CopyDatabaseName $copyDbName

    # Copy a database into an elastic pool
    # ---------------------------------------------
    $poolName = "pool1"

    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -ElasticPoolName $poolName -CopyDatabaseName $copyDbName





## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre como gerenciar usuários e logons ao copiar um banco de dados para um servidor lógico diferente, confira [Como gerenciar a segurança do banco de dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md).
* Para exportar um banco de dados para um arquivo BACPAC usando o PowerShell, veja [Exportar o banco de dados para um BACPAC usando o PowerShell](sql-database-export-powershell.md).
* [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
* [Documentação do banco de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)

## <a name="additional-resources"></a>Recursos adicionais
* [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt603644\(v=azure.300\).aspx)
* [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/mt603687\(v=azure.300\).aspx)
* [Gerenciar logons](sql-database-manage-logins.md)
* [Conectar-se ao Banco de Dados SQL com o SQL Server Management Studio e executar um exemplo de consulta T-SQL](sql-database-connect-query-ssms.md)
* [Exportar o banco de dados para um BACPAC](sql-database-export.md)
* [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
* [Documentação do Banco de Dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)
* [Referência de cmdlet do PowerShell de Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/mt574084\(v=azure.300\).aspx)




<!--HONumber=Feb17_HO2-->


