<properties 
   pageTitle="Como usar o Cache Redis do Azure com Node.js" 
   description="Introdução ao Cache Redis do Azure usando Node.js e node_redis." 
   services="redis-cache" 
   documentationCenter="" 
   authors="MikeWasson" 
   manager="wpickett" 
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="nodejs"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="required" 
   ms.date="02/23/2015"
   ms.author="mwasson"/>

# Como usar o Cache Redis do Azure com Node.js 

O Cache Redis do Azure fornece acesso a um cache Redis dedicado e seguro, gerenciado pela Microsoft. O cache é acessível por meio de qualquer aplicativo no Microsoft Azure.

Este tópico mostra uma introdução ao Cache Redis do Azure usando Node.js. Para obter outro exemplo do uso do Cache Redis do Azure com Node.js, consulte [Criar um Aplicativo de Chat do Node.js com Socket.IO em um Site do Azure][].


## Pré-requisitos

Instale o [node_redis](https://github.com/mranney/node_redis):

    npm install redis

Este tutorial usa [node_redis](https://github.com/mranney/node_redis), mas você pode usar qualquer cliente Node.js listado em [http://redis.io/clients](http://redis.io/clients).

## Criar um cache Redis no Azure

Na [Visualização do Portal de Gerenciamento do Azure](http://go.microsoft.com/fwlink/?LinkId=398536), clique em **Novo** e selecione **Cache Redis**. 

  ![][1]

Insira um nome de host DNS. Ele terá o formato `<nome>.redis.cache.windows.net`. Clique em **Criar**.

  ![][2]


Depois que o cache for criado, clique nele no portal para exibir as configurações de cache. Clique no link em **Chaves** e copie a chave primária. Ela será necessária para autenticar solicitações.

  ![][4]


## Habilitar o ponto de extremidade não SSL


Clique no link em **Portas** e clique em **Não** para "Permitir acesso somente via SSL". Isso habilitará a porta não SSL para o cache. O cliente node_redis atualmente não dá suporte a SSL.

  ![][3]


## Adicionar algo ao cache e recuperá-lo

	var redis = require("redis");
	
    // Put in your cache name and access key.
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

- [Habilite o diagnóstico de cache](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) para que você possa [monitorar](https://msdn.microsoft.com/library/azure/dn763945.aspx) a integridade do cache. 
- Leia a [documentação oficial do Redis](http://redis.io/documentation).


<!--Image references-->
[1]: ./media/cache-java-get-started/cache01.png
[2]: ./media/cache-java-get-started/cache02.png
[3]: ./media/cache-java-get-started/cache03.png
[4]: ./media/cache-java-get-started/cache04.png

[Criar um aplicativo de chat do Node.js com Socket.IO em um site do Azure]: app-service-web/web-sites-nodejs-chat-app-socketio.md


<!--HONumber=49-->