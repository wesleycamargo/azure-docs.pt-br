---
title: Privileged Identity Management - Fluxo de trabalho de aprovação para funções de recursos do Azure | Microsoft Docs
description: Descreve como é o processo de fluxo de trabalho de aprovação para recursos do Azure.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: c02d595d75b2d63558896054c185102ebb23cc9e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---approve"></a>Privileged Identity Management - Funções de recurso - Aprovar

Com o fluxo de trabalho de aprovação no PIM para funções de recurso do Azure, os administradores podem proteger ou restringir ainda mais o acesso a recursos críticos ao exigir aprovação para ativar as atribuições de função. O conceito de uma hierarquia de recursos é exclusivo das funções de recurso do Azure. Essa hierarquia permite a herança de atribuições de função de um objeto de recursos pai por todos os recursos filhos subordinados dentro do contêiner pai. 

Por exemplo, Bob, um administrador de recursos, usa PIM para atribuir Alice como membro qualificado para a função de Proprietário na assinatura do Contoso. Com essa atribuição, Alice também é uma Proprietária qualificada de todos os contêineres de grupo de recursos dentro da assinatura do Contoso e de todos os recursos (como máquinas virtuais) contidos em cada grupo de recursos da assinatura. Vamos supor que há três grupos de recursos na assinatura do Contoso; Teste Fabrikam, Desenvolvimento Fabrikam e Produção Fabrikam. Cada um desses grupos de recursos contém uma única máquina virtual.

As configurações do PIM estão configuradas para cada função de um recurso e, ao contrário das atribuições, essas configurações não são herdadas e se aplicam estritamente à função do recurso. [Saiba mais sobre atribuições qualificadas e visibilidade de recursos.](pim-resource-roles-eligible-visibility.md)

Usando o exemplo acima, Bob usa o PIM para exigir que todos os membros na função de Proprietário da assinatura do Contoso solicitem aprovação para ativar. Para proteger os recursos contidos no grupo de recursos de Produção Fabrikam, Bob requer aprovação para membros da função de Proprietário desse recurso também. As funções de proprietário em Teste Fabrikam e Desenvolvimento Fabrikam não requerem aprovação para ativar.

Quando Alice solicitar a ativação da sua função de Proprietário para a assinatura do Contoso, um aprovador deverá aprovar ou negar a solicitação antes que ela se torne ativa na função. Além disso, se Alice decidir [definir o escopo de sua ativação](pim-resource-roles-activate-your-roles.md#just-enough-administration) para o grupo de recursos de Produção Fabrikam, um aprovador deverá aprovar ou negar essa solicitação também. No entanto, se Alice decidir definir o escopo de sua ativação para Teste Fabrikam e/ou Desenvolvimento Fabrikam, a aprovação não será necessária.

O fluxo de trabalho de aprovação pode não ser necessário para todos os membros de uma função. Considere um cenário em que sua organização contrata vários colaboradores contratados para ajudar no desenvolvimento de um aplicativo que será executado em uma assinatura do Azure. Como administrador de recursos, você gostaria que os funcionários tivessem acesso qualificado sem necessidade aprovação, mas os colaboradores contratados precisam solicitar aprovação. Para configurar o fluxo de trabalho de aprovação para apenas colaboradores contratados, você pode criar uma função personalizada com as mesmas permissões que a função atribuída aos funcionários e exigir a aprovação para ativar a função personalizada. [Saiba mais sobre funções personalizadas](pim-resource-roles-custom-role-policy.md).

Siga as etapas abaixo para configurar o fluxo de trabalho de aprovação e especificar quem pode aprovar ou negar solicitações.

## <a name="require-approval-to-activate"></a>Exigir aprovação para ativar

Navegue até o PIM no Portal do Azure e selecione um recurso na lista.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

No menu de navegação à esquerda, selecione **Configurações de função**.

Procure e selecione uma função e clique em **Editar** para modificar as configurações.

![](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

Na seção de Ativação, marque a caixa **Exigir aprovação para ativar**.

![](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>Especificar aprovadores

Clique em **Selecionar aprovadores** para abrir a tela de seleção.

>[!NOTE]
>Você deve selecionar pelo menos um usuário ou grupo para atualizar a configuração. Não há nenhum aprovador padrão.

Os administradores de recurso podem adicionar qualquer combinação de usuários e grupos à lista de aprovadores. 

![](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>Solicitar aprovação para ativar

A solicitação de aprovação não tem impacto no procedimento que um membro deve ser seguir para ativar. [Examine as etapas para ativar uma função](pim-resource-roles-activate-your-roles.md).

Se um membro solicitou a ativação de uma função que requer aprovação e a função não é mais necessária, o membro pode cancelar sua solicitação no PIM.

Para cancelar, navegue até o PIM e selecione "Minhas solicitações". Localize a solicitação e clique em "Cancelar".

![](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="approve-or-deny-a-request"></a>Aprovar ou negar uma solicitação

Para aprovar ou negar uma solicitação, você deve ser um membro da lista de aprovadores. No PIM, selecione "Aprovar solicitações" na guia do menu de navegação à esquerda e localize a solicitação.

![](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

Selecione a solicitação, forneça uma justificativa para a decisão e selecione aprovar ou negar, resolvendo a solicitação.

![](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>Notificações de fluxo de trabalho

- Todos os membros da lista de aprovadores são notificados por email quando uma solicitação para uma função está aguardando revisão. As notificações por email incluem um link direto para a solicitação no qual o aprovador pode aprovar ou negar.
- As solicitações são resolvidas pelo primeiro membro da lista que aprovar ou recusar. 
- Quando um aprovador responde à solicitação, todos os membros da lista de aprovadores são notificados da ação. 
- Os administradores de recursos são notificados quando um membro aprovado se torna ativo em sua função. 

>[!Note]
>Caso um administrador de recursos acredite que o membro aprovado não deve estar ativo, ele pode remover a atribuição de função ativa no PIM. Embora os administradores de recursos não sejam notificados de solicitações pendentes a menos que sejam membros da lista de aprovadores, eles podem exibir e cancelar solicitações pendentes de todos os usuários exibindo solicitações pendentes no PIM. 

## <a name="next-steps"></a>Próximas etapas

[Aplicar configurações do PIM a grupos exclusivos de usuários](pim-resource-roles-custom-role-policy.md)
