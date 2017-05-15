---
title: "Solução de problemas de colaboração B2B do Azure Active Directory | Microsoft Docs"
description: "Correções para problemas comuns com a colaboração B2B do Azure Active Directory"
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
ms.date: 04/12/2017
ms.author: sasubram
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 4ae08f16db8c0b8cd2e918d25aa546f1da615af1
ms.contentlocale: pt-br
ms.lasthandoff: 04/27/2017


---

# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Solução de problemas de colaboração B2B do Azure Active Directory

Confira aqui algumas correções para problemas comuns da colaboração B2B do Azure Active Directory (Azure AD).


## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Adicionei um usuário externo, mas não consigo vê-lo em meu Catálogo de Endereços Global ou no seletor de pessoas

Para casos nos quais os usuários externos não são preenchidos na lista, o objeto pode demorar alguns minutos para replicar.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>Um usuário convidado de B2B não está aparecendo no seletor de pessoas do SharePoint Online/OneDrive 
 
A capacidade de pesquisar por usuários convidados existentes no seletor de pessoas do SharePoint Online está desativado por padrão para coincidir com o comportamento herdado.
Isso pode ser habilitado usando a configuração 'ShowPeoplePickerSuggestionsForGuestUsers' na coleta do site e do locatário. Isso pode ser definido usando os cmdlets Set-SPOTenant e SPOSite, que permite que os membros pesquisem todos os usuários convidados existentes no diretório. Alterações no escopo de locatário não afetam sites SPO já provisionados.

## <a name="invitations-have-been-disabled-for-directory"></a>Os convites foram desabilitados para o diretório

Se você receber uma mensagem de erro indicando que você não tem permissões para convidar usuários, verifique se a sua conta de usuário tem autorização para convidar usuários externos. Isso pode ser feito nas Configurações do Usuário:

![](media/active-directory-b2b-troubleshooting/external-user-settings.png)

Se você tiver modificado essas configurações recentemente ou se tiver atribuído a função Emissor de convite convidado a um usuário, talvez ocorra um atraso de 15 a 60 minutos até que as alterações tenham efeito.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>O usuário que eu convidei está recebendo um erro durante o resgate

Os erros comuns incluem:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>O Admin do convidado não permite a criação de Usuários Verificados por Email em seu locatário

Ao convidar usuários cuja organização está usando o Azure Active Directory onde a conta do usuário específico não existe (por exemplo, o usuário não existe no Azure AD contoso.com). O administrador de contoso.com pode ter uma política em vigor para impedir a criação de usuários. O usuário deve verificar com o administrador para determinar se os usuários externos são permitidos. A administração de usuário externo pode ter que permitir usuários verificados por email em seu domínio (consulte este [artigo](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) sobre como permitir Usuários de Email Verificados).

![](media/active-directory-b2b-troubleshooting/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>O usuário externo ainda não existe em um domínio federado

Em casos nos quais o usuário externo está usando uma solução de federação em que a autenticação está sendo executada no local, e o usuário ainda não existe no Azure Active Directory, o usuário não poderá ser convidado.

Para resolver esse problema, o administrador do usuário externo deve sincronizar a conta do usuário ao Azure Active Directory.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Como ‘\#’, que normalmente não é um caractere válido, é sincronizado com o Azure AD?

“\#” é um caractere reservado em UPNs para colaboração B2B do Azure AD ou usuários externos (ou seja, o user@contoso.com convidado torna-se user_contoso.com#EXT@fabrikam.onmicrosoft.com); portanto, \# em UPNs obtidos localmente não tem permissão para entrar no portal do Azure.

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Recebo um erro ao adicionar usuários externos a um grupo sincronizado

Os usuários externos podem ser adicionados apenas a grupos de "Segurança" ou "atribuído", e não a grupos que são masterizados localmente.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Meu usuário externo não recebeu um email para o resgate

O convidado deve verificar com seu ISP ou o filtro de spam para garantir que o seguinte endereço tem permissão: Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Observei que, às vezes, a mensagem personalizada não é incluída nas mensagens de convite

A fim de estar em conformidade as leis de privacidade, nossas APIs não incluem mensagens personalizadas no convite por email quando o emissor do convite não tem um endereço de email na organização do recurso (também conhecida como o locatário emissor do convite) ou quando uma entidade de serviço de aplicativo envia o convite. Se esse for um cenário importante para você, você poderá suprimir nossa API de enviar o email de convite e enviá-lo por meio de um mecanismo de email de sua escolha. Lembre-se de consultar o departamento jurídico de sua organização para verificar se todo email enviado dessa forma também está em conformidade com as leis de privacidade.

## <a name="next-steps"></a>Próximas etapas

Procure nossos outros artigos sobre a colaboração B2B do AD do Azure:

* [O que é a colaboração B2B do AD do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Como os administradores do Azure Active Directory adicionam usuários de colaboração B2B?](active-directory-b2b-admin-add-users.md)
* [Como os operadores de informação adicionam usuários de colaboração B2B?](active-directory-b2b-iw-add-users.md)
* [The elements of the B2B collaboration invitation email](active-directory-b2b-invitation-email.md) (Os elementos do email de convite para colaboração B2B)
* [Resgate de convite de colaboração B2B](active-directory-b2b-redemption-experience.md)
* [Licenciamento da colaboração B2B do Azure AD](active-directory-b2b-licensing.md)
* [Perguntas frequentes sobre a colaboração B2B do Azure Active Directory](active-directory-b2b-faq.md)
* [API e personalização da colaboração B2B do Azure Active Directory](active-directory-b2b-api.md)
* [Autenticação multifator para usuários de colaboração B2B](active-directory-b2b-mfa-instructions.md)
* [Adicionar usuários de colaboração B2B sem um convite](active-directory-b2b-add-user-without-invite.md)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)

