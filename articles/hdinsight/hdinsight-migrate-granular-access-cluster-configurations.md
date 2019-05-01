---
title: Migrar para o acesso granular com base em função para configurações de cluster - HDInsight do Azure
description: Saiba mais sobre as alterações necessárias como parte da migração para o acesso granular com base em função para configurações de cluster do HDInsight.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/26/2019
ms.openlocfilehash: 8bcb20ec5c85c3cfa2e481a4a5848f404a2fb4eb
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64685452"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrar para acesso baseado em função granular para configurações de cluster

Estamos introduzindo algumas alterações importantes para dar suporte a mais acesso refinado baseado em função para obter informações confidenciais. Como parte dessas alterações, alguns **ação pode ser necessária** se você estiver usando um dos [afetados entidades/cenários](#am-i-affected-by-these-changes).

## <a name="what-is-changing"></a>O que está mudando?

Anteriormente, os segredos pôde ser obtidos por meio da API do HDInsight por usuários de cluster que possui o proprietário, colaborador ou leitor [funções RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles).
No futuro, esses segredos não estarão acessíveis aos usuários com a função de leitor. Segredos são definidos como valores que poderiam ser usados para obter acesso mais elevado do que uma função de usuário devem permitir. Eles incluem valores como credenciais HTTP de gateway do cluster, as chaves de conta de armazenamento e as credenciais de banco de dados.

Também estamos introduzindo uma nova [operador de Cluster HDInisght](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) função que será capaz de recuperar segredos sem receber permissões administrativas do Colaborador ou proprietário. Resumidamente:

| Função                                  | Anteriormente                                                                                       | No futuro       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Leitor                                | -Acesso de leitura, incluindo segredos                                                                   | -Acesso de leitura, **excluindo** segredos |           |   |   |
| Operador de Cluster do HDInsight<br>(Nova função) | N/D                                                                                              | -Acesso de leitura/gravação, incluindo segredos         |   |   |
| Colaborador                           | -Acesso de leitura/gravação, incluindo segredos<br>-Criar e gerenciar todos os tipos de recursos do Azure.     | Sem alteração |
| Proprietário                                 | -Acesso de leitura/gravação incluindo segredos<br>-Total acesso a todos os recursos<br>-Delegar acesso a outras pessoas | Sem alteração |

Para obter informações sobre como adicionar a atribuição de função de operador do Cluster HDInsight a um usuário para conceder a eles acesso de leitura/gravação para os segredos de cluster, consulte a seção abaixo, [adicionar a atribuição de função de operador do Cluster HDInsight a um usuário](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Estou afetado por essas alterações?

As entidades e os cenários a seguir são afetados:

- [API](#api): Os usuários usando o `/configurations` ou `/configurations/{configurationName}` pontos de extremidade.
- [Ferramentas de HDInsight do Azure para Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) versão 1.1.1 ou abaixo.
- [Kit de ferramentas do Azure para IntelliJ](#azure-toolkit-for-intellij) versão 3.20.0 ou abaixo.
- [SDK para .NET](#sdk-for-net)
    - [as versões 1.x ou 2.x](#versions-1x-and-2x): Os usuários usando o `GetClusterConfigurations`, `GetConnectivitySettings`, `ConfigureHttpSettings`, `EnableHttp` ou `DisableHttp` métodos da classe ConfigurationsOperationsExtensions.
    - [versões 3.x e superior](#versions-3x-and-up): Usuários usando o `Get`, `Update`, `EnableHttp`, ou `DisableHttp` métodos a partir de `ConfigurationsOperationsExtensions` classe.
- [SDK para Python](#sdk-for-python): Usuários usando o `get` ou `update` métodos a partir de `ConfigurationsOperations` classe.
- [SDK para Java](#sdk-for-java): Usuários usando o `update` ou `get` métodos a partir de `ConfigurationsInner` classe.
- [SDK para linguagem Go](#sdk-for-go): Os usuários usando o `Get` ou `Update` métodos do `ConfigurationsClient` struct.

Veja as seções abaixo (ou use os links acima) para ver as etapas de migração para seu cenário.

### <a name="api"></a>API

As seguintes APIs serão alteradas ou substituídas:

- [**GET /configurations/ {configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (informações confidenciais removidas)
    - Anteriormente, usado para obter tipos de configuração individuais (incluindo segredos).
    - Essa chamada à API agora irá retornar tipos de configuração individuais com segredos omitidos. Para obter todas as configurações, incluindo segredos, use a nova chamada /configurations POST. Para obter apenas as configurações de gateway, use a nova chamada /getGatewaySettings POST.
- [**GET /configurations** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configurations) (preterido)
    - Anteriormente usado para obter todas as configurações (incluindo segredos)
    - Essa chamada à API não terá suporte. Para obter todas as configurações no futuro, use a nova chamada /configurations POST. Para obter as configurações com parâmetros confidenciais omitidos, use chamada GET /configurations/ {configurationName}.
- [**POST /configurations/{configurationName}**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#change-connectivity-settings) (deprecated)
    - Anteriormente, usado para atualizar as credenciais de gateway.
    - Essa chamada à API será preterida e não há mais suporte. Use o novo /updateGatewaySettings POST em vez disso.

A substituição seguinte que APIs foram adicionadas:</span>

- [**POST /configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Use essa API para obter todas as configurações, incluindo segredos.
- [**POST /getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Use essa API para obter as configurações de gateway.
- [**POST /updateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Use essa API para atualizar as configurações de gateway (nome de usuário e/ou senha).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Ferramentas de HDInsight do Azure para Visual Studio Code

Se você estiver usando a versão 1.1.1 ou abaixo, atualize para o [a versão mais recente das ferramentas de HDInsight do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) para evitar interrupções.

### <a name="azure-toolkit-for-intellij"></a>Kit de Ferramentas do Azure para IntelliJ

Se você estiver usando a versão 3.20.0 ou abaixo, atualize para o [a versão mais recente do Kit de ferramentas do Azure para o plug-in IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) para evitar interrupções.

### <a name="sdk-for-net"></a>SDK para .NET

#### <a name="versions-1x-and-2x"></a>As versões 1.x e 2.x

Atualizar para [versão 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) do SDK do HDInsight para .NET. Modificações mínimas de código podem ser necessárias se você estiver usando um método afetado por essas alterações:

- `ClusterOperationsExtensions.GetClusterConfigurations` serão **não retornar mais parâmetros confidenciais** como chaves de armazenamento (core-site) ou credenciais HTTP (gateway).
    - Para recuperar todas as configurações, incluindo parâmetros confidenciais, use `ClusterOperationsExtensions.ListConfigurations` no futuro.  Observe que os usuários com a função 'Reader' não será capazes de usar esse método. Isso permite que um controle granular sobre quais usuários podem acessar informações confidenciais para um cluster.
    - Para recuperar apenas as credenciais de gateway HTTP, use `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.GetConnectivitySettings` Agora é preterido e foi substituído pelo `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.ConfigureHttpSettings` Agora é preterido e foi substituído pelo `ClusterOperationsExtensions.UpdateGatewaySettings`.

- `ConfigurationsOperationsExtensions.EnableHttp` e `DisableHttp` agora são preteridos. HTTP está sempre habilitada, portanto, esses métodos não são mais necessários.

#### <a name="versions-3x-and-up"></a>Versões 3.x e superior

Atualizar para [versão 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) ou posterior do SDK do HDInsight para .NET. Modificações mínimas de código podem ser necessárias se você estiver usando um método afetado por essas alterações:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) serão **não retornar mais parâmetros confidenciais** como chaves de armazenamento (core-site) ou credenciais HTTP (gateway).
    - Para recuperar todas as configurações, incluindo parâmetros confidenciais, use [ `ConfigurationOperationsExtensions.List` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) no futuro.  Observe que os usuários com a função 'Reader' não será capazes de usar esse método. Isso permite que um controle granular sobre quais usuários podem acessar informações confidenciais para um cluster. 
    - Para recuperar apenas as credenciais de gateway HTTP, use [ `ClusterOperationsExtensions.GetGatewaySettings` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) Agora é preterido e foi substituído por [ `ClusterOperationsExtensions.UpdateGatewaySettings` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) e [ `DisableHttp` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) agora são preteridos. HTTP está sempre habilitada, portanto, esses métodos não são mais necessários.

### <a name="sdk-for-python"></a>SDK para Python

Atualizar para [versão 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) ou posterior do SDK do HDInsight para Python. Modificações mínimas de código podem ser necessárias se você estiver usando um método afetado por essas alterações:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) serão **não retornar mais parâmetros confidenciais** como chaves de armazenamento (core-site) ou credenciais HTTP (gateway).
    - Para recuperar todas as configurações, incluindo parâmetros confidenciais, use [ `ConfigurationsOperations.list` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurations_operations.configurationsoperations?view=azure-python#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) no futuro.  Observe que os usuários com a função 'Reader' não será capazes de usar esse método. Isso permite que um controle granular sobre quais usuários podem acessar informações confidenciais para um cluster. 
    - Para recuperar apenas as credenciais de gateway HTTP, use [ `ConfigurationsOperations.get_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-).
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-resource-group-name--cluster-name--tags-none--custom-headers-none--raw-false----operation-config-) Agora é preterido e foi substituído por [ `ClusterOperationsExtensions.update_gateway_settings` ](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clusters_operations.clustersoperations?view=azure-python#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-).

### <a name="sdk-for-java"></a>SDK para Java

Atualizar para [versão 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/) ou posterior do SDK do HDInsight para Java. Modificações mínimas de código podem ser necessárias se você estiver usando um método afetado por essas alterações:

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get) serão **não retornar mais parâmetros confidenciais** como chaves de armazenamento (core-site) ou credenciais HTTP (gateway).
    - Para recuperar todas as configurações, incluindo parâmetros confidenciais, use [ `ConfigurationsInner.list` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.configurationsinner.list?view=azure-java-stable) no futuro.  Observe que os usuários com a função 'Reader' não será capazes de usar esse método. Isso permite que um controle granular sobre quais usuários podem acessar informações confidenciais para um cluster. 
    - Para recuperar apenas as credenciais de gateway HTTP, use [ `ConfigurationsOperations.get_gateway_settings` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.getgatewaysettings?view=azure-java-stable).
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update) Agora é preterido e foi substituído por [ `ClusterOperationsExtensions.update_gateway_settings` ](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.updategatewaysettings?view=azure-java-stable).

### <a name="sdk-for-go"></a>SDK para linguagem Go

Atualizar para [versão 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) ou posterior do SDK do HDInsight para linguagem Go. Modificações mínimas de código podem ser necessárias se você estiver usando um método afetado por essas alterações:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get) serão **não retornar mais parâmetros confidenciais** como chaves de armazenamento (core-site) ou credenciais HTTP (gateway).
    - Para recuperar todas as configurações, incluindo parâmetros confidenciais, use [ `ConfigurationsClient.list` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) no futuro.  Observe que os usuários com a função 'Reader' não será capazes de usar esse método. Isso permite que um controle granular sobre quais usuários podem acessar informações confidenciais para um cluster. 
    - Para recuperar apenas as credenciais de gateway HTTP, use [ `ClustersClient.get_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings).
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update) Agora é preterido e foi substituído por [ `ClustersClient.update_gateway_settings` ](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings).

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Adicionar a atribuição de função de operador do Cluster HDInsight a um usuário

Um usuário com o [Colaborador](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) ou [proprietário](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) função pode atribuir a [operador do Cluster HDInisght](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) função aos usuários que você gostaria de ter acesso de leitura/gravação para minúsculas Valores de configuração do cluster HDInsight (como credenciais de gateway do cluster e as chaves de conta de armazenamento).

### <a name="using-the-azure-cli"></a>Usando a CLI do Azure

A maneira mais simples de adicionar essa atribuição de função é usando o `az role assignemnt create` comando na CLI do Azure.

> [!NOTE]
> Este comando deve ser executado por um usuário com as funções de Colaborador ou proprietário, como só podem conceder essas permissões. O `--assignee` é o endereço de email do usuário a quem você deseja atribuir a função de operador do Cluster HDInsight.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Conceder a função no nível de recursos (cluster)

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Conceder a função no nível do grupo de recursos

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Conceder a função no nível da assinatura

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Usando o portal do Azure

Como alternativa, você pode usar o portal do Azure para adicionar a atribuição de função de operador do Cluster HDInsight a um usuário. Consulte a documentação [gerenciar o acesso aos recursos do Azure usando RBAC e o portal do Azure - adicionar uma atribuição de função](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).
