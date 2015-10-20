<properties
   pageTitle="Como usar o Cache Redis do Azure com Java | Microsoft Azure"
	description="Introdução ao Cache Redis do Azure usando o Java"
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="cache"
	ms.devlang="java"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="10/05/2015"
	ms.author="sdanie"/>

# Como usar o Cache Redis do Azure com Java

O Cache Redis do Azure fornece acesso a um cache Redis dedicado, gerenciado pela Microsoft. O cache é acessível por meio de qualquer aplicativo no Microsoft Azure.

Este tópico mostra uma introdução ao Cache Redis do Azure usando Java.


## Pré-requisitos

[Jedis](https://github.com/xetorthio/jedis) - cliente Java para Redis

Este tutorial usa Jedis, mas você pode usar qualquer cliente Java listado em [http://redis.io/clients](http://redis.io/clients).


## Criar um cache Redis no Azure

No [portal de visualização do Azure](http://go.microsoft.com/fwlink/?LinkId=398536), clique em **Novo**, **Dados + Armazenamento** e selecione **Cache Redis**.

  ![][1]

Insira um nome de host DNS. Ele terá o formato `<name>.redis.cache.windows.net`. Clique em **Criar**.

  ![][2]


Depois de criar o cache, clique nele no portal de visualização para exibir as configurações do cache. Clique no link em **Chaves** e copie a chave primária. Ela será necessária para autenticar as solicitações.

  ![][4]


## Habilitar o ponto de extremidade não SSL


Clique no link em **Portas** e clique em **Não** para "Permitir acesso somente via SSL". Isso habilita a porta não SSL para o cache. O cliente Jedis atualmente não dá suporte a SSL.

  ![][3]


## Adicionar algo ao cache e recuperá-lo

	package com.mycompany.app;
	import redis.clients.jedis.Jedis;
	import redis.clients.jedis.JedisShardInfo;

	/* Make sure you turn on non-SSL port in Azure Redis using the Configuration section in the preview portal */
	public class App
	{
	  public static void main( String[] args )
	  {
        /* In this line, replace <name> with your cache name: */
	    JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379);
	    shardInfo.setPassword("<key>"); /* Use your access key. */
	    Jedis jedis = new Jedis(shardInfo);
     	jedis.set("foo", "bar");
     	String value = jedis.get("foo");
	  }
	}


## Próximas etapas

- [Habilite o diagnóstico de cache](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) para que você possa [monitorar](https://msdn.microsoft.com/library/azure/dn763945.aspx) a integridade do cache.
- Leia a [documentação oficial do Redis](http://redis.io/documentation).


<!--Image references-->
[1]: ./media/cache-java-get-started/cache01.png
[2]: ./media/cache-java-get-started/cache02.png
[3]: ./media/cache-java-get-started/cache03.png
[4]: ./media/cache-java-get-started/cache04.png

<!---HONumber=Oct15_HO3-->