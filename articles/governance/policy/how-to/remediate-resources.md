---
title: Corrigir recursos que não estão em conformidade com o Azure Policy
description: Estas instruções o orientam pela correção de recursos que não estão em conformidade com as políticas no Azure Policy.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/25/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: adba2322bce5f0884cba51078e65feeaeaf193d9
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392680"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Corrigir recursos que não estão em conformidade com o Azure Policy

Recursos que não estão em conformidade com uma política de **deployIfNotExists** podem ser colocados em um estado de conformidade por meio de **Correção**. A correção é realizada instruindo a política a executar o efeito **deployIfNotExists** da política atribuída em seus recursos existentes. Estas instruções o orientam pelas etapas necessárias para fazer isso.

## <a name="how-remediation-security-works"></a>Como funciona a correção de segurança

Quando a política executa o modelo na definição de política **deployIfNotExists**, ela faz isso usando uma [identidade gerenciada](../../../active-directory/managed-identities-azure-resources/overview.md).
A política cria uma identidade gerenciada para cada atribuição para você, mas deve ser fornecidos detalhes sobre a quais funções conceder a identidade gerenciada. Se a identidade gerenciada estiver ausente de funções, isso será exibido durante a atribuição de política ou uma iniciativa que contém a política. Ao usar o portal, a Política automaticamente concederá a identidade gerenciada às funções listadas após o início de atribuição.

