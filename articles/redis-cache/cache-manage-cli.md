<properties 
	pageTitle="Como criar e gerenciar o Cache Redis do Azure usando a Interface de Linha de Comando do Azure (CLI do Azure)" 
	description="Saiba como instalar a CLI do Azure em qualquer plataforma, como usá-la para se conectar à sua conta do Azure e como criar e gerenciar um cache Redis da CLI do Azure." 
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
	ms.date="09/16/2015" 
	ms.author="sdanie"/>

# Como criar e gerenciar o Cache Redis do Azure usando a Interface de Linha de Comando do Azure (CLI do Azure)

A CLI do Azure é uma ótima maneira de gerenciar sua infraestrutura do Azure de qualquer plataforma. Este artigo mostra como criar e gerenciar suas instâncias do Cache Redis do Azure usando a CLI do Azure.

## Pré-requisitos

Para criar e gerenciar as instâncias do Cache Redis do Azure usando a CLI do Azure, você deverá concluir as etapas a seguir.

-	Você deve ter uma conta do Azure. Se você não tiver, poderá criar uma [conta de avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.
-	[Instale a CLI do Azure](../xplat-cli.md#install).
-	Conecte sua instalação da CLI do Azure a uma conta pessoal, empresarial ou escolar do Azure e faça logon na CLI do Azure usando o comando `azure login`. Para compreender as diferenças e escolher, consulte [Como se conectar à sua assinatura do Azure](../xplat-cli.md#configure).
-	Antes de executar qualquer um dos comandos a seguir, alterne a CLI do Azure para o modo Gerenciador de Recursos executando o comando a seguir. `azure config mode arm` Para saber mais, confira [Configurando o modo do Gerenciador de Recursos do Azure](../virtual-machines/xplat-cli-azure-resource-manager.md#setting-the-azure-resource-manager-mode).

## Propriedades do Cache Redis

As propriedades a seguir são usadas durante a criação e a atualização de instâncias do Cache Redis.

| Propriedade | Switch | Descrição |
|------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| name | -n, --name | Nome do Cache Redis. |
| grupo de recursos | -g, --resource-group | Nome do Grupo de Recursos. |
| location | -l, --location | Local para criar o cache. |
| tamanho | -z, --size | Tamanho do Cache Redis. Valores válidos: [C0, C1, C2, C3, C4, C5, C6] |
| sku | -x, --sku | SKU Redis. Deve ser um destes: [Basic, Standard] |
| MaxMemoryPolicy | -m, --max-memory-policy | Propriedade MaxMemoryPolicy do Cache Redis. Valores válidos: [AllKeysLRU, AllKeysRandom, NoEviction, VolatileLRU, VolatileRandom, VolatileTTL] |
| EnableNonSslPort | -e, --enable-non-ssl-port | Propriedade EnableNonSslPort do Cache Redis. Adicione este sinalizador se quiser habilitar a Porta Não SSL para o cache |
| assinatura | -s, --subscription | O identificador da assinatura. |
| tipo de chave | -t, --key-type | Tipo de chave a ser renovada. Valores válidos: [Primary, Secondary] |

## Ver todos os comandos do Cache Redis

Para ver todos os comandos do Cache Redis e seus parâmetros, use o comando `azure rediscache -h`.

	C:\>azure rediscache -h
	help:    Commands to manage your Azure Redis Cache(s)
	help:
	help:    Create a Redis Cache
	help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
	help:
	help:    Delete an existing Redis Cache
	help:      rediscache delete [--name <name> --resource-group <resource-group> ]
	help:
	help:    List all Redis Caches within your Subscription or Resource Group
	help:      rediscache list [options]
	help:
	help:    Show properties of an existing Redis Cache
	help:      rediscache show [--name <name> --resource-group <resource-group>]
	help:
	help:    Change settings of an existing Redis Cache
	help:      rediscache set [--name <name> --resource-group <resource-group> --max-memory-policy <max-memory-policy>]
	help:
	help:    Renew the authentication key for an existing Redis Cache
	help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
	help:
	help:    Lists Primary and Secondary key of an existing Redis Cache
	help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
	help:
	help:    Options:
	help:      -h, --help  output usage information
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Criar um Cache Redis

Para criar um Cache Redis, use o seguinte comando:

	azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]

Para saber mais sobre esse comando, execute o comando `azure rediscache create -h`.

	C:\>azure rediscache create -h
	help:    Create a Redis Cache
	help:
	help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
	help:
	help:    Options:
	help:      -h, --help                                   output usage information
	help:      -v, --verbose                                use verbose output
	help:      -vv                                          more verbose with debug output
	help:      --json                                       use json output
	help:      -n, --name <name>                            Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>        Name of the Resource Group
	help:      -l, --location <location>                    Location to create cache.
	help:      -z, --size <size>                            Size of the Redis Cache. Valid values: [C0, C1, C2, C3, C4, C5, C6]
	help:      -x, --sku <sku>                              Redis SKU. Should be one of : [Basic, Standard]
	help:      -m, --max-memory-policy <max-memory-policy>  MaxMemoryPolicy property of the Redis Cache. Valid values: [AllKeysLRU, AllKeysRandom, NoEviction, VolatileLRU, VolatileRandom, VolatileTTL]
	help:      -e, --enable-non-ssl-port                    EnableNonSslPort property of the Redis Cache. Add this flag if you want to enable the Non SSL Port for your cache
	help:      -s, --subscription <id>                      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Excluir um Cache Redis existente

Para excluir um Cache Redis, use o seguinte comando:

	azure rediscache delete [--name <name> --resource-group <resource-group> ]

Para saber mais sobre esse comando, execute o comando `azure rediscache delete -h`.

	C:\>azure rediscache delete -h
	help:    Delete an existing Redis Cache
	help:
	help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -n, --name <name>                      Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Listar todos os Caches Redis em sua Assinatura ou Grupo de Recursos

Para listar todos os Caches Redis em sua Assinatura ou Grupo de Recursos, use o seguinte comando:

	azure rediscache list [options]

Para saber mais sobre esse comando, execute o comando `azure rediscache list -h`.

	C:\>azure rediscache list -h
	help:    List all Redis Caches within your Subscription or Resource Group
	help:
	help:    Usage: rediscache list [options]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -g, --resource-group <resource-group>  Name of the Resource Group
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Mostrar as propriedades de um Cache Redis existente

Para mostrar as propriedades de um Cache Redis existente, use o seguinte comando:

	azure rediscache show [--name <name> --resource-group <resource-group>]

Para saber mais sobre esse comando, execute o comando `azure rediscache show -h`.

	C:\>azure rediscache show -h
	help:    Show properties of an existing Redis Cache
	help:
	help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -n, --name <name>                      Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>  Name of the Resource Group
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Alterar as configurações de um Cache Redis existente

Para alterar as propriedades de um Cache Redis existente, use o seguinte comando:

	azure rediscache set [--name <name> --resource-group <resource-group> --max-memory-policy <max-memory-policy>]

Para saber mais sobre esse comando, execute o comando `azure rediscache set -h`.

	C:\>azure rediscache set -h
	help:    Change settings of an existing Redis Cache
	help:
	help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --max-memory-policy <max-memory-policy>]
	help:
	help:    Options:
	help:      -h, --help                                   output usage information
	help:      -v, --verbose                                use verbose output
	help:      -vv                                          more verbose with debug output
	help:      --json                                       use json output
	help:      -n, --name <name>                            Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>        Name of the Resource Group
	help:      -m, --max-memory-policy <max-memory-policy>  Max Memory Policy of the Redis Cache. Valid values: [AllKeysLRU, AllKeysRandom, NoEviction, VolatileLRU, VolatileRandom, VolatileTTL]
	help:      -s, --subscription <subscription>            the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Renovar a chave de autenticação para um Cache Redis existente

Para renovar a chave de autenticação para um Cache Redis existente, use o seguinte comando:

	azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]

Especifique `Primary` ou `Secondary` como `key-type`.

Para saber mais sobre esse comando, execute o comando `azure rediscache renew-key -h`.

	C:\>azure rediscache renew-key -h
	help:    Renew the authentication key for an existing Redis Cache
	help:
	help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -n, --name <name>                      Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
	help:      -t, --key-type <key-type>              type of key to renew
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

## Listar as chaves Primária e Secundária de um Cache Redis existente

Para listar as chaves Primária e Secundária de um Cache Redis, use o seguinte comando:

	azure rediscache list-keys [--name <name> --resource-group <resource-group>]

Para saber mais sobre esse comando, execute o comando `azure rediscache list-keys -h`.

	C:\>azure rediscache list-keys -h
	help:    Lists Primary and Secondary key of an existing Redis Cache
	help:
	help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
	help:
	help:    Options:
	help:      -h, --help                             output usage information
	help:      -v, --verbose                          use verbose output
	help:      -vv                                    more verbose with debug output
	help:      --json                                 use json output
	help:      -n, --name <name>                      Name of the Redis Cache.
	help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
	help:      -s, --subscription <subscription>      the subscription identifier
	help:
	help:    Current Mode: arm (Azure Resource Management)

<!---HONumber=Sept15_HO3-->