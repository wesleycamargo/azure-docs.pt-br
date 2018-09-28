---
title: Entenda a sequência de implantação nos Blueprints do Azure
description: Aprenda sobre o ciclo de vida pelo qual um blueprint passa e detalhes sobre cada estágio.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: c09fb26d8375e08281241aaed3f6f6e30acc755b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955445"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Entenda a sequência de implantação nos Blueprints do Azure

O Blueprint do Azure usa uma **ordem de sequenciamento** para determinar a ordem de criação do recurso ao processar a atribuição de um blueprint. Este artigo explica a ordem de sequenciamento padrão usada, como personalizar o pedido e como o pedido personalizado é processado.

Existem variáveis nos exemplos JSON que você precisa substituir por seus próprios valores:

- `{YourMG}` - Substitua pelo nome do seu grupo de gerenciamento

## <a name="default-sequencing-order"></a>Ordem de sequenciamento padrão

Se o blueprint não contiver nenhuma diretriz para a ordem de implementação de artefatos ou a diretiva for nula, a seguinte ordem será usada:

- Designação de função de **nível de assinatura** artefatos classificados por nome de artefato
- Designação de política do **nível de assinatura** artefatos classificados pelo nome do artefato
- Nível de assinatura **Artefatos do modelo** do Azure Resource Manager classificados por nome de artefato
- **Artefatos do grupo de recursos** (incluindo artefatos derivados) classificados por nome de espaço reservado

Em cada artefato do grupo de **recursos processado** que é processado, a seguinte ordem de sequência é usada para artefatos a serem criados dentro desse grupo de recursos:

- Filho de grupo de recursos **atribuição de função** artefatos classificados por nome de artefato
- Filho de grupo de recursos **atribuição de política** artefatos classificados por nome de artefato
- Filho do grupo de recursos **Artefatos do modelo do Azure Resource Manager** classificados por nome de artefato

## <a name="customizing-the-sequencing-order"></a>Personalizando o pedido de sequenciamento

Ao compor grandes esquemas, pode ser necessário que um recurso seja criado em uma ordem específica em relação a outro recurso. O padrão de uso mais comum disso é quando um blueprint inclui vários modelos do Azure Resource Manager. Blueprints lida com isso permitindo que a ordem de sequenciamento seja definida.

Isso é feito definindo uma propriedade `dependsOn` no JSON. Apenas o blueprint (para grupos de recursos) e objetos de artefato suportam essa propriedade. `dependsOn` é uma matriz de cadeia de caracteres de nomes de artefatos que o artefato específico precisa ser criado antes de ser criado.

### <a name="example---blueprint-with-ordered-resource-group"></a>Exemplo - blueprint com grupo de recursos ordenado

Este é um exemplo de blueprint com um grupo de recursos que definiu uma ordem de sequenciamento customizada, declarando um valor para `dependsOn`, junto com um grupo de recursos padrão. Nesse caso, o artefato denominado **assignPolicyTags** será processado antes do grupo de recursos **ordered-rg**.
**standard-rg** será processado de acordo com a ordem de sequenciamento padrão.

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

Este é um artefato de política de exemplo que depende de um modelo do Azure Resource Manager. Por padrão, um artefato de política seria criado antes do modelo do Azure Resource Manager. Isso permite que o artefato de política aguarde o modelo do Azure Resource Manager ser criado.

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

Durante o processo de criação, uma classificação topológica é usada para criar o gráfico de dependência do blueprint e seus artefatos. Isso garante que vários níveis de dependência entre grupos de recursos e artefatos possam ser suportados.

Se uma dependência for declarada no blueprint ou um artefato que não alteraria a ordem padrão, nenhuma alteração será feita na ordem de sequenciamento. Exemplos disso são um grupo de recursos que depende de uma atribuição de diretiva filho 'standard-rg' de diretiva ou grupo de recursos que depende da atribuição de função filho 'standard-rg' do grupo de recursos. Em ambos os casos, o `dependsOn` não teria alterado a ordem de sequenciamento padrão e nenhuma alteração seria feita.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [ciclo de vida do plano gráfico](lifecycle.md)
- Entenda como usar [parâmetros estáticos e dinâmicos](parameters.md)
- Saiba como fazer uso de [especificações técnicas de recurso de bloqueio](resource-locking.md)
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md)
- Resolver problemas durante a atribuição de um plano gráfico com [gerais de solução de problemas](../troubleshoot/general.md)