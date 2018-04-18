---
title: Atualizar para a última geração do SQL Data Warehouse do Azure | Microsoft Docs
description: Etapas para atualizar o SQL Data Warehouse do Azure para a última geração de arquitetura de armazenamento e hardware do Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.services: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/02/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 6ea45398b0bf7fca43c75797313b7e683972b1ab
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>Otimize o desempenho ao fazer upgrade do SQL Data Warehouse

Agora você pode atualizar diretamente para o nível de desempenho Otimizado para Computação no Portal do Azure. Se tiver um data warehouse Otimizado para Elasticidade, é recomendável atualizar para a geração mais recente de hardware do Azure e uma arquitetura de armazenamento avançada. Você poderá aproveitar um desempenho mais rápido, escalabilidade mais alta e armazenamento em colunas ilimitado. 

## <a name="applies-to"></a>Aplica-se a
Essa atualização aplica-se aos data warehouses no nível de desempenho do Otimizado para Elasticidade.

## <a name="sign-in-to-the-azure-portal"></a>Entrar no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Antes de começar

> [!NOTE]
> A partir de 30/3, você deve ter [auditorias no nível de servidor](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing#subheading-8) desativada antes de iniciar a atualização.
> 
>

> [!NOTE]
> Se seu data warehouse Otimizado para Elasticidade existente não estiver em uma região onde Otimizado para Computação está disponível, você poderá [restauração geograficamente para Otimizado para Computação](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region) por meio do PowerShell para uma região com suporte.
> 
>

1. Se o data warehouse Otimizado para Elasticidade a ser atualizado estiver pausado, [retomará o data warehouse](pause-and-resume-compute-portal.md).
2. Esteja preparado para alguns minutos de tempo de inatividade. 



## <a name="start-the-upgrade"></a>Inicie a atualização

1. Vá até seu data warehouse Otimizado para Elasticidade no Portal do Azure e clique em **Atualizar para Otimizado para Computação**:  ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)

2. Por padrão, **selecione o nível de desempenho sugerido** para o data warehouse com base em seu nível de desempenho atual em Otimizado para Elasticidade usando o mapeamento abaixo:
    
| Otimizado para elasticidade | Otimizado para computação |
| :----------------------: | :-------------------: |
|      DW100 – DW1000      |        DW1000c        |
|          DW1200          |        DW1500c        |
|          DW1500          |        DW1500c        |
|          DW2000          |        DW2000c        |
|          DW3000          |        DW3000c        |
|          DW6000          |        DW6000c        |


3. Certifique-se de que sua carga de trabalho teve sua execução concluída e foi confirmada antes de atualizar. Você experimentará um tempo de inatividade por alguns minutos antes de seu data warehouse ficar novamente online como um data warehouse Otimizado para Computação. **Clique em Atualizar**. Atualmente, o preço do nível de desempenho Otimizado para Computação estará pela metade durante o período de versão prévia:
    
    ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. **Monitore a atualização** verificando o status no Portal do Azure:

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)
   
   A primeira etapa do processo de atualização passa pela operação de expansão ("Atualização - Offline") em que todas as sessões serão eliminadas e as conexões serão removidas. 
   
   A segunda etapa do processo de atualização é a migração de dados ("Atualização - Online"). Migração de dados é um processo lento em plano de fundo online, que lentamente move dados de colunas da arquitetura de armazenamento Gen1 antiga para a nova arquitetura de armazenamento Gen2 para aproveitar o cache SSD Gen2 local. Durante esse tempo, o data warehouse ficará online para consultas e carregamento. Todos os dados estarão disponíveis para consulta, independentemente se forem migrados ou não. A migração de dados ocorre em uma taxa que varia dependendo do tamanho dos seus dados, do nível de desempenho e do número de segmentos do columnstore. 

5. **Recomendação opcional:** para agilizar o processo em plano de fundo de migração de dados, é recomendável forçar imediatamente a movimentação de dados executando [Alter Index rebuild](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-tables-index) em todas as tabelas de columnstore em uma classe maior de SLO e recursos. Esta operação está offline, em comparação com o processo lento em plano de fundo; no entanto, a migração de dados será muito mais rápida, em que você pode, em seguida, aproveitar ao máximo a arquitetura de armazenamento Gen2 uma vez concluída com grupos de linhas de alta qualidade. 

Esta consulta a seguir gera os comandos necessários Alter Index Rebuild para acelerar o processo de migração de dados:

```sql
SELECT 'ALTER INDEX [' + idx.NAME + '] ON [' 
       + Schema_name(tbl.schema_id) + '].[' 
       + Object_name(idx.object_id) + '] REBUILD ' + ( CASE 
                                                         WHEN ( 
                                                     (SELECT Count(*) 
                                                      FROM   sys.partitions 
                                                             part2 
                                                      WHERE  part2.index_id 
                                                             = idx.index_id 
                                                             AND 
                                                     idx.object_id = 
                                                     part2.object_id) 
                                                     > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              ELSE '' 
                                                       END ) + '; SELECT ''[' + 
              idx.NAME + '] ON [' + Schema_name(tbl.schema_id) + '].[' + 
              Object_name(idx.object_id) + '] ' + ( 
              CASE 
                WHEN ( (SELECT Count(*) 
                        FROM   sys.partitions 
                               part2 
                        WHERE 
                     part2.index_id = 
                     idx.index_id 
                     AND idx.object_id 
                         = part2.object_id) > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              + ' completed'';' 
              ELSE ' completed'';' 
                                                    END ) 
FROM   sys.indexes idx 
       INNER JOIN sys.tables tbl 
               ON idx.object_id = tbl.object_id 
       LEFT OUTER JOIN sys.partitions part 
                    ON idx.index_id = part.index_id 
                       AND idx.object_id = part.object_id 
WHERE  idx.type_desc = 'CLUSTERED COLUMNSTORE'; 
```



## <a name="next-steps"></a>Próximas etapas
Seu data warehouse atualizado está online. Para aproveitar a arquitetura avançada, consulte [Classes de recursos para gerenciamento de carga de trabalho](resource-classes-for-workload-management.md).
 
