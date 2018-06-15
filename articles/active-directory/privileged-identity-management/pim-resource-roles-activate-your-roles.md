---
title: Ativar funções para recursos do Azure usando Privileged Identity Management | Microsoft Doc
description: Descreve como ativar funções no PIM.
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
ms.openlocfilehash: 30032229c3c24a7f7450b3f590ed1d3acd18b47d
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233124"
---
# <a name="activate-roles-for-azure-resources-by-using-privileged-identity-management"></a>Ativar funções para recursos do Azure usando Privileged Identity Management
O Privileged Identity Management (PIM) apresenta uma nova experiência em ativar funções para recursos do Azure. Membros de função qualificados podem agendar a ativação para uma data e hora no futuro. Eles também podem selecionar uma duração de ativação específica até o valor máximo (configurado pelos administradores). Para obter mais informações, consulte [Como ativar ou desativar funções no Azure AD Privileged Identity Management](../active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="activate-roles"></a>Ativar funções
Navegue até a seção **Minhas funções** no painel esquerdo. Selecione **Ativar** para a função que você deseja ativar.

![Guia "Funções qualificadas" no painel "Minhas funções".](media/azure-pim-resource-rbac/rbac-roles.png)

No menu de **Ativações**, insira a data e a hora de início para ativar a função. Você pode optar por diminuir a duração da ativação (o período de tempo durante o qual a função está ativa) e inserir uma justificativa se necessário. Em seguida, selecione **Ativar**.

Se a data de início e a hora não forem modificadas, a função será ativada em alguns segundos. No painel **Minhas funções**, uma mensagem do cabeçalho mostra que uma função está na fila para ativação. Selecione o botão para atualizar e essa mensagem será eliminada.

![Painel de "Minhas funções" com uma mensagem de cabeçalho e uma notificação sobre uma aprovação pendente](media/azure-pim-resource-rbac/rbac-activate-notification.png)

Se a ativação for agendada para uma data e hora futuras, a solicitação pendente será exibida na guia **Solicitações pendentes** do painel esquerdo. Se a ativação de função não for mais necessária, você pode cancelar a solicitação selecionando o botão **Cancelar**.

![Lista de solicitações pendentes com os botões "Cancelar"](media/azure-pim-resource-rbac/rbac-activate-pending.png)


## <a name="apply-just-enough-administration-practices"></a>Aplicar as práticas Administração Just Enough

Usar as práticas recomendadas da Administração Just Enough (JEA) nas suas atribuições de função de recurso é simples com o PIM para recursos do Azure. Usuários e membros de grupo com atribuições nas assinaturas ou grupos de recursos do Azure podem ativar sua atribuição de função existente em escopo reduzido. 

Na página de pesquisa, localize o recurso subordinado que você precisa gerenciar.

![Selecionando um recurso](media/azure-pim-resource-rbac/azure-resources-02.png)

Selecione **Minhas funções** no painel esquerdo e escolha a função apropriada para ativação. O tipo de atribuição é **Herdada**, pois a função foi atribuída na assinatura e não para no grupo de recursos.

![Lista de atribuições de função qualificada, com o tipo de atribuição realçado](media/azure-pim-resource-rbac/my-roles-02.png)
