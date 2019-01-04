---
title: Como configurar o Banco de Dados SQL do Azure – Singleton | Microsoft Docs
description: Saiba como configurar e gerenciar o Banco de Dados SQL do Azure – Banco de Dados Individual.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 12/14/2018
ms.openlocfilehash: d34853220e423e73c6ca8cf7c76ba616b815b8bd
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53439743"
---
# <a name="how-to-use-single-database"></a>Como usar o banco de dados individual

Nesta seção, é possível encontrar vários guias, scripts e explicações que podem ajudar você a gerenciar e configurar seu Banco de Dados SQL do Azure – Banco de Dados Individual.

## <a name="migrate"></a>Migrar

- [Migrar para o Banco de Dados SQL](sql-database-cloud-migrate.md) – saiba mais sobre o processo de migração recomendado e ferramentas para a migração para a Instância Gerenciada.
- Saiba como [gerenciar o Banco de Dados SQL após a migração](sql-database-manage-after-migration.md).

## <a name="configure-features"></a>Configurar recursos

- [Configurar a replicação transacional](replication-to-sql-database.md) para replicar sua data entre bancos de dados.
- [Configurar a detecção de ameaças](sql-database-threat-detection.md) para permitir que o Banco de Dados SQL do Azure identifique atividades suspeitas como Injeção de SQL ou acesso de locais suspeitos.
- [Configurar a máscara de dados dinâmicos](sql-database-dynamic-data-masking-get-started-portal.md) para proteger seus dados confidenciais.
- [Configurar a retenção de backup](sql-database-long-term-backup-retention-configure.md) para um banco de dados manter os backups no Armazenamento de Blobs do Azure. Como alternativa, há a abordagem de [Configurar a retenção de backup usando o cofre do Azure (preterido)](sql-database-long-term-backup-retention-configure-vault.md).
- [Configurar a replicação geográfica](sql-database-geo-replication-portal.md) para manter uma réplica do banco de dados em outra região.
- [Configurar a segurança para réplicas geográficas](sql-database-geo-replication-security-config.md).

## <a name="monitor-and-tune-your-database"></a>Monitorar e ajustar o banco de dados

- [Habilitar o ajuste automático](sql-database-automatic-tuning-enable.md) para permitir que o Banco de Dados SQL do Azure otimize o desempenho da sua carga de trabalho.
- [Habilitar as notificações por email para ajuste automático](sql-database-automatic-tuning-email-notifications.md) para obter informações sobre recomendações de ajuste.
- [Aplicar as recomendações de desempenho](sql-database-advisor-portal.md) e otimizar seu banco de dados.
- [Criar alertas](sql-database-insights-alerts-portal.md) para receber notificações do Banco de Dados SQL do Azure.
- [Solucionar problemas de conectividade](sql-database-troubleshoot-common-connection-issues.md) se perceber alguns problemas de conectividade entre os aplicativos e o banco de dados. Você também pode usar o [Resource Health para problemas de conectividade](sql-database-resource-health.md).
- [Gerenciar o espaço de arquivo](sql-database-file-space-management.md) para monitorar o uso de armazenamento no banco de dados.

## <a name="query-distributed-data"></a>Consulta de dados distribuídos

- [Consultar dados particionados verticalmente](sql-database-elastic-query-getting-started-vertical.md) entre vários bancos de dados.
- [Relatar na camada de dados de expansão](sql-database-elastic-query-horizontal-partitioning.md).
- [Consultar em tabelas com esquemas diferentes](sql-database-elastic-query-vertical-partitioning.md).

## <a name="elastic-database-jobs"></a>Trabalhos de Banco de Dados Elástico

- [Criar e gerenciar](elastic-jobs-powershell.md) trabalhos do Banco de Dados Elástico usando o PowerShell.
- [Criar e gerenciar](elastic-jobs-tsql.md) trabalhos do Banco de Dados Elástico usando o Transact-SQL.
- [Migrar de trabalhos Elásticos antigos](elastic-jobs-migrate.md).

## <a name="database-sharding"></a>Fragmentação do banco de dados

- [Atualizar a biblioteca de cliente do banco de dados elástico](sql-database-elastic-scale-upgrade-client-library.md).
- [Criar aplicativo fragmentado](sql-database-elastic-scale-get-started.md).
- [Consultar dados fragmentados horizontalmente](sql-database-elastic-query-getting-started.md).
- Executar [Consultas com vários fragmentos](sql-database-elastic-scale-multishard-querying.md).
- [Mover dados fragmentados](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
- [Configurar a segurança](sql-database-elastic-scale-split-merge-security-configuration.md) em fragmentos de banco de dados.
- [Adicionar um fragmento](sql-database-elastic-scale-add-a-shard.md) ao conjunto atual de fragmentos de bancos de dados.
- [Corrigir problemas de mapa de fragmentos](sql-database-elastic-database-recovery-manager.md).
- [Migrar Banco de Dados fragmentado](sql-database-elastic-convert-to-use-elastic-tools.md).
- [Criar contadores](sql-database-elastic-database-perf-counters.md).
- [Usar o Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) para consultar dados fragmentados.
- [Usar a estrutura de Dapper](sql-database-elastic-scale-working-with-dapper.md) para consultar dados fragmentados.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [Guias de instruções na Instância Gerenciada](sql-database-howto-managed-instance.md)
