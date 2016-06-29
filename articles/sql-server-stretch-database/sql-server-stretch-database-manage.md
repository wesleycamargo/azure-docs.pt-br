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
	ms.date="06/14/2016"
	ms.author="douglasl"/>

# Gerenciar e solucionar problemas do Stretch Database

Para gerenciar e solucionar problemas do Banco de Dados de Stretch, use as ferramentas e os métodos descritos neste tópico.

## Gerenciar dados locais

### <a name="LocalInfo"></a>Obter informações sobre bancos de dados e tabelas locais habilitadas para o Stretch Database
Abra os modos de exibição de catálogo **sys.databases** e **sys.tables** para ver informações sobre tabelas e bancos de dados do SQL Server habilitados para o Stretch. Para saber mais, confira [sys.databases (Transact-SQL)](https://msdn.microsoft.com/library/ms178534.aspx) e [sys.tables (Transact-SQL)](https://msdn.microsoft.com/library/ms187406.aspx).

Para ver quanto espaço uma tabela habilitada para Stretch está usando no SQL Server, execute a seguinte instrução.

 ```tsql
 EXEC sp_spaceused '<table name>', 'true', 'LOCAL_ONLY';
 ```
## Gerenciar a migração de dados

### Verificar o predicado de filtro aplicado a uma tabela
Abra o modo de exibição de catálogo **sys.remote\_data\_archive\_tables** e verifique o valor da coluna **filter\_predicate** para identificar a função usada pelo Stretch Database para selecionar linhas para migração. Se o valor for nulo, a tabela inteira poderá ser migrada. Para saber mais, confira [sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

### <a name="Migration"></a>Verificar o status da migração de dados
Escolha **Tarefas | Stretch | Monitorar** para que um banco de dados no SQL Server Management Studio monitore a migração de dados no Monitor do Stretch Database. Para saber mais, confira [Monitorar e solucionar problemas de migração de dados (Stretch Database)](sql-server-stretch-database-monitor.md).

Ou, abra o modo de exibição de gerenciamento dinâmico **sys.dm\_db\_rda\_migration\_status** para ver quantos lotes e linhas de dados foram migrados.

### <a name="Firewall"></a>Solucionar problemas de migração de dados
Para ver sugestões de solução de problema, confira [Monitorar e solucionar problemas de migração de dados (Stretch Database)](sql-server-stretch-database-monitor.md).

## Gerenciar dados remotos

### <a name="RemoteInfo"></a>Obter informações sobre bancos de dados e tabelas remotas usadas pelo Stretch Database
Abra os modos de exibição de catálogo **sys.remote\_data\_archive\_databases** e **sys.remote\_data\_archive\_tables** para ver informações sobre as tabelas e bancos de dados remotos nos quais os dados migrados são armazenados. Para saber mais, confira [sys.remote\_data\_archive\_databases (Transact-SQL)](https://msdn.microsoft.com/library/dn934995.aspx) e [sys.remote\_data\_archive\_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

### Excluir os dados migrados  
Se você quiser excluir dados que já foram migrados para o Azure, execute as etapas descritas em [sys.sp\_rda\_reconcile\_batch](https://msdn.microsoft.com/library/mt707768.aspx).

## Gerenciar o esquema da tabela

### Não altere o esquema da tabela remota
Não altere o esquema de uma tabela remota do Azure que está associada a uma tabela do SQL Server configurada para o Banco de Dados de Stretch. Especificamente, não modifique o nome ou o tipo de dados de uma coluna. O recurso Banco de Dados de Stretch faz várias suposições sobre o esquema da tabela remota em relação ao esquema da tabela do SQL Server. Se você alterar o esquema remoto, o Banco de Dados de Stretch deixará de funcionar para a tabela alterada.

### Reconciliar as colunas da tabela  
Se você excluiu acidentalmente as colunas da tabela remota, execute **sp\_rda\_reconcile\_columns** para adicionar colunas à tabela remota que existe na tabela do SQL Server habilitada para Stretch, mas não na tabela remota. Para saber mais, confira [sys.sp\_rda\_reconcile\_columns](https://msdn.microsoft.com/library/mt707765.aspx).

  > [IMPORTANTE] Quando **sp\_rda\_reconcile\_columns** recria colunas acidentalmente excluídas da tabela remota, ele não restaura os dados existentes nas colunas excluídas.

O **sp\_rda\_reconcile\_columns** não exclui colunas da tabela remota que existem na tabela remota, mas não na tabela do SQL Server habilitada para Stretch. Se houver colunas na tabela remota do Azure que não existem mais na tabela do SQL Server habilitada para Stretch, essas colunas extras não impedirão o Stretch Database de funcionar normalmente. Opcionalmente, você pode remover as colunas extras manualmente.

## Gerenciar o desempenho e os custos  

### Solucionar problemas com o desempenho da consulta
Consultas que incluem tabelas habilitadas para o Stretch devem executar mais lentamente do que antes de serem habilitadas para o Stretch. Se o desempenho da consulta diminuir consideravelmente, examine os possíveis problemas a seguir.

-   Seu servidor do Azure está em uma região geográfica diferente do SQL Server? Configure seu servidor do Azure para estar na mesma região geográfica que seu SQL Server para reduzir a latência de rede.

-   As condições de sua rede podem estar degradadas. Entre em contato com o administrador de rede para obter informações sobre problemas recentes ou interrupções.

### Aumentar o nível de desempenho do Azure para operações com uso intenso de recursos, como a indexação
Quando você compila, recompila ou reorganiza um índice em uma tabela grande configurada para o Stretch Database, e antecipa uma quantidade grande de consultas nos dados migrados no Azure durante esse período, considere o aumento do nível de desempenho do banco de dados remoto do Azure correspondente durante a operação. Para saber mais sobre níveis de desempenho e preços, confira [Preços do SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### Não é possível pausar o serviço do SQL Server Stretch Database no Azure  
 Selecione os níveis de desempenho e de preços adequados. Se você aumentar temporariamente o nível de desempenho para uma operação com uso pesado de recursos, restaure-o ao nível anterior após a conclusão da operação. Para saber mais sobre níveis de desempenho e preços, confira [Preços do SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## Alterar o escopo das consultas  
 Consultas em tabelas habilitadas para o Stretch retornam dados locais e remotos por padrão. Você pode alterar o escopo das consultas para todas as consultas de todos os usuários, ou apenas para uma única consulta de um administrador.

### Alterar o escopo das consultas para todas as consultas de todos os usuários  
 Para alterar o escopo de todas as consultas de todos os usuários, execute o procedimento armazenado **sys.sp\_rda\_set\_query\_mode**. Você pode reduzir o escopo para consultar apenas os dados locais, desabilitar todas as consultas ou restaurar a configuração padrão. Para saber mais, confira [sys.sp\_rda\_set\_query\_mode](https://msdn.microsoft.com/library/mt703715.aspx).

### <a name="queryHints"></a>Alterar o escopo das consultas para uma única consulta de um administrador  
 Para alterar o escopo de uma única consulta de um membro da função db\_owner, adicione a dica de consulta **WITH (REMOTE\_DATA\_ARCHIVE\_OVERRIDE = *value* )** à instrução SELECT. A dica de consulta REMOTE\_DATA\_ARCHIVE\_OVERRIDE pode ter os seguintes valores.
 -   **LOCAL\_ONLY**. Consultar somente dados locais.  

 -   **REMOTE\_ONLY**. Consultar somente dados remotos.

 -   **STAGE\_ONLY**. Consulte somente os dados na tabela na qual o Stretch Database prepara linhas qualificadas para migração e retém linhas migradas durante o período especificado após a migração. Essa dica de consulta é a única maneira de consultar a tabela de preparo.

Por exemplo, a consulta a seguir retorna apenas resultados locais.

 ```tsql  
SELECT * FROM Stretch_enabled_table WITH (REMOTE_DATA_ARCHIVE_OVERRIDE = LOCAL_ONLY) WHERE ...  
```  

## <a name="adminHints"></a>Fazer exclusões e atualizações administrativas  
 Por padrão, não é possível ATUALIZAR ou EXCLUIR linhas qualificadas para migração, ou linhas que já foram migradas, em uma tabela habilitada para Stretch. Quando for necessário corrigir um problema, um membro da função db\_owner poderá executar uma operação UPDATE ou DELETE adicionando a dica de consulta **WITH ( REMOTE\_DATA\_ARCHIVE\_OVERRIDE = *value* )** à instrução. A dica de consulta REMOTE\_DATA\_ARCHIVE\_OVERRIDE pode ter os seguintes valores.
 -   **LOCAL\_ONLY**. Atualize ou exclua somente dados locais.  

 -   **REMOTE\_ONLY**. Atualize ou exclua somente dados remotos.

 -   **STAGE\_ONLY**. Atualize ou exclua somente os dados na tabela na qual o Stretch Database prepara linhas qualificadas para migração e retém linhas migradas durante o período especificado após a migração.

## Consulte também

[Monitorar o Stretch Database](sql-server-stretch-database-monitor.md)

[Fazer backup de bancos de dados habilitados para o Stretch](sql-server-stretch-database-backup.md)

[Restaurar o backup de bancos de dados habilitados para o Stretch](sql-server-stretch-database-restore.md)

<!---HONumber=AcomDC_0615_2016-->