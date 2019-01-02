---
title: Entender como funcionam os efeitos
description: A definição do Azure Policy tem vários efeitos que determinam como a conformidade é gerenciada e relatada.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/06/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 0fcb30132a83502b8ca5f58364d78129109b8a9d
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310837"
---
# <a name="understand-policy-effects"></a>Compreender os efeitos da Política

Cada definição de política no Azure Policy tem um único efeito. Esse efeito determina o que acontece quando a regra de política é avaliada para correspondência. Os efeitos se comportam de modo diferente caso sejam para um novo recurso, um recurso atualizado ou um recurso existente.

Atualmente, há seis efeitos com suporte em uma definição de política:

- Acrescentar
- Audit
- AuditIfNotExists
- Negar
- DeployIfNotExists
- Desabilitado

## <a name="order-of-evaluation"></a>Ordem de avaliação

As solicitações para criar ou atualizar um recurso por meio do Azure Resource Manager são avaliadas pelo Policy primeiro. O Policy cria uma lista de todas as atribuições que se aplicam ao recurso e o avalia em relação a cada definição. O Policy processa vários efeitos antes de enviar a solicitação ao provedor de recursos apropriado. Isso impede o processamento desnecessário por um provedor de recursos quando um recurso não atende aos controles de governança criados pelo Policy.

- **Desabilitado** é marcado primeiro para determinar se a regra de política deve ser avaliada.
- **Append** é, então, avaliado. Como o efeito acrescentar pode alterar a solicitação, a alteração feita ao acrescentar pode impedir uma auditoria ou negar o efeito do gatilho.
- **Negar** é avaliada. Ao avaliar o efeito negar antes da auditoria, evita-se o log duplo de um recurso indesejado.
- **Audit** é avaliado antes de a solicitação ir para o provedor de recursos.

Depois que o provedor de recursos retorna um código de êxito, **AuditIfNotExists** e **DeployIfNotExists** fazem a avaliação para determinar se é necessário fazer registro em log para aumentar a conformidade.

## <a name="disabled"></a>Desabilitado

Esse efeito é útil para testar situações ou quando a definição de política tiver parametrizado o efeito. Essa flexibilidade possibilita desabilitar uma única atribuição em vez de desabilitar todas as atribuições da política.

## <a name="append"></a>Acrescentar

O efeito acrescentar é usado para adicionar outros campos ao recurso solicitado durante a criação ou atualização. Um exemplo comum é a adição de marcas em recursos, como costCenter, ou especificar IPs permitidos para um recurso de armazenamento.

### <a name="append-evaluation"></a>Avaliação de acréscimo

Append é avaliado antes que a solicitação seja processada por um provedor de recursos durante a criação ou a atualização de um recurso. O efeito append adiciona o campos ao recurso quando a condição **if** da regra de política for atendida. Se o efeito append substituir um valor na solicitação original por um valor diferente, ele atuará como um efeito Negar e rejeitará a solicitação.

Quando uma definição de política usando o efeito append é executada como parte de um ciclo de avaliação, ela não faz alterações em recursos já existentes. Em vez disso, ela marca qualquer recurso que atende a condição **se** como não conforme.

### <a name="append-properties"></a>Propriedades de acréscimo

