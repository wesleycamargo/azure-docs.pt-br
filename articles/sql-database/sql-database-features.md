---
title: Comparação de recursos do Banco de Dados SQL do Azure | Microsoft Docs
description: Este artigo compara os recursos do SQL Server disponíveis em diferentes tipos do Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 82ed8783444a414d4706f97c8c9b6ea18734561e
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311740"
---
# <a name="feature-comparison-azure-sql-database-versus-sql-server"></a>Comparação de Recursos: Banco de Dados SQL do Azure versus SQL Server

O Banco de Dados SQL do Azure compartilha uma base de código comum com o SQL Server. Os recursos do SQL Server compatíveis com o Banco de Dados SQL do Azure dependem do tipo de banco de dados SQL do Azure criado. Com o Banco de Dados SQL do Azure, você pode criar um banco de dados como parte de uma [instância gerenciada](sql-database-managed-instance.md), como um banco de dados individual ou como parte de um pool elástico.

A Microsoft continua adicionando recursos ao Banco de Dados SQL do Azure. Visite a página da Web Atualizações de serviço do Azure para obter as atualizações mais recentes usando estes filtros:

- Filtrado para o [Serviço de Banco de Dados SQL](https://azure.microsoft.com/updates/?service=sql-database).
- Filtrado para disponibilidade geral [Anúncios (GA)](https://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) para recursos do Banco de Dados SQL.

## <a name="sql-server-feature-support-in-azure-sql-database"></a>Suporte ao recurso do SQL Server no Banco de Dados SQL do Azure

A tabela a seguir lista os principais recursos do SQL Server e fornece informações sobre se há suporte parcial ou completo para o recurso e um link para mais informações sobre o recurso.

| **Recurso do SQL** | **Com suporte em bancos de dados únicos e pools Elásticos** | **Com suporte por instâncias gerenciadas** |
| --- | --- | --- |
| [Replicação geográfica ativa](sql-database-active-geo-replication.md) | Sim - todos os tipos diferentes de hiperescala de serviço | Não, confira [Grupos de failover automático](sql-database-auto-failover-group.md) |
| [Grupos de failover automático](sql-database-auto-failover-group.md) | Sim - todos os tipos diferentes de hiperescala de serviço | Sim, em [versão prévia pública](sql-database-auto-failover-group.md)|
| [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Sim - veja [Armazenamento de certificados](sql-database-always-encrypted.md) e [Cofre de chaves](sql-database-always-encrypted-azure-key-vault.md) | Sim - veja [Armazenamento de certificados](sql-database-always-encrypted.md) e [Cofre de chaves](sql-database-always-encrypted-azure-key-vault.md) |
| [Grupos de disponibilidade AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | A [alta disponibilidade](sql-database-high-availability.md) é incluída em todos os bancos de dados. A recuperação de desastre é abordada em [Visão geral da continuidade de negócios com o banco de dados SQL do Azure](sql-database-business-continuity.md) | A [alta disponibilidade](sql-database-high-availability.md) é incluída em todos os bancos de dados. A recuperação de desastre é abordada em [Visão geral da continuidade de negócios com o banco de dados SQL do Azure](sql-database-business-continuity.md) |
| [Anexar um banco de dados](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Não | Não |
| [Funções do aplicativo](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | Sim | Sim |
|[Auditoria](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Sim](sql-database-auditing.md)| [Sim](sql-database-managed-instance-auditing.md) |
| [Backups automáticos](sql-database-automated-backups.md) | Sim | Sim |
| [Ajuste automático (imposição de plano)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Sim](sql-database-automatic-tuning.md)| [Sim](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) |
| [Ajuste automático (índices)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Sim](sql-database-automatic-tuning.md)| Não |
| [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) | Sim | Sim |
| [Arquivo BACPAC (exportação)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Sim, veja [Exportação do Banco de Dados SQL](sql-database-export.md) | Sim |
| [Arquivo BACPAC (importação)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Sim - veja [Importação de Banco de Dados SQL](sql-database-import.md) | Sim |
| [Comando BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | Não, somente backups automáticos iniciados pelo sistema – consulte [Backups automatizados](sql-database-automated-backups.md) | Backups automatizados iniciados pelo sistema e backups somente cópia iniciados pelo usuário – consulte [Diferenças de backup](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Funções internas](https://docs.microsoft.com/sql/t-sql/functions/functions) | Maioria - veja funções individuais | Sim – consulte [Diferenças entre procedimentos armazenados, funções e gatilhos](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Alterar captura de dados](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Não | Sim |
| [Controle de alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | Sim |Sim |
| [Agrupamento - banco de dados](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation) | Sim | Sim |
| [Agrupamento - servidor/instância](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation) | Não | Sim, em [versão prévia pública](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)|
| [Índices Columnstore](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | Sim - [Camada Premium, camada Standard - S3 e superior, camada Uso Geral e camada Comercialmente Crítico](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |Sim |
| [Common language runtime (CLR)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Não | Sim – consulte [Diferenças do CLR](sql-database-managed-instance-transact-sql-information.md#clr) |
| [Bancos de dados independentes](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | Sim | Não [devido a falha na restauração, incluindo restauração point-in-time](sql-database-managed-instance-transact-sql-information.md#cannot-restore-contained-database) |
| [Usuários independentes](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | Sim | Sim |
| [Controle de palavras-chave da linguagem de fluxo](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | Sim | Sim |
| [Consultas entre bancos de dados](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Não – consulte [Consultas elásticas](sql-database-elastic-query-overview.md) | Sim, além de [Consultas elásticas](sql-database-elastic-query-overview.md) |
| [Transações entre bancos de dados](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Não | Sim, dentro da instância. Ver [vinculado diferenças de servidor](sql-database-managed-instance-transact-sql-information.md#linked-servers) para consultas entre instâncias. |
| [Cursores](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | Sim |Sim |
| [Compactação de dados](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | Sim |Sim |
| [Database mail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Não | Sim |
| [DMS (Serviço de Migração de Dados)](https://docs.microsoft.com/sql/dma/dma-overview) | Sim | Sim |
| [Espelhamento de banco de dados](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Não | Não |
| [Definições de configuração do banco de dados](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | Sim | Sim |
| [Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Não | Não |
| [Instantâneos de banco de dados](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Não | Não |
| [Tipos de dados](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | Sim |Sim |
| [Instruções DBCC](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | Maioria - veja Instruções individuais | Sim – consulte [Diferenças do DBCC](sql-database-managed-instance-transact-sql-information.md#dbcc) |
| [Instruções DDL](https://docs.microsoft.com/sql/t-sql/statements/statements) | Maioria - veja Instruções individuais | Sim – consulte [Diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Gatilhos DDL](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | Apenas banco de dados |  Sim |
| [Exibições de partição distribuída](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | Não | Sim |
| [Transações distribuídas - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Não - veja [transações elásticas](sql-database-elastic-transactions-overview.md) |  Não – consulte [vinculado diferenças de servidor](sql-database-managed-instance-transact-sql-information.md#linked-servers) |
| [Instruções DML](https://docs.microsoft.com/sql/t-sql/queries/queries) | Sim | Sim |
| [Gatilhos DML](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | Maioria - veja Instruções individuais |  Sim |
| [DMVs](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | A maioria – consulte DMVs individuais |  Sim – consulte [Diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md) |
|[Mascaramento de dados dinâmicos](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)|[Sim](sql-database-dynamic-data-masking-get-started.md)| [Sim](sql-database-dynamic-data-masking-get-started.md) |
| [Pools elásticos](sql-database-elastic-pool.md) | Sim | Interna - uma única Instância Gerenciada pode ter vários bancos de dados que compartilham o mesmo pool de recursos |
| [Notificações de eventos](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | Não - veja [Alertas](sql-database-insights-alerts-portal.md) | Não |
| [Expressões](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |Sim | Sim |
| [Eventos estendidos](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Alguns - veja [Eventos estendidos no Banco de Dados SQL](sql-database-xevent-db-diff-from-svr.md) | Sim – consulte [Diferenças de eventos estendidos ](sql-database-managed-instance-transact-sql-information.md#extended-events) |
| [Procedimentos armazenados estendidos](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Não | Não |
[Arquivos e grupos de arquivos](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | Somente o grupo de arquivos primários | Sim |
| [Filestream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Não | Não |
| [Pesquisa de texto completo](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |  Separadores de palavras de terceiros sem suporte |Separadores de palavras de terceiros sem suporte |
| [Funções](https://docs.microsoft.com/sql/t-sql/functions/functions) | Maioria - veja funções individuais | Sim – consulte [Diferenças entre procedimentos armazenados, funções e gatilhos](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Restauração geográfica](sql-database-recovery-using-backups.md#geo-restore) | Sim - todos os tipos diferentes de hiperescala de serviço | Não – você pode restaurar backups completos COPY_ONLY feitos periodicamente – consulte [Diferenças de backup](sql-database-managed-instance-transact-sql-information.md#backup) e [Diferenças de restauração](sql-database-managed-instance-transact-sql-information.md#restore-statement). |
| [Processamento de grafo](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) | Sim | Sim |
| [Otimização na memória](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | Sim – [Apenas camadas Premium e Comercialmente Crítico](sql-database-in-memory.md) | Sim - [Camada Comercialmente Crítico somente](sql-database-managed-instance.md) |
| [Suporte a dados JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [Sim](sql-database-json-features.md) | [Sim](sql-database-json-features.md) |
| [Elementos de linguagem](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | Maioria - veja elementos individuais |  Sim – consulte [Diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Servidores vinculados](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Não - veja [Consulta elástica](sql-database-elastic-query-horizontal-partitioning.md) | Apenas para SQL Server e Banco de Dados SQL |
| [Envio de logs](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | A [alta disponibilidade](sql-database-high-availability.md) é incluída em todos os bancos de dados. A recuperação de desastre é abordada em [Visão geral da continuidade de negócios com o banco de dados SQL do Azure](sql-database-business-continuity.md) |A [alta disponibilidade](sql-database-high-availability.md) é incluída em todos os bancos de dados. A recuperação de desastre é abordada em [Visão geral da continuidade de negócios com o banco de dados SQL do Azure](sql-database-business-continuity.md) |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Não | Não |
| [Log mínimo na importação em massa](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Não | Não |
| [Modificação dos dados do sistema](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Não | Sim |
| [Automação OLE](https://docs.microsoft.com/sql/database-engine/configure-windows/ole-automation-procedures-server-configuration-option) | Não | Não |
| [Operações de índice online](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | Sim | Sim |
| [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)|Não|Sim – consulte [Diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENJSON](https://docs.microsoft.com/sql/t-sql/functions/openjson-transact-sql)|Sim|Sim|
| [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql)|Não|Sim – consulte [Diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)|Não|Sim – consulte [Diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [OPENXML](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql)|Sim|Sim|
| [Operadores](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | Maioria - veja operadores individuais |Sim – consulte [Diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Particionamento](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Sim | Sim |
| [Restauração pontual de banco de dados](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | Sim - Veja todas as camadas de serviço que não seja em hiperescala - [recuperação de banco de dados SQL](sql-database-recovery-using-backups.md#point-in-time-restore) | Sim - veja [Recuperação do Banco de Dados SQL](sql-database-recovery-using-backups.md#point-in-time-restore) |
| [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Não | Não |
| [Gerenciamento baseado em políticas](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Não | Não |
| [Predicados](https://docs.microsoft.com/sql/t-sql/queries/predicates) | Sim | Sim |
| [Notificações de consulta](https://docs.microsoft.com/sql/relational-databases/native-client/features/working-with-query-notifications) | Não | Sim |
| [Insights de Desempenho de Consulta](sql-database-query-performance.md) | Sim | Não |
| [R Services](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | Sim, em [versão prévia pública](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  | Não |
| [Resource governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Não | Sim |
| [Instruções RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Não | Sim – consulte [Diferenças de restauração](sql-database-managed-instance-transact-sql-information.md#restore-statement) |
| [Restaurar banco de dados desde o backup](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | Somente de backups automatizados – consulte [Recuperação do Banco de Dados SQL](sql-database-recovery-using-backups.md) | De backups automatizados – consulte [Recuperação do Banco de Dados SQL](sql-database-recovery-using-backups.md) e de backups completos – consulte [Diferenças de backup](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Segurança em Nível de Linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | Sim | Sim |
| [Pesquisa semântica](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Não | Não |
| [Números de sequência](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | Sim | Sim |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Não | Sim – consulte [Diferenças do Service Broker](sql-database-managed-instance-transact-sql-information.md#service-broker) |
| [Definições de configuração do servidor](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Não | Sim – consulte [Diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Instruções Set](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | Maioria - veja Instruções individuais | Sim – consulte [Diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md)|
| [SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | Sim | Sim |
| [Espacial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | Sim | Sim |
| [Análise de SQL](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) | Sim | Sim |
| [Sincronização de Dados SQL](sql-database-get-started-sql-data-sync.md) | Sim | Não |
| [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Não – consulte [Trabalhos elásticos](sql-database-elastic-jobs-getting-started.md) | Sim – consulte [Diferenças do SQL Server Agent](sql-database-managed-instance-transact-sql-information.md#sql-server-agent) |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | Não – consulte [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) | Não, veja [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| [Auditoria do SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | Não - veja [auditoria do Banco de Dados SQL](sql-database-auditing.md) | Sim – consulte [Diferenças de auditoria](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [SSDT (Ferramentas de Dados do SQL Server)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) | Sim | Sim |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Sim, com um SSIS gerenciado no ambiente da fábrica de dados do Azure (AAD), onde os pacotes estão armazenados no SSISDB hospedado pelo banco de dados SQL e executado no Azure SSIS integração em tempo de execução (IV), consulte [criar IR do SSIS do Azure no ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). <br/><br/>Para comparar os recursos do SSIS na Instância Gerenciada e no servidor de Banco de Dados SQL, confira [Comparar os pools elásticos/bancos de dados individuais do Banco de Dados SQL do Azure e a Instância Gerenciada](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). | Sim, com um SSIS gerenciado no ambiente da fábrica de dados do Azure (AAD), onde os pacotes estão armazenados no SSISDB hospedada pela instância gerenciada e executado no Azure SSIS integração em tempo de execução (IV), consulte [criar IR do SSIS do Azure no ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). <br/><br/>Para comparar os recursos do SSIS na Instância Gerenciada e Banco de Dados SQL, confira [Comparar os pools elásticos/bancos de dados individuais do Banco de Dados SQL do Azure e a Instância Gerenciada](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). |
| [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) | Sim | Sim |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | Sim | Sim |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | Não - veja [Eventos estendidos](sql-database-xevent-db-diff-from-svr.md) | Sim |
| [Replicação do SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Somente assinante de replicação de instantâneo e transacional](sql-database-single-database-migrate.md) | Sim, em [versão prévia pública](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance) |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | Não – [consulte Power BI](https://docs.microsoft.com/power-bi/) | Não – [consulte Power BI](https://docs.microsoft.com/power-bi/) |
| [Procedimentos armazenados](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | Sim | Sim |
| [Funções armazenadas do sistema](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | Maioria - veja funções individuais | Sim – consulte [Diferenças entre procedimentos armazenados, funções e gatilhos](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Procedimentos armazenados do sistema](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Alguns - veja procedimentos armazenados individuais | Sim – consulte [Diferenças entre procedimentos armazenados, funções e gatilhos](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Tabelas do sistema](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Alguns - veja tabelas individuais | Sim – consulte [Diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Exibições do catálogo do sistema](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Alguns - veja exibições individuais | Sim – consulte [Diferenças do T-SQL](sql-database-managed-instance-transact-sql-information.md) |
| [Tabelas temporárias](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) | Tabelas locais e temporárias globais no escopo do banco de dados | Tabelas locais e temporárias globais no escopo da instância |
| [Tabelas temporais](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [Sim](sql-database-temporal-tables.md) | [Sim](sql-database-temporal-tables.md) |
|Detecção de ameaças|  [Sim](sql-database-threat-detection.md)|[Sim](sql-database-managed-instance-threat-detection.md)|
| [Sinalizadores de rastreamento](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | Não | Não |
| [Variáveis](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | Sim | Sim |
| [Transparent data encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | Sim – somente as camadas de serviço Comercialmente Crítica e de Uso Geral| [Sim](transparent-data-encryption-azure-sql.md) |
[Rede virtual](../virtual-network/virtual-networks-overview.md) | Parcial – consulte [Pontos de extremidade de VNet](sql-database-vnet-service-endpoint-rule-overview.md) | Sim, somente no modelo do Resource Manager |
| [Clustering de Failover do Windows Server](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | A [alta disponibilidade](sql-database-high-availability.md) é incluída em todos os bancos de dados. A recuperação de desastre é abordada em [Visão geral da continuidade de negócios com o banco de dados SQL do Azure](sql-database-business-continuity.md) | A [alta disponibilidade](sql-database-high-availability.md) é incluída em todos os bancos de dados. A recuperação de desastre é abordada em [Visão geral da continuidade de negócios com o banco de dados SQL do Azure](sql-database-business-continuity.md) |
| [Índices XML](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | Sim | Sim |

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre o serviço do Banco de Dados SQL do Azure, consulte [O que é o Banco de Dados SQL?](sql-database-technical-overview.md)
- Para obter informações sobre uma Instância Gerenciada, consulte [O que é uma Instância Gerenciada?](sql-database-managed-instance.md).
