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
ms.date: 09/14/2018
ms.openlocfilehash: 5f0e5de7503d06d1aff319434d763d3b034053b3
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47166343"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-a-single-database"></a>Limites do modelo de compra com base em vCore do Banco de Dados SQL do Azure para um banco de dados individual

Este artigo fornece os limites de recursos detalhados para bancos de dados individuais do Banco de Dados SQL do Azure usando o modelo de compra com base em vCore.

Para limites de modelo de compra baseados em DTU, consulte [Limites de recursos baseados em DTU de Banco de Dados SQL](sql-database-dtu-resource-limits.md).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL](sql-database-file-space-management.md).


## <a name="single-database-storage-sizes-and-compute-sizes"></a>Banco de dados individual: tamanhos de armazenamento e tamanhos de computação

Para bancos de dados individuais, as tabelas a seguir mostram os recursos disponíveis para um único banco de dados individual em cada camada de serviço e tamanho de computação. É possível definir a camada de serviço, o tamanho de computação e a quantidade de armazenamento para um banco de dados individual usando o [portal do Azure](sql-database-single-databases-manage.md#azure-portal-manage-logical-servers-and-databases), o [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-logical-servers-and-databases), o [PowerShell](sql-database-single-databases-manage.md#powershell-manage-logical-servers-and-databases), a [CLI do Azure](sql-database-single-databases-manage.md#azure-cli-manage-logical-servers-and-databases) ou a [API REST](sql-database-single-databases-manage.md#rest-api-manage-logical-servers-and-databases).

### <a name="general-purpose-service-tier"></a>Camada de serviço de Uso Geral

#### <a name="generation-4-compute-platform"></a>Plataforma de computação de Geração 4
|Tamanho de computação|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de hardware|4|4|4|4|4|4|
|vCores|1|2|4|8|16|24|
|Memória (GB)|7|14|28|56|112|168|
|Suporte de columnstore|SIM|sim|sim|sim|sim|SIM|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|Tamanho máximo de dados (GB)|1024|1024|1536|3072|4096|4096|
|Tamanho máximo de log (GB)|307|307|461|922|1229|1229|
|Tamanho de TempDB (GB)|32|64|128|256|384|384|
|IOPS de destino (64 KB)|500|1000|2000|4000|7000|7000|
|Máximo de trabalhos simultâneos (solicitações)|200|400|800|1600|3200|4800|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|000
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|
|||

#### <a name="generation-5-compute-platform"></a>Plataforma de computação de Geração 5
|Tamanho de computação|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40| GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |
|Geração de hardware|5|5|5|5|5|5|5|
|vCores|2|4|8|16|24|32|40|80|
|Memória (GB)|11|22|44|88|132|176|220|440|
|Suporte de columnstore|SIM|sim|sim|sim|sim|sim|sim|SIM|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tipo de armazenamento|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|Armazenamento Premium (remoto)|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|Tamanho máximo de dados (GB)|1024|1024|1536|3072|4096|4096|4096|4096|
|Tamanho máximo de log (GB)|307|307|461|614|1229|1229|1229|1229|
|Tamanho de TempDB (GB)|64|128|256|384|384|384|384|384|
|IOPS de destino (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|Máximo de trabalhos simultâneos (solicitações)|200|400|800|1600|2400|3200|4000|8000|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|30000|30000|
|Número de réplicas|1|1|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|
|||

### <a name="business-critical-service-tier"></a>Camada de serviço comercialmente crítica

#### <a name="generation-4-compute-platform"></a>Plataforma de computação de Geração 4
|Tamanho de computação|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de hardware|4|4|4|4|4|4|
|vCores|1|2|4|8|16|24|
|Memória (GB)|7|14|28|56|112|168|
|Suporte de columnstore|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento OLTP na memória (GB)|1|2|4|8|20|36|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Tamanho máximo de dados (GB)|1024|1024|1024|1024|1024|1024|
|Tamanho máximo de log (GB)|307|307|307|307|307|307|
|Tamanho de TempDB (GB)|32|64|128|256|384|384|
|IOPS de destino (64 KB)|5.000|10000|20000|40000|80000|120000|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|Máximo de trabalhos simultâneos (solicitações)|200|400|800|1600|3200|4800|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|
|Número de réplicas|3|3|3|3|3|3|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|SIM|sim|sim|sim|sim|SIM|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|
|||

#### <a name="generation-5-compute-platform"></a>Plataforma de computação de Geração 5
|Tamanho de computação|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|GP_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |--: |
|Geração de hardware|5|5|5|5|5|5|5|5|
|vCores|2|4|8|16|24|32|40|80|
|Memória (GB)|11|22|44|88|132|176|220|440|
|Suporte de columnstore|SIM|sim|sim|sim|sim|sim|sim|SIM|
|Armazenamento OLTP na memória (GB)|1. 571|3.142|6.284|15.768|25.252|37.936|52.22|131.64|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|Tamanho máximo de dados (GB)|1024|1024|1024|1024|2.048|4096|4096|4096|
|Tamanho máximo de log (GB)|307|307|307|307|614|1229|1229|1229|
|Tamanho de TempDB (GB)|64|128|256|384|384|384|384|384|
|IOPS de destino (64 KB)|5.000|10000|20000|40000|60000|80000|100000|200000
|Máximo de trabalhos simultâneos (solicitações)|200|400|800|1600|2400|3200|4000|8000|
|Máximo permitido de sessões|30000|30000|30000|30000|30000|30000|30000|30000|
|Número de réplicas|3|3|3|3|3|3|3|3|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|
|||

### <a name="hyperscale-service-tier-preview"></a>Camada de serviço de hiperescala (versão prévia)

#### <a name="generation-4-compute-platform"></a>Plataforma de computação de Geração 4
|Nível de desempenho|HS_Gen4_1|HS_Gen4_2|HS_Gen4_4|HS_Gen4_8|HS_Gen4_16|HS_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |--: |
|Geração de hardware|4|4|4|4|4|4|
|vCores|1|2|4|8|16|24|
|Memória (GB)|7|14|28|56|112|168|
|Suporte de columnstore|SIM|sim|sim|sim|sim|SIM|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Tamanho máximo de dados (TB)|100 |100 |100 |100 |100 |100 |
|Tamanho máximo do log (TB)|1 |1 |1 |1 |1 |1 |
|Tamanho de TempDB (GB)|32|64|128|256|384|384|
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
|Memória (GB)|11|22|44|88|132|176|220|440|
|Suporte de columnstore|SIM|sim|sim|sim|sim|sim|sim|SIM|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Tamanho máximo de dados (TB)|100 |100 |100 |100 |100 |100 |100 |100 |
|Tamanho máximo do log (TB)|1 |1 |1 |1 |1 |1 |1 |1 |
|Tamanho de TempDB (GB)|64|128|256|384|384|384|384|384|
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
