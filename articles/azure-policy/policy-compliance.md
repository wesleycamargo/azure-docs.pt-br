---
title: Obter dados de conformidade no Azure Policy
description: Efeitos e avaliações do Azure Policy determinam a conformidade. Saiba como obter os detalhes de conformidade.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 390935d80e903631287b1a4b9f1075e547298d99
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39250109"
---
# <a name="getting-compliance-data"></a>Obter dados de conformidade

Os maiores benefícios do Azure Policy são o insight e os controles que ele fornece sobre os recursos em uma assinatura ou [grupo de gerenciamento](../azure-resource-manager/management-groups-overview.md) de assinaturas. Este controle pode ser desempenhado de várias maneiras diferentes, como impedindo que os recursos sejam criados no local errado, imposição do uso comum e consistente de marcas ou auditoria de recursos existentes para configurações apropriadas. Em todos os casos, os dados são gerados pelo Policy para que você entenda o estado de conformidade de seu ambiente.

Há várias maneiras de acessar as informações de conformidade geradas por sua política e iniciativas de atribuições:

- Usando o [Portal do Azure](#portal)
- Usando script de [linha de comando](#command_line)

Antes de examinar os métodos de relatório de conformidade, vamos ver quando as informações de conformidade são atualizadas, além da frequência e dos eventos que disparam um ciclo de avaliação.

## <a name="evaluation-triggers"></a>Gatilhos de avaliação

Os resultados de um ciclo de avaliação concluído são refletidos no Provedor de Recursos `Microsoft.PolicyInsights` por meio das operações `PolicyStates` e `PolicyEvents`. Para saber mais sobre as opções e recursos da API REST de Insights do Policy, consulte [Insights do Policy](/rest/api/policy-insights/).

As avaliações de políticas atribuídas e iniciativas ocorrem como resultado de diversos eventos:

- Uma política ou iniciativa foi recentemente atribuída a um escopo. Quando isso ocorrer, demora cerca de 30 minutos para que a atribuição seja aplicada ao escopo definido. Após a aplicação, o ciclo de avaliação começa para recursos dentro desse escopo na política ou iniciativa recentemente atribuída e, dependendo dos efeitos usados pela política ou iniciativa, os recursos serão marcados como em conformidade ou sem conformidade. Uma política ou iniciativa de grande porte avaliada com base em um grande escopo de recursos pode demorar, portanto, não há uma expectativa predefinida de conclusão do ciclo de avaliação. Após a conclusão, os resultados de conformidade atualizados estarão disponíveis no Portal e nos SDKs.
- Uma política ou iniciativa já atribuída a um escopo foi atualizada. O ciclo de avaliação e o tempo desse cenário são iguais aos de uma nova atribuição a um escopo.
- Um recurso é implantado em um escopo com uma atribuição por meio do Gerenciador de Recursos, REST, CLI do Azure ou do Azure PowerShell. Nesse cenário, o evento de efeito (anexar, auditar, negar, implantar) e as informações de status de conformidade ficam disponíveis no portal e nos SDKs cerca de 15 minutos mais tarde.
- Ciclo de avaliação de conformidade padrão. Uma vez a cada 24 horas, as atribuições são automaticamente reavaliadas. Uma política ou iniciativa de grande porte avaliada com base em um grande escopo de recursos pode demorar, portanto, não há uma expectativa predefinida de conclusão do ciclo de avaliação. Após a conclusão, os resultados de conformidade atualizados estarão disponíveis no Portal e nos SDKs.

## <a name="how-compliance-works"></a>Como funciona a conformidade

Em uma atribuição, um recurso não está em conformidade se ele não segue as regras de iniciativa ou política. A tabela a seguir mostra como os diferentes efeitos da política funcionam com a avaliação da condição para o estado de conformidade resultante:

| Estado do recurso | Efeito | Avaliação da política | Estado de conformidade |
| --- | --- | --- | --- |
| Exists | Negar, Auditoria, Acrescentar\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Sem conformidade |
| Exists | Negar, Auditoria, Acrescentar\*, DeployIfNotExist\*, AuditIfNotExist\* | Falso | Em conformidade |
| Novo | Auditoria, AuditIfNotExist\* | True | Sem conformidade |
| Novo | Auditoria, AuditIfNotExist\* | Falso | Em conformidade |

\* Os efeitos de Acrescentar, DeployIfNotExist e AuditIfNotExist exigem que a instrução IF seja TRUE.
Os efeitos também exigem que a condição de existência seja FALSE para não estar em conformidade. Quando TRUE, a condição IF dispara a avaliação da condição de existência para os recursos relacionados.

Para entender melhor como os recursos são sinalizados como sem conformidade, vamos usar o exemplo de atribuição de política criado anteriormente.

Por exemplo, suponha que você tenha um grupo de recursos – ContsoRG, com algumas contas de armazenamento (realçadas em vermelho) que são expostas para redes públicas.

![Contas de armazenamento expostas a redes públicas](media/policy-insights/resource-group01.png)

Neste exemplo, você precisa estar atento aos riscos de segurança. Agora que você criou uma atribuição de política, ela é avaliada em relação a todas as contas de armazenamento no grupo de recursos ContosoRG. Ela realiza auditoria nas três contas de armazenamento sem conformidade, consequentemente alterando seus estados para **sem conformidade**.

![Auditoria de contas de armazenamento sem conformidade](media/policy-insights/resource-group03.png)

## <a name="portal"></a>Portal

O Portal do Azure apresenta uma experiência gráfica de visualização e compreensão do estado de conformidade em seu ambiente. Na página **Política**, a opção **Visão Geral** fornece detalhes dos escopos disponíveis sobre a conformidade das políticas e iniciativas. Além do estado de conformidade e da contagem por atribuição, ela contém um gráfico mostrando a conformidade nos últimos sete dias. A página **Conformidade** contém muitas dessas mesmas informações (exceto o gráfico), mas fornece opções adicionais de filtragem e classificação.

![Página de Conformidade da política](media/policy-compliance/compliance-page.png)

Como uma política ou iniciativa pode ser atribuída a escopos diferentes, observe na tabela o escopo de cada atribuição e o tipo de definição que foi atribuído a esse escopo. O número de políticas e recursos sem conformidade de cada atribuição também é fornecido. Clicar em uma política ou iniciativa na tabela fornece uma análise mais profunda sobre a conformidade dessa atribuição específica.

![Detalhes da conformidade com a política](media/policy-compliance/compliance-details.png)

Embora a lista de recursos na guia **Recursos sem conformidade** reflita o status de avaliação dos recursos existentes para a atribuição atual, os eventos (anexar, auditar, negar, implantar) disparados pela solicitação para criar um recurso são mostrados na guia **Eventos**.

![Eventos de conformidade da política](media/policy-compliance/compliance-events.png)

Clique com o botão direito na linha do evento para obter mais detalhes e selecione **Mostrar logs de atividade**. A página de registro de atividade é aberta e pré-filtrada na pesquisa, mostrando detalhes da atribuição e dos eventos. O log de atividades fornece um contexto adicional e informações sobre esses eventos.

![Log de atividades de conformidade de política](media/policy-compliance/compliance-activitylog.png)

## <a name="command-line"></a>Linha de Comando

As mesmas informações disponíveis no portal podem ser recuperadas usando diretamente a API REST (incluindo com [ARMClient](https://github.com/projectkudu/ARMClient)) ou o Azure PowerShell com a API REST. Para obter detalhes completos sobre a API REST, consulte a referência [Insights do Policy](/rest/api/policy-insights/). As páginas de referência da API REST têm um botão verde "Experimente" em cada operação, permitindo que você experimente diretamente no navegador.

Para usar os exemplos a seguir no Azure PowerShell, construa um token de autenticação com este exemplo de código. Em seguida, substitua o $restUri pela cadeia de caracteres desejada nos exemplos para recuperar um objeto JSON que pode ser analisado.

```azurepowershell-interactive
# Login first with Connect-AzureRmAccount if not using Cloud Shell

$azContext = Get-AzureRmContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Define the REST API to communicate with
# Use double quotes for $restUri as some endpoints take strings passed in single quotes
$restUri = "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04"

# Invoke the REST API
$response = Invoke-RestMethod -Uri $restUri -Method POST -Headers $authHeader

# View the response object (as JSON)
$response
```

### <a name="summarize-results"></a>Resumir resultados

Com a API REST, o resumo pode ser executado pelo grupo de gerenciamento, assinatura, grupo de recursos, recursos, iniciativa, política, atribuição de nível de assinatura ou atribuição de nível de grupo de recursos. Veja um exemplo de resumo no nível de assinatura usando [Resumir para Assinatura](/rest/api/policy-insights/policystates/summarizeforsubscription) do Insight do Policy:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

A saída resume a assinatura. No exemplo abaixo, a conformidade resumida está sob **value.results.nonCompliantResources** e **value.results.nonCompliantPolicies**. Essa solicitação fornece mais detalhes, incluindo cada atribuição que compõem os números sem conformidade e as informações de definição de cada atribuição. Cada objeto de política na hierarquia fornece um **queryResultsUri** que pode ser usado para obter detalhes adicionais nesse nível.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>Pesquisar recursos

Usando o exemplo acima, **value.policyAssignments.policyDefinitions.results.queryResultsUri** nos forneceu um URI de exemplo para obter todos os recursos sem conformidade de uma definição de política específica. Observando o valor **$filter**, IsCompliant é igual (eq) a false, PolicyAssignmentId é especificado para a definição de política e, em seguida, o próprio PolicyDefinitionId.
O motivo para incluir o PolicyAssignmentId no filtro é porque PolicyDefinitionId pode existir em várias atribuições ou iniciativas de política com diversos escopos. Ao especificar o PolicyAssignmentId e o PolicyDefinitionId, podemos ser explícitos com relação aos resultados que procuramos. Anteriormente, usamos **mais recente** para PolicyStates (o único valor permitido para **policyStatesSummaryResource** no operador de Resumir para Assinatura), que define automaticamente um período **de** e **para** das últimas 24 horas.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

O exemplo de resposta a seguir foi aparado a fim de mostrar apenas um único recurso sem conformidade, para fins de brevidade, (observe que @odata.count é realmente 15 e corresponde à contagem de recursos sem conformidade do exemplo acima). A resposta detalhada fornece várias partes de dados sobre o recurso, a política (ou iniciativa) e a atribuição. Observe que você também pode ver quais parâmetros de atribuição foram passados para a definição de política.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 15,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "timestamp": "2018-05-19T04:41:09Z",
        "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Compute/virtualMachines/linux",
        "policyAssignmentId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Authorization/policyAssignments/37ce239ae4304622914f0c77",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "effectiveParameters": "",
        "isCompliant": false,
        "subscriptionId": "{subscriptionId}",
        "resourceType": "/Microsoft.Compute/virtualMachines",
        "resourceLocation": "westus2",
        "resourceGroup": "RG-Tags",
        "resourceTags": "tbd",
        "policyAssignmentName": "37ce239ae4304622914f0c77",
        "policyAssignmentOwner": "tbd",
        "policyAssignmentParameters": "{\"tagName\":{\"value\":\"costCenter\"},\"tagValue\":{\"value\":\"Contoso-Test\"}}",
        "policyAssignmentScope": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags",
        "policyDefinitionName": "1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "policyDefinitionAction": "deny",
        "policyDefinitionCategory": "tbd",
        "policySetDefinitionId": "",
        "policySetDefinitionName": "",
        "policySetDefinitionOwner": "",
        "policySetDefinitionCategory": "",
        "policySetDefinitionParameters": "",
        "managementGroupIds": "",
        "policyDefinitionReferenceId": ""
    }]
}
```

### <a name="view-events"></a>Exibir eventos

Quando um recurso é criado ou atualizado, um resultado da avaliação da política é gerado. Os resultados são chamados _Eventos de Política_. Use o URI a seguir para exibir eventos recentes de política associados à assinatura.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2018-04-04
```

Seus resultados devem se parecer com o exemplo a seguir:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 16
    }]
}
```

Para obter mais informações sobre como consultar os eventos de política, consulte o artigo de referência de [Eventos de Política](/rest/api/policy-insights/policyevents).

### <a name="azure-powershell"></a>Azure PowerShell

O módulo do Azure PowerShell para a política está disponível na Galeria do PowerShell como [AzureRM.PolicyInsights](https://www.powershellgallery.com/packages/AzureRM.PolicyInsights). Usando o PowerShellGet, você pode instalar o módulo usando `Install-Module -Name AzureRM.PolicyInsights` (verifique se você tem a versão mais recente do [Azure PowerShell](/powershell/azure/install-azurerm-ps) instalada):

```powershell
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name AzureRM.PolicyInsights

