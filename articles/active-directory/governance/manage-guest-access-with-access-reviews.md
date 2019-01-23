---
title: Gestão do acesso de convidado com revisões de acesso do Azure AD | Microsoft Docs
description: Gerenciar usuários convidados como membros de um grupo ou atribuídos a um aplicativo com revisões de acesso do Azure Active Directory
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
ms.component: compliance
ms.date: 12/13/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 4d1a9696587ae128b35970414c6c8753179c3f81
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332567"
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Gerenciar o acesso de convidado com revisões de acesso do Azure AD


Com o Azure AD (Azure Active Directory), você pode habilitar com facilidade a colaboração em limites organizacionais usando o [recurso B2B do Azure AD](../b2b/what-is-b2b.md). Usuários convidados de outros locatários podem ser [convidados por administradores](../b2b/add-users-administrator.md) ou por [outros usuários](../b2b/what-is-b2b.md). Isso também se aplica às identidades sociais, como contas da Microsoft.

Você também pode assegurar com facilidade que os usuários convidados tenham o acesso apropriado. Você pode pedir que os próprios convidados ou que um tomador de decisão participem de uma revisão de acesso e reconfirmem (ou atestem) o acesso de convidado. Revisores podem fornecer a sua entrada na necessidade de cada usuário de acesso contínuo, com base nas sugestões do Azure AD. Quando uma revisão de acesso for finalizada, você poderá alterar e remover o acesso de convidados que não precisam mais dela.

> [!NOTE]
> Este documento se concentra na revisão do acesso de usuários convidados. Se você quiser revisar o acesso de todos os usuários, não apenas convidados, consulte [Gestão do acesso do usuário com revisões de acesso](manage-user-access-with-access-reviews.md). Se você deseja revisar a associação dos usuários em funções administrativas, como administrador global, consulte [Iniciar uma revisão de acesso no Azure AD Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md). 
>
>

## <a name="prerequisites"></a>Pré-requisitos 


As revisões de acesso estão disponíveis com a edição Premium P2 do Azure AD, que está incluída no Microsoft Enterprise Mobility + Security, E5. Para obter mais informações, consulte [Edições do Active Directory do Azure](../fundamentals/active-directory-whatis.md). Cada usuário que interage com esse recurso, inclusive para criar uma revisão, preencher uma revisão ou confirmar seu acesso, deve ter uma licença. 

Você também pode solicitar que usuários convidados revisem o próprio acesso. Para cada licença paga do Azure AD Premium P2 atribuída a um dos usuários de sua própria organização, você poderá usar o B2B para convidar até cinco usuários convidados sob a Provisão de Usuário Externo. Esses usuários convidados também poderão usar os recursos do Azure AD Premium P2. Para obter mais informações, consulte [Licenciamento de colaboração B2B do Azure AD](../b2b/licensing-guidance.md).

## <a name="create-and-perform-an-access-review-for-guests"></a>Criar e executar uma revisão de acesso para convidados

Primeiro, como um administrador global ou administrador da conta de usuário, vá para a [página de revisões de acesso](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) para garantir que as revisões de acesso estejam prontas para a sua organização. 

O Azure AD proporciona vários cenários para revisão de usuários convidados.

Você pode revisar uma das seguintes opções:

 - Um grupo no Azure AD que tenha um ou mais convidados como membros.
 - Um aplicativo conectado ao Azure AD que tenha um ou mais usuários convidados atribuídos a ele. 

Você pode decidir se deseja pedir que cada convidado revise seu próprio acesso ou solicitar que um ou mais usuários revisem o acesso de cada convidado.

 Esses cenários são abordados nas seções a seguir.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Pedir que os convidados revisem suas próprias associações em um grupo

As revisões de acesso podem ser usadas para garantir que usuários convidados e adicionados a um grupo ainda precisam do acesso. Você pode pedir que os convidados revisem suas próprias associações em tal grupo.

1. Para iniciar uma revisão de acesso para o grupo, selecione que a revisão inclua apenas membros usuários convidados e que os membros sejam responsáveis pela própria revisão. Para obter mais informações, confira [Criar uma revisão de acesso](create-access-review.md).

2. Peça que cada convidado revise a própria associação. Por padrão, cada convidado que aceita um convite receberá um email do Azure AD com um link para a revisão de acesso. O Azure AD tem instruções para convidados sobre [como revisar o acesso](perform-access-review.md).

3. Depois dos revisores enviarem a avaliação, pare a revisão de acesso e aplique as alterações. Para obter mais informações, confira [Concluir uma revisão de acesso](complete-access-review.md).

4. Além daqueles usuários que negaram a necessidade de continuar com o acesso, convém também remover os usuários que não responderam. Usuários que não responderam possivelmente não recebem mais emails.

5. Se o grupo não estiver sendo usado para gerenciamento de acesso, você também poderá remover usuários que não foram selecionados para participar da revisão por não terem aceitado o convite. Caso o usuário não aceite, isso pode indicar que o endereço de email do usuário convidado continha um erro de digitação. Se um grupo for usado como uma lista de distribuição, talvez alguns usuários convidados não tenham sido selecionados para participarem por serem objetos de contato.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Pedir que um responsável revise a associação de um convidado em um grupo

Você pode pedir a um responsável, como o proprietário de um grupo, que revise a necessidade do convidado de continuar com a associação em um grupo.

