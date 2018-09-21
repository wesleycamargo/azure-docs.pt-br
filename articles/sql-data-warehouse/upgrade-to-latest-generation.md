---
title: Atualizar para a última geração do SQL Data Warehouse do Azure | Microsoft Docs
description: Atualize o SQL Data Warehouse do Azure para a última geração de arquitetura de armazenamento e hardware do Azure.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/22/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 9b2c5fa38d0ad21a374d3e5acee8534aeb9c0384
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/14/2018
ms.locfileid: "45580541"
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>Otimize o desempenho ao fazer upgrade do SQL Data Warehouse
Atualize o SQL Data Warehouse do Azure para a última geração de arquitetura de armazenamento e hardware do Azure.

## <a name="why-upgrade"></a>Por que atualizar?
Agora, você pode atualizar diretamente para o nível de Computação Otimizada Gen2 do SQL Data Warehouse no portal do Azure. Se você tem um data warehouse na camada de Computação Otimizada Gen1, a atualização é recomendada. Com o upgrade, você poderá usar a última geração de hardware do Azure e a arquitetura de armazenamento aprimorada. Você poderá aproveitar um desempenho mais rápido, escalabilidade mais alta e armazenamento em colunas ilimitado. 

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

## <a name="applies-to"></a>Aplica-se a
Esta atualização se aplica a data warehouses na camada de Computação Otimizada Gen1.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Antes de começar
> [!NOTE]
> Se seu data warehouse na camada de Computação Otimizada Gen1 não estiver em uma região em que a camada de Computação Otimizada Gen2 está disponível, você poderá fazer a [restauração geográfica](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region) por meio do PowerShell para uma região com suporte.
> 
>

1. Se o data warehouse na camada de Computação Otimizada Gen1 a ser atualizado estiver em pausa, [retome o data warehouse](pause-and-resume-compute-portal.md).
2. Esteja preparado para alguns minutos de tempo de inatividade. 



## <a name="start-the-upgrade"></a>Inicie a atualização

1. Vá para o seu data warehouse na camada de Computação Otimizada Gen1 no portal do Azure e clique em **Atualizar para Gen2**: ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)

2. Por padrão, **selecione o nível de desempenho sugerido** para o data warehouse com base em seu nível de desempenho atual na camada de Computação Otimizada Gen1 usando o mapeamento abaixo:
    
   | Camada de Computação Otimizada Gen1 | Camada de Computação Otimizada Gen2 |
   | :----------------------: | :-------------------: |
   |      DW100 – DW1000      |        DW1000c        |
   |          DW1200          |        DW1500c        |
   |          DW1500          |        DW1500c        |
   |          DW2000          |        DW2000c        |
   |          DW3000          |        DW3000c        |
   |          DW6000          |        DW6000c        |

3. Certifique-se de que sua carga de trabalho teve sua execução concluída e foi confirmada antes de atualizar. Você experimentará um tempo de inatividade por alguns minutos antes de seu data warehouse ficar novamente online como um data warehouse na camada de Computação Otimizada Gen2. **Clique em Atualizar**. Atualmente, o nível de desempenho da camada de Computação Otimizada Gen2 está pela metade do preço durante o período de versão prévia:
    
   ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. **Monitore a atualização** verificando o status no Portal do Azure:

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)
   
   A primeira etapa do processo de atualização passa pela operação de expansão ("Atualização - Offline") em que todas as sessões serão eliminadas e as conexões serão removidas. 
   
   A segunda etapa do processo de atualização é a migração de dados ("Atualização - Online"). A migração de dados é um processo lento em segundo plano online, que move lentamente os dados de colunas da arquitetura de armazenamento antiga para a nova arquitetura de armazenamento para aproveitar o cache SSD local. Durante esse tempo, o data warehouse ficará online para consultas e carregamento. Todos os dados estarão disponíveis para consulta, independentemente se forem migrados ou não. A migração de dados ocorre em uma taxa que varia dependendo do tamanho dos seus dados, do nível de desempenho e do número de segmentos do columnstore. 

5. **Recomendação opcional:** para agilizar o processo de migração de dados em segundo plano, você pode forçar imediatamente a movimentação de dados executando [Alter Index rebuild](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-index) em todas as tabelas de columnstore primárias que você estaria consultando em uma classe maior de SLO e recursos. Esta operação é **offline**, em comparação com o processo lento em segundo plano, que pode levar horas para ser concluído dependendo do número e do tamanho de suas tabelas. No entanto, a migração de dados será muito mais rápida, e você poderá aproveitar ao máximo a arquitetura de armazenamento aprimorada depois de concluída com grupos de linhas de alta qualidade. 

A consulta a seguir gera os comandos necessários Alter Index Rebuild para acelerar o processo de migração de dados:

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
 
