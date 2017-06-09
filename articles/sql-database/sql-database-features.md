---
title: "Visão geral dos recursos do Banco de Dados SQL do Azure | Microsoft Docs"
description: "Esta página fornece uma visão geral dos servidores lógicos e bancos de dados do Banco de Dados SQL do Azure e inclui uma matriz de suporte a recursos com links de cada recurso listado."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d1a46fa4-53d2-4d25-a0a7-92e8f9d70828
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/03/2017
ms.author: carlrab; jognanay
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 9daf8be93216aefbcf02e3fcba44d048ab95b43d
ms.contentlocale: pt-br
ms.lasthandoff: 05/18/2017


---
# <a name="azure-sql-database-features"></a>Recursos do Banco de Dados SQL do Azure

As tabelas a seguir listam os principais recursos do Banco de Dados SQL do Azure e os recursos equivalentes do SQL Server - e fornecem informações sobre se há suporte para cada recurso específico e um link para obter mais informações sobre o recurso em cada plataforma. Para ver as diferenças do Transact-SQL a considerar ao migrar uma solução existente do banco de dados, consulte [Resolvendo as diferenças do Transact-SQL durante a migração para o Banco de Dados SQL](sql-database-transact-sql-information.md).

Continuamos a adicionar recursos ao Banco de Dados SQL do Azure. Portanto, recomendamos que você visite a nossa página da Web de atualizações de serviço do Azure e use os seus filtros:

