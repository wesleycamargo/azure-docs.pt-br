<properties
	pageTitle="Gerenciar o Cache Redis do Azure com o Azure PowerShell | Microsoft Azure"
	description="Saiba como executar tarefas administrativas para o Cache Redis do Azure usando o PowerShell do Azure."
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
	ms.date="02/01/2016" 
	ms.author="sdanie"/>

# Gerenciar o Cache Redis do Azure com o PowerShell do Azure

> [AZURE.SELECTOR]
- [PowerShell](cache-howto-manage-redis-cache-powershell.md)
- [Azure CLI](cache-manage-cli.md)

Este tópico mostra como executar tarefas comuns, como criar, atualizar e dimensionar suas instâncias de Cache Redis do Azure, como regenerar chaves de acesso e exibir informações sobre seus caches. Para obter uma lista completa de cmdlets do PowerShell do Cache Redis do Azure, confira [Cmdlets do Cache Redis do Azure](https://msdn.microsoft.com/library/azure/mt634513.aspx).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](#classic) descrito posteriormente neste artigo.

## Pré-requisitos

Se você já instalou o Azure PowerShell, você deve ter o Azure PowerShell versão 1.0.0 ou posterior. Você pode verificar a versão do Azure PowerShell instalada com o comando este comando no prompt de comando do Azure PowerShell.

	Get-Module azure | format-table version

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

Primeiro, você deve fazer logon no Azure com este comando.

	Login-AzureRmAccount

Especifique o endereço de email de sua conta do Azure e sua senha na caixa de diálogo de logon do Microsoft Azure.

Em seguida, se tiver várias assinaturas do Azure, você precisará definir sua assinatura do Azure. Para ver uma lista de suas assinaturas atuais, execute este comando.

	Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Para especificar a assinatura, execute o comando a seguir. No exemplo a seguir, o nome da assinatura é `ContosoSubscription`.

	Select-AzureRmSubscription -SubscriptionName ContosoSubscription

Para poder usar o Windows PowerShell com o Gerenciador de Recursos do Azure, você precisa o seguinte:

- Windows PowerShell, versão 3.0 ou 4.0. Para encontrar a versão do Windows PowerShell, digite:`$PSVersionTable` e verifique se o valor de `PSVersion` é 3.0 ou 4.0. Para instalar uma versão compatível, confira [Windows Management Framework 3.0 ](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Para obter ajuda detalhada sobre qualquer cmdlet que você vir neste tutorial, use o cmdlet Get-Help.

	Get-Help <cmdlet-name> -Detailed

Por exemplo, para obter ajuda para o cmdlet `New-AzureRmRedisCache`, digite:

	Get-Help New-AzureRmRedisCache -Detailed

## Como se conectar à Nuvem do Azure Government ou à Nuvem do Azure na China

Por padrão, o ambiente do Azure é `AzureCloud`, que representa a instância de nuvem global do Azure. Para se conectar a uma instância diferente, use o comando `Add-AzureRmAccount` com a opção de linha de comando `-Environment` ou -`EnvironmentName` com o ambiente desejado ou o nome do ambiente.

Para ver a lista de ambientes disponíveis, execute o cmdlet `Get-AzureRmEnvironment`.

### Para se conectar à Nuvem do Azure Government

Para se conectar à Nuvem do Azure Government, use um dos comandos a seguir.

	Add-AzureRMAccount -EnvironmentName AzureUSGovernment

ou o

	Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureUSGovernment)

Para criar um cache na Nuvem do Azure Government, use um dos locais a seguir.

-	Gov. dos EUA – Virgínia
-	Gov. dos EUA – Iowa

Para saber mais sobre a Nuvem do Azure Government, confira [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) e [Guia do Desenvolvedor do Microsoft Azure Government](azure-government-developer-guide.md).

### Para se conectar à Nuvem do Azure na China

Para se conectar à Nuvem do Azure na China, use um dos comandos a seguir.

	Add-AzureRMAccount -EnvironmentName AzureChinaCloud

ou o

	Add-AzureRmAccount -Environment (Get-AzureRmEnvironment -Name AzureChinaCloud)

Para criar um cache na Nuvem do Azure na China, use um dos locais a seguir.

-	Leste da China
-	Norte da China

Para obter mais informações sobre a Nuvem do Azure na China, confira [AzureChinaCloud para Azure operado pelo 21Vianet na China](http://www.windowsazure.cn/).

## Propriedades usadas para o PowerShell do Cache Redis do Azure

A tabela a seguir contém as propriedades e as descrições dos parâmetros usados ao criar e gerenciar suas instâncias do Cache Redis do Azure usando o Azure PowerShell.

| Parâmetro | Descrição | Padrão |
|--------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|
| Nome | Nome do cache | |
| Local | Local do cache | |
| ResourceGroupName | Nome do grupo de recursos no qual criar o cache | |
| Tamanho | O tamanho do cache. Os valores válidos são: P1, P2, P3, P4, C0, C1, C2, C3, C4, C5, C6, 250 MB, 1 GB, 2.5 GB, 6 GB, 13 GB, 26 GB, 53 GB | 1 GB |
| ShardCount | O número de fragmentos para criar durante a criação de um cache premium com o cluster ativado. Os valores válidos são: 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 | |
| SKU | Especifica o SKU do cache. Os valores válidos são: Básico, Standard e Premium | Standard |
| RedisConfiguration | Especifica as configurações do Redis para maxmemory-delta, maxmemory-policy e notify-keyspace-events. Observe que os eventos maxmemory-delta e notify-keyspace-events estão disponíveis para os caches Standard e Premium apenas. | |
| EnableNonSslPort | Indica se a porta não SSL está habilitada. | Falso |
| MaxMemoryPolicy | Esse parâmetro foi desaprovado - use RedisConfiguration em vez disso. | |
| StaticIP | Ao hospedar o cache em uma VNET, especifica um endereço IP exclusivo na sub-rede do cache. Se ele não for fornecido, um será escolhido para você na sub-rede. | |
| Sub-rede | Ao hospedar o cache em uma VNET, especifica o nome da sub-rede na qual implantar o cache. | |
| VirtualNetwork | Ao hospedar o cache em uma VNET, especifica a ID do recurso da VNET na qual implantar o cache. | |
| KeyType | Especifica qual chave de acesso regenerar durante a renovação das chaves de acesso. Os valores válidos são: Primary, Secondary | | | |


## Para criar uma Cache Redis

As novas instâncias Cache Redis do Azure são criadas usando o cmdlet [New-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634517.aspx).

>[AZURE.IMPORTANT] Na primeira vez em que você cria um cache Redis em uma assinatura usando o portal do Azure, o portal registra o namespace `Microsoft.Cache` para a assinatura. Se você tentar criar o cache Redis primeiro em uma assinatura usando o PowerShell, deverá primeiro registrar esse namespace usando o comando a seguir; caso contrário, cmdlets como `New-AzureRmRedisCache` e `Get-AzureRmRedisCache` irão falhar.
>
>`Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Cache"`

Para ver uma lista dos parâmetros disponíveis e suas descrições para `New-AzureRmRedisCache`, execute o seguinte comando.

	PS C:\> Get-Help New-AzureRmRedisCache -detailed
	
	NAME
	    New-AzureRmRedisCache
	
	SYNOPSIS
	    Creates a new redis cache.
	
	SYNTAX
	    New-AzureRmRedisCache -Name <String> -ResourceGroupName <String> -Location <String> [-RedisVersion <String>]
	    [-Size <String>] [-Sku <String>] [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort
	    <Boolean>] [-ShardCount <Integer>] [-VirtualNetwork <String>] [-Subnet <String>] [-StaticIP <String>]
	    [<CommonParameters>]
	
	DESCRIPTION
	    The New-AzureRmRedisCache cmdlet creates a new redis cache.
	
	PARAMETERS
	    -Name <String>
	        Name of the redis cache to create.
	
	    -ResourceGroupName <String>
	        Name of resource group in which to create the redis cache.
	
	    -Location <String>
	        Location in which to create the redis cache.
	
	    -RedisVersion <String>
	        RedisVersion is deprecated and will be removed in future release.
	
	    -Size <String>
	        Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
	        C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.
	
	    -Sku <String>
	        Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.
	
	    -MaxMemoryPolicy <String>
	        The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
	        MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}
	
	    -RedisConfiguration <Hashtable>
	        All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
	        rdb-backup-frequency, maxmemory-delta, maxmemory-policy, notify-keyspace-events, maxmemory-samples,
	        slowlog-log-slower-than, slowlog-max-len, list-max-ziplist-entries, list-max-ziplist-value,
	        hash-max-ziplist-entries, hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries,
	        zset-max-ziplist-value etc.
	
	    -EnableNonSslPort <Boolean>
	        EnableNonSslPort is used by Azure Redis Cache. If no value is provided, the default value is false and the
	        non-SSL port will be disabled. Possible values are true and false.

	    -ShardCount <Integer>
	        The number of shards to create on a Premium Cluster Cache.
	
	    -VirtualNetwork <String>
	        The exact ARM resource ID of the virtual network to deploy the redis cache in. Example format:
	        /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
	
	    -Subnet <String>
	        Required when deploying a redis cache inside an existing Azure Virtual Network.
	
	    -StaticIP <String>
	        Required when deploying a redis cache inside an existing Azure Virtual Network.
	
	    <CommonParameters>
	        This cmdlet supports the common parameters: Verbose, Debug,
	        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
	        OutBuffer, PipelineVariable, and OutVariable. For more information, see
	        about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Para criar um cache com parâmetros padrão, execute o comando a seguir.

	New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US"

`ResourceGroupName`, `Name` e `Location` são parâmetros obrigatórios, mas os restantes são opcionais e têm valores padrão. Executar o comando anterior cria uma instância Cache Redis do Azure do SKU Standard com o nome especificado, local e grupo de recursos, que é tem 1 GB de tamanho com a porta não SSL desativada.

Para criar um cache premium, especifique um tamanho de P1 (6 GB - 60 GB), P2 (13 GB - 130 GB), P3 (26 GB - 260 GB) ou P4 (53 GB - 530 GB). Para habilitar o clustering, especifique uma contagem de fragmentos usando o parâmetro `ShardCount`. O exemplo a seguir cria um cache premium P1 com 3 fragmentos. Um cache premium P1 tem 6 GB de tamanho e como especificamos três fragmentos, o tamanho total é de 18 GB (3 x 6 GB).

	New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -Sku Premium -Size P1 -ShardCount 3

Para especificar valores para o parâmetro `RedisConfiuration`, coloque os valores dentro de `{}` como pares de chave/valor como `@{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}`. O exemplo a seguir cria um cache padrão de 1 GB com as `allkeys-random` notificações maxmemory-policy e keyspace configuradas com `KEA`. Para obter mais informações, consulte [Notificações de Keyspace (configurações avançadas)](cache-configure.md#keyspace-notifications-advanced-settings) e [Maxmemory-policy e maxmemory-reserved](cache-configure.md#maxmemory-policy-and-maxmemory-reserved).

	New-AzureRmRedisCache -ResourceGroupName myGroup -Name mycache -Location "North Central US" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random", "notify-keyspace-events" = "KEA"}

## Para atualizar um cache Redis

As instâncias Cache Redis do Azure são atualizadas usando o cmdlet [Set-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634518.aspx).

Para ver uma lista dos parâmetros disponíveis e suas descrições para `Set-AzureRmRedisCache`, execute o seguinte comando.

	PS C:\> Get-Help Set-AzureRmRedisCache -detailed
	
	NAME
	    Set-AzureRmRedisCache
	
	SYNOPSIS
	    Set redis cache updatable parameters.
	
	SYNTAX
	    Set-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Size <String>] [-Sku <String>]
	    [-MaxMemoryPolicy <String>] [-RedisConfiguration <Hashtable>] [-EnableNonSslPort <Boolean>] [-ShardCount
	    <Integer>] [<CommonParameters>]
	
	DESCRIPTION
	    The Set-AzureRmRedisCache cmdlet sets redis cache parameters.
	
	PARAMETERS
	    -Name <String>
	        Name of the redis cache to update.
	
	    -ResourceGroupName <String>
	        Name of the resource group for the cache.
	
	    -Size <String>
	        Size of the redis cache. The default value is 1GB or C1. Possible values are P1, P2, P3, P4, C0, C1, C2, C3,
	        C4, C5, C6, 250MB, 1GB, 2.5GB, 6GB, 13GB, 26GB, 53GB.
	
	    -Sku <String>
	        Sku of redis cache. The default value is Standard. Possible values are Basic, Standard and Premium.
	
	    -MaxMemoryPolicy <String>
	        The 'MaxMemoryPolicy' setting has been deprecated. Please use 'RedisConfiguration' setting to set
	        MaxMemoryPolicy. e.g. -RedisConfiguration @{"maxmemory-policy" = "allkeys-lru"}
	
	    -RedisConfiguration <Hashtable>
	        All Redis Configuration Settings. Few possible keys: rdb-backup-enabled, rdb-storage-connection-string,
	        rdb-backup-frequency, maxmemory-delta, maxmemory-policy, notify-keyspace-events, maxmemory-samples,
	        slowlog-log-slower-than, slowlog-max-len, list-max-ziplist-entries, list-max-ziplist-value,
	        hash-max-ziplist-entries, hash-max-ziplist-value, set-max-intset-entries, zset-max-ziplist-entries,
	        zset-max-ziplist-value etc.
	
	    -EnableNonSslPort <Boolean>
	        EnableNonSslPort is used by Azure Redis Cache. The default value is null and no change will be made to the
	        currently configured value. Possible values are true and false.
	
	    -ShardCount <Integer>
	        The number of shards to create on a Premium Cluster Cache.
	
	    <CommonParameters>
	        This cmdlet supports the common parameters: Verbose, Debug,
	        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
	        OutBuffer, PipelineVariable, and OutVariable. For more information, see
	        about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

O `Set-AzureRmRedisCache` pode ser usado para atualizar propriedades como `Size`, `Sku`, `EnableNonSslPort` e os valores `RedisConfiguration`.

O comando a seguir atualiza a maxmemory-policy do Cache Redis chamada myCache.

	Set-AzureRmRedisCache -ResourceGroupName "myGroup" -Name "myCache" -RedisConfiguration @{"maxmemory-policy" = "allkeys-random"}

<a name="scale"></a>
## Para dimensionar um cache Redis

`Set-AzureRmRedisCache` pode ser usado para dimensionar uma instância do cache Redis do Azure quando as propriedades `Size`, `Sku` ou `ShardCount` são modificadas.

>[AZURE.NOTE]Dimensionar um cache usando o PowerShell está sujeito aos mesmos limites e diretrizes de dimensionar um cache no Portal do Azure. Você pode dimensionar para uma camada de preços diferente com as restrições a seguir.
>
>-	Não é possível dimensionar para ou a partir de um cache **Premium**.
>-	Não é possível dimensionar de um cache **Standard** para um cache **Básico**.
>-	Você pode dimensionar de um cache **Básico** para um cache **Padrão**, mas não pode alterar o tamanho ao mesmo tempo. Se precisar de um tamanho diferente, você pode fazer uma operação de dimensionamento subsequente para o tamanho desejado.
>-	Você não pode escalonar de um tamanho maior para o tamanho **C0 (250 MB)**.
>
>Para saber mais, confira [Como dimensionar o Cache Redis do Azure](cache-how-to-scale.md).

O exemplo a seguir mostra como dimensionar um cache denominado `myCache` para um cache de 2,5 GB. Observe que esse comando funcionará para um cache Básico ou Standard.

	Set-AzureRmRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Depois do comando ser emitido, o status do cache é retornado (semelhante a chamar `Get-AzureRmRedisCache`). Observe que `ProvisioningState` é `Scaling`.

	PS C:\> Set-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup -Size 2.5GB
	
	
	Name               : mycache
	Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/mygroup/providers/Mi
	                     crosoft.Cache/Redis/mycache
	Location           : South Central US
	Type               : Microsoft.Cache/Redis
	HostName           : mycache.redis.cache.windows.net
	Port               : 6379
	ProvisioningState  : Scaling
	SslPort            : 6380
	RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 150], [notify-keyspace-events, KEA],
	                     [maxmemory-delta, 150]...}
	EnableNonSslPort   : False
	RedisVersion       : 3.0
	Size               : 1GB
	Sku                : Standard
	ResourceGroupName  : mygroup
	PrimaryKey         : ....
	SecondaryKey       : ....
	VirtualNetwork     :
	Subnet             :
	StaticIP           :
	TenantSettings     : {}
	ShardCount         :

