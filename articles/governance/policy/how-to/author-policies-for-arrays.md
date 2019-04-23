---
title: Políticas de autor para propriedades de matriz em recursos do Azure
description: Aprenda a criar parâmetros de matriz, criar regras para a matriz de expressões de linguagem, avaliar o alias [*] e para acrescentar elementos a uma matriz existente com as regras de definição de política do Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/06/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 38cf6decb8e61768faa9680058f6366e1550ba40
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59793265"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Políticas de autor para propriedades de matriz em recursos do Azure

Propriedades do Gerenciador de recursos do Azure normalmente são definidas como cadeias de caracteres e boolianos. Quando existe uma relação um-para-muitos, propriedades complexas em vez disso, são definidas como matrizes. Na política do Azure, as matrizes são usadas de várias maneiras diferentes:

- O tipo de um [parâmetro de definição de](../concepts/definition-structure.md#parameters), para fornecer várias opções
- Parte de um [regra de política](../concepts/definition-structure.md#policy-rule) usando as condições **na** ou **notIn**
- Parte de uma regra de política que avalia a [ \[ \* \] alias](../concepts/definition-structure.md#understanding-the--alias) para avaliar os cenários específicos, como **None**, **qualquer**, ou  **Todos os**
- No [acrescentar efeito](../concepts/effects.md#append) para substituir ou adicionar a uma matriz existente

Este artigo aborda cada uso pela política do Azure e fornece várias definições de exemplo.

## <a name="parameter-arrays"></a>Matrizes de parâmetros

### <a name="define-a-parameter-array"></a>Definir uma matriz de parâmetros

Definindo um parâmetro como uma matriz permite a flexibilidade de política quando mais de um valor é necessário.
Esta definição de política permite que qualquer local único para o parâmetro **allowedLocations** e o padrão _eastus2_:

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

Como **tipo** foi _cadeia de caracteres_, somente um valor pode ser definido quando atribuir a política. Se essa política for atribuída, os recursos no escopo só são permitidos em uma única região do Azure. A maioria das definições de políticas precisam permitir para obter uma lista das opções aprovadas, como permitir que _eastus2_, _eastus_, e _westus2_.

Para criar a definição de política para permitir que várias opções, use o _array_ **tipo**. A mesma política pode ser reescrita da seguinte maneira:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> Quando uma definição de política for salva, o **tipo** propriedade em um parâmetro não pode ser alterada.

Essa nova definição de parâmetro leva mais de um valor durante a atribuição de política. Com a propriedade de matriz **allowedValues** definido, os valores disponíveis durante a atribuição são ainda mais limitado a lista predefinida de opções. Uso de **allowedValues** é opcional.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Passar valores para uma matriz de parâmetros durante a atribuição

Ao atribuir a política por meio do portal do Azure, um parâmetro de **tipo** _matriz_ é exibido como uma caixa de texto. A dica diz "Use; para separar os valores. (por exemplo, Londres; New York) ". Para passar os valores de local permitido de _eastus2_, _eastus_, e _westus2_ para o parâmetro, use a seguinte cadeia:

`eastus2;eastus;westus2`

O formato do valor do parâmetro é diferente ao usar a CLI do Azure, Azure PowerShell ou a API REST. Os valores são passados por meio de uma cadeia de caracteres JSON que também inclui o nome do parâmetro.

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

Para usar essa cadeia de caracteres com cada SDK, use os seguintes comandos:

- CLI do Azure: Comando [criar atribuição de política az](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) com o parâmetro **param. autom.**
- PowerShell do Azure: Cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) com o parâmetro **PolicyParameter**
- API REST: No _colocar_ [crie](/rest/api/resources/policyassignments/create) operação como parte do corpo da solicitação como o valor da **properties.parameters** propriedade

## <a name="policy-rules-and-arrays"></a>Matrizes e as regras de política

### <a name="array-conditions"></a>Condições de matriz

A regra de política [condições](../concepts/definition-structure.md#conditions) que um _array_
**tipo** do parâmetro pode ser usado com é limitado a `in` e `notIn`. Executar a seguinte definição de política com a condição `equals` como um exemplo:

```json
{
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "equals": "[parameters('allowedLocations')]"
      }
    },
    "then": {
      "effect": "audit"
    }
  },
  "parameters": {
    "allowedLocations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed locations for resources.",
        "displayName": "Allowed locations",
        "strongType": "location"
      }
    }
  }
}
```

Tentativa de criar essa definição de política por meio de leads portal do Azure para um erro como esta mensagem de erro:

- "A política '{GUID}' não pôde ser parametrizada devido a erros de validação. Verifique se os parâmetros de política estão definidos adequadamente. A exceção interna 'avaliação resultado da expressão de linguagem '[parameters('allowedLocations')]' é o tipo 'Array', tipo esperado é 'String' '."

Esperado **tipo** da condição `equals` é _cadeia de caracteres_. Uma vez que **allowedLocations** é definido como **tipo** _matriz_, o mecanismo da diretiva avalia a expressão de linguagem e gera o erro. Com o `in` e `notIn` condição, o mecanismo da diretiva espera que o **tipo** _matriz_ na expressão de linguagem. Para resolver essa mensagem de erro, altere `equals` para um `in` ou `notIn`.

### <a name="evaluating-the--alias"></a>Avaliando o alias [*]

Aliases que tenham **[\*]** anexado ao seu nome de indicar o **tipo** é um _matriz_. Em vez de avaliar o valor de toda a matriz **[\*]** torna possível avaliar cada elemento da matriz. Há três cenários em que isso por avaliação de item é útil: None, quaisquer e todos.

Os gatilhos de mecanismo de diretiva a **efeito** na **, em seguida,** somente quando o **se** regra será avaliada como true.
Esse fato é importante entender no contexto da maneira **[\*]** avalia cada elemento individual da matriz.

A regra de política de exemplo para a tabela de cenário a seguir:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

O **ipRules** matriz é o seguinte para a tabela de cenário a seguir:

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Para cada exemplo de condição abaixo, substitua `<field>` com `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`.

Os seguintes resultados são o resultado da combinação da condição e a regra de política de exemplo e a matriz de valores existentes acima:

|Condição |Resultado |Explicação |
|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Nada |Um elemento da matriz for avaliada como falsa (127.0.0.1! = 127.0.0.1) e outra como true (127.0.0.1! = 192.168.1.1), portanto, o **notEquals** condição é _falso_ e o efeito não é disparado. |
|`{<field>,"notEquals":"10.0.4.1"}` |Efeito da política |Ambos os elementos da matriz são avaliados como true (10.0.4.1! = 127.0.0.1 e 10.0.4.1! = 192.168.1.1), portanto, o **notEquals** condição é _verdadeiro_ e o efeito é disparado. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Efeito da política |Um elemento de matriz é avaliada como true (127.0.0.1 = = 127.0.0.1) e outra como false (127.0.0.1 = = 192.168.1.1), portanto, o **é igual a** condição é _falso_. O operador lógico é avaliada como true (**não** _falso_), portanto, o efeito é disparado. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Efeito da política |Ambos os elementos da matriz é avaliada como false (10.0.4.1 = = 127.0.0.1 e 10.0.4.1 = = 192.168.1.1), portanto, o **é igual a** condição é _falso_. O operador lógico é avaliada como true (**não** _falso_), portanto, o efeito é disparado. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Efeito da política |Um elemento da matriz for avaliada como falsa (127.0.0.1! = 127.0.0.1) e outra como true (127.0.0.1! = 192.168.1.1), portanto, o **notEquals** condição é _falso_. O operador lógico é avaliada como true (**não** _falso_), portanto, o efeito é disparado. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Nada |Ambos os elementos da matriz são avaliados como true (10.0.4.1! = 127.0.0.1 e 10.0.4.1! = 192.168.1.1), portanto, o **notEquals** condição é _verdadeiro_. O operador lógico for avaliada como falsa (**não** _verdadeiro_), portanto, o efeito não é disparado. |
|`{<field>,"Equals":"127.0.0.1"}` |Nada |Um elemento de matriz é avaliada como true (127.0.0.1 = = 127.0.0.1) e outra como false (127.0.0.1 = = 192.168.1.1), portanto, o **é igual a** condição é _falso_ e o efeito não é disparado. |
|`{<field>,"Equals":"10.0.4.1"}` |Nada |Ambos os elementos da matriz é avaliada como false (10.0.4.1 = = 127.0.0.1 e 10.0.4.1 = = 192.168.1.1), portanto, o **é igual a** condição é _falso_ e o efeito não é disparado. |

## <a name="the-append-effect-and-arrays"></a>O efeito de acréscimo e matrizes

O [acrescentar efeito](../concepts/effects.md#append) tem um comportamento diferente, dependendo se o **details.field** é um **[\*]** alias ou não.

- Quando não uma **[\*]** alias, acrescente substitui toda a matriz com o **valor** propriedade
- Quando um **[\*]** alias, acrescentar adiciona o **valor** de matriz de propriedade à existente ou cria a nova matriz

Para obter mais informações, consulte o [acrescentar exemplos](../concepts/effects.md#append-examples).

## <a name="next-steps"></a>Próximas etapas

- Revise os exemplos em [amostras da Política do Azure](../samples/index.md)
- Revise a [estrutura de definição de política](../concepts/definition-structure.md)
- Revisão [Noções básicas sobre os efeitos de política](../concepts/effects.md)
- Entender como [criar políticas de forma programática](programmatically-create.md)
- Saiba como [corrigir recursos fora de conformidade](remediate-resources.md)
- Examine o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md)