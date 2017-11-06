---
title: "Gestão do acesso de convidado com revisões de acesso do Azure AD | Microsoft Docs"
description: "Gerenciar usuários convidados como membros de um grupo ou atribuídos a um aplicativo com revisões de acesso do Azure Active Directory"
services: active-directory
documentationcenter: 
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
ms.openlocfilehash: d67b12eaac17e278724ddf8670e65afba235f099
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/02/2017
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Gerenciar o acesso de convidado com revisões de acesso do Azure AD


Com o Azure AD (Azure Active Directory), você pode habilitar com facilidade a colaboração em limites organizacionais usando o [recurso B2B do Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md). Usuários convidados de outros locatários podem ser [convidados por administradores](active-directory-b2b-admin-add-users.md) ou por [outros usuários](active-directory-b2b-how-it-works.md). Isso também se aplica às identidades sociais, como contas da Microsoft.

Você também pode assegurar com facilidade que os usuários convidados tenham o acesso apropriado. Você pode pedir que os próprios convidados ou que um tomador de decisão participem de uma revisão de acesso e reconfirmem (ou atestem) o acesso de convidado. Revisores podem fornecer a sua entrada na necessidade de cada usuário de acesso contínuo, com base nas sugestões do Azure AD. Quando uma revisão de acesso for finalizada, você poderá alterar e remover o acesso de convidados que não precisam mais dela.

> [!NOTE]
> Este documento se concentra na revisão do acesso de usuários convidados. Se você quiser revisar o acesso de todos os usuários, não apenas convidados, consulte [Gestão do acesso do usuário com revisões de acesso](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md). Se você deseja revisar a associação dos usuários em funções administrativas, como administrador global, consulte [Iniciar uma revisão de acesso no Azure AD Privileged Identity Management](active-directory-privileged-identity-management-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Pré-requisitos 

As revisões de acesso estão disponíveis com a edição Premium P2 do Azure AD, que está incluída no Microsoft Enterprise Mobility + Security, E5. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md). Cada usuário que interage com esse recurso para criar, acessar ou aplicar uma revisão deve ter uma licença.

Se você planeja solicitar que os usuários convidados revisem seu próprio acesso, leia sobre licenciamento de usuário convidado. Para obter mais informações, consulte [Licenciamento de colaboração B2B do Azure AD](active-directory-b2b-licensing.md).

## <a name="create-and-perform-an-access-review-for-guests"></a>Criar e executar uma revisão de acesso para convidados

Primeiro, permita que as revisões de acesso apareçam nos painéis de acesso do revisor. Como administrador global, acesse a [página de revisões de acesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/). 

O Azure AD proporciona vários cenários para revisão de usuários convidados.

Selecione uma das seguintes:

 - Um grupo no Azure AD que tenha um ou mais convidados como membros.
 - Um aplicativo conectado ao Azure AD que tenha um ou mais usuários convidados atribuídos a ele. 

Você pode decidir se deseja pedir que cada convidado revise seu próprio acesso ou solicitar que um ou mais usuários revisem o acesso de cada convidado.

 Esses cenários são abordados nas seções a seguir.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Pedir que os convidados revisem suas próprias associações em um grupo

As revisões de acesso podem ser usadas para garantir que usuários convidados e adicionados a um grupo ainda precisam do acesso. Você pode pedir que os convidados revisem suas próprias associações em tal grupo.

1. Para iniciar uma revisão de acesso para o grupo, selecione que a revisão inclua apenas membros usuários convidados e que os membros sejam responsáveis pela própria revisão. Para obter mais informações, consulte [Criar uma revisão de acesso](active-directory-azure-ad-controls-create-access-review.md).

2. Peça que cada convidado revise a própria associação. Por padrão, cada convidado que aceita um convite receberá um email do Azure AD com um link para a revisão de acesso. O Azure AD tem instruções para convidados sobre [como revisar o acesso](active-directory-azure-ad-controls-perform-access-review.md).

3. Depois dos revisores enviarem a avaliação, pare a revisão de acesso e aplique as alterações. Para obter mais informações, consulte [Concluir uma revisão de acesso](active-directory-azure-ad-controls-complete-access-review.md).

4. Além dos usuários que negaram a necessidade de continuar com o acesso, convém também remover os usuários que não responderam. Usuários que não responderam possivelmente não recebem mais emails.

5. Se o grupo não estiver sendo usado para gerenciamento de acesso, você também poderá remover usuários que não foram selecionados para participar da revisão por não terem aceitado o convite. Caso o usuário não aceite, isso pode indicar que o endereço de email do usuário convidado continha um erro de digitação. Se um grupo for usado como uma lista de distribuição, talvez alguns usuários convidados não tenham sido selecionados para participarem por serem objetos de contato.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Pedir que um responsável revise a associação de um convidado em um grupo

Você pode pedir a um responsável, como o proprietário de um grupo, que revise a necessidade do convidado de continuar com a associação em um grupo.

1. Para iniciar uma revisão de acesso para o grupo, selecione que a revisão inclua apenas membros usuários convidados. Em seguida, especifique um ou mais revisores. Para obter mais informações, consulte [Criar uma revisão de acesso](active-directory-azure-ad-controls-create-access-review.md).

