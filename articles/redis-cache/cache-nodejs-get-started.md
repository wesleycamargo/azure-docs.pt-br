<properties
	pageTitle="Como usar o Cache Redis do Azure com Node.js | Microsoft Azure"
	description="Introdução ao Cache Redis do Azure usando Node.js e node_redis."
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="dwrede"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.devlang="nodejs"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="10/23/2015"
	ms.author="sdanie"/>

# Como usar o Cache Redis do Azure com Node.js

> [AZURE.SELECTOR]
- [.Net](cache-dotnet-how-to-use-azure-redis-cache.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

O Cache Redis do Azure fornece acesso a um cache Redis dedicado e seguro, gerenciado pela Microsoft. O cache é acessível por meio de qualquer aplicativo no Microsoft Azure.

Este tópico mostra uma introdução ao Cache Redis do Azure usando Node.js. Para obter outro exemplo do uso do Cache Redis do Azure com Node.js, consulte [Criar um aplicativo de chat do Node.js com Socket.IO em um site do Azure][].


## Pré-requisitos

Instale o [node\_redis](https://github.com/mranney/node_redis):

    npm install redis

Este tutorial usa [node\_redis](https://github.com/mranney/node_redis), mas você pode usar qualquer cliente Node.js listado em [http://redis.io/clients](http://redis.io/clients).

## Criar um cache Redis no Azure

No [portal de visualização do Azure](http://go.microsoft.com/fwlink/?LinkId=398536), clique em **Novo**, **Dados + Armazenamento** e selecione **Cache Redis**.

  ![][1]

Insira um nome de host DNS. Ele terá o formato `<name>.redis.cache.windows.net`. Clique em **Criar**.

  ![][2]


Depois de criar o cache, clique nele no portal de visualização para exibir as configurações do cache. Clique no link em **Chaves** e copie a chave primária. Ela será necessária para autenticar as solicitações.

  ![][4]


## Habilitar o ponto de extremidade não SSL


Clique no link em **Portas** e clique em **Não** para "Permitir acesso somente via SSL". Isso habilita a porta não SSL para o cache. O cliente node\_redis atualmente não dá suporte a SSL.

  ![][3]


## Adicionar algo ao cache e recuperá-lo

	var redis = require("redis");

    // Add your cache name and access key.
	var client = redis.createClient(6379,'<name>.redis.cache.windows.net', {auth_pass: '<key>' });

	client.set("foo", "bar", function(err, reply) {
	    console.log(reply);
	});

	client.get("foo",  function(err, reply) {
	    console.log(reply);
	});


Saída:

	OK
	bar


## Próximas etapas

- [Habilite o diagnóstico de cache](cache-how-to-monitor.md#enable-cache-diagnostics) para que você possa [monitorar](cache-how-to-monitor.md) a integridade do cache.
- Leia a [documentação oficial do Redis](http://redis.io/documentation).


<!--Image references-->
[1]: ./media/cache-nodejs-get-started/cache01.png
[2]: ./media/cache-nodejs-get-started/cache02.png
[3]: ./media/cache-nodejs-get-started/cache03.png
[4]: ./media/cache-nodejs-get-started/cache04.png

[Criar um aplicativo de chat do Node.js com Socket.IO em um site do Azure]: ../app-service-web/web-sites-nodejs-chat-app-socketio.md

<!---HONumber=Nov15_HO1-->