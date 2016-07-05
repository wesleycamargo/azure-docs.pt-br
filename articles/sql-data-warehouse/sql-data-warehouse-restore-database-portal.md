<properties
   pageTitle="Restaurar um SQL Data Warehouse do Azure (Portal) | Microsoft Azure"
   description="Tarefas do portal do Azure para restaurar um Azure SQL Data Warehouse."
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
   ms.date="06/17/2016"
   ms.author="elfish;barbkess;sonyama;nicw"/>

# Restaurar um Azure SQL Data Warehouse (Portal)

> [AZURE.SELECTOR]
- [Visão geral][]
- [Portal][]
- [PowerShell][]
- [REST][]

Neste artigo, você aprenderá como restaurar um Azure SQL Data Warehouse usando o Portal do Azure.

## Antes de começar

**Verifique sua capacidade de DTU.** Cada SQL Data Warehouse é hospedado por um servidor lógico SQL Server. Esse servidor lógico tem um limite de capacidade medido em DTUs. Antes de restaurar um SQL Data Warehouse, é importante verificar se o servidor lógico SQL Server que hospeda seu banco de dados tem capacidade de DTU suficiente para o banco de dados que está sendo restaurado. Confira esta postagem no blog para saber mais sobre [como exibir e aumentar a cota de DTU][].


## Restaurar um banco de dados ativo ou pausado

Para restaurar um banco de dados:

1. Faça logon no [Portal do Azure][]
2. No lado esquerdo da tela, escolha **Procurar** e **Servidores SQL**
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
    
3. Navegue até o servidor e selecione-o
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)

4. Localize o SQL Data Warehouse do qual você quer restaurar e selecione-o
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. Na parte superior da folha Data Warehouse, clique em **Restaurar**
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)

6. Especifique um novo **Nome de banco de dados**
7. Escolha o **Ponto de Restauração** mais recente
    1. Certifique-se de escolher o ponto de restauração mais recente. Uma vez que os pontos de restauração são mostrados em UTC, às vezes, a opção padrão mostrada não é o ponto de restauração mais recente.
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)

8. Clique em **OK**
9. O processo de restauração do banco de dados começará e poderá ser monitorado usando **NOTIFICAÇÕES**

>[AZURE.NOTE] Depois que a restauração estiver concluída, você poderá configurar o banco de dados recuperado seguindo o guia [Finalizar um banco de dados recuperado][].


## Restaurar um banco de dados excluído

Para restaurar um banco de dados excluído:

1. Faça logon no [Portal do Azure][]
2. No lado esquerdo da tela, escolha **Procurar** e **Servidores SQL**
    
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)

3. Navegue até o servidor e selecione-o
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)

4. Role para baixo até a seção Operações na folha do seu servidor
5. Clique no bloco **Bancos de Dados Excluídos**
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)

6. Escolha o banco de dados excluído que deseja restaurar
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)

7. Especifique um novo **Nome de banco de dados**
    
    ![](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
    
8. Clique em **OK**
9. O processo de restauração do banco de dados começará e poderá ser monitorado usando **NOTIFICAÇÕES**

>[AZURE.NOTE] Depois que a restauração estiver concluída, você poderá configurar o banco de dados recuperado seguindo o guia [Finalizar um banco de dados recuperado][].


## Próximas etapas
Para saber mais sobre os recursos de continuidade dos negócios das edições do Banco de Dados SQL do Azure, leia a [Visão geral da continuidade dos negócios do Banco de Dados SQL do Azure][].

<!--Image references-->

<!--Article references-->
[Visão geral da continuidade dos negócios do Banco de Dados SQL do Azure]: ./sql-database-business-continuity.md
[Visão geral]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Finalizar um banco de dados recuperado]: ./sql-database-recovered-finalize.md

<!--MSDN references-->

<!--Blog references-->
[como exibir e aumentar a cota de DTU]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!--Other Web references-->
[Portal do Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0622_2016-->