---
title: Resgate do convite em colaboração B2B - Azure Active Directory | Microsoft Docs
description: Descreve a experiência de resgate de convite de colaboração B2B do Azure AD para usuários finais, incluindo o contrato de termos de privacidade.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 2e354bc4ae06e86afd5d14e87ef796fce942521b
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2018
ms.locfileid: "34074774"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Resgate do convite de colaboração do Azure Active Directory B2B

Para colaborar com usuários de organizações parceiras por meio de colaboração B2B do Azure Active Directory (Azure AD), você pode convidar usuários para acessar aplicativos compartilhados. Depois que um usuário convidado é adicionado ao diretório por meio da interface de usuário ou o usuário é convidado por meio do PowerShell, os usuários convidados devem passar por um processo de consentimento pela primeira vez, afirmando que concordam com os [termos de privacidade](#privacy-policy-agreement). Esse processo acontece de qualquer uma das seguintes maneiras:

- O emissor do convite envia um link direto para um aplicativo compartilhado. O convidado clica no link para entrar, aceita os termos de privacidade e acessa diretamente o recurso compartilhado. (O usuário convidado ainda recebe um email de convite com uma URL de resgate, mas diferente de alguns casos especiais, não é mais necessário para usar o email de convite.)  
- O usuário convidado recebe um email de convite e clica na URL de resgate. Como parte do primeiro logon, os usuários serão solicitados a aceitar os termos de privacidade.

## <a name="redemption-through-a-direct-link"></a>Resgate por meio de um link direto

Um emissor do convite pode convidar um usuário enviando um link direto para um aplicativo compartilhado. Para o usuário convidado, a experiência de resgate é simples: basta entrar no aplicativo que foi compartilhado com ele. Os usuários convidados podem clicar em um link para o aplicativo, analisar e aceitar os termos de privacidade e acessar diretamente o aplicativo. Na maioria dos casos, os usuários convidados não precisam mais clicar em uma URL de resgate em um email de convite.

Se você convidou usuários por meio da interface do usuário, ou optou por enviar o email de convite como parte da experiência de convite do PowerShell, o usuário convidado ainda recebe um email de convite. Este email é útil para seguintes casos especiais:

- O usuário não tem uma conta do Azure AD ou uma conta da Microsoft (MSA). Nesse caso, o usuário deve criar uma MSA antes de clicar no link ou usar a URL de resgate no email de convite. O processo de resgate solicita automaticamente ao usuário para criar uma MSA.
- Às vezes, o objeto de usuário convidado não pode ter um endereço de email devido a um conflito com um objeto de contato (por exemplo, um objeto de contato Outlook). Nesse caso, o usuário deve clicar na URL de resgate no email de convite.
- O usuário pode entrar com um alias do endereço de email que foi convidado. (Um alias é um endereço de email adicional associado a uma conta de email.) Nesse caso, o usuário deve clicar na URL de resgate no email de convite.

Se esses casos especiais forem importantes para sua organização, é recomendável que você convide usuários usando métodos que ainda enviam emails de convite. Além disso, se um usuário não estiver em um desses casos especiais, ele ainda poderá clicar na URL em um email de convite para obter acesso.

## <a name="redemption-through-the-invitation-email"></a>Resgate por meio de email de convite

Se o usuário for convidado por meio de um método que envia um email de convite, ele também podem resgatar um convite por email de convite. Um usuário convidado pode clicar na URL de resgate no email e, em seguida, ler e aceitar os termos de privacidade. O processo é descrito com mais detalhes aqui:

1.  Depois de ser convidado, o usuário recebe um convite por email enviado pelo **Microsoft Invitations**.
2.  O convidado seleciona a opção **Começar** no email.
3.  Se o convidado não tiver uma conta do Azure AD ou uma MSA, ele será solicitado a criar uma MSA.
4.  O convidado é redirecionado para a tela **Revisar permissões**, onde ele poderá analisar a declaração de privacidade da organização que convida e aceitar os termos.

## <a name="privacy-policy-agreement"></a>Contrato de política de privacidade

Depois que qualquer usuário convidado fizer logon para acessar os recursos em uma organização parceira pela primeira vez, surgirá a tela **Revisar permissões**. Nela, eles podem analisar a declaração de privacidade da organização que convida. Um usuário deve aceitar o uso de suas informações de acordo com as políticas de privacidade da organização que convida para continuar.

![Captura de tela mostrando as configurações do usuário no painel de acesso](media/active-directory-b2b-redemption-experience/ConsentScreen.png) 

Para obter informações sobre como você, administrador de locatários, pode vincular a declaração de privacidade da sua organização, consulte [Como adicionar informações de privacidade da sua organização no Azure Active Directory](https://aka.ms/adprivacystatement).

## <a name="next-steps"></a>Próximas etapas

- [O que é a colaboração B2B do AD do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Adicionar usuários de colaboração B2B do Azure Active Directory no Portal do Azure](active-directory-b2b-admin-add-users.md)
- [Como os operadores de informação adicionam usuários de colaboração B2B ao Azure Active Directory?](active-directory-b2b-iw-add-users.md)
- [Adicionar usuários de colaboração B2B do Azure Active Directory usando o PowerShell](active-directory-b2b-api.md#powershell)
- [Deixar uma organização como usuário convidado](active-directory-b2b-leave-the-organization.md)