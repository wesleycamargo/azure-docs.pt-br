---
title: "Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure com o Transact-SQL | Microsoft Docs"
description: "Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure usando o Transact-SQL"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 5eb2d256-025d-4f5a-99d4-17f702b37f14
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 01/10/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 473d6195acf7867c3dd1348ff8644d0c3a26f986


---
# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-transact-sql"></a>Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure com o Transact-SQL
> [!div class="op_single_selector"]
> * [Portal do Azure](sql-database-geo-replication-failover-portal.md)
> * [PowerShell](sql-database-geo-replication-failover-powershell.md)
> * [T-SQL](sql-database-geo-replication-failover-transact-sql.md)
> 
> 

Este artigo mostra como iniciar o failover para um Banco de Dados SQL secundário usando o Transact-SQL. Para configurar a Replicação Geográfica, consulte [Configurar a Replicação Geográfica para o Banco de Dados SQL do Azure](sql-database-geo-replication-transact-sql.md).

Para iniciar o failover, você precisará do seguinte:

* Um logon que é o DBManager no primário, ter o db_ownership do banco de dados local que você replicará geograficamente e ser o DBManager no servidor parceiro para o qual você vai configurar a Replicação Geográfica.
* SQL Server Management Studio (SSMS)

> [!IMPORTANT]
> Recomendamos que você sempre use a versão mais recente do Management Studio a fim de permanecer sincronizado com as atualizações no Microsoft Azure e no Banco de Dados SQL. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="initiate-a-planned-failover-promoting-a-secondary-database-to-become-the-new-primary"></a>Iniciar um failover planejado promovendo um banco de dados secundário para se tornar o novo primário
Você pode usar a instrução **ALTER DATABASE** para promover um banco de dados secundário para se tornar o novo banco de dados primário de maneira planejada, rebaixando o primário existente para se tornar um secundário. Essa instrução é executada no banco de dados mestre no servidor lógico do Banco de Dados SQL do Azure no qual reside o banco de dados secundário replicado geograficamente que está sendo promovido. Essa funcionalidade é designada para o failover planejado, como durante os exercícios de recuperação de desastres, e requer que o banco de dados primário esteja disponível.

O comando executa o seguinte fluxo de trabalho:

1. Alterna temporariamente a replicação para o modo síncrono, fazendo com que todas as transações pendentes sejam enviadas para o secundário e bloqueando todas as novas transações;
2. Alterna as funções dos dois bancos de dados na parceria de Replicação Geográfica.  

Essa sequência garante que os dois bancos de dados estejam sincronizados antes que as funções sejam alternadas, de forma que não ocorra nenhuma perda de dados. Há um breve período durante o qual os bancos de dados não estão disponíveis (na ordem de 0 a 25 segundos) enquanto as funções são alternadas. Se o banco de dados primário tiver vários bancos de dados secundários, o comando reconfigurará automaticamente os outros secundários para se conectar ao novo primário.  A operação inteira deve levar menos de um minuto para ser concluída em circunstâncias normais. Para saber mais, confira [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) e [Camadas de Serviço](sql-database-service-tiers.md).

Use as etapas a seguir para iniciar um failover planejado.

1. No Management Studio, conecte o servidor lógico do Banco de Dados SQL do Azure no qual reside o banco de dados secundário replicado geograficamente.
2. Abra a pasta Bancos de Dados, expanda a pasta **Bancos de Dados do Sistema**, clique com o botão direito do mouse em **mestre** e, em seguida, clique em **Nova Consulta**.
3. Use a seguinte instrução **ALTER DATABASE** para trocar o banco de dados secundário para a função primária.
   
        ALTER DATABASE <MyDB> FAILOVER;
4. Clique em **Execute** para executar a consulta.

> [!NOTE]
> Em situações raras, é possível que a operação não seja concluída e pareça paralisada. Nesse caso, o usuário pode executar o comando de failover à força e aceitar a perda de dados.
> 
> 

