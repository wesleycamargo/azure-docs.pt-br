---
title: Notas de versão do SQL Data Warehouse do Azure abril de 2018 | Microsoft Docs
description: Notas de versão do SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 05/28/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: ae3d4c3e732024baae29f75fda6f6e821af701a2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38630336"
---
# <a name="whats-new-in-azure-sql-data-warehouse-april-2018"></a>O que há de novo no SQL Data Warehouse do Azure? Abril de 2018
O SQL Data Warehouse do Azure recebe melhorias continuamente. Este artigo descreve os novos recursos e alterações que foram introduzidas em abril de 2018.

## <a name="ability-to-truncate-a-partition-before-a-switch"></a>Capacidade de truncar uma partição antes de um comutador
Os clientes geralmente usam a comutação de partição como um padrão para carregar dados de uma tabela para outra alterando os metadados da tabela por meio da sintaxe `ALTER TABLE SourceTable SWITCH PARTITION X TO TargetTable PARTITION X`. O SQL Data Warehouse não é compatível com a comutação quando a partição de destino contém dados. Se a partição de destino já contiver dados, o cliente precisará truncar a partição de destino e, em seguida, executar a comutação.

O SQL Data Warehouse agora é compatível com esta operação em uma única instrução T-SQL.

```sql
ALTER TABLE SourceTable 
    SWITCH PARTITION X TO TargetTable PARTITION X
    WITH (TRUNCATE_TARGET_PARTITION = ON)
```
Para saber mais, confira o artigo [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## <a name="improved-query-compilation-performance"></a>Desempenho de compilação de consulta aprimorado
O SQL Data Warehouse introduziu um conjunto de alterações para melhorar a etapa de compilação de consulta das consultas distribuídas. Essas alterações melhoram os tempos de compilação de consulta em até **dez vezes**, reduzindo os tempos de execução da consulta em geral. Essas alterações são mais evidentes nos data warehouses com um grande número de objetos (tabelas, funções, exibições, procedimentos).

## <a name="dbcc-commands-do-not-consume-concurrency-slots-behavior-change"></a>Comandos DBCC não consomem slots de simultaneidade (alteração de comportamento)
O SQL Data Warehouse é compatível com um subconjunto dos [comandos DBCC](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) do T-SQL como [DBCC DROPCLEANBUFFERS](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropcleanbuffers-transact-sql). Anteriormente, esses comandos consumiriam um [slot de simultaneidade](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#concurrency-slots), reduzindo o número de carregamentos/consultas do usuário que poderiam ser executadas. Os comandos `DBCC` agora são executados em uma fila local que não consome um slot de recurso, melhorando o desempenho geral de execução de consulta.

## <a name="updated-error-message-for-excessive-literals-behavior-change"></a>Mensagem de erro para literais excessivos atualizada (alteração de comportamento)
Anteriormente, o SQL Data Warehouse incluiria uma contagem *aproximada* quando uma consulta continha muitos literais.
```
Msg 100086
Cannot have more than 20,000 literals in the query. The query contains [n] literals.
```

A mensagem de erro foi atualizada para indicar apenas que você atingiu o limite de literais.
```
Msg 100086
The number of literals in the query is beyond the limit. Please rewrite your query.
```

Para obter mais informações, confira a seção [Consultas](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits#queries) do artigo [Limites de capacidade](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-service-capacity-limits) para encontrar mais detalhes sobre os limites máximos.

## <a name="removed-the-syspdwdatabasemappings-view-behavior-change"></a>Exibição SYS.PDW_DATABASE_MAPPINGS removida (alteração de comportamento)
Essa exibição `sys.pdw_database_mappings` é não é usada no SQL Data Warehouse. Anteriormente, um SELECT dessa exibição não retornaria resultados. A exibição foi removida. 