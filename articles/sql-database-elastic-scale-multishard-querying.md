<properties 
	pageTitle="Consultar vários fragmentos" 
	description="Execute consultas entre fragmentos usando APIs de escala elástica." 
	services="sql-database" 
	documentationCenter="" 
	manager="stuartozer" 
	authors="torsteng" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/16/2015" 
	ms.author="torsteng@microsoft.com"/>

# Consultar vários fragmentos
## Visão geral
**Consultar vários fragmentos** é usado para tarefas, como coleta/relatórios de dados que exigem a execução de uma consulta que se estendem por vários fragmentos. (Compare isso [roteamento dependente de dados](./sql-database-elastic-scale-data-dependent-routing.md), que executa todo o trabalho em um único fragmento.) 

A biblioteca de cliente de Escala Elástica introduz um novo namespace chamado **Microsoft.Azure.SqlDatabase.ElasticScale.Query** que fornece a capacidade de consultar vários fragmentos usando uma única consulta e resultado. Ele fornece uma abstração de consulta em uma coleção de fragmentos. Ele também fornece diretivas de execução alternativo, resultados parciais em particular, para lidar com falhas ao consultar sobre vários fragmentos.  

O ponto de entrada principal para consultar vários fragmentos é a classe **MultiShardConnection**. Como com roteamento dependente de dados, a API segue a experiência familiar de **[System.Data.SqlClient](http://msdn.microsoft.com/library/System.Data.SqlClient(v=vs.110).aspx)** classes e métodos. Com a biblioteca do **SqlClient**, a primeira etapa é criar um **SqlConnection**, em seguida, criar um **SqlCommand** para a conexão e execute o comando por meio de um dos métodos **Execute**. Por fim, **SqlDataReader** percorre os conjuntos de resultados retornados da execução do comando. A experiência com a consulta de fragmento várias APIs segue estas etapas: 

1. Crie um **MultiShardConnection**.
2. Crie um **MultiShardCommand** para um **MultiShardConnection**.
3. Execute o comando.
4. Consuma os resultados por meio de **MultiShardDataReader**. 

Uma diferença importante é a construção de fragmentos várias conexões. Onde o **SqlConnection** opera em um único banco de dados, o **MultiShardConnection** usa um ***conjunto de fragmentos*** como sua entrada. Uma possível preencher a coleção de fragmentos de um mapa do fragmento. A consulta é executada, em seguida, na coleção de fragmentos usando a semântica **UNION ALL** para montar um único resultado geral. Opcionalmente, o nome do fragmento que origina a linha pode ser adicionado à saída usando a propriedade **ExecutionOptions** no comando. O código a seguir ilustra o uso de vários fragmentos consultas usando um determinado **ShardMap** chamado  *myShardMap*. 

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
 

Observe a chamada para **myShardMap.GetShards()**. Esse método recupera todos os fragmentos do mapa do fragmento e fornece uma maneira fácil de executar uma consulta em todos os fragmentos de mapa fragmento. A coleção de fragmentos para uma consulta de vários fragmento pode ser refinada ainda mais executando uma consulta LINQ sobre a coleção retornada da chamada para **myShardMap.GetShards()**. Em combinação com a política de resultados parciais, a funcionalidade atual de consultas do fragmento vários foi projetado para funcionar bem para dezenas, centenas de fragmentos.
Uma limitação com as consultas de vários fragmentos no momento é a ausência de validação de fragmentos e shardlets são consultados. Enquanto o roteamento dependentes de dados verifica que um determinado fragmento faz parte do mapa do fragmento no momento da consulta, consultas de vários fragmentos não executam essa verificação. Isso pode levar a fragmentar várias consultas em execução em fragmentos que já foram removidos do mapa do fragmento.

## Fragmentos várias consultas e operações de mesclagem de divisão

Vários fragmentos consultas não verifica se shardlets sobre o fragmento consultado estão participando em operações de divisão/mesclagem contínua. Isso pode levar a inconsistências onde as linhas de shardlet o mesmo mostram para vários fragmentos na mesma consulta vários fragmento. Lembre-se dessas limitações e considere descarga operações de divisão/mesclagem contínua e alterações no mapa do fragmento ao executar consultas de vários fragmentos.

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--HONumber=47-->
