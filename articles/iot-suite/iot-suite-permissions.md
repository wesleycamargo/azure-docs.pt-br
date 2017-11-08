---
title: Azure IoT Suite e Azure Active Directory | Microsoft Docs
description: "Descreve como o Pacote IoT do Azure usa o Active Directory do Azure para gerenciar permissões."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 246228ba-954a-4d96-b6d6-e53e4590cb4f
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2017
ms.author: dobett
ms.openlocfilehash: 84d2bcc4ccc102f03d878bfede43672158469190
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2017
---
# <a name="permissions-on-the-azureiotsuitecom-site"></a>Permissões no site azureiotsuite.com

## <a name="what-happens-when-you-sign-in"></a>O que acontece quando você entra

Quando você entra pela primeira vez no [azureiotsuite.com][lnk-azureiotsuite], o site determina os níveis de permissão que você tem com base no locatário do AAD (Azure Active Directory) e na assinatura do Azure selecionados no momento.

1. Primeiro, para preencher a lista de locatários logo ao lado de seu nome de usuário, site descobre a quais locatários do AAD no Azure você pertence. No momento, o site só consegue obter os tokens de usuário de um locatário por vez. Por isso, quando você alternar locatários usando o menu suspenso no canto superior direito, o site conectará você a esse locatário para obter os tokens para ele.

2. Em seguida, o site descobre no Azure quais assinaturas estão associadas ao locatário selecionado. Você verá as assinaturas quando criar uma nova solução pré-configurada.

3. Por fim, o site recuperará todos os recursos nas assinaturas e nos grupos de recursos marcados como soluções pré-configuradas e preencherá os blocos na home page.

As seções a seguir descrevem as funções que controlam o acesso às soluções pré-configuradas.

## <a name="aad-roles"></a>Funções do AAD

As funções do AAD controlam as soluções pré-configuradas de provisão de capacidade e gerenciam usuários em uma solução pré-configurada.

Saiba mais sobre funções de administrador no AAD em [Atribuir funções de administrador no Azure AD][lnk-aad-admin]. O artigo atual enfoca as funções do diretório **Administrador Global** e **Usuário** conforme utilizadas pelas soluções pré-configuradas.

### <a name="global-administrator"></a>Administrador global

Pode haver muitos administradores globais por locatário do AAD:

* Quando você cria um locatário do AAD, por padrão vira o administrador global desse locatário.
* O administrador global pode provisionar soluções pré-configuradas básicas e padrão.

### <a name="domain-user"></a>Usuário de domínio

Pode haver muitos usuários de domínio por locatário do AAD:

* Um usuário do domínio pode provisionar uma solução pré-configurada básica por meio do site [azureiotsuite.com][lnk-azureiotsuite].
* Um usuário de domínio pode criar uma solução pré-configurada básica usando a CLI.

### <a name="guest-user"></a>Usuário Convidado

Pode haver muitos usuários convidados por locatário do AAD. Os usuários convidados têm um conjunto limitado de direitos no locatário do AAD. Como resultado, os usuários convidados não podem provisionar uma solução pré-configurada no locatário do AAD.

Para obter mais informações sobre usuários e funções no AAD, confira os seguintes recursos:

* [Criar usuários no Azure AD][lnk-create-edit-users]
* [Atribuir usuários a aplicativos][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Funções de administrador da assinatura do Azure

As funções de administrador do Azure controlam a capacidade de mapear uma assinatura do Azure para um locatário do AD.

Descubra mais sobre as funções de administrador do Azure no artigo [Como adicionar ou alterar o Coadministrador, o Administrador de Serviços e o Administrador da Conta do Azure][lnk-admin-roles].

## <a name="faq"></a>Perguntas frequentes

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>Sou um administrador de serviços e gostaria de alterar o mapeamento de diretório entre minha assinatura e um locatário do AAD específico. Como posso concluir esta tarefa?

Consulte [Como adicionar uma assinatura existente ao seu diretório do Azure AD](../active-directory/active-directory-how-subscriptions-associated-directory.md#to-add-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>Quero alterar um Administrador de Serviços ou um Coadministrador quando o logon for feito com uma conta organizacional

Consulte o artigo de suporte [Alterando um Administrador de Serviços e um Coadministrador quando o logon for feito com uma conta organizacional][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Por que vejo este erro? "Sua conta não tem as permissões adequadas para criar uma solução. Verifique com o administrador da conta ou tente com uma conta diferente."

Observe o seguinte diagrama para obter orientação:

![][img-flowchart]

> [!NOTE]
> Se você continuar recebendo o erro após validar que é um administrador global no locatário do AAD e um coadministrador na assinatura, peça ao administrador da conta que remova o usuário e reatribua as permissões necessárias nesta ordem. Primeiro, adicione o usuário como um administrador global e adicionar o usuário como um coadministrador na assinatura do Azure. Se o problema persistir, entre em contato com [Ajuda e Suporte][lnk-help-support].

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Por que estou vendo este erro se eu tenho uma assinatura do Azure? “Uma assinatura do Azure é necessária para criar soluções pré-configuradas. Você pode criar uma conta de avaliação gratuita em apenas alguns minutos.”

Se você tiver certeza de que tem uma assinatura do Azure, valide o mapeamento do locatário para a sua assinatura e certifique-se de que o locatário correto tenha sido selecionado na lista suspensa. Se você tiver validado corretamente o locatário desejado, siga o diagrama acima e valide o mapeamento de sua assinatura e este locatário do AAD.

## <a name="next-steps"></a>Próximas etapas
Para continuar aprendendo sobre o IoT Suite, veja como é possível [personalizar uma solução pré-configurada][lnk-customize].

[img-flowchart]: media/iot-suite-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]: ../active-directory/active-directory-assign-admin-roles.md
[lnk-portal]: https://portal.azure.com/
[lnk-create-edit-users]: ../active-directory/active-directory-create-users.md
[lnk-assign-app-roles]: ../active-directory/active-directory-coreapps-assign-user-azure-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
