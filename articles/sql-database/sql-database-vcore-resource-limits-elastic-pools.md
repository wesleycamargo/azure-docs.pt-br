---
title: Limites de recursos baseados em vCore do Banco de Dados SQL do Azure – pool elásticos | Microsoft Docs
description: Esta página descreve alguns limites de recursos baseados em vCore comuns para pools elásticos no Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: f41974c6e2b2b0565f0a2703cfd638777a6bb9eb
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52878016"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-elastic-pools"></a>Limites do modelo de compra baseado em vCore para Banco de Dados SQL do Azure para pools elásticos

Este artigo fornece os limites de recursos detalhados para pools elásticos do Banco de Dados SQL e bancos de dados em pool usando o modelo de compra baseado em vCore.

Para limites de modelo de compra baseados em DTU, veja [Limites de recursos baseados em DTU de Banco de Dados SQL – pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL](sql-database-file-space-management.md).

Você pode definir a camada de serviço, o tamanho de computação e a quantidade de armazenamento usando o [portal do Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), o [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), a [CLI do Azure](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases) ou a [API REST](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!NOTE]
> Os limites de recursos de bancos de dados individuais em pools elásticos geralmente são os mesmos dos bancos de dados individuais fora dos pools com o mesmo tamanho de computação. Por exemplo, o máximo de trabalhos simultâneos para um banco de dados GP_Gen4_1 é 200. Assim, o máximo de trabalhos simultâneos para um banco de dados em um pool de GP_Gen4_1 também é 200. Observe que o número total de trabalhos simultâneos no pool de GP_Gen4_1 é 210.

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>Camada de serviço de Uso Geral: Tamanhos de armazenamento e tamanhos de computação

### <a name="generation-4-compute-platform"></a>Plataforma de computação de Geração 4

|Tamanho de computação|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de hardware|4|4|4|4|4|4|
|vCores|1|2|4|8|16|24|
|Memória (GB)|7|14|28|56|112|168|
|Suporte de columnstore|SIM|sim|sim|sim|sim|SIM|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Tamanho máximo de dados (GB)|512|756|1536|2.048|3584|4096|
|Tamanho máximo de log|154|227|461|614|1075|1229|
|Tamanho de TempDB (DB)|32|64|128|256|384|384|
|IOPS de destino (64 KB)|500|1000|2000|4000|7000|7000|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|Máximo de trabalhos simultâneos (solicitações)|210|420|840|1680|3360|5040|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|
|Número máximo de BDs por pool|100|200|500|500|500|500|
|Opções mínimas/máximas de vCore do pool elástico por banco de dados|0, 0,25, 0,5, 1|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0.25, 0.5, 1, 2, 4, 8, 16, 24|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|
|||

### <a name="generation-5-compute-platform"></a>Plataforma de computação de Geração 5