2. Solicite que os revisores forneçam a entrada. Por padrão, todos receberão um email do Azure AD com um link para o painel de acesso, no qual poderão [executar sua revisão de acesso](active-directory-azure-ad-controls-perform-access-review.md).

3. Depois dos revisores enviarem a avaliação, pare a revisão de acesso e aplique as alterações. Para obter mais informações, consulte [Concluir uma revisão de acesso](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Pedir que os convidados revisem seus próprios acessos a um aplicativo

As revisões de acesso podem ser usadas para garantir que os usuários convidados de um aplicativo específico ainda precisam do acesso. Você pode pedir que os próprios convidados revisem suas necessidades de acesso.

1. Para iniciar uma revisão de acesso para o aplicativo, selecione que a revisão inclua apenas convidados e que os usuários sejam responsáveis pela própria revisão de acesso. Para obter mais informações, consulte [Criar uma revisão de acesso](active-directory-azure-ad-controls-create-access-review.md).

2. Peça que cada convidado revise seu próprio acesso ao aplicativo. Por padrão, cada convidado que aceita um convite receberá um email do Azure AD com um link para a revisão de acesso no painel de acesso de sua organização. O Azure AD tem instruções para convidados sobre [como revisar o acesso](active-directory-azure-ad-controls-perform-access-review.md).

3. Depois dos revisores enviarem a avaliação, pare a revisão de acesso e aplique as alterações. Para obter mais informações, consulte [Concluir uma revisão de acesso](active-directory-azure-ad-controls-complete-access-review.md).

4. Além dos usuários que negaram a necessidade de continuar com o acesso, convém também remover os usuários convidados que não responderam. Usuários que não responderam possivelmente não recebem mais emails. Você também pode remover usuários convidados que não foram selecionados para participar, especialmente se eles não foram convidados recentemente. Tais usuários não aceitaram o convite e, portanto, não tiveram acesso ao aplicativo. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Pedir que um responsável revise o acesso de um convidado a um aplicativo

Você pode pedir a um responsável, como o proprietário de um aplicativo, que analise a necessidade do convidado de continuar com o acesso ao aplicativo.

1. Para iniciar uma revisão de acesso para o aplicativo, selecione que a revisão inclua apenas convidados. Em seguida, especifique um ou mais usuários como revisores. Para obter mais informações, consulte [Criar uma revisão de acesso](active-directory-azure-ad-controls-create-access-review.md).

2. Solicite que os revisores forneçam a entrada. Por padrão, todos receberão um email do Azure AD com um link para o painel de acesso, no qual poderão [executar sua revisão de acesso](active-directory-azure-ad-controls-perform-access-review.md).

3. Depois dos revisores enviarem a avaliação, pare a revisão de acesso e aplique as alterações. Para obter mais informações, consulte [Concluir uma revisão de acesso](active-directory-azure-ad-controls-complete-access-review.md).

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Pedir que os convidados revisem a própria necessidade de acesso em geral

Em algumas organizações, talvez os convidados não estejam cientes de suas associações de grupo.

> [!NOTE]
> Versões anteriores do portal do Azure não permitem acesso administrativo por usuários com UserType definido como convidado. Em alguns casos, um administrador no seu diretório pode ter alterado o valor de UserType do convidado para Membro usando o PowerShell. Se essa alteração ocorreu anteriormente em seu diretório, a consulta anterior pode não incluir todos os usuários convidados que historicamente tinham direitos de acesso administrativo. Nesse caso, você precisa alterar o UserType do convidado ou incluir o convidado manualmente na associação de grupo.

1. Crie um grupo de segurança no Azure AD com os convidados como membros, caso um grupo adequado ainda não exista. Por exemplo, você pode criar um grupo com uma associação de convidados mantida manualmente. Ou, criar um grupo dinâmico com um nome como "Convidados da Contoso" para usuários no locatário Contoso que têm o valor do atributo UserType de Convidado.

2. Para iniciar uma revisão de acesso para esse grupo, selecione os revisores como os próprios membros. Para obter mais informações, consulte [Criar uma revisão de acesso](active-directory-azure-ad-controls-create-access-review.md).

3. Peça que cada convidado revise a própria associação. Por padrão, cada convidado que aceita um convite receberá um email do Azure AD com um link para a revisão de acesso no painel de acesso de sua organização. O Azure AD tem instruções para convidados sobre [como revisar o acesso](active-directory-azure-ad-controls-perform-access-review.md).

4. Depois dos revisores enviarem a avaliação, pare a revisão de acesso. Para obter mais informações, consulte [Concluir uma revisão de acesso](active-directory-azure-ad-controls-complete-access-review.md).

5. Remova o acesso de convidado para convidados que foram negados, não completaram a revisão ou não tinham aceito o convite anteriormente. Se alguns dos convidados forem contatos selecionados para participar da revisão porque não aceitaram anteriormente um convite, você poderá desabilitar as contas deles usando o portal do Azure ou o PowerShell. Se o convidado não precisar mais de acesso e não for um contato, o objeto de usuário dele poderá ser removido de seu diretório usando o portal do Azure ou o PowerShell.

## <a name="next-steps"></a>Próximas etapas

[Como criar uma revisão de acesso para os membros de um grupo ou para o acesso a um aplicativo](active-directory-azure-ad-controls-create-access-review.md)







