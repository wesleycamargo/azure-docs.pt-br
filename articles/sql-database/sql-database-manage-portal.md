<properties
	pageTitle="Gerenciar Bancos de Dados SQL do Azure usando o Portal Clássico do Azure"
	description="Saiba como usar o Portal Clássico do Azure para gerenciar um banco de dados relacional na nuvem usando o Portal Clássico."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.date="01/22/2016"
	ms.author="sstein"/>


# Gerenciando Bancos de Dados SQL do Azure usando o Portal do Azure


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-manage-portal.md)
- [SSMS](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-command-line-tools.md)

O [portal do Azure](https://portal.azure.com/) permite que você crie, monitore e gerencie servidores lógicos e bancos de dados SQL Azure. Este artigo destaca algumas das tarefas mais comuns.

![Visão geral sobre o banco de dados](./media/sql-database-manage-portal/sqldatabase_annotated.png)

## 1\. Ações de gerenciamento de banco de dados
![Ações de gerenciamento de banco de dados](./media/sql-database-manage-portal/sqldatabase_actions.png)

O Portal do Azure fornece um conjunto de ações comuns de banco de dados acessíveis na parte superior da folha de um banco de dados. Você pode restaurar um banco de dados para um ponto anterior no tempo, abrir um banco de dados no Visual Studio, copiar um banco de dados para um novo servidor e exportar o banco de dados para uma conta de armazenamento do Azure.

- [Restaurar um banco de dados SQL](sql-database-user-error-recovery.md)
- [Abrir um banco de dados SQL no Visual Studio](sql-database-connect-query.md)
- [Exportar um banco de dados SQL](sql-database-export.md)

## 2\. Monitoramento de banco de dados
![Monitoramento de banco de dados](./media/sql-database-manage-portal/sqldatabase_monitoring.png)

Por padrão, os bancos de dados SQL Azure apresentam gráficos de monitoramento de DTU (Unidade de Transação de Banco de Dados), do tamanho do banco de dados e da integridade da conexão. Esses gráficos de monitoramento podem ser personalizados e ampliados para mostrar a Porcentagem de CPU, Porcentagem de E/S de dados, Bloqueios, Porcentagem de E/S de log ou até mesmo a porcentagem de solicitações bloqueadas pelo firewall. Para saber mais sobre como personalizar os gráficos de monitoramento, clique [aqui][Azure part monitoring].

Além disso, as regras de alerta podem ser configuradas para monitorar uma métrica especificada e alertar um administrador e coadministrador designados quando os limites predefinidos forem atingidos. Para saber mais sobre como configurar as regras de alerta no Portal Clássico do Azure, clique [aqui][Azure part monitoring].

## 3\. Segurança e auditoria de banco de dados
![Segurança de banco de dados](./media/sql-database-manage-portal/sqldatabase_security.png)

Os Bancos de Dados SQL do Azure podem ser configurados para rastrear todos os eventos do banco de dados e gravá-los em um log de auditoria em sua conta de armazenamento do Azure. Esse recurso pode ajudar você a manter uma conformidade regulatória, a entender a atividade do banco de dados e a obter informações sobre discrepâncias que poderiam indicar preocupações de negócios ou suspeitas de violações de segurança.

- [Auditoria de Banco de Dados SQL do Azure](sql-database-auditing-get-started.md)

Os bancos de dados SQL do Azure também podem ser configurados para mascarar dados confidenciais para usuários não privilegiados.

- [Mascaramento de dados dinâmicos](sql-database-dynamic-data-masking-get-started.md)


## 4\. Replicação geográfica
![Replicação geográfica](./media/sql-database-manage-portal/sqldatabase_georeplication.png)

É possível configurar bancos de dados SQL do Azure para replicar de forma assíncrona as transações confirmadas para um banco de dados secundário. Parte da replicação geográfica no portal permite que você selecione a região do Azure na qual gostaria de colocar o banco de dados secundário.

- [Replicação Geográfica](https://msdn.microsoft.com/library/azure/dn783447.aspx)





##Recursos adicionais
* [Banco de Dados SQL](sql-database-technical-overview.md)   
* [Monitorando o Banco de Dados SQL Usando Exibições de Gerenciamento Dinâmico][]   
* [Referência Transact-SQL (Banco de Dados SQL)][]

  [Azure Classic Portal Tour]: https://go.microsoft.com/fwlink/?LinkID=522341
  [Classic Portal]: https://portal.azure.com
  [Azure part monitoring]: ../documentdb-monitor-accounts.md
  [AzureDb management overview]: http://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/
  [Introducing SQL Database]: http://azure.microsoft.com/services/sql-database
  [Database geo-replication]: http://azure.microsoft.com/blog/2014/07/12/spotlight-on-sql-database-active-geo-replication/
  [Managing Azure SQL Database using SQL Server Management Studio]: sql-database-manage-azure-ssms.md
  [Monitorando o Banco de Dados SQL Usando Exibições de Gerenciamento Dinâmico]: http://msdn.microsoft.com/library/windowsazure/ff394114.aspx
  [Referência Transact-SQL (Banco de Dados SQL)]: http://msdn.microsoft.com/library/bb510741(v=sql.120).aspx
  [AzureDb Auditing]: http://azure.microsoft.com/documentation/articles/sql-database-auditing-get-started/
  [AzureDb datamasking]: http://azure.microsoft.com/documentation/articles/sql-database-dynamic-data-masking-get-started/

<!---HONumber=AcomDC_0302_2016-->