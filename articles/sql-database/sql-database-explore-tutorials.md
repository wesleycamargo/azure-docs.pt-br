<properties
   pageTitle="Explorar Tutoriais do Banco de Dados SQL do Azure | Microsoft Azure"
   description="Saiba mais sobre recursos e funcionalidades do Banco de Dados SQL"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="06/01/2016"
   ms.author="carlrab"/>
   
# Explorar Tutoriais do Banco de Dados SQL do Azure

Os links a seguir levam você a uma visão geral de cada área de recurso listado e a um tutorial passo a passo simples para cada área. Para ver guias de início rápido referentes às soluções que demonstram o uso do Banco de Dados SQL em uma solução completa com base em cenários do mundo real, consulte [Azure SQL Database Solution Quick Starts](sql-database-solution-quick-starts.md) (Inícios Rápidos da solução de Banco de Dados SQL do Azure).

## Como usar o SQL Server Management Studio.

Nos tutoriais a seguir, você aprenderá sobre o uso do SQL Server Management Studio para administrar e consultar o Banco de Dados SQL do Azure.


> [AZURE.IMPORTANT] Recomendamos que você sempre use a versão mais recente do Management Studio a fim de permanecer sincronizado com as atualizações no Microsoft Azure e no Banco de Dados SQL. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


| Tutorial | Descrição |
|---|---|---|
| [Conectar-se ao Banco de Dados SQL do Azure usando um logon de entidade no nível do servidor](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login)| Nesse tutorial, você aprenderá a se conectar ao Banco de Dados SQL do Azure usando um logon de entidade de segurança no nível do servidor.|
| [Conectar-se ao Banco de Dados SQL do Azure como um usuário](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) | Nesse tutorial, você aprenderá a se conectar ao Banco de Dados SQL do Azure usando uma conta de usuário no nível do banco de dados.|
||||

## Pools elásticos

Nos tutoriais a seguir, você aprenderá sobre o uso de [pools elásticos](sql-database-elastic-pool.md) para gerenciar as metas de desempenho para vários bancos de dados que têm padrões de uso muito variáveis e imprevisíveis.

