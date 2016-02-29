<properties
    pageTitle="Configurar a replicação geográfica para o Banco de Dados SQL do Azure com o Transact-SQL | Microsoft Azure"
    description="Configurar a replicação geográfica para o Banco de Dados SQL do Azure usando o Transact-SQL"
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
    ms.date="02/12/2016"
    ms.author="carlrab"/>

# Configurar a replicação geográfica para o Banco de Dados SQL do Azure com o Transact-SQL



> [AZURE.SELECTOR]
- [Azure Portal](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [Transact-SQL](sql-database-geo-replication-transact-sql.md)


Este artigo mostra como configurar a replicação geográfica para um Banco de Dados SQL do Azure usando o Transact-SQL.


A replicação geográfica permite criar até quatro bancos de dados de réplica (secundários) em diferentes locais do datacenter (regiões). Os bancos de dados secundários estão disponíveis no caso de uma paralisação do data center ou da incapacidade de conectar ao banco de dados primário.

A replicação geográfica só está disponível para os bancos de dados Standard e Premium.

Os bancos de dados Standard podem ter um secundário não legível e devem usar a região recomendada. Os bancos de dados Premium podem ter até quatro secundários legíveis em qualquer uma das regiões disponíveis.


Para configurar a replicação geográfica, você precisa do seguinte:

- Uma assinatura do Azure - Se você não tiver uma assinatura do Azure, basta clicar em **AVALIAÇÃO GRATUITA** na parte superior desta página, em seguida, voltar para concluir este artigo.
- Um servidor do Banco de Dados SQL do Azure lógico <MyLocalServer> e um banco de dados SQL <MyDB> -O banco de dados primário que você deseja replicar em uma região geográfica diferente.
- Um ou mais servidores do Banco de Dados SQL do Azure lógicos < MySecondaryServer(n) > - Os servidores lógicos que serão os servidores parceiros no qual você criará os bancos de dados secundários de replicação geográfica.
- Um logon que é o DBManager no primário, ter o db\_ownership do banco de dados local que você replicará geograficamente e ser o DBManager no(s) servidor(es) parceiro(s) para o qual você irá configurar a replicação geográfica.
- A versão mais recente do SQL Server Management Studio - Para obter a versão mais recente do SQL Server Management Studio (SSMS), vá para [Baixar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx). Para obter informações sobre como usar o SQL Server Management Studio para gerenciar os servidores lógicos e os bancos de dados do Banco de Dados SQL do Azure, confira [Gerenciamento do Banco de Dados SQL do Azure usando o SQL Server Management Studio](sql-database-manage-azure-ssms.md)

## Adicionar banco de dados secundário

Você pode usar a instrução **ALTER DATABASE** para criar um banco de dados secundário replicado geograficamente em um servidor parceiro. Você executa essa instrução no banco de dados mestre do servidor que contém o banco de dados a ser replicado. O banco de dados replicado geograficamente (o "banco de dados primário") terá o mesmo nome do banco de dados sendo replicado e, por padrão, terá o mesmo nível de serviço do banco de dados primário. O banco de dados secundário pode ser legível ou não legível, e pode ser um único banco de dados ou um banco de dados elástico. Para obter mais informações, confira [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) e [Camadas de Serviço](sql-database-service-tiers.md). Depois do banco de dados secundário ser criado e propagado, os dados começarão a replicação assíncrona a partir do banco de dados primário. As etapas a seguir descrevem como configurar a replicação geográfica usando o Management Studio. As etapas para criar secundários não legíveis e legíveis, com um banco de dados individual ou um banco de dados elástico, são fornecidas.

> [AZURE.NOTE] Se o banco de dados secundário existir no servidor parceiro especificado (por exemplo, porque no momento existe uma relação de replicação geográfica ou existiu anteriormente), o comando falhará.


### Adicionar um secundário não legível (banco de dados individual)

Use as seguintes etapas para criar um secundário não legível como um banco de dados individual.

1. Com a versão 13.0.600.65 ou posterior do SQL Server Management Studio.

 	 > [AZURE.IMPORTANT] Baixe a versão [mais recente](https://msdn.microsoft.com/library/mt238290.aspx) do SQL Server Management Studio. É recomendável usar sempre a versão mais recente do Management Studio para continuar em sincronia com as atualizações do portal do Azure.


2. Abra a pasta Bancos de Dados, expanda a pasta **Bancos de Dados do Sistema**, clique com o botão direito do mouse em **mestre** e, em seguida, clique em **Nova Consulta**.

3. Use a seguinte instrução **ALTER DATABASE** para transformar um banco de dados local em uma replicação geográfica primária com um banco de dados secundário ilegível em MySecondaryServer1, no qual MySecondaryServer1 é o nome amigável do servidor.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer1> WITH (ALLOW_CONNECTIONS = NO);

4. Clique em **Execute** para executar a consulta.


### Adicionar um secundário legível (banco de dados individual)
Use as seguintes etapas para criar um secundário legível como um banco de dados individual.

1. No Management Studio, conecte seu servidor lógico do Banco de Dados SQL do Azure.

2. Abra a pasta Bancos de Dados, expanda a pasta **Bancos de Dados do Sistema**, clique com o botão direito do mouse em **mestre** e, em seguida, clique em **Nova Consulta**.

3. Use a seguinte instrução **ALTER DATABASE** para transformar um banco de dados local em uma replicação geográfica primária com um banco de dados secundário legível em um servidor secundário.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer2> WITH (ALLOW_CONNECTIONS = ALL);

4. Clique em **Execute** para executar a consulta.



### Adicionar um secundário não legível (banco de dados elástico)
Use as seguintes etapas para criar um secundário não legível como um banco de dados elástico.

1. No Management Studio, conecte seu servidor lógico do Banco de Dados SQL do Azure.

2. Abra a pasta Bancos de Dados, expanda a pasta **Bancos de Dados do Sistema**, clique com o botão direito do mouse em **mestre** e, em seguida, clique em **Nova Consulta**.

3. Use a seguinte instrução **ALTER DATABASE** para transformar um banco de dados local em uma replicação geográfica primária com um banco de dados secundário não legível em um servidor secundário em um pool elástico.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer3> WITH (ALLOW_CONNECTIONS = NO
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool1));

4. Clique em **Execute** para executar a consulta.



### Adicionar um secundário legível (banco de dados elástico)
Use as seguintes etapas para criar um secundário legível como um banco de dados elástico.

1. No Management Studio, conecte seu servidor lógico do Banco de Dados SQL do Azure.

2. Abra a pasta Bancos de Dados, expanda a pasta **Bancos de Dados do Sistema**, clique com o botão direito do mouse em **mestre** e, em seguida, clique em **Nova Consulta**.

3. Use a seguinte instrução **ALTER DATABASE** para transformar um banco de dados local em uma replicação geográfica primária com um banco de dados secundário legível em um servidor secundário em um pool elástico.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer4> WITH (ALLOW_CONNECTIONS = ALL
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool2));