|Tamanho de computação|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|Geração de hardware|5|5|5|5|5|5|5|5|
|vCores|2|4|8|16|24|32|40|80|
|Memória (GB)|11|22|44|88|132|176|220|440|
|Suporte de columnstore|SIM|sim|sim|sim|sim|sim|sim|SIM|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Tamanho máximo de dados (GB)|512|756|1536|2.048|3072|4096|4096|4096|
|Tamanho máximo de log|154|227|461|614|922|1229|1229|1229|
|Tamanho de TempDB (DB)|64|128|256|384|384|384|384|384|
|IOPS de destino (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|Máximo de trabalhos simultâneos (solicitações)|210|420|840|1680|2520|3360|4200|8400
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|30000|30000|
|Número máximo de BDs por pool|100|200|500|500|500|500|500|500|
|Opções mínimas/máximas de vCore do pool elástico por banco de dados|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0.25, 0.5, 1, 2, 4, 8, 16, 24|0, 0.5, 1, 2, 4, 8, 16, 24, 32|0, 0.5, 1, 2, 4, 8, 16, 24, 32, 40|0, 0.5, 1, 2, 4, 8, 16, 24, 32, 40, 80|
|Número de réplicas|1|1|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|
|||

## <a name="business-critical-service-tier-storage-sizes-and-compute-sizes"></a>Camada de serviço Comercialmente Crítico: Tamanhos de armazenamento e tamanhos de computação

### <a name="generation-4-compute-platform"></a>Plataforma de computação de Geração 4

|Tamanho de computação|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de hardware|4|4|4|4|4|4|
|vCores|1|2|4|8|16|24|
|Memória (GB)|7|14|28|56|112|168|
|Suporte de columnstore|SIM|sim|sim|sim|sim|SIM|
|Armazenamento OLTP na memória (GB)|1|2|4|8|20|36|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Tamanho máximo de dados (GB)|1024|1024|1024|1024|1024|1024|
|Tamanho máximo de log|307|307|307|307|307|307|
|Tamanho de TempDB (DB)|32|64|128|256|384|384|
|IOPS de destino (64 KB)|5.000|10000|20000|40000|80000|120000|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|Máximo de trabalhos simultâneos (solicitações)|210|420|840|1680|3360|5040|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|
|Número máximo de BDs por pool|Somente bancos de dados individuais têm suporte nesse tamanho de computação|50|100|100|100|100|
|Opções mínimas/máximas de vCore do pool elástico por banco de dados|N/D|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0.25, 0.5, 1, 2, 4, 8, 16, 24|
|Número de réplicas|3|3|3|3|3|3|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|SIM|sim|sim|sim|sim|SIM|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|
|||

#### <a name="generation-5-compute-platform"></a>Plataforma de computação de Geração 5

|Tamanho de computação|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|GP_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|Geração de hardware|5|5|5|5|5|5|5|5|
|vCores|2|4|8|16|24|32|40|80|
|Memória (GB)|11|22|44|88|132|176|220|440|
|Suporte de columnstore|SIM|sim|sim|sim|sim|sim|sim|SIM|
|Armazenamento OLTP na memória (GB)|1. 571|3.142|6.284|15.768|25.252|37.936|52.22|131.64|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|Tamanho máximo de dados (GB)|1024|1024|1024|1024|2.048|4096|4096|4096|
|Tamanho máximo de log|307|307|307|307|614|1229|1229|1229|
|Tamanho de TempDB (DB)|64|128|256|384|384|384|384|384|
|IOPS de destino (64 KB)|5.000|10000|20000|40000|60000|80000|100000|200000
|Máximo de trabalhos simultâneos (solicitações)|210|420|840|1680|2520|3360|5040|8400|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|30000|30000|
|Número máximo de BDs por pool|N/D|50|100|100|100|100|100|100|
|Opções mínimas/máximas de vCore do pool elástico por banco de dados|N/D|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0.25, 0.5, 1, 2, 4, 8, 16, 24|0, 0.5, 1, 2, 4, 8, 16, 24, 32|0, 0.5, 1, 2, 4, 8, 16, 24, 32, 40|0, 0.5, 1, 2, 4, 8, 16, 24, 32, 40, 80|
|Número de réplicas|3|3|3|3|3|3|3|3|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|SIM|sim|sim|sim|sim|sim|sim|SIM|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|
|||

Se todos os vCores de um pool elástico estiverem ocupados, cada banco de dados no pool receberá uma quantidade igual de recursos de computação para processar as consultas. O serviço de Banco de Dados SQL fornece integridade de compartilhamento de recursos entre os bancos de dados ao garantir fatias iguais de tempo de computação. A integridade de compartilhamento de recursos do pool elástico é adicional a qualquer quantidade de recursos garantidos de outra forma a cada banco de dados quando o mínimo de vCores por banco de dados é definido com um valor diferente de zero.

## <a name="database-properties-for-pooled-databases"></a>Propriedades do banco de dados para bancos de dados em pool

A tabela a seguir descreve as propriedades dos bancos de dados em pool.

| Propriedade | DESCRIÇÃO |
|:--- |:--- |
| Máximo de vCores por banco de dados |O número máximo de vCores que qualquer banco de dados no pool pode usar, se disponível, com base na utilização por outros bancos de dados no pool. O máximo de vCores por banco de dados não é uma garantia de recursos para um banco de dados. Essa configuração é uma configuração global que se aplica a todos os bancos de dados no pool. Defina um valor para o máximo de vCores por banco de dados que seja alto o suficiente para lidar com picos de utilização do banco de dados. Espera-se um grau de sobrecarga, uma vez que o pool normalmente assume padrões de uso dos bancos de dados com altos e baixos, em que todos os bancos de dados não atingem um pico simultaneamente.|
| Mínimo de VCores por banco de dados |O número mínimo de vCores garantido para qualquer banco de dados no pool. Essa configuração é uma configuração global que se aplica a todos os bancos de dados no pool. O mínimo de vCores por banco de dados pode ser definido como 0 e também é o valor padrão. Essa propriedade é definida entre 0 e a utilização média de vCores por banco de dados. O produto do número de bancos de dados no pool e o mínimo de vCores por banco de dados não pode exceder os vCores por pool.|
| Armazenamento máximo por banco de dados |O tamanho máximo do banco de dados definido pelo usuário para um banco de dados em um pool. Os bancos de dados em pool compartilham armazenamento de pool alocado, para que o tamanho que um banco de dados possa atingir seja limitado ao menor tamanho de banco de dados e armazenamento de pool restante. O tamanho máximo por banco de dados refere-se ao tamanho máximo dos arquivos de dados e não inclui o espaço usado pelos arquivos de log. |
|||

## <a name="next-steps"></a>Próximas etapas

- Consulte [Perguntas frequentes sobre Banco de Dados SQL](sql-database-faq.md) para obter respostas para as perguntas frequentes.
- Consulte [Visão geral dos limites de recursos em um servidor lógico](sql-database-resource-limits-logical-server.md) para obter informações sobre limites nos níveis de servidor e assinatura.
- Para saber mais sobre limites gerais do Azure, confira [Assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md).
