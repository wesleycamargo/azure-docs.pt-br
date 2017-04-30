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
ms.date: 04/13/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: c06e8b98fea6036704b07b0f12397fc4bb59cb3f
ms.lasthandoff: 04/15/2017


---

# <a name="azure-active-directory-b2b-collaboration-frequently-asked-questions-faq"></a>Perguntas frequentes sobre a colaboração B2B do Azure Active Directory

As perguntas frequentes são atualizadas periodicamente para refletir novos interesses.

### <a name="is-this-functionality-available-in-the-azure-classic-portal"></a>Essa funcionalidade está disponível no Portal Clássico do Azure?
Os novos recursos na colaboração B2B do Azure AD estão disponíveis por meio do [Portal do Azure](https://portal.azure.com) e no novo Painel de Acesso. 

### <a name="is-it-possible-to-customize-our-sign-in-page-so-its-more-intuitive-for-your-b2b-collaboration-guest-users"></a>É possível personalizar nossa página de entrada para que seja mais intuitiva a seus usuários convidados de colaboração B2B?
Claro que não! Há uma [postagem no blog explicando o recurso](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/) e está documentado em [Adicionar identidade visual da empresa às páginas de entrada e Painel de Acesso](active-directory-add-company-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Os usuários de colaboração B2B podem acessar o SharePoint Online e o OneDrive?
Sim. Entretanto, a capacidade de pesquisar por usuários convidados existentes no seletor de pessoas do SharePoint Online está desativada por padrão para coincidir com o comportamento herdado. Isso pode ser habilitado usando a configuração 'ShowPeoplePickerSuggestionsForGuestUsers' na coleta do site e do locatário. Isso pode ser definido usando os cmdlets Set-SPOTenant e SPOSite, que permite que os membros pesquisem todos os usuários convidados existentes no diretório. Alterações no escopo de locatário não afetam sites do SharePoint Online já provisionados.

### <a name="is-the-csv-upload-mechanism-still-supported"></a>O mecanismo de carregamento CSV ainda tem suporte?
Sim. Consulte o [exemplo de PowerShell sample](active-directory-b2b-code-samples.md) que incluímos.

### <a name="how-can-i-customize-my-invitation-emails"></a>Como posso personalizar meus emails de convite?
É possível personalizar quase tudo no processo de convite, utilizando as [APIs de convite B2B](active-directory-b2b-api.md).

### <a name="can-the-invited-external-user-leave-the-organization-to-which-he-was-invited"></a>O usuário externo convidado pode deixar a organização para a qual ele foi convidado?
Isso não está disponível no momento.

### <a name="now-that-multi-factor-authentication-mfa-is-available-for-guest-users-can-they-also-reset-their-mfa-method"></a>Agora que a MFA (autenticação multifator) está disponível para usuários convidados, eles também podem redefinir o método MFA?
Sim, da mesma forma que os usuários normais podem.

### <a name="which-organization-is-responsible-for-mfa-licenses"></a>Qual organização é responsável pelas licenças de MFA?
A organização que convida é a responsável pela MFA. Assim, a organização que convida tem que se certificar de que existam licenças suficientes para seus usuários de B2B que executam a MFA.

### <a name="what-if-my-partner-org-already-has-mfa-set-up-can-we-trust-their-mfa-and-not-use-our-mfa"></a>E se a MFA de minha org. parceira já estiver configurada? Podemos confiar na MFA dela e não usar a nossa MFA?
Ofereceremos suporte para isso em versões futuras. Quando isso acontecer, você poderá selecionar parceiros específicos para excluir da MFA (da organização que convida).

### <a name="how-can-i-achieve-delayed-invitations"></a>Como posso lidar com convites atrasados?
Algumas organizações querem adicionar usuários de colaboração B2B, provisioná-los para aplicativos que exigem o provisionamento e, em seguida, enviar os convites. Se esse for o seu caso, use a API de convite de colaboração B2B para personalizar o fluxo de integração.

### <a name="can-i-make-my-guest-users-limited-admins"></a>Posso tornar meus usuários convidados administradores limitados?
Com certeza. Se essa for a necessidade de sua organização, saiba como em [Como adicionar usuários convidados a uma função](active-directory-users-assign-role-azure-portal.md).

### <a name="does-azure-ad-b2b-collaboration-support-permitting-b2b-users-to-access-the-azure-portal"></a>A colaboração B2B do Azure AD oferece suporte à permissão de acesso de usuários B2B ao Portal do Azure?
Os usuários de colaboração B2B não devem precisar de acesso ao Portal do Azure, a menos que eles recebam uma função de administrador limitado ou de administrador global. Nesse caso, eles podem acessar o portal. Se um usuário convidado que não esteja nessas funções acessar o portal, ele/ela poderá acessar determinadas partes da experiência, pois a função de usuário Convidado tem determinadas permissões no diretório, conforme descrito nas seções anteriores.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Posso bloquear o acesso ao Portal do Azure para usuários convidados?
Sim! Mas tenha cuidado ao configurar essa política para evitar o bloqueio acidental do acesso a membros e administradores.
Você pode bloquear o acesso ao [Portal do Azure](https://portal.azure.com) por usuários convidados por meio da política de acesso condicional na API de Gerenciamento de Serviços do Windows Azure, executando as três etapas a seguir.
1. Modifique o grupo **Todos os Usuários**para conter apenas Membros ![modificar a captura de tela de grupo](media/active-directory-b2b-faq/modify-all-users-group.png)
2. Crie um grupo dinâmico que contém usuários Convidados ![criar captura de tela de grupo](media/active-directory-b2b-faq/group-with-guest-users.png)
3. Configure uma política de acesso condicional para impedir que os usuários convidados acessem o portal, conforme mostrado no vídeo a seguir.
  
  >[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player] 

### <a name="what-is-the-timeline-by-which-azure-ad-b2b-collaboration-will-start-support-for-mfa-and-consumer-email-accounts"></a>Qual é o cronograma para início do suporte para MFA e para contas de email do consumidor na colaboração B2B do Azure AD?
As contas de email do consumidor e MFA agora têm suporte.

### <a name="is-there-a-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Há algum plano para oferecer suporte à redefinição de senha para usuários de colaboração B2B do Azure AD?
Sim. Aqui estão os detalhes sobre a redefinição de senha por autoatendimento (SSPR) para um usuário B2B que é convidado a uma locação de recursos a partir de sua locação de identidade:
 
* A SSPR ocorrerá apenas na locação de identidade do usuário B2B
* Se a locação de identidade for uma conta da Microsoft – utiliza o mecanismo SSPR da conta da Microsoft
* Se a locação de identidade for uma locação just-in-time/viral, um email de redefinição de senha será enviado
* Para os demais, o processo SSPR padrão será aplicado para usuários B2B, semelhante aos membros SSPR para usuários B2B no contexto da locação de recursos será bloqueado.

### <a name="is-it-also-enabled-for-users-in-a-viral-tenant"></a>Isso também está habilitado para usuários em um locatário viral?
Não atualmente.

### <a name="does-microsoft-crm-provide-online-support-to-azure-ad-b2b-collaboration"></a>O Microsoft CRM fornece suporte online para colaboração B2B do Azure AD?
Trabalho para fornecer esse suporte está em andamento.

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Qual é o tempo de vida de uma senha inicial para um usuário de colaboração B2B recém-criado?
O Azure AD tem um conjunto fixo de requisitos de caracteres, de força da senha e bloqueio de conta que é aplicado igualmente a todas as contas de usuário de nuvem do Azure AD. Contas de usuário de nuvem são as contas que não são federadas com outro provedor de identidade, como a conta da Microsoft, Facebook, ADFS ou até mesmo de outro locatário de nuvem (no caso da colaboração B2B). Para contas federadas, a política de senha depende da política no locatário local nas e configurações de conta do usuário da Microsoft.

### <a name="applications-want-to-differentiate-their-experience-between-a-tenant-user-and-a-guest-user-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-right-model-for-this"></a>Os aplicativos desejam diferenciar a experiência entre um usuário de locatário e um usuário convidado. Existem diretrizes padrão para isso? A presença da declaração do provedor de identidade é o modelo certo para isso?
 
Um usuário convidado pode usar qualquer provedor de identidade para autenticar conforme abordamos nas [Propriedades de um usuário de colaboração B2B](active-directory-b2b-user-properties.md). Portanto, o UserType é a propriedade certa para determinar isso. A declaração UserType atualmente não está incluída no token. Aplicativos devem usar a API gráfica para consultar o diretório para o usuário e obter seu UserType.

### <a name="where-can-find-a-b2b-collaboration-community-to-share-solutions-and-submit-ideas"></a>Onde posso encontrar uma comunidade de colaboração B2B para compartilhar soluções e enviar ideias?

Estamos constantemente ouvindo seus comentários sobre as maneiras de melhorar a colaboração B2B. Convidamos você para participar da discussão, compartilhar cenários de usuário, melhores práticas e o que você gosta da colaboração B2B do Azure AD na [Comunidade de Tecnologia da Microsoft](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b)
 
Também convidamos você para enviar ideias e votar em recursos futuros no site [Ideias de colaboração B2B](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="is-there-a-way-to-invite-the-user-such-that-the-invitation-is-automatically-redeemed-and-the-user-is-just-ready-to-go-or-will-the-user-always-have-to-click-through-to-the-redemption-url"></a>Existe uma maneira de convidar o usuário para que o convite seja automaticamente resgatado e o usuário esteja “pronto para começar” ou o usuário sempre terá que clicar na URL de resgate?
Os convites enviados por um usuário da organização emissora do convite, que também é membro da organização convidada (a organização do usuário B2B), não requerem resgate do usuário B2B.

É recomendável que você consiga isso, convidando um usuário da organização convidada para a organização emissora do convite. [Adicione esse usuário à função Emissor do convite para convidado na organização do recurso](active-directory-b2b-add-guest-to-role.md). Esse usuário pode convidar outros usuários na organização convidada por meio da UI de entrada, scripts do PowerShell ou de APIs sem a necessidade do usuário B2B dessa organização resgatar seu convite.

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Como funciona a colaboração B2B quando o parceiro convidado estiver utilizando federação para adicionar sua própria autenticação local?
Se o parceiro tiver um locatário Azure AD que esteja federado à infraestrutura de autenticação local, o logon único (SSO) no local será automaticamente alcançado. Se o parceiro não tiver um locatário Azure AD, uma conta Azure AD é criada para o usuário entrar. 

### <a name="i-didnt-think-azure-ad-b2b-would-accept-gmailcom-and-outlookcom-email-addresses-b2c-was-what-you-used-for-those"></a>Eu não acho que B2B do Azure AD aceitaria endereços de email outlook.com e gmail.com. B2C foi a utilizada.
Estamos removendo as diferenças entre B2B e B2C sobre quais identidades têm suporte. A identidade utilizada não é um bom motivo para decidir entre usar B2B ou B2C. Consulte este artigo para ajudá-lo a decidir qual delas utilizar: [Comparar colaboração B2B e B2C no Azure Active Directory](active-directory-b2b-compare-b2c.md).

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Quais aplicativos e serviços fornecem suporte aos usuários convidados B2B do Azure?
Todos os aplicativos integrados ao Azure AD. 

### <a name="is-it-possible-to-force-mfa-for-b2b-guest-users-if-partners-have-no-mfa-enabled"></a>É possível forçar MFA para usuários convidados B2B se os parceiros não tiverem MFA habilitado?
Sim. Detalhes no [Acesso condicional para usuários de colaboração B2B](active-directory-b2b-mfa-instructions.md).

### <a name="within-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-any-plans-to-extend-this-to-azure-or-across-all-of-office-365"></a>No SharePoint é possível definir uma lista "permitir" ou "negar" para usuários externos. Há planos para estender isso ao Azure ou a todo o Office 365?
Sim. A colaboração B2B do Azure AD oferece suporte ao recurso allowlist/denylist. 

### <a name="what-licenses-are-needed-for-azure-ad-b2b"></a>Quais são as licenças necessárias para B2B do Azure AD?
Consulte as [Diretrizes de licenciamento de colaboração B2B do Azure Active Directory](active-directory-b2b-licensing.md).

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

