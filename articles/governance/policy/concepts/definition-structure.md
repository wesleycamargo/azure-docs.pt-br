---
title: Detalhes da estrutura de definição de política
description: Descreve como a definição de diretiva de recurso é usada pela Política do Azure para estabelecer convenções para recursos em sua organização, descrevendo quando a diretiva é aplicada e qual efeito tomar.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/12/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: f1332e1622c34a33dd264a1115a0fd7f37ee8ba7
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53383962"
---
# <a name="azure-policy-definition-structure"></a>Estrutura de definição da Política do Azure

Definições de política de recurso são usadas pelo Azure Policy para estabelecer convenções para recursos. Cada definição descreve a conformidade do recurso e o efeito que ocorre quando um recurso não está em conformidade.
Definindo as convenções, você pode controlar os custos e muito mais fácil gerenciar seus recursos. Por exemplo, você pode especificar que somente determinados tipos de máquinas virtuais são permitidos. Ou você pode exigir que todos os recursos tenham uma marca específica. As políticas são herdadas por todos os recursos filho. Assim, se uma política for aplicada a um grupo de recursos, ela será aplicável a todos os recursos desse grupo de recursos.

O esquema usado pela política do Azure pode ser encontrado aqui: [https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json](https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json)

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

Todos os exemplos do Azure Policy estão em [Exemplos de política](../samples/index.md).

## <a name="mode"></a>Mode

O **modo** determina quais tipos de recursos serão avaliados para uma política. Os modos suportados são:

- `all`: avaliar grupos de recursos e todos os tipos de recursos
- `indexed`: avaliar apenas os tipos de recursos que oferecem suporte a marcas e local

É recomendável definir o **modo** como `all` na maioria dos casos. Todas as definições de políticas criadas através do portal usam o modo `all`. Se você usar a CLI do Azure ou PowerShell, será necessário especificar o modo **parâmetro** manualmente. Se a definição de política não incluir um valor **modo**, ela usará como padrão `all` no Azure PowerShell e `null` na CLI do Azure. Um modo `null` é o mesmo que usar `indexed` para dar suporte à compatibilidade com versões anteriores.

`indexed` deve ser usado ao criar políticas que vão impor marcas ou locais. Embora não seja obrigatório, impedirá que recursos que não oferecem suporte a marcas nem locais apareçam como não compatíveis nos resultados de conformidade. A exceção são **grupos de recursos**. As políticas que impõem local ou marcas em um grupo de recursos devem definir **mode** como `all` e direcionar especificamente o tipo `Microsoft.Resources/subscriptions/resourceGroup`. Para obter um exemplo, consulte [Impor marcas do grupo de recursos](../samples/enforce-tag-rg.md).

## <a name="parameters"></a>parâmetros

Parâmetros ajudam a simplificar o gerenciamento de política, reduzindo o número de definições de política. Pense em parâmetros como os campos em um formulário – `name`, `address`, `city`, `state`. Esses parâmetros sempre permanecem iguais, porém, seus valores mudam com base no preenchimento individual do formulário.
Os parâmetros funcionam da mesma maneira que ao criar políticas. Ao incluir parâmetros em uma definição de política, você pode reutilizar essa política para diferentes cenários usando valores diferentes.

> [!NOTE]
> A definição de parâmetros para uma definição de política ou iniciativa só pode ser configurada durante a criação inicial da política ou iniciativa. A definição de parâmetros não poderá ser alterada posteriormente.
> Isso impede que atribuições existentes da política ou da iniciativa sejam tornadas inválidas indiretamente.

Por exemplo, você pode definir uma política para limitar os locais em que os recursos podem ser implantados.
Quando você cria sua política, declarara os seguintes parâmetros:

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

Na propriedade de metadados, você pode usar **strongType**  para fornecer uma lista de opções de seleção múltipla no portal do Azure. Os valores atualmente permitidos para **strongType** incluem:

- `"location"`
- `"resourceTypes"`
- `"storageSkus"`
- `"vmSKUs"`
- `"existingResourceGroups"`
- `"omsWorkspace"`

Na regra de política, você faz referência a parâmetros com a seguinte sintaxe de função de valor de implantação `parameters`:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="definition-location"></a>Local da definição

Durante a criação de uma iniciativa ou política, é necessário especificar o local da definição. O local da definição deve ser um grupo de gerenciamento ou uma assinatura. Esse local determina o escopo para o qual pode ser atribuída a iniciativa ou política. Os recursos devem ser membros diretos ou filhos da hierarquia do local da definição para direcionar para a atribuição.

Se o local da definição for:

