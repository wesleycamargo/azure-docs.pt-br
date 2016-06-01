<properties
	pageTitle="Monitorar e solucionar problemas de migração de dados (Stretch Database) | Microsoft Azure"
	description="Saiba como monitorar o status de migração de dados."
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

# Monitorar e solucionar problemas de migração de dados (Stretch Database)

Para monitorar a migração de dados no Monitor do Stretch Database, selecione **Tarefas | Stretch | Monitor** de um banco de dados no SQL Server Management Studio.

## Verificar o status de migração de dados no Monitor do Stretch Database
Selecione **Tarefas | Stretch | Monitor** de um banco de dados no SQL Server Management Studio para abrir o Monitor do Stretch Database e monitorar a migração de dados.

-   A parte superior do monitor exibe informações gerais sobre o banco de dados SQL Server habilitado para Stretch e o banco de dados remoto do Azure.

-   A parte inferior do monitor exibe o status de migração de dados para cada tabela habilitada para Stretch no banco de dados.

![Monitor do Stretch Database][StretchMonitorImage1]

## <a name="Migration"></a>Verificar o status de migração de dados em uma exibição de gerenciamento dinâmico
Abra a exibição de gerenciamento dinâmico **sys.dm\_db\_rda\_migration\_status** para ver quantos lotes e linhas de dados foram migradas. Para obter mais informações, veja [sys.dm\_db\_rda\_migration\_status (Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx).

## <a name="Firewall"></a>Solucionar problemas de migração de dados
**O firewall do Azure está bloqueando as conexões do meu servidor local.**

Você precisará adicionar uma regra nas configurações do firewall do Azure do servidor do Azure para permitir que o SQL Server se comunique com o servidor remoto do Azure.

**Linhas da tabela habilitada para Stretch não estão sendo migradas para o Azure. Qual é o problema?**

Há vários problemas que podem afetar a migração. Verifique os seguintes procedimentos.

-   Verifique a conectividade de rede para o computador do SQL Server.

-   Verifique se o firewall do Azure não está impedindo o SQL Server de se conectar ao ponto de extremidade remoto.

-   Verifique a exibição de gerenciamento dinâmico **sys.dm\_db\_rda\_migration\_status** quanto ao status do lote mais recente. Se tiver ocorrido um erro, verifique os error\_number, error\_state e error\_severity do lote.

    -   Para obter mais informações sobre a exibição, veja [sys.dm\_db\_rda\_migration\_status (Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx).

    -   Para obter mais informações sobre o conteúdo de uma mensagem de erro do SQL Server, veja [sys.messages (Transact-SQL)](https://msdn.microsoft.com/library/ms187382.aspx).

## Consulte também

[Gerenciar e solucionar problemas do Stretch Database](sql-server-stretch-database-manage.md)

<!--Image references-->
[StretchMonitorImage1]: ./media/sql-server-stretch-database-monitor/StretchDBMonitor.png

<!---HONumber=AcomDC_0518_2016-->