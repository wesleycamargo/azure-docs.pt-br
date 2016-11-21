# Visão geral
## [O que é o Banco de Dados SQL?](sql-database-technical-overview.md)

## Recursos
### [Camadas de serviço](sql-database-service-tiers.md)
### [Unidades de Transação de Banco de Dados](sql-database-what-is-a-dtu.md)
### [Visão geral do parâmetro de comparação de DTU](sql-database-benchmark-overview.md)
### [Ferramentas de gerenciamento](sql-database-manage-overview.md)
## Considerações e limitações
### [Banco de Dados SQL versus SQL em uma VM](sql-database-paas-vs-sql-server-iaas.md)
### [Diferenças de T-SQL](sql-database-transact-sql-information.md)
### [Limites de recursos](sql-database-resource-limits.md)
### [Limitações gerais](sql-database-general-limitations.md)
### [Diretrizes de segurança](sql-database-security-guidelines.md)

## Benefícios
### [Aprende e adapta-se](sql-database-learn-and-adapt.md)
### [Escala de maneira dinâmica](sql-database-scale-on-the-fly.md)
### [Compila aplicativos de multilocatários](sql-database-build-multi-tenant-apps.md)
### [Resguarda e protege](sql-database-helps-secures-and-protects.md)
### [Trabalha em seu ambiente](sql-database-works-in-your-environment.md)

## Cenários

### Servidores, pools, bancos de dados e firewalls
#### [Quando usar um pool de banco de dados elástico](sql-database-elastic-pool-guidance.md)
#### [Pools de banco de dados elástico](sql-database-elastic-pool.md)
#### [Automação](sql-database-manage-automation.md)

### Bancos de dados expandidos
#### [Visão geral](sql-database-elastic-scale-introduction.md)
#### [Criar bancos de dados na nuvem escalonáveis](sql-database-elastic-database-client-library.md)
#### [Trabalhos entre bancos de dados](sql-database-elastic-jobs-overview.md)
#### [Glossário de ferramentas de banco de dados elástico](sql-database-elastic-scale-glossary.md)
#### [Perguntas frequentes](sql-database-elastic-scale-faq.md)

### Acesso e permissões
#### [Visão geral](sql-database-security.md)
#### [Central de Segurança do Azure para o Banco de Dados SQL do Azure](../security-center/security-center-sql-database.md?toc=%2fazure%2fsql-database%2ftoc.json)
#### [Central de Segurança do SQL](https://msdn.microsoft.com/library/azure/bb510589)

### Continuidade dos negócios
#### [Visão geral](sql-database-business-continuity.md)
#### [Backups de banco de dados](sql-database-automated-backups.md)
#### [Recuperação do banco de dados usando backups](sql-database-recovery-using-backups.md)
#### [Requisitos de autenticação para recuperação de desastre](sql-database-geo-replication-security-config.md)
#### [Cenários de design de continuidade dos negócios](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [Estratégias de recuperação de desastres com pools elásticos](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [Atualizações sem interrupção](sql-database-manage-application-rolling-upgrade.md)

### [Desenvolvimento de banco de dados](sql-database-develop-overview.md)
### [Migrar bancos de dados do SQL Server](sql-database-cloud-migrate.md)

## Implementações do Cliente
### [Software Daxko/CSI](sql-database-implementation-daxko.md)
### [GEP](sql-database-implementation-gep.md)
### [SnelStart](sql-database-implementation-snelstart.md)
### [Umbraco](sql-database-implementation-umbraco.md)


# Introdução
## [Inícios rápidos da solução](sql-database-solution-quick-starts.md)
## Criar um banco de dados SQL
### [Portal do Azure](sql-database-get-started.md)
### [C#](sql-database-get-started-csharp.md)
### [PowerShell](sql-database-get-started-powershell.md)
## Criar e gerenciar bancos de dados escalados horizontalmente
### [Escala elástica](sql-database-elastic-scale-get-started.md)
### [Trabalhos elásticos](sql-database-elastic-jobs-getting-started.md)
### [Relatórios entre bancos de dados](sql-database-elastic-query-getting-started.md)
### [Consultas entre bancos de dados](sql-database-elastic-query-getting-started-vertical.md)
## Monitorar e ajustar os bancos de dados
### [Visão Geral do Assistente do Banco de Dados SQL](sql-database-advisor.md)
### [Análises de carga de trabalho no portal do Azure](sql-database-performance.md)
## [Criar e gerenciar acesso e permissões](sql-database-get-started-security.md)
## [Otimização na memória](sql-database-in-memory.md)
## [Sincronização de dados](sql-database-get-started-sql-data-sync.md)
## [Perguntas frequentes sobre o Banco de Dados SQL](sql-database-faq.md)

# Como

