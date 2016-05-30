<properties
	pageTitle="Gerenciar e solucionar problemas do Stretch Database | Microsoft Azure"
	description="Saiba como gerenciar e solucionar problemas do Stretch Database."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="douglasl"/>

# Gerenciar e solucionar problemas do Stretch Database

Para gerenciar e solucionar problemas do Banco de Dados de Stretch, use as ferramentas e os métodos descritos neste tópico.

## <a name="LocalInfo"></a>Obter informações sobre bancos de dados e tabelas locais habilitadas para o Banco de Dados de Stretch
Abra os modos de exibição de catálogo **sys.databases** e **sys.tables** para ver informações sobre tabelas e bancos de dados do SQL Server habilitados para o Stretch. Para saber mais, confira [sys.databases (Transact-SQL)](https://msdn.microsoft.com/library/ms178534.aspx) e [sys.tables (Transact-SQL)](https://msdn.microsoft.com/library/ms187406.aspx).

## <a name="RemoteInfo"></a>Obter informações sobre bancos de dados e tabelas remotas usadas pelo Stretch Database
Abra os modos de exibição de catálogo **sys.remote\_data\_archive\_databases** e **sys.remote\_data\_archive\_tables** para ver informações sobre as tabelas e bancos de dados remotos nos quais os dados migrados são armazenados. Para saber mais, confira [sys.remote\_data\_archive\_databases (Transact-SQL)](https://msdn.microsoft.com/library/dn934995.aspx) e [sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

## Verificar o predicado de filtro aplicado a uma tabela
Abra o modo de exibição de catálogo **sys.remote\_data\_archive\_tables** e verifique o valor da coluna **filter\_predicate**. Se o valor for nulo, a tabela inteira poderá ser migrada. Para saber mais, consulte [sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

## <a name="Migration"></a>Verificar o status da migração de dados
Escolha **Tarefas | Stretch | Monitorar** para que um banco de dados no SQL Server Management Studio monitore a migração de dados no Monitor do Banco de Dados de Stretch. Para saber mais, confira [Monitorar e solucionar problemas de migração de dados (Banco de Dados de Stretch)](sql-server-stretch-database-monitor.md).

Ou, abra o modo de exibição de gerenciamento dinâmico **sys.dm\_db\_rda\_migration\_status** para ver quantos lotes e linhas de dados foram migrados.

## Aumentar o nível de desempenho do Azure para operações com uso intenso de recursos, como a indexação
Quando você compila, recompila ou reorganiza um índice em uma tabela grande configurada para o Banco de Dados de Stretch, considere o aumento do nível de desempenho do banco de dados remoto correspondente durante a operação.

## Não altere o esquema da tabela remota
Não altere o esquema de uma tabela remota do Azure que está associada a uma tabela do SQL Server configurada para o Banco de Dados de Stretch. Especificamente, não modifique o nome ou o tipo de dados de uma coluna. O recurso Banco de Dados de Stretch faz várias suposições sobre o esquema da tabela remota em relação ao esquema da tabela do SQL Server. Se você alterar o esquema remoto, o Banco de Dados de Stretch deixará de funcionar para a tabela alterada.

## <a name="Firewall"></a>Solucionar problemas de migração de dados
Para ver sugestões de solução de problema, confira [Monitorar e solucionar problemas de migração de dados (Banco de Dados de Stretch)](sql-server-stretch-database-monitor.md).

## Solucionar problemas com o desempenho da consulta
**As consultas que incluem minha tabela habilitada para Stretch estão lentas.** Consultas que incluem tabelas habilitadas para o Stretch devem executar mais lentamente do que antes de serem habilitadas para o Stretch. Se o desempenho da consulta diminuir consideravelmente, examine os possíveis problemas a seguir.

-   Seu servidor do Azure está em uma região geográfica diferente do SQL Server? Configure seu servidor do Azure para estar na mesma região geográfica que seu SQL Server para reduzir a latência de rede.

-   As condições de sua rede podem estar degradadas. Entre em contato com o administrador de rede para obter informações sobre problemas recentes ou interrupções.

## Consulte também

[Monitorar o Stretch Database](sql-server-stretch-database-monitor.md)

[Fazer backup e restaurar os bancos de dados habilitados para Stretch](sql-server-stretch-database-backup.md)

<!---HONumber=AcomDC_0518_2016-->