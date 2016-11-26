# Visão geral
## [O que é o Banco de Dados SQL?](sql-database-technical-overview.md)
### [Camadas de serviço](sql-database-service-tiers.md)
### [Unidades de Transação de Banco de Dados](sql-database-what-is-a-dtu.md)
### [Visão geral do parâmetro de comparação de DTU](sql-database-benchmark-overview.md)
### [Limites de recursos](sql-database-resource-limits.md)
### [Recursos](sql-database-features.md)
### [Perguntas frequentes sobre o Banco de Dados SQL](sql-database-faq.md)
## Benefícios
### [Aprende e adapta-se](sql-database-learn-and-adapt.md)
### [Escala de maneira dinâmica](sql-database-scale-on-the-fly.md)
### [Compila aplicativos de multilocatários](sql-database-build-multi-tenant-apps.md)
### [Resguarda e protege](sql-database-helps-secures-and-protects.md)
### [Trabalha em seu ambiente](sql-database-works-in-your-environment.md)
## Comparações
### [Banco de Dados SQL versus SQL em uma VM](sql-database-paas-vs-sql-server-iaas.md)
### [Diferenças de T-SQL](sql-database-transact-sql-information.md)
### [SQL versus NoSQL](../documentdb/documentdb-nosql-vs-sql.md)
## [Ferramentas do Banco de Dados SQL](sql-database-manage-overview.md)
## [Tutoriais do Banco de Dados SQL](sql-database-explore-tutorials.md)
## [Inícios rápidos da solução](sql-database-solution-quick-starts.md)
## Segurança
### [Central de Segurança do Azure para o Banco de Dados SQL do Azure](https://azure.microsoft.com/documentation/articles/security-center-sql-database/)
### [Central de Segurança do SQL](https://msdn.microsoft.com/library/azure/bb510589)
# Introdução
## Bancos de dados e servidores
### Bancos de dados únicos
#### Aprenda
##### [Tutorial do portal do Azure: criar um banco de dados do SQL Azure usando o portal do Azure](sql-database-get-started.md)
#### Certo
##### [Tutorial do portal do Azure: criar um banco de dados do SQL Azure usando o portal do Azure](sql-database-get-started.md)
##### [Tutorial do portal do Azure: criar um banco de dados do SQL Azure usando o PowerShell](sql-database-get-started-powershell.md)
##### [Tutorial do portal do Azure: criar um banco de dados do SQL Azure usando o C#](sql-database-get-started-csharp.md)
### Pools de banco de dados elástico
#### Aprenda
##### [Pools de banco de dados elástico](sql-database-elastic-pool.md)
##### [Quando usar um pool de banco de dados elástico](sql-database-elastic-pool-guidance.md)
##### [Preços do pool elástico](sql-database-elastic-pool-price.md)
#### Certo
##### [Criar usando o portal do Azure](sql-database-elastic-pool-create-portal.md)
##### [Criar usando o PowerShell](sql-database-elastic-pool-create-powershell.md)
##### [Criar usando o C#](sql-database-elastic-pool-create-csharp.md)
### Expansão
#### Aprenda
##### [Expansão](sql-database-elastic-scale-introduction.md)
##### [Escala elástica](sql-database-elastic-scale-get-started.md)
##### [Criando bancos de dados de nuvem escalonáveis](sql-database-elastic-database-client-library.md)
##### [Trabalhos entre banco de dados](sql-database-elastic-jobs-overview.md)
##### [Perguntas Frequentes das ferramentas elásticas](sql-database-elastic-scale-faq.md)
##### [Glossário de ferramentas de banco de dados elástico](sql-database-elastic-scale-glossary.md)
##### [Mover dados entre bancos de dados na nuvem escalados horizontalmente](sql-database-elastic-scale-overview-split-and-merge.md)
#### Certo
##### [Trabalhos elásticos](sql-database-elastic-jobs-getting-started.md)
##### [Relatórios entre bancos de dados](sql-database-elastic-query-getting-started.md)
##### [Consultas entre bancos de dados](sql-database-elastic-query-getting-started-vertical.md)
##### [Desinstalar trabalhos elásticos](sql-database-elastic-jobs-uninstall.md)
## Migrar e mover dados
### Aprenda
#### [Migrar um banco de dados](sql-database-cloud-migrate.md)
#### [Replicação transacional](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
#### [Sincronização de dados](sql-database-get-started-sql-data-sync.md)
#### [Copiar um banco de dados SQL](sql-database-copy.md)
### Certo
#### Determinar compatibilidade do banco de dados
##### [Utilitário do Pacote SQL](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)
#### Corrigir problemas de compatibilidade do banco de dados
##### [Ferramentas de dados do SQL Server](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
##### [SQL Azure Migration Wizard](sql-database-cloud-migrate-fix-compatibility-issues.md)
##### [Assistente de Migração do SQL Server Management Studio](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
#### Copiar um banco de dados
##### [Copiar usando o portal do Azure](sql-database-copy-portal.md)
##### [Copiar usando o PowerShell](sql-database-copy-powershell.md)
##### [Copiar usando o T-SQL](sql-database-copy-transact-sql.md)
#### Exportar banco de dados para um arquivo BACPAC
##### [portal do Azure](sql-database-export.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
##### [Utilitário do Pacote SQL](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
##### [PowerShell](sql-database-export-powershell.md)
#### Importar banco de dados de um arquivo BACPAC
##### [Portal do Azure](sql-database-import.md)
##### [PowerShell](sql-database-import-powershell.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
##### [Utilitário do Pacote SQL](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
#### [Carregar do arquivo CSV usando BCP](sql-database-load-from-csv-with-bcp.md)
## Autenticar e conceder acesso
### Aprenda
#### [Visão geral](sql-database-security.md)
#### [Diretrizes de segurança](sql-database-security-guidelines.md)
#### [Gerenciar logons](sql-database-manage-logins.md)
### Certo
### [Tutorial: autenticação do SQL e acesso](sql-database-get-started-security.md)
## Resguardar e proteger dados
### Aprenda
#### Auditoria
##### [Audit](sql-database-auditing-get-started.md)
##### [Suporte a clientes de nível inferior e mudanças do ponto de extremidade do IP para auditoria](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)
#### [Detecção de ameaças](sql-database-threat-detection-get-started.md)
#### Criptografar dados
##### [Cofre de chaves do Azure](sql-database-always-encrypted-azure-key-vault.md)
##### [Repositório de certificados do Windows](sql-database-always-encrypted.md)
##### [Transparent Data Encryption](https://msdn.microsoft.com/library/azure/dn948096)
##### [Criptografia de coluna](https://msdn.microsoft.com/library/azure/ms179331)
#### Mascarar de dados
##### Mascaramento de dados dinâmicos
###### [Portal do Azure](sql-database-dynamic-data-masking-get-started.md)
### Certo
#### [Mascaramento de dados dinâmico usando o portal do Azure](sql-database-dynamic-data-masking-get-started.md)
## Continuidade dos negócios
### Aprenda
#### [Visão geral](sql-database-business-continuity.md)
#### [Backups de banco de dados](sql-database-automated-backups.md)
#### [Retenção de longo prazo](sql-database-long-term-retention.md) 
#### [Recuperação do banco de dados usando backups](sql-database-recovery-using-backups.md)
#### [Requisitos de autenticação para recuperação de desastre](sql-database-geo-replication-security-config.md)
#### [Cenários de design de continuidade dos negócios](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [Estratégias de recuperação de desastres com pools elásticos](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [Atualizações sem interrupção](sql-database-manage-application-rolling-upgrade.md)
### Certo
#### Restaurar banco de dados excluído
##### [Portal do Azure](sql-database-restore-deleted-database-portal.md)
##### [PowerShell](sql-database-restore-deleted-database-powershell.md)
#### Restauração pontual
##### [Portal do Azure](sql-database-point-in-time-restore-portal.md)
##### [PowerShell](sql-database-point-in-time-restore-powershell.md)
#### Bancos de dados de restauração geográfica
##### [Portal do Azure](sql-database-geo-restore-portal.md)
##### [PowerShell](sql-database-geo-restore-powershell.md)
#### [Tabela única](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
#### [Recuperar-se de uma paralisação do data center](sql-database-disaster-recovery.md)
#### [Executar treinamentos de recuperação de desastre](sql-database-disaster-recovery-drills.md)
### Replicar bancos de dados
#### [Visão geral da Replicação Geográfica ativa](sql-database-geo-replication-overview.md)
#### Configurar Replicação Geográfica Ativa
##### [Portal do Azure](sql-database-geo-replication-portal.md)
##### [PowerShell](sql-database-geo-replication-powershell.md)
##### [T-SQL](sql-database-geo-replication-transact-sql.md)
#### Failover com Replicação Geográfica Ativa
##### [Portal do Azure](sql-database-geo-replication-failover-portal.md)
##### [PowerShell](sql-database-geo-replication-failover-powershell.md)
##### [T-SQL](sql-database-geo-replication-failover-transact-sql.md)
## Desenvolvimento de aplicativos
### Aprenda
#### [Visão geral do desenvolvimento de aplicativos do banco de dados](sql-database-develop-overview.md)
#### [Bibliotecas de conectividade](sql-database-libraries.md)
#### [Aplicativos de SaaS de multilocatários](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### [Usar portas além da 1433 para ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md)
#### [Obter os valores necessários para autenticar um aplicativo](sql-database-client-id-keys.md)
### Certo
#### Conectar um aplicativo
##### [.NET](sql-database-develop-dotnet-simple.md)
##### [C e C++](sql-database-develop-cplusplus-simple.md)
##### [Java](sql-database-develop-java-simple.md)
##### [Node.js](sql-database-develop-nodejs-simple.md)
##### [PHP](sql-database-develop-php-simple.md)
##### [Python](sql-database-develop-python-simple.md)
##### [Ruby](sql-database-develop-ruby-simple.md)
##### [Excel](sql-database-connect-excel.md)
#### [Conectar com Visual Studio](sql-database-connect-query.md)
#### [Compilar um aplicativo cliente](https://www.microsoft.com/sql-server/developer-get-started)
#### [Trabalhar com mensagens de erro](sql-database-develop-error-messages.md)
### Implementations de cliente do software como um serviço
#### [Software Daxko/CSI](sql-database-implementation-daxko.md)
#### [GEP](sql-database-implementation-gep.md)
#### [SnelStart](sql-database-implementation-snelstart.md)
#### [Umbraco](sql-database-implementation-umbraco.md)
## Desenvolvimento de bancos de dados
### Aprenda
#### Tabelas temporais
##### [Tabelas temporais](sql-database-temporal-tables.md)
##### [Políticas de retenção](sql-database-temporal-tables-retention-policy.md)
#### [Dados JSON](sql-database-json-features.md)
#### [Na Memória](sql-database-in-memory.md)
### Certo
#### [Desenvolvimento do SQL Server](https://msdn.microsoft.com/library/ms179422.aspx)
## Monitoramento e Ajuste
### Aprenda
#### [Bancos de dados únicos](sql-database-single-database-monitor.md)
#### [Visão Geral do Assistente do Banco de Dados SQL](sql-database-advisor.md)
#### [Diretrizes para banco de dados individual](sql-database-performance-guidance.md)
#### [Análises de carga de trabalho no portal do Azure](sql-database-performance.md)
#### [Usar envio em lote](sql-database-use-batching-to-improve-performance.md)
## Banco de Dados SQL V11
### [Última edição da Web e comercial](sql-database-web-business-sunset-faq.md)
### [Supervisor da camada de serviço](sql-database-service-tier-advisor.md)
### [Ferramenta de avaliação do pool elástico](sql-database-elastic-pool-database-assessment-powershell.md)
### [Atualizar para V12](sql-database-v12-plan-prepare-upgrade.md)
#### [Atualizar usando o portal do Azure](sql-database-upgrade-server-portal.md)
#### [Atualizar usando o PowerShell](sql-database-upgrade-server-powershell.md)
#### [Recomendações do tipo de preços](sql-database-service-tier-advisor.md)
# Como
## Criar e gerenciar
### Servidores e bancos de dados
#### [Bancos de dados únicos](sql-database-manage-portal.md)
#### [Portal do Azure](sql-database-get-started.md)
#### [C#](sql-database-get-started-csharp.md)
#### [PowerShell](sql-database-manage-powershell.md)
#### [SQL Server Management Studio](sql-database-manage-azure-ssms.md)
### Pools de banco de dados elástico
#### [Criar usando o portal do Azure](sql-database-elastic-pool-create-portal.md)
#### [Criar usando o PowerShell](sql-database-elastic-pool-create-powershell.md)
#### [Criar usando o C#](sql-database-elastic-pool-create-csharp.md)
#### [Gerenciar usando o portal do Azure](sql-database-elastic-pool-manage-portal.md)
#### [Gerenciar usando o PowerShell](sql-database-elastic-pool-manage-powershell.md)
#### [Gerenciar usando o C#](sql-database-elastic-pool-manage-csharp.md)
#### [Gerenciar usando o T-SQL](sql-database-elastic-pool-manage-tsql.md)
### Bancos de dados fragmentados
#### [Usar gerenciador de mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md)
#### [Dividir e mesclar configuração de segurança](sql-database-elastic-scale-split-merge-security-configuration.md)
#### [Como trabalhar com Dapper](sql-database-elastic-scale-working-with-dapper.md)
#### [Usar o Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [Roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md)
#### [Segurança em nível de linha de multilocatários](sql-database-elastic-tools-multi-tenant-row-level-security.md)
#### [Gerenciar credenciais](sql-database-elastic-scale-manage-credentials.md)
#### [Implantar um serviço de divisão e mesclagem](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
#### [Adicionar um fragmento](sql-database-elastic-scale-add-a-shard.md)
#### [Usando a classe RecoveryManager para corrigir problemas do mapa de fragmentos](sql-database-elastic-database-recovery-manager.md)
###  Autenticação
#### [Autenticação do Azure AD](sql-database-aad-authentication.md)
#### [Autenticação Multifator](sql-database-ssms-mfa-authentication.md)
### Regras de firewall
#### [Portal do Azure](sql-database-configure-firewall-settings.md)
#### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
#### [API REST](sql-database-configure-firewall-settings-rest.md)
#### [T-SQL](sql-database-configure-firewall-settings-tsql.md)
### Trabalhos e automação
#### [Instalação do serviço](sql-database-elastic-jobs-service-installation.md)
#### [Criar e gerenciar trabalhos elásticos no portal do Azure](sql-database-elastic-jobs-create-and-manage.md)
#### [Criar e gerenciar trabalhos elásticos usando o PowerShell](sql-database-elastic-jobs-powershell.md)
#### [Atualizar biblioteca do cliente](sql-database-elastic-scale-upgrade-client-library.md)
#### [Gerenciando Bancos de Dados SQL usando o serviço de Automação do Azure](sql-database-manage-automation.md)
### [Firewall](sql-database-firewall-configure.md)
## Criptografar dados
### [Visão geral do Always Encrypted](sql-database-always-encrypted.md)
### [Transparent Data Encryption](https://msdn.microsoft.com/library/azure/dn948096)
### [Criptografia de coluna](https://msdn.microsoft.com/library/azure/ms179331)
## Migrar
### Determinar compatibilidade
#### [Utilitário do Pacote SQL](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)
### Corrigir problemas de compatibilidade
#### [Ferramentas de dados do SQL Server](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
#### [SQL Azure Migration Wizard](sql-database-cloud-migrate-fix-compatibility-issues.md)
### [Usar o Assistente de Migração do SQL Server Management Studio](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
### [Usar replicação transacional](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
### [Migrar bancos de dados escalados horizontalmente existentes para escalar horizontalmente](sql-database-elastic-convert-to-use-elastic-tools.md)
## Monitorar e ajustar
### [Análise de Desempenho de Consultas](sql-database-query-performance.md)
### [Advisor do Banco de Dados SQL](sql-database-advisor-portal.md)
### [DMVs](sql-database-monitoring-with-dmvs.md)
### [Níveis de compatibilidade](sql-database-compatibility-level-query-performance-130.md)
### [Contadores de desempenho do gerenciador de mapa de fragmentos](sql-database-elastic-database-perf-counters.md)
### [Dicas de ajuste de desempenho](sql-database-troubleshoot-performance.md)
### Alterar as camadas de serviços e os níveis de desempenho
#### [Usando o portal do Azure](sql-database-scale-up.md)
#### [Usar o PowerShell](sql-database-scale-up-powershell.md)
### [Criar alertas](sql-database-insights-alerts-portal.md)
### OLTP Na Memória
#### [Adotar OLTP na memória](sql-database-in-memory-oltp-migration.md)
#### [Monitorar o armazenamento OLTP na memória](sql-database-in-memory-oltp-monitoring.md)
### Repositório de Consultas
#### [Monitoramento de desempenho usando o Repositório de Consultas](https://msdn.microsoft.com/library/dn817826.aspx)
#### [Cenários de uso do Repositório de Consultas](https://msdn.microsoft.com/library/mt614796.aspx)
#### [Operação do Repositório de Consultas](sql-database-operate-query-store.md)
### Eventos estendidos
#### [Eventos estendidos](sql-database-xevent-db-diff-from-svr.md)
#### [Código de destino do arquivo de evento](sql-database-xevent-code-event-file.md)
#### [Código de destino do buffer de anel](sql-database-xevent-code-ring-buffer.md)
## Mover dados
### [Copiar um banco de dados SQL](sql-database-copy.md)
#### [Portal do Azure](sql-database-copy-portal.md)
#### [PowerShell](sql-database-copy-powershell.md)
#### [T-SQL](sql-database-copy-transact-sql.md)
### Exportar banco de dados para um arquivo BACPAC
#### [portal do Azure](sql-database-export.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [Utilitário do Pacote SQL](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
#### [PowerShell](sql-database-export-powershell.md)
### Importar banco de dados de um arquivo BACPAC
#### [Portal do Azure](sql-database-import.md)
#### [PowerShell](sql-database-import-powershell.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
#### [Utilitário do Pacote SQL](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
### [Carregar do arquivo CSV usando BCP](sql-database-load-from-csv-with-bcp.md)
### [Mover dados entre bancos de dados na nuvem escalados horizontalmente](sql-database-elastic-scale-overview-split-and-merge.md)
## Consultar
### [SQL Server Management Studio](sql-database-connect-query-ssms.md)
### [Consulta Muiltishard](sql-database-elastic-scale-multishard-querying.md)
### Consultas entre bancos de dados
#### [Visão geral](sql-database-elastic-query-overview.md)
#### [Consulta entre bancos de dados com diferentes esquemas](sql-database-elastic-query-vertical-partitioning.md)
#### [Relatório entre bancos de dados](sql-database-elastic-query-horizontal-partitioning.md)
#### [Transações distribuídas entre bancos de dados na nuvem](sql-database-elastic-transactions-overview.md)
## Restaurar
### Restaurar banco de dados excluído
#### [Portal do Azure](sql-database-restore-deleted-database-portal.md)
#### [PowerShell](sql-database-restore-deleted-database-powershell.md)
### Recuperação pontual
#### [Portal do Azure](sql-database-point-in-time-restore-portal.md)
#### [PowerShell](sql-database-point-in-time-restore-powershell.md)
### Restauração geográfica
#### [Portal do Azure](sql-database-geo-restore-portal.md)
#### [PowerShell](sql-database-geo-restore-powershell.md)
#### [Tabela única](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
### [Recuperar-se de uma paralisação do data center](sql-database-disaster-recovery.md)
### [Executar treinamentos de recuperação de desastre](sql-database-disaster-recovery-drills.md)
## Replicar
### [Visão geral da Replicação Geográfica ativa](sql-database-geo-replication-overview.md)
### Configurar
#### [PowerShell](sql-database-geo-replication-powershell.md)
#### [T-SQL](sql-database-geo-replication-transact-sql.md)
### Failover
#### [portal do Azure](sql-database-geo-replication-failover-portal.md)
#### [PowerShell](sql-database-geo-replication-failover-powershell.md)
#### [T-SQL](sql-database-geo-replication-failover-transact-sql.md)

