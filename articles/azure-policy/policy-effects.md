---
title: Compreendendo os efeitos do Azure Policy
description: A definição do Azure Policy tem vários efeitos que determinam como a conformidade é gerenciada e relatada.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 17ad631e2441e4b8d6314557c17be143fd2f3de0
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39248719"
---
# <a name="understanding-policy-effects"></a>Compreendendo os efeitos da política

Cada definição de política no Azure Policy tem um único efeito que determina o que acontece durante o exame, quando o segmento **se** da regra de política é avaliado como correspondendo ao recurso que está sendo examinado. Os efeitos também podem se comportar de modo diferente caso sejam para um novo recurso, um recurso atualizado ou um recurso existente.

Atualmente, há cinco efeitos com suporte em uma definição de política:

- Acrescentar
- Audit
- AuditIfNotExists
- Negar
- DeployIfNotExists (disponível apenas para **internos** políticas)

## <a name="order-of-evaluation"></a>Ordem de avaliação

Quando é feita uma solicitação para criar ou atualizar um recurso por meio do Azure Resource Manager, a política processa vários efeitos antes de enviar a solicitação para o provedor de recursos apropriado.
Isso impede o processamento desnecessário por um provedor de recursos quando um recurso não atende aos controles de governança projetados pela política. A política cria uma lista de todas as definições de política atribuídas por uma política ou atribuição de iniciativa cujo escopo se aplica (menos exclusões) ao recurso e se prepara para avaliar o recurso em cada definição.

- **Acrescentar** é avaliado primeiro. Como o efeito acrescentar pode alterar a solicitação, a alteração feita ao acrescentar pode impedir uma auditoria ou negar o efeito do gatilho.
- **Negar** é avaliada. Ao avaliar o efeito negar antes da auditoria, evita-se o log duplo de um recurso indesejado.
- **Auditoria** é avaliada antes de a solicitação ir para o provedor de recursos.

Depois que a solicitação é enviada ao provedor de recursos e ele retorna um código de status de êxito, **AuditIfNotExists** e **DeployIfNotExists** são avaliadas para determinar se é preciso fazer o acompanhamento do log ou da ação de conformidade.

## <a name="append"></a>Acrescentar

O efeito acrescentar é usado para adicionar outros campos ao recurso solicitado durante a criação ou atualização. Pode ser útil para adicionar marcas em recursos, como costCenter ou especificar IPs permitido para um recurso de armazenamento.

### <a name="append-evaluation"></a>Avaliação do efeito acrescentar

Conforme mencionado, o efeito acrescentar é avaliado antes de a solicitação ser processada por um provedor de recursos durante a criação ou atualização de um recurso. O efeito acrescentar adiciona o campos ao recurso quando a condição **se** da regra de política for atendida. Se o efeito de acrescentar substituir um valor na solicitação original por um valor diferente, ele atua como um efeito negar e rejeita a solicitação.

Quando uma definição de política usando o efeito acrescentar é executada como parte de um ciclo de avaliação, ela não faz alterações a recursos já existentes. Em vez disso, ela marca qualquer recurso que atende a condição **se** como não conforme.

### <a name="append-properties"></a>Propriedades do efeito acrescentar