1. Para iniciar uma revisão de acesso para o grupo, selecione que a revisão inclua apenas membros usuários convidados. Em seguida, especifique um ou mais revisores. Para obter mais informações, confira [Criar uma revisão de acesso](create-access-review.md).

2. Solicite que os revisores forneçam a entrada. Por padrão, todos receberão um email do Azure AD com um link para o painel de acesso, no qual poderão [executar sua revisão de acesso](perform-access-review.md).

3. Depois dos revisores enviarem a avaliação, pare a revisão de acesso e aplique as alterações. Para obter mais informações, confira [Concluir uma revisão de acesso](complete-access-review.md).

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Pedir que os convidados revisem seus próprios acessos a um aplicativo

As revisões de acesso podem ser usadas para garantir que os usuários convidados de um aplicativo específico ainda precisam do acesso. Você pode pedir que os próprios convidados revisem suas necessidades de acesso.

1. Para iniciar uma revisão de acesso para o aplicativo, selecione que a revisão inclua apenas convidados e que os usuários sejam responsáveis pela própria revisão de acesso. Para obter mais informações, confira [Criar uma revisão de acesso](create-access-review.md).

2. Peça que cada convidado revise seu próprio acesso ao aplicativo. Por padrão, cada convidado que aceita um convite receberá um email do Azure AD. Esse o email tem um link para a revisão de acesso no painel de acesso da sua organização. O Azure AD tem instruções para convidados sobre [como revisar o acesso](perform-access-review.md).

3. Depois dos revisores enviarem a avaliação, pare a revisão de acesso e aplique as alterações. Para obter mais informações, consulte [Concluir uma revisão de acesso](complete-access-review.md).

4. Além dos usuários que negaram a necessidade de continuar com o acesso, convém também remover os usuários convidados que não responderam. Usuários que não responderam possivelmente não recebem mais emails. Você também pode remover usuários convidados que não foram selecionados para participar, especialmente se eles não foram convidados recentemente. Tais usuários não aceitaram o convite e, portanto, não tiveram acesso ao aplicativo. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Pedir que um responsável revise o acesso de um convidado a um aplicativo

Você pode pedir a um responsável, como o proprietário de um aplicativo, que analise a necessidade do convidado de continuar com o acesso ao aplicativo.

1. Para iniciar uma revisão de acesso para o aplicativo, selecione que a revisão inclua apenas convidados. Em seguida, especifique um ou mais usuários como revisores. Para obter mais informações, confira [Criar uma revisão de acesso](create-access-review.md).

2. Solicite que os revisores forneçam a entrada. Por padrão, todos receberão um email do Azure AD com um link para o painel de acesso, no qual poderão [executar sua revisão de acesso](perform-access-review.md).

3. Depois dos revisores enviarem a avaliação, pare a revisão de acesso e aplique as alterações. Para obter mais informações, confira [Concluir uma revisão de acesso](complete-access-review.md).

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Pedir que os convidados revisem a própria necessidade de acesso em geral

Em algumas organizações, talvez os convidados não estejam cientes de suas associações de grupo.

> [!NOTE]
> Versões anteriores do portal do Azure não permitem acesso administrativo por usuários com UserType definido como convidado. Em alguns casos, um administrador no seu diretório pode ter alterado o valor de UserType do convidado para Membro usando o PowerShell. Se essa alteração ocorreu anteriormente em seu diretório, a consulta anterior pode não incluir todos os usuários convidados que historicamente tinham direitos de acesso administrativo. Nesse caso, você precisa alterar o UserType do convidado ou incluir o convidado manualmente na associação de grupo.

1. Crie um grupo de segurança no Azure AD com os convidados como membros, caso um grupo adequado ainda não exista. Por exemplo, você pode criar um grupo com uma associação de convidados mantida manualmente. Ou, criar um grupo dinâmico com um nome como "Convidados da Contoso" para usuários no locatário Contoso que têm o valor do atributo UserType de Convidado.  Para eficiência, verifique se o grupo é formado predominantemente de convidados. Não selecione um grupo que tenha usuários membros, pois eles não precisam ser revisados.  Além disso, tenha em mente que um usuário convidado que é um membro do grupo pode ver os outros membros do grupo.

2. Para iniciar uma revisão de acesso para esse grupo, selecione os revisores como os próprios membros. Para obter mais informações, confira [Criar uma revisão de acesso](create-access-review.md).

3. Peça que cada convidado revise a própria associação. Por padrão, cada convidado que aceita um convite receberá um email do Azure AD com um link para a revisão de acesso no painel de acesso de sua organização. O Azure AD tem instruções para convidados sobre [como revisar o acesso](perform-access-review.md).  Os convidados que não aceitarem o convite aparecerão nos resultados da análise como "Não Notificado".

4. Depois dos revisores enviarem a avaliação, pare a revisão de acesso. Para obter mais informações, confira [Concluir uma revisão de acesso](complete-access-review.md).

5. Remova o acesso de convidado para convidados que foram negados, não completaram a revisão ou não tinham aceito o convite anteriormente. Se alguns dos convidados forem contatos selecionados para participar da revisão ou não aceitarem um convite anteriormente, você poderá desabilitar as contas deles usando o Portal do Azure ou PowerShell. Se o convidado não precisar mais de acesso e não for um contato, você poderá remover o objeto de usuário do diretório usando o Portal do Azure ou o PowerShell para excluir o objeto de usuário convidado.

## <a name="next-steps"></a>Próximas etapas

[Como criar uma revisão de acesso para os membros de um grupo ou para o acesso a um aplicativo](create-access-review.md)







