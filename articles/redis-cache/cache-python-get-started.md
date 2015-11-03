<properties
	pageTitle="Como usar o Cache Redis do Azure com Python | Microsoft Azure"
	description="Introdução ao Cache Redis do Azure usando Python"
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="dwrede"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.devlang="python"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="10/23/2015"
	ms.author="sdanie"/>

# Como usar o Cache Redis do Azure com Python

> [AZURE.SELECTOR]
- [.Net](cache-dotnet-how-to-use-azure-redis-cache.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Este tópico mostra uma introdução ao Cache Redis do Azure usando Python.


## Pré-requisitos

Instale o [redis-py](https://github.com/andymccurdy/redis-py).


## Criar um cache Redis no Azure

No [portal de visualização do Azure](http://go.microsoft.com/fwlink/?LinkId=398536), clique em **Novo**, **Dados + Armazenamento** e selecione **Cache Redis**.

  ![][1]

Insira um nome de host DNS. Ele terá o formato `<name>.redis.cache.windows.net`. Clique em **Criar**.

  ![][2]

Depois de criar o cache, clique nele no portal de visualização para exibir as configurações do cache. Você precisará do seguinte:

- **Nome do host.** Esse é o nome que você inseriu ao criar o cache.
- **Porta.** Clique no link em **Portas** para exibir as portas. Use a porta SSL.
- **Chave de acesso.** Clique no link em **Chaves** e copie a chave primária.

## Adicionar algo ao cache e recuperá-lo

    >>> import redis
    >>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
          port=6380, db=0, password='<key>', ssl=True)
    >>> r.set('foo', 'bar')
    True
    >>> r.get('foo')
    b'bar'

Substitua *&lt;name&gt;* pelo nome do cache e *&lt;key&gt;* por sua chave de acesso.


<!--Image references-->
[1]: ./media/cache-python-get-started/cache01.png
[2]: ./media/cache-python-get-started/cache02.png

<!---HONumber=Nov15_HO1-->