# Import the downloaded module
Import-Module AzureRM.PolicyInsights

# Login with Connect-AzureRmAccount if not using Cloud Shell
Connect-AzureRmAccount
```

O módulo tem três cmdlets:

- `Get-AzureRmPolicyStateSummary`
- `Get-AzureRmPolicyState`
- `Get-AzureRmPolicyEvent`

Exemplo: obter o estado de resumo da política superior atribuída com o maior número de recursos sem conformidade.

```powershell
PS > Get-AzureRmPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Exemplo: obter o registro de estado para o recurso avaliada mais recentemente (o padrão é por data/hora em ordem decrescente).

```powershell
PS > Get-AzureRmPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/networkInterfaces
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Exemplo: obter os detalhes para todos os recursos de rede virtual sem conformidade.

```powershell
PS > Get-AzureRmPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Exemplo: obter eventos relacionados aos recursos de rede virtual sem conformidade que ocorreram após uma data específica.

```powershell
PS > Get-AzureRmPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2018-05-19'

Timestamp                  : 5/19/2018 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : eastus
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
TenantId                   : {tenantId}
PrincipalOid               : {principalOid}
```

O campo **PrincipalOid** pode ser usado para obter um usuário específico com o cmdlet do Azure PowerShell `Get-AzureRmADUser`. Substitua **{principalOid}** pela resposta obtida do exemplo anterior.

```powershell
PS > (Get-AzureRmADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="log-analytics"></a>Log Analytics

Se você tiver um espaço de trabalho do [Log Analytics](../log-analytics/log-analytics-overview.md) com a solução `AzureActivity` associada à sua assinatura, você também poderá exibir resultados sem conformidade do ciclo de avaliação usando consultas Kusto simples e a tabela `AzureActivity`. Com os detalhes sem conformidade no Log Analytics, isso também significa que os alertas podem ser configurados para ficarem atentos a falta de conformidade em um recurso específico, grupo de recursos ou até mesmo um limite de itens sem conformidade, por exemplo, mais de 10 nas últimas 24 horas.

![Conformidade de política usando o Log Analytics](media/policy-compliance/compliance-loganalytics.png)

## <a name="next-steps"></a>Próximas etapas

- Revisar a [Estrutura de definição de política](policy-definition.md).
- Revisar [Compreendendo os efeitos da política](policy-effects.md).
- Examine o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../azure-resource-manager/management-groups-overview.md)