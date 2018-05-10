---
title: Limites de recursos baseados em vCore do Banco de Dados SQL do Azure | Microsoft Docs
description: Esta página descreve alguns limites de recursos baseados em vCore comuns para o Banco de Dados SQL do Azure.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/04/2018
ms.author: carlrab
ms.openlocfilehash: 204702eee1cf502ac873e0c1f5e3fd257ecce33c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-preview"></a>Limites do modelo de compra baseado em vCore para o Banco de Dados SQL do Azure (visualização)

> [!IMPORTANT]
> Para limites de modelo de compra baseados em DTU, consulte [Limites de recursos baseados em DTU de Banco de Dados SQL](sql-database-dtu-resource-limits.md).

## <a name="single-database-storage-sizes-and-performance-levels"></a>Banco de dados individual: níveis de tamanhos e desempenho de armazenamento

Para bancos de dados individuais, as tabelas a seguir mostram os recursos disponíveis para um único banco de dados em cada nível de desempenho e camada de serviço. Você pode definir a camada de serviço, o nível de desempenho e a quantidade de armazenamento para um único banco de dados usando o [Portal do Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](sql-database-single-database-resources.md#manage-single-database-resources-using-transact-sql), [PowerShell](sql-database-single-database-resources.md#manage-single-database-resources-using-powershell), [CLI do Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-cli) ou a [API REST](sql-database-single-database-resources.md#manage-single-database-resources-using-the-rest-api).

### <a name="general-purpose-service-tier"></a>Camada de serviço de Uso Geral
|Nível de desempenho|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|
|:--- | --: |--: |--: |--: |--: |
|Geração de hardware|4|4|4|4|4|
|vCores|1|2|4|8|16|
|Memória (GB)|7|14|28|56|112|
|Suporte de columnstore|sim|sim|sim|sim|sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|Tamanho máximo de dados (GB)|1024|1024|1536|3072|4096|
|Tamanho máximo de log|307|307|461|922|1229|
|Tamanho de TempDB (DB)|32|64|128|256|384|
|IOPS de destino|320|640|1280|2560|5120|
|Latência de E/S (aproximada)|5-7 ms (gravação)
|Máximo de trabalhos simultâneos (solicitações)|200|400|800|1600|3200|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|
|Número de réplicas|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|
|||

### <a name="business-critical-service-tier"></a>Camada de serviço comercialmente crítica
|Nível de desempenho|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|
|:--- | --: |--: |--: |--: |--: |
|Geração de hardware|4|4|4|4|4|
|vCores|1|2|4|8|16|
|Memória (GB)|7|14|28|56|112|
|Suporte de columnstore|sim|sim|sim|sim|sim|
|Armazenamento OLTP na memória (GB)|1|2|4|8|20|
|Tipo de armazenamento|SSD anexado|SSD anexado|SSD anexado|SSD anexado|SSD anexado|
|Tamanho máximo de dados (GB)|1024|1024|1024|1024|1024|
|Tamanho máximo de log|307|307|307|307|307|
|Tamanho de TempDB (DB)|32|64|128|256|384|
|IOPS de destino|5.000|10000|20000|40000|80000|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|Máximo de trabalhos simultâneos (solicitações)|200|400|800|1600|3200|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|
|Número de réplicas|3|3|3|3|3|
|Multi-AZ|sim|sim|sim|sim|sim|
|Escala de leitura|sim|sim|sim|sim|sim|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|
|||

## <a name="single-database-change-storage-size"></a>Banco de dados individual: alterar o tamanho do armazenamento

- O armazenamento pode ser provisionado até o limite de tamanho máximo com incrementos de 1 GB. O armazenamento de dados configurável mínimo é de 5 GB 
- É possível provisionar o armazenamento para um único banco de dados aumentando ou diminuindo seu tamanho máximo usando o [Portal do Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [CLI do Azure](/cli/azure/sql/db#az_sql_db_update) ou [API REST](/rest/api/sql/databases/update).
- O banco de dados SQL automaticamente aloca 30% do armazenamento adicional para os arquivos de log e 32 GB por vCore para TempDB, mas não deve exceder 384 GB. O TempDB está localizado em um SSD anexado em todas as camadas de serviço.
- O preço do armazenamento para um único banco de dados é a soma das quantidades de armazenamento de dados e armazenamento de log multiplicada pelo preço unitário do armazenamento da camada de serviço. O custo de TempDB está incluído no preço de vCore. Para obter detalhes sobre o preço de armazenamento extra, confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-vcores"></a>Banco de dados individual: alterar vCores

Depois de escolher inicialmente o número de vCores, você poderá escalar verticalmente de forma dinâmica um banco de dados individual com base na experiência real usando o [Portal do Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), a [CLI do Azure](/cli/azure/sql/db#az_sql_db_update) ou a [API REST](/rest/api/sql/databases/update). 

A alteração da camada de serviço e/ou nível de desempenho de um banco de dados cria uma réplica do banco de dados original com o novo nível de desempenho e então faz a transição das conexões para réplica. Nenhum dado é perdido durante esse processo, mas durante o breve momento em que realizamos a transição para a réplica, conexões com o banco de dados são desabilitadas, então algumas transações em andamento podem ser revertidas. O período da transição varia, mas fica geralmente abaixo de 4 segundos e menos de 30 segundos 99% do tempo. Se houver muitas transações em andamento no momento de desabilitação das conexões, o período de transição poderá ser maior. 

A duração de todo o processo de expansão depende a camada tamanho e de serviço do banco de dados antes e após a alteração. Por exemplo, um banco de dados de 250 GB que está mudando para, de ou dentro de uma camada de serviço Uso Geral deverá ser concluído dentro de seis horas. Um banco de dados do mesmo tamanho cujos níveis de desempenho estão mudando dentro da camada de serviço Comercialmente Crítico deverá concluir a escala vertical dentro de três horas.

> [!TIP]
> Para monitorar operações em andamento, consulte: [Gerenciar operações usando a API REST SQL](/rest/api/sql/Operations/List), [Gerenciar operações usando a CLI](/cli/azure/sql/db/op), [Monitorar operações usando T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e esses dois comandos do PowerShell: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) e [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Se você estiver atualizando para um nível de serviço ou desempenho mais alto, o tamanho máximo do banco de dados não aumenta, a menos que você especifique explicitamente um tamanho maior (tamanho máximo).
* Para fazer downgrade de um banco de dados, o espaço usado dele deve ter um tamanho menor do que o máximo permitido para a camada de serviço e nível de desempenho de destino. 
* Ao atualizar um banco de dados com [replicação geográfica](sql-database-geo-replication-portal.md) habilitada, atualize seus bancos de dados secundários para o nível de desempenho desejado antes de atualizar o banco de dados primário (orientação geral para o melhor desempenho). Ao fazer upgrade para uma diferente, o upgrade do banco de dados secundário será exigido antes.
* Ao atualizar um banco de dados com [replicação geográfica](sql-database-geo-replication-portal.md) habilitada, faça downgrade de seus bancos de dados primários para o nível de desempenho desejado antes de fazer downgrade do banco de dados primário (orientação geral para o melhor desempenho). Ao fazer o downgrade para uma edição diferente, antes será necessário fazer o downgrade do banco de dados primário.
* As novas propriedades do banco de dados não serão aplicadas até que as alterações sejam concluídas.

## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>Pool elástico: tamanhos de armazenamento e níveis de desempenho

Para pools elásticos do Bancos de Dados SQL, as tabelas a seguir mostram os recursos disponíveis em cada nível de desempenho e camada de serviço. Você pode definir a camada de serviço, o nível de desempenho e a quantidade de armazenamento usando o [Portal do Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-powershell), [CLI do Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-cli) ou a [API REST](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-rest-api).

> [!NOTE]
> Os limites de recursos de bancos de dados individuais em pools elásticos geralmente são os mesmos dos bancos de dados individuais fora dos pools com o mesmo nível de desempenho. Por exemplo, o máximo de trabalhos simultâneos para um banco de dados GP_Gen4_1 é 200. Assim, o máximo de trabalhos simultâneos para um banco de dados em um pool de GP_Gen4_1 também é 200. Observe que o número total de trabalhos simultâneos no pool de GP_Gen4_1 é 210.

### <a name="general-purpose-service-tier"></a>Camada de serviço de Uso Geral
|Nível de desempenho|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|
|:--- | --: |--: |--: |--: |--: |
|Geração de hardware|4|4|4|4|4|
|vCores|1|2|4|8|16|
|Memória (GB)|7|14|28|56|112|
|Suporte de columnstore|sim|sim|sim|sim|sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Tamanho máximo de dados (GB)|512|756|1536|2.048|3584|
|Tamanho máximo de log|154|227|461|614|1075|
|Tamanho de TempDB (DB)|32|64|128|256|384|
|IOPS de destino|320|640|1280|2560|5120|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|Máximo de trabalhos simultâneos (solicitações)|210|420|840|1680|3360|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|
|Densidade de pool máxima|100|200|500|500|500|
|Paradas de clique do pool elástico mínimo/máximo|0, 0,25, 0,5, 1|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|
|Número de réplicas|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|
|||

### <a name="business-critical-service-tier"></a>Camada de serviço comercialmente crítica
|Nível de desempenho|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|
|:--- | --: |--: |--: |--: |--: |
|Geração de hardware|4|4|4|4|4|
|vCores|1|2|4|8|16|
|Memória (GB)|7|14|28|56|112|
|Suporte de columnstore|sim|sim|sim|sim|sim|
|Armazenamento OLTP na memória (GB)|1|2|4|8|20|
|Tipo de armazenamento|SSD anexado|SSD anexado|SSD anexado|SSD anexado|SSD anexado|
|Tamanho máximo de dados (GB)|1024|1024|1024|1024|1024|
|Tamanho máximo de log|307|307|307|461|614|
|Tamanho de TempDB (DB)|32|64|128|256|384|
|IOPS de destino|320|640|1280|2560|5120|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|Máximo de trabalhos simultâneos (solicitações)|210|420|840|1680|3360|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|
|Densidade de pool máxima|N/D|50|100|100|100|
|Paradas de clique do pool elástico mínimo/máximo|0, 0,25, 0,5, 1|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|
|Multi-AZ|sim|sim|sim|sim|sim|
|Escala de leitura|sim|sim|sim|sim|sim|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|
|||
Se todos os vCores de um pool elástico estiverem ocupados, cada banco de dados no pool receberá uma quantidade igual de recursos de computação para processar as consultas. O serviço de Banco de Dados SQL fornece integridade de compartilhamento de recursos entre os bancos de dados ao garantir fatias iguais de tempo de computação. A integridade de compartilhamento de recursos do pool elástico é adicional a qualquer quantidade de recursos garantidos de outra forma a cada banco de dados quando o mínimo de vCores por banco de dados é definido com um valor diferente de zero.

### <a name="database-properties-for-pooled-databases"></a>Propriedades do banco de dados para bancos de dados em pool

A tabela a seguir descreve as propriedades dos bancos de dados em pool.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| Máximo de vCores por banco de dados |O número máximo de vCores que qualquer banco de dados no pool pode usar, se disponível, com base na utilização por outros bancos de dados no pool. O máximo de vCores por banco de dados não é uma garantia de recursos para um banco de dados. Essa configuração é uma configuração global que se aplica a todos os bancos de dados no pool. Defina um valor para o máximo de vCores por banco de dados que seja alto o suficiente para lidar com picos de utilização do banco de dados. Espera-se um grau de sobrecarga, uma vez que o pool normalmente assume padrões de uso dos bancos de dados com altos e baixos, em que todos os bancos de dados não atingem um pico simultaneamente.|
| Mínimo de VCores por banco de dados |O número mínimo de vCores garantido para qualquer banco de dados no pool. Essa configuração é uma configuração global que se aplica a todos os bancos de dados no pool. O mínimo de vCores por banco de dados pode ser definido como 0 e também é o valor padrão. Essa propriedade é definida entre 0 e a utilização média de vCores por banco de dados. O produto do número de bancos de dados no pool e o mínimo de vCores por banco de dados não pode exceder os vCores por pool.|
| Armazenamento máximo por banco de dados |O tamanho máximo do banco de dados definido pelo usuário para um banco de dados em um pool. Os bancos de dados em pool compartilham armazenamento de pool alocado, para que o tamanho que um banco de dados possa atingir seja limitado ao menor tamanho de banco de dados e armazenamento de pool restante. O tamanho máximo por banco de dados refere-se ao tamanho máximo dos arquivos de dados e não inclui o espaço usado pelos arquivos de log. |
|||
 
## <a name="elastic-pool-change-storage-size"></a>Pool elástico: alterar o tamanho do armazenamento

- O armazenamento pode ser provisionado até o limite de tamanho máximo: 
 - Para armazenamento Standard, aumente ou diminua o tamanho em incrementos de 10 GB
 - Para armazenamento Premium, aumente ou diminua o tamanho em incrementos de 250 GB
- É possível provisionar o armazenamento para um pool elástico aumentando ou diminuindo seu tamanho máximo usando o [Portal do Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), a [CLI do Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) ou a [API REST](/rest/api/sql/elasticpools/update).
- O preço do armazenamento para um pool elástico é a quantidade de armazenamento multiplicada pelo preço unitário do armazenamento da camada de serviço. Para obter detalhes sobre o preço de armazenamento extra, confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="elastic-pool-change-vcores"></a>Pool elástico: alterar vCores

Você pode aumentar ou diminuir o nível de desempenho para um pool elástico com base nos recurso necessários usando o [Portal do Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), a [CLI do Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) ou a [API REST](/rest/api/sql/elasticpools/update).

- No redimensionamento de vCores de pool, as conexões de banco de dados ficam desativadas por um curto período. Esse é o mesmo comportamento que ocorre ao redimensionar DTUs para um único banco de dados (não em um pool). Para obter detalhes sobre a duração e o impacto da inativação das conexões de um banco de dados durante as operações de redimensionamento, confira [Redimensionar DTUs para um único banco de dados](#single-database-change-storage-size). 
- A duração do redimensionamento de vCores do pool pode depender da quantidade total de espaço de armazenamento usado por todos os bancos de dados no pool. Em geral, a latência do redimensionamento tem uma média de 90 minutos ou menos por 100 GB. Por exemplo, se o espaço total usado por todos os bancos de dados no pool for de 200 GB, a latência prevista para redimensionar o pool será de 3 horas ou menos. Em alguns casos, na camada Standard ou Básica, a latência de redimensionamento pode ficar abaixo de cinco minutos, independentemente da quantidade de espaço usado.
- Em geral, a duração da alteração dos vCores mínimos ou dos vCores máximos por banco de dados é um processo de cinco minutos ou menos.
- No downgrade de vCores de pool, o espaço usado do pool deve ser inferior ao tamanho máximo permitido para a camada de serviço e os vCores do pool de destino.

## <a name="what-is-the-maximum-number-of-servers-and-databases"></a>Qual é o número máximo de servidores e bancos de dados?

| Máximo | Valor |
| :--- | :--- |
| Bancos de dados por servidor | 5.000 |
| Número de servidores por assinatura por região | 20 |
|||

> [!IMPORTANT]
> Conforme o número de bancos de dados vai se aproximando do limite por servidor, pode ocorrer o seguinte:
> <br> • Aumento de latência nas consultas em execução no banco de dados mestre.  Isso inclui modos de exibição de estatísticas de utilização de recursos, como sys.resource_stats.
> <br> • Aumento de latência nas operações de gerenciamento e pontos de vista do portais de renderização que envolvem a enumeração de bancos de dados no servidor.

## <a name="what-happens-when-database-and-elastic-pool-resource-limits-are-reached"></a>O que acontece quando os limites de recursos do banco de dados e do pool elástico forem atingidos?

### <a name="compute-vcores"></a>Computação (vCores)

Quando a utilização de computação de banco de dados (medida por uso de vCore) torna-se alta, a latência da consulta aumenta e pode até mesmo atingir o tempo limite. Sob essas condições, as consultas poderão ser colocadas em fila pelo serviço e receberão recursos para execução à medida que os recursos forem liberados.
Ao encontrar uma utilização alta de computação, as opções de atenuação incluem:

- Aumentar o nível de desempenho do banco de dados ou do pool elástico a fim de fornecer ao banco de dados mais vCores. Consulte [Banco de dados individual: alterar vCores](#single-database-change-vcores) e [Pool elástico: alterar vCores](#elastic-pool-change-vcores).
- Otimizar consultas para reduzir a utilização de recursos de cada consulta. Para saber mais, consulte [Ajuste/Dicas de consulta](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Armazenamento

Quando o espaço usado do banco de dados atingir o limite de tamanho máximo, inserções e atualizações do banco de dados que aumentam o tamanho dos dados falharão, e os clientes receberão uma [mensagem de erro](sql-database-develop-error-messages.md). Os SELECTS e DELETES do banco de dados continuarão a ter êxito.

Ao encontrar uma utilização alta de espaço, as opções de atenuação incluem:

- Aumentar o tamanho máximo do banco de dados ou do pool elástico ou alterar o nível de desempenho para aumentar o armazenamento máximo. Consulte [Limites de recursos baseados em vCore do Banco de Dados SQL](sql-database-vcore-resource-limits.md).
- Se o banco de dados estiver em um pool elástico, o banco de dados poderá ser movido para fora do pool, de modo que seu espaço de armazenamento não seja compartilhado com outros bancos de dados.

### <a name="sessions-and-workers-requests"></a>Sessões e trabalhos (solicitações) 

O número máximo de sessões e trabalhos é determinado pelo nível de desempenho e camada de serviço. Novas solicitações serão rejeitadas quando os limites de sessão ou de trabalho forem atingidos, e os clientes receberão uma mensagem de erro. Embora o número de conexões disponíveis possa ser controlado pelo aplicativo, o número de trabalhos simultâneos costuma ser mais difícil de ser estimado e controlado. Isso é especialmente verdadeiro durante os períodos de pico de carga, quando os limites de recurso de banco de dados são atingidos e os trabalhos acumulam devido a consultas de execução mais longas. 

Ao encontrar uma utilização alta de sessão ou trabalho, as opções de atenuação incluem:
- Aumentar a camada de serviço ou o nível de desempenho do banco de dados ou do pool elástico. Consulte [Banco de dados individual: alterar o tamanho do armazenamento](#single-database-change-storage-size), [Banco de dados individual: alterar vCores](#single-database-change-vcores), [Pool elástico: alterar o tamanho do armazenamento](#elastic-pool-change-storage-size) e [Pool elástico: alterar vCores](#elastic-pool-change-vcores).
- Otimizar consultas para reduzir a utilização de recursos de cada consulta se a causa do aumento da utilização de trabalho for devido à contenção de recursos de computação. Para saber mais, consulte [Ajuste/Dicas de consulta](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>Próximas etapas

- Consulte [Perguntas frequentes sobre Banco de Dados SQL](sql-database-faq.md) para obter respostas para as perguntas frequentes.
- Para saber mais sobre limites gerais do Azure, confira [Assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md).
