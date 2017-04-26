---
title: "Automação do DocumentDB - CLI 2.0 do Azure | Microsoft Docs"
description: "Use a CLI 2.0 do Azure para gerenciar as contas de banco de dados do DocumentDB. O Banco de Dados de Documentos é um banco de dados NoSQL baseado em nuvem para dados JSON."
services: documentdb
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 6158c27f-6b9a-404e-a234-b5d48c4a5b29
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 04/04/2017
ms.author: dimakwan
translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: 150e8f3e186683bce735d0952adb57544505d1e9
ms.lasthandoff: 04/06/2017


---
# <a name="automate-azure-documentdb-account-management-using-azure-cli-20"></a>Como automatizar o gerenciamento de conta do Azure DocumentDB usando a CLI 2.0 do Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](documentdb-create-account.md)
> * [CLI 1.0 do Azure](documentdb-automation-resource-manager-cli-nodejs.md)
> * [CLI 2.0 do Azure](documentdb-automation-resource-manager-cli.md)
> * [Powershell do Azure](documentdb-manage-account-with-powershell.md)

O guia a seguir descreve os comandos para automatizar o gerenciamento das suas contas de banco de dados do DocumentDB usando os comandos de visualização do DocumentDB disponíveis na CLI 2.0 do Azure. Isso também inclui comandos para gerenciar as chaves de conta e as prioridades de failover em [contas de banco de dados com várias regiões][scaling-globally]. A atualização da sua conta de banco de dados permite que você adicione ou remova regiões e modifique políticas de consistência. Para o gerenciamento de plataforma cruzada da sua conta de banco de dados do DocumentDB, você pode usar o [Azure Powershell](documentdb-manage-account-with-powershell.md), a [REST API do provedor de recursos][rp-rest-api] ou o [Portal do Azure](documentdb-create-account.md).

## <a name="getting-started"></a>Introdução

Siga as instruções em [Como instalar e configurar a CLI 2.0 do Azure][install-az-cli2] para configurar o ambiente de desenvolvimento com a CLI 2.0 do Azure.

Faça logon na sua conta do Azure e execute o comando a seguir e siga as etapas na tela.

    az login

Se você ainda não tiver um [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups) existente, crie um:

    az group create --name <resourcegroupname> --location <resourcegrouplocation>
    az group list