## Criar e gerenciar
### Servidores e bancos de dados
#### [Bancos de dados únicos](sql-database-manage-portal.md)
#### [Portal do Azure](sql-database-get-started.md)
#### [C#](sql-database-get-started-csharp.md)
#### [PowerShell](sql-database-manage-powershell.md)
#### [SQL Server Management Studio](sql-database-manage-azure-ssms.md)
### Pools de banco de dados elástico
#### [Portal do Azure](sql-database-elastic-pool-create-portal.md)
#### [PowerShell](sql-database-elastic-pool-create-powershell.md)
#### [C#](sql-database-elastic-pool-create-csharp.md)
#### [T-SQL](sql-database-elastic-pool-manage-tsql.md)
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
### Trabalhos
#### [Instalação do serviço](sql-database-elastic-jobs-service-installation.md)
#### [Portal do Azure](sql-database-elastic-jobs-create-and-manage.md)
#### [PowerShell](sql-database-elastic-jobs-powershell.md)
#### [Atualizar biblioteca do cliente](sql-database-elastic-scale-upgrade-client-library.md)
### [Logons](sql-database-manage-logins.md)

## Desenvolver
### [Visão geral](https://msdn.microsoft.com/library/mt763826.aspx)
### [Bibliotecas de conectividade](sql-database-libraries.md)
### Cenários
#### [Aplicativos de SaaS de multilocatários](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### [Dados JSON](sql-database-json-features.md)
#### [Tabelas temporais](sql-database-temporal-tables.md)
#### [Políticas de retenção](sql-database-temporal-tables-retention-policy.md)
### Conectar um aplicativo
#### [.NET](sql-database-develop-dotnet-simple.md)
#### [Java](sql-database-develop-java-simple.md)
#### [Node.js](sql-database-develop-nodejs-simple.md)
#### [PHP](sql-database-develop-php-simple.md)
#### [Python](sql-database-develop-python-simple.md)
#### [Ruby](sql-database-develop-ruby-simple.md)
#### [Excel](sql-database-connect-excel.md)
#### [Visual Studio](sql-database-connect-query.md)

### [Criar Bancos de Dados](sql-database-get-started-powershell.md)
### Gerenciar pools elásticos
#### [PowerShell](sql-database-elastic-pool-manage-powershell.md)
#### [C#](sql-database-elastic-pool-manage-csharp.md)
### [Obter os valores necessários para autenticar um aplicativo](sql-database-client-id-keys.md)
### [Usar portas além da 1433 para ADO.NET 4.5](sql-database-develop-direct-route-ports-adonet-v12.md)
### [Trabalhar com mensagens de erro](sql-database-develop-error-messages.md)
### [Usar envio em lote](sql-database-use-batching-to-improve-performance.md)

### Referência
#### [.NET (conceitos)](https://msdn.microsoft.com/library/kb9s9ks0.aspx)
#### [.NET (Referência de API)](https://msdn.microsoft.com/library/system.data.sqlclient.aspx)
#### [Entity Framework](https://www.nuget.org/packages/EntityFramework/)
#### [SDK do Azure (download)](https://www.visualstudio.com/vs/azure-tools/)
#### [SDK do Azure (documentação)](../dotnet-sdk.md)
#### [Cmdlets de Gerenciamento de Serviço do PowerShell](https://msdn.microsoft.com/library/azure/dn546723.aspx)
#### REST
##### [REST (Gerenciamento de Recursos)](https://msdn.microsoft.com/library/azure/mt420159)
##### [REST (Gerenciamento de Serviço)](https://msdn.microsoft.com/library/azure/dn505719.aspx)

## Detectar ameaças
### [Detecção de ameaças](sql-database-threat-detection-get-started.md)
### [Firewall](sql-database-firewall-configure.md)

## Criptografar dados
### [Visão geral do Always Encrypted](sql-database-always-encrypted.md)
### [Cofre de Chaves do Azure Always Encrypted](sql-database-always-encrypted-azure-key-vault.md)
### [Transparent Data Encryption](https://msdn.microsoft.com/library/azure/dn948096)
### [Criptografia de coluna](https://msdn.microsoft.com/library/azure/ms179331)

## Mascarar de dados
### [Mascaramento de dados dinâmicos](sql-database-dynamic-data-masking-get-started-portal.md)
### [Clientes de nível inferior](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)

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
### [Bancos de dados únicos](sql-database-performance-guidance.md)
### [Análise de Desempenho de Consultas](sql-database-query-performance.md)
### [Advisor do Banco de Dados SQL](sql-database-advisor-portal.md)
### [Desempenho de banco de dados](sql-database-single-database-monitor.md)
### [DMVs](sql-database-monitoring-with-dmvs.md)
### [Níveis de compatibilidade](sql-database-compatibility-level-query-performance-130.md)
### [Auditoria de eventos](sql-database-auditing-get-started.md)
### [Contadores de desempenho do gerenciador de mapa de fragmentos](sql-database-elastic-database-perf-counters.md)
### [Dicas de ajuste de desempenho](sql-database-troubleshoot-performance.md)
### Alterar as camadas de serviços e os níveis de desempenho
#### [Portal do Azure](sql-database-scale-up.md)
#### [PowerShell](sql-database-scale-up-powershell.md)
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
## [PowerShell](/powershell/azureps-cmdlets-docs)
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




<!--HONumber=Nov16_HO3-->


