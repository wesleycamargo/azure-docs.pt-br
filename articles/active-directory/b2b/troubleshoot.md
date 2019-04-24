---
title: Solução de problemas de colaboração B2B – Azure Active Directory | Microsoft Docs
description: Correções para problemas comuns com a colaboração B2B do Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: af106650f6e1d139ec7af2c8d243dc50f2e963fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60412381"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Solução de problemas de colaboração B2B do Azure Active Directory

Confira aqui algumas correções para problemas comuns da colaboração B2B do Azure Active Directory (Azure AD).


## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Adicionei um usuário externo, mas não consigo vê-lo em meu Catálogo de Endereços Global ou no seletor de pessoas

Para casos nos quais os usuários externos não são preenchidos na lista, o objeto pode demorar alguns minutos para replicar.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>Um usuário convidado de B2B não está aparecendo no seletor de pessoas do SharePoint Online/OneDrive 
 
A capacidade de pesquisar por usuários convidados existentes no seletor de pessoas do SharePoint Online (SPO) está desativado por padrão para coincidir com o comportamento herdado.

Esse recurso pode ser habilitado usando a configuração 'ShowPeoplePickerSuggestionsForGuestUsers' na coleta do site e do locatário. Defina esse recurso usando os cmdlets Set-SPOTenant e SPOSite, que permitem aos membros pesquisar todos os usuários convidados existentes no diretório. Alterações no escopo de locatário não afetam sites SPO já provisionados.

## <a name="invitations-have-been-disabled-for-directory"></a>Os convites foram desabilitados para o diretório

Se você receber uma notificação indicando que você não tem permissões para convidar usuários, verifique se a sua conta de usuário tem autorização para convidar usuários externos em Configurações do Usuário:

![Captura de tela mostrando as configurações de usuários externos](media/troubleshoot/external-user-settings.png)

Se você tiver modificado essas configurações recentemente ou se tiver atribuído a função Emissor de convite convidado a um usuário, talvez ocorra um atraso de 15 a 60 minutos até que as alterações tenham efeito.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>O usuário que eu convidei está recebendo um erro durante o resgate

Os erros comuns incluem:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>O Admin do convidado não permite a criação de Usuários Verificados por Email em seu locatário

Ao convidar usuários cuja organização está usando o Azure Active Directory onde a conta do usuário específico não existe (por exemplo, o usuário não existe no Azure AD contoso.com). O administrador de contoso.com pode ter uma política em vigor para impedir a criação de usuários. O usuário deve verificar com o administrador para determinar se os usuários externos são permitidos. A administração de usuário externo pode ter que permitir usuários verificados por email em seu domínio (consulte este [artigo](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) sobre como permitir Usuários de Email Verificados).

![Usuários verificados de erro informando que o locatário não permite que o email](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>O usuário externo ainda não existe em um domínio federado

Se você estiver usando a autenticação de Federação e o usuário ainda não existir no Azure Active Directory, o usuário não poderá ser convidado.

Para resolver esse problema, o administrador do usuário externo deve sincronizar a conta do usuário ao Azure Active Directory.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Como ‘\#’, que normalmente não é um caractere válido, é sincronizado com o Azure AD?

"\#" é um caractere reservado no UPNs para colaboração B2B do Azure AD ou usuários externos, pois a conta convidada user@contoso.com se torna user_contoso.com#EXT#@fabrikam.onmicrosoft.com. Portanto, não há permissão para \# em UPNs provenientes do local entrar no Portal do Azure. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Recebo um erro ao adicionar usuários externos a um grupo sincronizado

Os usuários externos podem ser adicionados apenas a grupos de "Segurança" ou "atribuído", e não a grupos que são masterizados localmente.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Meu usuário externo não recebeu um email para o resgate

O convidado deve verificar com seu ISP ou o filtro de spam para garantir que o seguinte endereço tem permissão: Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Observei que, às vezes, a mensagem personalizada não é incluída nas mensagens de convite

Para cumprir as leis de privacidade, nossas APIs não incluem mensagens personalizadas no convite por email quando:

- O emissor do convite não tem um endereço de email no locatário que está convidando
- Quando uma entidade do serviço de aplicativo envia o convite

Se esse cenário for importante para você, suprima nosso email de convite de API e envie-o por meio de um mecanismo de email de sua escolha. Consulte o departamento jurídico de sua organização para verificar se todo email enviado dessa forma também está em conformidade com as leis de privacidade.

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>Um usuário convidado com um locatário just-in-time ou "viral" não pode redefinir sua senha

Se o locatário de identidade for um locatário JIT (just-in-time) ou viral (ou seja, um locatário do Azure não gerenciado e separado), somente o usuário convidado poderá redefinir sua senha. Às vezes, uma organização [assumirá o gerenciamento de locatários virais](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover) que são criados quando os funcionários usam seus emails de trabalho para se inscrever em serviços. Depois que a organização assumir um locatário viral, somente um administrador da organização poderá redefinir a senha do usuário ou habilitar a SSPR. Se necessário, como a organização de convite, você pode remover a conta de usuário convidado do diretório e reenviar um convite.

## <a name="next-steps"></a>Próximas etapas

- [Obtenção de suporte para colaboração B2B](get-support.md)
