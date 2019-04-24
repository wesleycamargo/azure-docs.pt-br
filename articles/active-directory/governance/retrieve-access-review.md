---
title: Recuperar os resultados da revisão de acesso para grupos ou aplicativos em revisões de acesso - Azure Active Directory | Microsoft Docs
description: Saiba como recuperar os resultados da revisão de acesso para membros do grupo ou acesso a aplicativos em revisões de acesso do Active Directory do Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: eae4bafb3eefcee2785c784030d7be8dde66988e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60245834"
---
# <a name="retrieve-access-review-results-for-groups-or-applications-in-azure-ad-access-reviews"></a>Revisão de acesso de recuperar os resultados para aplicativos no Azure AD ou grupos de revisões de acesso

Os administradores podem usar o Azure Active Directory (Azure AD) para [criar uma análise de acesso](create-access-review.md) para membros do grupo ou os usuários atribuídos a um aplicativo.  Um usuário que está na **Administrador Global**, **usuário administrador**, **Security administrator** ou **leitor de segurança** função também pode os resultados de uma revisão de acesso de leitura.  Para atribuir usuários a uma dessas funções, um administrador com privilégios de função pode usar o PIM do Azure AD para tornar um usuário elegível para ativar a função ou um Administrador Global pode permanentemente [atribuir um usuário à função](../fundamentals/active-directory-users-assign-role-azure-portal.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="locating-an-access-review"></a>Localizar uma revisão de acesso

Se você sabe qual programa contém a revisão de acesso, vá para a página de revisões de acesso, selecione **Programas** e escolha o programa que contém o controle de revisão de acesso.  Depois selecione **Controles** e selecione o controle de revisão de acesso. Se houver muitos controles no programa, você poderá filtrar os controles de um tipo específico e classificar pelo status. Você também pode pesquisar pelo nome do controle da análise de acesso ou pelo nome de exibição do proprietário que o criou. 

Se você não sabe qual programa contém a revisão de acesso, vá até a página de revisões de acesso e selecione **Controles**.  Você pode filtrar os controles de um tipo específico e classificar por seu status e pode também procurar pelo nome do controle de revisão de acesso ou pelo nome de exibição do proprietário que o criou. 

## <a name="retrieving-the-results-for-a-one-time-access-review"></a>Recuperar os resultados para uma única revisão de acesso único

Se o tipo de recorrência de revisão for uma vez, o progresso de uma revisão de acesso ativa e os resultados de uma revisão de acesso concluída podem ser obtidos na seção **Resultados**.  Você pode digitar o nome de exibição ou nome UPN de um usuário cujo acesso está sendo revisado para exibir apenas o acesso do usuário.  Para recuperar todos os resultados de uma revisão de acesso concluída, clique no botão **Baixar**.

## <a name="retrieving-the-results-for-multiple-instances-of-a-recurring-access-review"></a>Recuperar os resultados de várias instâncias de uma revisão de acesso recorrente

Para exibir o progresso de uma revisão de acesso ativa recorrente, clique na seção **Resultados**.  Você pode digitar o nome de exibição ou nome UPN de um usuário cujo acesso está sendo revisado.

Para exibir os resultados de uma instância concluída de uma revisão de acesso recorrente, selecione **Revisar histórico**, em seguida, selecione a instância específica da lista de instâncias de revisão de acesso concluídas, com base nas datas de início e término da instância.   Os resultados dessa instância podem ser obtidos na seção **Resultados**.  Você pode digitar o nome de exibição ou nome UPN de um usuário cujo acesso está sendo revisado para exibir apenas o acesso do usuário.  Para recuperar todos os resultados de uma instância concluída de revisão de acesso recorrente, clique no botão **Baixar**.


## <a name="removing-users-from-an-access-review"></a>Remover usuários de uma revisão de acesso

Por padrão, um usuário excluído permanecerá excluído no Azure AD por 30 dias, durante os quais ele pode ser restaurado por um administrador se necessário.  Após 30 dias, esse usuário será excluído permanentemente.  Além disso, usando o portal do Azure Active Directory, um Administrador Global pode explicitamente [excluir permanentemente um usuário excluído recentemente](../fundamentals/active-directory-users-restore.md) antes de atingir o período de tempo.  Após um usuário ter sido excluído permanentemente, os dados posteriores sobre o usuário serão removidos das revisões de acesso ativas.  Auditar informações sobre usuários excluídos na trilha de auditoria.

## <a name="next-steps"></a>Próximas etapas

- [Gerenciar o acesso do usuário com revisões de acesso ao Azure AD](manage-user-access-with-access-reviews.md)
- [Gerenciar o acesso de convidado com revisões de acesso do Azure AD](manage-guest-access-with-access-reviews.md)
- [Gerenciar os programas e os controles para análises de acesso do Azure AD](manage-programs-controls.md)
- [Criar uma revisão de acesso de grupos ou aplicativos](create-access-review.md)
- [Criar uma revisão de acesso de usuários em uma função administrativa do Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md)


