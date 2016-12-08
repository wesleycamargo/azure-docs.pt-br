---
title: Passo a passo da API REST de monitoramento do Azure | Microsoft Docs
description: "Como autenticar solicitações e usar a API REST de monitoramento do Azure."
author: mcollier
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 565e6a88-3131-4a48-8b82-3effc9a3d5c6
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: mcollier
translationtype: Human Translation
ms.sourcegitcommit: 830eb6627cae71f358b9790791b1d86f7c82c566
ms.openlocfilehash: 81564726dd03dbbb93629ebd296e288290d37778


---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Passo a passo da API REST de monitoramento do Azure
Este artigo mostra como executar autenticação para que o seu código possa usar a [referência de API REST do Monitor do Microsoft Azure](https://msdn.microsoft.com/library/azure/dn931943.aspx).         

A API do Azure Monitor possibilita recuperar programaticamente as definições de métricas padrões disponíveis (o tipo de métrica como, por exemplo, Tempo de CPU, Solicitações, etc.), granularidade e valores de métrica. Depois de recuperados, os dados podem ser salvos em um armazenamento de dados separados, como, por exemplo, Banco de Dados SQL do Azure, DocumentDB ou Azure Data Lake. A partir daí análises adicionais podem ser executadas conforme necessário.

Além de trabalhar com vários pontos de dados de métrica, como mostra este artigo, a API do Monitor possibilita listar as regras de alerta, exibir logs de atividade e muito mais. Para obter uma lista completa das operações disponíveis, consulte a [referência da API REST do Monitor do Microsoft Azure](https://msdn.microsoft.com/library/azure/dn931943.aspx).

## <a name="authenticating-azure-monitor-requests"></a>Autenticação de solicitações do Azure Monitor
A primeira etapa é autenticar a solicitação.

Todas as tarefas executadas em relação à API do Azure Monitor usam o modelo de autenticação do Azure Resource Manager. Portanto, todas as solicitações devem ser autenticadas com o Azure Active Directory (Azure AD). Uma abordagem para autenticar o aplicativo cliente é criar uma entidade de serviço do Azure AD e recuperar o token de autenticação (JWT). O script de exemplo a seguir demonstra como criar uma entidade de serviço do Azure AD por meio do PowerShell. Para obter instruções mais detalhadas, consulte a documentação sobre como [usar o Azure PowerShell para criar uma entidade de serviço para acessar recursos](../resource-group-authenticate-service-principal.md#create-service-principal-with-password). Também é possível [criar uma entidade de serviço por meio do portal do Azure](../resource-group-create-service-principal-portal.md).

```PowerShell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"
$location = "centralus"

# Authenticate to a specific Azure subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"

# Create a new Azure AD application
$azureAdApplication = New-AzureRmADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $pwd

# Create a new service principal associated with the designated application
New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzureRmRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Para consultar a API do Azure Monitor, o aplicativo cliente deve usar a entidade de serviço criada anteriormente para a autenticação. O script do PowerShell do exemplo a seguir mostra uma abordagem, usando a [Biblioteca de Autenticação do Active Directory](../active-directory/active-directory-authentication-libraries.md) (ADAL) para ajudar a obter o token de autenticação JWT. O token JWT é passado como parte de um parâmetro de autorização HTTP em solicitações para a API REST do Azure Monitor.

```PowerShell
$azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.windows.net/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)

$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)

# Build an array of HTTP header values
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

Após a conclusão da etapa de configuração de autenticação, as consultas podem ser executadas em relação à API REST do Azure Monitor. Há duas consultas úteis:

1. Listar as definições de métricas para um recurso
2. Recuperar os valores de métrica

## <a name="retrieve-metric-definitions"></a>Recuperar as definições de métrica
> [!NOTE]
> Para recuperar as definições de métricas usando a API REST do Azure Monitor, use "2016-03-01" como a versão de API.
>
>

```PowerShell
$apiVersion = "2016-03-01"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metricDefinitions?api-version=${apiVersion}"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -Verbose
```
Para um aplicativo lógico do Azure, as definições de métricas seriam semelhantes à seguinte tela:

![Alt "Visão JSON da resposta da definição da métrica."](./media/monitoring-rest-api-walkthrough/available_metric_definitions_logic_app_json_response_clean.png)

Para obter mais informações, consulte a documentação [Listar as definições de métricas para um recurso na API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/mt743621.aspx) .

## <a name="retrieve-metric-values"></a>Recuperar valores de métrica
Depois que as definições de métrica disponíveis são conhecidas, é possível recuperar os valores de métrica relacionados. Use o “valor” do nome da métrica (não o “valor localizado”) para todas as solicitações de filtragem (por exemplo, recuperar os pontos de dados da métrica “Tempo de CPU” e “Solicitações”). Se nenhum filtro for especificado, a métrica padrão será retornada.

> [!NOTE]
> Para recuperar os valores de métrica usando a API REST do Azure Monitor, use "2016-06-01" como a versão de API.
>
>

**Método**: GET

**URI de solicitação**: https://management.azure.com/subscriptions/*{id-da-assinatura}*/resourceGroups/*{nome-do-grupo-de-recursos}*/providers/*{namespace-do-provedor-recursos}*/*{tipo-de-recurso}*/*{nome-do-recurso}*/providers/microsoft.insights/metrics?$filter=*{filtro}*&api-version=*{versão-da-api}*

Por exemplo, para recuperar os pontos de dados da métrica RunsSucceeded para o intervalo de tempo determinado e para um intervalo de agregação de 1 hora, a solicitação seria a seguinte:

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2016-09-23 and endTime eq 2016-09-24 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

O resultado deve ser semelhante ao exemplo da tela a seguir:

![Alt "Resposta JSON mostrando o valor da métrica Tempo Médio de Resposta"](./media/monitoring-rest-api-walkthrough/available_metrics_logic_app_json_response.png)

Para recuperar vários pontos de dados ou de agregação, adicione os nomes de definição da métrica e os tipos de agregação para o filtro, conforme mostrado no exemplo a seguir:

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2016-09-23T13:30:00Z and endTime eq 2016-09-23T14:30:00Z and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

### <a name="use-armclient"></a>Use o ARMClient
Uma alternativa ao uso do PowerShell (conforme mostrado acima), é usar [ARMClient](https://github.com/projectkudu/ARMClient) no seu computador Windows. O ARMClient cuida da autenticação do Azure AD (e do token JWT resultante) automaticamente. As etapas a seguir descrevem o uso do ARMClient para recuperar dados de métrica:

1. Instalar [Chocolatey](https://chocolatey.org/) e [ARMClient](https://github.com/projectkudu/ARMClient).
2. Em uma janela de terminal, digite *armclient.exe login*. Você é solicitado a fazer logon no Azure.
3. Digite *armclient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01*
4. Digite *armclient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-06-01*

![Alt "Usar o ARMClient para trabalhar com a API REST de monitoramento do Azure"](./media/monitoring-rest-api-walkthrough/armclient_metricdefinitions.png)

## <a name="retrieve-the-resource-id"></a>Recuperar a ID do recurso
Usar a API REST realmente pode ajudar a entender as definições de métrica disponíveis, granularidade e valores relacionados. Essas informações são úteis ao usar a [Biblioteca de Gerenciamento do Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).

No código anterior, a ID do recurso a ser usada é o caminho completo para o recurso do Azure desejado. Por exemplo, para consultar um aplicativo Web do Azure, a ID do recurso seria:

*/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

A lista a seguir contém alguns exemplos de formatos de ID do recurso para vários recursos do Azure:

* **Hub IoT** - /subscriptions/*{id-da-assinatura}*/resourceGroups/*{nome-do-grupo-de-recursos}*/providers/Microsoft.Devices/IotHubs/*{nome-do-hub-iot}*
* **Pool SQL Elástico** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{pool-db}*/elasticpools/*{sql-pool-name}*
* **Banco de Dados SQL (v12)** - /subscriptions/*{id-da-assinatura}*/resourceGroups/*{nome-do-grupo-de-recursos}*/providers/Microsoft.Sql/servers/*{nome-do-servidor}*/databases/*{nome-do-banco-de-dados}*
* **Barramento de Serviço** - /subscriptions/*{id-da-assinatura}*/resourceGroups/*{nome-do-grupo-de-recursos}*/providers/Microsoft.ServiceBus/*{namespace}*/*{servicebus-name}*
* **Conjuntos de Dimensionamento da VM** - /subscriptions/*{id-da-assinatura}*/resourceGroups/*{nome-do-grupo-de-recursos}*/providers/Microsoft.Compute/virtualMachineScaleSets/*{vm-name}*
* **VMs** - /subscriptions/*{id-da-assinatura}*/resourceGroups/*{nome-do-grupo-de-recursos}*/providers/Microsoft.Compute/virtualMachines/*{vm-name}*
* **Hubs de Eventos** - /subscriptions/*{id-da-assinatura}*/resourceGroups/*{nome-do-grupo-de-recursos}*/providers/Microsoft.EventHub/namespaces/*{eventhub-namespace}*

Há abordagens alternativas para recuperar a ID do recurso, incluindo o uso do Azure Resource Manager, exibindo o recurso desejado no portal do Azure e por meio do PowerShell ou da CLI do Azure.

### <a name="azure-resource-explorer"></a>Gerenciador de Recursos do Azure
Para localizar a ID do recurso para um recurso desejado, uma abordagem útil é usar a ferramenta [Azure Resource Manager](https://resources.azure.com) . Navegue até o recurso desejado e, em seguida, verifique a ID exibida, conforme a tela a seguir:

![Alt "Explorador de Recursos do Azure"](./media/monitoring-rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Portal do Azure
A ID do recurso também pode ser obtida no portal do Azure. Para fazer isso, navegue até o recurso desejado e, em seguida, selecione Propriedades. A ID do recurso é exibida na folha de Propriedades, como mostrado na tela a seguir:

![Alt "ID do recurso exibido na folha de Propriedades no portal do Azure"](./media/monitoring-rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell
A ID do recurso também pode ser recuperada usando cmdlets do Azure PowerShell. Por exemplo, para obter a ID do recurso para um aplicativo Web do Azure, execute o cmdlet Get-AzureRmWebApp, como na tela a seguir:

![Alt "ID do recurso obtido por meio do PowerShell"](./media\\monitoring-rest-api-walkthrough\\resourceid_powershell.png)

### <a name="azure-cli"></a>CLI do Azure
Para recuperar a ID do recurso usando a CLI do Azure, execute o comando “azure webapp show”, especificando a opção “-json”, conforme mostrado na captura de tela a seguir:

![Alt "ID do recurso obtido por meio do PowerShell"](./media\\monitoring-rest-api-walkthrough\\resourceid_azurecli.png)

## <a name="retrieve-activity-log-data"></a>Recuperar dados de Log de atividade
Além de trabalhar com definições de métricas e valores relacionados, também é possível recuperar informações interessantes adicionais relacionadas aos recursos do Azure. Por exemplo, é possível consultar os dados do [log de atividades](https://msdn.microsoft.com/library/azure/dn931934.aspx) . O exemplo a seguir demonstra como usar o API REST do Azure Monitor para consultar dados de log de atividade dentro de um intervalo de datas específico para uma assinatura do Azure:

```PowerShell
$apiVersion = "2014-04-01"
$filter = "eventTimestamp ge '2016-09-23' and eventTimestamp le '2016-09-24'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

## <a name="next-steps"></a>Próximas etapas
* Verifique a [Visão geral do monitoramento](monitoring-overview.md).
* Visualize as [Métricas compatíveis com o Azure Monitor](monitoring-supported-metrics.md).
* Verifique a [referência da API REST do Monitor do Microsoft Azure](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Verifique a [Biblioteca de Gerenciamento do Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).



<!--HONumber=Nov16_HO3-->


