---
title: Ativar minhas funções do Azure AD no PIM - Azure Active Directory | Microsoft Docs
description: Saiba como ativar funções do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa820d6c140251fce6b09110e65b45005b53afcc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60289483"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Ativar minhas funções do Azure AD no PIM

O Azure AD (Azure Active Directory) PIM (Privileged Identity Management) simplifica a forma como as empresas gerenciam o acesso privilegiado a recursos no Azure AD e em outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.  

Se você tiver se tornado elegível para uma função administrativa, isso significa que você poderá ativar essa função quando precisar executar ações que demandam privilégios. Por exemplo, se você ocasionalmente gerencia recursos do Office 365, administradores de função com privilégios de sua organização podem não o tornar um Administrador Global permanente, pois essa função também afeta outros serviços. Em vez disso, eles o tornam qualificado para funções do Azure AD, como Administrador do Exchange Online. Você pode solicitar a ativação da função quando precisar de seus privilégios e terá controle de administrador por um período predeterminado.

Este artigo destina-se a administradores que precisam ativar sua função do Azure AD no PIM.

## <a name="activate-a-role"></a>Ativar uma função

Quando você precisar assumir uma função do Azure AD, você poderá solicitar a ativação usando o **minhas funções** opção de navegação no PIM.

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. Abra o **Azure AD Privileged Identity Management**. Para obter informações sobre como adicionar o bloco do PIM ao painel, consulte [Começar a usar o PIM](pim-getting-started.md).

1. Clique em **funções do Microsoft Azure Active Directory**.

1. Clique em **minhas funções** para ver uma lista de seus qualificados para funções do Azure AD.

    ![Funções do Azure AD - minhas funções](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Localize uma função que você deseja ativar.

    ![Funções do Azure AD - minha lista de funções](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Clique em **Ativar** para abrir o painel Detalhes de ativação de função.

1. Se sua função exigir a MFA (autenticação multifator), clique em **Verificar sua identidade antes de prosseguir**. Você só precisa se autenticar uma vez por sessão.

    ![Verificar com o MFA antes da ativação de função](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Clique em **Verificar minha identidade** e siga as instruções para fornecer a verificação de segurança adicional.

    ![Verificação de segurança adicional](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Clique em **Ativar** para abrir o painel Ativação.

    ![Painel Ativação](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. Se necessário, especifique uma hora de início de ativação personalizada.

1. Especifique a duração da ativação.

1. Na caixa **Motivo da ativação**, insira o motivo para a solicitação de ativação. Algumas funções exigem que você forneça um número de tíquete de problema.

    ![Painel Ativação Concluída](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Clique em **Ativar**.

    Se a função não exigir aprovação, uma **status de ativação** painel será exibido que exibe o status de ativação.

    ![Status de ativação](./media/pim-how-to-activate-role/activation-status.png)

    Depois que todas as etapas forem concluídas, clique no **sair** link para sair do portal do Azure. Quando você entrar no portal, agora você pode usar a função.

    Se a [função exigir aprovação](./azure-ad-pim-approval-workflow.md) para ser ativada, uma notificação será exibida no canto superior direito do seu navegador informando que a solicitação está com a aprovação pendente.

    ![Notificação de solicitação pendente](./media/pim-how-to-activate-role/directory-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests"></a>Exibir o status de suas solicitações

Você pode exibir o status das suas solicitações pendentes a serem ativadas.

1. Abra o Azure AD Privileged Identity Management.

1. Clique em **funções do Microsoft Azure Active Directory**.

1. Clique em **Minhas solicitações** para ver uma lista das suas solicitações.

    ![Funções do Azure AD - Minhas solicitações](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role"></a>Desativar uma função

Após uma função ter sido ativada, ela será desativada automaticamente quando limite de tempo (duração qualificada) for atingido.

Se concluir suas tarefas de administrador antes, você também poderá desativar uma função manualmente no Azure AD Privileged Identity Management.

1. Abra o Azure AD Privileged Identity Management.

1. Clique em **funções do Microsoft Azure Active Directory**.

1. Clique em **Minhas funções**.

1. Clique em **Funções ativas** para ver a lista de funções ativas.

1. Localize a função que você terminou de usar e clique em **Destivar**.

## <a name="cancel-a-pending-request"></a>Cancelar uma solicitação pendente

Caso não precise da ativação de uma função que requer aprovação, você pode cancelar uma solicitação pendente a qualquer momento.

1. Abra o Azure AD Privileged Identity Management.

1. Clique em **funções do Microsoft Azure Active Directory**.

1. Clique em **Minhas solicitações**.

1. Para a função que você deseja cancelar, clique no botão **Cancelar**.

    Quando você clicar em Cancelar, a solicitação será cancelada. Para ativar a função novamente, você precisará enviar uma nova solicitação de ativação.

   ![Cancelar uma solicitação pendente](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot"></a>Solução de problemas

### <a name="permissions-not-granted-after-activating-a-role"></a>Permissões não concedidas depois de ativar uma função

Quando você ativa uma função no PIM, leva pelo menos 10 minutos para acessar o portal administrativo desejado ou executar funções dentro de uma carga de trabalho administrativa específica. Quando a ativação for concluída, saia do portal do Azure e entre novamente para começar a usar a função recém-ativado.

Para obter etapas adicionais de solução de problemas, consulte [Solucionando problemas de permissões elevadas](https://social.technet.microsoft.com/wiki/contents/articles/37568.troubleshooting-elevated-permissions-with-azure-ad-privileged-identity-management.aspx).

## <a name="next-steps"></a>Próximas etapas

- [Ativar minhas funções de recurso do Azure no PIM](pim-resource-roles-activate-your-roles.md)
