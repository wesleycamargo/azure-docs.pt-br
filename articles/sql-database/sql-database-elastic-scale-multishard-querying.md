<properties 
	pageTitle="Consulta de vários fragmentos" 
	description="Execute consultas entre fragmentos usando a biblioteca de cliente de banco de dados elástico." 
	services="sql-database" 
	documentationCenter="" 
	manager="jeffreyg" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/24/2015" 
	ms.author="sidneyh"/>

# Consulta de vários fragmentos

## Visão geral

**Consulta de vários fragmentos** é usada para tarefas, como coleta/relatórios de dados que exigem a execução de uma consulta que se estende por vários fragmentos. (Compare isso ao [roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md), que executa todo o trabalho em um único fragmento). Para usar o SQL Server Management Studio, consulte [Introdução à consulta de Banco de Dados Elástico](sql-database-elastic-query-getting-started.md).

A biblioteca de cliente de banco de dados elástico introduz um novo namespace chamado **Microsoft.Azure.SqlDatabase.ElasticScale.Query**, que fornece a capacidade de consultar vários fragmentos usando consulta e resultado únicos. Ele fornece uma abstração de consulta em uma coleção de fragmentos. Ele também fornece diretivas de execução alternativo, resultados parciais em particular, para lidar com falhas ao consultar sobre vários fragmentos.

O ponto de entrada principal para consultar vários fragmentos é a classe **MultiShardConnection**. Como com roteamento dependente de dados, a API segue a experiência familiar de classes e métodos ****[System.Data.SqlClient](http://msdn.microsoft.com/library/System.Data.SqlClient(v=vs.110).aspx)**. Com a biblioteca **SqlClient**, a primeira etapa é criar uma **SqlConnection**, então criar um **SqlCommand** para a conexão e, por fim, executar o comando por meio de um dos métodos **Execute**. Por fim, **SqlDataReader** itera pelos conjuntos de resultados retornados da execução do comando. A experiência com a consulta de fragmento várias APIs segue estas etapas:

1. Crie uma **MultiShardConnection**.
2. Crie um **MultiShardCommand** para uma **MultiShardConnection**.
3. Execute o comando.
4. Consuma os resultados por meio do **MultiShardDataReader**. 

Uma diferença importante é a construção de fragmentos várias conexões. Enquanto a **SqlConnection** opera em um banco de dados individual, a **MultiShardConnection** usa um ***conjunto de fragmentos*** como sua entrada. Uma possível preencher a coleção de fragmentos de um mapa do fragmento. A consulta então é executada na coleção de fragmentos usando a semântica **UNION ALL** para montar um único resultado geral. Como alternativa, o nome do fragmento que origina a linha pode ser adicionado à saída usando a propriedade **ExecutionOptions** no comando. O código a seguir ilustra o uso da consulta de vários fragmentos usando um determinado **ShardMap** chamado *myShardMap*.

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
 

Observe a chamada para **myShardMap.GetShards()**. Esse método recupera todos os fragmentos do mapa de fragmentos e fornece uma maneira fácil de executar uma consulta em todos os bancos de dados relevantes. A coleção de fragmentos para uma consulta de vários fragmentos pode ser refinada ainda mais executando uma consulta LINQ sobre a coleção retornada da chamada para **myShardMap.GetShards()**. Em combinação com a política de resultados parciais, a funcionalidade atual de consultas do fragmento vários foi projetado para funcionar bem para dezenas, centenas de fragmentos. Uma limitação com as consultas de vários fragmentos no momento é a ausência de validação de fragmentos e shardlets são consultados. Enquanto o roteamento dependentes de dados verifica que um determinado fragmento faz parte do mapa do fragmento no momento da consulta, consultas de vários fragmentos não executam essa verificação. Isso pode levar à execução de consultas em vários fragmentos que já foram removidas do mapa de fragmentos.

## Consultas de vários fragmentos e operações de divisão/mesclagem

Consultas de vários fragmentos não verificam se shardlets do banco de dados consultado estão participando de operações de divisão/mesclagem em andamento. Isso pode levar a inconsistências em que as linhas do mesmo shardlet são mostradas para vários bancos de dados na mesma consulta de vários fragmento. Lembre-se dessas limitações e considere drenar as operações de divisão/mesclagem em execução e alterações no mapa de fragmentos ao executar consultas de vários fragmentos.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 

<!---HONumber=August15_HO6-->