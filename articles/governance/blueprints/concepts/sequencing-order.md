---
title: Entender a ordem da sequência de implantação
description: Aprenda sobre o ciclo de vida de um blueprint e os detalhes de cada estágio.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 11/12/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: bd12aabf0ca8f82261e6b3c677d7306ee46c4171
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53308610"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Entenda a sequência de implantação nos Blueprints do Azure

O Blueprint do Azure usa uma **ordem de sequenciamento** para determinar a ordem de criação do recurso ao processar a atribuição de um blueprint. Este artigo o guiará pelos seguintes conceitos:

- A ordem de sequenciamento padrão usada
- Como personalizar o pedido
- Como o pedido personalizado é processado

Existem variáveis nos exemplos JSON que você precisa substituir por seus próprios valores:

- `{YourMG}` - Substitua pelo nome do seu grupo de gerenciamento

## <a name="default-sequencing-order"></a>Ordem de sequenciamento padrão

Se o blueprint não contiver nenhuma diretriz para a ordem de implementação de artefatos ou a diretiva for nula, a seguinte ordem será usada:

- Designação de função de **nível de assinatura** artefatos classificados por nome de artefato
- Designação de política do **nível de assinatura** artefatos classificados pelo nome do artefato
- Nível de assinatura **Artefatos do modelo** do Azure Resource Manager classificados por nome de artefato
- **Artefatos do grupo de recursos** (incluindo artefatos derivados) classificados por nome de espaço reservado

Em cada artefato do **grupo de recursos**, a seguinte ordem de sequência é usada para artefatos a serem criados dentro desse grupo de recursos:

- Filho de grupo de recursos **atribuição de função** artefatos classificados por nome de artefato
- Filho de grupo de recursos **atribuição de política** artefatos classificados por nome de artefato
- Filho do grupo de recursos **Artefatos do modelo do Azure Resource Manager** classificados por nome de artefato

## <a name="customizing-the-sequencing-order"></a>Personalizando o pedido de sequenciamento

Ao compor grandes esquemas, pode ser necessário que recursos sejam criados em uma ordem específica. O padrão de uso mais comum desse cenário é quando um blueprint inclui vários modelos do Azure Resource Manager. Blueprints lida com esse padrão permitindo que a ordem de sequenciamento seja definida.

A ordenação é realizada definindo uma propriedade `dependsOn` no JSON. Apenas o blueprint (para grupos de recursos) e objetos de artefato suportam essa propriedade. `dependsOn` é uma matriz de cadeia de caracteres de nomes de artefatos que o artefato específico precisa ser criado antes de ser criado.

> [!NOTE]
> Artefatos de **grupo de recursos** dão suporte para a propriedade `dependsOn`, mas não podem ser o destino de um `dependsOn` por qualquer tipo de artefato.

### <a name="example---blueprint-with-ordered-resource-group"></a>Exemplo - blueprint com grupo de recursos ordenado

Este blueprint de exemplo possui um grupo de recursos que definiu uma ordem de sequenciamento customizada, declarando um valor para `dependsOn`, junto com um grupo de recursos padrão. Nesse caso, o artefato denominado **assignPolicyTags** será processado antes do grupo de recursos **ordered-rg**. **standard-rg** será processado de acordo com a ordem de sequenciamento padrão.

```json
{
    "properties": {
        "description": "Example blueprint with custom sequencing order",
        "resourceGroups": {
            "ordered-rg": {
                "dependsOn": [
                    "assignPolicyTags"
                ],
                "metadata": {
                    "description": "Resource Group that waits for 'assignPolicyTags' creation"
                }
            },
            "standard-rg": {
                "metadata": {
                    "description": "Resource Group that follows the standard sequence ordering"
                }
            }
        },
        "targetScope": "subscription"
    },
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint",
    "type": "Microsoft.Blueprint/blueprints",
    "name": "mySequencedBlueprint"
}
```

### <a name="example---artifact-with-custom-order"></a>Exemplo - artefato com ordem personalizada

Este exemplo é um artefato de política que depende de um modelo do Azure Resource Manager. Por padrão, um artefato de política seria criado antes do modelo do Azure Resource Manager. Essa ordenação permite que o artefato de política aguarde o modelo do Azure Resource Manager ser criado.

```json
{
    "properties": {
        "displayName": "Assigns an identifying tag",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "resourceGroup": "standard-rg",
        "dependsOn": [
            "customTemplate"
        ]
    },
    "kind": "policyAssignment",
    "id": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/mySequencedBlueprint/artifacts/assignPolicyTags",
    "type": "Microsoft.Blueprint/artifacts",
    "name": "assignPolicyTags"
}
```

## <a name="processing-the-customized-sequence"></a>Processando a sequência customizada

Durante o processo de criação, uma classificação topológica é usada para criar o gráfico de dependência dos artefatos de blueprints. A verificação assegura que cada nível de dependência entre grupos de recursos e artefatos seja suportado.

Se uma dependência de artefato for declarada que não alteraria a ordem padrão, nenhuma alteração será feita. Um exemplo é um grupo de recursos que depende de uma política no nível da assinatura. Outro exemplo é uma atribuição de política filho 'standard-rg' do grupo de recursos que depende da atribuição de função filho 'standard-rg' do grupo de recursos. Em ambos os casos, o `dependsOn` não teria alterado a ordem de sequenciamento padrão e nenhuma alteração seria feita.

## <a name="next-steps"></a>Próximas etapas

- Aprenda sobre o [ciclo de vida do blueprint](lifecycle.md)
- Entenda como usar [parâmetros estáticos e dinâmicos](parameters.md)
- Saiba como fazer uso de [especificações técnicas de recurso de bloqueio](resource-locking.md)
- Saiba como [atualizar atribuições existentes](../how-to/update-existing-assignments.md)
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md)