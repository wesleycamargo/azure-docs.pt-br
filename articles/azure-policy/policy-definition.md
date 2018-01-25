---
title: "Estrutura de definição do Azure Policy | Microsoft Docs"
description: "Descreve como a definição de política de recurso é usada pelo Azure Policy para estabelecer as convenções para recursos em sua organização descrevendo quando a política é imposta e a ação a tomar."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/17/2018
ms.topic: article
ms.service: azure-policy
ms.custom: 
ms.openlocfilehash: af373e2770ad020b3a3eb669424c001670ec9204
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2018
---
# <a name="azure-policy-definition-structure"></a>Estrutura de definição da Política do Azure

Definição de política de recursos usada pelo Azure Policy permite que você estabeleça convenções para recursos em sua organização descrevendo quando a política é aplicada e a ação a realizar. Definindo as convenções, você pode controlar os custos e muito mais fácil gerenciar seus recursos. Por exemplo, você pode especificar que somente determinados tipos de máquinas virtuais são permitidos. Ou você pode exigir que todos os recursos tenham uma marca específica. As políticas são herdadas por todos os recursos filho. Então, se uma política for aplicada a um grupo de recursos, ela será aplicável a todos os recursos desse grupo de recursos.

Você usa JSON para criar uma definição de política. A definição de política contém elementos para:

* modo
* parâmetros
* nome de exibição
* descrição
* regra de política
  * avaliação de lógica
  * efeito

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

Todos os exemplos de modelo do Azure Policy estão em [Modelos para o Azure Policy](json-samples.md).

## <a name="mode"></a>Mode

Recomendamos que você defina `mode` como `all` para que uma atribuição de política avalie todos os grupos e tipos de recursos. Você pode ver um exemplo de uma definição de política que impõe marcas em um grupo de recursos em [Permitir imagem personalizada da VM de um Grupo de Recursos](scripts/allow-custom-vm-image.md).

Quando você define como **all**, os grupos de recursos e todos os tipos de recurso são avaliados para a política. O portal usa **all** para todas as políticas. Se usar o PowerShell ou a CLI do Azure, você precisará especificar o parâmetro `mode` e defini-lo como **all**.

Todas as definições de política criadas usando o portal de usam um modo `all`, porém, se você quiser usar o PowerShell ou a CLI do Azure, precisará especificar o parâmetro `mode` e defini-lo como `all`.

Se você definir o modo como `indexed`, a atribuição de política será avaliada apenas em tipos de recursos que dão suporte a marcas e a local.


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

Dentro da propriedade de metadados, você pode usar **strongType** para fornecer uma lista de opções que permite selecionar várias opções no portal do Azure.  Os valores atualmente permitidos para **strongType** incluem:

* `"location"`
* `"resourceTypes"`
* `"storageSkus"`
* `"vmSKUs"`

