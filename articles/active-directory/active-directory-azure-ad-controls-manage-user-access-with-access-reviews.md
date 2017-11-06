---
title: "Gestão do acesso do usuário com revisões de acesso do Azure AD | Microsoft Docs"
description: "Saiba como gerenciar o acesso de usuários como membros de um grupo ou a atribuição de um aplicativo com revisões de acesso do Azure Active Directory"
services: active-directory
documentationcenter: 
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: c4ea5d797613b214be569c6405a0fc672cae83a0
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/02/2017
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Gestão do acesso do usuário com revisões de acesso do Azure AD

Com o Azure Active Directory (Azure AD), você pode garantir facilmente que os usuários tenham o acesso apropriado. Você pode pedir aos próprios usuários, ou a um tomador de decisão, que participem de uma revisão de acesso e reconfirmar (ou atestar) o acesso dos usuários. Os revisores podem fornecer as suas respectivas avaliações sobre a necessidade de acesso contínuo de cada usuário, com base nas sugestões do Azure AD. Quando uma revisão de acesso for finalizada, você poderá alterar e remover o acesso de usuários que não precisam mais dela.

> [!NOTE]
> Se você quiser revisar somente o acesso de usuários convidados, em vez de revisar todos os tipos de acesso de usuários, confira [Gerenciamento de acesso de usuários convidados com revisões de acesso](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md). E se você desejar revisar a associação do usuário em funções administrativas, como administrador global, confira [Iniciar uma revisão de acesso no Azure AD Privileged Identity Management](active-directory-privileged-identity-management-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Pré-requisitos 

As revisões de acesso estão disponíveis com a edição Premium P2 do Azure AD, que está incluída no Microsoft Enterprise Mobility + Security, E5. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md). Cada usuário que interage com esse recurso, para criar uma revisão, acessar uma revisão ou aplicar uma revisão, deve ter uma licença.


## <a name="create-and-perform-an-access-review"></a>Criar e executar uma revisão de acesso

Você pode ter um ou mais usuários como revisores de uma revisão de acesso.  

1. Selecione um grupo no Azure AD que tenha um ou mais membros. Ou selecione um aplicativo conectado ao Azure AD que tenha um ou mais usuários atribuídos a ele. 

2. Decida se cada usuário revisará o próprio acesso ou se um ou mais usuários revisarão o acesso de todos.

3. Permita que as revisões de acesso apareçam nos painéis de acesso do revisor. Como administrador global, acesse a [página de revisões de acesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

4. Inicie a revisão de acesso. Para obter mais informações, confira [Criar uma revisão de acesso](active-directory-azure-ad-controls-create-access-review.md).

5. Solicite que os revisores forneçam a entrada. Por padrão, todos receberão um email do Azure AD com um link para o painel de acesso, onde poderão [executar sua revisão de acesso](active-directory-azure-ad-controls-perform-access-review.md).

6. Se os revisores não tiverem fornecido uma avaliação, você pode pedir ao Azure AD para enviar um lembrete a eles. Por padrão, o Azure AD enviará automaticamente um lembrete após passar a metade do tempo para o prazo final para revisores que ainda não responderam.

7. Depois dos revisores enviarem a avaliação, pare a revisão de acesso e aplique as alterações. Para obter mais informações, confira [Concluir uma revisão de acesso](active-directory-azure-ad-controls-complete-access-review.md).


## <a name="next-steps"></a>Próximas etapas

[Como criar uma revisão de acesso para os membros de um grupo ou para o acesso a um aplicativo](active-directory-azure-ad-controls-create-access-review.md)




