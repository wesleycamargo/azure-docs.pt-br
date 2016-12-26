---
title: "Automação do DocumentDB do Azure - Gerenciamento com o Powershell | Microsoft Docs"
description: "Usar o Azure Powershell para gerenciar suas contas de banco de dados do DocumentDB. O Banco de Dados de Documentos é um banco de dados NoSQL baseado em nuvem para dados JSON."
services: documentdb
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2016
ms.author: dimakwan
translationtype: Human Translation
ms.sourcegitcommit: d220b3b8189a22e6450897fd5e7a865725051a8f
ms.openlocfilehash: 0722c7c08cd7b994c25284cac45bd6f663b289b7


---
# <a name="automate-azure-documentdb-account-management-using-azure-powershell"></a>Automatizar o gerenciamento de conta do DocumentDB do Azure usando o Azure Powershell
> [!div class="op_single_selector"]
> * [portal do Azure](documentdb-create-account.md)
> * [CLI do Azure e ARM](documentdb-automation-resource-manager-cli.md)
> * [Powershell do Azure](documentdb-manage-account-with-powershell.md)

O guia a seguir descreve os comandos para automatizar o gerenciamento de suas contas de banco de dados do DocumentDB usando o Azure Powershell. Isso também inclui comandos para gerenciar as chaves de conta e as prioridades de failover em [contas de banco de dados com várias regiões][scaling-globally]. A atualização de sua conta de banco de dados permite que você adicione ou remova regiões e modifique políticas de consistência. Para gerenciamento de plataforma cruzada de sua conta de banco de dados do DocumentDB, você pode usar a [CLI do Azure](documentdb-automation-resource-manager-cli.md), a [API REST do Provedor de recursos][rp-rest-api] ou o [Portal do Azure](documentdb-create-account.md).

## <a name="getting-started"></a>Introdução

Siga as instruções em [Como instalar e configurar o Azure PowerShell][powershell-install-configure] para instalar e fazer logon em sua conta do Azure Resource Manager no Powershell.

### <a name="notes"></a>Observações

* Se você quiser executar os comandos a seguir sem a necessidade de confirmação do usuário, acrescente o sinalizador `-Force` ao comando.
* Todos os comandos a seguir são síncronos.

## <a name="a-idcreate-documentdb-account-powershella-create-a-documentdb-database-account"></a><a id="create-documentdb-account-powershell"></a> Criar uma conta de banco de dados do DocumentDB

Esse comando permite que você crie uma conta de banco de dados do DocumentDB. Configure sua nova conta de banco de dados como região única ou [várias regiões][scaling-globally] com uma [política de consistência](documentdb-consistency-levels.md) determinada.

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $DocumentDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name>  -Location "<resource-group-location>" -Name <database-account-name> -PropertyObject $DocumentDBProperties
    
* `<write-region-location>` O nome do local da região de gravação da conta do banco de dados. Esse local deve ter um valor de prioridade de failover de 0. Deve haver exatamente uma região de gravação por conta do banco de dados.
* `<read-region-location>` O nome do local da região de leitura da conta do banco de dados. Esse local deve ter um valor de prioridade de failover maior do que 0. Pode haver mais de uma região de leitura por conta do banco de dados.
* `<default-consistency-level>` O nível de consistência padrão da conta do DocumentDB. Para saber mais, confira [Níveis de consistência no DocumentDB](documentdb-consistency-levels.md).
* `<max-interval>` Ao ser usado com a consistência Estagnação Limitada, esse valor representa a quantidade de tempo de desatualização (em segundos) tolerada. O intervalo aceito para este valor é de 1 a 100.
* `<max-staleness-prefix>` Ao ser usado com a consistência Estagnação Limitada, esse valor representa o número de solicitações de estagnação tolerado. O intervalo aceito para este valor é de 1 a 2,147,483,647.
* `<resource-group-name>` O nome do [Grupo de recursos do Azure][azure-resource-groups] ao qual pertence a nova conta de banco de dados do DocumentDB.
* `<resource-group-location>` O local do Grupo de recursos do Azure ao qual pertence a nova conta de banco de dados do DocumentDB.
* `<database-account-name>` O nome da conta de banco de dados do DocumentDB a ser criada. Pode usar apenas letras minúsculas, números, o caractere '-' e deve ter entre três e 50 caracteres.

Exemplo: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $DocumentDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy}
    New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Location "West US" -Name "docdb-test" -PropertyObject $DocumentDBProperties

