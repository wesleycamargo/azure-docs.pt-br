---
title: "Automação do Azure Cosmos DB – CLI 2.0 do Azure | Microsoft Docs"
description: "Use a CLI 2.0 do Azure para criar e gerenciar contas do Azure Cosmos DB. O Azure Cosmos DB é um banco de dados altamente disponível e distribuído globalmente."
services: cosmosdb
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 6158c27f-6b9a-404e-a234-b5d48c4a5b29
ms.custom: quick start create
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/20/2017
ms.author: dimakwan
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 6150914e623cb6c54f1257c772be62150ab81e17
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="create-an-azure-cosmos-db-account-using-the-azure-cli"></a>Criar uma conta do Azure Cosmos DB usando a CLI do Azure

O guia a seguir descreve os comandos para automatizar o gerenciamento de suas contas de banco de dados do Azure Cosmos DB usando os comandos de versão prévia disponíveis na CLI 2.0 do Azure. Isso também inclui comandos para gerenciar as chaves de conta e as prioridades de failover em [contas de banco de dados com várias regiões][scaling-globally]. A atualização da sua conta de banco de dados permite que você adicione ou remova regiões e modifique políticas de consistência. Para o gerenciamento de plataforma cruzada de sua conta de banco de dados do Azure Cosmos DB, use o [Azure PowerShell](documentdb-manage-account-with-powershell.md), a [REST API do Provedor de Recursos][rp-rest-api] ou o [portal do Azure](documentdb-create-account.md).

## <a name="getting-started"></a>Introdução

Siga as instruções em [Como instalar e configurar a CLI 2.0 do Azure][install-az-cli2] para configurar o ambiente de desenvolvimento com a CLI 2.0 do Azure.

Faça logon na sua conta do Azure e execute o comando a seguir e siga as etapas na tela.

```azurecli
az login
```

Se você ainda não tiver um [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups) existente, crie um:

```azurecli
az group create --name <resourcegroupname> --location <resourcegrouplocation>
az group list
```

