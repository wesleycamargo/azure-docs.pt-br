---
title: Instruções de T-SQL — SQL Data Warehouse do Azure | Microsoft Docs
description: Links para a documentação para instruções T-SQL compatíveis no SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 06/13/2018
ms.author: twounder
ms.reviewer: igorstan
ms.openlocfilehash: 8922d4399f52937710f111c3c8ad4c9af91b6bb7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57890093"
---
# <a name="t-sql-statements-supported-in-azure-sql-data-warehouse"></a>Elementos de T-SQL compatíveis com o SQL Data Warehouse do Azure
Links para a documentação para instruções T-SQL compatíveis no SQL Data Warehouse do Azure.

## <a name="data-definition-language-ddl-statements"></a>Instruções de DDL (Linguagem de Definição de Dados)
* [ALTER DATABASE](https://msdn.microsoft.com/library/mt204042.aspx)
* [ALTER INDEX](https://msdn.microsoft.com/library/ms188388.aspx)
* [ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)
* [ALTER SCHEMA](https://msdn.microsoft.com/library/ms173423.aspx)
* [ALTER TABLE](https://msdn.microsoft.com/library/ms190273.aspx)
* [CREATE COLUMNSTORE INDEX](https://msdn.microsoft.com/library/gg492153.aspx)
* [CREATE DATABASE](https://msdn.microsoft.com/library/mt204021.aspx)
* [CRIAR UMA CREDENCIAL NO ESCOPO DO BANCO DE DADOS](https://msdn.microsoft.com/library/mt270260.aspx)
* [CREATE EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx)
* [CREATE EXTERNAL FILE FORMAT](https://msdn.microsoft.com/library/dn935026.aspx)
* [CREATE EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx)
* [CREATE FUNCTION](https://msdn.microsoft.com/library/mt203952.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [CREATE PROCEDURE](https://msdn.microsoft.com/library/ms187926.aspx)
* [CREATE SCHEMA](https://msdn.microsoft.com/library/ms189462.aspx)
* [CREATE STATISTICS](https://msdn.microsoft.com/library/ms188038.aspx)
* [CREATE TABLE](https://msdn.microsoft.com/library/mt203953.aspx)
* [CREATE TABLE AS SELECT](https://msdn.microsoft.com/library/mt204041.aspx)
* [CREATE VIEW](https://msdn.microsoft.com/library/ms187956.aspx)
* [CRIAR um CLASSIFICADOR de carga de trabalho](/sql/t-sql/statements/create-workload-classifier-transact-sql) (visualização)
* [DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/mt146367.aspx)
* [DROP EXTERNAL FILE FORMAT](https://msdn.microsoft.com/library/mt146379.aspx)
* [DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/mt130698.aspx)
* [DROP INDEX](https://msdn.microsoft.com/library/ms176118.aspx)
* [DROP PROCEDURE](https://msdn.microsoft.com/library/ms174969.aspx)
* [DROP STATISTICS](https://msdn.microsoft.com/library/ms175075.aspx)
* [DROP TABLE](https://msdn.microsoft.com/library/ms173790.aspx)
* [DROP SCHEMA](https://msdn.microsoft.com/library/ms186751.aspx)
* [DROP VIEW](https://msdn.microsoft.com/library/ms173492.aspx)
* [CLASSIFICADOR de carga de trabalho de SOLTAR](/sql/t-sql/statements/drop-workload-classifier-transact-sql) (visualização)
* [RENAME](https://msdn.microsoft.com/library/mt631611.aspx)
* [TRUNCATE TABLE](https://msdn.microsoft.com/library/ms177570.aspx)
* [UPDATE STATISTICS](https://msdn.microsoft.com/library/ms187348.aspx)

## <a name="data-manipulation-language-dml-statements"></a>Instruções de DML (Linguagem de Manipulação de Dados)
* [DELETE](https://msdn.microsoft.com/library/ms189835.aspx)
* [INSERT](https://msdn.microsoft.com/library/ms174335.aspx)
* [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx)

## <a name="database-console-commands"></a>Comandos do console do banco de dados
* [DBCC DROPCLEANBUFFERS](https://msdn.microsoft.com/library/ms187762.aspx)
* [DBCC FREEPROCCACHE](https://msdn.microsoft.com/library/mt204018.aspx)
* [DBCC SHRINKLOG](https://msdn.microsoft.com/library/mt204020.aspx)
* [DBCC PDW_SHOWEXECUTIONPLAN](https://msdn.microsoft.com/library/mt204017.aspx)
* [DBCC PDW_SHOWPARTITIONSTATS](https://msdn.microsoft.com/library/mt204013.aspx)
* [DBCC PDW_SHOWSPACEUSED](https://msdn.microsoft.com/library/mt204028.aspx)
* [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/mt204043.aspx)

## <a name="query-statements"></a>Instruções de consulta
* [SELECT](https://msdn.microsoft.com/library/ms189499.aspx)
* [WITH common_table_expression](https://msdn.microsoft.com/library/ms175972.aspx)
* [EXCEPT e INTERSECT](https://msdn.microsoft.com/library/ms188055.aspx)
* [EXPLAIN](https://msdn.microsoft.com/library/mt631615.aspx)
* [FROM](https://msdn.microsoft.com/library/ms177634.aspx)
* [Como usar PIVOT e UNPIVOT](https://msdn.microsoft.com/library/ms177410.aspx)
* [GROUP BY](https://msdn.microsoft.com/library/ms177673.aspx)
* [HAVING](https://msdn.microsoft.com/library/ms180199.aspx)
* [ORDER BY](https://msdn.microsoft.com/library/ms188385.aspx)
* [OPTION](https://msdn.microsoft.com/library/ms190322.aspx)
* [UNION](https://msdn.microsoft.com/library/ms180026.aspx)
* [WHERE](https://msdn.microsoft.com/library/ms188047.aspx)
* [TOP](https://msdn.microsoft.com/library/ms189463.aspx)
* [Atribuição de alias](https://msdn.microsoft.com/library/mt631614.aspx)
* [Condição de pesquisa](https://msdn.microsoft.com/library/ms173545.aspx)
* [Subconsultas](https://msdn.microsoft.com/library/mt631613.aspx)

## <a name="security-statements"></a>Instruções de segurança
* Permissões: [GRANT](https://msdn.microsoft.com/library/ms187965.aspx), [DENY](https://msdn.microsoft.com/library/ms188338.aspx), [REVOKE](https://msdn.microsoft.com/library/ms187728.aspx)
* [ALTER AUTHORIZATION](https://msdn.microsoft.com/library/ms187359.aspx)
* [ALTER CERTIFICATE](https://msdn.microsoft.com/library/ms189511.aspx)
* [ALTER DATABASE ENCRYPTION KEY](https://msdn.microsoft.com/library/bb630389.aspx)
* [ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)
* [ALTER MASTER KEY](https://msdn.microsoft.com/library/ms186937.aspx)
* [ALTER ROLE](https://msdn.microsoft.com/library/ms189775.aspx)
* [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx)
* [BACKUP CERTIFICATE](https://msdn.microsoft.com/library/ms178578.aspx)
* [CLOSE MASTER KEY](https://msdn.microsoft.com/library/ms188387.aspx)
* [CREATE CERTIFICATE](https://msdn.microsoft.com/library/ms187798.aspx)
* [CREATE DATABASE ENCRYPTION KEY](https://msdn.microsoft.com/library/bb677241.aspx)
* [CREATE LOGIN](https://msdn.microsoft.com/library/ms189751.aspx)
* [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx)
* [CREATE ROLE](https://msdn.microsoft.com/library/ms187936.aspx)
* [CREATE USER](https://msdn.microsoft.com/library/ms173463.aspx)
* [DROP CERTIFICATE](https://msdn.microsoft.com/library/ms179906.aspx)
* [DROP DATABASE ENCRYPTION KEY](https://msdn.microsoft.com/library/bb630256.aspx)
* [DROP LOGIN](https://msdn.microsoft.com/library/ms188012.aspx)
* [DROP MASTER KEY](https://msdn.microsoft.com/library/ms180071.aspx)
* [DROP ROLE](https://msdn.microsoft.com/library/ms174988.aspx)
* [DROP USER](https://msdn.microsoft.com/library/ms189438.aspx)
* [OPEN MASTER KEY](https://msdn.microsoft.com/library/ms174433.aspx)

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações de referência, consulte [Elementos da linguagem T-SQL no SQL Data Warehouse do Azure](sql-data-warehouse-reference-tsql-language-elements.md) e [Exibições do sistema no SQL Data Warehouse do Azure](sql-data-warehouse-reference-tsql-system-views.md).
