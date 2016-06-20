<properties
   pageTitle="Restaurar um banco de dados no SQL Data Warehouse do Azure (Portal) | Microsoft Azure"
   description="Tarefas do portal do Azure para restaurar um banco de dados ativo ou excluído no SQL Data Warehouse do Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/04/2016"
   ms.author="elfish;barbkess;sonyama"/>

# Restaurar um banco de dados no SQL Data Warehouse do Azure (Portal)

> [AZURE.SELECTOR]
- [Visão geral](sql-data-warehouse-restore-database-overview.md)
- [Portal](sql-data-warehouse-restore-database-portal.md)
- [PowerShell](sql-data-warehouse-restore-database-powershell.md)
- [REST](sql-data-warehouse-manage-restore-database-rest-api.md)

Tarefas do PowerShell para restaurar um banco de dados ativo ou excluído no SQL Data Warehouse do Azure.

Tarefas neste tópico:

- Restaurar um banco de dados dinâmico
- Restaurar um banco de dados excluído

## Antes de começar

Verifique a capacidade de DTU do Banco de Dados SQL. Como o SQL Data Warehouse restaura para um novo banco de dados no servidor SQL lógico, é importante verificar se o servidor SQL para o qual você está restaurando possui capacidade de DTU suficiente para o novo banco de dados. Consulte esta postagem no blog para obter mais informações sobre [como exibir e aumentar a cota de DTU][].


## Restaurar um banco de dados dinâmico

Para restaurar um banco de dados:

1. Faça logon no [Portal do Azure][].
2. No lado esquerdo da tela, selecione **PROCURAR** e, em seguida, **Bancos de Dados SQL**.
3. Navegue até o banco de dados e selecione-o.
4. Na parte superior da folha do banco de dados, clique em **Restaurar**.
5. Especifique um novo **Nome do banco de dados**, selecione um **Ponto de Restauração** e clique em **Criar**.
6. O processo de restauração do banco de dados começará e poderá ser monitorado usando **NOTIFICAÇÕES**.


## Restaurar um banco de dados excluído

Para restaurar um banco de dados excluído:

1. Faça logon no [Portal do Azure][].
2. No lado esquerdo da tela, selecione **PROCURAR** e, em seguida, **Servidores SQL**.
3. Navegue até o servidor e selecione-o.
4. Role para baixo até Operações na folha do servidor e clique no bloco **Bancos de Dados Excluídos**.
5. Selecione o banco de dados excluído que deseja restaurar.
5. Especifique um novo **Nome de banco de dados** e clique em **Criar**.
6. O processo de restauração do banco de dados começará e poderá ser monitorado usando **NOTIFICAÇÕES**.


## Próximas etapas
Para saber mais, confira [Visão geral de continuidade de negócios do Banco de Dados SQL do Azure][] e [Visão geral de gerenciamento][].

<!--Image references-->

<!--Article references-->
[Visão geral de continuidade de negócios do Banco de Dados SQL do Azure]: sql-database-business-continuity.md
[How to install and configure Azure PowerShell]: powershell-install-configure.md
[Visão geral de gerenciamento]: sql-data-warehouse-overview-manage.md

<!--MSDN references-->

<!--Blog references-->
[como exibir e aumentar a cota de DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Portal do Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0608_2016-->