4. Clique em **Execute** para executar a consulta.



## Remover banco de dados secundário

Você pode usar a instrução **ALTER DATABASE** para encerrar permanentemente a parceria de replicação entre um banco de dados secundário e seu primário. Essa instrução é executada no banco de dados mestre no qual reside o banco de dados primário. Após o encerramento da relação, o banco de dados secundário se torna um banco de dados de leitura/gravação normal. Se a conectividade com o banco de dados secundário for interrompida, o comando terá êxito, mas o secundário se tornará de leitura/gravação após a conectividade ser restaurada. Para obter mais informações, confira [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) e [Camadas de Serviço](sql-database-service-tiers.md).

Use as seguintes etapas para remover um secundário replicado geograficamente de uma parceria de replicação geográfica.

1. No Management Studio, conecte seu servidor lógico do Banco de Dados SQL do Azure.

2. Abra a pasta Bancos de Dados, expanda a pasta **Bancos de Dados do Sistema**, clique com o botão direito do mouse em **mestre** e, em seguida, clique em **Nova Consulta**.

3. Use a seguinte instrução **ALTER DATABASE** para remover um secundário replicado geograficamente.

        ALTER DATABASE <MyDB>
           REMOVE SECONDARY ON SERVER <MySecondaryServer1>;

4. Clique em **Execute** para executar a consulta.


## Iniciar um failover planejado promovendo um banco de dados secundário para se tornar o novo primário

