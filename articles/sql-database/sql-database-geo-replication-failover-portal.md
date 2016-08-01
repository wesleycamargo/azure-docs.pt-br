<properties 
    pageTitle="Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure com o portal do Azure | Microsoft Azure" 
    description="Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure usando o portal do Azure" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="07/19/2016"
    ms.author="sstein"/>

# Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure com o portal do Azure


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


Este artigo mostra como iniciar o failover para um Banco de Dados SQL secundário usando o [portal do Azure](http://portal.azure.com). Para configurar a Replicação Geográfica, consulte [Configurar a Replicação Geográfica para o Banco de Dados SQL do Azure](sql-database-geo-replication-portal.md).


## Iniciar um failover

O banco de dados secundário pode ser alternado para se tornar primário.

1. No [Portal do Azure](http://portal.azure.com), procure o banco de dados primário na parceria de Replicação Geográfica.
2. Na folha Banco de Dados SQL, selecione **Todas as configurações** > **Replicação Geográfica**.
3. Na lista **SECUNDÁRIOS**, selecione o banco de dados que deverá se tornar o novo primário e clique em **Failover**.

    ![failover][2]

4. Clique em **Sim** para iniciar o failover.

O comando alterará imediatamente o banco de dados secundário para a função primária.

Há um breve período durante o qual os bancos de dados não estão disponíveis (na ordem de 0 a 25 segundos) enquanto as funções são alternadas. Se o banco de dados primário tiver vários bancos de dados secundários, o comando reconfigurará automaticamente os outros secundários para se conectar ao novo primário. A operação inteira deve levar menos de um minuto para ser concluída em circunstâncias normais.

>[AZURE.NOTE] Se o primário estiver online e realizando transações quando o comando for emitido, alguma perda de dados poderá ocorrer.


## Recursos adicionais   


- [Executar análise de recuperação de desastres](sql-database-disaster-recovery-drills.md)
- [Destacar novos recursos de Replicação Geográfica](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- [Projetando aplicativos de nuvem para continuidade de negócios usando a Replicação Geográfica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Documentação do banco de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-geo-replication-failover-portal/failover.png
[2]: ./media/sql-database-geo-replication-failover-portal/secondaries.png

<!---HONumber=AcomDC_0720_2016-->