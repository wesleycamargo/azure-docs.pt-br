---
title: Ativar minhas funções de recurso do Azure no PIM | Microsoft Docs
description: Saiba como ativar suas funções de recurso do Azure no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/21/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07d9d6eaf0426512e93574d733e7daee1cab1ddb
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56183506"
---
# <a name="activate-my-azure-resource-roles-in-pim"></a>Ativar minhas funções de recurso do Azure no PIM

Usando o Azure AD Privileged Identity Management (PIM), os membros de função qualificada para recursos do Azure podem agendar a ativação para uma data e hora futura. Eles também podem selecionar uma duração de ativação específica até o valor máximo (configurado pelos administradores).

Este artigo é para membros que precisam ativar sua função de recurso do Azure no PIM.

## <a name="activate-a-role"></a>Ativar uma função

Quando você precisar assumir uma função de recurso do Azure, poderá solicitar a ativação usando a opção de navegação **Minhas funções** no PIM.

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. Abra o **Azure AD Privileged Identity Management**. Para obter informações sobre como adicionar o bloco do PIM ao painel, consulte [Começar a usar o PIM](pim-getting-started.md).

1. Clique em **Minhas funções** para ver uma lista das suas funções de diretório do Azure AD elegíveis e funções de recursos do Azure.

    ![Funções de diretório do Azure AD e funções de recursos do Azure - Meus papéis](./media/pim-resource-roles-activate-your-roles/resources-my-roles.png)

1. Na lista **Funções de recursos do Azure**, encontre a função que você deseja ativar.

    ![Funções de recurso do Azure - minha lista de funções](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate.png)

1. Clique em **ativar** para abrir o painel de ativar.

1. Se sua função exigir a MFA (autenticação multifator), clique em **Verificar sua identidade antes de prosseguir**. Você só precisa se autenticar uma vez por sessão.

    ![Verificar com o MFA antes da ativação de função](./media/pim-resource-roles-activate-your-roles/resources-my-roles-mfa.png)

1. Clique em **Verificar minha identidade** e siga as instruções para fornecer a verificação de segurança adicional.

    ![Verificação de segurança adicional](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. Se você quiser especificar um escopo reduzido, clique em **Escopo** para abrir o painel de filtro Recursos.

    É uma prática recomendada solicitar apenas o acesso aos recursos de que você precisa. No painel de filtro Recursos, você pode especificar os grupos de recursos ou recursos aos quais você precisa acessar.

    ![Ative - o filtro de recurso](./media/pim-resource-roles-activate-your-roles/resources-my-roles-resource-filter.png)

1. Se necessário, especifique uma hora de início de ativação personalizada. O membro seria ativado após o horário selecionado.

1. Na caixa **Motivo**, insira o motivo da solicitação de ativação.

    ![Painel de ativar concluído](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-done.png)

1. Clique em **Ativar**.

    Se a função não exigir aprovação, ela já estará ativada e a função será exibida na lista de funções ativas. Se você quiser usar a função imediatamente, siga as etapas na próxima seção.

    Se a [função exigir aprovação](pim-resource-roles-approval-workflow.md) para ser ativada, uma notificação será exibida no canto superior direito do seu navegador informando que a solicitação está com a aprovação pendente.

    ![Notificação de solicitação pendente](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="use-a-role-immediately-after-activation"></a>Usar uma função imediatamente após a ativação

Quando você ativa uma função no PIM, leva pelo menos 10 minutos para acessar o portal administrativo desejado ou executar funções dentro de uma carga de trabalho administrativa específica. Para forçar uma atualização de suas permissões, use a página **Acesso ao aplicativo**, conforme descrito nas etapas a seguir.

1. Abra o Azure AD Privileged Identity Management.

1. Clique na página **Acesso a aplicativos**.

    ![Acesso do aplicativo PIM – captura de tela](./media/pim-resource-roles-activate-your-roles/pim-application-access.png)

1. Clique no link **Recursos do Azure** para reabrir o portal na página **Todos os recursos**.

    Ao clicar nesse link, você invalida seu token atual e força o portal do Azure a obter um novo token que deve conter suas permissões atualizadas.

## <a name="view-the-status-of-your-requests"></a>Exibir o status de suas solicitações

Você pode exibir o status das suas solicitações pendentes a serem ativadas.

1. Abra o Azure AD Privileged Identity Management.

1. Clique em **Minhas solicitações** para ver uma lista de sua função de diretório do Azure AD e solicitações de função de recurso do Azure.

    ![Funções de diretório do AD do Azure e funções de recurso do Azure - Minhas solicitações](./media/pim-resource-roles-activate-your-roles/resources-my-requests.png)

1. Role para a direita para exibir o **Status da solicitação** coluna.

## <a name="cancel-a-pending-request"></a>Cancelar uma solicitação pendente

Caso não precise da ativação de uma função que requer aprovação, você pode cancelar uma solicitação pendente a qualquer momento.

1. Abra o Azure AD Privileged Identity Management.

1. Clique em **Minhas solicitações**.

1. Para a função que você deseja cancelar, clique no link **Cancelar**.

    Quando você clicar em Cancelar, a solicitação será cancelada. Para ativar a função novamente, você precisará enviar uma nova solicitação de ativação.

   ![Cancelar uma solicitação pendente](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot"></a>Solucionar problemas

### <a name="permissions-not-granted-after-activating-a-role"></a>Permissões não concedidas depois de ativar uma função

Quando você ativa uma função no PIM, leva pelo menos 10 minutos para acessar o portal administrativo desejado ou executar funções dentro de uma carga de trabalho administrativa específica. Para forçar uma atualização de suas permissões, use a página **Acesso ao aplicativo**, conforme descrito anteriormente em [Use uma função imediatamente após a ativação](#use-a-role-immediately-after-activation).

Para obter etapas adicionais de solução de problemas, consulte [Solucionando problemas de permissões elevadas](https://social.technet.microsoft.com/wiki/contents/articles/37568.troubleshooting-elevated-permissions-with-azure-ad-privileged-identity-management.aspx).

### <a name="cannot-activate-a-role-due-to-a-resource-lock"></a>Não é possível ativar uma função devido a um bloqueio de recurso

Se você receber uma mensagem de que um recurso do Azure está bloqueado ao tentar ativar uma função, talvez seja porque um recurso no escopo de uma atribuição de função possui um bloqueio de recurso. Bloqueios protegem recursos de exclusão acidental ou alterações inesperadas. Um bloqueio também impede que o PIM remova uma atribuição de função no recurso no final do período de ativação. Como o PIM não pode funcionar corretamente quando um bloqueio é aplicado, o PIM proíbe que os usuários ativem funções no recurso. Há duas maneiras de resolver esse problema:

- Exclua o bloqueio conforme descrito em [Bloquear recursos para evitar alterações inesperadas](../../azure-resource-manager/resource-group-lock-resources.md).
- Se você quiser manter o bloqueio, torne a atribuição de função permanente ou use uma conta break-glass.

## <a name="next-steps"></a>Próximas etapas

- [Estender ou renovar funções de recurso do Azure no PIM](pim-resource-roles-renew-extend.md)
- [Ativar minhas funções de diretório do Azure AD no PIM](pim-how-to-activate-role.md)
