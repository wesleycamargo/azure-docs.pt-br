---
title: Criar e gerenciar usando o PowerShell do Azure Cosmos DB
description: Use Azure Powershell para gerenciar suas contas do Azure Cosmos DB, os bancos de dados, contêineres e taxa de transferência.
author: markjbrown
ms.service: cosmos-db
ms.topic: samples
ms.date: 05/06/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 347c3a1c6cabca9532e5ada1237f2933841d5094
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077584"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>Gerenciar recursos da API de SQL do Azure Cosmos DB usando o PowerShell

O guia a seguir descreve como usar o PowerShell para script e automatizar o gerenciamento do Azure Cosmos DB, incluindo a conta, banco de dados, contêiner e taxa de transferência. O gerenciamento do Azure Cosmos DB é não por meio de cmdlets específicos do Azure Cosmos DB, mas com o provedor de recursos diretamente por meio do cmdlet AzResource. Para exibir todas as propriedades que podem ser gerenciadas usando o PowerShell para o provedor de recursos do Azure Cosmos DB, consulte [esquema do provedor de recursos do Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)

Para o gerenciamento de plataforma cruzada do Azure Cosmos DB, você pode usar [CLI do Azure](manage-with-cli.md), o [API REST][rp-rest-api], ou o [portal do Azure](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Introdução

Siga as instruções em [como instalar e configurar o Azure PowerShell] [ powershell-install-configure] para instalar e entrar em sua conta do Azure no Powershell.

* Se você quiser executar os comandos a seguir sem a necessidade de confirmação do usuário, acrescente o sinalizador `-Force` ao comando.
* Todos os comandos a seguir são síncronos.

## <a name="azure-cosmos-accounts"></a>Contas do Cosmos do Azure

As seções a seguir demonstram como gerenciar a conta do Cosmos do Azure, incluindo:

* [Criar uma conta do Azure Cosmos](#create-account)
* [Atualizar uma conta do Azure Cosmos](#update-account)
* [Obter uma conta do Azure Cosmos](#get-account)
* [Excluir uma conta do Azure Cosmos](#delete-account)
* [Atualizar as marcas para uma conta do Azure Cosmos](#update-tags)
* [Listar chaves para uma conta do Azure Cosmos](#list-keys)
* [Regenerar chaves para uma conta do Azure Cosmos](#regenerate-keys)
* [Lista cadeias de caracteres de conexão para uma conta do Azure Cosmos](#list-connection-strings)
* [Modificar a prioridade de failover para uma conta do Azure Cosmos](#modify-failover-priority)

### <a id="create-account"></a> Criar uma conta do Azure Cosmos

Esse comando permite que você crie uma conta de banco de dados do Azure Cosmos DB. Configure sua nova conta de banco de dados como região única ou [várias regiões][distribute-data-globally] com uma [política de consistência](consistency-levels.md) determinada.

```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.

$locations = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East US"; "failoverPriority"=1 }
)

$consistencyPolicy = @{
    "defaultConsistencyLevel"="BoundedStaleness";
    "maxIntervalInSeconds"=300;
    "maxStalenessPrefix"=100000
}

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="true"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

* `$accountName` O nome da conta do Cosmos do Azure. Deve estar em minúsculas, aceita alfanuméricos e '-' caracteres e entre 3 e 50 caracteres.
* `$location` O local para a conta do Cosmos do Azure.
* `$locations` As regiões de réplica para a conta de banco de dados. Deve haver uma região de gravação por conta de banco de dados com um valor de prioridade de failover de 0.
* `$consistencyPolicy` O nível de consistência padrão da conta do Cosmos do Azure. Para obter mais informações, consulte [Níveis de consistência no Azure Cosmos DB](consistency-levels.md).
* `$CosmosDBProperties` Os valores de propriedade passados para o provedor de Gerenciador de recursos do Cosmos DB do Azure para provisionar a conta.

Pontos de extremidade de serviço de contas podem ser configuradas com Firewall de IP, bem como a rede Virtual do Azure Cosmos. Para obter informações sobre como configurar o Firewall de IP para o Azure Cosmos DB, consulte [configurar o Firewall do IP](how-to-configure-firewall.md).  Para obter mais informações sobre como habilitar pontos de extremidade de serviço do Azure Cosmos DB, consulte [configurar o acesso de redes virtuais](how-to-configure-vnet-service-endpoint.md) .

### <a id="get-account"></a> Obter as propriedades de uma conta do Azure Cosmos

Esse comando permite que você obtenha as propriedades de uma conta existente do Cosmos do Azure.

```azurepowershell-interactive
# Get the properties of an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName | Select-Object Properties
```

### <a id="update-account"></a> Atualizar uma conta do Azure Cosmos

Esse comando permite que você atualize as propriedades de sua conta de banco de dados do Azure Cosmos DB. As propriedades que podem ser atualizadas incluem o seguinte:

* Adicionar ou remover regiões
* Alterar a política de consistência padrão
* Alterar a política de Failover
* Alterar o filtro de intervalo de IP
* Alterar as configurações de rede Virtual
* Habilitando vários mestres

> [!NOTE]
> Esse comando permite adicionar e remover regiões, mas não permite modificar as prioridades de failover. Para modificar as prioridades de failover, consulte [modificar a prioridade de failover para uma conta do Azure Cosmos](#modify-failover-priority).

```azurepowershell-interactive
# Update an Azure Cosmos Account and set Consistency level to Session

$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }

$account.Properties.consistencyPolicy = $consistencyPolicy
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="delete-account"></a> Excluir uma conta do Azure Cosmos

Esse comando permite que você exclua uma conta existente do Cosmos do Azure.

```azurepowershell-interactive
# Delete an Azure Cosmos Account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName
```

### <a id="update-tags"></a> Marcas de atualização de uma conta do Azure Cosmos

O exemplo a seguir descreve como definir [as marcas de recurso do Azure] [ azure-resource-tags] para uma conta do Cosmos do Azure.

> [!NOTE]
> Este comando pode ser combinado com os comandos de criação ou atualização anexando o sinalizador `-Tags` com o parâmetro correspondente.

```azurepowershell-interactive
# Update tags for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$tags = @{
    "dept" = "Finance";
    "environment" = "Production"
}

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -Tags $tags
```

### <a id="list-keys"></a> Listar chaves da conta

Quando você cria uma conta do Azure Cosmos DB, o serviço gera duas chaves de acesso mestras que podem ser usadas para autenticação quando a conta do Azure Cosmos DB é acessada. Ao fornecer duas chaves de acesso, o Azure Cosmos DB permite regenerar as chaves sem nenhuma interrupção na conta do Azure Cosmos DB. Também estão disponíveis chaves somente leitura para autenticação de operações somente leitura. Há duas chaves de leitura/gravação (primária e secundária) e duas chaves somente leitura (primária e secundária).

```azurepowershell-interactive
# List keys for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listKeys `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="list-connection-strings"></a> Listar Cadeias de Conexão

Para contas do MongoDB, a cadeia de conexão para conectar seu aplicativo do MongoDB à conta do banco de dados pode ser recuperada usando o comando a seguir.

```azurepowershell-interactive
# List connection strings for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listConnectionStrings `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="regenerate-keys"></a> Regenerar chaves de conta

Chaves de acesso a uma conta do Cosmos do Azure deverá ser regeneradas periodicamente para ajudar a manter conexões mais seguras. Uma chave de acesso primária e secundária é atribuídos à conta. Isso permite que os clientes manter o acesso enquanto a outra é regenerada. Há quatro tipos de chaves para uma conta de Cosmos do Azure (primário, secundário, PrimaryReadonly e SecondaryReadonly)

```azurepowershell-interactive
# Regenerate the primary key for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keyKind = @{ "keyKind"="Primary" }

$keys = Invoke-AzResourceAction -Action regenerateKey `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $keyKind

Select-Object $keys
```

### <a id="modify-failover-priority"></a> Modificar a prioridade de Failover

Para contas de banco de dados de várias regiões, você pode alterar a prioridade de failover de várias regiões em que a conta de banco de dados do Azure Cosmos DB existe. Para obter mais informações sobre failover em sua conta de banco de dados do Azure Cosmos DB, consulte [Distribuir dados globalmente com o Azure Cosmos DB][distribute-data-globally].

Para o exemplo a seguir, suponha que a conta tem uma prioridade de failover atual do Oeste dos EUA = 0 e eastus = 1. Este exemplo a seguir mudarão as regiões.

> [!CAUTION]
> Esta operação vai disparar um failover manual de sua conta para a nova região com uma failoverPriority igual a zero.

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverPolicies = @(
    @{ "locationName"="East US"; "failoverPriority"=0 },
    @{ "locationName"="West US"; "failoverPriority"=1 }
)

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a name="azure-cosmos-database"></a>Banco de dados Cosmos do Azure

As seções a seguir demonstram como gerenciar o banco de dados Cosmos do Azure, incluindo:

* [Criar um banco de dados Cosmos do Azure](#create-db)
* [Criar um banco de dados Cosmos do Azure com taxa de transferência compartilhada](#create-db-ru)
* [Listar todos os bancos de dados Cosmos do Azure em uma conta](#get-all-db)
* [Obter um único banco de dados Cosmos do Azure](#get-db)
* [Excluir um banco de dados Cosmos do Azure](#delete-db)

### <a id="create-db"></a>Criar um banco de dados Cosmos do Azure

```azurepowershell-interactive
# Create an Azure Cosmos database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{"id"=$databaseName}
} 
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="create-db-ru"></a>Criar um banco de dados Cosmos do Azure com taxa de transferência compartilhada

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database2"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="get-all-db"></a>Obter todos os bancos de dados Cosmos do Azure em uma conta

```azurepowershell-interactive
# Get all databases in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceName = $accountName + "/sql/"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName  | Select-Object Properties
```

### <a id="get-db"></a>Obter um único banco de dados Cosmos do Azure

```azurepowershell-interactive
# Get a single database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-db"></a>Excluir um banco de dados Cosmos do Azure

```azurepowershell-interactive
# Delete a database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName
Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="azure-cosmos-container"></a>Azure Cosmos Container

As seções a seguir demonstram como gerenciar o contêiner do Cosmos do Azure, incluindo:

* [Criar um contêiner do Azure Cosmos](#create-container)
* [Criar um contêiner do Azure Cosmos com taxa de transferência compartilhada](#create-container-ru)
* [Criar um contêiner do Azure Cosmos com indexação personalizada](#create-container-custom-index)
* [Criar um contêiner do Azure Cosmos com indexação desativado](#create-container-no-index)
* [Criar um contêiner do Azure Cosmos com chave exclusiva e o TTL](#create-container-unique-key-ttl)
* [Criar um contêiner do Azure Cosmos com resolução de conflitos](#create-container-lww)
* [Listar todos os contêineres de Cosmos do Azure em um banco de dados](#list-all-container)
* [Obter um único contêiner do Cosmos do Azure em um banco de dados](#get-container)
* [Excluir um contêiner do Azure Cosmos](#delete-container)

### <a id="create-container"></a>Criar um contêiner do Azure Cosmos

```azurepowershell-interactive
# Create an Azure Cosmos container with default indexes and throughput at 400 RU 
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }
    }; 
    "options"=@{ "Throughput"="400" }
} 
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-ru"></a>Criar um contêiner do Azure Cosmos com taxa de transferência compartilhada

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container2"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }
    }; 
    "options"=@{}
} 
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties 
```

### <a id="create-container-custom-index"></a>Criar um contêiner do Azure Cosmos com a política de índice personalizado

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container3"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent"; 
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @(@{
                        "kind"="Range";
                        "dataType"="number";
                        "precision"=-1
                    },
                    @{
                        "kind"="Range";
                        "dataType"="string";
                        "precision"=-1
                    }
                )
            });
            "excludedPaths"= @(@{
                "path"="/myPathToNotIndex/*"
            })
        }
    };
    "options"=@{ "Throughput"="400" }
} 

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-no-index"></a>Criar um contêiner do Azure Cosmos com indexação desativado

```azurepowershell-interactive
# Create an Azure Cosmos container with no indexing 
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container4"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="none"
        }
    };
    "options"=@{ "Throughput"="400" }
} 

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-unique-key-ttl"></a>Criar um contêiner do Azure Cosmos com política de chave exclusiva e o TTL

```azurepowershell-interactive
# Create a container with a unique key policy and TTL
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent"; 
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @(@{
                        "kind"="Range";
                        "dataType"="number";
                        "precision"=-1
                    },
                    @{
                        "kind"="Range";
                        "dataType"="string";
                        "precision"=-1
                    }
                )
            });
            "excludedPaths"= @()
        };
        "uniqueKeyPolicy"= @{
            "uniqueKeys"= @(@{
                "paths"= @(
                    "/myUniqueKey1";
                    "/myUniqueKey2"
                )
            })
        };
        "defaultTtl"= 100;
    }; 
    "options"=@{ "Throughput"="400" }
} 

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-lww"></a>Criar um contêiner do Azure Cosmos com resolução de conflitos

Para criar uma política de resolução de conflito para usar um procedimento armazenado, defina `"mode"="custom"` e defina o caminho de resolução como o nome do procedimento armazenado, `"conflictResolutionPath"="myResolverStoredProcedure"`. Para todos os conflitos de gravação para o ConflictsFeed e manipular separadamente, definir `"mode"="custom"` e `"conflictResolutionPath"=""`

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy 
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container6"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "conflictResolutionPolicy"=@{
            "mode"="lastWriterWins"; 
            "conflictResolutionPath"="/myResolutionPath"
        }
    }; 
    "options"=@{ "Throughput"="400" }
} 
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="list-all-container"></a>Listar todos os contêineres de Cosmos do Azure em um banco de dados

```azurepowershell-interactive
# List all Azure Cosmos containers in a database 
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="get-container"></a>Obter um único contêiner do Cosmos do Azure em um banco de dados

```azurepowershell-interactive
# Get a single Azure Cosmos container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-container"></a>Excluir um contêiner do Azure Cosmos

```azurepowershell-interactive
# Delete an Azure Cosmos container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="next-steps"></a>Próximas etapas

* [Todos os exemplos do PowerShell](powershell-samples.md)
* [Como gerenciar a conta do Azure Cosmos](how-to-manage-database-account.md)
* [Criar um contêiner do Azure Cosmos](how-to-create-container.md)
* [Configurar o tempo de vida no Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/