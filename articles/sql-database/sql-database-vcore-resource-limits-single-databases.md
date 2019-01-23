---
title: Limites de recursos baseados em vCore do Banco de Dados SQL do Azure – banco de dados individual | Microsoft Docs
description: Esta página descreve alguns limites de recursos baseados em vCore comuns para um banco de dados único no Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 01/09/2019
ms.openlocfilehash: 894922a80ab874e5304ef441571e03ef559a34b0
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2019
ms.locfileid: "54215415"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-a-single-database"></a>Limites do modelo de compra com base em vCore do Banco de Dados SQL do Azure para um banco de dados individual

Este artigo fornece os limites de recursos detalhados para bancos de dados individuais do Banco de Dados SQL do Azure usando o modelo de compra com base em vCore.

Para limites do modelo de compra com base em DTU para bancos de dados individuais em um servidor lógico, consulte [Visão geral dos limites de recurso em um servidor lógico](sql-database-resource-limits-logical-server.md).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL](sql-database-file-space-management.md).

É possível definir a camada de serviço, o tamanho de computação e a quantidade de armazenamento para um banco de dados individual usando o [portal do Azure](sql-database-single-databases-manage.md#azure-portal-manage-logical-servers-and-databases), o [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-logical-servers-and-databases), o [PowerShell](sql-database-single-databases-manage.md#powershell-manage-logical-servers-and-databases), a [CLI do Azure](sql-database-single-databases-manage.md#azure-cli-manage-logical-servers-and-databases) ou a [API REST](sql-database-single-databases-manage.md#rest-api-manage-logical-servers-and-databases).

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>Camada de serviço de Uso Geral: Tamanhos de armazenamento e tamanhos de computação

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Camada de serviço de Uso Geral: Plataforma de computação de geração 4 (parte 1)

|Tamanho de computação|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de hardware|4|4|4|4|4|4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Suporte de columnstore|SIM|sim|sim|sim|sim|SIM|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|1024|1024|1024|1536|1536|1536|
|Tamanho máximo de log (GB)|307|307|307|461|461|461|
|Tamanho de TempDB (GB)|32|64|96|128|160|192|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de destino (64 KB)|500|1000|1500|2000|2500|3000|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1000|1.200|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|000
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Camada de serviço de Uso Geral: Plataforma de computação de geração 4 (parte 2)

|Tamanho de computação|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de hardware|4|4|4|4|4|4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|168|
|Suporte de columnstore|SIM|sim|sim|sim|sim|SIM|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|1536|3072|3072|3072|4096|4096|
|Tamanho máximo de log (GB)|461|922|922|922|1229|1229|
|Tamanho de TempDB (GB)|224|256|288|320|384|384|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)
|IOPS de destino (64 KB)|3500|4000|4500|5.000|7000|7000|
|Máximo de trabalhos simultâneos (solicitações)|1.400|1600|1800|2000|3200|4800|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Camada de serviço de Uso Geral: Plataforma de computação de geração 5 (parte 1)

|Tamanho de computação|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Geração de hardware|5|5|5|5|5|5|5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Suporte de columnstore|SIM|sim|sim|sim|sim|sim|SIM|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|1024|1024|1024|1536|1536|1536|1536|
|Tamanho máximo de log (GB)|307|307|307|461|461|461|461|
|Tamanho de TempDB (GB)|64|128|192|256|320|384|384|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de destino (64 KB)|500|1000|1500|2000|2500|3000|3500|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1000|1.200|1.400|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|30000|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Camada de serviço de Uso Geral: Plataforma de computação de geração 5 (parte 2)

|Tamanho de computação|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Geração de hardware|5|5|5|5|5|5|5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Suporte de columnstore|SIM|sim|sim|sim|sim|sim|SIM|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|3072|3072|3072|4096|4096|4096|4096|
|Tamanho máximo de log (GB)|922|922|922|1229|1229|1229|1229|
|Tamanho de TempDB (GB)|384|384|384|384|384|384|384|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de destino (64 KB)|4000|4500|5.000|6000|7000|7000|7000|
|Máximo de trabalhos simultâneos (solicitações)|1600|1800|2000|2400|3200|4000|8000|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|30000|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

## <a name="business-critical-service-tier-storage-sizes-and-compute-sizes"></a>Camada de serviço Comercialmente Crítico: Tamanhos de armazenamento e tamanhos de computação

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Camada de serviço Comercialmente Crítico: Plataforma de computação de geração 4 (parte 1)

|Tamanho de computação|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de hardware|4|4|4|4|4|4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Suporte de columnstore|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento OLTP na memória (GB)|1|2|3|4|5|6|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Tamanho máximo de dados (GB)|1024|1024|1024|1024|1024|1024|
|Tamanho máximo de log (GB)|307|307|307|307|307|307|
|Tamanho de TempDB (GB)|32|64|96|128|160|192|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de destino (64 KB)|5.000|10000|15000|20000|25000|30000|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1000|1.200|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|SIM|sim|sim|sim|sim|SIM|
|Escala de leitura|SIM|sim|sim|sim|sim|SIM|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Camada de serviço Comercialmente Crítico: Plataforma de computação de geração 4 (parte 2)