Um efeito acrescentar tem apenas uma matriz **detalhes**, que é necessária. Como **detalhes** é uma matriz, pode levar um único par **valor do campo** ou múltiplos. Confira a [estrutura de definição](definition-structure.md#fields) para saber a lista de campos aceitáveis.

### <a name="append-examples"></a>Exemplos de acréscimo

Exemplo 1: par **campo/valor** exclusivo para acrescentar uma marca.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "tags.myTag",
        "value": "myTagValue"
    }]
}
```

Exemplo 2: dois pares **campo/valor** para acrescentar um conjunto de marcas.

```json
"then": {
    "effect": "append",
    "details": [{
            "field": "tags.myTag",
            "value": "myTagValue"
        },
        {
            "field": "tags.myOtherTag",
            "value": "myOtherTagValue"
        }
    ]
}
```

Exemplo 3: par **campo/valor** exclusivo usando um [alias](definition-structure.md#aliases) com uma matriz **value** para definir as regras de IP em uma conta de armazenamento.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

## <a name="deny"></a>Negar

O efeito deny é usado para impedir uma solicitação de recurso que não corresponde aos padrões definidos em uma definição de política e leva à falha da solicitação.

### <a name="deny-evaluation"></a>Avaliação de deny

Ao criar ou atualizar um recurso, o efeito deny impede a solicitação antes de ser enviada ao provedor de recursos. A solicitação é retornada como um `403 (Forbidden)`. No portal, o Forbidden pode ser exibido como um status sobre a implantação que foi impedida pela atribuição de política.

Durante a avaliação dos recursos existentes, os recursos que correspondem a uma definição de política de negação são marcados como fora de conformidade.

### <a name="deny-properties"></a>Propriedades de deny

O efeito deny não têm propriedades adicionais para uso na condição **then** da definição de política.

### <a name="deny-example"></a>Exemplo de deny

Exemplo: Usando o efeito deny.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Audit

Audit é usado para criar um evento de aviso no log de atividades ao avaliar um recurso fora de conformidade, mas ela não para a solicitação.

### <a name="audit-evaluation"></a>Avaliação de auditoria

Audit é o último efeito verificado pelo Policy durante a criação ou a atualização de um recurso. O Policy, em seguida, envia o recurso para o provedor de recursos. Audit funciona da mesma forma para uma solicitação de recurso e um ciclo de avaliação. O Policy adiciona uma operação `Microsoft.Authorization/policies/audit/action` ao log de atividades e a marca como fora de conformidade.

### <a name="audit-properties"></a>Propriedades de auditoria

O efeito audit não têm propriedades adicionais para uso na condição **then** da definição de política.

### <a name="audit-example"></a>Exemplo de auditoria

Exemplo: Usando o efeito audit.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

O efeito AuditIfNotExists habilita a auditoria em recursos que correspondem à condição **if**, mas não têm os componentes especificados nos **detalhes** da condição **then**.

### <a name="auditifnotexists-evaluation"></a>Avaliação de AuditIfNotExists

O efeito AuditIfNotExists é executado depois de um provedor de recursos ter tratado uma solicitação de criação ou atualização de recurso e ter retornado um código de status de êxito. A auditoria ocorre quando não existem recursos relacionados ou se os recursos definidos por **ExistenceCondition** não são avaliados como verdadeiros. O Policy adiciona uma operação `Microsoft.Authorization/policies/audit/action` ao log de atividades da mesma maneira que o efeito audit. Quando disparado, o recurso que atendeu à condição **se** é o recurso marcado como não compatível.

### <a name="auditifnotexists-properties"></a>Propriedades de AuditIfNotExists

A propriedade **detalhes** dos efeitos AuditIfNotExists tem todas as subpropriedades que definem os recursos relacionados a serem correspondidos.

- **Tipo** [obrigatório]
  - Especifica o tipo do recurso relacionado a ser correspondido.
  - Começa tentando buscar um recurso sob o recurso de condição **se**, depois consulta dentro do mesmo grupo de recursos como o recurso de condição **se**.
- **Nome** (opcional)
  - Especifica o nome exato do recurso a ser correspondido e faz com que a política busque um recurso específico em vez de todos os recursos do tipo especificado.
- **ResourceGroupName** (opcional)
  - Permite que a correspondência do recurso relacionado venha de um grupo de recursos diferente.
  - Não se aplica se **type** for um recurso que estaria sob o recurso de condição **if**.
  - O padrão é o grupo de recursos do recurso de condição **se**.
- **ExistenceScope** (opcional)
  - Os valores permitidos são _Assinatura_ e _ResourceGroup_.
  - Define o escopo de onde buscar o recurso relacionado com o qual corresponder.
  - Não se aplica se **type** for um recurso que estaria sob o recurso de condição **if**.
  - Para _ResourceGroup_, limitaria para o grupo de recursos do recurso da condição **se** ou o grupo de recursos especificado em **ResourceGroupName**.
  - Para _Assinatura_, consulta a assinatura inteira para o recurso relacionado.
  - O padrão é _ResourceGroup_.
- **ExistenceCondition** (opcional)
  - Se não for especificado, todos os recursos relacionados de **type** cumprem o efeito e não acionam a auditoria.
  - Usa a mesma linguagem como a regra de política para a condição **se**, mas é avaliada em relação a cada recurso relacionado individualmente.
  - Se algum recurso relacionado correspondente for avaliado como verdadeiro, o efeito será atendido e não acionará a auditoria.
  - Pode usar [field()] para verificar a equivalência com valores na condição **se**.
  - Por exemplo, pode ser usado para validar que o recurso pai (na condição **se**) está no mesmo local que recursos como o recurso relacionado correspondente.

### <a name="auditifnotexists-example"></a>Exemplo de AuditIfNotExists

Exemplo: Avalia máquinas virtuais para determinar se a extensão antimalware existe e faz auditorias quando ausente.

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

Semelhante ao efeito AuditIfNotExists, o efeito DeployIfNotExists executa uma implantação de modelo quando a condição for atendida.

> [!NOTE]
> [Modelos aninhados](../../../azure-resource-manager/resource-group-linked-templates.md#nested-template) são compatíveis com **deployIfNotExists**, mas [modelos vinculados](../../../azure-resource-manager/resource-group-linked-templates.md) não são compatíveis atualmente.

### <a name="deployifnotexists-evaluation"></a>Avaliação de DeployIfNotExists

DeployIfNotExists é executado depois de um provedor de recursos ter tratado uma solicitação de criação ou atualização de um recurso e ter retornado um código de status de êxito. Uma implantação de modelo ocorre quando não existem recursos relacionados ou se os recursos definidos por **ExistenceCondition** não são avaliados como verdadeiros.

Durante um ciclo de avaliação, as definições de política com um efeito DeployIfNotExists que correspondam a recursos são marcadas como não conformes, mas nenhuma ação é realizada no recurso.

### <a name="deployifnotexists-properties"></a>Propriedades de DeployIfNotExists

A propriedade **detalhes** dos efeitos DeployIfNotExists tem todas as subpropriedades que definem os recursos relacionados a serem correspondidos e a implantação de modelo a ser executada.

- **Tipo** [obrigatório]
  - Especifica o tipo do recurso relacionado a ser correspondido.
  - Começa tentando buscar um recurso sob o recurso de condição **se**, depois consulta dentro do mesmo grupo de recursos como o recurso de condição **se**.
- **Nome** (opcional)
  - Especifica o nome exato do recurso a ser correspondido e faz com que a política busque um recurso específico em vez de todos os recursos do tipo especificado.
- **ResourceGroupName** (opcional)
  - Permite que a correspondência do recurso relacionado venha de um grupo de recursos diferente.
  - Não se aplica se **type** for um recurso que estaria sob o recurso de condição **if**.
  - O padrão é o grupo de recursos do recurso de condição **se**.
  - Se uma implantação de modelo é executada, ele é implantado no grupo de recursos desse valor.
- **ExistenceScope** (opcional)
  - Os valores permitidos são _Assinatura_ e _ResourceGroup_.
  - Define o escopo de onde buscar o recurso relacionado com o qual corresponder.
  - Não se aplica se **type** for um recurso que estaria sob o recurso de condição **if**.
  - Para _ResourceGroup_, limitaria para o grupo de recursos do recurso da condição **se** ou o grupo de recursos especificado em **ResourceGroupName**.
  - Para _Assinatura_, consulta a assinatura inteira para o recurso relacionado.
  - O padrão é _ResourceGroup_.
- **ExistenceCondition** (opcional)
  - Se não for especificado, todos os recursos relacionados de **type** cumprem o efeito e não acionam a implantação.
  - Usa a mesma linguagem como a regra de política para a condição **se**, mas é avaliada em relação a cada recurso relacionado individualmente.
  - Se algum recurso relacionado correspondente for avaliado como verdadeiro, o efeito será atendido e não acionará a implantação.
  - Pode usar [field()] para verificar a equivalência com valores na condição **se**.
  - Por exemplo, pode ser usado para validar que o recurso pai (na condição **se**) está no mesmo local que recursos como o recurso relacionado correspondente.
- **roleDefinitionIds** [obrigatório]
  - Essa propriedade deve incluir uma matriz de cadeias de caracteres que correspondem à ID de controle de acesso baseado em função que pode ser acessada pela assinatura. Para obter mais informações, confira [correção – configurar a definição de política](../how-to/remediate-resources.md#configure-policy-definition).
- **Implantação** [obrigatória]
  - Essa propriedade deve incluir a implantação de modelo completo que seria passada para o API PUT de `Microsoft.Resources/deployments`. Para obter mais informações, consulte a [API REST de implantações](/rest/api/resources/deployments).

  > [!NOTE]
  > Todas as funções dentro da propriedade **Implantação** são avaliadas como componentes do modelo, não da política. A exceção é a propriedade **parâmetros**, que passa os valores da política para o modelo. O **valor** nesta seção em um nome de parâmetro de modelo é usado para executar essa passagem de valor (consulte _fullDbName_ no exemplo do efeito DeployIfNotExists).

### <a name="deployifnotexists-example"></a>Exemplo de DeployIfNotExists

Exemplo: Avalia os bancos de dados do SQL Server para determinar se transparentDataEncryption está habilitado. Se não estiver, uma implantação será executada para habilitá-lo.

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "roleDefinitionIds": [
            "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
            "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
        ],
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## <a name="layering-policies"></a>Políticas de camadas

Um recurso pode ser afetado por várias atribuições. Essas atribuições podem estar no mesmo escopo ou em escopos diferentes. Também é provável que cada uma dessas atribuições tenha um efeito diferente definido. A condição e o efeito de cada política são avaliados independentemente. Por exemplo: 

- Política 1
  - Restringe o local do recurso para 'westus'
  - Atribuída à assinatura A
  - Efeito deny
- Política 2
  - Restringe o local do recurso para 'eastus'
  - Atribuída ao grupo de recursos B na assinatura A
  - Efeito audit
  
Essa configuração resultaria no seguinte:

- Os recursos já presentes no grupo de recursos B em “eastus” estão em conformidade em relação à política 2 e fora de conformidade em relação à política 1
- Os recursos presentes no grupo de recursos B e fora de 'eastus' estão fora de conformidade em relação à política 2 e fora de conformidade em relação à política 1 se não estão em 'westus'
- Novos recursos na assinatura A não presentes em “westus” são negados pela política 1
- Novos recursos na assinatura A e no grupo de recursos B em 'westus' são criados e estão fora de conformidade em relação à política 2

Se ambas as políticas 1 e 2 tiveram o efeito negar, a situação será alterada para:

- Os recursos já presentes no grupo de recursos B fora de “eastus” serão marcados como não conformes com a política 2
- Os recursos já presentes no grupo de recursos B fora de “westus” serão marcados como não conformes com a política 1
- Novos recursos na assinatura A não presentes em “westus” são negados pela política 1
- Os novos recursos no grupo de recursos B da assinatura A são negados

Cada atribuição é avaliada individualmente. Assim, não existe chance de um recurso passar por uma brecha nas diferenças de escopo. O resultado de políticas em camadas ou sobreposição de políticas é considerado **cumulativo mais restritivo**. Por exemplo, se as políticas 1 e 2 tivessem efeito deny, um recurso seria bloqueado pelas políticas conflitantes e sobrepostas. Caso ainda precise que o recurso seja criado no escopo de destino, revise as exclusões em cada atribuição para validar que as políticas corretas estão afetando os escopos corretos.

## <a name="next-steps"></a>Próximas etapas

- Revise os exemplos em [amostras da Política do Azure](../samples/index.md)
- Revise a [estrutura de definição de política](definition-structure.md)
- Entender como [criar políticas de forma programática](../how-to/programmatically-create.md)
- Saiba como [obter dados de conformidade](../how-to/getting-compliance-data.md)
- Saiba como [corrigir recursos fora de conformidade](../how-to/remediate-resources.md)
- Examine o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md)