* Filtrado para o [Serviço de Banco de Dados SQL](https://azure.microsoft.com/updates/?service=sql-database).
* Filtrado para disponibilidade geral [Anúncios (GA)](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) para recursos do Banco de Dados SQL.

| **Recurso** | **SQL Server** | **Banco de Dados SQL do Azure** | 
| --- | :---: | :---: | 
| Replicação geográfica ativa | Sem suporte – consulte [Grupos de Disponibilidade AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | [Com suporte](sql-database-geo-replication-overview.md)
| Always Encrypted | [Com suporte](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Suporte - consulte [Armazenamento de certificados](sql-database-always-encrypted.md) e [Cofre da chave](sql-database-always-encrypted-azure-key-vault.md)|
| Grupos de disponibilidade AlwaysOn | [Com suporte](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | Sem suporte - Consulte [replicação geográfica ativa](sql-database-geo-replication-overview.md) |
| Anexar um banco de dados | [Com suporte](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Sem suporte |
| Funções de aplicativo | [Com suporte](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | [Com suporte](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) |
| Escala automática | Sem suporte | Suporte - consulte [Camadas de serviço](sql-database-service-tiers.md) |
| Azure Active Directory | Sem suporte | [Com suporte](sql-database-aad-authentication.md) |
| Fábrica de dados do Azure | [Com suporte](../data-factory/data-factory-introduction.md) | [Com suporte](../data-factory/data-factory-introduction.md) |
| Auditoria | [Com suporte](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Com suporte](sql-database-auditing.md) |
| Arquivo BACPAC (exportação) | [Com suporte](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | [Com suporte](sql-database-export.md) |
| Arquivo BACPAC (importação) | [Com suporte](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | [Com suporte](sql-database-import.md) |
| BACKUP | [Com suporte](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | Sem suporte |
| Funções internas | [Com suporte](https://docs.microsoft.com/sql/t-sql/functions/functions) | Maioria - consulte [funções individuais] (https://docs.microsoft.com/sql/t-sql/functions/functions) |
| Captura de dados de alteração | [Com suporte](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Sem suporte |
| Controle de alterações | [Com suporte](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | [Com suporte](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) |
| Instruções de agrupamento | [Com suporte](https://docs.microsoft.com/sql/t-sql/statements/collations) | [Com suporte](https://docs.microsoft.com/sql/t-sql/statements/collations) |
| Índices ColumnStore | [Com suporte](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | [Somente no Premium Edition](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |
| CLR (Common Language Runtime) | [Com suporte](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Sem suporte |
| Bancos de dados independentes | [Com suporte](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | [Com suporte](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) |
| Usuários independentes | [Com suporte](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | [Com suporte](sql-database-manage-logins.md#non-administrator-users) |
| Controle de palavras-chave da linguagem de controle de fluxo | [Com suporte](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | [Com suporte](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) |
| Consultas entre bancos de dados | [Com suporte](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | Parcial - consulte [Consultas elásticas](sql-database-elastic-query-overview.md) |
| Cursores | [Com suporte](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | [Com suporte](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | 
| Compactação de dados | [Com suporte](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | [Com suporte](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) |
| Backups de banco de dados | [Usuário gerenciado](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) | [Gerenciado pelo serviço do Banco de Dados SQL](sql-database-automated-backups.md) |
| Database Mail | [Com suporte](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Sem suporte |
| Espelhamento de banco de dados | [Com suporte](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Sem suporte |
| Definições de configuração do banco de dados | [Com suporte](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | [Com suporte](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) |
| DQS (Data Quality Services) | [Com suporte](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Sem suporte |
| Instantâneos de banco de dados | [Com suporte](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Sem suporte |
| Tipos de dados | [Com suporte](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | [Com suporte](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) |  
| Instruções DBCC | [Com suporte](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | Maioria - consulte [instruções individuais](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) |
| Instruções DDL | [Com suporte](https://docs.microsoft.com/sql/t-sql/statements/statements) | Maioria - consulte [Instruções individuais](https://docs.microsoft.com/sql/t-sql/statements/statements)
| Gatilhos DDL | [Com suporte](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | [Somente banco de dados](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) |
| Transações distribuídas | [MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Somente em cenários limitados internos do Banco de Dados SQL |
| Instruções DML | [Com suporte](https://docs.microsoft.com/sql/t-sql/queries/queries) | Maioria - consulte [Instruções individuais]] (https://docs.microsoft.com/sql/t-sql/queries/queries) |
| Gatilhos DML | [Com suporte](https://docs.microsoft.com/sql/relational-databases/triggers/dml-triggers) | [Com suporte](https://docs.microsoft.com/sql/relational-databases/triggers/dml-triggers) |
| DMVs | [Todas](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | Alguns - consulte [DMVs individuais](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) |
| Pools elásticos | Sem suporte | [Com suporte](sql-database-elastic-pool.md) |
| Trabalhos elásticos | Sem suporte – consulte [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | [Com suporte](sql-database-elastic-jobs-getting-started.md) | 
| Consultas elásticas | Sem suporte – consulte [Consultas de bancos de dados](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | [Com suporte](sql-database-elastic-query-overview.md) |
| Notificações de eventos | [Com suporte](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | [Com suporte](sql-database-insights-alerts-portal.md) |
| Expressões | [Com suporte](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) | [Com suporte](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |
| Eventos estendidos | [Com suporte](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Alguns - consulte [Eventos individuais](sql-database-xevent-db-diff-from-svr.md) |
| Procedimentos armazenados estendidos | [Com suporte](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Sem suporte |
| Arquivos e grupos de arquivos | [Com suporte](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | [Somente primário](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) |
| Fluxo de arquivos | [Com suporte](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Sem suporte |
| Pesquisa de texto completo | [Com suporte](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) | [Separadores de palavras de terceiros sem suporte](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |
| Funções | [Com suporte](https://docs.microsoft.com/sql/t-sql/functions/functions) | Maioria - consulte [Funções individuais](https://docs.microsoft.com/sql/t-sql/functions/functions) |
| Otimização na memória | [Com suporte](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | [Somente no Premium Edition](sql-database-in-memory.md) |
| Trabalhos | Consulte [Agente do SQL Server](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Consulte [Trabalhos elásticos](sql-database-elastic-jobs-getting-started.md) |
| Suporte a dados JSON | [Com suporte](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [Com suporte](sql-database-json-features.md) |
| Elementos de linguagem | [Com suporte](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | Maioria - consulte [Elementos individuais](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) |  
| Servidores vinculados | [Com suporte](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Sem suporte – consulte [Consulta elástica](sql-database-elastic-query-horizontal-partitioning.md) |
| Envio de logs | [Com suporte](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | Sem suporte - consulte [replicação geográfica ativa](sql-database-geo-replication-overview.md) |
| MDS (Master Data Services) | [Com suporte](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Sem suporte |
| Log mínimo na importação em massa | [Com suporte](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Sem suporte |
| Modificação dos dados do sistema | [Com suporte](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Sem suporte |
| Operações de índice online | [Com suporte](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | [Suporte - tamanho da transação limitado pela camada de serviço](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) |
| Operadores | [Com suporte](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | Maioria - consulte [Operadores individuais](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) |
| Recuperação pontual de banco de dados | [Com suporte](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | [Com suporte](sql-database-recovery-using-backups.md#point-in-time-restore) |
| Polybase | [Com suporte](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Sem suporte
| Gerenciamento baseado em políticas | [Com suporte](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Sem suporte |
| Predicados | [Com suporte](https://docs.microsoft.com/sql/t-sql/queries/predicates) | Maioria - consulte [Predicados individuais](https://docs.microsoft.com/sql/t-sql/queries/predicates)
| R Services | [Com suporte](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services)
| Administrador de recursos | [Com suporte](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Sem suporte |
| Instruções RESTORE | [Com suporte](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Sem suporte | 
| Restauração do banco de dados por meio de backup | [Com suporte](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | [Somente de backups internos](sql-database-recovery-using-backups.md) |
| Segurança em nível de linha | [Com suporte](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | [Com suporte](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) |
| Pesquisa semântica | [Com suporte](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Sem suporte |
| Números de sequência | [Com suporte](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | [Com suporte](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) |
| Agente de Serviço | [Com suporte](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Sem suporte |
| Definições de configuração do servidor | [Com suporte](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Sem suporte – consulte [Opções de configuração do banco de dados](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) |
| Instruções SET | [Com suporte](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | Maioria - consulte [Instruções individuais](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) 
| Espacial | [Com suporte](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | [Com suporte](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) |
| SQL Server Agent | [Com suporte](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Sem suporte – consulte [Trabalhos elásticos](sql-database-elastic-jobs-getting-started.md) |
| SSAS (SQL Server Analysis Services) | [Com suporte](https://docs.microsoft.com/sql/analysis-services/analysis-services) | Sem suporte – consulte [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| SQL Server Integration Services (SSIS) | [Com suporte](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Sem suporte – consulte [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) |
| SQL Server PowerShell | [Com suporte](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | [Com suporte](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) |
| SQL Server Profiler | [Com suporte](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | Sem suporte – consulte [Eventos estendidos](sql-database-xevent-db-diff-from-svr.md) |
| Replicação do SQL Server | [Com suporte](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Somente assinante de replicação de instantâneo e transacional](sql-database-cloud-migrate.md) |
| SSRS (SQL Server Reporting Services) | [Com suporte](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | Sem suporte |
| Procedimentos armazenados | [Com suporte](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | [Com suporte](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) |
| Funções armazenadas do sistema | [Com suporte](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | Maioria - consulte [Função individual](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) |
| Procedimentos armazenados do sistema | [Com suporte](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Alguns - consulte [Procedimento armazenado individual](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) |
| Tabelas do sistema | [Com suporte](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Alguns - consulte [Tabela individual](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) |
| Exibições do catálogo do sistema | [Com suporte](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Alguns - consulte [Exibições individuais](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql)
| Particionamento de tabela | [Com suporte](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Suporte - [Grupo de arquivos primário](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) |
| Tabelas temporárias | [Local e global](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) | [Somente local](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) |
| Tabelas temporais | [Com suporte](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [Com suporte](sql-database-temporal-tables.md) |
| Transações | [Com suporte](https://docs.microsoft.com/sql/t-sql/language-elements/transactions-transact-sql) | [Com suporte](https://docs.microsoft.com/sql/t-sql/language-elements/transactions-transact-sql) |
| variáveis | [Com suporte](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | [Com suporte](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | 
| TDE (Transparent Data Encryption)  | [Com suporte](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | [Com suporte](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) |
| Windows Server Failover Clustering | [Com suporte](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | Sem suporte - Consulte [replicação geográfica ativa](sql-database-geo-replication-overview.md) |
| Índices XML | [Com suporte](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | [Com suporte](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) |

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre o serviço do Banco de Dados SQL do Azure, consulte [O que é o Banco de Dados SQL?](sql-database-technical-overview.md)
- Para obter informações sobre o suporte do Transact-SQL e as diferenças, consulte [Resolvendo as diferenças do Transact-SQL durante a migração para o Banco de Dados SQL](sql-database-transact-sql-information.md).

