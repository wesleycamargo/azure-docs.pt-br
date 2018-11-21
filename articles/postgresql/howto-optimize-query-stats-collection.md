---
title: Otimizar a coleção de estatísticas de consulta na coleção de estatísticas de consulta do servidor do Banco de Dados do Azure para PostgreSQL
description: Este artigo descreve como é possível otimizar a coleção de estatísticas de consulta em um servidor do Banco de Dados do Azure para PostgreSQL.
author: dianaputnam
ms.author: dianas
editor: jasonwhowell
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 8b8d1a5a16a948953838f51edca091121dc14df1
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628268"
---
# <a name="optimize-query-statistics-collection-in-azure-database-for-postgresql-server"></a>Otimizar a coleção de estatísticas de consulta no servidor do Banco de Dados do Azure para PostgreSQL 
Este artigo descreve como otimizar a coleção de estatísticas de consulta em um servidor do Banco de Dados do Azure para PostgreSQL.

## <a name="using-pgstatsstatements"></a>Usando pg_stats_statements
**Pg_stat_statements** é uma extensão do PostgreSQL habilitada por padrão no Banco de Dados do Azure para PostgreSQL. A extensão fornece um modo para rastrear as estatísticas de execução de todas as instruções SQL executadas por um servidor. No entanto, esse módulo está associado a cada execução de consulta e é fornecido com um custo de desempenho não trivial. Habilitar **pg_stat_statements** força as gravações de texto de consulta em arquivos no disco.

Para clientes que tenham consultas exclusivas com texto de consulta longo ou que não estejam monitorando **pg_stat_statements** ativamente, é recomendável desabilitar **pg_stat_statements** para obter um melhor desempenho definindo `pg_stat_statements.track = NONE`.

Em algumas cargas de trabalho de clientes, observamos uma melhoria de desempenho de até 50% desabilitando **pg_stat_statements**. No entanto, a desvantagem de desabilitar pg_stat_statements, é a impossibilidade de solucionar problemas de desempenho.

Para definir `pg_stat_statements.track = NONE`:

- No portal do Azure, navegue até a [página de gerenciamento de recursos do PostgreSQL e selecione a folha de parâmetros do servidor](howto-configure-server-parameters-using-portal.md).

![Folha de parâmetros do servidor PostgreSQL](.\media\howto-optimize-query-stats-collection\pg_stats_statements_portal.png)

- Use a [CLI do Azure](howto-configure-server-parameters-using-cli.md), az postgres server configuration set `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`.

## <a name="using-query-store"></a>Usar o Repositório de Consultas 
O recurso [Repositório de Consultas](concepts-query-store.md) no Banco de Dados do Azure para PostgreSQL fornece um método com melhor desempenho para rastrear estatísticas de consulta e é recomendado como uma alternativa ao uso de *pg_stats_statements*. 

## <a name="next-steps"></a>Próximas etapas
Considere configurar `pg_stat_statements.track = NONE` no [Portal do Azure](howto-configure-server-parameters-using-portal.md) ou usar a [CLI do Azure](howto-configure-server-parameters-using-cli.md).

Consulte os [Cenários de uso do Repositório de Consultas](concepts-query-store-scenarios.md) e as [Melhores práticas do Repositório de Consultas](concepts-query-store-best-practices.md) para obter mais informações. 