| Tutorial | Descrição |
|---|---|---|
| [Criar um pool elástico](sql-database-elastic-pool-create-portal.md) | Nesse tutorial, você aprenderá como criar um pool escalonável de Bancos de Dados SQL do Azure. |
| [Monitorar um banco de dados elástico](sql-database-elastic-pool-manage-portal.md#elastic-database-monitoring) | Nesse tutorial, você aprenderá a monitorar um banco de dados elástico individual para problemas em potencial. |
| [Adicionar um alerta a um recurso de pool](sql-database-elastic-pool-manage-portal.md#add-an-alert-to-a-pool-resource) | Nesse tutorial, você aprenderá a adicionar regras a recursos que enviam emails para as pessoas ou cadeias de caracteres de alerta para pontos de extremidade da URL quando o recurso atingir o limite de utilização que você configurou. |
| [Mover um banco de dados para um pool elástico](sql-database-elastic-pool-manage-portal.md#move-a-database-into-an-elastic-pool) | Nesse tutorial, você aprenderá a mover um banco de dados para um pool elástico. |
| [Remover um banco de dados de um pool elástico](sql-database-elastic-pool-manage-portal.md#move-a-database-out-of-an-elastic-pool) | Nesse tutorial, você aprenderá a mover um banco de dados fora de um pool elástico. |
| [Alterar as configurações de desempenho de um pool](sql-database-elastic-pool-manage-portal.md#change-performance-settings-of-a-pool) | Nesse tutorial, você aprenderá como ajustar os limites de desempenho e armazenamento para um pool. |
||||

## Trabalhos de banco de dados elástico

Nos tutoriais a seguir, você aprenderá sobre o uso de [trabalhos de banco de dados elástico](sql-database-elastic-jobs-overview.md).

| Tutorial | Descrição |
|---|---|---|
| [Introdução às ferramentas do Banco de Dados Elástico](sql-database-elastic-scale-get-started.md) | Nesse tutorial, você aprenderá a usar as funcionalidades das ferramentas do banco de dados elástico usando um aplicativo fragmentado simples. |
| [Introdução aos trabalhos elásticos do Banco de Dados SQL do azure](sql-database-elastic-jobs-getting-started.md) | Nesse tutorial, você aprenderá a criar e gerenciar trabalhos que gerenciam um grupo de bancos de dados relacionados. | 
||||

## Consultas elásticas

Nos tutoriais a seguir, você aprenderá sobre a execução de [consultas elásticas](sql-database-elastic-query-overview.md).

| Tutorial | Descrição |
|---|---|---|
| [Consultando em um banco de dados horizontalmente particionado (fragmentado)](sql-database-elastic-query-getting-started.md) | Neste tutorial, você aprenderá a criar relatórios de todos os bancos de dados em um banco de dados horizontalmente particionado (fragmentado) usando a [consulta elástica](sql-database-elastic-query-overview.md) |
| [Consultando em um banco de dados verticalmente particionado](sql-database-elastic-query-getting-started-vertical.md#create-database-objects) | Neste tutorial, você aprenderá a criar relatórios de todos os bancos de dados em um banco de dados vertical usando uma [consulta elástica](sql-database-elastic-query-overview.md) |
| [Migrar um banco de dados existente para escalar horizontalmente](sql-database-elastic-convert-to-use-elastic-tools.md)| Nesse tutorial, você aprenderá a escalar horizontalmente (fragmentos) um Banco de Dados SQL do Azure. |
||||

## Otimização do Desempenho

Nos tutoriais a seguir, você aprenderá sobre a otimização do [desempenho de bancos de dados únicos](sql-database-performance-guidance.md). Para otimizar o desempenho de vários bancos de dados, consulte [Pools elásticos](#elastic-pools).

| Tutorial | Descrição |
|---|---|---|
| [Alterar a camada de serviço e o nível de desempenho do banco de dados](sql-database-scale-up.md#change-the-service-tier-and-performance-level-of-your-database) | Nesse tutorial, você aprenderá a escalar verticalmente ou reduzir verticalmente o desempenho de um Banco de Dados SQL do Azure usando camadas de serviço. |
| [Análise de Desempenho de Consultas do Consultor do Banco de Dados SQL](sql-database-performance.md#performance-overview) | Nesse tutorial, você aprenderá como abrir e usar a Análise de Desempenho de Consultas do Consultor do Banco de Dados SQL.|
| [Recomendações de desempenho do Consultor do Banco de Dados SQL](sql-database-advisor.md#viewing-recommendations) | Nesse tutorial, você aprenderá a exibir e a aplicar as recomendações de desempenho do Consultor do Banco de Dados SQL. |
| [Examinar as consultas que mais consomem CPU](sql-database-query-performance.md#review-top-cpu-consuming-queries)| Nesse tutorial, você aprenderá como abrir e usar a Análise de Desempenho de Consultas do Consultor do Banco de Dados SQL para examinar as consultas que mais consomem CPU.|
| [Exibindo detalhes de uma consulta individual](sql-database-query-performance.md#viewing-individual-query-details)| Nesse tutorial, você aprenderá a usar a Análise de Desempenho de Consultas do Consultor do Banco de Dados SQL para exibir detalhes de desempenho de uma consulta individual.|
||||

## Migração e Arquivo do Banco de Dados SQL 

Nos tutoriais a seguir, você aprenderá sobre a [migração de um banco de dados do SQL Server existente para um Banco de Dados SQL do Azure](sql-database-cloud-migrate.md).

| Tutorial | Descrição |
|---|---|---|
| [Detectando problemas de compatibilidade usando o SQL Server Data Tools para Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md#detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio) | Nesse tutorial, você aprenderá a usar o SQL Server Data Tools para Visual Studio para determinar a compatibilidade do Banco de Dados SQL do Azure. |
| [Corrigindo problemas de compatibilidade usando o SQL Server Data Tools para Visual Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssdt#fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio) | Nesse tutorial, você aprenderá a usar o SQL Server Data Tools para Visual Studio para corrigir problemas de compatibilidade do Banco de Dados SQL do Azure. |
| [Determinar a compatibilidade com o Banco de Dados SQL usando SqlPackage.exe](ql-database-cloud-migrate-determine-compatibility-sqlpackage.md#using-sqlpackageexe) | Nesse tutorial, você aprenderá a usar o utilitário da linha de comando SQLPackage.exe para determinar a compatibilidade do Banco de Dados SQL do Azure.|
| [Determinar a compatibilidade com o Banco de Dados SQL usando SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md#using-sql-server-management-studio) |Nesse tutorial, você aprenderá a usar o SQL Server Management Studio para determinar a compatibilidade do Banco de Dados SQL do Azure.|
| [Migrar o banco de dados do SQL Server para o Banco de Dados SQL usando o Assistente para implantação de Banco de Dados no Banco de Dados do Microsoft Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md#use-the-deploy-database-to-microsoft-azure-database-wizard) | Nesse tutorial, você aprenderá a migrar um banco de dados do SQL Server compatível para o Banco de Dados SQL do Azure usando o Assistente Implantar Banco de Dados no Banco de Dados do Microsoft Azure no SQL Server Management Studio.
| [Exportar um Banco de Dados do SQL Server para um arquivo BACPAC usando SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | Nesse tutorial, você aprenderá a exportar um banco de dados do SQL Server compatível para um arquivo BACPAC usando o Assistente de Exportação de Aplicativo de Camada de Dados no SQL Server Management Studio.|
| [Exportar um banco de dados do SQL Server para um arquivo BACPAC usando SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | Nesse tutorial, você aprenderá a exportar um banco de dados do SQL Server compatível para um arquivo BACPAC usando o utilitário de linha de comando SQLPackage.exe.|
| [Importar um arquivo BACPAC para o Banco de Dados SQL do Azure usando SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | Nesse tutorial, você aprenderá a importar um banco de dados no Banco de Dados SQL do Azure por meio de um arquivo BACPAC usando o Assistente de Exportação de Aplicativo de Camada de Dados no SQL Server Management Studio. |
| [Importar um arquivo BACPAC para o Banco de Dados SQL do Azure usando o SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md#import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage) | Nesse tutorial, você aprenderá a importar um banco de dados no Banco de Dados SQL do Azure por meio de um arquivo BACPAC usando o utilitário da linha de comando SQLPackage. |
| [Importar um arquivo BACPAC para o Banco de Dados SQL do Azure usando o portal do Azure](sql-database-import.md) | Nesse tutorial, você aprenderá a importar um banco de dados no Banco de Dados SQL do Azure por meio de um arquivo BACPAC armazenado em um blob do Azure usando o Portal do Azure.|
| [Importar um arquivo BACPAC para um Banco de Dados SQL do Azure usando o PowerShell](sql-database-import-powershell.md) | Nesse tutorial, você aprenderá a importar um banco de dados no Banco de Dados SQL do Azure por meio de um arquivo BACPAC usando o PowerShell.|
| [Arquivar um Banco de Dados SQL do Azure usando o portal do Azure](sql-database-export.md#export-your-database) | Nesse tutorial, você aprenderá a arquivar um banco de dados SQL do Azure em um arquivo BACPAC usando o portal do Azure. |
| [Arquivar um banco de dados SQL do Azure usando o PowerShell](sql-database-export-powershell.md) | Nesse tutorial, você aprenderá a arquivar um banco de dados SQL do Azure em um arquivo BACPAC usando o PowerShell. |
| [Copiar um Banco de Dados SQL do Azure usando o portal do Azure](sql-database-copy.md#copy-your-sql-database) | Nesse tutorial, você aprenderá a copiar um Banco de Dados SQL do Azure usando o portal do Azure. |
| [Copiar um banco de dados SQL do Azure usando o PowerShell](sql-database-copy-powershell#copy-your-sql-database) | Nesse tutorial, você aprenderá a copiar um Banco de Dados SQL do Azure usando o PowerShell. |
| [Copiar um banco de dados SQL do Azure usando o Transact-SQL](sql-database-copy-transact-sql.md#copy-your-sql-database) | Nesse tutorial, você aprenderá a copiar um Banco de Dados SQL do Azure usando o Transact-SQL. |
||||

##Desenvolver

Nos tutoriais a seguir, você aprenderá sobre o [Desenvolvimento do Banco de Dados SQL](sql-database-develop-overview.md) e o uso das [bibliotecas de conectividade](sql-database-libraries.md).

| Tutorial | Descrição |
|---|---|---|
| [Conectar-se ao Banco de Dados SQL usando .NET (C#)](sql-database-develop-dotnet-simple.md) | Nesse tutorial, você aprenderá a se conectar a um Banco de Dados SQL do Azure usando o C#. |
| [Conectar-se ao Banco de Dados SQL usando o Java](sql-database-develop-java-simple.md) | Nesse tutorial, você aprenderá a se conectar a um Banco de Dados SQL do Azure usando o Java. |
| [Conectar-se ao Banco de Dados SQL usando Node.js](sql-database-develop-nodejs-simple.md) | Nesse tutorial, você aprenderá a se conectar a um Banco de Dados SQL do Azure usando Node.js. |
| [Conectar-se ao Banco de Dados SQL usando o PHP](sql-database-develop-php-simple.md) | Nesse tutorial, você aprenderá a se conectar a um Banco de Dados SQL do Azure usando PHP. |
| [Conectar-se ao Banco de Dados SQL usando Phyton](sql-database-develop-python-simple.md) | Nesse tutorial, você aprenderá a se conectar a um Banco de Dados SQL do Azure usando Python. |
| [Conectar-se ao Banco de Dados SQL usando o Ruby](sql-database-develop-ruby-simple.md) | Nesse tutorial, você aprenderá a se conectar a um Banco de Dados SQL do Azure usando Ruby. |
||||
 
## Acesso a banco de dados

Nos tutoriais a seguir, você aprenderá a [criar e gerenciar logons e usuários](sql-database-manage-logins.md).

| Tutorial | Descrição |
|---|---|---|
| [Criar uma regra de firewall de nível de servidor do Banco de Dados SQL do Azure usando o portal do Azure](sql-database-configure-firewall-settings.md) | Nesse tutorial, você aprenderá a configurar um firewall de nível de servidor do Banco de Dados SQL usando o portal do Azure. |
| [Criar uma regra de firewall de nível de banco de dados usando Transact-SQL](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules) | Nesse tutorial, você aprenderá a criar uma regra de firewall de nível de banco de dados usando o Transact-SQL.|
| [Gerenciar regras de firewall no nível de servidor usando Transact-SQL](sql-database-configure-firewall-settings-tsql.md#manage-server-level-firewall-rules-through-transact-sql) | Nesse tutorial, você aprenderá a gerenciar um firewall de nível de servidor do Banco de Dados SQL do Azure usando Transact-SQL.|
| [Gerenciar regras de firewall de nível de servidor usando o PowerShell](sql-database-configure-firewall-settings-powershell.md#manage-firewall-rules-using-powershell) | Nesse tutorial, você aprenderá a gerenciar um firewall de nível de servidor do Banco de Dados SQL do Azure usando o PowerShell.|
| [Gerenciar regras de firewall no nível de servidor usando a API REST](sql-database-configure-firewall-settings-rest.md#manage-firewall-rules-using-the-service-management-rest-api) | Nesse tutorial, você aprenderá a gerenciar um firewall de nível de servidor do Banco de Dados SQL do Azure usando a API REST.|
| [Conectar-se ao Banco de Dados SQL do Azure usando um logon de entidade no nível do servidor](sql-database-get-started-security.md#connect-to-azure-sql-database-using-a-server-level-principal-login)| Nesse tutorial, você aprenderá a se conectar ao Banco de Dados SQL do Azure usando um logon de entidade de segurança no nível do servidor.|
| [Concedendo acesso ao banco de dados para um logon] (sql-database-manage-logins.md#granting-database-access-to-a-login() | Nesse tutorial, você aprenderá a conceder acesso ao banco de dados a um logon de nível de servidor.|
| [Criar um novo usuário de banco de dados usando SSMS](sql-database-get-started-security.md#create-new-database-user-using-ssms) | Nesse tutorial, você aprenderá a criar um novo usuário de banco de dados em um banco de dados existente usando o SSMS.|
| [Conceder permissões de db\_owner a um novo usuário de banco de dados](sql-database-get-started-security.md#grant-new-database-user-dbowner-permissions) | Nesse tutorial, você aprenderá a conceder permissões de db\_owner a um usuário de banco de dados existente.|
| [Conectar-se ao Banco de Dados SQL do Azure como um usuário](sql-database-get-started-security.md#connect-to-azure-sql-database-as-a-user) | Nesse tutorial, você aprenderá a se conectar a um banco de dados SQL do Azure usando uma conta de usuário de nível de banco de dados.|
||||


## Segurança de Dados

Nos tutoriais a seguir, você aprenderá sobre a [proteção de dados do Banco de Dados SQL do Azure](sql-database-security.md).

| Tutorial | Descrição |
|---|---|---|
| [Habilitar a detecção de ameaças para seu banco de dados usando o portal do Azure](sql-database-threat-detection-get-started.md#set-up-threat-detection-for-your-database) | Neste tutorial, você aprenderá como configurar a detecção de ameaças no portal do Azure para seu banco de dados.|
| [Proteger dados confidenciais usando Sempre Criptografados ](sql-database-always-encrypted-azure-key-vault.md) | Nesse tutorial, você usará o assistente Sempre Criptografados para proteger dados confidenciais em um Banco de Dados SQL do Azure.|
| [Proteger dados confidenciais usando Transparent Data Encryption](https://msdn.microsoft.com/library/dn948096.aspx)| Nesse tutorial, você aprenderá a usar a Transparent Data Encryption para proteger dados confidenciais.|
| [Criptografar uma coluna de dados](https://msdn.microsoft.com/library/ms179331.aspx)| Nesse tutorial, você aprenderá a criptografar uma coluna de dados usando o Transact-SQL.|
| [Configurar mascaramento de dados dinâmicos](sql-database-dynamic-data-masking-get-started.md#set-up-dynamic-data-masking-for-your-database-using-the-azure-portal) | Nesse tutorial, você aprenderá a configurar o mascaramento de dados dinâmicos para seu Banco de Dados SQL do Azure. |
||||

## Continuidade dos negócios e Expansão de Consulta

Nos tutoriais a seguir, você aprenderá sobre o uso da [Restauração Geográfica e da Replicação Geográfica Ativa](sql-database-business-continuity.md) para se recuperar de erros, para a continuidade dos negócios e para a escala horizontal de consulta.

| Tutorial | Descrição |
|---|---|---|
| [Restaurar um Banco de Dados SQL do Azure para um determinado momento anterior com o Portal do Azure](sql-database-point-in-time-restore-portal.md)| Nesse tutorial, você aprenderá a restaurar seu banco de dados para determinado momento anterior usando o portal do Azure.|
| [Restaurar um Banco de Dados SQL do Azure para um determinado momento anterior com o PowerShell](sql-database-point-in-time-restore-powershell.md) | Nesse tutorial, você aprenderá a restaurar seu banco de dados para um determinado momento anterior usando o PowerShell|
| [Restaurar um Banco de Dados SQL do Azure excluído usando o Portal do Azure](sql-database-restore-deleted-database-portal.md) | Nesse tutorial, você aprenderá a restaurar um banco de dados excluído usando o portal do Azure.|
| [Restaurar um Banco de Dados SQL do Azure excluído usando o PowerShell](sql-database-restore-deleted-database-powershell.md) | Nesse tutorial, você aprenderá a restaurar um banco de dados excluído usando o PowerShell.|
| [Configurar a Replicação Geográfica para o Banco de Dados SQL do Azure usando o portal do Azure](sql-database-geo-replication-portal.md)| Nesse tutorial, você aprenderá a configurar a Replicação Geográfica Ativa usando o portal do Azure.|
| [Configurar Replicação Geográfica para o Banco de Dados SQL do Azure usando o PowerShell](sql-database-geo-replication-powershell.md)| Nesse tutorial, você aprenderá a configurar a Replicação Geográfica Ativa usando o PowerShell.|
| [Configurar a Replicação Geográfica para o Banco de Dados SQL do Azure usando o Transact-SQL](sql-database-geo-replication-transact-sql.md)| Nesse tutorial, você aprenderá a configurar a Replicação Geográfica Ativa usando o Transact-SQL.|
| [Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure usando o portal do Azure](sql-database-geo-replication-failover-portal.md) | Nesse tutorial, você aprenderá a fazer failover para uma réplica secundária replicada geograficamente usando o portal do Azure.|
| [Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure usando o PowerShell](sql-database-geo-replication-failover-powershell.md) | Nesse tutorial, você aprenderá a fazer failover para uma réplica secundária replicada geograficamente usando o PowerShell.|
| [Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure usando o Transact-SQL](sql-database-geo-replication-failover-transact-sql.md) | Nesse tutorial, você aprenderá a fazer failover para uma réplica secundária replicada geograficamente usando o Transact-SQL.|
||||

## Sincronização de Dados

Neste tutorial, você aprenderá sobre a [Sincronização de Dados](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).

| Tutorial | Descrição |
|---|---|---| 
| [Introdução à Sincronização de Dados do SQL do Azure (visualização)](sql-database-get-started-sql-data-sync.md) | Neste tutorial, você aprenderá os conceitos básicos da Sincronização de Dados do SQL do Azure usando o portal clássico do Azure. |
||||

## Próximas etapas

[Explorar os Guias de Início Rápido da solução de Banco de Dados SQL do Azure](sql-database-solution-quick-starts.md)

<!---HONumber=AcomDC_0803_2016-->