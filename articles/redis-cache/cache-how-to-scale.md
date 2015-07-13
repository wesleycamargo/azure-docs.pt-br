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
	ms.date="06/18/2015" 
	ms.author="sdanie"/>

# Como dimensionar o Cache Redis do Azure

>[AZURE.NOTE]O recurso de dimensionamento do Cache Redis do Azure está atualmente na visualização.

O Cache Redis do Azure tem diferentes ofertas de cache que fornecem flexibilidade na escolha do tamanho do e dos recursos de cache. Se os requisitos de seu aplicativo se alterarem depois que um cache for criado, você poderá dimensionar o tamanho do cache usando a folha **Alterar camada de preços** no [portal do Azure](https://portal.azure.com).

>[AZURE.NOTE]Ao dimensionar um Cache Redis do Azure, você pode alterar o tamanho, mas não pode mudar de um cache Padrão para um cache Básico e vice-versa.

## Quando dimensionar

Você pode usar os recursos de [monitoramento](cache-how-to-monitor.md) do Cache Redis do Azure para monitorar a integridade e o desempenho de seus aplicativos de cache e para ajudar a determinar se é necessário dimensionar o cache.

Você pode monitorar as métricas a seguir para ajudá-lo a determinar se é preciso dimensionar.

-	Carga do Servidor Redis
-	Uso de Memória
-	Largura de Banda de Rede
-	Uso da CPU

Se determinar que o cache não atende mais aos requisitos de seu aplicativo, você poderá mudar para uma camada de preços cache maior ou menor que seja adequada ao aplicativo. Para obter mais informações sobre como determinar qual camada de preços de cache usar, consulte [Qual oferta e tamanho do Cache Redis devo usar](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## Dimensionar um cache
Para dimensionar o cache, [navegue até o cache](https://msdn.microsoft.com/library/azure/dn793612.aspx#RedisCacheConfiguration) no [portal do Azure](https://portal.azure.com) e clique na **Camada padrão** ou na **Camada básica** na folha do **Cache Redis**.

![Camada de preços][redis-cache-pricing-tier-part]

Selecione a camada de preços desejada na folha **Camada de preços** e clique em **Selecionar**.

![Camada de preços][redis-cache-pricing-tier-blade]

>[AZURE.NOTE]Os caches não podem alterar os planos de Básico para Padrão ou vice-versa, e você não pode reduzir verticalmente um cache de um dos tamanhos maiores para 250 MB. Você pode escalar verticalmente de um cache de 250 MB para um tamanho maior, mas não poderá dimensionar de volta para a camada de preços de 250 MB. Se precisar alterar de Básico para Padrão ou reduzir verticalmente para o tamanho de 250 MB, você deverá criar um novo cache.

Enquanto o cache é dimensionado para a nova camada de preços, um status **Dimensionando** é exibido na folha do **Cache Redis**.

![Dimensionamento][redis-cache-scaling]

Quando o dimensionamento for concluído, o status será alterado de **Dimensionando** para **Executando**.

>[AZURE.IMPORTANT]Durante operações de dimensionamento, os caches Básicos ficam offline, e todos os dados em cache são perdidos. Uma vez concluída a operação de dimensionamento, o cache Básico estará online novamente, sem dados. Os caches Padrão permanecem online durante uma operação de dimensionamento, e normalmente nenhum dado é perdido ao se dimensionar um cache Padrão para um tamanho maior. Ao se dimensionar um cache Padrão para um tamanho menor, alguns dados poderão ser perdidos se o novo tamanho for menor do que a quantidade de dados armazenados em cache. Se dados forem perdidos ao se reduzir, as chaves serão removidas usando a política de remoção [allkeys lru](http://redis.io/topics/lru-cache). Observe que, embora os caches Padrão tenham um SLA de 99,9% de disponibilidade, não há SLA para perda de dados.

## Como automatizar uma operação de dimensionamento

Além de dimensionar sua instância de Cache Redis do Azure no portal do Azure, você pode dimensionar usando [MAML (Bibliotecas de Gerenciamento do Microsoft Azure)](http://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/). Para dimensionar seu cache, chame o método `IRedisOperations.CreateOrUpdate` e passeo novo tamanho para `RedisProperties.SKU.Capacity`.

    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://msdn.microsoft.com/pt-br/library/azure/dn790557.aspx#bk_portal
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

## Depois do dimensionamento, é necessário alterar minhas chaves de acesso ou o nome do cache?

Não, o nome do cache e as chaves permanecem inalterados durante uma operação de dimensionamento.

## Como funciona o dimensionamento?

Quando um cache **Básico** é dimensionado, ele é desligado e um novo cache é provisionado com o novo tamanho. Durante esse tempo, o cache não está disponível e todos os dados em cache são perdidos.

Quando um cache **Padrão** é dimensionado, uma das réplicas é desligada e provisionada novamente para o novo tamanho, os dados são transferidos e a outra réplica realiza um failover antes de ser provisionada novamente, de forma semelhante ao processo que ocorre durante uma falha de um dos nós de cache.

## Perderei dados de meu cache durante o dimensionamento?

Quando um cache **Básico** é dimensionado, todos os dados são perdidos, e o cache fica indisponível durante a operação de dimensionamento.

Quando um cache **Padrão** é dimensionado para um tamanho maior, normalmente todos os dados são preservados. Ao se dimensionar um cache **Padrão** para um tamanho menor, dados podem ser perdidos, dependendo da quantidade de dados no cache em relação ao novo tamanho quando ele for dimensionado. Se dados forem perdidos ao se reduzir, as chaves serão removidas usando a política de remoção [allkeys-lru](http://redis.io/topics/lru-cache). Observe que, embora os caches Padrão tenham um SLA de 99,9% de disponibilidade, não há SLA para perda de dados.

## O cache estará disponível durante o dimensionamento?

Caches **Padrão** permanecem disponíveis durante a operação de dimensionamento.

Caches **Básicos** ficam offline durante a operação de dimensionamento.

## Operações que não têm suporte

Você não pode alterar de um cache **Básico** para um **Padrão** ou vice-versa durante uma operação de dimensionamento.

Você pode escalar verticalmente de um cache **C0** (250 MB) para um tamanho maior, mas não pode dimensionar de um tamanho maior para um cache **C0**.

Se uma operação de dimensionamento falhar, o serviço tentará reverter a operação, e o cache será revertido para o tamanho original.

## Quanto tempo o dimensionamento leva?

O dimensionamento leva aproximadamente 20 minutos, dependendo da quantidade de dados no cache.

## Como saber quando o dimensionamento é concluído?

No portal, você pode ver a operação de dimensionamento em andamento. Quando o dimensionamento for concluído, o status do cache será alterado para **Executando**.

## Por que esse recurso está na visualização?

Estamos lançando esse recurso para obter comentários. Com base nos comentários, lançaremos esse recurso para Disponibilidade Geral em breve.





  
<!-- IMAGES -->
[redis-cache-pricing-tier-part]: ./media/cache-how-to-scale/redis-cache-pricing-tier-part.png

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png

<!---HONumber=July15_HO1-->