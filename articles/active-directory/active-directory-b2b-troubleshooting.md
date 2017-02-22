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
ms.date: 02/09/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: a4e59dfa8a098f63c3173176c4d2675d6a59af00
ms.openlocfilehash: f85c6bcc2abbd14c7879462f7013a97f550fdca5


---

# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Solução de problemas de colaboração B2B do Azure Active Directory

Confira aqui algumas correções para problemas comuns da colaboração B2B do Azure Active Directory (Azure AD).

## <a name="i-cant-create-an-external-user-due-to-an-existing-contact"></a>Não consigo criar um usuário externo devido a um contato existente

Se o usuário externo que você está convidando já tiver um objeto de contato pré-existente, não será possível convidar esse usuário até que você resolva o conflito, geralmente pela remoção do objeto de contato. Até que a colaboração B2B esteja disponível para o público geral, o conflito deve ser resolvido manualmente.

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Adicionei um usuário externo, mas não consigo vê-lo em meu Catálogo de Endereços Global ou no seletor de pessoas

Para casos nos quais os usuários externos não são preenchidos na lista, o objeto pode demorar alguns minutos para replicar.

## <a name="invitations-have-been-disabled-for-directory"></a>Os convites foram desabilitados para o diretório

Se você receber uma mensagem de erro indicando que você não tem permissões para convidar usuários, verifique se a sua conta de usuário tem autorização para convidar usuários externos. Isso pode ser feito nas Configurações do Usuário:

![](media/active-directory-b2b-troubleshooting/external-user-settings.png)

Se você tiver modificado essas configurações recentemente ou se tiver atribuído a função Emissor de convite convidado a um usuário, talvez ocorra um atraso de 15 a 60 minutos até que as alterações tenham efeito.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>O usuário que eu convidei está recebendo um erro durante o resgate

Os erros comuns incluem:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>O Admin do convidado não permite a criação de Usuários Verificados por Email em seu locatário:

Ao convidar usuários cuja organização está utilizando o Azure Active Directory onde a conta do usuário específico não existe (o Usuário não existe no AAD contoso.com). O administrador de contoso.com pode ter uma política em vigor para impedir a criação de usuários. O Usuário Externo deve consultar o administrador para determinar se há permissão para usuários externos. Talvez o administrador do usuário externo tenha que permitir usuários Verificados por Email em seu domínio (consulte este [artigo](https://docs.microsoft.com/en-us/powershell/msonline/v1/set-msolcompanysettings#parameters) sobre como permitir os usuários Verificados por Email).

![](media/active-directory-b2b-troubleshooting/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>O usuário externo ainda não existe em um domínio federado

Em casos nos quais o usuário externo está usando uma solução de federação em que a autenticação está sendo executada no local, e o usuário ainda não existe no Azure Active Directory, o usuário não poderá ser convidado.

Para resolver esse problema, o administrador do usuário externo deve sincronizar a conta do usuário ao Azure Active Directory.

## <a name="how-does--which-is-normally-an-invalid-character-sync-with-azure-ad"></a>Como ‘\#’, que normalmente é um caractere inválido, é sincronizado com o Azure AD?

"\#" é um caractere reservado em UPNs para colaboração B2B do Azure AD ou usuários externos (ou seja, &lt;user@contoso.com&gt; convidado, torna-se &lt;user_contoso.com#EXT@fabrikam.onmicrosoft.com&gt;) então \# em UPNs provenientes de locais não podem entrar no Portal do Azure.

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Recebo um erro ao adicionar usuários externos a um grupo sincronizado

Os usuários externos podem ser adicionados apenas a grupos de "Segurança" ou "atribuído", e não a grupos que são masterizados localmente.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Meu usuário externo não recebeu um email para o resgate

O convidado deve verificar com seu ISP ou o filtro de spam para garantir que o endereço a seguir tenha permissão:&lt;Invites@microsoft.com&gt;

## <a name="my-recipient-received-multiple-emails-from-me"></a>Meu destinatário recebeu vários emails meus

Em alguns casos, quando o destinatário do convite tiver vários aliases da conta, eles poderão receber dois convites. Nesses casos, o primeiro link resgatado é a conta que é criada, e o segundo link de resgate perde a validade.

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



<!--HONumber=Feb17_HO2-->