Quando a operação de dimensionamento for concluída, `ProvisioningState` será alterado para `Succeeded`. Se você precisar fazer uma operação de dimensionamento subsequente, como alterar de Básico para Standard, em seguida, alterar o tamanho, deverá aguardar até que a operação anterior seja concluída ou receberá um erro semelhante ao seguinte.

	Set-AzureRmRedisCache : Conflict: The resource '...' is not in a stable state, and is currently unable to accept the update request.

## Para obter informações sobre um cache Redis

Você pode recuperar informações sobre um cache usando o cmdlet [Get-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634514.aspx).

Para ver uma lista dos parâmetros disponíveis e suas descrições para `Get-AzureRmRedisCache`, execute o comando a seguir.

	PS C:\> Get-Help Get-AzureRmRedisCache -detailed
	
	NAME
	    Get-AzureRmRedisCache
	
	SYNOPSIS
	    Gets details about a single cache or all caches in the specified resource group or all caches in the current
	    subscription.
	
	SYNTAX
	    Get-AzureRmRedisCache [-Name <String>] [-ResourceGroupName <String>] [<CommonParameters>]
	
	DESCRIPTION
	    The Get-AzureRmRedisCache cmdlet gets the details about a cache or caches depending on input parameters. If both
	    ResourceGroupName and Name parameters are provided then Get-AzureRmRedisCache will return details about the
	    specific cache name provided.
	
	    If only ResourceGroupName is provided than it will return details about all caches in the specified resource group.
	
	    If no parameters are given than it will return details about all caches the current subscription.
	
	PARAMETERS
	    -Name <String>
	        The name of the cache. When this parameter is provided along with ResourceGroupName, Get-AzureRmRedisCache
	        returns the details for the cache.
	
	    -ResourceGroupName <String>
	        The name of the resource group that contains the cache or caches. If ResourceGroupName is provided with Name
	        then Get-AzureRmRedisCache returns the details of the cache specified by Name. If only the ResourceGroup
	        parameter is provided, then details for all caches in the resource group are returned.
	
	    <CommonParameters>
	        This cmdlet supports the common parameters: Verbose, Debug,
	        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
	        OutBuffer, PipelineVariable, and OutVariable. For more information, see
	        about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Para retornar informações sobre todos os caches na assinatura atual, execute `Get-AzureRmRedisCache` sem parâmetros.

	Get-AzureRmRedisCache