## Solucionar problemas
### [Problemas de conexão](sql-database-troubleshoot-common-connection-issues.md)
### [Erro de conexão transitório](sql-database-troubleshoot-connection.md)
### [Diagnosticar e evitar](sql-database-connectivity-issues.md)
### [Permissões](sql-database-troubleshoot-permissions.md)
### [Movimentação de bancos de dados](sql-database-troubleshoot-moving-data.md)


# Referência
## [PowerShell](/powershell/azureps-cmdlets-docs/)
## [Implantação clássica do PowerShell](/powershell/servicemanagement/)
## [Java](/java/api/)
## [.NET](/dotnet/api/)
## [T-SQL](https://msdn.microsoft.com/library/azure/bb510741.aspx)
## [Cmdlets do Banco de Dados SQL do Azure](/powershell/resourcemanager/AzureRM.Sql/v1.0.12/AzureRM.Sql)
## [Cmdlets do SQL Server](https://msdn.microsoft.com/library/mt740629.aspx)
## [REST](/rest/api/sql/)

## Biblioteca de Gerenciamento do Banco de Dados SQL
### [Referência de Biblioteca de Gerenciamento de Banco de Dados SQL](https://msdn.microsoft.com/library/azure/mt349017.aspx)
### [Obtenha o pacote de Biblioteca de Gerenciamento de Banco de Dados SQL](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
## [Drivers do SQL Server](https://msdn.microsoft.com/library/mt654049.aspx)
### [ADO.NET](https://msdn.microsoft.com/library/mt657768.aspx)
### [JDBC](https://msdn.microsoft.com/library/mt484311.aspx)
### [Node.js](https://msdn.microsoft.com/library/mt652093.aspx)
### [ODBC](https://msdn.microsoft.com/library/mt654048.aspx)
### [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
### [Python](https://msdn.microsoft.com/library/mt652092.aspx)
### [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)

# Recursos
## [Preços](https://azure.microsoft.com/pricing/details/sql-database/)
## [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/sql-azure)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=sql-database)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?service=sql-database)
## [Ferramentas do SQL Server](https://msdn.microsoft.com/library/mt238365.aspx)
## [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
## [SSDT (Ferramentas de Dados do SQL Server)](https://msdn.microsoft.com/library/mt204009.aspx)
## [BCP](https://msdn.microsoft.com/library/ms162802.aspx)
## [SQLCMD](https://msdn.microsoft.com/library/ms162773.aspx)
## [SqlPackage](https://msdn.microsoft.com/hh550080.aspx)




<!--HONumber=Nov16_HO4-->


