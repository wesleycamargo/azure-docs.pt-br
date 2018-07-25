---
title: Estrutura de definição da Política do Azure
description: Descreve como a definição de diretiva de recurso é usada pela Política do Azure para estabelecer convenções para recursos em sua organização, descrevendo quando a diretiva é aplicada e qual efeito tomar.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 7f01464c4b9063f20a83c3626d7f92a5e0524f7a
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989118"
---
# <a name="azure-policy-definition-structure"></a>Estrutura de definição da Política do Azure

Definição de política de recursos usada pelo Azure Policy permite que você estabeleça convenções para recursos em sua organização descrevendo quando a política é aplicada e a ação a realizar. Definindo as convenções, você pode controlar os custos e muito mais fácil gerenciar seus recursos. Por exemplo, você pode especificar que somente determinados tipos de máquinas virtuais são permitidos. Ou você pode exigir que todos os recursos tenham uma marca específica. As políticas são herdadas por todos os recursos filho. Então, se uma política for aplicada a um grupo de recursos, ela será aplicável a todos os recursos desse grupo de recursos.

O esquema usado pela política do Azure pode ser encontrado aqui: [https://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json](https://schema.management.azure.com/schemas/2016-12-01/policyDefinition.json)

Você usa JSON para criar uma definição de política. A definição de política contém elementos para:

- modo
- parâmetros
- nome de exibição
- descrição
- regra de política
  - avaliação de lógica
  - efeito

Por exemplo, o JSON a seguir mostra uma política que limita os locais em que os recursos são implantados:

```json
{
    "properties": {
        "mode": "all",
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                }
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

Todos os exemplos do Azure Policy estão em [Exemplos de política](json-samples.md).

## <a name="mode"></a>Mode

O **modo** determina quais tipos de recursos serão avaliados para uma política. Os modos suportados são:

- `all`: avaliar grupos de recursos e todos os tipos de recursos
- `indexed`: avaliar apenas os tipos de recursos que oferecem suporte a marcas e local

É recomendável definir o **modo** como `all` na maioria dos casos. Todas as definições de políticas criadas através do portal usam o modo `all`. Se você usar a CLI do Azure ou PowerShell, será necessário especificar o modo **parâmetro** manualmente. Se a definição de política não contiver um valor **no modo** o padrão será `all` no Azure PowerShell e `null` na CLI do Azure, o que equivale a `indexed`, para compatibilidade com versões anteriores.

`indexed` deve ser usado ao criar políticas que irão impor marcas ou locais. Isso não é obrigatório, mas impedirá que recursos que não oferecem suporte a marcas nem locais apareçam como não compatíveis nos resultados de conformidade. A única exceção a isso são **grupos de recursos**. As políticas que estão tentando impor local ou marcas em um grupo de recursos devem definir **mode** como `all` e direcionar especificamente o tipo `Microsoft.Resources/subscriptions/resourceGroup`. Para obter um exemplo, consulte [Impor marcas do grupo de recursos](scripts/enforce-tag-rg.md).

## <a name="parameters"></a>parâmetros

Parâmetros ajudam a simplificar o gerenciamento de política, reduzindo o número de definições de política. Pense em parâmetros como os campos em um formulário – `name`, `address`, `city`, `state`. Esses parâmetros sempre permanecem iguais, porém, seus valores mudam com base no preenchimento individual do formulário. Os parâmetros funcionam da mesma maneira que ao criar políticas. Ao incluir parâmetros em uma definição de política, você pode reutilizar essa política para diferentes cenários usando valores diferentes.

Por exemplo, você pode definir uma política para uma propriedade de recurso para limitar os locais em que os recursos podem ser implantados. Nesse caso, você declararia os seguintes parâmetros ao criar sua política:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        }
    }
}
```

O tipo de um parâmetro pode ser cadeia de caracteres ou matriz. A propriedade de metadados é usada para ferramentas como o portal do Azure exibirem informações fáceis e simples ao usuário.

Na propriedade de metadados, você pode usar **strongType**  para fornecer uma lista de opções de seleção múltipla no portal do Azure.  Os valores atualmente permitidos para **strongType** incluem:

- `"location"`
- `"resourceTypes"`
- `"storageSkus"`
- `"vmSKUs"`
- `"existingResourceGroups"`
- `"omsWorkspace"`

Na regra de política, você fazer referência a parâmetros com a seguinte sintaxe:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="definition-location"></a>Local da definição

Ao criar uma definição de política ou iniciativa, é importante que você especifique o local da definição.

O local da definição determina o escopo ao qual a definição de política ou iniciativa pode ser atribuída a. O local pode ser especificado como um grupo de gerenciamento ou uma assinatura.

> [!NOTE]
> Se você pretende aplicar essa definição de política a várias assinaturas, o local deve ser um grupo de gerenciamento que contenha as assinaturas para as quais você atribuirá a iniciativa ou a política.

## <a name="display-name-and-description"></a>Nome de exibição e descrição

Você pode usar **displayName** e **description** para identificar a definição de política e fornecer contexto de quando ela é usado.

## <a name="policy-rule"></a>Regra de política

A regra de política consiste em **se** e **, em seguida,** blocos. No **se** bloco, você define uma ou mais condições que especificam quando a política é aplicada. Você pode aplicar os operadores lógicos para essas condições para definir exatamente o cenário para uma política.

No **, em seguida,** bloco, você define o efeito que acontecerá quando o **se** condições sejam atendidas.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists"
    }
}
```

### <a name="logical-operators"></a>Operadores lógicos

Os operadores lógicos compatíveis são:

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

O **não** sintaxe inverte o resultado da condição. A sintaxe de **allOf** (semelhante à operação **E** lógica) requer que todas as condições sejam verdadeiras. A sintaxe de **anyOf** (semelhante à operação **Ou** lógica) requer que uma ou mais condições sejam verdadeiras.

Você pode aninhar operadores lógicos. A exemplo a seguir mostra uma operação **not** operação é aninhada dentro de uma operação **allOf**.

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>Condições

Uma condição avalia se um **campo** atende a determinados critérios. As condições com suporte são:

- `"equals": "value"`
- `"notEquals": "value"`
- `"like": "value"`
- `"notLike": "value"`
- `"match": "value"`
- `"notMatch": "value"`
- `"contains": "value"`
- `"notContains": "value"`
- `"in": ["value1","value2"]`
- `"notIn": ["value1","value2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"exists": "bool"`

Ao usar as condições **like** e **notLike**, você pode fornecer um curinga (`*`) no valor.
O valor não deve conter mais do que um caractere curinga (`*`).

Ao usar as condições **match** e **notMatch**, forneça `#` para representar um dígito, `?` para uma letra e outro caractere para representar o caractere real. Para exemplos, consulte [Permitir vários padrões de nome](scripts/allow-multiple-name-patterns.md).

### <a name="fields"></a>Campos

As condições são formadas usando campos. Um campo representa as propriedades na carga de solicitação de recurso que é usada para descrever o estado do recurso.  

Há suporte para os seguintes campos:

- `name`
- `fullName`
  - Retorna o nome completo do recurso. O nome completo de um recurso é o nome do recurso precedido dos nomes dos recursos pai (por exemplo, "myServer/myDatabase").
- `kind`
- `type`
- `location`
- `tags`
- `tags.<tagName>`
  - Em que **\<tagName\>** é o nome da marca para a qual validar a condição.
  - Exemplo: `tags.CostCenter` em que **CostCenter** é o nome da marca.
- `tags[<tagName>]`
  - Essa sintaxe de colchete é compatível com nomes de marca que contêm pontos.
  - Em que **\<tagName\>** é o nome da marca para a qual validar a condição.
  - Exemplo: `tags.[Acct.CostCenter]` em que **Acct.CostCenter** é o nome da marca.
- aliases de propriedade - para obter uma lista, confira [Aliases](#aliases).

### <a name="alternative-accessors"></a>Acessadores alternativos

**Campo** é o acessador primário usado nas regras de política. Ele inspeciona diretamente o recurso que está sendo avaliado. No entanto, a política dá suporte a outro acessador, **fonte**.

```json
"source": "action",
"equals": "Microsoft.Compute/virtualMachines/write"
```

**Fonte** só oferece suporte a um valor, **ação**. Ação retorna a ação de autorização da solicitação que está sendo avaliada. Ações de autorização são expostas na seção de autorização de [Log de atividades](../monitoring-and-diagnostics/monitoring-activity-log-schema.md).

Quando a política está avaliando os recursos existentes no plano de fundo, ela define **ação** para uma ação de autorização `/write` no tipo do recurso.

### <a name="effect"></a>Efeito

A política é compatível com os seguintes tipos de efeito:

- **Negar**: gera um evento no log de auditoria e causa uma falha da solicitação
- **Auditar**: gera um evento de aviso no log de auditoria, mas não causa falha da solicitação
- **Acrescentar**: adiciona o conjunto de campos definido à solicitação
- **AuditIfNotExists**: habilitará a auditoria se um recurso não existir
- **DeployIfNotExists**: implantará um recurso se ele ainda não existir. Atualmente, esse efeito tem suporte apenas por meio de políticas internas.

Para **acrescentar**, você precisa fornecer os detalhes abaixo:

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

O valor pode ser uma cadeia de caracteres ou um objeto no formato JSON.

Com **AuditIfNotExists** e **DeployIfNotExists**, você pode avaliar a existência de um recurso relacionado e aplicar uma regra e um efeito correspondente quando esse recurso não existir. Por exemplo, você pode exigir que um observador de rede seja implantado para todas as redes virtuais.
Para obter um exemplo de como fazer auditoria quando uma extensão da máquina virtual não está implantada, consulte [Auditoria se não existir extensão](scripts/audit-ext-not-exist.md).

Para obter detalhes completos sobre cada efeito, ordem de avaliação, propriedades e exemplos, confira [Compreendendo os efeitos da Política](policy-effects.md).

## <a name="aliases"></a>Aliases

Você pode usar aliases de propriedade para acessar propriedades específicas para um tipo de recurso. Os aliases permitem restringir quais valores ou condições são permitidas para uma propriedade em um recurso. Cada alias é mapeado para caminhos em diferentes versões de API para um tipo de recurso específico. Durante a avaliação de política, o mecanismo de políticas obtém o caminho de propriedade para essa versão de API.

A lista de aliases sempre está aumentando. Para descobrir quais aliases são atualmente suportados pela política do Azure, use um dos seguintes métodos:

- Azure PowerShell

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

  # Invoke the REST API
  $response = Invoke-RestMethod -Uri 'https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases' -Method Get -Headers $authHeader

  # Create an Array List to hold discovered aliases
  $aliases = New-Object System.Collections.ArrayList

  foreach ($ns in $response.value) {
      foreach ($rT in $ns.resourceTypes) {
          if ($rT.aliases) {
              foreach ($obj in $rT.aliases) {
                  $alias = [PSCustomObject]@{
                      Namespace       = $ns.namespace
                      resourceType    = $rT.resourceType
                      alias           = $obj.name
                  }
                  $aliases.Add($alias) | Out-Null
              }
          }
      }
  }

  # Output the list, sort, and format. You can customize with Where-Object to limit as desired.
  $aliases | Sort-Object -Property Namespace, resourceType, alias | Format-Table
  ```

- CLI do Azure

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # Get Azure Policy aliases for a specific Namespace
  az provider show --namespace Microsoft.Automation --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- API REST / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

## <a name="initiatives"></a>Iniciativas

Iniciativas permitem que você agrupe várias definições de políticas relacionadas para simplificar atribuições e gerenciamento, pois você trabalha com um grupo como um único item. Por exemplo, você pode agrupar todas as definições de política de marcação relacionadas em uma única iniciativa. Em vez de atribuir cada política individualmente, você aplica a iniciativa.

O exemplo a seguir ilustra como criar uma iniciativa para lidar com duas marcas: `costCenter` e `productName`. Ele usa duas políticas internas para aplicar o valor da marca padrão.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                }
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                }
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    },
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicy",
    "type": "Microsoft.Authorization/policySetDefinitions",
    "name": "billingTagsPolicy"
}
```

## <a name="next-steps"></a>Próximas etapas

- Revisar mais exemplos em [exemplos do Azure Policy](json-samples.md).
