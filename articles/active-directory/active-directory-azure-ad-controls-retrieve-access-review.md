---
title: Recuperar resultados de revisão de acesso do Azure AD | Microsoft Docs
description: Como recuperar os resultados das revisões de acesso do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: markwahl-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/16/2018
ms.author: billmath
ms.openlocfilehash: cdd07fd837863d9a5abced0db8cacaded6288a41
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34192217"
---
# <a name="retrieve-access-review-results"></a>Recuperar resultados de revisão do acesso

Os administradores podem usar o Azure Active Directory (Azure AD) para [criar uma análise de acesso](active-directory-azure-ad-controls-create-access-review.md) para membros do grupo ou os usuários atribuídos a um aplicativo.  Um usuário com a função de **Administrador Global**, **Administrador de segurança** ou **Leitor de segurança** também pode ler os resultados de uma revisão de acesso.  Para atribuir usuários a uma dessas funções, um administrador com função com privilégios pode usar o PIM do Azure AD para tornar um usuário elegível para ativar a função ou um Administrador global pode [atribuir um usuário à função](active-directory-users-assign-role-azure-portal.md) de forma permanente.

## <a name="locating-an-access-review"></a>Localizar uma revisão de acesso

Se você sabe qual programa contém a revisão de acesso, vá para a página de revisões de acesso, selecione **Programas** e escolha o programa que contém o controle de revisão de acesso.  Depois selecione **Controles** e selecione o controle de revisão de acesso. Se houver muitos controles no programa, você poderá filtrar os controles de um tipo específico e classificar pelo status. Você também pode pesquisar pelo nome do controle da análise de acesso ou pelo nome de exibição do proprietário que o criou. 

Se você não sabe qual programa contém a revisão de acesso, vá até a página de revisões de acesso e selecione **Controles**.  Você pode filtrar os controles de um tipo específico e classificar por seu status e pode também procurar pelo nome do controle de revisão de acesso ou pelo nome de exibição do proprietário que o criou. 

## <a name="retrieving-the-results-for-a-one-time-access-review"></a>Recuperar os resultados para uma única revisão de acesso único

Se o tipo de recorrência de revisão for uma vez, o progresso de uma revisão de acesso ativa e os resultados de uma revisão de acesso concluída podem ser obtidos na seção **Resultados**.  Você pode digitar o nome de exibição ou nome UPN de um usuário cujo acesso está sendo revisado para exibir apenas o acesso do usuário.  Para recuperar todos os resultados de uma revisão de acesso concluída, clique no botão **Baixar**.

## <a name="retrieving-the-results-for-multiple-instances-of-a-recurring-access-review"></a>Recuperar os resultados de várias instâncias de uma revisão de acesso recorrente

Para exibir o progresso de uma revisão de acesso ativa recorrente, clique na seção **Resultados**.  Você pode digitar o nome de exibição ou nome UPN de um usuário cujo acesso está sendo revisado.

Para exibir os resultados de uma instância concluída de uma revisão de acesso recorrente, selecione **Revisar histórico**, em seguida, selecione a instância específica da lista de instâncias de revisão de acesso concluídas, com base nas datas de início e término da instância.   Os resultados dessa instância podem ser obtidos na seção **Resultados**.  Você pode digitar o nome de exibição ou nome UPN de um usuário cujo acesso está sendo revisado para exibir apenas o acesso do usuário.  Para recuperar todos os resultados de uma instância concluída de revisão de acesso recorrente, clique no botão **Baixar**.


## <a name="removing-users-from-an-access-review"></a>Remover usuários de uma revisão de acesso

[!INCLUDE [Privacy](../../includes/gdpr-intro-sentence.md)]

Por padrão, um usuário excluído permanecerá excluído no Azure AD por 30 dias, durante os quais ele pode ser restaurado por um administrador se necessário.  Após 30 dias, esse usuário será excluído permanentemente.  Além disso, usando o portal do Azure Active Directory, um Administrador Global pode explicitamente [excluir permanentemente um usuário excluído recentemente](active-directory-users-restore.md) antes de atingir o período de tempo.  Após um usuário ter sido excluído permanentemente, os dados posteriores sobre o usuário serão removidos das revisões de acesso ativas.  Auditar informações sobre usuários excluídos na trilha de auditoria.

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar o acesso do usuário com revisões de acesso ao Azure AD](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
- [Gerenciar o acesso de convidado com revisões de acesso do Azure AD](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
- [Gerenciar os programas e os controles para análises de acesso do Azure AD](active-directory-azure-ad-controls-manage-programs-controls.md)
- [Como criar uma revisão de acesso para os membros de um grupo ou para o acesso a um aplicativo](active-directory-azure-ad-controls-create-access-review.md)
- [Criar uma revisão de acesso de usuários em uma função administrativa do Azure AD](active-directory-privileged-identity-management-how-to-start-security-review.md)


