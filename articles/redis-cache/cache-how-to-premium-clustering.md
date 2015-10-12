<properties 
	pageTitle="Como configurar o clustering do Redis para um Cache Redis do Azure Premium" 
	description="Saiba como criar e gerenciar o clustering do Redis para as instâncias da camada Premium do Cache Redis do Azure" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/30/2015" 
	ms.author="sdanie"/>

# Como configurar o clustering do Redis para um Cache Redis do Azure Premium
O Cache Redis do Azure apresenta diferentes ofertas de cache que fornecem flexibilidade na escolha do tamanho e dos recursos do cache, incluindo a nova camada Premium, atualmente em visualização.

A camada premium do Cache Redis do Azure inclui clustering, persistência e suporte de rede virtual. Este artigo descreve como configurar o clustering em uma instância premium do Cache Redis do Azure.

Para obter informações sobre outros recursos de cache premium, veja [Como configurar a persistência para um Cache Redis do Azure Premium](cache-how-to-premium-persistence.md) e [Como configurar o suporte de Rede Virtual para um Cache Redis do Azure Premium](cache-how-to-premium-vnet.md).

>[AZURE.NOTE]A camada Premium do Cache Redis do Azure está atualmente em visualização.

## O que é o Cluster Redis?
O Cache Redis do Azure oferece o cluster Redis como [implementado no Redis](http://redis.io/topics/cluster-tutorial). Com o Cluster Redis, você obtém os seguintes benefícios:

-	A capacidade de dividir automaticamente o conjunto de dados entre vários nós. 
-	A capacidade de continuar as operações quando um subconjunto dos nós estiver apresentando falhas ou não conseguir se comunicar com o restante do cluster. 
-	Mais taxa de transferência: a taxa de transferência aumenta linearmente à medida que o número de fragmentos aumenta. 
-	Mais tamanho de memória: aumenta linearmente à medida que o número de fragmentos aumenta.  

Veja [Perguntas frequentes sobre o Cache Redis do Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) para obter mais detalhes sobre o tamanho, taxa de transferência e largura de banda com caches premium.

No Azure, o cluster Redis é oferecido como um modelo primário/de réplica em que cada fragmento tem um par primário/de réplica, com a replicação gerenciada pelo serviço de Cache Redis do Azure.

## Clustering
O clustering é configurado na folha **Novo Cache Redis** durante a criação do cache. Para criar um cache, entre no [portal de visualização do Azure](https://portal.azure.com) e clique em **Novo**->**Dados + Armazenamento**>**Cache Redis**.

![Criar um Cache Redis][redis-cache-new-cache-menu]

Para configurar o clustering, primeiro selecione um dos caches **Premium** na folha **Escolha o Tipo de preço**.

![Escolha sua camada de preço][redis-cache-premium-pricing-tier]

O clustering é configurado na folha **Redis Cluster**.

![Clustering][redis-cache-clustering]

Você pode ter até 10 fragmentos no cluster. Clique em **Habilitado** e mova o controle deslizante ou digite um número entre 1 e 10 para a **Contagem de fragmentos** e clique em **OK**.

Cada fragmento é um par de cache primário/de réplica gerenciado pelo Azure, e o tamanho total do cache é calculado multiplicando o número de fragmentos pelo tamanho do cache selecionado no tipo de preço.

![Clustering][redis-cache-clustering-selected]

Depois que o cache for criado, conecte-se a ele e use-o apenas como um cache não clusterizado, e o Redis distribuirá os dados por todos os fragmentos do Cache.

## Perguntas frequentes sobre clustering

A lista a seguir contém respostas para perguntas frequentes sobre o clustering do Cache Redis do Azure.

## Qual é o maior tamanho de cache que eu posso criar?

O maior tamanho de cache premium é de 53 GB. Você pode criar até 10 fragmentos, fornecendo um tamanho máximo de 530 GB. Se precisar de um tamanho maior, você pode [solicitar mais](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Para obter mais informações, veja [Preço do Cache Redis do Azure](https://azure.microsoft.com/pricing/details/cache/).

## Todos os clientes do Redis dão suporte ao clustering?

No momento, nem todos os clientes dão suporte ao clustering do Redis. StackExchange.Redis é o que dá suporte a ele. Para obter mais informações sobre outros clientes, veja a seção [Reproduzindo com o cluster](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) do [tutorial de cluster do Redis](http://redis.io/topics/cluster-tutorial).

>[AZURE.NOTE]Se estiver usando o StackExchange.Redis como seu cliente, verifique se está usando a versão mais recente do [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 ou posterior para que o clustering funcione corretamente.

## Como posso me conectar ao meu cache quando o clustering estiver habilitado?

Você pode se conectar ao seu cache usando os mesmos [pontos de extremidade, portas e chaves](cache-configure.md#properties) que você usa ao se conectar a um cache que não tem o clustering habilitado. O Redis gerencia o clustering no back-end para que você não precise gerenciá-lo por meio de seu cliente.

## Posso me conectar diretamente aos fragmentos individuais do meu cache?

Oficialmente, não há suporte para isso. Dito isso, cada fragmento consiste em um par de cache primário/de réplica que é conhecido coletivamente como uma instância de cache. Você pode se conectar a essas instâncias de cache usando o redis-cli.exe usando o padrão a seguir.

Para não SSL, use os comandos a seguir.

	Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
	Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
	Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
	...
	Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

Para SSL, substitua `1300N` por `1500N`.

## Posso configurar o clustering para um cache criado anteriormente?

Durante o período de visualização, você pode habilitar e configurar o clustering apenas durante a criação de um cache.

## Posso configurar o clustering para um cache básico ou standard?

O clustering está disponível apenas para os caches premium.

## Próximas etapas

Saiba como usar mais recursos de cache premium. - [Como configurar a persistência para um Cache Redis do Azure Premium](cache-how-to-premium-persistence.md) - [Como configurar o suporte de Rede Virtual para um Cache Redis do Azure Premium](cache-how-to-premium-vnet.md)
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-clustering/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-clustering/redis-cache-premium-pricing-tier.png

[NewCacheMenu]: ./media/cache-how-to-premium-clustering/redis-cache-new-cache-menu.png

[CacheCreate]: ./media/cache-how-to-premium-clustering/redis-cache-cache-create.png

[redis-cache-premium-pricing-group]: ./media/cache-how-to-premium-clustering/redis-cache-premium-pricing-group.png

[redis-cache-premium-features]: ./media/cache-how-to-premium-clustering/redis-cache-premium-features.png

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

<!---HONumber=Oct15_HO1-->