Um efeito acrescentar tem apenas uma matriz **detalhes**, que é necessária. Como **detalhes** é uma matriz, pode levar um único par **valor do campo** ou múltiplos. Consulte [definição de política](policy-definition.md#fields) para saber a lista de campos aceitáveis.

### <a name="append-examples"></a>Exemplos do efeito acrescentar

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

Exemplo 2: pares **campo/valor** múltiplos para acrescentar um conjunto de marcas.

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

Exemplo 3: par **campo/valor** exclusivo usando um [alias](policy-definition.md#aliases) com uma matriz **valor** para definir as regras IP em uma conta de armazenamento.

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

O efeito negar é usado para impedir uma solicitação de recurso que não corresponde aos padrões desejados por meio de uma definição de política e leva à falha da solicitação.

### <a name="deny-evaluation"></a>Avaliação do efeito negar

Quando criar ou atualizar um recurso, o efeito negar impede a solicitação antes de ser enviada ao provedor de recursos. A solicitação é retornada como um erro 403 (Proibido). No portal, o Proibido pode ser exibido como um status sobre a implantação foi impedida devido à atribuição de política.

Durante um ciclo de avaliação, as definições de política com um efeito negar que correspondam a recursos são marcadas como não conformes, mas nenhuma ação é executada no recurso.

### <a name="deny-properties"></a>Propriedades do efeito negar

O efeito negar não têm propriedades adicionais para uso na condição **então** da definição de política.

### <a name="deny-example"></a>Exemplo do efeito negar

Exemplo: usando o efeito negar.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Audit

O efeito auditoria é usado para criar um evento de aviso no log de atividade quando um recurso não conforme é avaliado, mas não interrompe a solicitação.

### <a name="audit-evaluation"></a>Avaliação do efeito auditoria

O efeito auditoria é o último a ser executado durante a criação ou atualização de um recurso antes de o recurso ser enviado para o provedor de recursos. O efeito auditoria funciona da mesma forma para uma solicitação de recurso e um ciclo de avaliação, executando uma operação `Microsoft.Authorization/policies/audit/action` para o log de atividades. Em ambos os casos, o recurso é marcado como não compatível.

### <a name="audit-properties"></a>Propriedades do efeito auditoria

O efeito auditoria não têm propriedades adicionais para uso na condição **então** da definição de política.

### <a name="audit-example"></a>Exemplo do efeito auditoria

Exemplo: usando o efeito auditoria.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

O efeito AuditIfNotExists habilita a auditoria em um recurso que corresponde à condição **se**, mas não tem os componentes especificados nos **detalhes** da condição **então**.

### <a name="auditifnotexists-evaluation"></a>Avaliação do efeito AuditIfNotExists

O efeito AuditIfNotExists é executado depois de um provedor de recursos ter tratado uma solicitação de criação ou atualização para um recurso e ter retornado um código de status de êxito. O efeito é acionado se não existirem recursos relacionados ou se os recursos definidos por **ExistenceCondition** não são avaliadas como verdadeiros. Quando o efeito é disparado, uma operação `Microsoft.Authorization/policies/audit/action` para o log de atividades é executada a da mesma maneira como o efeito auditoria. Quando disparado, o recurso que atendeu à condição **se** é o recurso marcado como não compatível.

### <a name="auditifnotexists-properties"></a>Propriedades do efeito AuditIfNotExists

A propriedade **detalhes** dos efeitos AuditIfNotExists tem todas as subpropriedades que definem os recursos relacionados a serem correspondidos.

- **Tipo** [obrigatório]
  - Especifica o tipo do recurso relacionado a ser correspondido.
  - Começa tentando buscar um recurso sob o recurso de condição **se**, depois consulta dentro do mesmo grupo de recursos como o recurso de condição **se**.
- **Nome** (opcional)
  - Especifica o nome exato do recurso a ser correspondido e faz com que a política busque um recurso específico em vez de todos os recursos do tipo especificado.
- **ResourceGroupName** (opcional)
  - Permite que a correspondência do recurso relacionado venha de um grupo de recursos diferente.
  - Não se aplica se **tipo** for um recurso que estaria sob o recurso de condição **se**.
  - O padrão é o grupo de recursos do recurso de condição **se**.
- **ExistenceScope** (opcional)
  - Os valores permitidos são _Assinatura_ e _ResourceGroup_.
  - Define o escopo de onde buscar o recurso relacionado com o qual corresponder.
  - Não se aplica se **tipo** for um recurso que estaria sob o recurso de condição **se**.
  - Para _ResourceGroup_, limitaria para o grupo de recursos do recurso da condição **se** ou o grupo de recursos especificado em **ResourceGroupName**.
  - Para _Assinatura_, consulta a assinatura inteira para o recurso relacionado.
  - O padrão é _ResourceGroup_.
- **ExistenceCondition** (opcional)
  - Se não for especificado, qualquer recurso relacionado de **tipo** cumpre o efeito e não aciona a auditoria.
  - Usa a mesma linguagem como a regra de política para a condição **se**, mas é avaliada em relação a cada recurso relacionado individualmente.
  - Se qualquer recurso relacionado correspondente for avaliado como verdadeiro, o efeito é atendido e não aciona a auditoria.
  - Pode usar [field()] para verificar a equivalência com valores na condição **se**.
  - Por exemplo, pode ser usado para validar que o recurso pai (na condição **se**) está no mesmo local que recursos como o recurso relacionado correspondente.

### <a name="auditifnotexists-example"></a>Exemplo de efeito AuditIfNotExists

Exemplo: avalia máquinas virtuais para determinar se a extensão antimalware existe e faz auditorias quando ausente.

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

> [!WARNING]
> DeployIfNotExists só está disponível para **políticas internas**.

### <a name="deployifnotexists-evaluation"></a>Avaliação do efeito DeployIfNotExists

O efeito DeployIfNotExists também é executado depois de um provedor de recursos ter tratado uma solicitação de criação ou atualização para um recurso e ter retornado um código de status de êxito. O efeito é acionado se não existirem recursos relacionados ou se os recursos definidos por **ExistenceCondition** não são avaliadas como verdadeiros. Quando o efeito é disparado, é executada uma implantação de modelo.

Durante um ciclo de avaliação, as definições de política com um efeito DeployIfNotExists que correspondam a recursos são marcadas como não conformes, mas nenhuma ação é executada no recurso.

### <a name="deployifnotexists-properties"></a>Propriedades do efeito DeployIfNotExists

A propriedade **detalhes** dos efeitos DeployIfNotExists tem todas as subpropriedades que definem os recursos relacionados a serem correspondidos e a implantação de modelo a ser executada.

- **Tipo** [obrigatório]
  - Especifica o tipo do recurso relacionado a ser correspondido.
  - Começa tentando buscar um recurso sob o recurso de condição **se**, depois consulta dentro do mesmo grupo de recursos como o recurso de condição **se**.
- **Nome** (opcional)
  - Especifica o nome exato do recurso a ser correspondido e faz com que a política busque um recurso específico em vez de todos os recursos do tipo especificado.
- **ResourceGroupName** (opcional)
  - Permite que a correspondência do recurso relacionado venha de um grupo de recursos diferente.
  - Não se aplica se **tipo** for um recurso que estaria sob o recurso de condição **se**.
  - O padrão é o grupo de recursos do recurso de condição **se**.
  - Se uma implantação de modelo é executada, ele será implantado no grupo de recursos desse valor.
- **ExistenceScope** (opcional)
  - Os valores permitidos são _Assinatura_ e _ResourceGroup_.
  - Define o escopo de onde buscar o recurso relacionado com o qual corresponder.
  - Não se aplica se **tipo** for um recurso que estaria sob o recurso de condição **se**.
  - Para _ResourceGroup_, limitaria para o grupo de recursos do recurso da condição **se** ou o grupo de recursos especificado em **ResourceGroupName**.
  - Para _Assinatura_, consulta a assinatura inteira para o recurso relacionado.
  - O padrão é _ResourceGroup_.
- **ExistenceCondition** (opcional)
  - Se não for especificado, qualquer recurso relacionado de **tipo** cumpre o efeito e não aciona a implantação.
  - Usa a mesma linguagem como a regra de política para a condição **se**, mas é avaliada em relação a cada recurso relacionado individualmente.
  - Se qualquer recurso relacionado correspondente for avaliado como verdadeiro, o efeito é atendido e não aciona a implantação.
  - Pode usar [field()] para verificar a equivalência com valores na condição **se**.
  - Por exemplo, pode ser usado para validar que o recurso pai (na condição **se**) está no mesmo local que recursos como o recurso relacionado correspondente.
- **Implantação** [obrigatória]
  - Essa propriedade deve conter a implantação de modelo completo que seria passada para o API PUT de `Microsoft.Resources/deployments`. Para obter mais informações, consulte a [API REST de implantações](/rest/api/resources/deployments).

  > [!NOTE]
  > Todas as funções dentro da propriedade **Implantação** são avaliadas como componentes do modelo, não da política. A exceção é a propriedade **parâmetros**, que passa os valores da política para o modelo. O **valor** nesta seção em um nome de parâmetro de modelo é usado para executar essa passagem de valor (consulte _fullDbName_ no exemplo do efeito DeployIfNotExists).

### <a name="deployifnotexists-example"></a>Exemplo do efeito DeployIfNotExists

Exemplo: avalia os bancos de dados do SQL Server para determinar se transparentDataEncryption está habilitado. Se não estiver, é executada uma implantação para habilitá-lo.

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

Um recurso pode ser afetado por várias atribuições. Essas atribuições podem estar no mesmo escopo (recurso específico, grupo de recursos, assinatura ou grupo de gerenciamento) ou em escopos diferentes. Também é provável que cada uma dessas atribuições tenha um efeito diferente definido. De qualquer forma, a condição e o efeito de cada política (atribuída diretamente ou como parte de uma iniciativa) são avaliados de maneira independente. Por exemplo, se a política tiver uma condição que restrinja o local de recurso para assinatura A para ser criada apenas em “westus” com o efeito de negação e a política tiver 2, haverá uma condição que restringe o  local de recurso para o gurpo de recursos B (que está na assinatura A) para apenas ser criada em “eastus” com o efeito de auditoria são ambas atribuídas, o resultado seria:

- Qualquer recurso já presente no grupo de recursos B em “eastus” está em conformidade com a política 2, mas está marcado como não conforme com a política 1.
- Qualquer recurso já presente no grupo de recursos B não em “eastus” será marcado como não conforme com a política 2 e também pode ser marcado como não conforme com a política 1 se não estiver em “westus”.
- Qualquer novo recurso na assinatura A não presente em “westus” seria negado pela política 1.
- Novos recursos na assinatura A/grupo de recursos B em “westus” podem ser marcados como não conforme na política 2, mas podem ser criados (a conformidade com as políticas 1 e 2 é do efeito auditoria, não do efeito negar).

Se ambas as políticas 1 e 2 tiveram o efeito negar, a situação seria alterada para:

- Qualquer recurso já presente no grupo de recursos B não em “eastus” será marcado como não conforme com a política 2.
- Qualquer recurso já presente no grupo de recursos B não em “westus” será marcado como não conforme com a política 1.
- Qualquer novo recurso na assinatura A não presente em “westus” seria negado pela política 1.
- Qualquer novo recurso na assinatura A/grupo de recursos B seria negado (uma vez que sua localização nunca poderia atender às política 1 e 2).

Como cada atribuição é avaliada individualmente, não existe uma oportunidade para que um recurso passe por uma lacuna devido a diferenças no escopo. Portanto, o resultado de políticas em camadas ou sobreposição de políticas é considerado **cumulativo mais restritivo**. Em outras palavras, um recurso que você deseja criar poderá ser bloqueado devido a políticas sobrepostas e conflitantes, como o exemplo acima, se ambas as políticas 1 e 2 tivessem um efeito negar. Caso ainda precise que o recurso seja criado no escopo de destino, revise as exclusões em cada atribuição para garantir que as políticas corretas estão afetando os escopos corretos.

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma compreensão mais profunda dos efeitos de definição de política, examine os exemplos de política:

- Revisar mais exemplos em [exemplos do Azure Policy](json-samples.md).
