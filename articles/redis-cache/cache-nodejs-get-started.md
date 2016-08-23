<properties
	pageTitle="Como usar o Cache Redis do Azure com Node.js | Microsoft Azure"
	description="Introdução ao Cache Redis do Azure usando Node.js e node_redis."
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="douge"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.devlang="nodejs"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="08/16/2016"
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

Este tutorial usa o [node\_redis](https://github.com/mranney/node_redis). Para exemplos de uso de outros clientes do Node.js, veja a documentação individual para os clientes do Node.js listado em [clientes do Redis Node.js](http://redis.io/clients#nodejs).

## Criar um cache Redis no Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## Recuperar as chaves de acesso e o nome do host

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## Conectar-se ao cache com segurança usando SSL

As últimas compilações do [node\_redis](https://github.com/mranney/node_redis) oferecem suporte para se conectar ao Cache Redis do Azure usando SSL. O exemplo a seguir mostra como se conectar ao Cache Redis do Azure usando o ponto de extremidade SSL 6380. Substitua `<name>` com o nome do seu cache e `<key>` com a sua chave primária ou secundária, como descrito anteriormente na seção [Recuperar as chaves de acesso e o nome do host](#retrieve-the-host-name-and-access-keys).

	 var redis = require("redis");
	
	  // Add your cache name and access key.
	var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});


## Adicionar algo ao cache e recuperá-lo

O exemplo a seguir mostra como se conectar a uma instância do Cache Redis do Azure e armazena e recupera um item do cache. Para obter mais exemplos de como usar Redis com o cliente [node\_redis](https://github.com/mranney/node_redis), veja [http://redis.js.org/](http://redis.js.org/).

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

<!---HONumber=AcomDC_0817_2016-->