---
title: Como usar o Cache Redis do Azure com Python | Microsoft Docs
description: Introdução ao Cache Redis do Azure usando Python
services: redis-cache
documentationcenter: ''
author: steved0x
manager: douge
editor: v-lincan

ms.service: cache
ms.devlang: python
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 08/16/2016
ms.author: sdanie

---
# Como usar o Cache Redis do Azure com Python
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Este tópico mostra uma introdução ao Cache Redis do Azure usando Python.

## Pré-requisitos
Instale o [redis-py](https://github.com/andymccurdy/redis-py).

## Criar um cache Redis no Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## Recuperar as chaves de acesso e o nome do host
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## Habilitar o ponto de extremidade não SSL
Alguns clientes Redis não dão suporte a SSL e, por padrão, a [porta não SSL está desabilitada para novas instâncias do Cache Redis do Azure](cache-configure.md#access-ports). No momento da redação deste artigo, o cliente [redis\_py](https://github.com/andymccurdy/redis-py) não oferecia suporte a SSL.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]

## Adicionar algo ao cache e recuperá-lo
    >>> import redis
    >>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
          port=6380, db=0, password='<key>', ssl=True)
    >>> r.set('foo', 'bar')
    True
    >>> r.get('foo')
    b'bar'


Substitua `<name>` pelo nome do seu cache e `key` por sua chave de acesso.

<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png

<!---HONumber=AcomDC_0817_2016-->