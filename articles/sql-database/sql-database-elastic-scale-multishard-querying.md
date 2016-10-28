<properties 
	pageTitle="Consulta de vários fragmentos | Microsoft Azure" 
	description="Execute consultas entre fragmentos usando a biblioteca de cliente de banco de dados elástico." 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="torsteng" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/12/2016" 
	ms.author="torsteng"/>

# Consulta de vários fragmentos

## Visão geral

Com as [ferramentas de Banco de Dados Elástico](sql-database-elastic-scale-introduction.md), você pode criar soluções de banco de dados fragmentado. A **Consulta de vários fragmentos** é usada para tarefas, como coleta/relatórios de dados que exigem a execução de uma consulta que se estende por vários fragmentos. (Compare-a com o [roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md), que executa todo o trabalho em um único fragmento).

## Visão geral

1. Obtenha um [**RangeShardMap**](https://msdn.microsoft.com/library/azure/dn807318.aspx) ou [**ListShardMap**](https://msdn.microsoft.com/library/azure/dn807370.aspx) usando o método [**TryGetRangeShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx), [**TryGetListShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx) ou [**GetShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx). Consulte [**Construindo um ShardMapManager**](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) e [**Obter um RangeShardMap ou ListShardMap**](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Crie um objeto **[MultiShardConnection](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection.aspx)**.
2. Crie um **[MultiShardCommand](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.aspx)**. 
3. Defina a **[propriedade CommandText](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.commandtext.aspx#P:Microsoft.Azure.SqlDatabase.ElasticScale.Query.MultiShardCommand.CommandText)** como um comando T-SQL.
3. Execute o comando chamando o **[método ExecuteReader](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.executereader.aspx)**.
4. Veja os resultados usando a classe **[MultiShardDataReader](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader.aspx)**. 

## Exemplo

O código a seguir ilustra o uso da consulta de vários fragmentos usando um determinado **ShardMap** chamado *myShardMap*.

    using (MultiShardConnection conn = new MultiShardConnection( 
                                        myShardMap.GetShards(), 
                                        myShardConnectionString) 
          ) 
    { 
    using (MultiShardCommand cmd = conn.CreateCommand())
           { 
            cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable"; 
            cmd.CommandType = CommandType.Text; 
            cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn; 
            cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults; 

            using (MultiShardDataReader sdr = cmd.ExecuteReader()) 
            	{ 
                	while (sdr.Read())
                    	{ 
                        	var c1Field = sdr.GetString(0); 
                        	var c2Field = sdr.GetFieldValue<int>(1); 
                        	var c3Field = sdr.GetFieldValue<Int64>(2);
                    	} 
             	} 
           } 
    } 

 
Uma diferença importante é a construção de fragmentos várias conexões. Enquanto a **SqlConnection** opera em um banco de dados individual, a **MultiShardConnection** usa um ***conjunto de fragmentos*** como sua entrada. Popule a coleção de fragmentos por meio de um mapa do fragmento. Em seguida, a consulta é executada na coleção de fragmentos usando a semântica **UNION ALL** para montar um único resultado geral. Como alternativa, o nome do fragmento que origina a linha pode ser adicionado à saída usando a propriedade **ExecutionOptions** no comando.

Observe a chamada para **myShardMap.GetShards()**. Esse método recupera todos os fragmentos do mapa de fragmentos e fornece uma maneira fácil de executar uma consulta em todos os bancos de dados relevantes. A coleção de fragmentos para uma consulta de vários fragmento pode ser refinada ainda mais executando uma consulta LINQ sobre a coleção retornada da chamada para **myShardMap.GetShards()**. Em combinação com a política de resultados parciais, a funcionalidade atual de consultas do fragmento vários foi projetado para funcionar bem para dezenas, centenas de fragmentos.

Uma limitação com as consultas de vários fragmentos no momento é a ausência de validação de fragmentos e shardlets são consultados. Enquanto o roteamento dependentes de dados verifica que um determinado fragmento faz parte do mapa do fragmento no momento da consulta, consultas de vários fragmentos não executam essa verificação. Isso pode levar à execução de consultas em vários fragmentos em bancos de dados que já foram removidos do mapa de fragmentos.

## Consultas de vários fragmentos e operações de divisão/mesclagem

Consultas de vários fragmentos não verificam se shardlets do banco de dados consultado estão participando de operações de divisão/mesclagem em andamento. (Veja [Escalando com a ferramenta de divisão/mesclagem de Banco de Dados Elástico](sql-database-elastic-scale-overview-split-and-merge.md)). Isso pode levar a inconsistências em que as linhas do mesmo shardlet são mostradas para vários bancos de dados na mesma consulta de vários fragmento. Lembre-se dessas limitações e considere drenar as operações de divisão/mesclagem em execução e alterações no mapa de fragmentos ao executar consultas de vários fragmentos.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

## Consulte também
Classes e métodos **[System.Data.SqlClient](http://msdn.microsoft.com/library/System.Data.SqlClient.aspx)**.


Gerenciar fragmentos usando a [biblioteca de cliente de Banco de Dados Elástico](sql-database-elastic-database-client-library.md). Ela inclui um namespace chamado [Microsoft.Azure.SqlDatabase.ElasticScale.Query](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.aspx) que fornece a capacidade de consultar vários fragmentos usando uma única consulta e resultado. Ele fornece uma abstração de consulta em uma coleção de fragmentos. Ele também fornece diretivas de execução alternativo, resultados parciais em particular, para lidar com falhas ao consultar sobre vários fragmentos.

 

<!---HONumber=AcomDC_0420_2016-->