- **Assinatura**: somente os recursos dentro dessa assinatura podem ser atribuídos à política.
- **Grupo de gerenciamento**: somente os recursos dentro de grupos de gerenciamento secundários e assinaturas secundárias podem ser atribuídos à política. Se você planeja aplicar esta definição de política a diversas assinaturas, o local deve ser um grupo de gerenciamento que contém as assinaturas.

## <a name="display-name-and-description"></a>Nome de exibição e descrição

Use **displayName** e **description** para identificar a definição de política e fornecer contexto de quando ela é usada.

## <a name="policy-rule"></a>Regra de política

A regra de política consiste em **se** e **, em seguida,** blocos. No **se** bloco, você define uma ou mais condições que especificam quando a política é aplicada. Você pode aplicar os operadores lógicos para essas condições para definir exatamente o cenário para uma política.

No **, em seguida,** bloco, você define o efeito que acontecerá quando o **se** condições sejam atendidas.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists | disabled"
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

Ao usar as condições **like** e **notLike**, você fornece um curinga (`*`) no valor.
O valor não deve ter mais de um curinga `*`.

Ao usar as condições **match** e **notMatch**, forneça `#` para corresponder a um dígito, `?` para uma letra, `.` para corresponder a todos os caracteres e qualquer outro caractere para corresponder ao caractere real. Para obter exemplos, veja [Permitir vários padrões de nome](../samples/allow-multiple-name-patterns.md).

### <a name="fields"></a>Campos

As condições são formadas usando campos. Um campo combina às propriedades no conteúdo da solicitação de recurso e descreve o estado do recurso.

Há suporte para os seguintes campos:

- `name`
- `fullName`
  - Retorna o nome completo do recurso. O nome completo de um recurso é o nome do recurso precedido dos nomes dos recursos pai (por exemplo, "myServer/myDatabase").
- `kind`
- `type`
- `location`
  - Use **global** para recursos independentes de local. Por exemplo, veja [Exemplos – locais permitidos](../samples/allowed-locations.md).
- `identity.type`
  - Retorna o tipo de [Identidade Gerenciada](../../../active-directory/managed-identities-azure-resources/overview.md) habilitada no recurso.
- `tags`
- `tags.<tagName>`
  - Em que **\<tagName\>** é o nome da marca para a qual validar a condição.
  - Exemplo: `tags.CostCenter` em que **CostCenter** é o nome da marca.
- `tags[<tagName>]`
  - Essa sintaxe de colchete é compatível com nomes de marca com um ponto.
  - Em que **\<tagName\>** é o nome da marca para a qual validar a condição.
  - Exemplo: `tags[Acct.CostCenter]` em que **Acct.CostCenter** é o nome da marca.
