---
title: Como usar o Cache Redis do Azure com Node.js | Microsoft Docs
description: "Introdução ao Cache Redis do Azure usando Node.js e node_redis."
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: v-lincan
ms.assetid: 06fddc95-8029-4a8d-83f5-ebd5016891d9
ms.service: cache
ms.devlang: nodejs
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/10/2017
ms.author: wesmc
ms.openlocfilehash: e8b4818ad2b876ce4d55cf8aa2aa0e0162dea963
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-use-azure-redis-cache-with-nodejs"></a>Como usar o Cache Redis do Azure com Node.js
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

O Cache Redis do Azure fornece acesso a um cache Redis dedicado e seguro, gerenciado pela Microsoft. O cache é acessível por meio de qualquer aplicativo no Microsoft Azure.

Este tópico mostra uma introdução ao Cache Redis do Azure usando Node.js. 

## <a name="prerequisites"></a>pré-requisitos
Instale o [node_redis](https://github.com/mranney/node_redis):

    npm install redis

Este tutorial usa o [node_redis](https://github.com/mranney/node_redis). Para exemplos de uso de outros clientes do Node.js, veja a documentação individual para os clientes do Node.js listado em [clientes do Redis Node.js](http://redis.io/clients#nodejs).

## <a name="create-a-redis-cache-on-azure"></a>Criar um cache Redis no Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Recuperar as chaves de acesso e o nome do host
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="connect-to-the-cache-securely-using-ssl"></a>Conectar-se ao cache com segurança usando SSL
As últimas compilações do [node_redis](https://github.com/mranney/node_redis) oferecem suporte para se conectar ao Cache Redis do Azure usando SSL. O exemplo a seguir mostra como se conectar ao Cache Redis do Azure usando o ponto de extremidade SSL 6380. Substitua `<name>` pelo nome do seu cache e `<key>` pela chave primária ou secundária, como descrito anteriormente na seção [Recuperar as chaves de acesso e o nome do host](#retrieve-the-host-name-and-access-keys).

     var redis = require("redis");

      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});

> [!NOTE]
> A porta não SSL está desabilitada para novas instâncias do Cache Redis do Azure. Se você estiver usando outro cliente que não ofereça suporte a SSL, veja [Como habilitar a porta não SSL](cache-configure.md#access-ports).
> 
> 

## <a name="add-something-to-the-cache-and-retrieve-it"></a>Adicionar algo ao cache e recuperá-lo
O exemplo a seguir mostra como se conectar a uma instância do Cache Redis do Azure e armazena e recupera um item do cache. Para obter mais exemplos de como usar Redis com o cliente [node_redis](https://github.com/mranney/node_redis), veja [http://redis.js.org/](http://redis.js.org/).

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


## <a name="next-steps"></a>Próximas etapas
* [Habilite o diagnóstico de cache](cache-how-to-monitor.md#enable-cache-diagnostics) para que você possa [monitorar](cache-how-to-monitor.md) a integridade do cache.
* Leia a [documentação oficial do Redis](http://redis.io/documentation).