Para retornar informações sobre todos os caches em um grupo de recursos específico, execute `Get-AzureRmRedisCache` com o parâmetro `ResourceGroupName`.

	Get-AzureRmRedisCache -ResourceGroupName myGroup

Para retornar informações sobre um cache específico, execute `Get-AzureRmRedisCache` com o parâmetro `Name` que contém o nome do cache e o parâmetro `ResourceGroupName` com o grupo de recursos que contém esse cache.

	PS C:\> Get-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup
	
	Name               : mycache
	Id                 : /subscriptions/12ad12bd-abdc-2231-a2ed-a2b8b246bbad4/resourceGroups/myGroup/providers/Mi
	                     crosoft.Cache/Redis/mycache
	Location           : South Central US
	Type               : Microsoft.Cache/Redis
	HostName           : mycache.redis.cache.windows.net
	Port               : 6379
	ProvisioningState  : Succeeded
	SslPort            : 6380
	RedisConfiguration : {[maxmemory-policy, volatile-lru], [maxmemory-reserved, 62], [notify-keyspace-events, KEA],
	                     [maxclients, 1000]...}
	EnableNonSslPort   : False
	RedisVersion       : 3.0
	Size               : 1GB
	Sku                : Standard
	ResourceGroupName  : myGroup
	VirtualNetwork     :
	Subnet             :
	StaticIP           :
	TenantSettings     : {}
	ShardCount         :

