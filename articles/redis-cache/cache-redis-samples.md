<properties 
	pageTitle="Exemplos do Cache Redis do Azure" 
	description="Saiba como usar o Cache Redis do Azure" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="05/19/2015" 
	ms.author="sdanie"/>

# Exemplos do Cache Redis do Azure 

Este tópico fornece uma lista de exemplos do Cache Redis do Azure que abrangem cenários como conexão a um cache, leitura e gravação de dados para e de um cache e uso dos provedores do Cache Redis do ASP.NET. Alguns dos exemplos são projetos para download, e outros fornecem orientação passo a passo e incluem trechos de código, mas não se vinculam a um projeto baixável.

## Exemplos Hello world

Os exemplos nesta seção mostram as noções básicas da conexão a uma instância do Cache Redis do Azure e da leitura e gravação de dados no cache usando diversas linguagens e clientes Redis.

O exemplo [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) mostra como executar várias operações de cache usando o cliente .NET [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis).

Este exemplo mostra como:

-	Usar várias opções de conexão
-	Ler e gravar objetos em cache usando operações síncronas e assíncronas
-	Usar comandos MGET/MSET do Redis para retornar valores de chaves especificadas
-	Executar operações transacionais do Redis
-	Trabalhar com listas do Redis e conjuntos classificados
-	Armazenar objetos .NET usando serializadores JsonConvert
-	Usar conjuntos do Redis para implementar a marcação

Para obter mais informações, consulte a documentação do [Stackexchange](https://github.com/StackExchange/StackExchange.Redis) no github e, para obter mais cenários de uso, consulkte os testes de unidade de [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/StackExchange.Redis.Tests).

[Como usar o Cache Redis do Azure com o Node.js](cache-nodejs-get-started.md) mostra como começar a usar o Cache Redis do Azure usando Node.js e o cliente [node_redis](https://github.com/mranney/node_redis).

[Como usar o Cache Redis do Azure com Java](cache-java-get-started.md) mostra como começar a usar o Cache Redis do Azure usando Java e o cliente [Jedis](https://github.com/xetorthio/jedis).

[Como usar o Cache Redis do Azure com Python](cache-python-get-started.md) mostra como começar a usar o Cache Redis do Azure usando Python e o cliente [redis py](https://github.com/andymccurdy/redis-py).

O [exemplo PHP](https://msdn.microsoft.com/library/azure/dn690470.aspx#PHPExample) mostra como começar a usar o Cache Redis do Azure com PHP e o cliente [predis](https://github.com/nrk/predis).

[Trabalhar com objetos .NET no cache](https://msdn.microsoft.com/library/azure/dn690521.aspx#Objects) mostra uma maneira de serializar objetos .NET para que você possa gravá-los para e lê-los de uma instância do Cache Redis do Azure.

## Usar o Cache Redis como um backplane de expansão para ASP.NET SignalR

O exemplo [Usar o Cache Redis como um Backplane para escalar horizontalmente para ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) demonstra como você pode usar o Cache Redis do Azure como um backplane SignalR. Para obter mais informações sobre o backplane, consulte [SignalR Scaleout com Redis](http://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## Exemplo de consulta de cliente do Cache Redis

Esse exemplo demonstra a comparação de desempenho entre o acesso a dados de um cache e o acesso a dados do armazenamento de persistência. Esse exemplo tem dois projetos.

-	[Demonstração de como o Cache Redis pode melhorar o desempenho armazenando dados em cache](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
-	[Propagar o banco de dados e o cache para a demonstração](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## Estado de sessão do ASP.NET e cache de saída

O exemplo [Usar o Cache Redis do Azure para armazenar SessionState e OutputCache do ASP.NET](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) demonstra como usar o Cache Redis do Azure para armazenar a Sessão do ASP.NET e o Cache de Saída usando os provedores SessionState e OutputCache para Redis.

## Gerenciar o Cache Redis do Azure com MAML

O exemplo [Gerenciar o Cache Redis do Azure usando Bibliotecas de Gerenciamento do Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) demonstra como você pode usar Bibliotecas de Gerenciamento do Azure para gerenciar - (criar/atualizar/excluir) o cache.

## Exemplo de monitoramento personalizado

O exemplo [Acessar dados de monitoramento do Cache Redis](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) demonstra como é possível acessar os dados de monitoramento do Cache Redis do Azure fora do portal do Azure.

## Um clone no estilo do Twitter escrito usando PHP e Redis

O exemplo [Retwis](https://github.com/SyntaxC4-MSFT/retwis) é o Redis Hello World. É um clone de rede social mínimo no estilo do Twitter escrito usando Redis e PHP usando o cliente [Predis](https://github.com/nrk/predis). O código-fonte é projetado para ser muito simples e, ao mesmo tempo, mostrar diferentes estruturas de dados do Redis.

## Monitor de largura de banda

O exemplo de [Monitor de largura de banda](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) permite que você monitore a largura de banda usada no cliente. Para medir a largura de banda, execute o exemplo no computador cliente de cache, faça chamadas ao cache e observe a largura de banda relatada pelo exemplo de monitor de largura de banda.

<!---HONumber=July15_HO4-->