- aliases de propriedade - para obter uma lista, confira [Aliases](#aliases).

### <a name="effect"></a>Efeito

A política é compatível com os seguintes tipos de efeito:

- **Negar**: gera um evento no log de atividades e falha na solicitação
- **Auditoria**: gera um evento de aviso no log de atividades, mas não falha na solicitação
- **Acrescentar**: adiciona o conjunto de campos definido à solicitação
- **AuditIfNotExists**: habilitará a auditoria se um recurso não existir
- **DeployIfNotExists**: implanta um recurso caso ele ainda não exista
- **Desabilitado**: não avalia os recursos de conformidade para a regra de política

Para **acrescentar**, você precisa fornecer os detalhes abaixo:

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

O valor pode ser uma cadeia de caracteres ou um objeto no formato JSON.

**AuditIfNotExists** e **DeployIfNotExists** avaliam a existência de um recurso relacionado e aplicam uma regra. Se o recurso não corresponder à regra, o efeito será implementado. Por exemplo, você pode exigir que um observador de rede seja implantado para todas as redes virtuais. Para obter mais informações, veja o exemplo [Auditar se a extensão não existir](../samples/audit-ext-not-exist.md).

O efeito **DeployIfNotExists** requer a propriedade **roleDefinitionId** na parte de **detalhes** da regra de política. Para saber mais, confira [Correção – configurar a definição de política](../how-to/remediate-resources.md#configure-policy-definition).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

Para obter detalhes completos sobre cada efeito, ordem de avaliação, propriedades e exemplos, confira [Compreendendo os efeitos da Política](effects.md).

### <a name="policy-functions"></a>Funções de política

Várias [Funções de modelo do Resource Manager](../../../azure-resource-manager/resource-group-template-functions.md) estão disponíveis para uso dentro de uma regra de política. As funções atualmente com suporte são:

- [parâmetros](../../../azure-resource-manager/resource-group-template-functions-deployment.md#parameters)
- [concat](../../../azure-resource-manager/resource-group-template-functions-array.md#concat)
- [resourceGroup](../../../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)
- [subscription](../../../azure-resource-manager/resource-group-template-functions-resource.md#subscription)

Além disso, a função `field` está disponível para as regras de política. `field` é principalmente para uso com **AuditIfNotExists** e **DeployIfNotExists** para referenciar campos no recurso que estão sendo avaliados. Um exemplo desse uso pode ser visto no [exemplo DeployIfNotExists](effects.md#deployifnotexists-example).

#### <a name="policy-function-examples"></a>Exemplos de função de política

Este exemplo de regra de política usa a função de recurso `resourceGroup` para obter a propriedade **name**, combinada com a matriz `concat` e o objeto de função para criar uma condição `like` que impõe o nome do recurso para iniciar com o nome do grupo de recursos.

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

Este exemplo de regra de política usa a função de recurso `resourceGroup` para obter o valor de matriz de propriedade **tags** da marca **CostCenter** no grupo de recursos e acrescentá-lo à marca **CostCenter** no novo recurso.

```json
{
    "if": {
        "field": "tags.CostCenter",
        "exists": "false"
    },
    "then": {
        "effect": "append",
        "details": [{
            "field": "tags.CostCenter",
            "value": "[resourceGroup().tags.CostCenter]"
        }]
    }
}
```

## <a name="aliases"></a>Aliases

Você pode usar aliases de propriedade para acessar propriedades específicas para um tipo de recurso. Os aliases permitem restringir quais valores ou condições são permitidas para uma propriedade em um recurso. Cada alias é mapeado para caminhos em diferentes versões de API para um tipo de recurso específico. Durante a avaliação de política, o mecanismo de políticas obtém o caminho de propriedade para essa versão de API.

A lista de aliases sempre está aumentando. Para descobrir quais aliases atualmente são compatíveis com o Azure Policy, use um dos seguintes métodos:

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzureRmAccount if not using Cloud Shell

  # Use Get-AzureRmPolicyAlias to list available providers
  Get-AzureRmPolicyAlias -ListAvailable

  # Use Get-AzureRmPolicyAlias to list aliases for a Namespace (such as Azure Automation -- Microsoft.Automation)
  Get-AzureRmPolicyAlias -NamespaceMatch 'automation'
  ```

- CLI do Azure

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Automation -- Microsoft.Automation)
  az provider show --namespace Microsoft.Automation --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- API REST / ARMClient

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Noções básicas sobre o alias [*]

Vários aliases disponíveis têm uma versão que é exibida como um nome "normal" e outra que tem **[\*]** anexado a ela. Por exemplo: 

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

O primeiro exemplo é usado para avaliar toda a matriz, em que o alias **[\*]** avalia cada elemento dela.

Vamos examinar uma regra de política como um exemplo. Essa política **Negará** uma conta de armazenamento que tiver ipRules configurado e se **nenhum** dos ipRules tiver um valor de "127.0.0.1".

```json
"policyRule": {
    "if": {
        "allOf": [{
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
                "notEquals": "127.0.0.1"
            }
        ]
    },
    "then": {
        "effect": "deny",
    }
}
```

A matriz **ipRules** é a seguinte para o exemplo:

```json
"ipRules": [{
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Veja como este exemplo é processado:

- `networkAcls.ipRules` – Verifique se a matriz não é nula. Ela é avaliada como verdadeira para que a avaliação continue.

  ```json
  {
    "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
    "exists": "true"
  }
  ```

- `networkAcls.ipRules[*].value` – Verifica cada propriedade _Valor_ na matriz **ipRules**.

  ```json
  {
    "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
    "notEquals": "127.0.0.1"
  }
  ```

  - Como uma matriz, cada elemento será processado.

    - "127.0.0.1" != "127.0.0.1" é avaliado como false.
    - "127.0.0.1" != "192.168.1.1" é avaliado como true.
    - Pelo menos uma propriedade _valor_ na matriz **ipRules** foi avaliada como false; portanto, a avaliação será interrompida.

Como uma condição avaliada como false, o efeito **Negação** não é disparado.

## <a name="initiatives"></a>Iniciativas

Iniciativas permitem que você agrupe várias definições de políticas relacionadas para simplificar atribuições e gerenciamento, pois você trabalha com um grupo como um único item. Por exemplo, você pode agrupar as definições de política de marcação relacionadas em uma única iniciativa. Em vez de atribuir cada política individualmente, você aplica a iniciativa.

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

- Revise os exemplos em [amostras da Política do Azure](../samples/index.md)
- Revisão [Noções básicas sobre os efeitos de política](effects.md)
- Entender como [criar políticas de forma programática](../how-to/programmatically-create.md)
- Saiba como [obter dados de conformidade](../how-to/getting-compliance-data.md)
- Saiba como [corrigir recursos fora de conformidade](../how-to/remediate-resources.md)
- Examine o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md)