## Para recuperar as chaves de acesso para um cache Redis

Para recuperar as chaves de acesso para seu cache, você pode usar o cmdlet [Get-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634516.aspx).

Para ver uma lista dos parâmetros disponíveis e suas descrições para `Get-AzureRmRedisCacheKey`, execute o comando a seguir.

	PS C:\> Get-Help Get-AzureRmRedisCacheKey -detailed
	
	NAME
	    Get-AzureRmRedisCacheKey
	
	SYNOPSIS
	    Gets the accesskeys for the specified redis cache.
	
	
	SYNTAX
	    Get-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> [<CommonParameters>]
	
	DESCRIPTION
	    The Get-AzureRmRedisCacheKey cmdlet gets the access keys for the specified cache.
	
	PARAMETERS
	    -Name <String>
	        Name of the redis cache.
	
	    -ResourceGroupName <String>
	        Name of the resource group for the cache.
	
	    <CommonParameters>
	        This cmdlet supports the common parameters: Verbose, Debug,
	        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
	        OutBuffer, PipelineVariable, and OutVariable. For more information, see
	        about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

Para recuperar as chaves para seu cache, chame o cmdlet `Get-AzureRmRedisCacheKey` e passe no nome de seu cache o nome do grupo de recursos que contém o cache.

	PS C:\> Get-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup
	
	PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
	SecondaryKey : ABhfB757JgjIgt785JgKH9865eifmekfnn649303JKL=

