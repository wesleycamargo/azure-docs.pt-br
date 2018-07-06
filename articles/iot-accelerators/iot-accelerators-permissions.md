---
title: Aceleradores de solução do Azure IoT e Azure Active Directory | Microsoft Docs
description: Descreve como os aceleradores de solução do Azure IoT usam o Azure Active Directory para gerenciar permissões.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: 676d5e553e2929ae09d447141ca315fd1cc448e3
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448692"
---
# <a name="permissions-on-the-azureiotsolutionscom-site"></a>Permissões no site azureiotsolutions.com

## <a name="what-happens-when-you-sign-in"></a>O que acontece quando você entra

Quando você entra pela primeira vez no [azureiotsuite.com][lnk-azureiotsolutions], o site determina os níveis de permissão que você tem com base no locatário do AAD (Azure Active Directory) e na assinatura do Azure selecionados no momento.

1. Primeiro, para preencher a lista de locatários logo ao lado de seu nome de usuário, site descobre a quais locatários do AAD no Azure você pertence. No momento, o site só consegue obter os tokens de usuário de um locatário por vez. Por isso, quando você alternar locatários usando o menu suspenso no canto superior direito, o site conectará você a esse locatário para obter os tokens para ele.

2. Em seguida, o site descobre no Azure quais assinaturas estão associadas ao locatário selecionado. Você vê as assinaturas disponíveis quando cria um novo acelerador de solução.

3. Por fim, o site recupera todos os recursos nas assinaturas e grupos de recursos marcados como aceleradores de solução e preenche os blocos na home page.

As seções a seguir descrevem as funções que controlam o acesso aos aceleradores de solução.

## <a name="aad-roles"></a>Funções do AAD

As funções do AAD controlam a capacidade de provisionar aceleradores de solução e de gerenciar usuários e alguns serviços do Azure em um acelerador de solução.

Saiba mais sobre funções de administrador no AAD em [Atribuir funções de administrador no Azure AD][lnk-aad-admin]. O artigo atual enfoca as funções do diretório **Administrador Global** e **Usuário**, conforme utilizadas pelos aceleradores de solução.

### <a name="global-administrator"></a>Administrador global

Pode haver muitos administradores globais por locatário do AAD:

* Quando você cria um locatário do AAD, por padrão vira o administrador global desse locatário.
* O administrador global pode provisionar aceleradores de solução básico e padrão (uma implantação básica usa uma única Máquina Virtual do Azure).

### <a name="domain-user"></a>Usuário de domínio

Pode haver muitos usuários de domínio por locatário do AAD:

* Um usuário do domínio pode provisionar um acelerador de solução básico por meio do site [azureiotsolutions.com][lnk-azureiotsolutions].
* Um usuário de domínio pode criar um acelerador de solução básico usando a CLI.

### <a name="guest-user"></a>Usuário Convidado

Pode haver muitos usuários convidados por locatário do AAD. Os usuários convidados têm um conjunto limitado de direitos no locatário do AAD. Como resultado, os usuários convidados não podem provisionar um acelerador de solução no locatário do AAD.

Para obter mais informações sobre usuários e funções no AAD, confira os seguintes recursos:

* [Criar usuários no Azure AD][lnk-create-edit-users]
* [Atribuir usuários a aplicativos][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Funções de administrador da assinatura do Azure

As funções de administrador do Azure controlam a capacidade de mapear uma assinatura do Azure para um locatário do AAD.

Descubra mais sobre as funções de administrador do Azure no artigo [Como adicionar ou alterar o Coadministrador, o Administrador de Serviços e o Administrador da Conta do Azure][lnk-admin-roles].

## <a name="faq"></a>Perguntas frequentes

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>Sou um administrador de serviços e gostaria de alterar o mapeamento de diretório entre minha assinatura e um locatário do AAD específico. Como posso concluir esta tarefa?

Consulte [Como adicionar uma assinatura existente ao seu diretório do Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Quero alterar um Administrador de Serviços ou um Coadministrador quando o logon for feito com uma conta organizacional

Confira o artigo de suporte [Alterando um Administrador de Serviços e um Coadministrador quando o logon for feito com uma conta organizacional][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Por que vejo este erro? "Sua conta não tem as permissões adequadas para criar uma solução. Verifique com o administrador da conta ou tente com uma conta diferente."

Observe o seguinte diagrama para obter orientação:

![][img-flowchart]

> [!NOTE]
> Se você continuar recebendo o erro após validar que é um administrador global no locatário do AAD e um coadministrador na assinatura, peça ao administrador da conta que remova o usuário e reatribua as permissões necessárias nesta ordem. Primeiro, adicione o usuário como um administrador global e adicione o usuário como um coadministrador na assinatura do Azure. Se o problema persistir, entre em contato com [Ajuda e Suporte][lnk-help-support].

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Por que estou vendo este erro se eu tenho uma assinatura do Azure? “Uma assinatura do Azure é necessária para criar soluções pré-configuradas. Você pode criar uma conta de avaliação gratuita em apenas alguns minutos.”

Se você tiver certeza de que tem uma assinatura do Azure, valide o mapeamento do locatário para a sua assinatura e certifique-se de que o locatário correto tenha sido selecionado na lista suspensa. Se você tiver validado corretamente o locatário desejado, siga o diagrama acima e valide o mapeamento de sua assinatura e este locatário do AAD.

## <a name="next-steps"></a>Próximas etapas
Para continuar a aprender sobre aceleradores de solução IoT, veja como é possível [personalizar um acelerador de solução][lnk-customize].

[img-flowchart]: media/iot-accelerators-permissions/flowchart.png

[lnk-azureiotsolutions]: https://www.azureiotsolutions.com
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]:../active-directory/users-groups-roles/directory-assign-admin-roles.md
[lnk-portal]: https://portal.azure.com
[lnk-create-edit-users]:../active-directory/fundamentals/active-directory-users-profile-azure-portal.md
[lnk-assign-app-roles]:../active-directory/manage-apps/assign-user-or-group-access-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-accelerators-remote-monitoring-customize.md
