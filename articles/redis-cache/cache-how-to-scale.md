<properties 
	pageTitle="Como dimensionar o Cache Redis do Azure | Microsoft Azure" 
	description="Saiba como dimensionar suas instâncias do Cache Redis do Azure" 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/16/2016" 
	ms.author="sdanie"/>

# Como dimensionar o Cache Redis do Azure

>[AZURE.NOTE] O recurso de dimensionamento do Cache Redis do Azure está atualmente na visualização.

O Cache Redis do Azure tem diferentes ofertas de cache que fornecem flexibilidade na escolha do tamanho do e dos recursos de cache. Se os requisitos de seu aplicativo mudarem depois que um cache for criado, você poderá dimensionar o tamanho do cache usando a folha **Alterar tipo de preço** no [Portal do Azure](https://portal.azure.com).

## Quando dimensionar

Você pode usar os recursos de [monitoramento](cache-how-to-monitor.md) do Cache Redis do Azure para monitorar a integridade e o desempenho de seus aplicativos de cache e para ajudar a determinar se é necessário dimensionar o cache.

Você pode monitorar as métricas a seguir para ajudá-lo a determinar se é preciso dimensionar.

-	Carga do Servidor Redis
-	Uso de Memória
-	Largura de Banda de Rede
-	Uso da CPU

Se determinar que o cache não atende mais aos requisitos de seu aplicativo, você poderá mudar para uma camada de preços cache maior ou menor que seja adequada ao aplicativo. Para obter mais informações sobre como determinar qual camada de preços de cache usar, consulte [Qual oferta e tamanho do Cache Redis devo usar](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).

## Dimensionar um cache
Para dimensionar o cache, [navegue até ele](cache-configure.md#configure-redis-cache-settings) no [Portal do Azure](https://portal.azure.com) e clique em **Configurações**, **Tipo de preço**.

Você também pode clicar na parte **Camada de preços** da folha **Cache Redis**.

![Camada de preços][redis-cache-pricing-tier-part]

Selecione a camada de preços desejada na folha **Camada de preços** e clique em **Selecionar**.

![Camada de preços][redis-cache-pricing-tier-blade]

>[AZURE.NOTE] Você pode dimensionar para uma camada de preços diferente com as restrições a seguir.
>
>-	Você não pode dimensionar de uma camada de preços mais alta para uma camada de preços mais baixa.
>    -    Você não pode dimensionar para baixo de um cache **Premium** para um cache **Standard** ou **Básico**.
>    -    Você não pode dimensionar de um cache **Standard** para um cache **Básico**.
>-	É possível dimensionar de um cache **Básico** para um cache **Standard**, mas não é possível alterar o tamanho simultaneamente. Se precisar de um tamanho diferente, você pode fazer uma operação de dimensionamento subsequente para o tamanho desejado.
>-	Você não pode dimensionar de um cache **Básico** diretamente para um cache **Premium**. Você deve dimensionar do **Básico** para o **Standard** em uma única operação de dimensionamento e do **Standard** para o **Premium** em uma operação de dimensionamento subsequente.
>-	Não é possível dimensionar de um tamanho maior para o tamanho **C0 (250 MB)**.

Enquanto o cache é dimensionado para a nova camada de preços, um status **Dimensionando** é exibido na folha do **Cache Redis**.

![Dimensionamento][redis-cache-scaling]

Quando o dimensionamento for concluído, o status será alterado de **Dimensionando** para **Executando**.

## Como automatizar uma operação de dimensionamento

Além de dimensionar instâncias do Cache Redis do Azure no Portal do Azure, você pode dimensionar usando cmdlets do PowerShell do Cache Redis do Azure, a CLI do Azure e a MAML (Bibliotecas de Gerenciamento do Microsoft Azure).

-	[Dimensionar usando o PowerShell](#scale-using-powershell)
-	[Dimensionar usando a CLI do Azure](#scale-using-azure-cli)
-	[Dimensionar usando MAML](#scale-using-maml)

### Dimensionar usando o PowerShell

É possível dimensionar as instâncias do Cache Redis do Azure com o PowerShell usando o cmdlet [Set-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634518.aspx) quando as propriedades `Size`, `Sku` ou `ShardCount` forem modificadas. O exemplo a seguir mostra como dimensionar um cache denominado `myCache` para um cache de 2,5 GB.

	Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Para obter mais informações sobre o dimensionamento com o PowerShell, confira [To scale a Redis cache using Powershell](cache-howto-manage-redis-cache-powershell.md#scale).

### Dimensionar usando a CLI do Azure

Para dimensionar instâncias do Cache Redis do Azure usando a CLI do Azure, chame o comando `azure rediscache set` e transmita as mudanças de configuração desejadas que incluam novo tamanho, SKU ou tamanho de cluster, de acordo com a operação de dimensionamento desejada.

Para obter mais informações sobre o dimensionamento com CLI do Azure, confira [Alterar as configurações de um Cache Redis existente](cache-manage-cli.md#scale).

### Dimensionar usando MAML

Para dimensionar as instâncias do Cache Redis do Azure usando a [MAML (Bibliotecas de Gerenciamento do Microsoft Azure)](http://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/), chame o método `IRedisOperations.CreateOrUpdate` e transmita o novo tamanho para `RedisProperties.SKU.Capacity`.

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

-	[Posso escalonar para um cache Premium, por meio dele ou nele?](#can-i-scale-to-from-or-within-a-premium-cache)
-	[Depois do dimensionamento, é necessário alterar minhas chaves de acesso ou o nome do cache?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
-	[Como funciona o dimensionamento?](#how-does-scaling-work)
-	[Perderei dados de meu cache durante o dimensionamento?](#will-i-lose-data-from-my-cache-during-scaling)
-	[A configuração dos meus bancos de dados personalizados foi afetada durante o dimensionamento?](#is-my-custom-databases-setting-affected-during-scaling)
-	[O cache estará disponível durante o dimensionamento?](#will-my-cache-be-available-during-scaling)
-	[Operações que não têm suporte](#operations-that-are-not-supported)
-	[Quanto tempo o dimensionamento leva?](#how-long-does-scaling-take)
-	[Como saber quando o dimensionamento é concluído?](#how-can-i-tell-when-scaling-is-complete)
-	[Por que esse recurso está na visualização?](#why-is-this-feature-in-preview)

### Posso escalonar para um cache Premium, por meio dele ou nele?

-	Você não pode dimensionar de um cache **Premium** para um tipo de preço **Básico** ou **Standard**.
-	Você pode dimensionar de um tipo de preço de cache **Premium** para outro.
-	Você não pode dimensionar de um cache **Básico** diretamente para um cache **Premium**. Primeiro, você deve dimensionar do **Básico** para o **Standard** em uma única operação de dimensionamento e do **Standard** para o **Premium** em uma operação de dimensionamento subsequente.
-	Se você habilitou o clustering quando criou o cache **Premium**, será possível [alterar o tamanho do cluster](cache-how-to-premium-clustering.md#cluster-size). Neste momento, você não pode habilitar clustering em um cache existente que foi criado sem cluster.

    Para obter mais informações, confira [Como configurar o clustering do Redis para um Cache Redis do Azure Premium](cache-how-to-premium-clustering.md).

### Depois do dimensionamento, é necessário alterar minhas chaves de acesso ou o nome do cache?

Não, o nome do cache e as chaves permanecem inalterados durante uma operação de dimensionamento.

### Como funciona o dimensionamento?

-	Quando um cache **Básico** é escalonado para um tamanho diferente, ele é desligado e um novo cache é provisionado usando o novo tamanho. Durante esse tempo, o cache não está disponível e todos os dados em cache são perdidos.
-	Quando um cache **Básico** é escalonado para um cache **Standard**, um cache de réplica é provisionado e os dados são copiados do cache primário no cache de réplica. O cache permanece disponível durante o processo de dimensionamento.
-	Quando um cache **Standard** é dimensionado para um tamanho diferente ou para um cache **Premium**, uma das réplicas é desligada e provisionada novamente para o novo tamanho, os dados são transferidos e a outra réplica executa um failover antes de ser provisionada novamente, de forma semelhante ao processo que ocorre durante uma falha em um dos nós de cache.

### Perderei dados de meu cache durante o dimensionamento?

-	Quando um cache **Básico** é escalonado para um novo tamanho, todos os dados são perdidos, e o cache fica indisponível durante a operação de colocação em escala.
-	Quando um cache **Básico** é dimensionado para um cache **Padrão**, os dados no cache geralmente são preservados.
-	Quando um cache **Standard** é dimensionado para uma camada ou tamanho maior, ou quando um cache **Premium** é dimensionado para um tamanho maior, todos os dados normalmente são preservados. Ao se dimensionar um cache **Standard** ou **Premium** para um tamanho menor, dados podem ser perdidos, dependendo da quantidade de dados estão no cache em relação ao novo tamanho quando ele for dimensionado. Se dados forem perdidos ao se reduzir, as chaves serão removidas usando a política de remoção [allkeys-lru](http://redis.io/topics/lru-cache). 

### A configuração dos meus bancos de dados personalizados foi afetada durante o dimensionamento?

Alguns tipos de preço têm diferentes [limites de bancos de dados](cache-configure.md#databases), portanto, há algumas considerações a fazer ao reduzir verticalmente um valor personalizado para a configuração `databases` durante a criação do cache.

-	Ao escalar para um tipo de preço com menos `databases` limite do que a camada atual:
	-	Se você estiver usando o número padrão de `databases`, que é 16 para todos os tipos de preço, nenhum dado será perdido.
	-	Se você estiver usando um número personalizado de `databases`, que se encaixa dentro dos limites do tipo para o qual você está dimensionando, essa configuração `databases` será mantida e nenhum dado será perdido.
	-	Se você estiver usando um número personalizado de `databases`, que excede os limites do novo tipo, a configuração `databases` será reduzida para os limites do novo tipo e todos os dados nos bancos de dados removidos são perdidos.
-	Ao escalonar para um tipo de preço com o mesmo limite ou limite superior `databases` do que o tipo atual, sua configuração `databases` é mantida e nenhum dado é perdido.

Observe que, embora os caches Standard e Premium tenham um SLA de 99,9% de disponibilidade, não há SLA para perda de dados.

### O cache estará disponível durante o dimensionamento?

-	Os caches **Standard** e **Premium** permanecem disponíveis durante a operação de dimensionamento.
-	Os caches **Básicos** ficam offline durante as operações de dimensionamento para um tamanho diferente, mas permanecem disponíveis durante o dimensionamento do **Básico** para **Standard**.

### Operações que não têm suporte

-	Você não pode dimensionar de uma camada de preços mais alta para uma camada de preços mais baixa.
    -    Você não pode dimensionar de um cache **Premium** para um cache **Standard** ou **Básico**.
    -    Você não pode dimensionar de um cache **Standard** para um cache **Básico**.
-	É possível dimensionar de um cache **Básico** para um cache **Standard**, mas não é possível alterar o tamanho simultaneamente. Se precisar de um tamanho diferente, você pode fazer uma operação de dimensionamento subsequente para o tamanho desejado.
-	Você não pode dimensionar de um cache **Básico** diretamente para um cache **Premium**. Você deve dimensionar do **Básico** para o **Standard** em uma única operação de dimensionamento e do **Standard** para o **Premium** em uma operação de dimensionamento subsequente.
-	Não é possível dimensionar de um tamanho maior para o tamanho **C0 (250 MB)**.

Se uma operação de dimensionamento falhar, o serviço tentará reverter a operação, e o cache será revertido para o tamanho original.

### Quanto tempo o dimensionamento leva?

O dimensionamento leva aproximadamente 20 minutos, dependendo da quantidade de dados no cache.

### Como saber quando o dimensionamento é concluído?

No Portal do Azure, você pode ver a operação de dimensionamento em andamento. Quando o dimensionamento for concluído, o status do cache será alterado para **Executando**.

### Por que esse recurso está na visualização?

Estamos lançando esse recurso para obter comentários. Com base nos comentários, lançaremos esse recurso para Disponibilidade Geral em breve.





  
<!-- IMAGES -->
[redis-cache-pricing-tier-part]: ./media/cache-how-to-scale/redis-cache-pricing-tier-part.png

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png

<!---HONumber=AcomDC_0622_2016-->