## Para regenerar chaves de acesso para seu cache Redis

Para recuperar as chaves de acesso para seu cache, você pode usar o cmdlet [New-AzureRmRedisCacheKey](https://msdn.microsoft.com/library/azure/mt634512.aspx).

Para ver uma lista dos parâmetros disponíveis e suas descrições para `New-AzureRmRedisCacheKey`, execute o comando a seguir.

	PS C:\> Get-Help New-AzureRmRedisCacheKey -detailed
	
	NAME
	    New-AzureRmRedisCacheKey
	
	SYNOPSIS
	    Regenerates the access key of a redis cache.
	
	SYNTAX
	    New-AzureRmRedisCacheKey -Name <String> -ResourceGroupName <String> -KeyType <String> [-Force] [<CommonParameters>]
	
	DESCRIPTION
	    The New-AzureRmRedisCacheKey cmdlet regenerate the access key of a redis cache.
	
	PARAMETERS
	    -Name <String>
	        Name of the redis cache.
	
	    -ResourceGroupName <String>
	        Name of the resource group for the cache.
	
	    -KeyType <String>
	        Specifies whether to regenerate the primary or secondary access key. Possible values are Primary or Secondary.
	
	    -Force
	        When the Force parameter is provided, the specified access key is regenerated without any confirmation prompts.
	
	    <CommonParameters>
	        This cmdlet supports the common parameters: Verbose, Debug,
	        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
	        OutBuffer, PipelineVariable, and OutVariable. For more information, see
	        about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).
	