|Tamanho de computação|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de hardware|4|4|4|4|4|4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|168|
|Suporte de columnstore|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento OLTP na memória (GB)|7|8|9,5|11|20|36|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Tamanho máximo de dados (GB)|1024|1024|1024|1024|1024|1024|
|Tamanho máximo de log (GB)|307|307|307|307|307|307|
|Tamanho de TempDB (GB)|224|256|288|320|384|384|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de destino (64 KB)|35.000|40000|45000|50000|80000|120000|
|Máximo de trabalhos simultâneos (solicitações)|1.400|1600|1800|2000|3200|4800|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|SIM|sim|sim|sim|sim|SIM|
|Escala de leitura|SIM|sim|sim|sim|sim|SIM|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Camada de serviço Comercialmente Crítico: Plataforma de computação de geração 5 (parte 1)

|Tamanho de computação|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |
|Geração de hardware|5|5|5|5|5|5|5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|11|22|33|44|55|66|77|
|Suporte de columnstore|SIM|sim|sim|sim|sim|sim|SIM|
|Armazenamento OLTP na memória (GB)|1. 571|3.142|4,713|6.284|8,655|11,026|13,397|
|Tamanho máximo de dados (GB)|1024|1024|1024|1536|1536|1536|1536|
|Tamanho máximo de log (GB)|307|307|307|461|461|461|461|
|Tamanho de TempDB (GB)|64|128|192|256|320|384|384|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de destino (64 KB)|5.000|10000|15000|20000|25000|30000|35.000|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1000|1.200|1.400|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|30000|
|Número de réplicas|4|4|4|4|4|4|4|
|Multi-AZ|SIM|sim|sim|sim|sim|sim|SIM|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Camada de serviço Comercialmente Crítico: Plataforma de computação de geração 5 (parte 2)

|Tamanho de computação|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|GP_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |
|Geração de hardware|5|5|5|5|5|5|5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|88|99|110|132|176|220|440|
|Suporte de columnstore|SIM|sim|sim|sim|sim|sim|SIM|
|Armazenamento OLTP na memória (GB)|15.768|18,139|20,51|25.252|37.936|52.22|131.64|
|Tamanho máximo de dados (GB)|3072|3072|3072|4096|4096|4096|4096|
|Tamanho máximo de log (GB)|922|922|922|1229|1229|1229|1229|
|Tamanho de TempDB (GB)|384|384|384|384|384|384|384|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de destino (64 KB)|40000|45000|50000|60000|80000|100000|200000|
|Máximo de trabalhos simultâneos (solicitações)|1600|1800|2000|2400|3200|4000|8000|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|30000|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|SIM|sim|sim|sim|sim|SIM|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

## <a name="hyperscale-service-tier-preview"></a>Camada de serviço de hiperescala (versão prévia)

### <a name="generation-4-compute-platform-storage-sizes-and-compute-sizes"></a>Plataforma de computação de geração 4: Tamanhos de armazenamento e tamanhos de computação

|Nível de desempenho|HS_Gen4_1|HS_Gen4_2|HS_Gen4_4|HS_Gen4_8|HS_Gen4_16|HS_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |--: |
|Geração de hardware|4|4|4|4|4|4|
|vCores|1|2|4|8|16|24|
|Memória (GB)|7|14|28|56|112|168|
|Suporte de columnstore|SIM|sim|sim|sim|sim|SIM|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (TB)|100 |100 |100 |100 |100 |100 |
|Tamanho máximo do log (TB)|1 |1 |1 |1 |1 |1 |
|Tamanho de TempDB (GB)|32|64|128|256|384|384|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|IOPS de destino (64 KB)|A ser determinado|A ser determinado|A ser determinado|A ser determinado|A ser determinado|A ser determinado|
|Latência de E/S (aproximada)|A ser determinado|A ser determinado|A ser determinado|A ser determinado|A ser determinado|A ser determinado|
|Máximo de trabalhos simultâneos (solicitações)|200|400|800|1600|3200|4800|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|
|Número de réplicas|2|2|2|2|2|2|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|SIM|sim|sim|sim|sim|SIM|
|Armazenamento de backup incluído|7|7|7|7|7|7|
|||

### <a name="generation-5-compute-platform"></a>Plataforma de computação de Geração 5

|Nível de desempenho|HS_Gen5_2|HS_Gen5_4|HS_Gen5_8|HS_Gen5_16|HS_Gen5_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |--: |
|Geração de hardware|5|5|5|5|5|5|5|5|
|vCores|2|4|8|16|24|32|40|80|
|Memória (GB)|10.2|20,4|40,8|81,6|122,4|163,2|204|408|
|Suporte de columnstore|SIM|sim|sim|sim|sim|sim|sim|SIM|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (TB)|100 |100 |100 |100 |100 |100 |100 |100 |
|Tamanho máximo do log (TB)|1 |1 |1 |1 |1 |1 |1 |1 |
|Tamanho de TempDB (GB)|64|128|256|384|384|384|384|384|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|IOPS de destino (64 KB)|A ser determinado|A ser determinado|A ser determinado|A ser determinado|A ser determinado|A ser determinado|A ser determinado|A ser determinado|
|Latência de E/S (aproximada)|A ser determinado|A ser determinado|A ser determinado|A ser determinado|A ser determinado|A ser determinado|A ser determinado|A ser determinado|
|Máximo de trabalhos simultâneos (solicitações)|200|400|800|1600|2400|3200|4000|8000|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|30000|30000|
|Número de réplicas|2|2|2|2|2|2|2|2|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|SIM|sim|sim|sim|sim|sim|sim|SIM|
|Armazenamento de backup incluído (limite da versão prévia)|7|7|7|7|7|7|7|7|
|||

## <a name="next-steps"></a>Próximas etapas

- Consulte [Perguntas frequentes sobre Banco de Dados SQL](sql-database-faq.md) para obter respostas para as perguntas frequentes.
- Para saber mais sobre limites gerais do Azure, confira [Assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md).
