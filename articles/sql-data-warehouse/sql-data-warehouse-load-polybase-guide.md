---
title: Guia para usar PolyBase no SQL Data Warehouse | Microsoft Docs
description: "Diretrizes e recomendações para usar o PolyBase em cenários do SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 4757fce1-96b3-48ea-8a51-be1385705f9f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 6/5/2016
ms.custom: loading
ms.author: cakarst;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: 6938b92d8e5b46d908dc5b2155bdfdc89bb1dc8c
ms.contentlocale: pt-br
ms.lasthandoff: 06/07/2017



---
# <a name="guide-for-using-polybase-in-sql-data-warehouse"></a>Guia para usar o PolyBase no SQL Data Warehouse
Este guia fornece informações práticas para usar o PolyBase no SQL Data Warehouse.

Para começar, consulte o tutorial [Carregar dados com o PolyBase][Load data with PolyBase].

## <a name="rotating-storage-keys"></a>Rotação de chaves de armazenamento
Às vezes você desejará alterar a chave de acesso para o armazenamento de blob por motivos de segurança.

A maneira mais elegante de realizar essa tarefa é seguir um processo conhecido como "rotação das chaves". Você talvez tenha observado que há duas chaves de armazenamento para a conta de armazenamento de blob. Isso é para que você possa fazer a transição

A rotação das suas chaves da conta de Armazenamento do Azure é um processo simples de três etapas

1. Criar uma segunda credencial no escopo do banco de dados com base na chave de acesso de armazenamento secundário
2. Criar a segunda fonte de dados externa com base nessa nova credencial
3. Remover e criar as tabelas externas apontando para a nova fonte de dados externa

Depois de migrar todas as tabelas externas para a nova fonte de dados externa, você pode executar as tarefas de limpeza:

1. Remover a primeira fonte de dados externa
2. Remover a primeira credencial no escopo do banco de dados na chave de acesso de armazenamento primário
3. Fazer logon no Azure e regenerar a chave de acesso primária pronta para a próxima vez

## <a name="query-azure-blob-storage-data"></a>Consultar dados de armazenamento de blob do Azure
As consultas em tabelas externas simplesmente usam o nome da tabela como se ele fosse uma tabela relacional.

```sql
-- Query Azure storage resident data via external table.
SELECT * FROM [ext].[CarSensor_Data]
;
```

> [!NOTE]
> Uma consulta em uma tabela externa pode falhar com o erro *“Consulta anulada – o limite de rejeição máximo foi atingido durante a leitura de uma fonte externa”*. Isso indica que os dados externos contêm registros *sujos* . Um registro de dados é considerado “sujo” se os tipos de dados/número de colunas reais não correspondem às definições de coluna da tabela externa ou se os dados não são compatíveis com o formato de arquivo externo especificado. Para corrigir esse problema, verifique se a tabela externa e as definições de formato de arquivo externo estão corretas e se os dados externos são compatíveis com essas definições. Caso um subconjunto de registros de dados externos esteja sujo, é possível rejeitar esses registros para suas consultas usando as opções de rejeição em CREATE EXTERNAL TABLE DDL.
> 
> 

## <a name="load-data-from-azure-blob-storage"></a>Carregar dados do armazenamento de blob do Azure
Este exemplo carrega dados do armazenamento de blob do Azure no banco de dados do SQL Data Warehouse.

Armazenar dados diretamente elimina o tempo de transferência de dados para consultas. Armazenar dados com um índice columnstore melhora o desempenho de consultas de análise em até 10 vezes.

Este exemplo usa a instrução CREATE TABLE AS SELECT para carregar dados. A nova tabela herda as colunas nomeadas na consulta. Ela herda os tipos de dados dessas colunas da definição de tabela externa.

CREATE TABLE AS SELECT é uma instrução Transact-SQL de alto desempenho que carrega os dados em paralelo em todos os nós de computação do seu SQL Data Warehouse.  Ela foi originalmente desenvolvida para o mecanismo MPP (processamento massivamente paralelo) no Analytics Platform System e agora está no SQL Data Warehouse.

```sql
-- Load data from Azure blob storage to SQL Data Warehouse

CREATE TABLE [dbo].[Customer_Speed]
WITH
(   
    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS
SELECT *
FROM   [ext].[CarSensor_Data]
;
```

Consulte [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)].

## <a name="create-statistics-on-newly-loaded-data"></a>Criar estatísticas sobre os dados recém-carregados
O SQL Data Warehouse do Azure ainda não dá suporte a estatísticas de criação ou atualização automática.  Para obter o melhor desempenho de suas consultas, é importante que as estatísticas sejam criadas em todas as colunas de todas as tabelas após o primeiro carregamento ou após uma alteração significativa nos dados.  Para obter uma explicação detalhada das estatísticas, confira o tópico [Estatísticas][Statistics] no grupo de tópicos Desenvolver.  Veja abaixo um exemplo de como criar estatísticas na tabela carregada neste exemplo.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="export-data-to-azure-blob-storage"></a>Exportar dados no armazenamento de blob do Azure
Esta seção mostra como exportar dados do SQL Data Warehouse para o armazenamento de blob do Azure. Este exemplo usa CREATE EXTERNAL TABLE AS SELECT, que é uma instrução Transact-SQL de alto desempenho, para exportar os dados em paralelo de todos os nós de computação.

O exemplo a seguir cria uma tabela Weblogs2014 externa usando definições de coluna e dados da tabela dbo.Weblogs. A definição da tabela externa é armazenada no SQL Data Warehouse e os resultados da instrução SELECT são exportados para o diretório "/archive/log2014 /" no contêiner de blob especificado pela fonte de dados. Os dados são exportados no formato de arquivo de texto especificado.

```sql
CREATE EXTERNAL TABLE Weblogs2014 WITH
(
    LOCATION='/archive/log2014/',
    DATA_SOURCE=azure_storage,
    FILE_FORMAT=text_file_format
)
AS
SELECT
    Uri,
    DateRequested
FROM
    dbo.Weblogs
WHERE
    1=1
    AND DateRequested > '12/31/2013'
    AND DateRequested < '01/01/2015';
```
## <a name="isolate-loading-users"></a>Isolar Usuários em carregamento
Geralmente, há a necessidade de ter vários usuários que podem carregar dados em um SQL DW. Como [CREATE TABLE AS SELECT (Transact-SQL)] [ CREATE TABLE AS SELECT (Transact-SQL)] requer permissões CONTROL do banco de dados, você acabará tendo vários usuários com acesso de controle sobre todos os esquemas. Para limitar isso, você pode usar a instrução DENY CONTROL.

Exemplo: considere os esquemas de banco de dados schema_A para o departamento A e schema_B para o departamento B. Os usuários de banco de dados user_A e user_B serão usuários de carregamento de PolyBase nos departamentos A e B, respectivamente. Ambos receberam permissões de banco de dados CONTROL.
Os criadores dos esquemas A e B agora bloquearam seus esquemas usando DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```   
 Com isso, user_A e user_B devem agora ser bloqueados do esquema do outro departamento.
 


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como mover dados para o SQL Data Warehouse, consulte o [Visão geral de migração de dados][data migration overview].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Load data with PolyBase]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[data migration overview]: ./sql-data-warehouse-overview-migrate.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/ms174335.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189522.aspx
[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189450.aspx

<!-- External Links -->

