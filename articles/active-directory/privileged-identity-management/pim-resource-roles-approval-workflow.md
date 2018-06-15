---
title: Fluxo de trabalho de aprovação para funções de recursos do Azure no Privileged Identity Management | Microsoft Docs
description: Descreve o processo de fluxo de trabalho de aprovação para recursos do Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: de15a02e706ec7f7b4cff0af303ea30fc87b8f34
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233760"
---
# <a name="approval-workflow-for-azure-resource-roles-in-privileged-identity-management"></a>Fluxo de trabalho de aprovação para funções de recursos do Azure no Privileged Identity Management

Com o fluxo de trabalho de aprovação no PIM (Privileged Identity Management) para funções de recurso do Azure, os administradores podem proteger ou restringir ainda mais o acesso a recursos críticos. Ou seja, os administradores podem exigir aprovação para ativar as atribuições de função. 

O conceito de uma hierarquia de recursos é exclusivo das funções de recurso do Azure. Essa hierarquia permite a herança de atribuições de função de um objeto de recursos pai por todos os recursos filhos dentro do contêiner pai. 

Por exemplo, Bob, um administrador de recursos, usa PIM para atribuir Alice como membro qualificado para a função de proprietário na assinatura do Contoso. Com a atribuição, Alice é uma proprietária qualificada de todos os contêineres de grupo de recursos dentro da assinatura do Contoso. Alice também é uma proprietária qualificada de todos os recursos (como máquinas virtuais) em cada grupo de recursos da assinatura. 

Vamos supor que há três grupos de recursos na assinatura do Contoso: Teste Fabrikam, Desenvolvimento Fabrikam e Produção Fabrikam. Cada um desses grupos de recursos contém uma única máquina virtual.

As configurações PIM são definidas para cada função de um recurso. Ao contrário das atribuições, essas configurações não são herdadas e se aplicam estritamente à função do recurso. [Saiba mais sobre atribuições qualificadas e visibilidade de recursos.](pim-resource-roles-eligible-visibility.md)

Continuando com o exemplo: Bob usa o PIM para exigir que todos os membros na função de proprietário da assinatura do Contoso solicitem aprovação para ativar. Para ajudar a proteger os recursos no grupo de recursos de Produção Fabrikam, Bob também requer aprovação para membros da função de proprietário desse recurso. As funções de proprietário em Teste Fabrikam e Desenvolvimento Fabrikam não requerem aprovação para ativar.

Quando Alice solicitar a ativação da sua função de proprietário para a assinatura do Contoso, um aprovador deverá aprovar ou negar a solicitação antes que ela se torne ativa na função. Se Alice decidir [definir o escopo de sua ativação](pim-resource-roles-activate-your-roles.md#apply-just-enough-administration-practices) para o grupo de recursos de Produção Fabrikam, um aprovador deverá aprovar ou negar essa solicitação também. Mas, se Alice decidir definir o escopo de sua ativação para Teste Fabrikam e/ou Desenvolvimento Fabrikam, a aprovação não será necessária.

O fluxo de trabalho de aprovação pode não ser necessário para todos os membros de uma função. Considere um cenário em que sua organização contrata vários colaboradores contratados para ajudar no desenvolvimento de um aplicativo que será executado em uma assinatura do Azure. Como administrador de recursos, você gostaria que os funcionários tivessem acesso qualificado sem necessidade aprovação, mas os colaboradores contratados precisam solicitar aprovação. Para configurar o fluxo de trabalho de aprovação para apenas colaboradores contratados, você pode criar uma função personalizada com as mesmas permissões que a função atribuída aos funcionários. Você pode exigir aprovação para ativar a função personalizada. [Saiba mais sobre funções personalizadas](pim-resource-roles-custom-role-policy.md).

Para configurar o fluxo de trabalho de aprovação e especificar quem pode aprovar ou negar solicitações, use os procedimentos a seguir.

## <a name="require-approval-to-activate"></a>Exigir aprovação para ativar

1. Navegue até o PIM no Portal do Azure e selecione um recurso na lista.

   ![Painel "Recursos do Azure" com um recurso selecionado](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

2. No painel esquerdo, selecione **Configurações de função**.

3. Procure e selecione uma função e selecione **Editar** para modificar as configurações.

   ![Botão "Editar" para a função de operador](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

4. Na seção de Ativação, selecione a caixa **Exigir aprovação para ativar**.

   ![Seção "Ativação" das configurações de função](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>Especificar aprovadores

Clique em **Selecionar aprovadores** para abrir o painel **Selecionar um usuário ou grupo**.

>[!NOTE]
>Você deve selecionar pelo menos um usuário ou grupo para atualizar a configuração. Não há nenhum aprovador padrão.

Os administradores de recurso podem adicionar qualquer combinação de usuários e grupos à lista de aprovadores. 

![Painel "Selecionar um usuário ou grupo" com um usuário selecionado](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>Solicitar aprovação para ativar

A solicitação de aprovação não tem impacto no procedimento que um membro deve ser seguir para ativar. [Examine as etapas para ativar uma função](pim-resource-roles-activate-your-roles.md).

Se um membro solicitou a ativação de uma função que requer aprovação e a função não é mais necessária, o membro pode cancelar sua solicitação no PIM.

Para cancelar, navegue até o PIM e selecione **Minhas solicitações**. Localize a solicitação e selecione **Cancelar**.

![Painel “Minhas Solicitações”](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="approve-or-deny-a-request"></a>Aprovar ou negar uma solicitação

Para aprovar ou negar uma solicitação, você deve ser um membro da lista de aprovadores. 

1. No PIM, selecione **Aprovar solicitações** na guia do menu à esquerda e localize a solicitação.

   ![Painel “Aprovar solicitações”](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

2. Selecione a solicitação, forneça uma justificativa para a decisão e selecione **Aprovar** ou **Negar**. A solicitação é resolvida.

   ![Solicitação selecionada com informações detalhadas](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>Notificações de fluxo de trabalho

Aqui estão alguns fatos sobre notificações de fluxo de trabalho:

- Todos os membros da lista de aprovadores são notificados por email quando uma solicitação para uma função está aguardando revisão. As notificações por email incluem um link direto para a solicitação no qual o aprovador pode aprovar ou negar.
- As solicitações são resolvidas pelo primeiro membro da lista que aprovar ou recusar. 
- Quando um aprovador responde à solicitação, todos os membros da lista de aprovadores são notificados da ação. 
- Os administradores de recursos são notificados quando um membro aprovado se torna ativo em sua função. 

>[!Note]
>Um administrador de recursos que acredite que o membro aprovado não deve estar ativo pode remover a atribuição de função ativa no PIM. Embora os administradores de recursos não sejam notificados de solicitações pendentes a menos que sejam membros da lista de aprovadores, eles podem exibir e cancelar solicitações pendentes de todos os usuários exibindo solicitações pendentes no PIM. 

## <a name="next-steps"></a>Próximas etapas

[Aplicar configurações do PIM a grupos exclusivos de usuários](pim-resource-roles-custom-role-policy.md)
