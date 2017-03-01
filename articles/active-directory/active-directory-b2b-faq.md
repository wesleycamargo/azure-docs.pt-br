---
title: "Perguntas frequentes sobre a colaboração B2B do Azure Active Directory | Microsoft Docs"
description: "Perguntas frequentes sobre colaboração B2B do Azure Active Directory"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/16/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0c07c842ba8c6214d6746b0361af7b416069a6f5
ms.openlocfilehash: 3e92da61128a18c42726f4c3c85cd6e5024a697f
ms.lasthandoff: 02/17/2017


---

# <a name="azure-active-directory-b2b-collaboration-frequently-asked-questions-faq"></a>Perguntas frequentes sobre a colaboração B2B do Azure Active Directory

As perguntas frequentes são atualizadas periodicamente para refletir novos interesses.

### <a name="is-this-functionality-available-in-the-azure-classic-portal"></a>Essa funcionalidade está disponível no Portal Clássico do Azure?
Os novos recursos nessa atualização da visualização pública de colaboração B2B do Azure AD estão disponíveis apenas por meio do [Portal do Azure](https://portal.azure.com) e no novo Painel de Acesso. Experimente!

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Os usuários de colaboração B2B podem acessar o SharePoint Online e o OneDrive?
Os usuários convidados de colaboração B2B estão no diretório. Você pode adicioná-los a grupos nos quais pode permitir os sites do SharePoint Online e do OneDrive, ou até mesmo selecioná-los diretamente seletor de pessoas do SharePoint Online. Como eles são usuários convidados, o compartilhamento externo deve estar habilitado nos sites do SharePoint Online.

### <a name="is-the-csv-upload-mechanism-still-supported"></a>O mecanismo de carregamento CSV ainda tem suporte?
Sim. Consulte o exemplo de PowerShell que incluímos.

### <a name="how-can-i-customize-my-invitation-emails"></a>Como posso personalizar meus emails de convite?
É possível personalizar quase tudo no processo de convite usando as APIs de convite B2B.

### <a name="can-the-invited-external-user-leave-the-organization-to-which-he-was-invited"></a>O usuário externo convidado pode deixar a organização para a qual ele foi convidado?
No momento, isso não está disponível na atualização da visualização pública.

### <a name="can-i-use-my-microsoft-account-johncontosomicrosoftacctcom-to-sign-in-to-resources"></a>Posso usar minha conta da Microsoft (John@contosomicrosoftacct.com) entrar nos recursos?
Não é possível usar sua conta da Microsoft durante esta atualização da visualização pública. Se você tiver um sufixo de conta não padrão da Microsoft (possivelmente para email corporativo, como @contoso.com),, um locatário do Azure Active Directory será criado para seu uso.

### <a name="now-that-multi-factor-authentication-mfa-is-available-for-guest-users-can-they-also-reset-their-mfa-method"></a>Agora que a MFA (autenticação multifator) está disponível para usuários convidados, eles também podem redefinir o método MFA?
Sim, da mesma forma que os usuários normais podem.

### <a name="which-organization-is-responsible-for-mfa-licenses"></a>Qual organização é responsável pelas licenças de MFA?
A organização que convida é a responsável pela MFA. Assim, a organização que convida tem que se certificar de que existam licenças suficientes para seus usuários de B2B que executam a MFA.

### <a name="what-if-my-partner-org-already-has-mfa-set-up-can-we-trust-their-mfa-and-not-use-our-mfa"></a>E se a MFA de minha org. parceira já estiver configurada? Podemos confiar na MFA dela e não usar a nossa MFA?
Não nesta atualização de visualização pública, mas ofereceremos suporte a isso em versões futuras. Quando isso acontecer, você poderá selecionar parceiros específicos para excluir da MFA (da organização que convida).

### <a name="how-can-i-achieve-delayed-invitations"></a>Como posso lidar com convites atrasados?
Algumas organizações querem adicionar usuários de colaboração B2B, provisioná-los para aplicativos que exigem o provisionamento e, em seguida, enviar os convites. Se esse for o seu caso, use a API de convite de colaboração B2B para personalizar o fluxo de integração.

### <a name="can-guest-users-and-contacts-co-exist"></a>Os contatos e usuários convidados podem coexistir?
Talvez sua organização tenha adicionado contatos que representam colaboradores externos para que eles apareçam na Lista de Endereços Global como sugestões de endereço de email durante a composição do email. Você deve estar se perguntando o que acontece agora quando você adiciona esses mesmos colaboradores como usuários de colaboração B2B no diretório, certo? Em uma versão futura, os usuários de colaboração B2B e seus objetos de contato serão capazes de coexistir no diretório de sua empresa. Fique atento aos nossos anúncios!

### <a name="can-i-make-my-guest-users-limited-admins"></a>Posso tornar meus usuários convidados administradores limitados?
Com certeza. Se essa for a necessidade de sua organização, saiba como em [Como adicionar usuários convidados a uma função](active-directory-users-assign-role-azure-portal.md).

### <a name="does-azure-ad-b2b-collaboration-support-permitting-b2b-users-to-access-the-azure-portal"></a>A colaboração B2B do Azure AD oferece suporte à permissão de acesso de usuários B2B ao Portal do Azure?
Os usuários de colaboração B2B não devem precisar de acesso ao Portal do Azure, a menos que eles recebam uma função de administrador limitado ou de administrador global. Nesse caso, eles podem acessar o portal. Se um usuário convidado que não esteja nessas funções acessar o portal, ele/ela poderá acessar determinadas partes da experiência, pois a função de usuário Convidado tem determinadas permissões no diretório, conforme descrito nas seções anteriores.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Posso bloquear o acesso ao Portal do Azure para usuários convidados?
Sim! Mas tenha cuidado ao configurar essa política para evitar o bloqueio acidental do acesso a membros e administradores.
Você pode bloquear o acesso ao [Portal do Azure](https://portal.azure.com) por usuários convidados por meio da política de acesso condicional na API de Gerenciamento de Serviços do Windows Azure, executando as três etapas a seguir.
1. Modifique o grupo **Todos os Usuários** para conter apenas Membros![](media/active-directory-b2b-faq/modify-all-users-group.png)
2. Crie um grupo dinâmico que contém usuários Convidados![](media/active-directory-b2b-faq/group-with-guest-users.png)
3. Configure uma política de acesso condicional para impedir que os usuários convidados acessem o portal, conforme mostrado no vídeo a seguir.

  >[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player]

  Se o vídeo não aparecer incorporado, acesse-o [aqui](https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user).

### <a name="what-is-the-timeline-by-which-azure-ad-b2b-collaboration-will-start-support-for-mfa-and-consumer-email-accounts"></a>Qual é o cronograma para início do suporte para MFA e para contas de email do consumidor na colaboração B2B do Azure AD?
A MFA e as contas de email do consumidor já têm suporte nesta atualização da visualização pública.

### <a name="what-is-the-ga-timeline-for-azure-ad-b2b"></a>Qual é o cronograma de lançamento para o Azure AD B2B?
Isso vai depender dos comentários recebidos dos clientes sobre o conjunto de recursos atual.

### <a name="is-there-a-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Há algum plano para oferecer suporte à redefinição de senha para usuários de colaboração B2B do Azure AD?
Sim, há suporte para usuários (convidados) de colaboração B2B.

### <a name="is-it-also-enabled-for-users-in-a-viral-tenant"></a>Isso também está habilitado para usuários em um locatário viral?
Não atualmente.

### <a name="does-microsoft-crm-provide-online-support-to-azure-ad-b2b-collaboration"></a>O Microsoft CRM fornece suporte online para colaboração B2B do Azure AD?
CRM fornecerá suporte à colaboração B2B do Azure AD depois que estiver totalmente disponível.

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Qual é o tempo de vida de uma senha inicial para um usuário de colaboração B2B recém-criado?
O Azure AD tem um conjunto fixo de requisitos de caracteres, de força da senha e bloqueio de conta que é aplicado igualmente a todas as contas de usuário de nuvem do Azure AD. Contas de usuário de nuvem são as contas que não são federadas com outro provedor de identidade, como a conta da Microsoft, Facebook, ADFS ou até mesmo de outro locatário de nuvem (no caso da colaboração B2B). Para contas federadas, a política de senha depende da política no locatário local nas e configurações de conta do usuário da Microsoft.

### <a name="next-steps"></a>Próximas etapas

Procure nossos outros artigos sobre a colaboração B2B do AD do Azure:

* [O que é a colaboração B2B do AD do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Como os administradores do Azure Active Directory adicionam usuários de colaboração B2B?](active-directory-b2b-admin-add-users.md)
* [Como os operadores de informação adicionam usuários de colaboração B2B?](active-directory-b2b-iw-add-users.md)
* [The elements of the B2B collaboration invitation email](active-directory-b2b-invitation-email.md) (Os elementos do email de convite para colaboração B2B)
* [Resgate de convite de colaboração B2B](active-directory-b2b-redemption-experience.md)
* [Licenciamento da colaboração B2B do Azure AD](active-directory-b2b-licensing.md)
* [Solução de problemas de colaboração B2B do Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [API e personalização da colaboração B2B do Azure Active Directory](active-directory-b2b-api.md)
* [Autenticação multifator para usuários de colaboração B2B](active-directory-b2b-mfa-instructions.md)
* [Adicionar usuários de colaboração B2B sem um convite](active-directory-b2b-add-user-without-invite.md)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)

