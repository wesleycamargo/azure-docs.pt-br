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
	ms.date="12/16/2015"
	ms.author="sdanie" />

# Como configurar o Cache Redis do Azure

Este tópico descreve como examinar e atualizar a configuração para suas instâncias de Cache Redis do Azure e abrange a configuração do servidor Redis padrão para instâncias de Cache Redis do Azure.

>[AZURE.NOTE]Para obter mais informações sobre como configurar e usar os recursos de cache premium, veja [Como configurar a persistência para um Cache Redis do Azure Premium](cache-how-to-premium-persistence.md), [Como configurar o clustering para um Cache Redis do Azure Premium](cache-how-to-premium-clustering.md) e [Como configurar o suporte da Rede Virtual para um Cache Redis do Azure Premium](cache-how-to-premium-vnet.md).

## Definir configurações de cache Redis

Os caches podem ser acessados no [Portal do Azure](https://portal.azure.com) usando a folha **Procurar**.

![Folha de navegação de Cache Redis do Azure](./media/cache-configure/IC796920.png)

Clique em **Caches Redis** para exibir seus caches.

![Lista de cache de navegação de Cache Redis do Azure](./media/cache-configure/IC796921.png)

Selecione o cache desejado para exibir as propriedades desse cache.

![Todas as configurações de Cache Redis](./media/cache-configure/IC808312.png)

Clique em **Configurações** ou em **Todas as configurações** para exibir e configurar o cache.

![Configurações de Cache Redis](./media/cache-configure/IC808313.png)

## Propriedades

Clique em **Propriedades** para exibir informações sobre o cache, incluindo o ponto de extremidade e as portas do cache.

![Propriedades de Cache Redis](./media/cache-configure/IC808314.png)

## Chaves de acesso

Clique em **Teclas de acesso** para exibir ou regenerar as teclas de acesso para seu cache. Essas teclas são usadas junto com o nome do host e as portas na folha **Propriedades** pelos clientes que se conectam ao seu cache.

![Chaves de acesso de Cache Redis](./media/cache-configure/IC808315.png)

## Portas de acesso

Por padrão, o acesso não SSL está desabilitado para novos caches. Para habilitar a porta não SSL, clique na folha **Portas de Acesso** e clique em **Não**.

![Portas de acesso de Cache Redis](./media/cache-configure/IC808316.png)

## Camada de preços

Clique em **Tipo de preço** para exibir ou alterar o tipo de preço do cache. Para obter mais informações sobre escala, veja [Como escalonar o Cache Redis do Azure](cache-how-to-scale.md).

![Camada de preços do Cache Redis](./media/cache-configure/pricing-tier.png)

## Diagnostics

Clique em **Diagnóstico** para configurar a conta de armazenamento usada para armazenar o diagnóstico de cache.

![Diagnósticos de Cache Redis](./media/cache-configure/IC808317.png)

Para obter mais informações, veja [Como monitorar o Cache Redis do Azure](cache-how-to-monitor.md).

## Maxmemory-policy e maxmemory-reserved

Clique em **política Maxmemory** para configurar as políticas de memória para o cache. A configuração **maxmemory-policy** define a política de remoção para o cache e **maxmemory-reserved** define a memória reservada para processos não cache.

![Política Maxmemory de Cache Redis](./media/cache-configure/IC808318.png)

A **política Maxmemory** permite que você escolha uma das políticas de remoção a seguir.

-	volatile-lru - esse é o padrão.
-	allkeys-lru
-	volatile-random
-	allkeys-random
-	volatile-ttl
-	noeviction

Para saber mais sobre políticas Maxmemory, veja [Políticas de remoção](http://redis.io/topics/lru-cache#eviction-policies).

A configuração **maxmemory-reserved** define a quantidade de memória em MB reservada para operações não cache, como replicação durante o failover. Ela também pode ser usada quando você tem uma taxa alta de fragmentação. Definir esse valor permite que você tenha uma experiência mais consistente do servidor Redis quando sua carga varia. Esse valor deve ser definido como maior para cargas de trabalho que executam muitas operações de gravação. Quando a memória é reservada para essas operações, não está disponível para o armazenamento de dados armazenados em cache.

>[AZURE.IMPORTANT]A configuração **maxmemory-reserved** está disponível apenas para os caches Standard e Premium.

## Notificações de Keyspace (configurações avançadas)

Clique em **Configurações avançadas** para configurar as notificações de keyspace do Redis. Notificações de Keyspace permitem que os clientes recebam notificações quando ocorrerem determinados eventos.

![Configurações avançadas de Cache Redis](./media/cache-configure/IC808319.png)

>[AZURE.IMPORTANT]As notificações de Keyspace e a configuração **notify-keyspace-events** estão disponíveis apenas para os caches Standard e Premium.

Para obter mais informações, veja [Notificações de Keyspace do Redis](http://redis.io/topics/notifications). Para o código de exemplo, veja o arquivo [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) no exemplo [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).

## Persistência de dados do Redis

Clique em **Persistência de dados do Redis** para habilitar, desabilitar ou configurar a persistência de dados para o cache premium.

![Persistência de dados do Redis](./media/cache-configure/redis-cache-persistence-settings.png)

Para habilitar a persistência do Redis, clique em **Habilitado** para habilitar o backup do RDB (banco de dados do Redis). Para desabilitar a persistência do Redis, clique em **Desabilitado**.

Para configurar o intervalo de backup, selecione uma **Frequência de Backup** na lista suspensa. As opções incluem **15 minutos**, **30 minutos**, **60 minutos**, **6 horas**, **12 horas** e **24 horas**. Esse intervalo inicia a contagem regressiva depois que a operação de backup anterior for concluída com êxito e quando ela expira, um novo backup é iniciado.

Clique em **Conta de Armazenamento** para selecionar a conta de armazenamento a ser usada e escolha a **Chave primária** ou **Chave secundária** a ser usada na lista suspensa **Chave de Armazenamento**. Você deve escolher uma conta de armazenamento na mesma região que o cache e uma conta do **Armazenamento Premium** é recomendada, pois o armazenamento premium tem uma maior taxa de transferência. Sempre que a chave de armazenamento para a sua conta de persistência é regenerada, você deve escolher novamente a chave desejada no menu suspenso **Chave de Armazenamento**.

Clique em **OK** para salvar a configuração de persistência.

>[AZURE.IMPORTANT]A persistência de dados do Redis só está disponível para os caches Premium. Para obter mais informações, veja [Como configurar a persistência para um Cache Redis do Azure Premium](cache-how-to-premium-persistence.md).

<a name="cluster-size"></a>
## Tamanho do Cluster Redis

Clique em **Tamanho do Cluster Redis (VISUALIZAÇÃO)** para alterar o tamanho do cluster de um cache premium em execução com clustering habilitado.

>[AZURE.NOTE]Observe que, enquanto a camada Cache Redis do Azure Premium foi liberada para disponibilidade geral, o recurso de Tamanho do Cluster Redis está atualmente em visualização.

![Tamanho do cluster Redis](./media/cache-configure/redis-cache-redis-cluster-size.png)

Para alterar o tamanho do cluster, use o controle deslizante ou digite um número entre 1 e 10 na caixa de texto **Contagem de fragmentos** e clique em **OK** para salvar.

>[AZURE.IMPORTANT]O clustering está disponível apenas para os Caches premium. Para saber mais, confira [Como configurar o clustering para um Cache Redis do Azure Premium](cache-how-to-premium-clustering.md).


## Usuários e marcas

![Usuários e marcas de Cache Redis](./media/cache-configure/IC808320.png)

A seção **Usuários** dá suporte ao RBAC (controle de acesso baseado em função) no Portal do Azure para ajudar as organizações a atender aos seus requisitos de gerenciamento de acesso de maneira simples e precisa. Para saber mais, confira [Controle de acesso baseado em função no Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=512803).

A seção **Marcas** o ajuda a organizar seus recursos. Para saber mais, confira [Usando marcas para organizar os recursos do Azure](../resource-group-using-tags.md).

## Configuração padrão do servidor Redis

Novas instâncias de Cache Redis do Azure são configuradas com os seguintes valores de configuração padrão Redis.

>[AZURE.NOTE]As configurações nesta seção não podem ser alteradas com o método `StackExchange.Redis.IServer.ConfigSet`. Se esse método for chamado com um dos comandos nesta seção, será gerada uma exceção similar à seguinte:
>
>`StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>  
>Todos os valores que podem ser configurados, como **max-memory-policy**, podem ser configurados por meio do Portal do Azure ou de ferramentas de gerenciamento de linha de comando, como a CLI do Azure ou o PowerShell.

|Configuração|Valor padrão|Descrição|
|---|---|---|
|databases|16|O banco de dados padrão é o DB 0. você pode selecionar um valor diferente por conexão usando connection.GetDataBase(dbid), em que dbid é um número entre 0 e 15.|
|maxclients|Depende do tipo de preço<sup>1</sup>|Esse é o número máximo de clientes conectados permitidos ao mesmo tempo. Quando o limite é atingido, Redis fecha todas as novas conexões enviando um erro "número máximo de clientes atingido".|
|maxmemory-policy|volatile-lru|A política MaxMemory é a configuração de como o Redis seleciona o que remover quando maxmemory (o tamanho da oferta de cache que você selecionou quando criou o cache) é atingido. Com o Cache Redis do Azure, a configuração padrão é volatile-lru, que remove as chaves com um conjunto de expiração usando um algoritmo LRU. Essa configuração pode ser definida no portal do Azure. Para saber mais, confira [Maxmemory-policy e maxmemory-reserved](#maxmemory-policy-and-maxmemory-reserved).|
|maxmemory-samples|3|LRU e algoritmos TTL mínimos não são algoritmos precisos, mas aproximados (para economizar memória), para que você possa selecionar também o tamanho da amostra para verificar. Por exemplo, por padrão, o Redis verificará três chaves e escolherá aquela que foi usada menos recentemente.|
|lua-time-limit|5\.000|Tempo máximo de execução de um script Lua em milissegundos. Se o tempo de execução máximo for atingido, o Redis registrará em log que um script ainda está em execução depois do tempo máximo permitido e começará a responder a consultas com um erro.|
|lua-event-limit|500|Esse é o tamanho máximo da fila de eventos de script.|
|client-output-buffer-limit normalclient-output-buffer-limit pubsub|0 0 032mb 8mb 60|Os limites de buffer de saída do cliente podem ser usados para impor a desconexão de clientes que não estão lendo dados do servidor de forma rápida o suficiente, por algum motivo (uma razão comum é que um cliente Pub/Sub não consegue consumir mensagens de forma tão rápida quanto o editor consegue produzi-las). Para saber mais, confira [http://redis.io/topics/clients](http://redis.io/topics/clients).|

<sup>1</sup>`maxclients` é diferente para cada tipo de preço do Cache Redis do Azure.

-	Caches Básico e Standard
	-	Cache C0 (250 MB) - até 256 conexões
	-	Cache C1 (1 GB) - até 1.000 conexões
	-	Cache C2 (2.5 GB) - até 2.000 conexões
	-	Cache C3 (6 GB) - até 5.000 conexões
	-	Cache C4 (13 GB) - até 10.000 conexões
	-	Cache C5 (26 GB) - até 15.000 conexões
	-	Cache C6 (53 GB) - até 20.000 conexões
-	Caches Premium
	-	P1 (6 GB - 60 GB) - até 7.500 conexões
	-	P2 (13 GB - 130 GB) - até 15.000 conexões
	-	P3 (26 GB - 260 GB) - até 30.000 conexões
	-	P4 (53 GB - 530 GB) - até 40.000 conexões

## Comandos Redis não têm suporte no Cache Redis do Azure

>[AZURE.IMPORTANT]Como a configuração e o gerenciamento de instâncias de Cache Redis do Azure é gerenciada pela Microsoft, os comandos a seguir são desabilitados. Se tentar invocá-los, você receberá uma mensagem de erro semelhante a `"(error) ERR unknown command"`.
>
>-	BGREWRITEAOF
>-	BGSAVE
>-	CONFIG
>-	DEBUG
>-	MIGRATE
>-	SALVAR
>-	SHUTDOWN
>-	SLAVEOF

Para saber mais sobre os comandos do Redis, confira [http://redis.io/commands](http://redis.io/commands).

## Console do Redis

Você pode emitir com segurança comandos para as suas instâncias do Cache Redis do Azure usando o **Console do Redis**, que está disponível para os caches Standard e Premium.

>[AZURE.IMPORTANT]O Console do Redis não funciona com VNET ou cluster.
>
>-	[VNET](cache-how-to-premium-vnet.md) – quando o seu cache faz parte de uma VNET, somente os clientes na VNET podem acessar o cache. Como o Console do Redis usa o cliente redis cli.exe hospedado em máquinas virtuais que não fazem parte da sua VNET, não pode se conectar ao seu cache.
>-	[Clustering](cache-how-to-premium-clustering.md) – o Console do Redis usa o cliente redis-cli.exe, que não dá suporte a clustering neste momento. O utilitário redis-cli na ramificação [instável](http://redis.io/download) do repositório do Redis no GitHub implementa um suporte básico quando iniciado com a opção `-c`. Para saber mais, confira [Reproduzindo com o cluster](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) em [http://redis.io](http://redis.io) no [tutorial do cluster Redis](http://redis.io/topics/cluster-tutorial).

Para acessar o Console do Redis, clique em **Console** na folha **Cache Redis**.

![Console do Redis](./media/cache-configure/redis-console-menu.png)

Para emitir comandos em sua instância de cache, simplesmente digite no comando desejado no console.

![Console do Redis](./media/cache-configure/redis-console.png)

Para obter uma lista de comandos do Redis que estão desabilitados para o Cache Redis do Azure, confira a seção anterior [Comandos do Redis sem suporte no Cache Redis do Azure](#redis-commands-not-supported-in-azure-redis-cache). Para saber mais sobre os comandos do Redis, confira [http://redis.io/commands](http://redis.io/commands).

## Próximas etapas
-	Para saber mais sobre como trabalhar com os comandos do Redis, confira [Como faço para executar comandos do Redis?](cache-faq.md#how-can-i-run-redis-commands).

<!---HONumber=AcomDC_1223_2015-->