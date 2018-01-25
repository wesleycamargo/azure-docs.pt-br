---
title: Como usar o Cache Redis do Azure com Java | Microsoft Docs
description: "Introdução ao Cache Redis do Azure usando o Java"
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 29275a5e-2e39-4ef2-804f-7ecc5161eab9
ms.service: cache
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 08/31/2017
ms.author: wesmc
ms.openlocfilehash: a93dbf78c9cb94f6c20c8569a69e068ceb3b98bc
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-use-azure-redis-cache-with-java"></a>Como usar o Cache Redis do Azure com Java
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

O Cache Redis do Azure fornece acesso a um cache Redis dedicado, gerenciado pela Microsoft. O cache é acessível por meio de qualquer aplicativo no Microsoft Azure.

Este tópico mostra uma introdução ao Cache Redis do Azure usando Java.

## <a name="prerequisites"></a>pré-requisitos
[Jedis](https://github.com/xetorthio/jedis) - cliente Java para Redis

Este tutorial usa Jedis, mas você pode usar qualquer cliente Java listado em [http://redis.io/clients](http://redis.io/clients).

## <a name="create-a-redis-cache-on-azure"></a>Criar um cache Redis no Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Recuperar as chaves de acesso e o nome do host
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="connect-to-the-cache-securely-using-ssl"></a>Conectar-se ao cache com segurança usando SSL
As últimas compilações do [jedis](https://github.com/xetorthio/jedis) oferecem suporte para se conectar ao Cache Redis do Azure usando SSL. O exemplo a seguir mostra como se conectar ao Cache Redis do Azure usando o ponto de extremidade SSL 6380. Substitua `<name>` pelo nome do seu cache e `<key>` pela chave primária ou secundária, como descrito anteriormente na seção [Recuperar as chaves de acesso e o nome do host](#retrieve-the-host-name-and-access-keys).

    boolean useSsl = true;
    /* In this line, replace <name> with your cache name: */
    JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6380, useSsl);
    shardInfo.setPassword("<key>"); /* Use your access key. */

> [!NOTE]
> A porta não SSL está desabilitada para novas instâncias do Cache Redis do Azure. Se você estiver usando outro cliente que não ofereça suporte a SSL, veja [Como habilitar a porta não SSL](cache-configure.md#access-ports).
> 
> 

## <a name="add-something-to-the-cache-and-retrieve-it"></a>Adicionar algo ao cache e recuperá-lo
    package com.mycompany.app;
    import redis.clients.jedis.Jedis;
    import redis.clients.jedis.JedisShardInfo;

    public class App
    {
      public static void main( String[] args )
      {
        boolean useSsl = true;
        /* In this line, replace <name> with your cache name: */
        JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6380, useSsl);
        shardInfo.setPassword("<key>"); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);
        jedis.set("foo", "bar");
        String value = jedis.get("foo");
      }
    }


## <a name="next-steps"></a>Próximas etapas
* [Habilite o diagnóstico de cache](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) para que você possa [monitorar](https://msdn.microsoft.com/library/azure/dn763945.aspx) a integridade do cache.
* Leia a [documentação oficial do Redis](http://redis.io/documentation).
* Aprenda a [configurar o aplicativo Inicializador do Spring Boot para usar o Cache Redis](cache-java-spring-boot-initializer-with-redis-cache.md).