O `<resourcegrouplocation>` deve ser uma das regiões na qual o DocumentDB normalmente está disponível. Confira a lista atual de regiões na [página Regiões do Azure](https://azure.microsoft.com/regions/#services).

### <a name="notes"></a>Observações

* Execute "az documentdb -h" para obter uma lista completa dos comandos disponíveis ou acesse a [página de referência][az-documentdb-ref].
* Execute "az documentdb <command> -h" para obter uma lista de detalhes dos parâmetros necessários e opcionais por comando.

## <a id="create-documentdb-account-cli"></a>Criação de uma conta de banco de dados do DocumentDB

Esse comando permite que você crie uma conta de banco de dados do DocumentDB. Configure sua nova conta de banco de dados como região única ou [várias regiões][scaling-globally] com uma [política de consistência](documentdb-consistency-levels.md) determinada. 

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the DocumentDB database account.
                                    Allowed values: BoundedStaleness, Eventual, Session, Strong.
    --ip-range-filter             : Firewall support. Specifies the set of IP addresses or IP
                                    address ranges in CIDR form to be included as the allowed list
                                    of client IPs for a given database account. IP addresses/ranges
                                    must be comma separated and must not contain any spaces.
    --kind                        : The type of DocumentDB database account to create.  Allowed
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

Exemplos: 

    az documentdb create -g rg-test -n docdb-test
    az documentdb create -g rg-test -n docdb-test --kind MongoDB
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 "South Central US"=2
    az documentdb create -g rg-test -n docdb-test --ip-range-filter "13.91.6.132,13.91.6.1/24"
    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1 --default-consistency-level BoundedStaleness --max-interval 10 --max-staleness-prefix 200

### <a name="notes"></a>Observações
* Os locais devem ser regiões nas quais o DocumentDB normalmente está disponível. Confira a lista atual de regiões na [página Regiões do Azure](https://azure.microsoft.com/regions/#services).
* Para habilitar o acesso ao portal, inclua o endereço IP do portal do Azure para sua região no filtro de intervalo de IP, conforme especificado em [Configurar a política de controle de acesso IP](documentdb-firewall-support.md#configure-ip-policy).

## <a id="update-documentdb-account-cli"></a> Atualização de uma conta de banco de dados do DocumentDB

Esse comando permite que você atualize as propriedades de sua conta de banco de dados do DocumentDB. Isso inclui a política de consistência e os locais nos quais a conta de banco de dados existe.

> [!NOTE]
> Esse comando permite adicionar e remover regiões, mas não permite modificar as prioridades de failover. Para modificar as prioridades de failover, confira [abaixo](#modify-failover-priority-powershell).

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --default-consistency-level   : Default consistency level of the DocumentDB database account.
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

## <a id="add-remove-region-documentdb-account-cli"></a>Como adicionar ou remover uma região de uma conta de banco de dados do DocumentDB

Para adicionar ou remover regiões da sua conta de banco de dados existente do DocumentDB, use o comando [atualizar](#update-documentdb-account-cli) com o sinalizador `--locations`. O exemplo a seguir mostra como criar uma conta nova e, posteriormente, adicionar e remover regiões dessa conta.

Exemplo:

    az documentdb create -g rg-test -n docdb-test --locations "East US"=0 "West US"=1
    az documentdb update -g rg-test -n docdb-test --locations "East US"=0 "North Europe"=1 "South Central US"=2


## <a id="delete-documentdb-account-cli"></a> Como excluir uma conta de banco de dados do DocumentDB

Esse comando permite que você exclua uma conta de banco de dados existente do DocumentDB.

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Exemplo:

    az documentdb delete -g rg-test -n docdb-test

## <a id="get-documentdb-properties-cli"></a>Como obter propriedades de uma conta de banco de dados do DocumentDB

Esse comando permite que você obtenha as propriedades de uma conta de banco de dados existente do DocumentDB.

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Exemplo:

    az documentdb show -g rg-test -n docdb-test

## <a id="list-account-keys-cli"></a> Como listar chaves de conta

Quando você cria uma conta do Banco de Dados de Documentos, o serviço gera duas chaves de acesso mestras que podem ser usadas para autenticação quando a conta do Banco de Dados de Documentos é acessada. Ao fornecer as duas chaves de acesso, o Banco de Dados de Documentos permite regenerar as chaves sem interrupção para a sua conta do Banco de Dados de Documentos. Também estão disponíveis chaves somente leitura para autenticação de operações somente leitura. Há duas chaves de leitura/gravação (primária e secundária) e duas chaves somente leitura (primária e secundária).

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Exemplo:

    az documentdb list-keys -g rg-test -n docdb-test

## <a id="list-connection-strings-cli"></a> Listar cadeias de conexão

Para contas do MongoDB, a cadeia de conexão para conectar seu aplicativo do MongoDB à conta do banco de dados pode ser recuperada usando o comando a seguir.

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
```

Exemplo:

    az documentdb list-connection-strings -g rg-test -n docdb-test

## <a id="regenerate-account-key-cli"></a> Como recuperar a chave de conta

Você deve alterar as chaves de acesso da sua conta do Banco de Dados de Documentos periodicamente para ajudar a manter as conexões mais seguras. Duas chaves de acesso são atribuídas para permitir que você mantenha conexões com a conta do Banco de Dados de Documentos usando uma chave de acesso enquanto regenera a outra chave de acesso.

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --key-kind          [Required]: The access key to regenerate.  Allowed values: primary, primaryReadonly,
                                    secondary, secondaryReadonly.
```

Exemplo:

    az documentdb regenerate-key -g rg-test -n docdb-test --key-kind secondary

## <a id="modify-failover-priority-cli"></a> Modificar a prioridade de failover de uma conta de banco de dados do DocumentDB

Para contas de banco de dados com várias regiões, você pode alterar a prioridade de failover das várias regiões nas quais a conta de banco de dados do DocumentDB existe. Para saber mais sobre failover em sua conta de banco de dados do DocumentDB, veja [Distribuir dados globalmente com o DocumentDB](documentdb-distribute-data-globally.md).

```
Arguments
    --name -n           [Required]: Name of the DocumentDB database account.
    --resource-group -g [Required]: Name of the resource group.
    --failover-policies [Required]: Space separated failover policies in 'regionName=failoverPriority' format.
                                    E.g "East US"=0 "West US"=1.
```

Exemplo:

    az documentdb failover-priority-change "East US"=1 "West US"=0 "South Central US"=2

## <a name="next-steps"></a>Próximas etapas
Agora que você tem uma conta do Banco de Dados de Documentos, a próxima etapa é criar um banco de dados do Banco de Dados de Documentos. Você pode criar um banco de dados usando:

* O Portal do Azure, como descrito em [Como criar uma coleção e um banco de dados do DocumentDB usando o Portal do Azure](documentdb-create-collection.md).
* As amostras do C# .NET no projeto [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) do repositório [azure-documentdb-dotnet](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) no GitHub.
* As [SDKs do Banco de Dados de Documentos](documentdb-sdk-dotnet.md). O Banco de Dados de Documentos tem SDKs de API de JavaScript, .NET, Java, Python e Node.js.

Depois de criar seu banco de dados, você precisará [adicionar uma ou mais coleções](documentdb-create-collection.md) ao banco de dados e [adicionar documentos](documentdb-view-json-document-explorer.md) às coleções.


Depois que os documentos estiverem em uma coleção, você poderá usar o [SQL do DocumentDB](documentdb-sql-query.md) para [executar consultas](documentdb-sql-query.md#ExecutingSqlQueries) nos documentos usando o [Gerenciador de Consultas](documentdb-query-collections-query-explorer.md) no portal, a [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) ou um dos [SDKs](https://msdn.microsoft.com/library/azure/dn781482.aspx).

Para saber mais sobre o DocumentDB, explore estes recursos:

* [Roteiro de aprendizagem para o Banco de Dados de Documentos](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
* [Conceitos e modelo de recursos do Banco de Dados de Documentos](documentdb-resources.md)


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[scaling-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet
[install-az-cli2]: https://docs.microsoft.com/en-us/cli/azure/install-az-cli2
[az-documentdb-ref]: https://docs.microsoft.com/en-us/cli/azure/documentdb
[az-documentdb-create-ref]: https://docs.microsoft.com/en-us/cli/azure/documentdb#create
[rp-rest-api]: https://docs.microsoft.com/en-us/rest/api/documentdbresourceprovider/

