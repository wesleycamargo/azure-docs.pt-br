<properties
	pageTitle="Como usar o Cache Redis do Azure com Node.js | Microsoft Azure"
	description="Introdução ao Cache Redis do Azure usando Node.js e node_redis."
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="erikre"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.devlang="nodejs"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="05/24/2016"
	ms.author="sdanie"/>

# Como usar o Cache Redis do Azure com Node.js

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

O Cache Redis do Azure fornece acesso a um cache Redis dedicado e seguro, gerenciado pela Microsoft. O cache é acessível por meio de qualquer aplicativo no Microsoft Azure.

Este tópico mostra uma introdução ao Cache Redis do Azure usando Node.js. Para obter outro exemplo do uso do Cache Redis do Azure com Node.js, consulte [Criar um aplicativo de chat do Node.js com Socket.IO em um site do Azure](../app-service-web/web-sites-nodejs-chat-app-socketio.md).


## Pré-requisitos

Instale o [node\_redis](https://github.com/mranney/node_redis):

    npm install redis

Este tutorial usa [node\_redis](https://github.com/mranney/node_redis), mas você pode usar qualquer cliente Node.js listado em [http://redis.io/clients](http://redis.io/clients).

## Criar um cache Redis no Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## Recuperar as chaves de acesso e o nome do host

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## Habilitar o ponto de extremidade não SSL

Alguns clientes Redis não dão suporte a SSL e, por padrão, a [porta não SSL está desabilitada para novas instâncias do Cache Redis do Azure](cache-configure.md#access-ports). No momento da redação deste artigo, o cliente [node\_redis](https://github.com/mranney/node_redis) não oferecia suporte a SSL.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]


## Adicionar algo ao cache e recuperá-lo

	  var redis = require("redis");
	
	  // Add your cache name and access key.
	var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});
	
	client.set("key1", "value", function(err, reply) {
		    console.log(reply);
		});
	
	client.get("key1",  function(err, reply) {
		    console.log(reply);
		});

Saída:

	OK
	value


## Próximas etapas

- [Habilite o diagnóstico de cache](cache-how-to-monitor.md#enable-cache-diagnostics) para que você possa [monitorar](cache-how-to-monitor.md) a integridade do cache.
- Leia a [documentação oficial do Redis](http://redis.io/documentation).

<!---HONumber=AcomDC_0525_2016-->