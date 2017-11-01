---
title: "Classes de recursos para gerenciamento de carga de trabalho – SQL Data Warehouse do Azure | Microsoft Docs"
description: Diretrizes para usar classes de recursos para gerenciar a simultaneidade e computar recursos para consultas no SQL Data Warehouse do Azure.
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 10/23/2017
ms.author: joeyong;barbkess;kavithaj
ms.openlocfilehash: 122646f73b6e4e7c62eb0e6d4b6672b603d8acb2
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2017
---
# <a name="resource-classes-for-workload-management"></a>Classes de recursos para gerenciamento de carga de trabalho
Diretrizes para usar classes de recursos para gerenciar a quantidade de consultas simultâneas e computar recursos para consultas no SQL Data Warehouse do Azure.
 
## <a name="what-is-workload-management"></a>O que é o gerenciamento de carga de trabalho?
O gerenciamento de carga de trabalho é a capacidade de otimizar o desempenho geral de todas as consultas. Uma carga de trabalho bem ajustada executa consultas e operações de carregamento de maneira eficiente independentemente de serem com uso intensivo de e/s ou de computação intensa. 

O SQL Data Warehouse fornece recursos de gerenciamento de carga de trabalho para ambientes de vários usuários. Um data warehouse não se destina a cargas de trabalho com vários locatários.

## <a name="what-are-resource-classes"></a>O que são classes de recursos?
Classes de recursos são os limites de recurso predeterminados que governam a execução da consulta. O SQL Data Warehouse limita os recursos de computação para cada consulta de acordo com a classe de recurso. 

As classes de recursos ajudam você a gerenciar o desempenho geral da sua carga de trabalho do data warehouse. O uso eficiente de classes de recursos ajuda você a gerenciar a carga de trabalho definindo limites no número de consultas executadas simultaneamente e os recursos de computação atribuídos a cada consulta. 

- Classes de recursos menores usem menos recursos de computação, mas habilitam maior simultaneidade de consulta geral
- Classes de recursos maiores fornecem mais recursos de computação, mas restringem a simultaneidade de consulta

Classes de recursos são projetadas para atividades de gerenciamento e manipulação de dados. Algumas consultas muito complexas também se beneficiarão quando houver associações e classificações grandes para que o sistema execute a consulta na memória, em vez de descarregar no disco.

As operações a seguir são governadas pelas classes de recursos:

* INSERT-SELECT, UPDATE, DELETE
* SELECT (ao consultar tabelas de usuário)
* ALTER INDEX - REBUILD ou REORGANIZE
* ALTER TABLE REBUILD
* CREATE INDEX
* CREATE CLUSTERED COLUMNSTORE INDEX
* CREATE TABLE AS SELECT (CTAS)
* Carregamento de dados
* Operações de movimentação de dados realizadas pelo Serviço de Movimentação de Dados (DMS)

> [!NOTE]  
> Instruções SELECT nas DMVs (exibições de gerenciamento dinâmico) ou em outras exibições do sistema não são regidas por nenhum dos limites de simultaneidade. Você pode monitorar o sistema independentemente do número de consultas em execução nele.
> 
> 

## <a name="static-and-dynamic-resource-classes"></a>Classes de recursos estáticos e dinâmicos

Há dois tipos de classes de recursos: dinâmico e estático.

- **Classes de recursos estáticos** alocam a mesma quantidade de memória, independentemente do nível de serviço atual, que é medido em [unidades do data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md). Essa alocação estática significa em maiores níveis de serviço que você pode executar mais consultas em cada classe de recurso.  As classes de recursos estáticos são nomeadas staticrc10, staticrc20, staticrc30, staticrc40, staticrc50, staticrc60, staticrc70 e staticrc80. Essas classes de recursos são mais adequadas a soluções que aumentam a classe de recurso para obter recursos de computação adicionais.

- **Classes de recursos dinâmicos** alocam uma quantidade variável de memória dependendo do nível de serviço atual. Quando você aumenta para um nível de serviço maior, as consultas automaticamente recebem mais memória. As classes de recursos dinâmicas são chamadas smallrc, mediumrc, largerc e xlargerc. Essas classes de recursos são mais bem adequadas a soluções que aumentam a escala de computação para obter recursos adicionais. 

