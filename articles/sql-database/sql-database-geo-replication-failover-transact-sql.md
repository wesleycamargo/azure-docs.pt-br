<properties 
    pageTitle="Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure com o Transact-SQL | Microsoft Azure" 
    description="Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure usando o Transact-SQL" 
    services="sql-database" 
    documentationCenter="" 
    authors="carlrabeler" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management"
    ms.date="04/27/2016"
    ms.author="carlrab"/>

# Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure com o Transact-SQL


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


Este artigo mostra como iniciar o failover para um Banco de Dados SQL secundário usando o Transact-SQL. Para configurar a Replicação Geográfica, consulte [Configurar a Replicação Geográfica para o Banco de Dados SQL do Azure](sql-database-geo-replication-transact-sql.md).



Para iniciar o failover, você precisará do seguinte:

- Um logon que é o DBManager no primário, ter o db\_ownership do banco de dados local que você replicará geograficamente e ser o DBManager no servidor parceiro para o qual você vai configurar a Replicação Geográfica.
- A versão mais recente do SQL Server Management Studio - Para obter a versão mais recente do SQL Server Management Studio (SSMS), vá para [Baixar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx). Para obter informações sobre como usar o SQL Server Management Studio para gerenciar os servidores lógicos e os bancos de dados do Banco de Dados SQL do Azure, confira [Gerenciamento do Banco de Dados SQL do Azure usando o SQL Server Management Studio](sql-database-manage-azure-ssms.md)



## Iniciar um failover planejado promovendo um banco de dados secundário para se tornar o novo primário

Você pode usar a instrução **ALTER DATABASE** para promover um banco de dados secundário para se tornar o novo banco de dados primário de maneira planejada, rebaixando o primário existente para se tornar um secundário. Essa instrução é executada no banco de dados mestre no servidor lógico do Banco de Dados SQL do Azure no qual reside o banco de dados secundário replicado geograficamente que está sendo promovido. Essa funcionalidade é designada para o failover planejado, como durante os exercícios de recuperação de desastres, e requer que o banco de dados primário esteja disponível.

O comando executa o seguinte fluxo de trabalho:

1. Alterna temporariamente a replicação para o modo síncrono, fazendo com que todas as transações pendentes sejam enviadas para o secundário e bloqueando todas as novas transações;

2. Alterna as funções dos dois bancos de dados na parceria de Replicação Geográfica.

Essa sequência garante que os dois bancos de dados estejam sincronizados antes que as funções sejam alternadas, de forma que não ocorra nenhuma perda de dados. Há um breve período durante o qual os bancos de dados não estão disponíveis (na ordem de 0 a 25 segundos) enquanto as funções são alternadas. Se o banco de dados primário tiver vários bancos de dados secundários, o comando reconfigurará automaticamente os outros secundários para se conectar ao novo primário. A operação inteira deve levar menos de um minuto para ser concluída em circunstâncias normais. Para obter mais informações, confira [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) e [Camadas de serviço](sql-database-service-tiers.md).


Use as etapas a seguir para iniciar um failover planejado.

1. No Management Studio, conecte o servidor lógico do Banco de Dados SQL do Azure no qual reside o banco de dados secundário replicado geograficamente.

2. Abra a pasta Bancos de Dados, expanda a pasta **Bancos de Dados do Sistema**, clique com o botão direito do mouse em **mestre** e, em seguida, clique em **Nova Consulta**.

3. Use a seguinte instrução **ALTER DATABASE** para alternar o banco de dados secundário para a função primária.

        ALTER DATABASE <MyDB> FAILOVER;

4. Clique em **Execute** para executar a consulta.

>[AZURE.NOTE] Em situações raras, é possível que a operação não seja concluída e pareça paralisada. Nesse caso, o usuário pode executar o comando de failover à força e aceitar a perda de dados.


## Iniciar um failover não planejado do banco de dados primário para o banco de dados secundário

Você pode usar a instrução **ALTER DATABASE** para promover um banco de dados secundário para se tornar o novo banco de dados primário de maneira planejada, rebaixando o primário existente para se tornar um secundário quando o banco de dados primário não estiver mais disponível. Essa instrução é executada no banco de dados mestre no servidor lógico do Banco de Dados SQL do Azure no qual reside o banco de dados secundário replicado geograficamente que está sendo promovido.

Essa funcionalidade foi designada para a recuperação de desastres quando a restauração da disponibilidade do banco de dados é fundamental e a perda de dados é aceitável. Quando o failover forçado é chamado, o banco de dados secundário especificado imediatamente se torna o banco de dados primário e começa a aceitar as transações de gravação. Assim que o banco de dados primário original for capaz de se reconectar com o novo banco de dados primário, um backup incremental será realizado no banco de dados primário original e o antigo banco de dados primário será transformado em um banco de dados secundário para o novo banco de dados primário; em seguida, será simplesmente uma réplica de sincronização do novo primário.

No entanto, como a Restauração Pontual não é compatível com os bancos de dados secundários, se o usuário quiser recuperar os dados confirmados no antigo banco de dados primário que não foi replicado no novo banco de dados primário antes de ocorrer o failover forçado, ele precisará empregar o suporte para recuperar essa perda de dados.

Se o banco de dados primário tiver vários bancos de dados secundários, o comando reconfigurará automaticamente os outros secundários para se conectar ao novo primário.

Use as etapas a seguir para iniciar um failover não planejado.

1. No Management Studio, conecte o servidor lógico do Banco de Dados SQL do Azure no qual reside o banco de dados secundário replicado geograficamente.

2. Abra a pasta Bancos de Dados, expanda a pasta **Bancos de Dados do Sistema**, clique com o botão direito do mouse em **mestre** e, em seguida, clique em **Nova Consulta**.

3. Use a seguinte instrução **ALTER DATABASE** para alternar o banco de dados secundário para a função primária.

        ALTER DATABASE <MyDB>   FORCE_FAILOVER_ALLOW_DATA_LOSS;

4. Clique em **Execute** para executar a consulta.

>[AZURE.NOTE] Se o comando for emitido quando o primário e o secundário estiverem online, o antigo primário se tornará o novo secundário, imediatamente e sem sincronização de dados. Se o primário estiver realizando transações quando o comando for emitido, alguma perda de dados poderá ocorrer.



## Recursos adicionais

- [Destacar novos recursos de Replicação Geográfica](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- [Projetando aplicativos de nuvem para continuidade de negócios usando a Replicação Geográfica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Documentação do Banco de Dados SQL](https://azure.microsoft.com/services/sql-database/)
- [Executar análise de recuperação de desastres](sql-database-disaster-recovery-drills.md)

<!---HONumber=AcomDC_0615_2016-->