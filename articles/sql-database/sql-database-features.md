---
title: Comparação de recursos do Banco de Dados SQL do Azure | Microsoft Docs
description: Este artigo compara os recursos do Banco de Dados SQL do Azure e as Instâncias Gerenciadas entre si e com o SQL Server.
services: sql-database
author: jovanpop-msft
ms.reviewer: bonova, carlrab
ms.service: sql-database
ms.topic: article
ms.date: 03/21/2018
ms.author: jovanpop
manager: craigg
ms.openlocfilehash: 842c65987dc97872751071dbcf138794a14940e2
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="feature-comparison-azure-sql-database-versus-sql-server"></a>Comparação de recursos: Banco de Dados SQL do Azure versus SQL Server 

O Banco de Dados SQL do Azure compartilha uma base de código comum com o SQL Server. Os recursos do SQL Server compatíveis com o Banco de Dados SQL do Azure dependem do tipo de banco de dados SQL do Azure criado. Com o Banco de Dados SQL do Azure, você pode criar um banco de dados como parte de uma [instância gerenciada](sql-database-managed-instance.md) (atualmente, em visualização pública) ou criar um banco de dados que é um banco de dados individual ou um banco de dados que faz parte de um pool elástico. 

A Microsoft continua adicionando recursos ao Banco de Dados SQL do Azure. Visite a página da Web Atualizações de serviço do Azure para obter as atualizações mais recentes usando estes filtros:

