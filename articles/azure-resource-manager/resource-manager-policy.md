---
title: "Políticas de recurso do Azure | Microsoft Docs"
description: "Descreve como usar as políticas do Azure Resource Manager para garantir recursos consistentes propriedades são definidas durante a implantação. As políticas podem ser aplicadas em grupos de recursos ou de assinatura."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: abde0f73-c0fe-4e6d-a1ee-32a6fce52a2d
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: f27bc3689f228809e9db8f61485ea0c8b4b302d1
ms.contentlocale: pt-br
ms.lasthandoff: 06/30/2017


---
# <a name="resource-policy-overview"></a>Visão geral de políticas de recursos
Políticas de recursos permitem que você estabeleça convenções para recursos em sua organização. Definindo as convenções, você pode controlar os custos e muito mais fácil gerenciar seus recursos. Por exemplo, você pode especificar que somente determinados tipos de máquinas virtuais são permitidos, ou você pode exigir que todos os recursos tenham uma determinada marca. As políticas são herdadas por todos os recursos filho. Então, se uma política for aplicada a um grupo de recursos, ela será aplicável a todos os recursos desse grupo de recursos.

Há dois conceitos a entender sobre políticas:

* definição da política - descrevem quando a política é aplicada e qual ação tomar
* atribuição de política - aplicar a definição de política a um escopo (assinatura ou grupo de recursos)

Este tópico se concentra na definição de política. Para obter informações sobre a atribuição de política, consulte [Usar o portal do Azure para atribuir e gerenciar políticas de recurso](resource-manager-policy-portal.md) ou [Atribuir e gerenciar políticas por meio de script](resource-manager-policy-create-assign.md).

Políticas são avaliadas durante a criação e atualização de recursos (PUT e operações de PATCHES).

> [!NOTE]
> Atualmente, a política não avalia os tipos de recursos que não dão suporte a marcas, tipo e local, como o tipo de recurso Microsoft.Resources/deployments. Esse suporte será adicionado no futuro. Para evitar problemas de compatibilidade com versões anteriores, você deve especificar explicitamente o tipo ao criar políticas. Por exemplo, uma política de marcação que não especifica tipos é aplicada a todos os tipos. Nesse caso, uma implantação de modelo poderá falhar se houver um recurso aninhado que não dê suporte a marcas e o tipo de recurso de implantação tiver sido adicionado à avaliação da política. 
> 
> 

## <a name="how-is-it-different-from-rbac"></a>Qual é a diferença dela em relação ao RBAC?
Há algumas diferenças importantes entre a política e o RBAC (controle de acesso baseado em função). O RBAC se concentra nas ações do **usuário** em escopos diferentes. Por exemplo, você é adicionado à função de colaborador de um grupo de recursos no escopo do desejado, para que você possa fazer alterações a esse grupo de recursos. Diretiva enfoca **recursos** propriedades durante a implantação. Por exemplo, por meio de políticas, é possível controlar os tipos de recursos que podem ser provisionados ou restringir os locais em que os recursos podem ser provisionados. Ao contrário do RBAC, a política é um sistema de permissão padrão e negação explícita. 

Para usar políticas, você deve estar autenticado pelo RBAC. Especificamente, a conta precisa de:

* `Microsoft.Authorization/policydefinitions/write` permissão para definir uma política
* `Microsoft.Authorization/policyassignments/write` permissão para atribuir uma política 

Essas permissões não estão incluídas na função **Colaborador**.

## <a name="built-in-policies"></a>Políticas internas

O Azure fornece algumas definições de política internas que podem reduzir o número de políticas que você precisa definir. Antes de continuar com as definições de política, você deve considerar se uma política interna já fornece a definição de que você precisa. As definições de políticas internas são:

* Locais permitidos
* Tipos de recursos permitidos
* SKUs de contas de armazenamento permitidas
* SKUs de máquinas virtuais permitidas
* Aplicar a marca e o valor padrão
* Impor a marca e o valor
* Tipos de recursos não permitidos
* Requer o SQL Server versão 12.0
* Exigir criptografia de conta de armazenamento

