---
title: Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure com o portal do Azure | Microsoft Docs
description: Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure usando o portal do Azure
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 08/29/2016
ms.author: sstein

---
# Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure com o portal do Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](sql-database-geo-replication-failover-portal.md)
> * [PowerShell](sql-database-geo-replication-failover-powershell.md)
> * [T-SQL](sql-database-geo-replication-failover-transact-sql.md)
> 
> 

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

> [!NOTE]
> Se o primário estiver online e realizando transações quando o comando for emitido, alguma perda de dados poderá ocorrer.
> 
> 

## Próximas etapas
* Após o failover, verifique se os requisitos de autenticação para o servidor e o banco de dados estão configurados no novo primário. Para obter detalhes, consulte [Segurança do Banco de Dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md).
* Para saber mais sobre a recuperação após um desastre usando a replicação geográfica ativa, incluindo as etapas anteriores e posteriores à recuperação e como executar uma análise de recuperação de desastre, confira [Disaster Recovery Drills](sql-database-disaster-recovery.md) (Análises de recuperação de desastre)
* Para ver uma postagem de blog de Sasha Nosov sobre a replicação geográfica ativa, confira [Spotlight on new Geo-Replication capabilities](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/) (Destaque para os novos recursos de replicação geográfica ativa)
* Para obter informações sobre a criação de aplicativos de nuvem para usar a replicação geográfica ativa, confira [Projetando aplicativos de nuvem para continuidade de negócios usando a Replicação Geográfica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* Para obter informações sobre como usar a replicação geográfica ativa com o Pool de Banco de Dados Elástico, confira [Elastic Pool disaster recovery strategies](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) (Estratégias de recuperação de desastre do pool elástico).
* Para obter uma visão geral sobre a continuidade dos negócios, confira [Visão geral de continuidade dos negócios](sql-database-business-continuity.md)

<!--Image references-->
[1]: ./media/sql-database-geo-replication-failover-portal/failover.png
[2]: ./media/sql-database-geo-replication-failover-portal/secondaries.png

<!---HONumber=AcomDC_0831_2016-->