![Identidade gerenciada – função ausente](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Se um recurso modificado por **deployIfNotExists** está fora do escopo da atribuição de política ou o modelo acessar propriedades em recursos que estão fora do escopo da atribuição de política, deve ser [concedido acesso manualmente](#manually-configure-the-managed-identity) à identidade gerenciada da atribuição, ou a implantação de correção falha.

## <a name="configure-policy-definition"></a>Configurar a definição de política

A primeira etapa é definir as funções de que **deployIfNotExists** precisa na definição de política para implantar com êxito o conteúdo do seu modelo incluído. Na propriedade **detalhes**, adicione uma propriedade **roleDefinitionIds**. Essa é uma matriz de cadeias de caracteres que correspondem a funções em seu ambiente.
Para obter um exemplo completo, confira o [exemplo deployIfNotExists](../concepts/effects.md#deployifnotexists-example).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

**roleDefinitionIds** usa o identificador de recurso completo e não assume o **roleName** curto da função. Para obter a ID para a função 'Colaborador' em seu ambiente, use o seguinte código:

```azurecli-interactive
az role definition list --name 'Contributor'
```

```azurepowershell-interactive
Get-AzureRmRoleDefinition -Name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Configurar manualmente a identidade gerenciada

Ao criar uma atribuição usando o portal, a Política gera a identidade gerenciada e a concede às funções definidas em **roleDefinitionIds**. Nas condições a seguir, as etapas para criar a identidade gerenciada e atribuir a ela permissões devem ser executadas manualmente:

- Ao usar o SDK (como o Azure PowerShell)
- Quando um recurso fora do escopo de atribuição é modificado pelo modelo
- Quando um recurso fora do escopo de atribuição é lido pelo modelo

> [!NOTE]
> O Azure PowerShell e o .NET são os únicos SDKs que atualmente dão suporte a essa funcionalidade.

### <a name="create-managed-identity-with-powershell"></a>Criar identidade gerenciada com o PowerShell

Para criar uma identidade gerenciada durante a atribuição da política, o **Local** deve ser definido e **AssignIdentity** deve ser usada. O exemplo a seguir obtém a definição da política interna **Implantar Transparent Data Encryption do Banco de Dados SQL**, define o grupo de recursos de destino e, em seguida, cria a atribuição.

```azurepowershell-interactive
# Login first with Connect-AzureRmAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzureRmPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzureRmResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzureRmPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

A variável `$assignment` agora contém a ID da entidade de segurança referente à identidade gerenciada, juntamente com os valores padrão retornados durante a criação de uma atribuição de política. Ela pode ser acessada por meio de `$assignment.Identity.PrincipalId`.

### <a name="grant-defined-roles-with-powershell"></a>Conceder funções definidas com o PowerShell

A nova identidade gerenciada deve concluir a replicação por meio do Azure Active Directory antes que possam ser concedidas a ela as funções necessárias. Após a conclusão da replicação, o exemplo a seguir itera a definição de política em `$policyDef` para **roleDefinitionIds** e usa [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) para conceder as funções à nova identidade gerenciada.

```azurepowershell-interactive
# Use the $policyDef to get to the roleDefinitionIds array
$roleDefinitionIds = $policyDef.Properties.policyRule.then.details.roleDefinitionIds

if ($roleDefinitionIds.Count -gt 0)
{
    $roleDefinitionIds | ForEach-Object {
        $roleDefId = $_.Split("/") | Select-Object -Last 1
        New-AzureRmRoleAssignment -Scope $resourceGroup.ResourceId -ObjectId $assignment.Identity.PrincipalId -RoleDefinitionId $roleDefId
    }
}
```

### <a name="grant-defined-roles-through-portal"></a>Conceder funções definidas por meio do portal

Há duas maneiras de conceder uma identidade gerenciada da atribuição às funções definidas usando o portal: usando **Controle de acesso (IAM)** ou editando a atribuição de política ou iniciativa e clicando em **Salvar**.

Para adicionar uma função à identidade gerenciada da atribuição, siga estas etapas:

1. Inicie o serviço de Azure Policy no portal do Azure clicando em**Todos os serviços**, em seguida pesquisando e selecionando **Política**.

1. Selecione **Atribuições** no lado esquerdo da página de Política do Azure.

1. Localize a atribuição que tem uma identidade gerenciada e clique no nome.

1. Localize a propriedade **ID de Atribuição** na página de edição. A ID de atribuição será algo como:

   ```
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   O nome da identidade gerenciada é a última parte da ID do recurso de atribuição, que é `2802056bfc094dfb95d4d7a5` neste exemplo. Copie essa parte da ID do recurso da atribuição.

1. Navegue até o recurso ou os recursos do contêiner pai (grupo de recursos, assinatura, grupo de gerenciamento) que precisa na definição de função adicionada manualmente.

1. Clique no link **Controle de acesso (IAM)** na página de recursos e clique em **+Adicionar** na parte superior da página de controle de acesso.

1. Selecione a função apropriada que corresponde a **roleDefinitionIds** da definição de política. Deixe **Atribuir acesso** definido como o padrão de 'Usuário, grupo ou aplicativo do Azure AD'. Na caixa **Selecionar**, cole ou digite a parte da ID do recurso de atribuição localizada anteriormente. Depois que a pesquisa for concluída, clique no objeto com o mesmo nome para selecionar a ID e clique em **Salvar**.

## <a name="create-a-remediation-task"></a>Criar uma tarefa de correção

Durante a avaliação, a atribuição de política com o efeito **deployIfNotExists** determinará se há recursos sem conformidade. Quando forem encontrados recursos sem conformidade, os detalhes serão fornecidos na página **Correção**. Junto com a lista de políticas que têm recursos sem conformidade está a opção para disparar uma **tarefa de correção**. Isso é o que cria uma implantação usando o modelo **deployIfNotExists**.

Para criar uma **tarefas de correção**, siga estas etapas:

1. Inicie o serviço de Azure Policy no portal do Azure clicando em**Todos os serviços**, em seguida pesquisando e selecionando **Política**.

   ![Pesquisar pela política](../media/remediate-resources/search-policy.png)

1. Selecione **Correção** no lado esquerdo da página do Azure Policy.

   ![Selecionar correção](../media/remediate-resources/select-remediation.png)

1. Todas as atribuições de política **deployIfNotExists** com recursos sem conformidade estão incluídas na tabela de dados e na guia **Políticas a corrigir**. Clique em uma política com recursos sem conformidade. A página **Nova tarefa de correção** é aberta.

   > [!NOTE]
   > Uma maneira alternativa de abrir a página **tarefa de correção** é localizar e clicar na política na página **Conformidade** e, em seguida, clicar no botão **Criar tarefa de correção**.

1. Na página **Nova tarefa de correção**, filtre os recursos a corrigir usando as reticências de **Escopo** para escolher os recursos filho dos quais a política foi atribuída (incluindo até os objetos do recurso individual). Além disso, use a lista suspensa **Locais** para filtrar ainda mais os recursos. Somente os recursos listados na tabela serão corrigidos.

   ![Corrigir – selecionar recursos](../media/remediate-resources/select-resources.png)

1. Inicie a tarefa de correção depois que os recursos tiverem sido filtrados clicando em **Corrigir**. A página de conformidade de política será aberta na guia **Tarefas de correção** para mostrar o estado do progresso das tarefas.

   ![Corrigir – progresso da tarefa](../media/remediate-resources/task-progress.png)

1. Clique na **tarefa de correção** na página de conformidade de política para obter detalhes sobre o progresso. A filtragem usada para a tarefa é mostrada junto com uma lista dos recursos que estão sendo corrigidos.

1. Na página **tarefa de correção**, clique com o botão direito do mouse em um recurso para exibir o recurso ou a implantação da tarefa de correção. No final da linha, clique em **Eventos relacionados** para ver detalhes como uma mensagem de erro.

   ![Remedir – menu de contexto da tarefa de recursos](../media/remediate-resources/resource-task-context-menu.png)

Os recursos implantados por meio de uma **tarefas de correção** serão adicionados à guia **Recursos implantados** na página de conformidade de política após um pequeno atraso.

## <a name="next-steps"></a>Próximas etapas

- Revise os exemplos em [amostras da Política do Azure](../samples/index.md)
- Revise a [estrutura de definição de política](../concepts/definition-structure.md)
- Revisão [Noções básicas sobre os efeitos de política](../concepts/effects.md)
- Entender como [criar políticas de forma programática](programmatically-create.md)
- Saiba como [obter dados de conformidade](getting-compliance-data.md)
- Examine o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md)