* Filtrado para o [Serviço de Banco de Dados SQL](https://azure.microsoft.com/updates/?service=sql-database).
* Filtrado para disponibilidade geral [Anúncios (GA)](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) para recursos do Banco de Dados SQL.

## <a name="sql-server-and-sql-database-feature-support"></a>Suporte ao recurso Banco de Dados SQL e SQL Server

A tabela a seguir lista os principais recursos do SQL Server e fornece informações sobre se há suporte parcial ou completo para o recurso e um link para mais informações sobre o recurso. 

| **Recurso do SQL** | **Com suporte no Banco de Dados SQL do Azure** | **Instância Gerenciada (versão prévia)** |
| --- | --- | --- |
| [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Sim - veja [Armazenamento de certificados](sql-database-always-encrypted.md) e [Cofre de chaves](sql-database-always-encrypted-azure-key-vault.md) | Sim - veja [Armazenamento de certificados](sql-database-always-encrypted.md) e [Cofre de chaves](sql-database-always-encrypted-azure-key-vault.md) |
| [Grupos de Disponibilidade AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | A [alta disponibilidade](sql-database-high-availability.md) é incluída em todos os bancos de dados. A recuperação de desastre é abordada em [Visão geral da continuidade de negócios com o banco de dados SQL do Azure](sql-database-business-continuity.md) | A [alta disponibilidade](sql-database-high-availability.md) é incluída em todos os bancos de dados. A recuperação de desastre é abordada em [Visão geral da continuidade de negócios com o banco de dados SQL do Azure](sql-database-business-continuity.md) |
| [Anexar um banco de dados](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Não  | Não  |
| [Funções do aplicativo](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | sim | sim |
|[Auditoria](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Sim](sql-database-auditing.md)| [Sim](sql-database-managed-instance-auditing.md) |
| [Backups automáticos](sql-database-automated-backups.md) | sim | sim |
| [Ajuste automático (imposição de plano)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Sim](sql-database-automatic-tuning.md)| [Sim](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) |
| [Ajuste automático (índices)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Sim](sql-database-automatic-tuning.md)| Não  |
| [Arquivo BACPAC (exportação)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Sim, veja [Exportação do Banco de Dados SQL](sql-database-export.md) | sim |
| [Arquivo BACPAC (importação)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Sim - veja [Importação de Banco de Dados SQL](sql-database-import.md) | sim |
| [Comando BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | Não, somente backups automáticos iniciados pelo sistema – consulte [Backups automatizados](sql-database-automated-backups.md) | Backups automatizados iniciados pelo sistema e backups somente cópia iniciados pelo usuário – consulte [Diferenças de backup](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Funções internas](https://docs.microsoft.com/sql/t-sql/functions/functions) | Maioria - veja funções individuais | Sim – consulte [Diferenças entre procedimentos armazenados, funções e gatilhos](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Alterar captura de dados](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Não  | sim |
| [Controle de alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | sim |sim |
| [Instruções de agrupamento](https://docs.microsoft.com/sql/t-sql/statements/collations) | sim | sim |
| [Índices Columnstore](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | Sim - [Camada Premium e camada Standard - S3 e posterior](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |sim |
| [Common language runtime (CLR)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Não  | Sim – consulte [Diferenças do CLR](sql-database-managed-instance-transact-sql-information.md#clr) |
| [Bancos de dados independentes](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | sim | sim |
| [Usuários independentes](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | sim | sim |
| [Controle de palavras-chave da linguagem de fluxo](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | sim | sim |
| [Consultas entre bancos de dados](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Não – consulte [Consultas elásticas](sql-database-elastic-query-overview.md) | Sim, além de [Consultas elásticas](sql-database-elastic-query-overview.md) |
| [Transações entre bancos de dados](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Não  | sim |
| [Cursores](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | sim |sim | 
| [Compactação de dados](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | sim |sim |
| [Database mail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Não  | sim |
| [DMS (Serviço de Migração de Dados)](https://docs.microsoft.com/sql/dma/dma-overview) | sim | sim |
| [Espelhamento de banco de dados](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Não  | Não  |
| [Definições de configuração do banco de dados](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | sim | sim |
| [Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Não  | Não  |
| [Instantâneos de banco de dados](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Não  | Não  |
| [Tipos de dados](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | sim |sim |
| [Instruções DBCC](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | Maioria - veja Instruções individuais | Sim – consulte [Diferenças do DBCC](sql-database-managed-instance-transact-sql-information.md#dbcc) |
| [Instruções DDL](https://docs.microsoft.com/sql/t-sql/statements/statements) | Maioria - veja Instruções individuais | Sim – consulte [Diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Gatilhos DDL](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | Apenas banco de dados |  sim |
| [Exibições de partição distribuída](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | Não  | sim |
| [Transações distribuídas - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Não - veja [transações elásticas](sql-database-elastic-transactions-overview.md) |  Não - veja [transações elásticas](sql-database-elastic-transactions-overview.md) |
| [Instruções DML](https://docs.microsoft.com/sql/t-sql/queries/queries) | sim | sim |
| [Gatilhos DML](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | Maioria - veja Instruções individuais |  sim |
| [DMVs](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | A maioria – consulte DMVs individuais |  Sim – consulte [Diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md) |
|[Mascaramento de dados dinâmicos](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)|[Sim](sql-database-dynamic-data-masking-get-started.md)| sim |
| [Pools elásticos](sql-database-elastic-pool.md) | sim | uma única Instância Gerenciada pode ter vários bancos de dados que compartilham o mesmo pool de recursos |
| [Notificações de eventos](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | Não - veja [Alertas](sql-database-insights-alerts-portal.md) | sim |
| [Expressões](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |sim | sim |
| [Eventos estendidos](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Alguns - veja [Eventos estendidos no Banco de Dados SQL](sql-database-xevent-db-diff-from-svr.md) | Sim – consulte [Diferenças de eventos estendidos ](sql-database-managed-instance-transact-sql-information.md#extended-events) |
| [Procedimentos armazenados estendidos](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Não  | Não  |
[Arquivos e grupos de arquivos](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | Somente o grupo de arquivos primários | sim |
| [Filestream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Não  | Não  |
| [Pesquisa de texto completo](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |  Separadores de palavras de terceiros sem suporte |Separadores de palavras de terceiros sem suporte |
| [Funções](https://docs.microsoft.com/sql/t-sql/functions/functions) | Maioria - veja funções individuais | Sim – consulte [Diferenças entre procedimentos armazenados, funções e gatilhos](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Restauração geográfica](sql-database-recovery-using-backups.md#geo-restore) | sim | Não – você pode restaurar backups completos COPY_ONLY feitos periodicamente – consulte [Diferenças de backup](sql-database-managed-instance-transact-sql-information.md#backup) e [Diferenças de restauração](sql-database-managed-instance-transact-sql-information.md#restore-statement). |
| [Replicação geográfica](sql-database-geo-replication-overview.md) | sim | Não  |
| [Processamento de grafo](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) | sim | sim |
| [Otimização na memória](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | Sim - [somente no Premium Edition](sql-database-in-memory.md) | Não  |
| [Suporte a dados JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | sim | sim |
| [Elementos de linguagem](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | Maioria - veja elementos individuais |  Sim – consulte [Diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Servidores vinculados](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Não - veja [Consulta elástica](sql-database-elastic-query-horizontal-partitioning.md) | Somente para o SQL Server |
| [Envio de logs](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | A [alta disponibilidade](sql-database-high-availability.md) é incluída em todos os bancos de dados. A recuperação de desastre é abordada em [Visão geral da continuidade de negócios com o banco de dados SQL do Azure](sql-database-business-continuity.md) |A [alta disponibilidade](sql-database-high-availability.md) é incluída em todos os bancos de dados. A recuperação de desastre é abordada em [Visão geral da continuidade de negócios com o banco de dados SQL do Azure](sql-database-business-continuity.md) |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Não  | Não  |
| [Log mínimo na importação em massa](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Não  | Não  |
| [Modificação dos dados do sistema](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Não  | sim |
| [Operações de índice online](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | sim | sim |
| [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)|Não |Sim – consulte [Diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENJSON](https://docs.microsoft.com/sql/t-sql/functions/openjson-transact-sql)|sim|sim|
| [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql)|Não |Sim – consulte [Diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)|Não |Sim – consulte [Diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENXML](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql)|sim|sim|
| [Operadores](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | Maioria - veja operadores individuais |Sim – consulte [Diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Particionamento](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | sim | sim |
| [Restauração pontual de banco de dados](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | Sim - veja [Recuperação do Banco de Dados SQL](sql-database-recovery-using-backups.md#point-in-time-restore) | Sim - veja [Recuperação do Banco de Dados SQL](sql-database-recovery-using-backups.md#point-in-time-restore) |
| [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Não  | Não  |
| [Gerenciamento baseado em políticas](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Não  | Não  |
| [Predicados](https://docs.microsoft.com/sql/t-sql/queries/predicates) | sim | sim |
| [R Services](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | Versão de teste; consulte [O que há de novo em aprendizado de máquina](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  | Não  |
| [Resource governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Não  | Não  |
| [Instruções RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Não  | Sim – consulte [Diferenças de restauração](sql-database-managed-instance-transact-sql-information.md#restore-statement) |
| [Restaurar banco de dados desde o backup](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | Somente de backups automatizados – consulte [Recuperação do Banco de Dados SQL](sql-database-recovery-using-backups.md) | De backups automatizados – consulte [Recuperação do Banco de Dados SQL](sql-database-recovery-using-backups.md) e de backups completos – consulte [Diferenças de backup](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Segurança em Nível de Linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | sim | sim |
| [Pesquisa semântica](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Não  | Não  |
| [Números de sequência](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | sim | sim |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Não  | Sim – consulte [Diferenças do Service Broker](sql-database-managed-instance-transact-sql-information.md#service-broker) |
| [Definições de configuração do servidor](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Não  | Sim – consulte [Diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Instruções Set](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | Maioria - veja Instruções individuais | Sim – consulte [Diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | sim | sim |
| [Espacial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | sim | sim |
| [Sincronização de Dados SQL](sql-database-get-started-sql-data-sync.md) | sim | sim |
| [SQL Operations Studio](https://docs.microsoft.com/sql/sql-operations-studio/what-is) | sim | sim |
| [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Não – consulte [Trabalhos elásticos](sql-database-elastic-jobs-getting-started.md) | Sim – consulte [Diferenças do SQL Server Agent](sql-database-managed-instance-transact-sql-information.md#sql-server-agent) |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | Não – consulte [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) | Não, veja [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| [Auditoria do SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | Não - veja [auditoria do Banco de Dados SQL](sql-database-auditing.md) | Sim – consulte [Diferenças de auditoria](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [SSDT (SQL Server Data Tools)] (https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) | sim | sim |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Parcial – não há suporte para o desenvolvimento de pacotes no SQL Server Data Tools. | Não. Não em Visualização Pública |
| [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) | sim | sim |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | sim | sim |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | Não - veja [Eventos estendidos](sql-database-xevent-db-diff-from-svr.md) | sim |
| [Replicação do SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Somente assinante de replicação de instantâneo e transacional](sql-database-cloud-migrate.md) | Não  |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | Não – [consulte Power BI](https://docs.microsoft.com/power-bi/) | Não – [consulte Power BI](https://docs.microsoft.com/power-bi/) |
| [Procedimentos armazenados](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | sim | sim |
| [Funções armazenadas do sistema](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | Maioria - veja funções individuais | Sim – consulte [Diferenças entre procedimentos armazenados, funções e gatilhos](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Procedimentos armazenados do sistema](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Alguns - veja procedimentos armazenados individuais | Sim – consulte [Diferenças entre procedimentos armazenados, funções e gatilhos](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Tabelas do sistema](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Alguns - veja tabelas individuais | Sim – consulte [Diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Exibições do catálogo do sistema](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Alguns - veja exibições individuais | Sim – consulte [Diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Tabelas temporárias](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) | Tabelas locais e temporárias globais no escopo do banco de dados | Tabelas locais e temporárias globais no escopo da instância |
| [Tabelas temporais](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | sim | sim |
|Detecção de ameaças|  [Sim](sql-database-threat-detection.md)|[Sim](sql-database-managed-instance-threat-detection.md)|
| [Sinalizadores de rastreamento](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | Não  | Não  |
| [Variáveis](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | sim | sim |
| [Transparent data encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | sim | Não. Não em Visualização Pública |
[Rede virtual](../virtual-network/virtual-networks-overview.md) | Parcial – consulte [Pontos de extremidade de VNET](sql-database-vnet-service-endpoint-rule-overview.md) | Sim, somente no modelo do Resource Manager |
| [Clustering de Failover do Windows Server](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | A [alta disponibilidade](sql-database-high-availability.md) é incluída em todos os bancos de dados. A recuperação de desastre é abordada em [Visão geral da continuidade de negócios com o banco de dados SQL do Azure](sql-database-business-continuity.md) | A [alta disponibilidade](sql-database-high-availability.md) é incluída em todos os bancos de dados. A recuperação de desastre é abordada em [Visão geral da continuidade de negócios com o banco de dados SQL do Azure](sql-database-business-continuity.md) |
| [Índices XML](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | sim | sim |

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre o serviço do Banco de Dados SQL do Azure, consulte [O que é o Banco de Dados SQL?](sql-database-technical-overview.md)
- Para obter informações sobre uma Instância Gerenciada, consulte [O que é uma Instância Gerenciada?](sql-database-managed-instance.md).