Você pode atribuir qualquer uma dessas políticas por meio do [portal](resource-manager-policy-portal.md), do [PowerShell](resource-manager-policy-create-assign.md#powershell) ou da [CLI do Azure](resource-manager-policy-create-assign.md#azure-cli).

## <a name="policy-definition-structure"></a>Estrutura da definição de política
Você usa JSON para criar uma definição de política. A definição de política contém elementos para:

* parameters
* nome de exibição
* description
* regra de política
  * avaliação de lógica
  * efeito

O exemplo a seguir mostra uma política que limita os locais em que os recursos são implantados:

```json
{
  "properties": {
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

## <a name="parameters"></a>Parâmetros
O uso de parâmetros ajuda a simplificar o gerenciamento de política, reduzindo o número de definições de política. Defina uma política para uma propriedade de recurso (por exemplo, limitando os locais onde os recursos podem ser implantados) e incluir parâmetros na definição. Em seguida, reutilizar essa definição de política para diferentes cenários pela passagem de valores diferentes (como especificar um conjunto de locais para uma assinatura) quando atribuir a política.

Declare parâmetros ao criar definições de política.

```json
"parameters": {
  "allowedLocations": {
    "type": "array",
    "metadata": {
      "description": "The list of allowed locations for resources.",
      "displayName": "Allowed locations"
    }
  }
}
```

O tipo de um parâmetro pode ser cadeia de caracteres ou matriz. A propriedade de metadados é usada para que ferramentas como o portal do Azure exibam informações amigáveis ao usuário. 

Na regra de política, você fazer referência a parâmetros com a seguinte sintaxe: 

```json
{ 
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

## <a name="display-name-and-description"></a>Nome de exibição e descrição

Você usa o **displayName** e **description** para identificar a definição de política e fornecer contexto para quando ele é usado.

## <a name="policy-rule"></a>Regra de política

A regra de política consiste em **se** e **, em seguida,** blocos. No **se** bloco, você define uma ou mais condições que especificam quando a política é aplicada. Você pode aplicar os operadores lógicos para essas condições para definir exatamente o cenário para uma política. No **, em seguida,** bloco, você define o efeito que acontecerá quando o **se** condições sejam atendidas.

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
Os operadores lógicos com suporte são:

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

Ao usar a condição **match**, forneça `#` para representar um dígito, `?` para uma letra e outro caractere para representar o caractere real. Para obter exemplos, consulte [Definir convenção de nomenclatura](#set-naming-convention).

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
A política dá suporte a três tipos de efeito - `deny`, `audit` e `append`. 

* **Negar** gera um evento no log de auditoria e causa uma falha da solicitação
* **Auditar** gera um evento de aviso no log de auditoria, mas não causa falha da solicitação
* **Acrescentar** adiciona o conjunto de campos definido à solicitação 

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

## <a name="aliases"></a>Aliases

Você pode usar aliases de propriedade para acessar propriedades específicas para um tipo de recurso. 

**Microsoft.Cache/Redis**

| Alias | Descrição |
| ----- | ----------- |
| Microsoft.Cache/Redis/enableNonSslPort | Defina se a porta (6379) do servidor Redis não ssl está habilitada. |
| Microsoft.Cache/Redis/shardCount | Defina o número de fragmentos a serem criados em um Cache de Cluster Premium.  |
| Microsoft.Cache/Redis/sku.capacity | Defina o tamanho do cache Redis a ser implantado.  |
| Microsoft.Cache/Redis/sku.family | Veja a família de SKU a ser usada. |
| Microsoft.Cache/Redis/sku.name | Defina o tipo de Cache Redis a ser implantado. |

**Microsoft.Cdn/profiles**

| Alias | Descrição |
| ----- | ----------- |
| Microsoft.CDN/profiles/sku.name | Defina o nome do tipo de preços. |

**Microsoft.Compute/disks**

| Alias | Descrição |
| ----- | ----------- |
| Microsoft.Compute/imageOffer | Defina a oferta da imagem da plataforma ou da imagem do marketplace usada para criar a máquina virtual. |
| Microsoft.Compute/imagePublisher | Defina o editor da imagem da plataforma ou da imagem do marketplace usada para criar a máquina virtual. |
| Microsoft.Compute/imageSku | Defina a SKU da imagem da plataforma ou da imagem do marketplace usada para criar a máquina virtual. |
| Microsoft.Compute/imageVersion | Defina a versão da imagem da plataforma ou da imagem do marketplace usada para criar a máquina virtual. |


**Microsoft.Compute/virtualMachines**

| Alias | Descrição |
| ----- | ----------- |
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

| Alias | Descrição |
| ----- | ----------- |
| Microsoft.Compute/virtualMachines/extensions/publisher | Defina o nome do publicador da extensão. |
| Microsoft.Compute/virtualMachines/extensions/type | Defina o tipo de extensão. |
| Microsoft.Compute/virtualMachines/extensions/typeHandlerVersion | Defina a versão da extensão. |

**Microsoft.Compute/virtualMachineScaleSets**

| Alias | Descrição |
| ----- | ----------- |
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

| Alias | Descrição |
| ----- | ----------- |
| Microsoft.Network/applicationGateways/sku.name | Defina o tamanho do gateway. |

**Microsoft.Network/virtualNetworkGateways**

| Alias | Descrição |
| ----- | ----------- |
| Microsoft.Network/virtualNetworkGateways/gatewayType | Defina o tipo deste gateway de rede virtual. |
| Microsoft.Network/virtualNetworkGateways/sku.name | Defina o nome da SKU de gateway. |

**Microsoft.Sql/servers**

| Alias | Descrição |
| ----- | ----------- |
| Microsoft.Sql/servers/version | Defina a versão do servidor. |

**Microsoft.Sql/databases**

| Alias | Descrição |
| ----- | ----------- |
| Microsoft.Sql/servers/databases/edition | Defina a edição do banco de dados. |
| Microsoft.Sql/servers/databases/elasticPoolName | Defina o nome do pool elástico em que o banco de dados está. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveId | Defina a ID de objetivo de nível de serviço configurada do banco de dados. |
| Microsoft.Sql/servers/databases/requestedServiceObjectiveName | Defina o nome do objetivo de nível de serviço configurado do banco de dados.  |

**Microsoft.Sql/elasticpools**

| Alias | Descrição |
| ----- | ----------- |
| servidores/elasticpools | Microsoft.Sql/servers/elasticPools/dtu | Defina o DTU compartilhado total para o pool elástico de banco de dados. |
| servidores/elasticpools | Microsoft.Sql/servers/elasticPools/edition | Defina a edição do pool elástico. |

**Microsoft.Storage/storageAccounts**

| Alias | Descrição |
| ----- | ----------- |
| Microsoft.Storage/storageAccounts/accessTier | Defina o nível de acesso usado para cobrança. |
| Microsoft.Storage/storageAccounts/accountType | Defina o nome da SKU. |
| Microsoft.Storage/storageAccounts/enableBlobEncryption | Defina se o serviço criptografa os dados conforme eles são armazenados no serviço de armazenamento de blobs. |
| Microsoft.Storage/storageAccounts/enableFileEncryption | Defina se o serviço criptografa os dados conforme eles são armazenados no serviço de armazenamento de arquivos. |
| Microsoft.Storage/storageAccounts/sku.name | Defina o nome da SKU. |
| Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly | Configurado para permitir somente o tráfego https para o serviço de armazenamento. |


## <a name="policy-examples"></a>Exemplos de políticas

Os tópicos a seguir contêm exemplos de política:

* Para obter exemplos de políticas de marca, veja [Aplicar políticas de recursos para marcas](resource-manager-policy-tags.md).
* Para obter exemplos de padrões de nomenclatura e texto, confira [Aplicar políticas de recursos a nomes e texto](resource-manager-policy-naming-convention.md).
* Para obter exemplos de políticas de armazenamento, veja [Aplicar políticas de recursos para contas de armazenamento](resource-manager-policy-storage.md).
* Para obter exemplos de políticas de máquina virtual, veja [Aplicar políticas de recursos a VMs Linux](../virtual-machines/linux/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json) e [Aplicar políticas de recursos a VMs do Windows](../virtual-machines/windows/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)


## <a name="next-steps"></a>Próximas etapas
* Depois de definir uma regra de política, atribua um escopo. Para atribuir políticas por meio do portal, consulte [Usar o portal do Azure para atribuir e gerenciar políticas de recurso](resource-manager-policy-portal.md). Para atribuir políticas por meio da API REST, do PowerShell ou da CLI do Azure, consulte [Atribuir e gerenciar políticas por meio de script](resource-manager-policy-create-assign.md).
* Para obter orientação sobre como as empresas podem usar o Resource Manager para gerenciar assinaturas de forma eficaz, consulte [Azure enterprise scaffold – controle de assinatura prescritivas](resource-manager-subscription-governance.md).
* O esquema da política é publicado em [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json). 


