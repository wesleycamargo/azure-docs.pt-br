---
title: Gerenciamento de espaço de arquivo do Banco de Dados SQL do Azure | Microsoft Docs
description: Esta página descreve como gerenciar o espaço no arquivo com o Banco de Dados SQL do Azure e fornece exemplos de código para determinar se você precisa reduzir um banco de dados, além de como executar uma operação de redução do banco de dados.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: how-to
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: 1ecc0ce08ef42f5f5935bca29e8269be2ea142f0
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39415999"
---
# <a name="manage-file-space-in-azure-sql-database"></a>Gerenciar espaço no arquivo no Banco de Dados SQL do Azure

Este artigo descreve os diferentes tipos de espaço de armazenamento no Banco de Dados SQL do Azure e as etapas que podem ser executadas quando o espaço no arquivo alocado para bancos de dados e pools elásticos precisa ser gerenciado pelo cliente.

## <a name="overview"></a>Visão geral

No Banco de Dados SQL do Azure, as métricas de tamanho de armazenamento exibidas no portal do Azure e as seguintes APIs medem o número de páginas de dados usadas para bancos de dados e pools elásticos:
- APIs de métricas baseadas no Azure Resource Manager, incluindo [get-metrics](https://docs.microsoft.com/powershell/module/azurerm.insights/get-azurermmetric) do PowerShell
- T-SQL: [sys.DM db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)
- T-SQL: [sys. resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

Existem padrões de carga de trabalho nos quais a alocação de espaço nos arquivos de dados subjacentes para bancos de dados se torna maior do que o número de páginas de dados usadas nos arquivos de dados. Esse cenário pode ocorrer quando o espaço usado aumenta e, em seguida, os dados são excluídos posteriormente. Quando os dados são excluídos, o espaço no arquivo alocado não é recuperado automaticamente quando os dados são excluídos. Nesses cenários, o espaço alocado para um banco de dados ou pool pode exceder os limites máximos suportados configurados (ou suportados) para o banco de dados e, como resultado, impedir o crescimento de dados ou evitar alterações no nível de desempenho, embora o espaço de banco de dados realmente usado seja menor que o limite máximo de espaço. Para atenuar, talvez seja necessário reduzir o banco de dados para reduzir o espaço alocado mas não usado no banco de dados.

O serviço Banco de Dados SQL não reduz automaticamente os arquivos de banco de dados para recuperar espaço alocado não utilizado devido ao possível impacto no desempenho do banco de dados. No entanto, você pode reduzir o arquivo em um banco de dados no momento de sua escolha, seguindo as etapas descritas em [Recuperar espaço não utilizado alocado](#reclaim-unused-allocated-space). 

> [!NOTE]
> Ao contrário dos arquivos de dados, o serviço Banco de Dados SQL reduz automaticamente os arquivos de log, pois essa operação não afeta o desempenho do banco de dados.

## <a name="understanding-the-types-of-storage-space-for-a-database"></a>Entendendo os tipos de espaço de armazenamento para um banco de dados

Para gerenciar o espaço no arquivo, você precisa entender os seguintes termos relacionados ao armazenamento do banco de dados para um único banco de dados e para um conjunto elástico.

|Termo do espaço de armazenamento|Definição|Comentários|
|---|---|---|
|**Espaço de dados usado**|A quantidade de espaço usada para armazenar dados do banco de dados em páginas de 8 KB.|Geralmente, esse espaço utilizado aumenta (diminui) em inserções (exclusões). Em alguns casos, o espaço utilizado não é alterado em inserções ou exclusões, dependendo da quantidade e do padrão de dados envolvidos na operação e de qualquer fragmentação. Por exemplo, excluir uma linha de cada página de dados não diminui necessariamente o espaço usado.|
|**Espaço alocado**|A quantidade de espaço no arquivo formatado disponibilizada para armazenar dados do banco de dados|O espaço alocado cresce automaticamente, mas nunca diminui após as exclusões. Esse comportamento garante que as futuras inserções sejam mais rápidas, já que o espaço não precisa ser reformatado.|
|**Espaço alocado, mas não usado**|A quantidade de espaço no arquivo de dados não utilizado alocada para o banco de dados.|Essa quantidade é a diferença entre a quantidade de espaço alocado e o espaço usado e representa a quantidade máxima de espaço que pode ser recuperada ao encolher arquivos de banco de dados.|
|**Tamanho máximo**|A quantidade máxima de espaço para dados que pode ser usada pelo banco de dados.|O espaço para dados alocado não pode crescer além do tamanho máximo dos dados.|
||||

O diagrama a seguir ilustra o relacionamento entre os tipos de espaço de armazenamento.

![relações e tipos de espaço de armazenamento](./media/sql-database-file-space-management/storage-types.png)

## <a name="query-a-database-for-storage-space-information"></a>Consultar um banco de dados para informações de espaço de armazenamento

Para determinar se você alocou, mas não usou espaço de dados para um banco de dados individual que você deseja recuperar, use as seguintes consultas:

### <a name="database-data-space-used"></a>Espaço de dados do banco de dados usado
Modifique a consulta a seguir para retornar a quantidade de espaço para dados do banco de dados usada em MB.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-allocated-and-allocated-space-unused"></a>Dados do banco de dados alocados e espaço alocado não utilizado
Modifique a consulta a seguir para retornar a quantidade de dados do banco de dados alocados e o espaço alocado não utilizado.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, 
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB 
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```
 
### <a name="database-max-size"></a>Tamanho máximo do banco de dados
Modifique a consulta a seguir para retornar o tamanho máximo do banco de dados em bytes.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Consultar um pool elástico para informações de espaço de armazenamento

Para determinar se você alocou, mas não usou o espaço para dados em um pool elástico e para cada banco de dados em pool que você deseja recuperar, use as seguintes consultas:

### <a name="elastic-pool-data-space-used"></a>Espaço de dados do pool elástico usado
Modifique a consulta a seguir para retornar a quantidade de espaço para dados do conjunto elástico usado em MB.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-allocated-and-allocated-space-unused"></a>Dados do conjunto elástico alocados e espaço alocado não utilizado

Modifique o seguinte script do PowerShell para retornar uma tabela listando o espaço total alocado e o espaço não utilizado alocado para cada banco de dados em um pool elástico. A tabela ordena bancos de dados daqueles com a maior quantidade de espaço alocado não utilizado para a menor quantidade de espaço alocado não utilizado.  

Os resultados da consulta para determinar o espaço alocado para cada banco de dados no conjunto podem ser incluídos juntos para determinar o espaço do conjunto elástico alocado. O espaço do pool elástico alocado não deve exceder o tamanho máximo do pool elástico.  

```powershell
# Resource group name
$resourceGroupName = "rg1" 
# Server name
$serverName = "ls2" 
# Elastic pool name
$poolName = "ep1"
# User name for server
$userName = "name"
# Password for server
$password = "password"

# Get list of databases in elastic pool
$databasesInPool = Get-AzureRmSqlElasticPoolDatabase `
    -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# For each database in the elastic pool,
# get its space allocated in MB and space allocated unused in MB.
# Requires SQL Server PowerShell module – see here to install.  
foreach ($database in $databasesInPool)
{
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn `
        -Database $database.DatabaseName `
        -Username $userName `
        -Password $password `
        -Query $sqlCommand)
}
# Display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort `
    -Property DatabaseDataSpaceAllocatedUnusedInMB `
    -Descending | Format-Table
```

A captura de tela a seguir é um exemplo da saída do script:

![espaço alocado de pool elástico e exemplo de espaço alocado não utilizado](./media/sql-database-file-space-management/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-max-size"></a>Tamanho máximo do pool Elástico

Use a seguinte consulta T-SQL para retornar o tamanho máximo do banco de dados elástico em MB.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>Recuperar espaço alocado não utilizado

Depois de determinar que você tem espaço alocado não usado que deseja recuperar, use o comando a seguir para reduzir o espaço do banco de dados alocado. 

> [!IMPORTANT]
> Para bancos de dados em um pool elástico, os bancos de dados com mais espaço alocado não utilizado devem ser reduzidos primeiro para recuperar o espaço no arquivo mais rapidamente.  

Use o seguinte comando para reduzir todos os arquivos de dados no banco de dados especificado:

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'<database_name>')
```

Para obter mais informações sobre este comando, consulte [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).

> [!IMPORTANT] 
> Considerar a reconstrução de índices do banco de dados Depois que os arquivos de dados do banco de dados são reduzidos, os índices podem se tornar fragmentados e perder a eficácia da otimização do desempenho. Se isso ocorrer, os índices devem ser recriados. Para obter mais informações sobre fragmentação e reconstrução de índices, consulte [Reorganizar e reconstruir índices](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre tamanhos máximos do banco de dados, consulte:
  - [Banco de dados SQL do Azure Limites do modelo de compra baseado no vCore para um único banco de dados](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases)
  - [Limites de recursos para bancos de dados individuais usando o modelo de compra baseado em DTU](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-single-databases)
  - [Banco de dados SQL do Azure limites de modelo para pools Elásticos de compra baseado em vCore](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools)
  - [Limites de recursos para pools elásticos usando o modelo de compra baseado em DTU](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-elastic-pools)
- Para mais informações sobre o `SHRINKDATABASE`comando, consulte [SHRINKDATABASE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql). 
- Para obter mais informações sobre fragmentação e reconstrução de índices, consulte [Reorganizar e reconstruir índices](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).