Na regra de política, você fazer referência a parâmetros com a seguinte sintaxe:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

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
    "effect": "deny | audit | append"
  }
}
```

### <a name="logical-operators"></a>Operadores lógicos

Os operadores lógicos compatíveis são:

* `"not": {condition  or operator}`
* `"allOf": [{condition or operator},{condition or operator}]`
* `"anyOf": [{condition or operator},{condition or operator}]`

O **não** sintaxe inverte o resultado da condição. A sintaxe de **allOf** (semelhante à operação **E** lógica) requer que todas as condições sejam verdadeiras. A sintaxe de **anyOf** (semelhante à operação **Ou** lógica) requer que uma ou mais condições sejam verdadeiras.

Você pode aninhar operadores lógicos. A exemplo a seguir mostra uma operação **not** operação é aninhada dentro de uma operação **allOf**.

```json
"if": {
  "allOf": [
    {
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

* `"equals": "value"`
* `"like": "value"`
* `"match": "value"`
* `"contains": "value"`
* `"in": ["value1","value2"]`
* `"containsKey": "keyName"`
* `"exists": "bool"`

Ao usar a condição **like**, você pode fornecer um curinga (*) no valor.

Ao usar a condição **match**, forneça `#` para representar um dígito, `?` para uma letra e outro caractere para representar o caractere real. Para obter exemplos, consulte [imagens da VM Aprovada](scripts/allowed-custom-images.md).

### <a name="fields"></a>Campos
As condições são formadas usando campos. Um campo representa as propriedades na carga de solicitação de recurso que é usada para descrever o estado do recurso.  

Há suporte para os seguintes campos:

* `name`
* `kind`
* `type`
* `location`
* `tags`
* `tags.*`
* aliases de propriedade - para obter uma lista, confira [Aliases](#aliases).

### <a name="effect"></a>Efeito
A política é compatível com os seguintes tipos de efeito:

* **Negar**: gera um evento no log de auditoria e causa uma falha da solicitação
* **Auditar**: gera um evento de aviso no log de auditoria, mas não causa falha da solicitação
* **Acrescentar**: adiciona o conjunto de campos definido à solicitação
* **AuditIfNotExists**: habilitará a auditoria se um recurso não existir
* **DeployIfNotExists**: implantará um recurso se ele ainda não existir. Atualmente, esse efeito tem suporte apenas por meio de políticas internas.

Para **acrescentar**, você precisa fornecer os detalhes abaixo:

```json
"effect": "append",
"details": [
  {
    "field": "field name",
    "value": "value of the field"
  }
]
```

O valor pode ser uma cadeia de caracteres ou um objeto no formato JSON.

Com **AuditIfNotExists**, e **DeployIfNotExists**, você pode avaliar a existência de um recurso filho e aplicar uma regra e um efeito correspondente quando esse recurso não existir. Por exemplo, você pode exigir que um observador de rede seja implantado para todas as redes virtuais.
Para obter um exemplo de como fazer auditoria quando uma extensão da máquina virtual não está implantada, consulte [Auditoria se não existir extensão](scripts/audit-ext-not-exist.md).


## <a name="aliases"></a>Aliases

Você pode usar aliases de propriedade para acessar propriedades específicas para um tipo de recurso. Os aliases permitem restringir quais valores ou condições são permitidas para uma propriedade em um recurso. Cada alias é mapeado para caminhos em diferentes versões de API para um tipo de recurso específico. Durante a avaliação de política, o mecanismo de políticas obtém o caminho de propriedade para essa versão de API.

**Microsoft.Cache/Redis**

| Alias | DESCRIÇÃO |
| ----- | ----------- |
| Microsoft.Cache/Redis/enableNonSslPort | Defina se a porta (6379) do servidor Redis não ssl está habilitada. |
| Microsoft.Cache/Redis/shardCount | Defina o número de fragmentos a serem criados em um Cache de Cluster Premium.  |
| Microsoft.Cache/Redis/sku.capacity | Defina o tamanho do cache Redis a ser implantado.  |
| Microsoft.Cache/Redis/sku.family | Veja a família de SKU a ser usada. |
| Microsoft.Cache/Redis/sku.name | Defina o tipo de Cache Redis a ser implantado. |

**Microsoft.Cdn/profiles**

| Alias | DESCRIÇÃO |
| ----- | ----------- |
| Microsoft.CDN/profiles/sku.name | Defina o nome do tipo de preços. |

**Microsoft.Compute/disks**

| Alias | DESCRIÇÃO |
| ----- | ----------- |
| Microsoft.Compute/imageOffer | Defina a oferta da imagem da plataforma ou da imagem do marketplace usada para criar a máquina virtual. |
| Microsoft.Compute/imagePublisher | Defina o editor da imagem da plataforma ou da imagem do marketplace usada para criar a máquina virtual. |
| Microsoft.Compute/imageSku | Defina a SKU da imagem da plataforma ou da imagem do marketplace usada para criar a máquina virtual. |
| Microsoft.Compute/imageVersion | Defina a versão da imagem da plataforma ou da imagem do marketplace usada para criar a máquina virtual. |


**Microsoft.Compute/virtualMachines**

| Alias | DESCRIÇÃO |
| ----- | ----------- |
| Microsoft.Compute/imageId | Defina o identificador da imagem usada para criar a máquina virtual. |
| Microsoft.Compute/imageOffer | Defina a oferta da imagem da plataforma ou da imagem do marketplace usada para criar a máquina virtual. |
| Microsoft.Compute/imagePublisher | Defina o editor da imagem da plataforma ou da imagem do marketplace usada para criar a máquina virtual. |
| Microsoft.Compute/imageSku | Defina a SKU da imagem da plataforma ou da imagem do marketplace usada para criar a máquina virtual. |
| Microsoft.Compute/imageVersion | Defina a versão da imagem da plataforma ou da imagem do marketplace usada para criar a máquina virtual. |
| Microsoft.Compute/licenseType | Defina que a imagem ou o disco está licenciado no local. Esse valor é usado apenas para imagens que contêm o sistema operacional Windows Server.  |
| Microsoft.Compute/virtualMachines/imageOffer | Defina a oferta da imagem da plataforma ou da imagem do marketplace usada para criar a máquina virtual. |
| Microsoft.Compute/virtualMachines/imagePublisher | Defina o editor da imagem da plataforma ou da imagem do marketplace usada para criar a máquina virtual. |
| Microsoft.Compute/virtualMachines/imageSku | Defina a SKU da imagem da plataforma ou da imagem do marketplace usada para criar a máquina virtual. |
| Microsoft.Compute/virtualMachines/imageVersion | Defina a versão da imagem da plataforma ou da imagem do marketplace usada para criar a máquina virtual. |
| Microsoft.Compute/virtualMachines/osDisk.Uri | Defina o URI do vhd. |
| Microsoft.Compute/virtualMachines/sku.name | Defina o tamanho da máquina virtual. |

**Microsoft.Compute/virtualMachines/extensions**

| Alias | DESCRIÇÃO |
| ----- | ----------- |
| Microsoft.Compute/virtualMachines/extensions/publisher | Defina o nome do publicador da extensão. |
| Microsoft.Compute/virtualMachines/extensions/type | Defina o tipo de extensão. |
| Microsoft.Compute/virtualMachines/extensions/typeHandlerVersion | Defina a versão da extensão. |

**Microsoft.Compute/virtualMachineScaleSets**

| Alias | DESCRIÇÃO |
| ----- | ----------- |
| Microsoft.Compute/imageId | Defina o identificador da imagem usada para criar a máquina virtual. |
| Microsoft.Compute/imageOffer | Defina a oferta da imagem da plataforma ou da imagem do marketplace usada para criar a máquina virtual. |
| Microsoft.Compute/imagePublisher | Defina o editor da imagem da plataforma ou da imagem do marketplace usada para criar a máquina virtual. |
| Microsoft.Compute/imageSku | Defina a SKU da imagem da plataforma ou da imagem do marketplace usada para criar a máquina virtual. |
| Microsoft.Compute/imageVersion | Defina a versão da imagem da plataforma ou da imagem do marketplace usada para criar a máquina virtual. |
| Microsoft.Compute/licenseType | Defina que a imagem ou o disco está licenciado no local. Esse valor é usado apenas para imagens que contêm o sistema operacional Windows Server. |
| Microsoft.Compute/VirtualMachineScaleSets/computerNamePrefix | Defina o prefixo do nome do computador para todas as máquinas virtuais no conjunto de dimensionamento. |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl | Defina o URI de blob da imagem de usuário. |
| Microsoft.Compute/VirtualMachineScaleSets/osdisk.vhdContainers | Defina as URLs de contêiner que são usadas para armazenar discos do sistema operacional para o conjunto de dimensionamento. |
| Microsoft.Compute/VirtualMachineScaleSets/sku.name | Defina o tamanho das máquinas virtuais em um conjunto de dimensionamento. |
| Microsoft.Compute/VirtualMachineScaleSets/sku.tier | Defina o nível de máquinas virtuais em um conjunto de dimensionamento. |

**Microsoft.Network/applicationGateways**

| Alias | DESCRIÇÃO |
| ----- | ----------- |
| Microsoft.Network/applicationGateways/sku.name | Defina o tamanho do gateway. |

**Microsoft.Network/virtualNetworkGateways**

| Alias | DESCRIÇÃO |
| ----- | ----------- |
| Microsoft.Network/virtualNetworkGateways/gatewayType | Defina o tipo deste gateway de rede virtual. |
| Microsoft.Network/virtualNetworkGateways/sku.name | Defina o nome da SKU de gateway. |

**Microsoft.Sql/servers**

| Alias | DESCRIÇÃO |
| ----- | ----------- |
| Microsoft.Sql/servers/version | Defina a versão do servidor. |

**Microsoft.Sql/databases**

| Alias | DESCRIÇÃO |
| ----- | ----------- |
| Microsoft.Sql/servers/databases/edition | Defina a edição do banco de dados. |
| Microsoft.Sql/servers/databases/elasticPoolName | Defina o nome do pool elástico em que o banco de dados está. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveId | Defina a ID de objetivo de nível de serviço configurada do banco de dados. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveName | Defina o nome do objetivo de nível de serviço configurado do banco de dados.  |

**Microsoft.Sql/elasticpools**

| Alias | DESCRIÇÃO |
| ----- | ----------- |
| servidores/elasticpools | Microsoft.Sql/servers/elasticPools/dtu | Defina o DTU compartilhado total para o pool elástico de banco de dados. |
| servidores/elasticpools | Microsoft.Sql/servers/elasticPools/edition | Defina a edição do pool elástico. |

**Microsoft.Storage/storageAccounts**

| Alias | DESCRIÇÃO |
| ----- | ----------- |
| Microsoft.Storage/storageAccounts/accessTier | Defina o nível de acesso usado para cobrança. |
| Microsoft.Storage/storageAccounts/accountType | Defina o nome da SKU. |
| Microsoft.Storage/storageAccounts/enableBlobEncryption | Defina se o serviço criptografa os dados conforme eles são armazenados no serviço de armazenamento de blobs. |
| Microsoft.Storage/storageAccounts/enableFileEncryption | Defina se o serviço criptografa os dados conforme eles são armazenados no serviço de armazenamento de arquivos. |
| Microsoft.Storage/storageAccounts/sku.name | Defina o nome da SKU. |
| Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly | Configurado para permitir somente o tráfego https para o serviço de armazenamento. |

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
        "policyDefinitions": [
            {
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

- Examine os exemplos de modelo do Azure Policy estão em [Modelos para o Azure Policy](json-samples.md).
