<properties
    pageTitle="Monitorar e gerenciar um pool de banco de dados elástico com C# | Microsoft Azure"
    description="Use técnicas de desenvolvimento de banco de dados C# para gerenciar um pool de banco de dados elástico do Banco de Dados SQL do Azure."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="csharp"
    ms.workload="data-management"
    ms.date="10/04/2016"
    ms.author="sstein"/>


# <a name="monitor-and-manage-an-elastic-database-pool-with-c&#x23;"></a>Monitorar e gerenciar um pool de banco de dados elástico com C&#x23; 

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)


Saiba como gerenciar um [pool de banco de dados elástico](sql-database-elastic-pool.md) usando C&#x23;. 

>[AZURE.NOTE] Muitos recursos novos do Banco de Dados SQL só têm suporte quando você está usando o [Modelo de implantação do Azure Resource Manager](../resource-group-overview.md), portanto, você sempre deve usar a versão mais recente da **Biblioteca de Gerenciamento do Banco de Dados SQL do Azure para .NET ([documentos](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [Pacote do NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. As [bibliotecas com base no modelo de implantação clássico](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) mais antigas têm suporte para a compatibilidade com versões anteriores, portanto, é recomendável usar as bibliotecas baseadas no Gerenciador de Recursos mais recentes.

Para concluir as etapas neste artigo, você precisa dos seguintes itens:

- Um pool elástico (o pool que você deseja gerenciar). Para criar um pool, consulte [Criar um pool de banco de dados elástico com o C#](sql-database-elastic-pool-create-csharp.md).
- Visual Studio. Para obter uma cópia gratuita do Visual Studio, consulte a página [Downloads do Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) .


## <a name="move-a-database-into-an-elastic-pool"></a>Mover um banco de dados para um pool elástico

Você pode mover um banco de dados autônomo para dentro ou para fora de um pool.  

    // Retrieve current database properties.

    currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation,
        }
    };

    // Update the database.
    var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);

## <a name="list-databases-in-an-elastic-pool"></a>Listar bancos de dados em um pool elástico

Para recuperar todos os bancos de dados em um pool, chame o método [ListDatabases](https://msdn.microsoft.com/library/microsoft.azure.management.sql.elasticpooloperationsextensions.listdatabases) .

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }

## <a name="change-performance-settings-of-a-pool"></a>Alterar as configurações de desempenho de um pool

Recuperar as propriedades do pool existente. Modifique os valores e execute o método CreateOrUpdate.

    var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

    // Configure create or update parameters with existing property values, override those to be changed.
    ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = currentPool.Location,
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = currentPool.Properties.Edition,
            DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
            DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
            Dtu = (int)currentPool.Properties.Dtu,
            StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
        }
    };

    newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);


## <a name="latency-of-elastic-pool-operations"></a>Latência de operações do pool elástico

- Normalmente, a alteração das eDTUs mínimas ou das eDTUs máximas por banco de dados é um processo concluído em cinco minutos ou menos.
- O tempo levado para alterar o tamanho do pool (eDTUs) depende do tamanho combinado de todos os bancos de dados no pool. As alterações levam, em média, 90 minutos ou menos a cada 100 GB. Por exemplo, se o espaço total de todos os bancos de dados no pool for de 200 GB, a latência prevista para alterar os eDTUs do pool será de 3 horas por pool ou menos.




## <a name="additional-resources"></a>Recursos adicionais

- [Códigos de erro de SQL para aplicativos cliente do Banco de Dados SQL: erro de conexão de banco de dados e outros problemas](sql-database-develop-error-messages.md).
- [Banco de Dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)
- [APIs de Gerenciamento de Recursos do Azure.](https://msdn.microsoft.com/library/azure/dn948464.aspx)
- [Criar um pool de banco de dados elástico com C#](sql-database-elastic-pool-create-csharp.md)
- [Quando um Pool de Banco de Dados Elástico deve ser usado?](sql-database-elastic-pool-guidance.md)
- Consulte [Escalando horizontalmente com o Banco de Dados SQL do Azure](sql-database-elastic-scale-introduction.md): use ferramentas do banco de dados elástico para escalar horizontalmente, mover os dados, consultar ou criar transações.




<!--HONumber=Oct16_HO2-->