Você pode usar a instrução **ALTER DATABASE** para promover um banco de dados secundário para se tornar o novo banco de dados primário de maneira planejada, rebaixando o primário existente para se tornar um secundário. Essa instrução é executada no banco de dados mestre no servidor lógico do Banco de Dados SQL do Azure no qual reside o banco de dados secundário replicado geograficamente que está sendo promovido. Essa funcionalidade é designada para o failover planejado, como durante os exercícios de recuperação de desastres, e requer que o banco de dados primário esteja disponível.

O comando executa o seguinte fluxo de trabalho:

1. Alterna temporariamente a replicação para o modo síncrono, fazendo com que todas as transações pendentes sejam enviadas para o secundário e bloqueando todas as novas transações;

2. Alterna as funções dos dois bancos de dados na parceria de replicação geográfica.

Essa sequência garante que não ocorrerá nenhuma perda de dados. Há um breve período durante o qual os bancos de dados não estão disponíveis (na ordem de 0 a 25 segundos) enquanto as funções são alternadas. A operação inteira deve levar menos de um minuto para ser concluída em circunstâncias normais. Para obter mais informações, confira [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) e [Camadas de serviço](sql-database-service-tiers.md).


> [AZURE.NOTE] Se o banco de dados primário não estiver disponível quando o comando for emitido, o comando falhará com uma mensagem de erro indicando que o servidor primário não está disponível. Em situações raras, é possível que a operação não seja concluída e pareça paralisada. Nesse caso, o usuário pode executar o comando de failover à força e aceitar a perda de dados.

Use as etapas a seguir para iniciar um failover planejado.

1. No Management Studio, conecte o servidor lógico do Banco de Dados SQL do Azure no qual reside o banco de dados secundário replicado geograficamente.

2. Abra a pasta Bancos de Dados, expanda a pasta **Bancos de Dados do Sistema**, clique com o botão direito do mouse em **mestre** e, em seguida, clique em **Nova Consulta**.

3. Use a seguinte instrução **ALTER DATABASE** para transformar o banco de dados replicado geograficamente em uma replicação geográfica primária com um banco de dados secundário legível em <MySecondaryServer4> em <ElasticPool2>.

        ALTER DATABASE <MyDB> FAILOVER;

4. Clique em **Execute** para executar a consulta.



## Iniciar um failover não planejado do banco de dados primário para o banco de dados secundário

Você pode usar a instrução **ALTER DATABASE** para promover um banco de dados secundário para se tornar o novo banco de dados primário de maneira planejada, rebaixando o primário existente para se tornar um secundário quando o banco de dados primário não estiver mais disponível. Essa instrução é executada no banco de dados mestre no servidor lógico do Banco de Dados SQL do Azure no qual reside o banco de dados secundário replicado geograficamente que está sendo promovido.

Essa funcionalidade foi designada para a recuperação de desastres quando a restauração da disponibilidade do banco de dados é fundamental e a perda de dados é aceitável. Quando o failover forçado é chamado, o banco de dados secundário especificado imediatamente se torna o banco de dados primário e começa a aceitar as transações de gravação. Assim que o banco de dados primário original for capaz de se reconectar com o novo banco de dados primário, um backup incremental será realizado no banco de dados primário original e o antigo banco de dados primário será transformado em um banco de dados secundário para o novo banco de dados primário; em seguida, será simplesmente uma réplica de sincronização do novo primário.

No entanto, como a Restauração Pontual não é compatível com os bancos de dados secundários, se o usuário quiser recuperar os dados confirmados no antigo banco de dados primário que não foi replicado no novo banco de dados primário antes de ocorrer o failover forçado, ele precisará empregar o suporte para recuperar essa perda de dados.

> [AZURE.NOTE] Se o comando for emitido quando o primário e o secundário estiverem online, o antigo primário se tornará o novo secundário, mas não haverá uma tentativa de sincronização dos dados. Então, poderá ocorrer uma perda de dados.


Se o banco de dados primário tiver vários bancos de dados secundários, o comando terá êxito apenas no servidor secundário, no qual o comando foi executado. No entanto, os outros secundários não saberão que ocorreu um failover forçado. O usuário terá que corrigir manualmente essa configuração usando uma API para "remover secundário" e, em seguida, reconfigurar a replicação geográfica nesses secundários adicionais.

Use as seguintes etapas para remover à força um secundário replicado geograficamente de uma parceria de replicação geográfica.

