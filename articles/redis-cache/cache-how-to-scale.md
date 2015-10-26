<properties 
	pageTitle="Como dimensionar o Cache Redis do Azure" 
	description="Saiba como dimensionar suas instâncias do Cache Redis do Azure" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/30/2015" 
	ms.author="sdanie"/>

# Como dimensionar o Cache Redis do Azure

>[AZURE.NOTE]O recurso de dimensionamento do Cache Redis do Azure está atualmente na visualização. Durante o período de visualização, não é possível escalonar para, de ou em um cache da camada premium.

O Cache Redis do Azure tem diferentes ofertas de cache que fornecem flexibilidade na escolha do tamanho do e dos recursos de cache. Se os requisitos de seu aplicativo se alterarem depois que um cache for criado, você poderá dimensionar o tamanho do cache usando a folha **Alterar camada de preços** no [portal de visualização do Azure](https://portal.azure.com).

## Quando dimensionar

Você pode usar os recursos de [monitoramento](cache-how-to-monitor.md) do Cache Redis do Azure para monitorar a integridade e o desempenho de seus aplicativos de cache e para ajudar a determinar se é necessário dimensionar o cache.

Você pode monitorar as métricas a seguir para ajudá-lo a determinar se é preciso dimensionar.

-	Carga do Servidor Redis
-	Uso de Memória
-	Largura de Banda de Rede
-	Uso da CPU

Se determinar que o cache não atende mais aos requisitos de seu aplicativo, você poderá mudar para uma camada de preços cache maior ou menor que seja adequada ao aplicativo. Para obter mais informações sobre como determinar qual camada de preços de cache usar, consulte [Qual oferta e tamanho do Cache Redis devo usar](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## Dimensionar um cache
Para dimensionar seu cache, [navegue até o cache](cache-configure.md#configure-redis-cache-settings) no [portal de visualização](https://portal.azure.com) e clique em **Configurações**, **Camada de preços**.

Você também pode clicar na parte **Camada padrão** ou **Camada básica** na folha **Cache Redis**.

![Camada de preços][redis-cache-pricing-tier-part]

Selecione a camada de preços desejada na folha **Camada de preços** e clique em **Selecionar**.

![Camada de preços][redis-cache-pricing-tier-blade]

>[AZURE.NOTE]Você pode dimensionar para uma camada de preços diferente com as restrições a seguir.
>
>-	Não é possível escalonar para ou de um cache **Premium**.
>-	Não é possível escalonar de um cache **Standard** para um cache **Básico**.
>-	É possível escalonar de um cache **Básico** para um cache **Standard**, mas não é possível alterar o tamanho ao mesmo tempo. Se precisar de um tamanho diferente, você pode fazer uma operação de dimensionamento subsequente para o tamanho desejado.
>-	Não é possível escalonar de um tamanho maior para o tamanho **C0 (250 MB)**.

Enquanto o cache é dimensionado para a nova camada de preços, um status **Dimensionando** é exibido na folha do **Cache Redis**.

![Dimensionamento][redis-cache-scaling]

Quando o dimensionamento for concluído, o status será alterado de **Dimensionando** para **Executando**.

## Como automatizar uma operação de dimensionamento

Além de escalonar sua instância do Cache Redis do Azure no portal de visualização, é possível escalonar usando a [MAML (Bibliotecas de Gerenciamento do Microsoft Azure)](http://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/). Para dimensionar seu cache, chame o método `IRedisOperations.CreateOrUpdate` e passeo novo tamanho para `RedisProperties.SKU.Capacity`.

    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }

Para obter mais informações, consulte o exemplo [Gerenciar o Cache Redis usando MAML](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML).

## Perguntas frequentes sobre dimensionamento

A lista a seguir contém as respostas a perguntas frequentes sobre o dimensionamento do Cache Redis do Azure.

## Posso escalonar para um cache Premium, por meio dele ou nele?

Durante o período de visualização, a escala não está disponível para os caches **Premium**.

## Depois do dimensionamento, é necessário alterar minhas chaves de acesso ou o nome do cache?

Não, o nome do cache e as chaves permanecem inalterados durante uma operação de dimensionamento.

## Como funciona o dimensionamento?

Quando um cache **Básico** é escalonado para um tamanho diferente, ele é desligado e um novo cache é provisionado com o novo tamanho. Durante esse tempo, o cache não está disponível e todos os dados em cache são perdidos.

Quando um cache **Básico** é escalonado para um cache **Standard**, um cache de réplica é provisionado e os dados são copiados do cache primário no cache de réplica. O cache permanece disponível durante o processo de dimensionamento.

Quando um cache **Standard** é escalonado para um tamanho diferente, uma das réplicas é desligada e provisionada novamente para o novo tamanho, os dados são transferidos e a outra réplica executa um failover antes de ser provisionada novamente, de forma semelhante ao processo que ocorre durante uma falha de um dos nós de cache.

## Perderei dados de meu cache durante o dimensionamento?

Quando um cache **Básico** é escalonado para um novo tamanho, todos os dados são perdidos, e o cache fica indisponível durante a operação de escala.

Quando um cache **Básico** é escalonado para um cache **Standard**, normalmente os dados no cache são preservados.

Quando um cache **Standard** é escalonado para um tamanho maior, normalmente todos os dados são preservados. Ao se dimensionar um cache **Padrão** para um tamanho menor, dados podem ser perdidos, dependendo da quantidade de dados no cache em relação ao novo tamanho quando ele for dimensionado. Se dados forem perdidos ao se reduzir, as chaves serão removidas usando a política de remoção [allkeys-lru](http://redis.io/topics/lru-cache).

Observe que, embora os caches Padrão tenham um SLA de 99,9% de disponibilidade, não há SLA para perda de dados.

## O cache estará disponível durante o dimensionamento?

Caches **Padrão** permanecem disponíveis durante a operação de dimensionamento.

Os caches **Básicos** ficam offline durante as operações de escala para um tamanho diferente, mas permanecem disponíveis durante a escala de **Básico** para **Standard**.

## Operações que não têm suporte

Não é possível escalonar para, de ou em um cache **Premium**.

Não é possível alterar de um cache **Standard** para um **Básico**.

É possível escalonar de um cache **Básico** para um cache **Standard**, mas não é possível alterar o tamanho ao mesmo tempo. Se precisar de um tamanho diferente, você pode fazer uma operação de dimensionamento subsequente para o tamanho desejado.

Você pode escalar verticalmente de um cache **C0** (250 MB) para um tamanho maior, mas não pode dimensionar de um tamanho maior para um cache **C0**.

Se uma operação de dimensionamento falhar, o serviço tentará reverter a operação, e o cache será revertido para o tamanho original.

## Quanto tempo o dimensionamento leva?

O dimensionamento leva aproximadamente 20 minutos, dependendo da quantidade de dados no cache.

## Como saber quando o dimensionamento é concluído?

No portal de visualização, você pode ver a operação de dimensionamento em andamento. Quando o dimensionamento for concluído, o status do cache será alterado para **Executando**.

## Por que esse recurso está na visualização?

Estamos lançando esse recurso para obter comentários. Com base nos comentários, lançaremos esse recurso para Disponibilidade Geral em breve.





  
<!-- IMAGES -->
[redis-cache-pricing-tier-part]: ./media/cache-how-to-scale/redis-cache-pricing-tier-part.png

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png

<!---HONumber=Oct15_HO3-->