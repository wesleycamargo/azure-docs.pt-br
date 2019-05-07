---
title: Criar uma revisão de acesso de funções do Azure AD no PIM - Azure Active Directory | Microsoft Docs
description: Saiba como criar uma revisão de acesso de funções do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/27/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a0680ddf2c9e654455933bf09699ab81e8ab65d
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65141670"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-pim"></a>Criar uma revisão de acesso de funções do Azure AD no PIM

Acesso privilegiado de funções do Azure AD para que as alterações de funcionários ao longo do tempo. Para reduzir os riscos associados às atribuições de função obsoletas, você deve examinar regularmente o acesso. Você pode usar o Azure Active Directory (Azure AD) gerenciamento de PIM (Privileged Identity) para criar as revisões de acesso para funções do Azure AD com privilégios. Você também pode configurar as revisões de acesso recorrentes que ocorrem automaticamente.

Este artigo descreve como criar um ou mais revisões de acesso para funções do Azure AD com privilégios.

## <a name="prerequisites"></a>Pré-requisitos

- [Administrador com privilégios de função](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Revisões de acesso aberto

1. Entrar no [portal do Azure](https://portal.azure.com/) com um usuário que seja membro da função de administrador com privilégios de função.

1. Abra o **Azure AD Privileged Identity Management**.

1. No menu à esquerda, clique em **funções do Azure AD** e, em seguida, clique em **revisões de acesso**.

1. Em gerenciar, clique em **revisões de acesso**.

    ![Revisões de acesso de funções do Azure AD-](./media/pim-how-to-start-security-review/access-reviews.png)


[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]


## <a name="start-the-access-review"></a>Inicie a revisão de acesso

Depois de especificar as configurações para uma revisão de acesso, clique em **Iniciar**. A revisão de acesso será exibido na lista com um indicador de seu status.

![Lista de revisões de acesso](./media/pim-how-to-start-security-review/access-reviews-list.png)

Por padrão, o Azure AD envia um email para os revisores logo após o início da análise. Se você optar pelo não envio do email pelo Azure AD, certifique-se de informar aos revisores que eles devem concluir uma análise de acesso pendente. Você pode mostrar as instruções de como a [revisar o acesso às funções do AD do Azure](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Gerenciar a análise de acesso

Você pode acompanhar o progresso conforme os revisores concluem suas revisões na **visão geral** página de revisão de acesso. Não há direitos de acesso são alterados no diretório até que o [revisão está concluída](pim-how-to-complete-review.md).

![Progresso de revisões de acesso](./media/pim-how-to-start-security-review/access-review-overview.png)

Quando se trata de uma única revisão, em seguida, após o período de revisão de acesso ou o administrador tiver interrompido a revisão de acesso, siga as etapas em [concluir uma revisão de acesso de funções do Azure AD](pim-how-to-complete-review.md) para ver e aplicar os resultados.  

Para gerenciar uma série de acesso de revisões, navegue até a revisão de acesso, e você será encontrar ocorrências futuras em revisões agendada e editar a data de término ou adicionar ou remover os revisores adequadamente.

Com base em suas seleções **nas configurações de preenchimento**, será de aplicação automática ser executada após a data de término da análise de ou quando você interromper a revisão manualmente. O status da revisão será alterado de **Completed** estados intermediários, como **aplicando** e, finalmente, para o estado **aplicada**. Você deve esperar ver os usuários negados, se houver, que estão sendo removidos das funções em alguns minutos.

## <a name="next-steps"></a>Próximas etapas

- [Examinar o acesso às funções do AD do Azure](pim-how-to-perform-security-review.md)
- [Concluir uma revisão de acesso de funções do Azure AD](pim-how-to-complete-review.md)
- [Criar uma revisão de acesso das funções de recurso do Azure](pim-resource-roles-start-access-review.md)
