---
title: Dimensionar os recursos de pool elástico – banco de dados do Azure SQL | Microsoft Docs
description: Esta página descreve os recursos de escala para pools elásticos no Banco de Dados do Azure SQL.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 0d15382f413485ccc287bac945b3c88eb748a9f6
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36311330"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Dimensionar os recursos de pool elástico no banco de dados do Azure SQL

Este artigo descreve como dimensionar os recursos de computação e armazenamento disponíveis para pools elásticos e bancos de dados em pool no Banco de Dados do Azure SQL. 

## <a name="vcore-based-purchasing-model-change-elastic-pool-storage-size"></a>Modelo de compra com base em vCore: alterar o tamanho de armazenamento de pool elástico

- O armazenamento pode ser provisionado até o limite de tamanho máximo: 
 - Para armazenamento Standard, aumente ou diminua o tamanho em incrementos de 10 GB
 - Para armazenamento Premium, aumente ou diminua o tamanho em incrementos de 250 GB
- O armazenamento de um pool elástico pode ser provisionado aumentando ou diminuindo seu tamanho máximo.
- O preço do armazenamento para um pool elástico é a quantidade de armazenamento multiplicada pelo preço unitário do armazenamento da camada de serviço. Para obter detalhes sobre o preço de armazenamento extra, confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="vcore-based-purchasing-model-change-elastic-pool-compute-resources-vcores"></a>Modelo de compra com base em vCore: alterar recursos de computação de pool elástico (vCores)

Você pode aumentar ou diminuir o nível de desempenho para um pool elástico com base nos recurso necessários usando o [Portal do Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), a [CLI do Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) ou a [API REST](/rest/api/sql/elasticpools/update).

- No redimensionamento de vCores de pool, as conexões de banco de dados ficam desativadas por um curto período. Esse é o mesmo comportamento que ocorre ao redimensionar DTUs para um único banco de dados (não em um pool). Para obter detalhes sobre a duração e o impacto da inativação das conexões de um banco de dados durante as operações de redimensionamento, confira [Redimensionar DTUs para um único banco de dados](#single-database-change-storage-size). 
- A duração do redimensionamento de vCores do pool pode depender da quantidade total de espaço de armazenamento usado por todos os bancos de dados no pool. Em geral, a latência do redimensionamento tem uma média de 90 minutos ou menos por 100 GB. Por exemplo, se o espaço total usado por todos os bancos de dados no pool for de 200 GB, a latência prevista para redimensionar o pool será de 3 horas ou menos. Em alguns casos, na camada Standard ou Básica, a latência de redimensionamento pode ficar abaixo de cinco minutos, independentemente da quantidade de espaço usado.
- Em geral, a duração da alteração dos vCores mínimos ou dos vCores máximos por banco de dados é um processo de cinco minutos ou menos.
- No downgrade de vCores de pool, o espaço usado do pool deve ser inferior ao tamanho máximo permitido para a camada de serviço e os vCores do pool de destino.

## <a name="dtu-based-purchasing-model-change-elastic-pool-storage-size"></a>Modelo de compra com base em DTU: alterar o tamanho de armazenamento do pool elástico

- O preço de eDTU para um pool elástico inclui uma determinada quantidade de armazenamento sem custo adicional. O armazenamento extra além da quantidade incluída pode ser provisionado mediante um custo adicional até o limite máximo de tamanho, em incrementos de 250 GB até 1 TB e, em seguida, em incrementos de 256 GB além de 1 TB. Para conhecer os valores de armazenamento incluídos e os limites de tamanho máximos, confira [Pool elástico: tamanhos de armazenamento e níveis de desempenho](#elastic-pool-storage-sizes-and-performance-levels).
- É possível provisionar o armazenamento extra para um pool elástico aumentando seu tamanho máximo usando o [Portal do Azure](sql-database-elastic-pool-scale.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), a [CLI do Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) ou a [API REST](/rest/api/sql/elasticpools/update).
- O preço do armazenamento extra para um pool elástico é a quantidade de armazenamento extra multiplicada pelo preço unitário do armazenamento extra da camada de serviço. Para obter detalhes sobre o preço de armazenamento extra, confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="dtu-based-purchasing-model-change-elastic-pool-compute-resources-edtus"></a>Modelo de compra com base em DTU: alterar recursos de computação de pool elástico (eDTUs)

Você pode aumentar ou diminuir os recursos disponíveis para um pool elástico com base nos recurso necessários usando o [Portal do Azure](sql-database-elastic-pool-scale.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), a [CLI do Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), ou a [API REST](/rest/api/sql/elasticpools/update).

- Ao redimensionar eDTUs de pool, as conexões de banco de dados ficam desativadas por um curto período. Esse é o mesmo comportamento que ocorre ao redimensionar DTUs para um único banco de dados (não em um pool). Para obter detalhes sobre a duração e o impacto da inativação das conexões de um banco de dados durante as operações de redimensionamento, confira [Redimensionar DTUs para um único banco de dados](#single-database-change-storage-size). 
- A duração do redimensionamento de eDTUs do pool pode depender da quantidade total de espaço de armazenamento usado por todos os bancos de dados no pool. Em geral, a latência do redimensionamento tem uma média de 90 minutos ou menos por 100 GB. Por exemplo, se o espaço total usado por todos os bancos de dados no pool for de 200 GB, a latência prevista para redimensionar o pool será de 3 horas ou menos. Em alguns casos, na camada Standard ou Básica, a latência de redimensionamento pode ficar abaixo de cinco minutos, independentemente da quantidade de espaço usado.
- Em geral, a duração da alteração das eDTUs mínimas ou das eDTUs máximas por banco de dados é um processo de cinco minutos ou menos.
- Ao fazer o downgrade de eDTUs de pool, o espaço usado do pool deve ser inferior ao tamanho máximo permitido para a camada de serviço e os eDTUs do pool de destino.
- Ao redimensionar eDTUs de pool, um custo de armazenamento extra será aplicado se (1) o tamanho máximo de armazenamento do pool tiver suporte do pool de destino, e (2) o tamanho máximo do armazenamento ultrapassar a quantidade de armazenamento incluída do pool de destino. Por exemplo, se um pool Standard de 100 eDTU com um tamanho máximo de 100 GB for reduzido para um pool Standard de 50 eDTU, um custo de armazenamento extra será aplicado, pois o pool de destino dá suporte a um tamanho máximo de 100 GB, e a quantidade de armazenamento incluído é somente de 50 GB. Assim, a quantidade de armazenamento extra será 100 GB – 50 GB = 50 GB. Para obter o preço do armazenamento extra, confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço usado for menor do que a quantidade de armazenamento incluído, esse custo extra poderá ser evitado por meio da redução do tamanho máximo do banco de dados para a quantidade incluída. 

## <a name="next-steps"></a>Próximas etapas

Para saber os limites de recurso global, confira [Limites de recurso baseado em vCore do banco de dados SQL – pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md) e [Limites de DTU de banco de dados do SQL com base em recurso – pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md).
