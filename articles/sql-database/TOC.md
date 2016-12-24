# Visão geral
## [O que é o Banco de Dados SQL?](sql-database-technical-overview.md)
### [Camadas de serviço](sql-database-service-tiers.md)
### [DTUs e eDTUs](sql-database-what-is-a-dtu.md)
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
### Aprenda
#### [Servidores](sql-database-server-overview.md)
#### [Bancos de dados únicos](sql-database-overview.md)
#### [Vários bancos de dados](sql-database-elastic-scale-introduction.md)
##### Mapeamento de locatários
###### [Biblioteca de cliente elástico](sql-database-elastic-database-client-library.md)
###### [Gerenciador de mapa de fragmentos](sql-database-elastic-scale-shard-map-management.md)
###### [Roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md)
###### [Gerenciar credenciais](sql-database-elastic-scale-manage-credentials.md)
###### [Consulta Multishard](sql-database-elastic-scale-multishard-querying.md)
##### Pools elásticos (grupos de recursos)
###### [O que é um pool elástico?](sql-database-elastic-pool.md)
###### [Quando usar um pool elástico](sql-database-elastic-pool-guidance.md)
###### [Preços do pool elástico](sql-database-elastic-pool-price.md)
##### Bancos de dados fragmentados
###### [Glossário de ferramentas elásticas](sql-database-elastic-scale-glossary.md)
###### [Movimentação de dados entre fragmentos](sql-database-elastic-scale-overview-split-and-merge.md)
###### [Perguntas Frequentes das ferramentas elásticas](sql-database-elastic-scale-faq.md)
##### Consulta elástica (consultas entre bancos de dados)
###### [O que é uma consulta elástica?](sql-database-elastic-query-overview.md)
##### Transações elásticas (transações distribuídas)
###### [Transações entre bancos de dados na nuvem](sql-database-elastic-transactions-overview.md)
##### Trabalhos elásticos (trabalhos entre bancos de dados)
###### [O que é um trabalho elástico?](sql-database-elastic-jobs-overview.md)
#### [Uso do Azure RemoteApp para se conectar a um Banco de Dados SQL](sql-database-ssms-remoteapp.md)
#### [Gerenciando Bancos de Dados SQL usando o serviço de Automação do Azure](sql-database-manage-automation.md)
### Certo
#### [Criar um banco de dados usando o portal do Azure](sql-database-get-started.md)
#### [Criar um banco de dados único usando o PowerShell](sql-database-get-started-powershell.md)
#### [Criar um banco de dados único usando C#](sql-database-get-started-csharp.md)
#### [Criar aplicativo fragmentado](sql-database-elastic-scale-get-started.md)
#### [Mover dados entre fragmentos](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
#### [Introdução aos trabalhos elásticos](sql-database-elastic-jobs-getting-started.md)
#### [Introdução às consultas elásticas](sql-database-elastic-query-getting-started-vertical.md)
#### [Criar relatórios usando a consulta elástica](sql-database-elastic-query-getting-started.md)
#### [Consultar bancos de dados com esquemas diferentes](sql-database-elastic-query-vertical-partitioning.md)
#### [Emissão de relatórios em bancos de dados expandidos](sql-database-elastic-query-horizontal-partitioning.md)
## Migrar e mover dados
### Aprenda
#### [Migrar um banco de dados](sql-database-cloud-migrate.md)
#### [Replicação transacional](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
#### [Sincronização de dados](sql-database-get-started-sql-data-sync.md)
#### [Copiar um banco de dados SQL](sql-database-copy.md)
## Regras, autenticação e autorização de firewall
### Aprenda
#### [Visão geral](sql-database-security.md)
#### [Diretrizes de segurança](sql-database-security-guidelines.md)
#### [Firewall](sql-database-firewall-configure.md)
#### [Gerenciar logons](sql-database-manage-logins.md)
### Certo
#### [Autenticação e autorização de SQL](sql-database-get-started-security.md)
## Resguardar e proteger dados
### Aprenda
#### Auditoria
##### [Audit](sql-database-auditing-get-started.md)
##### [Suporte a clientes de nível inferior e mudanças do ponto de extremidade do IP para auditoria](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)
#### [Detecção de ameaças](sql-database-threat-detection-get-started.md)
#### Criptografar dados
##### [Cofre de chaves do Azure](sql-database-always-encrypted-azure-key-vault.md)
##### [Transparent Data Encryption](https://msdn.microsoft.com/library/azure/dn948096)
##### [Criptografia de coluna](https://msdn.microsoft.com/library/azure/ms179331)
#### Mascarar de dados
##### Mascaramento de dados dinâmicos
###### [Portal do Azure](sql-database-dynamic-data-masking-get-started.md)
### Certo
#### [Mascaramento de dados dinâmico usando o portal do Azure](sql-database-dynamic-data-masking-get-started.md)
##### [Sempre Criptografado usando o repositório de certificados do Windows](sql-database-always-encrypted.md)
## Continuidade dos negócios
### Aprenda
#### [Visão geral](sql-database-business-continuity.md)
#### [Backups de banco de dados](sql-database-automated-backups.md)
#### [Retenção de longo prazo](sql-database-long-term-retention.md)
#### [Recuperação do banco de dados usando backups](sql-database-recovery-using-backups.md)
#### [Recuperar uma tabela única](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
#### [Recuperar-se de uma paralisação do data center](sql-database-disaster-recovery.md)
#### [Requisitos de autenticação para recuperação de desastre](sql-database-geo-replication-security-config.md)
#### [Cenários de design de continuidade dos negócios](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [Estratégias de recuperação de desastres com pools elásticos](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [Atualizações sem interrupção](sql-database-manage-application-rolling-upgrade.md)
#### [Executar treinamentos de recuperação de desastre](sql-database-disaster-recovery-drills.md)
#### [Visão geral da Replicação Geográfica ativa](sql-database-geo-replication-overview.md)
### Certo
#### [Introdução ao backup e à restauração do Banco de Dados SQL](sql-database-get-started-backup-recovery.md)
## Desenvolvimento de aplicativos
### Aprenda
#### [Visão geral do desenvolvimento de aplicativos do banco de dados](sql-database-develop-overview.md)
#### [Bibliotecas de conectividade](sql-database-libraries.md)
#### [Aplicativos de SaaS de multilocatários](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### [Dimensionamento de aplicativos de SaaS multilocatários com segurança em nível de linha](sql-database-elastic-tools-multi-tenant-row-level-security.md)
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
#### [Usar o Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [Usar a biblioteca de cliente com o Dapper](sql-database-elastic-scale-working-with-dapper.md)
### Implementações do cliente
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
#### [Otimização na memória](sql-database-in-memory.md)
### Certo
#### [Desenvolvimento do SQL Server](https://msdn.microsoft.com/library/ms179422.aspx)
#### [Adotar OLTP na memória](sql-database-in-memory-oltp-migration.md)
## Monitoramento e Ajuste
### Aprenda
#### [Bancos de dados únicos](sql-database-single-database-monitor.md)
#### [Visão Geral do Assistente do Banco de Dados SQL](sql-database-advisor.md)
#### [Diretrizes para banco de dados individual](sql-database-performance-guidance.md)
#### [Análises de carga de trabalho no portal do Azure](sql-database-performance.md)
#### [Usar envio em lote](sql-database-use-batching-to-improve-performance.md)
#### [Eventos estendidos](sql-database-xevent-db-diff-from-svr.md)
## Banco de Dados SQL V11
### [Última edição da Web e comercial](sql-database-web-business-sunset-faq.md)
### [Supervisor da camada de serviço](sql-database-service-tier-advisor.md)
### [Ferramenta de avaliação do pool elástico](sql-database-elastic-pool-database-assessment-powershell.md)
### [Atualizar para V12](sql-database-v12-plan-prepare-upgrade.md)
#### [Atualizar usando o portal do Azure](sql-database-upgrade-server-portal.md)
#### [Atualizar usando o PowerShell](sql-database-upgrade-server-powershell.md)
# Como
## Criar e gerenciar
### [Gerenciar um Banco de Dados SQL do Azure usando o portal do Azure](sql-database-manage-portal.md)
### [Gerenciar um Banco de Dados SQL do Azure usando o PowerShell](sql-database-manage-powershell.md)
### [Gerenciar um Banco de Dados SQL usando SSMS](sql-database-manage-azure-ssms.md)
### Servidores
#### [Criar servidores](sql-database-create-servers.md)
#### [Exibir ou atualizar as configurações do servidor](sql-database-view-update-server-settings.md)
### Bancos de dados únicos
#### [Criar bancos de dados únicos](sql-database-create-databases.md)
#### [Exibir ou atualizar as configurações de banco de dados](sql-database-view-update-database-settings.md)
### Regras de firewall
#### [Criar regras de firewall usando o portal do Azure](sql-database-configure-firewall-settings.md)
#### [Criar regras de firewall usando o PowerShell](sql-database-configure-firewall-settings-powershell.md)
#### [Criar regras de firewall usando a API REST](sql-database-configure-firewall-settings-rest.md)
#### [Criar regras de firewall usando T-SQL](sql-database-configure-firewall-settings-tsql.md)
### Vários bancos de dados
#### [Atualizar a biblioteca de cliente em aplicativos cliente](sql-database-elastic-scale-upgrade-client-library.md)
#### Bancos de dados fragmentados
##### [Configuração de segurança](sql-database-elastic-scale-split-merge-security-configuration.md)
##### [Adicionar um fragmento](sql-database-elastic-scale-add-a-shard.md)
##### [Corrigir problemas de mapa de fragmento](sql-database-elastic-database-recovery-manager.md)
##### [Migrar bancos de dados expandidos existentes para bancos de dados fragmentados](sql-database-elastic-convert-to-use-elastic-tools.md)
##### [Ciar contadores de desempenho do gerenciador de mapa de fragmentos](sql-database-elastic-database-perf-counters.md)
#### Trabalhos elásticos
##### [Como instalar o serviço de trabalhos elásticos?](sql-database-elastic-jobs-service-installation.md)
##### [Criar e gerenciar trabalhos elásticos usando o PowerShell](sql-database-elastic-jobs-powershell.md) 
##### [Criar e gerenciar trabalhos elásticos usando o portal do Azure](sql-database-elastic-jobs-create-and-manage.md)
##### [Como eu posso desinstalar trabalhos elásticos?](sql-database-elastic-jobs-uninstall.md)
#### Pools elásticos
##### [Criar usando o portal do Azure](sql-database-elastic-pool-create-portal.md)
##### [Criar usando o PowerShell](sql-database-elastic-pool-create-powershell.md)
##### [Criar usando o C#](sql-database-elastic-pool-create-csharp.md)
##### [Gerenciar usando o portal do Azure](sql-database-elastic-pool-manage-portal.md)
##### [Gerenciar usando o PowerShell](sql-database-elastic-pool-manage-powershell.md)
##### [Gerenciar usando o C#](sql-database-elastic-pool-manage-csharp.md)
##### [Gerenciar usando o T-SQL](sql-database-elastic-pool-manage-tsql.md)
##  Autenticar e autorizar
### [Autenticação do Azure AD](sql-database-aad-authentication.md)
### [Autenticação Multifator](sql-database-ssms-mfa-authentication.md)
## Criptografar dados
### [Transparent Data Encryption](https://msdn.microsoft.com/library/azure/dn948096)
### [Criptografia de coluna](https://msdn.microsoft.com/library/azure/ms179331)
## Migrar bancos de dados
### Determinar compatibilidade
#### [Determinar a compatibilidade com o utilitário Pacote SQL](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
#### [Determinar a compatibilidade usando o SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
### Corrigir problemas de compatibilidade
#### [Corrigir problemas de compatibilidade usando o SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
#### [Corrigir problemas de compatibilidade usando o SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
#### [Corrigir problemas de compatibilidade usando SMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
### [Migração usando o Assistente de Migração do SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
## Monitorar e ajustar
### [Análise de Desempenho de Consultas](sql-database-query-performance.md)
### [Advisor do Banco de Dados SQL](sql-database-advisor-portal.md)
### [DMVs](sql-database-monitoring-with-dmvs.md)
### [Níveis de compatibilidade](sql-database-compatibility-level-query-performance-130.md)
### [Dicas de ajuste de desempenho](sql-database-troubleshoot-performance.md)
### Alterar as camadas de serviços e os níveis de desempenho
#### [Alterar os níveis de serviço usando o portal do Azure](sql-database-scale-up.md)
#### [Alterar os níveis de serviço usando o PowerShell](sql-database-scale-up-powershell.md)
### [Criar alertas](sql-database-insights-alerts-portal.md)
#### [Monitorar o armazenamento OLTP na memória](sql-database-in-memory-oltp-monitoring.md)
### Repositório de Consultas
#### [Monitoramento de desempenho usando o Repositório de Consultas](https://msdn.microsoft.com/library/dn817826.aspx)
#### [Cenários de uso do Repositório de Consultas](https://msdn.microsoft.com/library/mt614796.aspx)
#### [Operação do Repositório de Consultas](sql-database-operate-query-store.md)
### Eventos estendidos
#### [Código de destino do arquivo de evento](sql-database-xevent-code-event-file.md)
#### [Código de destino do buffer de anel](sql-database-xevent-code-ring-buffer.md)
## Mover dados
### Copiar um Banco de Dados SQL
#### [Copiar usando o portal do Azure](sql-database-copy-portal.md)
#### [Copiar usando o PowerShell](sql-database-copy-powershell.md)
#### [Copiar usando o T-SQL](sql-database-copy-transact-sql.md)
### Exportar banco de dados para um arquivo BACPAC
#### [Exportar usando o portal do Azure](sql-database-export.md)
#### [Exportar usando o SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [Exportar usando o utilitário Pacote SQL](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
#### [Exportar usando o PowerShell](sql-database-export-powershell.md)
### Importar banco de dados de um arquivo BACPAC
#### [Importar usando o portal do Azure](sql-database-import.md)
#### [Importar usando o PowerShell](sql-database-import-powershell.md)
#### [Importar usando o SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
#### [Importar usando o utilitário Pacote SQL](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
### [Carregar do arquivo CSV usando BCP](sql-database-load-from-csv-with-bcp.md)
## Consultar
### [Consultar usando o SSMS](sql-database-connect-query-ssms.md)
## Backup e restauração
### Retenção de backup de longo prazo
#### [Configurar retenção de backup de longo prazo](sql-database-configure-long-term-retention.md)
#### [Exibir backups em um cofre dos Serviços de Recuperação](sql-database-view-backups-in-vault.md)
#### [Restaurar desde a retenção de backup de longo prazo](sql-database-restore-from-long-term-retention.md)
### Restaurar banco de dados excluído
#### [Restauração excluída usando o portal do Azure](sql-database-restore-deleted-database-portal.md)
#### [Restauração excluída usando o PowerShell](sql-database-restore-deleted-database-powershell.md)
### Recuperação pontual
#### [Restaurar para um ponto no tempo](sql-database-point-in-time-restore.md)
#### [Exibir ponto de restauração mais antigo](sql-database-view-oldest-restore-point.md)
### Restauração geográfica
#### [Restauração geográfica usando o portal do Azure](sql-database-geo-restore-portal.md)
#### [Restauração geográfica usando o PowerShell](sql-database-geo-restore-powershell.md)
## Replicação geográfica ativa
### [Configurar usando o portal do Azure](sql-database-geo-replication-portal.md)
### [Configurar usando o PowerShell](sql-database-geo-replication-powershell.md)
### [Configurar usando o T-SQL](sql-database-geo-replication-transact-sql.md)
### [Failover usando o portal do Azure](sql-database-geo-replication-failover-portal.md)
### [Failover usando o PowerShell](sql-database-geo-replication-failover-powershell.md)
### [Failover usando o T-SQL](sql-database-geo-replication-failover-transact-sql.md)
## Solucionar problemas
### [Problemas de conexão](sql-database-troubleshoot-common-connection-issues.md)
### [Erro de conexão transitório](sql-database-troubleshoot-connection.md)
### [Diagnosticar e evitar](sql-database-connectivity-issues.md)
### [Permissões](sql-database-troubleshoot-permissions.md)
### [Movimentação de bancos de dados](sql-database-troubleshoot-moving-data.md)
# Referência
## [PowerShell](/powershell/resourcemanager/azurerm.sql/v2.3.0/azurerm.sql)
## [PowerShell (Banco de dados Elástico)](/powershell/elasticdatabasejobs/v0.8.33/elasticdatabasejobs)
## [.NET](/dotnet/api/microsoft.azure.management.sql.models)
## [Java](/java/api/com.microsoft.azure.management.sql)
## [Node.js](https://msdn.microsoft.com/library/mt652093.aspx)
## [Python](https://msdn.microsoft.com/library/mt652092.aspx)
## [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)
## [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
## [T-SQL](https://msdn.microsoft.com/library/bb510741.aspx)
## [REST](https://msdn.microsoft.com/library/azure/mt163571.aspx)

# Relacionados
## Biblioteca de Gerenciamento do Banco de Dados SQL
### [Obtenha o pacote de Biblioteca de Gerenciamento de Banco de Dados SQL](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
## [Drivers do SQL Server](https://msdn.microsoft.com/library/mt654049.aspx)
### [ADO.NET](https://msdn.microsoft.com/library/mt657768.aspx)
### [JDBC](https://msdn.microsoft.com/library/mt484311.aspx)
### [ODBC](https://msdn.microsoft.com/library/mt654048.aspx)

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


<!--HONumber=Dec16_HO3-->


