<properties
   pageTitle="Como usar o Cache Redis do Azure com Java | Microsoft Azure"
	description="Introdução ao Cache Redis do Azure usando o Java"
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cache"
	ms.devlang="java"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="05/24/2016"
	ms.author="sdanie"/>

# Como usar o Cache Redis do Azure com Java

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

O Cache Redis do Azure fornece acesso a um cache Redis dedicado, gerenciado pela Microsoft. O cache é acessível por meio de qualquer aplicativo no Microsoft Azure.

Este tópico mostra uma introdução ao Cache Redis do Azure usando Java.

## Pré-requisitos

[Jedis](https://github.com/xetorthio/jedis) - cliente Java para Redis

Este tutorial usa Jedis, mas você pode usar qualquer cliente Java listado em [http://redis.io/clients](http://redis.io/clients).

## Criar um cache Redis no Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## Recuperar as chaves de acesso e o nome do host

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## Habilitar o ponto de extremidade não SSL

Alguns clientes Redis não dão suporte a SSL e, por padrão, a [porta não SSL está desabilitada para novas instâncias do Cache Redis do Azure](cache-configure.md#access-ports). No momento da redação deste artigo, o cliente [Jedis](https://github.com/xetorthio/jedis) não oferecia suporte a SSL.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]




## Adicionar algo ao cache e recuperá-lo

	package com.mycompany.app;
	import redis.clients.jedis.Jedis;
	import redis.clients.jedis.JedisShardInfo;

	/* Make sure you turn on non-SSL port in Azure Redis using the Configuration section in the Azure Portal */
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

<!---HONumber=AcomDC_0525_2016-->