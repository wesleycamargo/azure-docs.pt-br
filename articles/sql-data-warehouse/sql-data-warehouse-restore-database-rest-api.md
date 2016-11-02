<properties
   pageTitle="Restaurar um Azure SQL Data Warehouse (API REST) | Microsoft Azure"
   description="Tarefas da API REST para restaurar um Azure SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/31/2016"
   ms.author="lakshmir;barbkess"/>


# <a name="restore-an-azure-sql-data-warehouse-rest-api"></a>Recuperar um SQL Data Warehouse do Azure (API REST)

> [AZURE.SELECTOR]
- [Visão geral][]
- [Portal][]
- [PowerShell][]
- [REST][]

Neste artigo, você aprenderá como restaurar um Azure SQL Data Warehouse usando a API REST.

## <a name="before-you-begin"></a>Antes de começar

**Verifique sua capacidade de DTU.**  Cada SQL Data Warehouse é hospedado por um servidor SQL (por exemplo, myserver.database.windows.net) que tem uma cota de DTU padrão.  Antes de restaurar um SQL Data Warehouse, verifique se o SQL Server tem cota de DTU suficiente restante para o banco de dados que está sendo restaurado. Para saber como calcular a DTU necessária ou para solicitar mais DTU, veja [Solicitar uma alteração de cota de DTU][].

## <a name="restore-an-active-or-paused-database"></a>Restaurar um banco de dados ativo ou pausado

Para restaurar um banco de dados:

1. Obtenha a lista de pontos de restauração do banco de dados usando a operação de obtenção de pontos de restauração de banco de dados.
2. Comece sua restauração usando a operação [Criar solicitação de restauração do banco de dados][] .
3. Acompanhe o status de sua restauração usando a operação [Status de operações do banco de dados][] .

>[AZURE.NOTE] Depois que a restauração estiver concluída, você poderá configurar o banco de dados recuperado seguindo [Configurar o banco de dados após a recuperação][].

## <a name="restore-a-deleted-database"></a>Restaurar um banco de dados excluído

Para restaurar um banco de dados excluído:

1.  Liste todos os bancos de dados excluídos restauráveis usando a operação [Listar bancos de dados descartados restauráveis][] .
2.  Obtenha os detalhes do banco de dados excluído que deseja restaurar usando a operação [Obter banco de dados descartados restauráveis][] .
3.  Comece sua restauração usando a operação [Criar solicitação de restauração do banco de dados][] .
4.  Acompanhe o status de sua restauração usando a operação [Status de operações do banco de dados][] .

>[AZURE.NOTE] Para configurar o banco de dados após a conclusão da restauração, consulte [Configurar o banco de dados após a recuperação][]. 


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os recursos de continuidade dos negócios das edições do Banco de Dados SQL do Azure, leia a [Visão geral da continuidade dos negócios do Banco de Dados SQL do Azure][].

<!--Image references-->

<!--Article references-->
[Visão geral da continuidade dos negócios do Banco de Dados SQL do Azure]: ./sql-database-business-continuity.md
[Solicitar uma alteração de cota de DTU]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Configurar o banco de dados após a recuperação]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Como instalar e configurar o Azure PowerShell]: ./powershell-install-configure.md
[Visão geral]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->
[Criar solicitação de restauração do banco de dados]: https://msdn.microsoft.com/library/azure/dn509571.aspx
[Status de operações do banco de dados]: https://msdn.microsoft.com/library/azure/dn720371.aspx
[Obter banco de dados descartados restauráveis]: https://msdn.microsoft.com/library/azure/dn509574.aspx
[Listar bancos de dados descartados restauráveis]: https://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Portal do Azure]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps



<!--HONumber=Oct16_HO2-->