## <a name="initiate-an-unplanned-failover-from-the-primary-database-to-the-secondary-database"></a>Iniciar um failover não planejado do banco de dados primário para o banco de dados secundário
Você pode usar a instrução **ALTER DATABASE** para promover um banco de dados secundário para se tornar o novo banco de dados primário de maneira planejada, rebaixando o primário existente para se tornar um secundário quando o banco de dados primário não estiver mais disponível. Essa instrução é executada no banco de dados mestre no servidor lógico do Banco de Dados SQL do Azure no qual reside o banco de dados secundário replicado geograficamente que está sendo promovido.

Essa funcionalidade foi designada para a recuperação de desastres quando a restauração da disponibilidade do banco de dados é fundamental e a perda de dados é aceitável. Quando o failover forçado é chamado, o banco de dados secundário especificado imediatamente se torna o banco de dados primário e começa a aceitar as transações de gravação. Assim que o banco de dados primário original for capaz de se reconectar com o novo banco de dados primário, um backup incremental será realizado no banco de dados primário original e o antigo banco de dados primário será transformado em um banco de dados secundário para o novo banco de dados primário; em seguida, será simplesmente uma réplica de sincronização do novo primário.

No entanto, como a Restauração Pontual não é compatível com os bancos de dados secundários, se o usuário quiser recuperar os dados confirmados no antigo banco de dados primário que não foi replicado no novo banco de dados primário antes de ocorrer o failover forçado, ele precisará empregar o suporte para recuperar essa perda de dados.

Se o banco de dados primário tiver vários bancos de dados secundários, o comando reconfigurará automaticamente os outros secundários para se conectar ao novo primário.

Use as etapas a seguir para iniciar um failover não planejado.

1. No Management Studio, conecte o servidor lógico do Banco de Dados SQL do Azure no qual reside o banco de dados secundário replicado geograficamente.
2. Abra a pasta Bancos de Dados, expanda a pasta **Bancos de Dados do Sistema**, clique com o botão direito do mouse em **mestre** e, em seguida, clique em **Nova Consulta**.
3. Use a seguinte instrução **ALTER DATABASE** para trocar o banco de dados secundário para a função primária.
   
        ALTER DATABASE <MyDB>   FORCE_FAILOVER_ALLOW_DATA_LOSS;
4. Clique em **Execute** para executar a consulta.

> [!NOTE]
> Se o comando for emitido quando o primário e o secundário estiverem online, o antigo primário se tornará o novo secundário, imediatamente e sem sincronização de dados. Se o primário estiver realizando transações quando o comando for emitido, alguma perda de dados poderá ocorrer.
> 
> 

## <a name="next-steps"></a>Próximas etapas
* Após o failover, verifique se os requisitos de autenticação para o servidor e o banco de dados estão configurados no novo primário. Para obter detalhes, consulte [Segurança do Banco de Dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md).
* Para saber mais sobre a recuperação após um desastre usando a replicação geográfica ativa, incluindo as etapas anteriores e posteriores à recuperação e como executar uma análise de recuperação de desastre, confira [Recuperação de Desastre](sql-database-disaster-recovery.md)
* Para ver uma postagem de blog de Sasha Nosov sobre a replicação geográfica ativa, confira [Spotlight on new Geo-Replication capabilities](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
* Para obter informações sobre a criação de aplicativos de nuvem para usar a replicação geográfica ativa, confira [Projetando aplicativos de nuvem para continuidade de negócios usando a Replicação Geográfica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* Para obter informações sobre o uso da Replicação geográfica ativa com pools elásticos, consulte [Elastic Pool disaster recovery strategies](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)(Estratégias de recuperação de desastre do pool elástico).
* Para obter uma visão geral sobre a continuidade dos negócios, confira [Visão geral de continuidade dos negócios](sql-database-business-continuity.md)




<!--HONumber=Dec16_HO2-->


