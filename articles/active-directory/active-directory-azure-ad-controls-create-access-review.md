---
title: "Criar uma revisão de acesso de membros de um grupo ou usuários com acesso a um aplicativo no Azure AD | Microsoft Docs"
description: "Saiba como criar uma revisão de acesso para os membros de um grupo ou usuários para o acesso a um aplicativo."
services: active-directory
author: markwahl-msft
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: ae926e8d3a856c9b488d46b481a1c7b5b581d532
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/02/2017
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>Criar uma revisão de acesso de membros do grupo ou acesso do aplicativo com o Azure AD

As atribuições de acesso se tornam "obsoletas" quando os usuários possuem um acesso que não precisam mais. Para reduzir os riscos associados às atribuições de acesso obsoletas, os administradores podem usar o Azure Active Directory (Azure AD) para criar uma análise de acesso para membros do grupo ou usuários atribuídos a um aplicativo. Para obter mais informações sobre esses cenários, consulte [Gerenciar acesso de usuário](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md) e [Gerenciar acesso de convidado](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md). 

## <a name="create-an-access-review"></a>Criar uma análise de acesso

1. Como administrador global, vá até a [página de revisões de acesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) e selecione **Programas**.

2. Selecione o programa que mantém o controle de análise de acesso que deseja criar. O **Programa padrão** está sempre presente ou você pode criar um programa diferente. Por exemplo, é possível optar por ter um programa para cada iniciativa de conformidade ou meta de negócios.

3. Dentro do programa, selecione **Controles** e, em seguida, selecione **Adicionar** para adicionar um controle.

4. Nomeie cada análise de acesso. Como opção, forneça uma descrição para cada análise. O nome é mostrado para os revisores.

5. Defina as datas de início e término. Por padrão, uma análise de acesso é iniciada no mesmo dia em que é criada e termina em um mês. Você pode alterar as datas de início e de término para iniciar uma análise de acesso em uma data futura e que dure quantos dias você desejar.

6. As revisões de acesso podem ser de membros de um grupo ou usuários que foram atribuídos a um aplicativo. Você pode detalhar ainda mais o escopo da análise de acesso para examinar apenas os usuários convidados que são membros (ou atribuídos ao aplicativo), em vez de examinar todos os usuários que são membros ou que têm acesso ao aplicativo.

7. Selecione uma ou mais pessoas para examinar todos os usuários no escopo. Ou você pode selecionar para que os membros examinem seus próprios acessos. Se o recurso for um grupo, você pode pedir que a revisão seja realizada pelos proprietários de grupo. Você também pode exigir que os revisores forneçam um motivo ao aprovar o acesso.

8. Por fim, selecione **Iniciar**.


## <a name="manage-the-access-review"></a>Gerenciar a análise de acesso

Por padrão, o Azure AD envia um email para os revisores logo após o início da análise. Se você optar pelo não envio do email pelo Azure AD, certifique-se de informar aos revisores que eles devem concluir uma análise de acesso pendente. Você pode mostrar a eles as instruções de como [examinar o acesso](active-directory-azure-ad-controls-perform-access-review.md). Se sua análise for para convidados que examinam o próprio acesso, mostre a eles as instruções de como [examinar seu próprio acesso](active-directory-azure-ad-controls-perform-access-review.md).

Caso alguns revisores sejam convidados, os convidados serão notificados apenas por email se já tiverem aceitado seus convites.


É possível acompanhar o andamento conforme os revisores concluem suas revisões no painel do Azure AD na seção **Revisões de Acesso**. Nenhum direito de acesso será alterado no diretório até que a [análise seja concluída](active-directory-azure-ad-controls-complete-access-review.md).

## <a name="next-steps"></a>Próximas etapas

Quando uma análise de acesso é iniciada, o Azure AD enviará automaticamente aos revisores um email solicitando que examinem o acesso. Se um usuário não tiver recebido um email, você poderá enviar a ele as instruções de como [analisar o acesso](active-directory-azure-ad-controls-perform-access-review.md). 

Depois que o período de análise de acesso tiver acabado ou se o administrador tiver interrompido a análise de acesso, siga as etapas em [Concluir uma análise de acesso](active-directory-azure-ad-controls-complete-access-review.md) para ver e aplicar os resultados.