### <a name="notes"></a>Observações
* O exemplo anterior cria uma conta de banco de dados com duas regiões. Também é possível criar uma conta de banco de dados com uma região (que é designada como a região de gravação e tem um valor de prioridade de failover de 0) ou com mais de duas regiões. Para saber mais, veja [Contas de banco de dados com várias regiões][scaling-globally].
* Os locais devem ser regiões nas quais o DocumentDB normalmente está disponível. Confira a lista atual de regiões na [página Regiões do Azure](https://azure.microsoft.com/regions/#services).

## <a name="a-idupdate-documentdb-account-powershella-update-a-documentdb-database-account"></a><a id="update-documentdb-account-powershell"></a> Atualizar uma conta de banco de dados do DocumentDB

Esse comando permite que você atualize as propriedades de sua conta de banco de dados do DocumentDB. Isso inclui a política de consistência e os locais nos quais a conta de banco de dados existe.

> [!NOTE]
> Esse comando permite adicionar e remover regiões, mas não permite modificar as prioridades de failover. Para modificar as prioridades de failover, confira [abaixo](#modify-failover-priority-powershell).

    $locations = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0}, @{"locationName"="<read-region-location>"; "failoverPriority"=1})
    $consistencyPolicy = @{"defaultConsistencyLevel"="<default-consistency-level>"; "maxIntervalInSeconds"="<max-interval>"; "maxStalenessPrefix"="<max-staleness-prefix>"}
    $DocumentDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <resource-group-name> -Name <database-account-name> -PropertyObject $DocumentDBProperties
    
* `<write-region-location>` O nome do local da região de gravação da conta do banco de dados. Esse local deve ter um valor de prioridade de failover de 0. Deve haver exatamente uma região de gravação por conta do banco de dados.
* `<read-region-location>` O nome do local da região de leitura da conta do banco de dados. Esse local deve ter um valor de prioridade de failover maior do que 0. Pode haver mais de uma região de leitura por conta do banco de dados.
* `<default-consistency-level>` O nível de consistência padrão da conta do DocumentDB. Para saber mais, confira [Níveis de consistência no DocumentDB](documentdb-consistency-levels.md).
* `<max-interval>` Ao ser usado com a consistência Estagnação Limitada, esse valor representa a quantidade de tempo de desatualização (em segundos) tolerada. O intervalo aceito para este valor é de 1 a 100.
* `<max-staleness-prefix>` Ao ser usado com a consistência Estagnação Limitada, esse valor representa o número de solicitações de estagnação tolerado. O intervalo aceito para este valor é de 1 a 2,147,483,647.
* `<resource-group-name>` O nome do [Grupo de recursos do Azure][azure-resource-groups] ao qual pertence a nova conta de banco de dados do DocumentDB.
* `<resource-group-location>` O local do Grupo de recursos do Azure ao qual pertence a nova conta de banco de dados do DocumentDB.
* `<database-account-name>` O nome da conta de banco de dados do DocumentDB a ser atualizada.

Exemplo: 

    $locations = @(@{"locationName"="West US"; "failoverPriority"=0}, @{"locationName"="East US"; "failoverPriority"=1})
    $consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness"; "maxIntervalInSeconds"=5; "maxStalenessPrefix"=100}
    $DocumentDBProperties = @{"databaseAccountOfferType"="Standard"; "locations"=$locations; "consistencyPolicy"=$consistencyPolicy}
    Set-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -PropertyObject $DocumentDBProperties

## <a name="a-iddelete-documentdb-account-powershella-delete-a-documentdb-database-account"></a><a id="delete-documentdb-account-powershell"></a> Excluir uma conta de banco de dados do DocumentDB

Esse comando permite que você exclua uma conta de banco de dados existente do DocumentDB.

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"
    
* `<resource-group-name>` O nome do [Grupo de recursos do Azure][azure-resource-groups] ao qual pertence a nova conta de banco de dados do DocumentDB.
* `<database-account-name>` O nome da conta de banco de dados do DocumentDB a ser excluída.

Exemplo:

    Remove-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a name="a-idget-documentdb-properties-powershella-get-properties-of-a-documentdb-database-account"></a><a id="get-documentdb-properties-powershell"></a>Obter propriedades de uma conta de banco de dados do DocumentDB