A `<resourcegrouplocation>` deve ser uma das regiões na qual o Azure Cosmos DB está disponível. Confira a lista atual de regiões na [página Regiões do Azure](https://azure.microsoft.com/regions/#services).

### <a name="notes"></a>Observações

* Execute "az documentdb -h" para obter uma lista completa dos comandos disponíveis ou acesse a [página de referência][az-documentdb-ref].
* Execute “az documentdb &lt;command&gt; -h” para obter uma lista de detalhes dos parâmetros obrigatórios e opcionais por comando.

## <a name="register-your-subscription-to-use-azure-cosmos-db"></a>Registrar sua assinatura para usar o Azure Cosmos DB

Esse comando registra sua assinatura para usar o Azure Cosmos DB por meio da CLI.

```azurecli
az provider register -n Microsoft.DocumentDB 
```

## <a id="create-documentdb-account-cli"></a> Criar uma conta de banco de dados do Azure Cosmos DB

Esse comando permite que você crie uma conta de banco de dados do Azure Cosmos DB. Configure sua nova conta de banco de dados como região única ou [várias regiões][scaling-globally] com uma [política de consistência](documentdb-consistency-levels.md) determinada.

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account. The account 
                                    name must be unique.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the Azure Cosmos DB database account.
                                    Allowed values: BoundedStaleness, Eventual, Session, Strong.
    --ip-range-filter             : Firewall support. Specifies the set of IP addresses or IP
                                    address ranges in CIDR form to be included as the allowed list
                                    of client IPs for a given database account. IP addresses/ranges
                                    must be comma separated and must not contain any spaces.
    --kind                        : The type of Azure Cosmos DB database account to create.  Allowed
                                    values: GlobalDocumentDB, MongoDB, Parse.  Default:
                                    GlobalDocumentDB.
    --locations                   : Space separated locations in 'regionName=failoverPriority'
                                    format. E.g "East US"=0 "West US"=1. Failover priority values
                                    are 0 for write regions and greater than 0 for read regions. A
                                    failover priority value must be unique and less than the total
                                    number of regions. Default: single region account in the
                                    location of the specified resource group.
    --max-interval                : When used with Bounded Staleness consistency, this value
                                    represents the time amount of staleness (in seconds) tolerated.
                                    Accepted range for this value is 1 - 100.  Default: 5.
    --max-staleness-prefix        : When used with Bounded Staleness consistency, this value
                                    represents the number of stale requests tolerated. Accepted
                                    range for this value is 1 - 2,147,483,647.  Default: 100.
```

```azurecli
az documentdb create -g <resourcegroupname> -n <uniquedocumentdbaccountname> --kind <typeofdatabaseaccount>
```

Exemplos: 

    az documentdb create -g rg-test -n docdb-test
    az documentdb create -g rg-test -n docdb-test --kind MongoDB
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 "South Central US"=2
    az documentdb create -g rg-test -n docdb-test --ip-range-filter "13.91.6.132,13.91.6.1/24"
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 --default-consistency-level BoundedStaleness --max-interval 10 --max-staleness-prefix 200

### <a name="notes"></a>Observações 
* As localizações devem ser regiões nas quais o Azure Cosmos DB está disponível. Confira a lista atual de regiões na [página Regiões do Azure](https://azure.microsoft.com/regions/#services).
* Para habilitar o acesso ao portal, inclua o endereço IP do portal do Azure para sua região no filtro de intervalo de IP, conforme especificado em [Configurar a política de controle de acesso IP](documentdb-firewall-support.md#configure-ip-policy).

## <a id="update-documentdb-account-cli"></a> Atualizar uma conta de banco de dados do Azure Cosmos DB

Esse comando permite que você atualize as propriedades de sua conta de banco de dados do Azure Cosmos DB. Isso inclui a política de consistência e os locais nos quais a conta de banco de dados existe.

> [!NOTE]
> Esse comando permite adicionar e remover regiões, mas não permite modificar as prioridades de failover. Para modificar as prioridades de failover, confira [abaixo](#modify-failover-priority-powershell).

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the Azure Cosmos DB database account.
                                    Allowed values: BoundedStaleness, Eventual, Session, Strong.
    --ip-range-filter             : Firewall support. Specifies the set of IP addresses or IP address
                                    ranges in CIDR form to be included as the allowed list of client
                                    IPs for a given database account. IP addresses/ranges must be comma
                                    separated and must not contain any spaces.
    --locations                   : Space separated locations in 'regionName=failoverPriority' format.
                                    E.g "East US"=0. Failover priority values are 0 for write regions
                                    and greater than 0 for read regions. A failover priority value must
                                    be unique and less than the total number of regions.
    --max-interval                : When used with Bounded Staleness consistency, this value represents
                                    the time amount of staleness (in seconds) tolerated. Accepted range
                                    for this value is 1 - 100.
    --max-staleness-prefix        : When used with Bounded Staleness consistency, this value represents
                                    the number of stale requests tolerated. Accepted range for this
                                    value is 1 - 2,147,483,647.
```

Exemplos: 

    az documentdb update -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 "South Central US"=2
    az documentdb update -g rg-test -n docdb-test --ip-range-filter "13.91.6.132,13.91.6.1/24"
    az documentdb update -g rg-test -n docdb-test --default-consistency-level BoundedStaleness --max-interval 10 --max-staleness-prefix 200

## <a id="add-remove-region-documentdb-account-cli"></a> Adicionar/remover uma região de uma conta de banco de dados do Azure Cosmos DB

Para adicionar ou remover regiões de sua conta de banco de dados existente do Azure Cosmos DB, use o comando [update](#update-documentdb-account-cli) com o sinalizador `--locations`. O exemplo a seguir mostra como criar uma conta nova e, posteriormente, adicionar e remover regiões dessa conta.

Exemplo:

    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1
    az documentdb update -g rg-test -n docdb-test --locations "East US"=0 "North Europe"=1 "South Central US"=2


## <a id="delete-documentdb-account-cli"></a> Excluir uma conta de banco de dados do Azure Cosmos DB

Esse comando permite que você exclua uma conta de banco de dados existente do Azure Cosmos DB.

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Exemplo:

    az documentdb delete -g rg-test -n docdb-test

## <a id="get-documentdb-properties-cli"></a> Obter as propriedades de uma conta de banco de dados do Azure Cosmos DB

Esse comando permite que você obtenha as propriedades de uma conta de banco de dados existente do Azure Cosmos DB.

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Exemplo:

    az documentdb show -g rg-test -n docdb-test

## <a id="list-account-keys-cli"></a> Como listar chaves de conta

Quando você cria uma conta do Azure Cosmos DB, o serviço gera duas chaves de acesso mestras que podem ser usadas para autenticação quando a conta do Azure Cosmos DB é acessada. Ao fornecer duas chaves de acesso, o Azure Cosmos DB permite regenerar as chaves sem nenhuma interrupção na conta do Azure Cosmos DB. Também estão disponíveis chaves somente leitura para autenticação de operações somente leitura. Há duas chaves de leitura/gravação (primária e secundária) e duas chaves somente leitura (primária e secundária).

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Exemplo:

    az documentdb list-keys -g rg-test -n docdb-test

## <a id="list-connection-strings-cli"></a> Listar cadeias de conexão

Para contas do MongoDB, a cadeia de conexão para conectar seu aplicativo do MongoDB à conta do banco de dados pode ser recuperada usando o comando a seguir.

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Exemplo:

    az documentdb list-connection-strings -g rg-test -n docdb-test

## <a id="regenerate-account-key-cli"></a> Como recuperar a chave de conta

Você deve alterar as chaves de acesso de sua conta do Azure Cosmos DB periodicamente para ajudar a manter as conexões mais seguras. Duas chaves de acesso são atribuídas para permitir que você mantenha conexões com a conta do Azure Cosmos DB usando uma chave de acesso enquanto regenera a outra.

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
    --key-kind          [Required]: The access key to regenerate.  Allowed values: primary, primaryReadonly,
                                    secondary, secondaryReadonly.
```

Exemplo:

    az documentdb regenerate-key -g rg-test -n docdb-test --key-kind secondary

## <a id="modify-failover-priority-cli"></a> Modificar a prioridade de failover de uma conta de banco de dados do Azure Cosmos DB

Para contas de banco de dados com várias regiões, altere a prioridade de failover das várias regiões nas quais a conta de banco de dados do Azure Cosmos DB existe. Para obter mais informações sobre failover em sua conta de banco de dados do Azure Cosmos DB, consulte [Distribuir dados globalmente com o Azure Cosmos DB](documentdb-distribute-data-globally.md).

```
Arguments
    --name -n           [Required]: Name of the Azure Cosmos DB database account.
    --resource-group -g [Required]: Name of the resource group.
    --failover-policies [Required]: Space separated failover policies in 'regionName=failoverPriority' format.
                                    E.g "East US"=0 "West US"=1.
```

Exemplo:

    az documentdb failover-priority-change "East US"=1 "West US"=0 "South Central US"=2

## <a name="next-steps"></a>Próximas etapas

* Para se conectar usando o .NET, consulte [Conectar e consultar com o .NET](../cosmos-db/create-documentdb-dotnet.md).
* Para se conectar usando o .NET Core, consulte [Conectar e consultar com o .NET Core](../cosmos-db/create-documentdb-dotnet-core.md).
* Para se conectar usando o Node.js, consulte [Conectar e consultar com o Node.js e um aplicativo do MongoDB](../cosmos-db/create-mongodb-nodejs.md).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[scaling-globally]: https://azure.microsoft.com/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet
[install-az-cli2]: https://docs.microsoft.com/cli/azure/install-az-cli2
[az-documentdb-ref]: https://docs.microsoft.com/cli/azure/documentdb
[az-documentdb-create-ref]: https://docs.microsoft.com/cli/azure/documentdb#create
[rp-rest-api]: https://docs.microsoft.com/rest/api/documentdbresourceprovider/