Os [níveis de desempenho](performance-tiers.md) usam os mesmos nomes de classe de recurso, mas têm diferentes [especificações de memória e simultaneidade](performance-tiers.md). 


## <a name="assigning-resource-classes"></a>Atribuições de classes de recursos

Classes de recursos são implementadas atribuindo usuários a funções de banco de dados. Quando um usuário executa uma consulta, a consulta é executada com a classe de recurso do usuário. Por exemplo, quando um usuário é um membro da função de banco de dados smallrc ou staticrc10, suas consultas são executadas com pequenas quantidades de memória. Quando um usuário de banco de dados é um membro das funções de banco de dados xlargerc ou staticrc80, suas consultas são executadas com grandes quantidades de memória. 

Para aumentar a classe de recurso de um usuário, use o procedimento armazenado [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). 

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

Para diminuir a classe de recurso, use [sp_droprolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql).  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

A classe de recurso do administrador de serviços é fixa e não pode ser alterada.  O administrador de serviços é o usuário criado durante o processo de provisionamento.

### <a name="default-resource-class"></a>Classe de recurso padrão
Por padrão, cada usuário é um membro da pequena classe de recursos, **smallrc**. 

### <a name="resource-class-precedence"></a>Precedência de classe de recurso
Os usuários podem ser membros de várias classes de recursos. Quando um usuário pertence a mais de uma classe de recurso:

- Classes de recursos dinâmicos têm precedência sobre classes de recursos estáticos. Por exemplo, se um usuário for membro de mediumrc (dinâmico) e staticrc80 (estático), as consultas serão executadas com mediumrc.
- Classes de recursos maiores têm precedência sobre classes de recursos menores. Por exemplo, se um usuário for membro de mediumrc e largerc, as consultas serão executadas com largerc. Da mesma forma, se um usuário for membro de staticrc20 e statirc80, as consultas serão executadas com alocações de recursos staticrc80.

### <a name="queries-exempt-from-resource-classes"></a>Consultas isentas de classes de recursos
Algumas consultas sempre são executadas na classe de recurso smallrc, mesmo se o usuário for um membro de uma classe de recursos maior. Essas consultas isentas não são consideradas no limite de simultaneidade. Por exemplo, se o limite de simultaneidade for 16, muitos usuários poderão estar selecionando de exibições do sistema sem afetar os slots de simultaneidade disponíveis.

As instruções a seguir estão isentas das classes de recursos e sempre são executadas em smallrc:

* CREATE ou DROP TABLE
* ALTER TABLE ... SWITCH, SPLIT ou MERGE PARTITION
* ALTER INDEX DISABLE
* DROP INDEX
* CREATE, UPDATE ou DROP STATISTICS
* TRUNCATE TABLE
* ALTER AUTHORIZATION
* CREATE LOGIN
* CREATE, ALTER ou DROP USER
* CREATE, ALTER ou DROP PROCEDURE
* CREATE ou DROP VIEW
* INSERT VALUES
* SELECT de exibições do sistema e DMVs
* EXPLAIN
* DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="recommendations"></a>Recomendações
Recomendamos criar um usuário que seja dedicado à execução de um tipo específico de consultas ou operações de carregamento. Depois, ofereça a esse usuário uma classe de recursos permanente em vez de alterar a classe de recurso com frequência. Considerando que as classes de recursos estáticos têm maior controle geral sobre a carga de trabalho, também sugerimos usá-las primeiro antes de considerar classes de recursos dinâmicos.

### <a name="resource-classes-for-load-users"></a>Classes de recursos para usuários de carga
O `CREATE TABLE` usa índices columnstore clusterizados por padrão. A compactação de dados em um índice columnstore é uma operação de uso intensivo de memória e a pressão na memória pode reduzir a qualidade do índice. Portanto, é mais provável que você exija uma classe de recurso maior ao carregar os dados. Para garantir que as cargas tenham memória suficiente, você pode criar um usuário que seja designado para que execute cargas e atribuir esse usuário a uma classe de recurso maior.