Esse comando permite que você obtenha as propriedades de uma conta de banco de dados existente do DocumentDB.

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` O nome do [Grupo de recursos do Azure][azure-resource-groups] ao qual pertence a nova conta de banco de dados do DocumentDB.
* `<database-account-name>` O nome da conta de banco de dados do DocumentDB.

Exemplo:

    Get-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a name="a-idupdate-tags-powershella-update-tags-of-a-documentdb-database-account"></a><a id="update-tags-powershell"></a> Atualizar marcas de uma conta de banco de dados do DocumentDB

O exemplo a seguir descreve como definir [marcas de recurso do Azure][azure-resource-tags] para sua conta banco de dados do DocumentDB.

> [!NOTE]
> Este comando pode ser combinado com os comandos de criação ou atualização anexando o sinalizador `-Tags` com o parâmetro correspondente.

Exemplo:

    $tags = @{"dept" = "Finance”; environment = “Production”}
    Set-AzureRmResource -ResourceType “Microsoft.DocumentDB/databaseAccounts”  -ResourceGroupName "rg-test" -Name "docdb-test" -Tags $tags

## <a name="a-idlist-account-keys-powershella-list-account-keys"></a><a id="list-account-keys-powershell"></a> Listar chaves da conta

Quando você cria uma conta do Banco de Dados de Documentos, o serviço gera duas chaves de acesso mestras que podem ser usadas para autenticação quando a conta do Banco de Dados de Documentos é acessada. Ao fornecer as duas chaves de acesso, o Banco de Dados de Documentos permite regenerar as chaves sem interrupção para a sua conta do Banco de Dados de Documentos. Também estão disponíveis chaves somente leitura para autenticação de operações somente leitura. Há duas chaves de leitura/gravação (primária e secundária) e duas chaves somente leitura (primária e secundária).

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>"

* `<resource-group-name>` O nome do [Grupo de recursos do Azure][azure-resource-groups] ao qual pertence a nova conta de banco de dados do DocumentDB.
* `<database-account-name>` O nome da conta de banco de dados do DocumentDB.

Exemplo:

    $keys = Invoke-AzureRmResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test"

## <a name="a-idregenerate-account-key-powershella-regenerate-account-key"></a><a id="regenerate-account-key-powershell"></a> Recuperar a chave da conta

Você deve alterar as chaves de acesso da sua conta do Banco de Dados de Documentos periodicamente para ajudar a manter as conexões mais seguras. Duas chaves de acesso são atribuídas para permitir que você mantenha conexões com a conta do Banco de Dados de Documentos usando uma chave de acesso enquanto regenera a outra chave de acesso.

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"keyKind"="<key-kind>"}

* `<resource-group-name>` O nome do [Grupo de recursos do Azure][azure-resource-groups] ao qual pertence a nova conta de banco de dados do DocumentDB.
* `<database-account-name>` O nome da conta de banco de dados do DocumentDB.
* `<key-kind>` Um dos quatro tipos de chaves: ["Primary"|"Secondary"|"PrimaryReadonly"|"SecondaryReadonly"] que você quer gerar novamente.

Exemplo:

    Invoke-AzureRmResourceAction -Action regenerateKey -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"keyKind"="Primary"}

## <a name="a-idmodify-failover-priority-powershella-modify-failover-priority-of-a-documentdb-database-account"></a><a id="modify-failover-priority-powershell"></a> Modificar a prioridade de Failover de uma conta de banco de dados do DocumentDB

Para contas de banco de dados com várias regiões, você pode alterar a prioridade de failover das várias regiões nas quais a conta de banco de dados do DocumentDB existe. Para saber mais sobre failover em sua conta de banco de dados do DocumentDB, veja [Distribuir dados globalmente com o DocumentDB][distribute-data-globally].

    $failoverPolicies = @(@{"locationName"="<write-region-location>"; "failoverPriority"=0},@{"locationName"="<read-region-location>"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "<resource-group-name>" -Name "<database-account-name>" -Parameters @{"failoverPolicies"=$failoverPolicies}

* `<write-region-location>` O nome do local da região de gravação da conta do banco de dados. Esse local deve ter um valor de prioridade de failover de 0. Deve haver exatamente uma região de gravação por conta do banco de dados.
* `<read-region-location>` O nome do local da região de leitura da conta do banco de dados. Esse local deve ter um valor de prioridade de failover maior do que 0. Pode haver mais de uma região de leitura por conta do banco de dados.
* `<resource-group-name>` O nome do [Grupo de recursos do Azure][azure-resource-groups] ao qual pertence a nova conta de banco de dados do DocumentDB.
* `<database-account-name>` O nome da conta de banco de dados do DocumentDB.

Exemplo:

    $failoverPolicies = @(@{"locationName"="East US"; "failoverPriority"=0},@{"locationName"="West US"; "failoverPriority"=1})
    Invoke-AzureRmResourceAction -Action failoverPriorityChange -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName "rg-test" -Name "docdb-test" -Parameters @{"failoverPolicies"=$failoverPolicies}

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[powershell-install-configure]: https://docs.microsoft.com/en-us/azure/powershell-install-configure
[scaling-globally]: https://azure.microsoft.com/en-us/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet
[distribute-data-globally]: https://docs.microsoft.com/en-us/azure/documentdb/documentdb-distribute-data-globally
[azure-resource-groups]: https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/en-us/rest/api/documentdbresourceprovider/


<!--HONumber=Nov16_HO5-->


