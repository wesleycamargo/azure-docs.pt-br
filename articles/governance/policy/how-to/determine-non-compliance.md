---
title: Determinar as causas de não conformidade
description: Quando um recurso não está em conformidade, há muitas razões possíveis. Saiba mais descobrir o que causou a não conformidade.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 2f856e9c42b26d4e286493e2eb5d019a8cff6c23
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64868692"
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

## <a name="compliance-details-for-guest-configuration"></a>Detalhes de conformidade para a configuração do convidado

Para _auditar_ diretivas na _configuração do convidado_ categoria, pode haver várias configurações avaliadas dentro da VM e você precisará exibir detalhes por configuração. Por exemplo, se você estiver fazendo uma auditoria para obter uma lista de aplicativos instalados e o status de atribuição é _incompatível_, você precisará saber quais aplicativos específicos estão ausentes.

Você também não pode ter acesso ao entrar na VM diretamente, mas você precisa relatar por que a VM está _incompatível_. Por exemplo, você pode auditar que VMs ingressaram no domínio correto e incluem a participação no domínio atual nos detalhes do relatório.

### <a name="azure-portal"></a>Portal do Azure

1. Inicie o serviço de Azure Policy no portal do Azure clicando em**Todos os serviços**, em seguida pesquisando e selecionando **Política**.

1. Sobre o **visão geral** ou **conformidade** , selecione uma atribuição de política para qualquer iniciativa que contém uma definição de política de configuração do convidado que _incompatível_.

1. Selecione uma _auditar_ diretiva na iniciativa que _incompatível_.

   ![Exibir detalhes da definição de auditoria](../media/determine-non-compliance/guestconfig-audit-compliance.png)

1. Sobre o **conformidade do recurso** guia, as seguintes informações são fornecidas:

   - **Nome** -o nome das atribuições de configuração de convidado.
   - **Recurso pai** -a máquina virtual em um _incompatível_ estado para a atribuição de convidado configuração selecionada.
   - **Tipo de recurso** - o _guestConfigurationAssignments_ nome completo.
   - **Avaliado pela última vez** – a última vez em que o serviço de configuração de convidado notificado sobre a política do Azure sobre o estado da máquina virtual de destino.

   ![Exibir detalhes de conformidade.](../media/determine-non-compliance/guestconfig-assignment-view.png)

1. Selecione o nome da atribuição de configuração de convidado na **nome** coluna para abrir o **conformidade do recurso** página.

1. Selecione o **recurso de exibição** botão na parte superior da página para abrir o **convidado atribuição** página.

O **atribuição de convidados** página exibe todos os detalhes de conformidade disponíveis. Cada linha no modo de exibição representa uma avaliação que foi executada dentro da máquina virtual. No **motivo** coluna, uma frase que descreve por que é a atribuição de convidado _incompatível_ é mostrado. Por exemplo, se você estiver fazendo uma auditoria que VMs devem ser associadas a um domínio, o **motivo** coluna exibiria o texto, incluindo a associação de domínio atual.

![Exibir detalhes de conformidade.](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

Você também pode exibir detalhes de conformidade do Azure PowerShell. Primeiro, verifique se você tem o módulo de configuração do convidado instalado.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

Você pode exibir o status atual de todas as atribuições de convidado para uma VM usando o seguinte comando:

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

Para exibir apenas os _motivo_ frase que descreve por que a VM está _incompatível_, retornar apenas a propriedade filho do motivo.

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

Você também poderá gerar um histórico de conformidade para atribuições de convidado no escopo para a máquina virtual. A saída desse comando inclui os detalhes de cada relatório para a VM.

> [!NOTE]
> A saída pode retornar um grande volume de dados. É recomendável armazenar a saída em uma variável.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname>
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
[Preview]: Audit that an application is installed inside Windows VMs      NonCompliant                       02/10/2019 12:00:38 PM 02/10/2019 12:00:41 PM VM01  ../17fg0...
<truncated>
```

Para simplificar este modo de exibição, use o **ShowChanged** parâmetro. A saída desse comando inclui apenas os relatórios que seguido de uma alteração no status de conformidade.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname> -ShowChanged
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/10/2019 10:00:38 PM 02/10/2019 10:00:41 PM VM01  ../12ab0...
Audit that an application is installed inside Windows VMs.                Compliant                          02/09/2019 11:00:38 AM 02/09/2019 11:00:39 AM VM01  ../e3665...
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/09/2019 09:00:20 AM 02/09/2019 09:00:23 AM VM01  ../15ze1...
```

## <a name="a-namechange-historychange-history-preview"></a><a name="change-history"/>Histórico de alterações (versão prévia)

Como parte de uma nova **visualização pública**, dos últimos 14 dias de histórico de alterações estão disponíveis para todos os recursos do Azure que dão suporte ao [concluir exclusão do modo](../../../azure-resource-manager/complete-mode-deletion.md). O histórico de alterações fornece detalhes sobre quando uma alteração foi detectada e uma _comparação visual_ para cada alteração. A detecção de alteração é disparada quando as propriedades do Gerenciador de recursos são adicionadas, removidas ou alteradas.

1. Inicie o serviço de Azure Policy no portal do Azure clicando em**Todos os serviços**, em seguida pesquisando e selecionando **Política**.

1. Sobre o **visão geral** ou **conformidade** , selecione uma política em qualquer **estado de conformidade**.

1. Sob o **conformidade do recurso** guia da **conformidade com a política** , selecione um recurso.

1. Escolha a guia **Histórico de Alterações (versão prévia)** na página **Conformidade do Recurso**. Se houver uma lista de alterações detectadas, ela será exibida.

   ![Guia de histórico de alterações de política na página de conformidade do recurso](../media/determine-non-compliance/change-history-tab.png)

1. Escolha uma das alterações detectadas. O _comparação visual_ para o recurso é apresentado na **histórico de alterações** página.

   ![Histórico de alteração de política Visual Diff na página de histórico de alteração](../media/determine-non-compliance/change-history-visual-diff.png)

A _comparação visual_ ajuda a identificar alterações em um recurso. Não podem estar relacionadas as alterações detectadas para o estado atual de conformidade do recurso.

Dados de histórico de alterações são fornecidos pelo [grafo de recursos do Azure](../../resource-graph/overview.md). Para consultar essas informações fora do portal do Azure, consulte [obter alterações de recurso](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Próximas etapas

- Examine os exemplos na [exemplos do Azure Policy](../samples/index.md).
- Revisar a [Estrutura de definição de política](../concepts/definition-structure.md).
- Revisar [Compreendendo os efeitos da política](../concepts/effects.md).
- Entender como [criar políticas de forma programática](programmatically-create.md).
- Saiba como [obter dados de conformidade](getting-compliance-data.md).
- Saiba como [corrigir recursos sem conformidade](remediate-resources.md).
- Examine o que um grupo de gerenciamento com [organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).