1. No Management Studio, conecte o servidor lógico do Banco de Dados SQL do Azure no qual reside o banco de dados secundário replicado geograficamente.

2. Abra a pasta Bancos de Dados, expanda a pasta **Bancos de Dados do Sistema**, clique com o botão direito do mouse em **mestre** e, em seguida, clique em **Nova Consulta**.

3. Use a seguinte instrução **ALTER DATABASE** para transformar <MyLocalDB> em uma replicação geográfica primária com um banco de dados secundário legível em <MySecondaryServer4> em <ElasticPool2>.

        ALTER DATABASE <MyDB>   FORCE_FAILOVER_ALLOW_DATA_LOSS;

4. Clique em **Execute** para executar a consulta.

## Monitorar a configuração e a integridade da replicação geográfica

Monitorar as tarefas inclui o monitoramento da configuração de replicação geográfica e da integridade da replicação dos dados. Você pode usar a exibição de gerenciamento dinâmica **sys.dm\_geo\_replication\_links** no banco de dados mestre para retornar informações sobre todos os links de replicação existentes para cada banco de dados no servidor lógico do Banco de Dados SQL do Azure. Essa exibição contém uma linha para cada link de replicação entre os bancos de dados primários e secundários. Você pode usar a exibição de gerenciamento dinâmica **sys.dm\_replication\_status** para retornar uma linha para cada Banco de Dados SQL do Azure atualmente envolvido em um link de replicação. Isso inclui os bancos de dados primários e secundários. Se houver mais de um link de replicação contínua para um determinado banco de dados primário, essa tabela conterá uma linha para cada uma das relações. A exibição é criada em todos os bancos de dados, incluindo o mestre lógico. No entanto, consultar essa exibição no mestre lógico retorna um conjunto vazio. Você pode usar a exibição de gerenciamento dinâmica **sys.dm\_operation\_status** para mostrar o status de todas as operações do banco de dados, incluindo o status dos links de replicação. Para obter mais informações, confira [sys.geo\_replication\_links (Banco de Dados SQL do Azure)](https://msdn.microsoft.com/library/mt575501.aspx), [sys.dm\_geo\_replication\_link\_status (Banco de Dados SQL do Azure)](https://msdn.microsoft.com/library/mt575504.aspx) e [sys.dm\_operation\_status (Banco de Dados SQL do Azure)](https://msdn.microsoft.com/library/dn270022.aspx).

Use as etapas a seguir para monitorar uma parceria de replicação geográfica.

1. No Management Studio, conecte seu servidor lógico do Banco de Dados SQL do Azure.

2. Abra a pasta Bancos de Dados, expanda a pasta **Bancos de Dados do Sistema**, clique com o botão direito do mouse em **mestre** e, em seguida, clique em **Nova Consulta**.

3. Use a seguinte instrução para mostrar todos os bancos de dados com links de replicação geográfica.

        SELECT database_id, start_date, modify_date, partner_server, partner_database, replication_state_desc, role, secondary_allow_connections_desc FROM [sys].geo_replication_links;

4. Clique em **Execute** para executar a consulta.
5. Abra a pasta Bancos de Dados, expanda a pasta **Bancos de Dados do Sistema**, clique com o botão direito do mouse em **MyDB** e, em seguida, clique em **Nova Consulta**.
6. Use a seguinte instrução para mostrar os intervalos de replicação e a hora da última replicação de meus bancos de dados secundários do MyDB.

        SELECT link_guid, partner_server, last_replication, replication_lag_sec FROM sys.dm_geo_replication_link_status

7. Clique em **Execute** para executar a consulta.
8. Use a seguinte instrução para mostrar as operações de replicação geográfica mais recentes associadas ao banco de dados MyDB.

        SELECT * FROM sys.dm_operation_status where major_resource_is = 'MyDB'
        ORDER BY start_time DESC

9. Clique em **Execute** para executar a consulta.


## Próximas etapas

- [Executar análise de recuperação de desastres](sql-database-disaster-recovery-drills.md)


## Recursos adicionais

- [Destacar os novos recursos de replicação geográfica](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- [Projetando aplicativos de nuvem para a continuidade de negócios usando a replicação geográfica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Documentação do Banco de Dados SQL](https://azure.microsoft.com/services/sql-database/)

<!---HONumber=AcomDC_0218_2016-->