A memória necessária para processar cargas com eficiência depende da natureza da tabela carregada e do tamanho dos dados. Para obter mais informações sobre requisitos de memória, consulte [Maximizando a qualidade do grupo de linhas](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

Depois de determinar o requisito de memória, escolha se deseja atribuir o usuário de carga a uma classe de recurso estático ou dinâmico.

- Use uma classe de recurso estático quando os requisitos de memória de tabela estiverem em um intervalo específico. As cargas são executadas com memória apropriada. Quando você dimensiona o data warehouse, as cargas não precisam de mais memória. Ao usar uma classe de recurso estático, as alocações de memória permanecem constantes. Essa consistência economiza a memória e permite que mais consultas sejam executadas simultaneamente. É recomendável que novas soluções usem as classes de recursos estáticos primeiro, pois eles fornecem maior controle.
- Use uma classe de recursos dinâmicos quando os requisitos de memória de tabela variarem muito. As cargas podem exigir mais memória do que a DWU atual ou que o nível de cDWU fornece. Portanto, dimensionar o data warehouse adiciona mais memória para operações de carregamento, o que permite que cargas tenham um desempenho mais rápido.

### <a name="resource-classes-for-queries"></a>Classes de recursos para consultas

Algumas consultas são de computação intensiva e algumas não.  

- Escolha uma classe de recursos dinâmicos quando consultas forem complexas, mas não precisarem de alta simultaneidade.  Por exemplo, a geração de relatórios diários ou semanais é uma necessidade ocasional para recursos. Se os relatórios estiverem processando grandes quantidades de dados, dimensionar o data warehouse fornece mais memória à classe de recurso existente do usuário.
- Escolha uma classe de recurso estático quando as expectativas de recursos variarem ao longo do dia. Por exemplo, uma classe de recurso estático funciona bem quando o data warehouse é consultado por várias pessoas. Ao dimensionar o data warehouse, a quantidade de memória alocada para o usuário não é alterada. Consequentemente, mais consultas podem ser executadas em paralelo no sistema.

Selecionar a concessão de memória apropriada depende de muitos fatores, como a quantidade de dados consultada, a natureza dos esquemas de tabela e vários predicados de união, seleção e agrupamento. Em geral, alocar mais memória permitirá que consultas sejam concluídas mais rapidamente, mas reduzirá a simultaneidade geral. Se a simultaneidade não for um problema, a alocação excessiva de memória não prejudicará a taxa de transferência. 

Para ajustar o desempenho, use classes de recursos diferentes. A próxima seção fornece um procedimento armazenado que ajuda você a descobrir a melhor classe de recurso.

## <a name="example-code-for-finding-the-best-resource-class"></a>Exemplo de código para encontrar a melhor classe de recurso
 
Você pode usar o procedimento armazenado a seguir para descobrir a concessão de memória e a simultaneidade por classe de recursos em um determinado SLO e a melhor classe de recursos mais próxima para operações de CCI de uso intenso da memória em uma tabela CCI não particionada em uma determinada classe de recursos:

Aqui está a finalidade deste procedimento armazenado:  
1. Visualizar a simultaneidade e a concessão de memória por classe de recursos em um determinado SLO. O usuário precisa fornecer NULL para o esquema e tablename, conforme mostrado neste exemplo.  
2. Visualizar a melhor classe de recursos mais próxima para operações de CCI de uso intenso de memória (carregar, copiar tabela, recompilar índice etc.) na tabela CCI não particionada em uma determinada classe de recursos. O procedimento armazenado usa o esquema da tabela para descobrir a concessão de memória necessária.

### <a name="dependencies--restrictions"></a>Dependências e restrições:
- Esse procedimento armazenado não foi projetado para calcular os requisitos de memória para uma tabela CCI particionada.    
- Esse procedimento armazenado não considera os requisitos de memória para a parte SELECT de CTAS/INSERT-SELECT e pressupõe que sejam um SELECT.
- Esse procedimento armazenado usa uma tabela temporária que está disponível na sessão em que esse procedimento armazenado foi criado.    
- Esse procedimento armazenado depende das ofertas atuais (por exemplo, a configuração de hardware, a configuração DMS) e se qualquer uma delas for alterada, esse procedimento armazenado não funcionará corretamente.  
- Esse procedimento armazenado depende do limite de simultaneidade oferecido existente e, se isso mudar, o procedimento armazenado não funcionará corretamente.  
- Esse procedimento armazenado depende das ofertas de classe de recursos existentes e, se isso mudar, o procedimento armazenado não funcionará corretamente.  

>  [!NOTE]  
>  Se você não estiver obtendo a saída após executar o procedimento armazenado com parâmetros fornecidos, então poderá ser dois casos. <br />1. Um parâmetro de DW contém um valor inválido de SLO <br />2. Ou, não há nenhuma classe de recurso correspondente para a operação CCI na tabela. <br />Por exemplo, no DW100, a concessão de memória mais alta disponível é de 400 MB e se o esquema de tabela for grande o suficiente para atravessar o requisito de 400 MB.
      
### <a name="usage-example"></a>Exemplo de uso:
Sintaxe:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`  
1. @DWU: Forneça um parâmetro NULL para extrair a DWU atual do BD do DW ou forneça uma DWU compatível na forma de 'DW100'
2. @SCHEMA_NAME: Forneça um nome de esquema da tabela
3. @TABLE_NAME: Forneça um nome de tabela dos juros

Exemplos executando esse procedimento armazenado:  
```sql  
EXEC dbo.prc_workload_management_by_DWU 'DW2000', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

A instrução a seguir cria Table1 que é usada nos exemplos anteriores.
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition"></a>Definição do procedimento armazenado

```sql  
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(@DWU VARCHAR(7),
 @SCHEMA_NAME VARCHAR(128),
 @TABLE_NAME VARCHAR(128)
)
AS
IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.
SET @DWU = (
  SELECT 'DW'+CAST(COUNT(*)*100 AS VARCHAR(10))
  FROM sys.dm_pdw_nodes
  WHERE type = 'COMPUTE')
END

DECLARE @DWU_NUM INT
SET @DWU_NUM = CAST (SUBSTRING(@DWU, 3, LEN(@DWU)-2) AS INT)

-- Raise error if either schema name or table name is supplied but not both them supplied
--IF ((@SCHEMA_NAME IS NOT NULL AND @TABLE_NAME IS NULL) OR (@TABLE_NAME IS NULL AND @SCHEMA_NAME IS NOT NULL))
--     RAISEERROR('User need to supply either both Schema Name and Table Name or none of them')
       
-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END

-- Creating ref. temp table (CTAS) to hold mapping info.
-- CREATE TABLE #ref
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS 
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
     SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
   SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map
AS
(
  SELECT 'SloDWGroupC00' AS wg_name,1 AS slots_used,100 AS tgt_mem_grant_MB
  UNION ALL
    SELECT 'SloDWGroupC01',2,200
  UNION ALL
    SELECT 'SloDWGroupC02',4,400
  UNION ALL
    SELECT 'SloDWGroupC03',8,800
  UNION ALL
    SELECT 'SloDWGroupC04',16,1600
  UNION ALL
    SELECT 'SloDWGroupC05',32,3200
  UNION ALL
    SELECT 'SloDWGroupC06',64,6400
  UNION ALL
    SELECT 'SloDWGroupC07',128,12800
)
-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.tgt_mem_grant_MB AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.tgt_mem_grant_MB AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.tgt_mem_grant_MB AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.tgt_mem_grant_MB AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used
-- WHERE   a1.DWU = @DWU
  WHERE   a1.DWU = UPPER(@DWU)
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;
-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239) 
                                AND  co.max_length <= 32 
                                THEN COUNT(co.column_id) 
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239) 
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id) 
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as 
(
SELECT  CASE 
                     WHEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) > 0 THEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) 
                     ELSE 1 
              END AS multipliplication_factor
) 
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB       
       FROM    size, load_multiplier, #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```




## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre como gerenciar usuários de banco de dados e segurança, confira [Proteger um banco de dados no SQL Data Warehouse][Secure a database in SQL Data Warehouse]. Para obter mais informações sobre como classes de recursos maiores podem melhorar a qualidade do índice columnstore clusterizado, consulte [Otimizações de memória para compressão de columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Rebuilding indexes to improve segment quality]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
