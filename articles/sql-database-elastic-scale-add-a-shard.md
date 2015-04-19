<properties 
	pageTitle="Adicionar um fragmento a um aplicativo de Escala elástica." 
	description="Como usar APIs de Escala Elástica para adicionar novos fragmentos para um fragmento de conjunto." 
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

# Adicionar um fragmento a um aplicativo de Escala elástica. 


## Para adicionar um fragmento de um novo intervalo ou uma chave  

Geralmente, os aplicativos precisam simplesmente adicionar novos fragmentos para lidar com dados que são esperados de novas chaves ou intervalos de chaves para um mapa do fragmento que já existe. Por exemplo, um aplicativo fragmentado por ID de locatário talvez tenha provisionar um novo fragmento para um novo locatário ou dados mensalmente fragmentados talvez precisem de um novo fragmento provisionado antes do início de cada novo mês. 

Se o novo intervalo de valores de chave já não é parte de um mapeamento existente, é muito simples adicionar o novo fragmento e associar a nova chave ou o intervalo para esse fragmento. 

###Exemplo:  Adicionando um fragmento e seu intervalo a um mapa de fragmento existente
No exemplo a seguir, um banco de dados denominado **sample_shard_2** e todos os objetos de esquema necessários dentro dele foram criados para conter o intervalo [300, 400).  

    // sm is a RangeShardMap object.
    // Add a new shard to hold the range being added. 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Create the mapping and associate it with the new shard 
    sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                            (new Range<long>(300, 400), shard2, MappingStatus.Online)); 


## Para adicionar um fragmento de uma parte vazia de um intervalo existente  

Em algumas circunstâncias, você já mapeou um intervalo para um fragmento e parcialmente preencheu-o com dados, mas agora deseja que os dados futuros sejam direcionados para um fragmento diferente. Por exemplo, você fragmentou o intervalo por dia e já tem 50 dias alocados para um fragmento, mas no dia 24, você deseja que os dados futuros encaixem em um fragmento diferente. O [Serviço de Divisão e mesclagem] da visualização da Escala Elástica(sql-database-elastic-scale-overview-split-and-merge.md) pode executar essa operação, mas se a movimentação de dados não é necessária (por exemplo, os dados para o intervalo de dias [25, 50), por exemplo, dia 25 inclusive para 50 exclusivo, ainda não existe) você pode executar isso inteiramente usando as APIs de gerenciamento de mapa do fragmento diretamente.

###Exemplo:  Dividindo um intervalo e atribuindo a parte vazia para um fragmento adicionado recentemente

Um banco de dados chamado "sample_shard_2" e todos os objetos de esquema necessários dentro dele foram criados.  

 
    // sm is a RangeShardMap object.
    // Add a new shard to hold the range we will move 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
    
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Split the Range holding Key 25 

    sm.SplitMapping(sm.GetMappingForKey(25), 25); 

    // Map new range holding [25-50) to different shard: 
    // first take existing mapping offline 
    sm.MarkMappingOffline(sm.GetMappingForKey(25)); 
    // now map while offline to a different shard and take online 
    RangeMappingUpdate upd = new RangeMappingUpdate(); 
    upd.Shard = shard2; 
    sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd)); 

**Importante**:  Use essa técnica somente se você tiver certeza de que o intervalo para o mapeamento atualizado está vazio.  Os métodos acima não verificam os dados para o intervalo que está sendo movido, portanto, é melhor incluir verificações em seu código.  Se existirem linhas no intervalo que está sendo movido, a distribuição de dados real não corresponderá ao mapa do fragmento atualizado. Use o [Serviço de Divisão e mesclagem](sql-database-elastic-scale-overview-split-and-merge.md) para executar a operação nesses casos.  


[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--HONumber=47-->
