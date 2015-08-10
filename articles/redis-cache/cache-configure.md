<properties 
   pageTitle="Como configurar o Cache Redis do Azure"
   description="Entenda a configuração padrão Redis Cache Redis do Azure e aprenda a configurar as instâncias de Cache Redis do Azure"
   services="redis-cache"
   documentationCenter="na"
   authors="steved0x"
   manager="dwrede"
   editor="tysonn" />
<tags 
   ms.service="cache"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="tbd"
   ms.date="07/24/2015"
   ms.author="sdanie" />

# Como configurar o Cache Redis do Azure

Este tópico descreve como examinar e atualizar a configuração para suas instâncias de Cache Redis do Azure e abrange a configuração do servidor Redis padrão para instâncias de Cache Redis do Azure.

## Definir configurações de cache Redis

Os caches podem ser acessados no [portal de visualização do Microsoft Azure](https://portal.azure.com) usando a folha **Procurar**.

![Folha de navegação de Cache Redis do Azure](./media/cache-configure/IC796920.png)

Clique em **Caches Redis** para exibir seus caches.

![Lista de cache de navegação de Cache Redis do Azure](./media/cache-configure/IC796921.png)

Selecione o cache desejado para exibir as propriedades desse cache.

![Todas as configurações de Cache Redis](./media/cache-configure/IC808312.png)

Clique em **configurações** ou **Todas as configurações** para exibir e configurar o cache.

![Configurações de Cache Redis](./media/cache-configure/IC808313.png)

## Propriedades

Clique em **Propriedades** para exibir informações sobre o cache, incluindo o ponto de extremidade do cache e as portas.

![Propriedades de Cache Redis](./media/cache-configure/IC808314.png)

## Chaves de acesso

Clique em **Chaves de acesso** para exibir ou regenerar as chaves de acesso para seu cache. Essas chaves são usadas junto com o nome do host e as portas na folha **Propriedades** pelos clientes que se conectam ao seu cache.

![Chaves de acesso de Cache Redis](./media/cache-configure/IC808315.png)

## Portas de acesso

Por padrão, o acesso não SSL está desabilitado para novos caches. Para habilitar a porta não SSL, clique na folha **Portas de acesso** e clique em **Não**.

![Portas de acesso de Cache Redis](./media/cache-configure/IC808316.png)

## Diagnostics

Clique em **Diagnóstico** para configurar a conta de armazenamento usada para armazenar diagnósticos de cache.

![Diagnósticos de Cache Redis](./media/cache-configure/IC808317.png)

Para obter mais informações, consulte [Como monitorar o Cache Redis do Azure](cache-how-to-monitor.md).

## Maxmemory-policy e maxmemory-reserved

Clique em **Política Maxmemory** para configurar políticas de memória para o cache. A configuração **maxmemory-policy** configura a política de remoção para o cache e **maxmemory-reserved** configura a memória reservada para processos fora do cache.

![Política Maxmemory de Cache Redis](./media/cache-configure/IC808318.png)

A **política Maxmemory** permite que você escolha dentre as seguintes políticas de remoção.

-	volatile-lru - esse é o padrão.
-	allkeys-lru
-	volatile-random
-	allkeys-random
-	volatile-ttl
-	noeviction

Para obter mais informações sobre políticas maxmemory, consulte [Políticas de remoção](http://redis.io/topics/lru-cache#eviction-policies).

A configuração **maxmemory-reserved** configura a quantidade de memória em MB reservada para operações de não cache, como replicação, durante o failover. Ela também pode ser usada quando você tem uma taxa alta de fragmentação. Definir esse valor permite que você tenha uma experiência mais consistente do servidor Redis quando sua carga varia. Esse valor deve ser definido como maior para cargas de trabalho que executam muitas operações de gravação. Quando a memória é reservada para essas operações, não está disponível para o armazenamento de dados armazenados em cache.

>[AZURE.IMPORTANT]A configuração **maxmemory-reserved** só está disponível para os caches Padrão.

## Notificações de Keyspace (configurações avançadas)

Clique em **Configurações avançadas** para configurar as notificações de keyspace do Redis. Notificações de Keyspace permitem que os clientes recebam notificações quando ocorrerem determinados eventos.

![Configurações avançadas de Cache Redis](./media/cache-configure/IC808319.png)

>[AZURE.IMPORTANT]Notificações de Keyspace e a configuração **notify-keyspace-events** estão disponíveis apenas para os caches Padrão.

Para obter mais informações, consulte [Notificações de Keyspace Redis](http://redis.io/topics/notifications). Para o código de exemplo, consulte o arquivo [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) no exemplo [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).

## Usuários e marcas

![Usuários e marcas de Cache Redis](./media/cache-configure/IC808320.png)

A seção **Usuários** dá suporte para controle de acesso baseado em função (RBAC) no portal para ajudar as organizações a atender aos seus requisitos de gerenciamento de acesso de forma simples e precisa. Para obter mais informações, consulte [Controle de acesso baseado em função no portal de visualização do Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=512803).

A seção **marcas** o ajuda a organizar seus recursos. Para obter mais informações, consulte [Usando marcas para organizar os recursos do Azure](../resource-group-using-tags.md).

## Configuração padrão do servidor Redis

Novas instâncias de Cache Redis do Azure são configuradas com os seguintes valores de configuração padrão Redis.

>[AZURE.NOTE]As configurações nesta seção não podem ser alteradas usando o método `StackExchange.Redis.IServer.ConfigSet`. Se esse método for chamado com um dos comandos nesta seção, será gerada uma exceção similar à seguinte:
>
>`StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>  
>Todos os valores que podem ser configurados, como **max-memory-policy**, podem ser configurados por meio do portal.

|Configuração|Valor padrão|Descrição|
|---|---|---|
|databases|16|O banco de dados padrão é o DB 0. você pode selecionar um valor diferente por conexão usando connection.GetDataBase(dbid), em que dbid é um número entre 0 e 15.|
|maxclients|10\.000|Esse é o número máximo de clientes conectados permitidos ao mesmo tempo. Quando o limite é atingido, Redis fecha todas as novas conexões enviando um erro "número máximo de clientes atingido".|
|maxmemory-policy|volatile-lru|A política MaxMemory é a configuração de como o Redis seleciona o que remover quando maxmemory (o tamanho da oferta de cache que você selecionou quando criou o cache) é atingido. Com o Cache Redis do Azure, a configuração padrão é volatile-lru, que remove as chaves com um conjunto de expiração usando um algoritmo LRU. Essa configuração pode ser definida no portal. Para obter mais informações, consulte [Maxmemory-policy e maxmemory-reserved](#maxmemory-policy-and-maxmemory-reserved).|
|maxmemory-samples|3|LRU e algoritmos TTL mínimos não são algoritmos precisos, mas aproximados (para economizar memória), para que você possa selecionar também o tamanho da amostra para verificar. Por exemplo, por padrão, o Redis verificará três chaves e escolherá aquela que foi usada menos recentemente.|
|lua-time-limit|5\.000|Tempo máximo de execução de um script Lua em milissegundos. Se o tempo de execução máximo for atingido, o Redis registrará em log que um script ainda está em execução depois do tempo máximo permitido e começará a responder a consultas com um erro.|
|lua-event-limit|500|Esse é o tamanho máximo da fila de eventos de script.|
|client-output-buffer-limit normalclient-output-buffer-limit pubsub|0 0 032mb 8mb 60|Os limites de buffer de saída do cliente podem ser usados para impor a desconexão de clientes que não estão lendo dados do servidor de forma rápida o suficiente, por algum motivo (uma razão comum é que um cliente Pub/Sub não consegue consumir mensagens de forma tão rápida quanto o editor consegue produzi-las). Para obter mais informações, consulte [http://redis.io/topics/clients](http://redis.io/topics/clients).|

## Comandos Redis não têm suporte no Cache Redis do Azure

>[AZURE.IMPORTANT]Como a configuração e o gerenciamento de instâncias de Cache Redis do Azure são feitas usando o portal do Azure, os comandos a seguir são desabilitados. Se tentar invocá-los, você receberá uma mensagem de erro semelhante a `"(error) ERR unknown command"`.
>
>-	BGREWRITEAOF
>-	BGSAVE
>-	CONFIG
>-	DEBUG
>-	MIGRATE
>-	SALVAR
>-	SHUTDOWN
>-	SLAVEOF

Para obter mais informações sobre os comandos do Redis, consulte [http://redis.io/commands](http://redis.io/commands).

## Console do Redis

Você pode emitir com segurança comandos para suas instâncias de Cache Redis do Azure usando o **Console Redis** que está disponível para os caches Standard. Para acessar o Console do Redis, clique em **Console** da folha **Cache Redis**.

![Console do Redis](./media/cache-configure/redis-console-menu.png)

>[AZURE.IMPORTANT]O Console do Redis só está disponível para os caches Standard.

Para emitir comandos em sua instância de cache, simplesmente digite no comando desejado no console.

![Console do Redis](./media/cache-configure/redis-console.png)

Para obter a lista de comandos Redis que estão desabilitados para o Cache Redis do Azure, consulte a seção [Comandos Redis que não têm suporte no Cache Redis do Azure](#redis-commands-not-supported-in-azure-redis-cache) anterior. Para obter mais informações sobre os comandos do Redis, consulte [http://redis.io/commands](http://redis.io/commands).

## Próximas etapas
-	Para obter mais informações sobre como trabalhar com os comandos Redis, consulte [Como executar comandos Redis?](cache-faq.md#how-can-i-run-redis-commands).

<!---HONumber=July15_HO5-->