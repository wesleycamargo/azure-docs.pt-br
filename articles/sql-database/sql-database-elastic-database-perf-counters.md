<properties
    pageTitle="Contadores de desempenho do gerenciador de mapa de fragmentos"
    description="Classe do ShardMapManager e contadores de desempenho de roteamento dependente de dados"
    services="sql-database"
    documentationCenter=""
    manager="jhubbard"
    authors="SilviaDoomra"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/23/2016"
    ms.author="SilviaDoomra"/>

# Contadores de desempenho do gerenciador de mapa de fragmentos

Você pode capturar o desempenho de um [Gerenciador de mapa do fragmento](sql-database-elastic-scale-shard-map-management.md), especialmente ao usar [roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md). Contadores são criados com métodos da classe Microsoft.Azure.SqlDatabase.ElasticScale.Client.

Contadores são usados para controlar o desempenho de operações de [roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md). Esses contadores estão acessíveis no Monitor de Desempenho, na categoria "Banco de Dados Elástico: Gerenciamento de Fragmento".

**Para obter a versão mais recente:** acesse [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Consulte também [Atualizar um aplicativo para usar a biblioteca de cliente de banco de dados elástico mais recente](sql-database-elastic-scale-upgrade-client-library.md).

## Pré-requisitos

* Para criar a categoria de desempenho e contadores, o usuário deve ser uma parte do grupo **Administradores** local no computador que hospeda o aplicativo.  

* Para criar uma instância do contador de desempenho e atualizar os contadores, o usuário deve ser um membro do grupo **Administradores** ou **Usuários do Monitor de Desempenho**.

## Criar categoria e contadores de desempenho 

Para criar os contadores, chame o método CreatePeformanceCategoryAndCounters da [classe ShardMapManagmentFactory](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx). Somente um administrador pode executar o método:

	ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Você também pode usar [este](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) script do PowerShell para executar o método. O método cria os seguintes contadores de desempenho:

* **Mapeamentos em cache**: número de mapeamentos armazenados em cache para o mapa de fragmentos.
*  **Operações de DDR/s**: a taxa de operações de roteamento dependentes de dados para o mapa de fragmentos. Esse contador é atualizado quando uma chamada para [OpenConnectionForKey()](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) resulta em uma conexão bem-sucedida com o fragmento de destino. 
*  **Mapeamento de acertos de pesquisa do cache/s**: taxa de operações bem-sucedidas de pesquisa de cache para mapeamentos no mapa de fragmentos. 
*  **Mapeamento de erros de pesquisa de cache/s**: taxa de operações bem-sucedidas de pesquisa de cache para mapeamentos no mapa de fragmentos.
*  **Mapeamentos adicionados ou atualizados no cache/s**: taxa à qual os mapeamentos estão sendo adicionados ou atualizados no cache para o mapa de fragmentos. 
*  **Mapeamentos removidos do cache/s**: taxa à qual os mapeamentos estão sendo removidos do cache para o mapa de fragmentos. 

Contadores de desempenho são criados para cada mapa de fragmentos em cache por processo.


## Observações
Os seguintes eventos disparam a criação dos contadores de desempenho:

* Inicialização do [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) com [carregamento adiantado](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy.aspx), se o ShardMapManager contiver quaisquer mapas de fragmentos. Inclui os métodos [GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx?f=255&MSPPError=-2147217396#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerFactory.GetSqlShardMapManager%28System.String,Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerLoadPolicy%29) e [TryGetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx).
* Pesquisa bem-sucedida de um mapa de fragmentos (usando [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) ou [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)). 

* Criação bem-sucedida do mapa de fragmentos usando CreateShardMap().

Os contadores de desempenho serão atualizados por todas as operações de cache executadas no mapa do fragmentos e nos mapeamentos. A remoção bem-sucedida do mapa de fragmentos usando DeleteShardMap() resulta na exclusão da instância de contadores de desempenho.

## Práticas recomendadas

* A criação de categoria e dos contadores de desempenho deve ser feita apenas uma vez antes da criação do objeto ShardMapManager. Cada execução do comando CreatePerformanceCategoryAndCounters() limpa os contadores anteriores (perdendo dados relatados por todas as instâncias) e cria novos.  

* Instâncias de contador de desempenho são criadas por processo. Qualquer falha do aplicativo ou remoção de um mapa de fragmentos do cache resultará na exclusão das instâncias de contadores de desempenho.

### Consulte também

[Visão geral dos recursos do Banco de Dados Elástico](sql-database-elastic-scale-introduction.md)

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->

<!---HONumber=AcomDC_0608_2016-->