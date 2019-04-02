---
title: Determinar as causas de não conformidade
description: Quando um recurso não está em conformidade, há muitas razões possíveis. Saiba mais descobrir o que causou a não conformidade.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 1121bdb379265ef592a26a8a31a90d402e529cc8
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58802738"
---
# <a name="determine-causes-of-non-compliance"></a>Determinar as causas de não conformidade

Quando um recurso do Azure é determinado para estar fora de conformidade para uma regra de política, é útil entender qual parte da regra que o recurso não está em conformidade com o. Também é útil para entender quais alterações alterado um recurso anteriormente compatível para torná-lo fora de conformidade. Há duas maneiras para encontrar essas informações:

> [!div class="checklist"]
> - [Detalhes de conformidade](#compliance-details)
> - [Histórico de alterações (versão prévia)](#change-history-preview)

## <a name="compliance-details"></a>Detalhes de conformidade

Quando um recurso não está em conformidade, os detalhes de conformidade para esse recurso estão disponíveis na **conformidade com a política** página. O painel de detalhes de conformidade inclui as seguintes informações:

- Detalhes do recurso como a ID de nome, tipo, local e recursos
- Estado de conformidade e o carimbo de hora da última avaliação para a atribuição de política atual
- Uma lista dos _motivos_ para o recurso não-conformidade

> [!IMPORTANT]
> Como os detalhes de conformidade para um _incompatível_ recurso mostra o valor atual de propriedades neste recurso, o usuário deve ter **ler** operação para o **tipo** de recurso. Por exemplo, se o _incompatível_ recurso estiver **Compute/virtualmachines** e em seguida, o usuário deve ter o **Microsoft.Compute/virtualMachines/read** operação. Se o usuário não tiver a operação necessária, é exibido um erro de acesso.

Para exibir os detalhes de conformidade, siga estas etapas:

1. Inicie o serviço de Azure Policy no portal do Azure clicando em**Todos os serviços**, em seguida pesquisando e selecionando **Política**.

1. No **visão geral** ou **conformidade** , selecione uma política em um **estado de conformidade** que é _incompatível_.

1. Sob o **conformidade do recurso** guia da **conformidade com a política** da página, clique com botão direito ou selecione as reticências de um recurso em um **estado de conformidade** que é  _Fora de conformidade_. Em seguida, selecione **exibir detalhes de conformidade**.

   ![Opção de exibição de detalhes de conformidade](../media/determine-non-compliance/view-compliance-details.png)

1. O **detalhes de conformidade** painel exibe informações de avaliação mais recente do recurso para a atribuição de política atual. Neste exemplo, o campo **Microsoft.Sql/servers/version** for _12.0_ enquanto a definição de política esperada _14.0_. Se o recurso não está em conformidade por vários motivos, cada um é listado nesse painel.

   ![Painel de detalhes de conformidade e os motivos para não conformidade](../media/determine-non-compliance/compliance-details-pane.png)

   Para um **auditIfNotExists** ou **deployIfNotExists** definição de política, os detalhes incluem o **details.type** propriedade e qualquer propriedade opcional. Para obter uma lista, consulte [propriedades auditIfNotExists](../concepts/effects.md#auditifnotexists-properties) e [deployIfNotExists propriedades](../concepts/effects.md#deployifnotexists-properties). **Avaliado pela última vez resource** é um recurso relacionado do **detalhes** seção da definição.

   Exemplo parcial **deployIfNotExists** definição:

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   ![Painel de detalhes de conformidade - * ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> Para proteger dados, quando um valor de propriedade é uma _segredo_ o valor atual exibe asteriscos.

Esses detalhes explicam por que um recurso atualmente não está em conformidade, mas não mostram quando a alteração foi feita para o recurso que fez com que ele ficar fora de conformidade. Para obter essas informações, consulte [(visualização) do histórico de alterações](#change-history-preview) abaixo.

### <a name="compliance-reasons"></a>Motivos de conformidade

A matriz a seguir mapeia cada possível _motivo_ para o responsável [condição](../concepts/definition-structure.md#conditions) na definição de política:

|Motivo | Condição |
|-|-|
|O valor atual deve conter o valor de destino como uma chave. |containsKey ou **não** notContainsKey |
|O valor atual deve conter o valor de destino. |contém ou **não** notContains |
|O valor atual deve ser igual ao valor de destino. |é igual a ou **não** notEquals |
|O valor atual deve existir. |exists |
|O valor atual deve estar no valor de destino. |em ou **não** notIn |
|O valor atual deve ser como o valor de destino. |como ou **não** notLike |
|O valor atual precisa corresponder ao valor de destino diferenciando maiúsculas de minúsculas. |corresponder ou **não** notMatch |
|O valor atual precisa corresponder ao valor de destino sem diferenciar maiúsculas de minúsculas. |matchInsensitively ou **não** notMatchInsensitively |
|O valor atual não deve conter o valor de destino como uma chave. |notContainsKey ou **não** containsKey|
|O valor atual não deve conter o valor de destino. |notContains ou **não** contém |
|O valor atual não deve ser igual ao valor de destino. |notEquals ou **não** é igual a |
|O valor atual não deve existir. |**não** existe  |
|O valor atual não deve estar no valor de destino. |notIn ou **não** em |
|O valor atual não deve ser como o valor de destino. |notLike ou **não** como |
|O valor atual não pode corresponder ao valor de destino diferenciando maiúsculas de minúsculas. |notMatch ou **não** corresponder |
|O valor atual não pode corresponder ao valor de destino sem diferenciar maiúsculas de minúsculas. |notMatchInsensitively ou **não** matchInsensitively |
|Não há recursos relacionados que correspondam aos detalhes de efeito na definição de política. |Um recurso do tipo definido em **then.details.type** e relacionados ao recurso definido na **se** parte da regra de política não existe. |

## <a name="change-history-preview"></a>Alterar histórico (Versão prévia)

Como parte de uma nova **visualização pública**, dos últimos 14 dias de alteração de histórico está disponível para todos os recursos do Azure que dão suporte ao [concluir exclusão do modo](../../../azure-resource-manager/complete-mode-deletion.md). O histórico de alterações fornece detalhes sobre quando uma alteração foi detectada e uma _comparação visual_ para cada alteração. A detecção de alteração é disparada quando as propriedades do Gerenciador de recursos são adicionadas, removidas ou alteradas.

1. Inicie o serviço de Azure Policy no portal do Azure clicando em**Todos os serviços**, em seguida pesquisando e selecionando **Política**.

1. Sobre o **visão geral** ou **conformidade** , selecione uma política em qualquer **estado de conformidade**.

1. Sob o **conformidade do recurso** guia da **conformidade com a política** , selecione um recurso.

1. Escolha a guia **Histórico de Alterações (versão prévia)** na página **Conformidade do Recurso**. Se houver uma lista de alterações detectadas, ela será exibida.

   ![Guia de histórico de alterações de política na página de conformidade do recurso](../media/determine-non-compliance/change-history-tab.png)

1. Escolha uma das alterações detectadas. O _comparação visual_ para o recurso é apresentado na **histórico de alterações** página.

   ![Histórico de alteração de política Visual Diff na página de histórico de alteração](../media/determine-non-compliance/change-history-visual-diff.png)

A _comparação visual_ ajuda a identificar alterações em um recurso. Não podem estar relacionadas as alterações detectadas para o estado atual de conformidade do recurso.

## <a name="next-steps"></a>Próximas etapas

- Revise os exemplos em [amostras da Política do Azure](../samples/index.md)
- Revise a [estrutura de definição de política](../concepts/definition-structure.md)
- Revisão [Noções básicas sobre os efeitos de política](../concepts/effects.md)
- Entender como [criar políticas de forma programática](programmatically-create.md)
- Saiba como [obter dados de conformidade](getting-compliance-data.md)
- Saiba como [corrigir recursos fora de conformidade](remediate-resources.md)
- Examine o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md)