Para regenerar a chave primária ou secundária para seu cache, chame o cmdlet `New-AzureRmRedisCacheKey`, passe no nome o grupo de recursos e especifique `Primary` ou `Secondary` para o parâmetro `KeyType`. No exemplo a seguir, a chave de acesso secundária para um cache é regenerada.

	PS C:\> New-AzureRmRedisCacheKey -Name myCache -ResourceGroupName myGroup -KeyType Secondary
	
	Confirm
	Are you sure you want to regenerate Secondary key for redis cache 'myCache'?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
	
	
	PrimaryKey   : b2wdt43sfetlju4hfbryfnregrd9wgIcc6IA3zAO1lY=
	SecondaryKey : c53hj3kh4jhHjPJk8l0jji785JgKH9865eifmekfnn6=

## Para excluir um cache Redis

Para excluir um cache Redis, use o cmdlet [Remove-AzureRmRedisCache](https://msdn.microsoft.com/library/azure/mt634515.aspx).

Para ver uma lista dos parâmetros disponíveis e suas descrições para `Remove-AzureRmRedisCache`, execute o comando a seguir.

	PS C:\> Get-Help Remove-AzureRmRedisCache -detailed
	
	NAME
	    Remove-AzureRmRedisCache
	
	SYNOPSIS
	    Remove redis cache if exists.
	
	SYNTAX
	    Remove-AzureRmRedisCache -Name <String> -ResourceGroupName <String> [-Force] [-PassThru] [<CommonParameters>
	
	DESCRIPTION
	    The Remove-AzureRmRedisCache cmdlet removes a redis cache if it exists.
	
	PARAMETERS
	    -Name <String>
	        Name of the redis cache to remove.
	
	    -ResourceGroupName <String>
	        Name of the resource group of the cache to remove.
	
	    -Force
	        When the Force parameter is provided, the cache is removed without any confirmation prompts.
	
	    -PassThru
	        By default Remove-AzureRmRedisCache removes the cache and does not return any value. If the PassThru par
	        is provided then Remove-AzureRmRedisCache returns a boolean value indicating the success of the operatio
	
	    <CommonParameters>
	        This cmdlet supports the common parameters: Verbose, Debug,
	        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
	        OutBuffer, PipelineVariable, and OutVariable. For more information, see
	        about_CommonParameters (http://go.microsoft.com/fwlink/?LinkID=113216).

No exemplo a seguir, o cache denominado `myCache` é removido.

	PS C:\> Remove-AzureRmRedisCache -Name myCache -ResourceGroupName myGroup
	
	Confirm
	Are you sure you want to remove redis cache 'myCache'?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

<a name="classic"></a>
## Gerenciar instâncias Cache Redis do Azure com o modelo de implantação clássico do PowerShell

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](cache-howto-manage-redis-cache-powershell.md) descrito no início deste artigo.

O script a seguir demonstra como criar, atualizar e excluir um Cache Redis do Azure usando o modelo de implantação clássico.
		
		$VerbosePreference = "Continue"

    	# Create a new cache with date string to make name unique.
		$cacheName = "MovieCache" + $(Get-Date -Format ('ddhhmm'))
		$location = "West US"
		$resourceGroupName = "Default-Web-WestUS"
		
		$movieCache = New-AzureRedisCache -Location $location -Name $cacheName  -ResourceGroupName $resourceGroupName -Size 250MB -Sku Basic
		
		# Wait until the Cache service is provisioned.
		
		for ($i = 0; $i -le 60; $i++)
		{
		    Start-Sleep -s 30
		    $cacheGet = Get-AzureRedisCache -ResourceGroupName $resourceGroupName -Name $cacheName
		    if ([string]::Compare("succeeded", $cacheGet[0].ProvisioningState, $True) -eq 0)
		    {
		        break
		    }
		    If($i -eq 60)
		    {
		        exit
		    }
		}
		
		# Update the access keys.
		
		Write-Verbose "PrimaryKey: $($movieCache.PrimaryKey)"
		New-AzureRedisCacheKey -KeyType "Primary" -Name $cacheName  -ResourceGroupName $resourceGroupName -Force
		$cacheKeys = Get-AzureRedisCacheKey -ResourceGroupName $resourceGroupName  -Name $cacheName
		Write-Verbose "PrimaryKey: $($cacheKeys.PrimaryKey)"
		
		# Use Set-AzureRedisCache to set Redis cache updatable parameters.
		# Set the memory policy to Least Recently Used.
		
		Set-AzureRedisCache -Name $cacheName -ResourceGroupName $resourceGroupName -RedisConfiguration @{"maxmemory-policy" = "AllKeys-LRU"}
		
		# Delete the cache.
		
		Remove-AzureRedisCache -Name $movieCache.Name -ResourceGroupName $movieCache.ResourceGroupName  -Force

## Próximas etapas

Para saber mais sobre como usar o Windows PowerShell com o Azure, consulte os seguintes recursos:

- [Documentação de cmdlet do Cache Redis do Azure no MSDN](https://msdn.microsoft.com/library/azure/mt634513.aspx)
- [Cmdlets do Gerenciador de Recursos do Azure (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkID=394765): saiba usar os cmdlets no módulo AzureResourceManager.
- [Usando Grupos de recursos para gerenciar seus recursos do Azure](../azure-portal/resource-group-portal.md): saiba como criar e gerenciar grupos de recursos no Portal do Azure.
- [Blog do Azure](http://blogs.msdn.com/windowsazure): obtenha informações sobre os novos recursos no Azure.
- [Blog do Windows PowerShell](http://blogs.msdn.com/powershell): obtenha informações sobre os novos recursos do Windows PowerShell.
- [Blog "Hey, Scripting Guy!" Blog](http://blogs.technet.com/b/heyscriptingguy/): obtenha dicas reais e truques da comunidade.do Windows PowerShell.

<!---HONumber=AcomDC_0204_2016-->