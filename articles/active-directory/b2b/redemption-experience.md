---
title: Resgate do convite em colaboração B2B - Azure Active Directory | Microsoft Docs
description: Descreve a experiência de resgate de convite de colaboração B2B do Azure AD para usuários finais, incluindo o contrato de termos de privacidade.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: f580c78fa2114905a0ef28f51feee50361717a27
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53435320"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Resgate do convite de colaboração do Azure Active Directory B2B

Para colaborar com usuários de organizações parceiras por meio de colaboração B2B do Azure Active Directory (Azure AD), você pode convidar usuários para acessar aplicativos compartilhados. Depois que um usuário convidado é adicionado ao diretório por meio da interface de usuário ou o usuário é convidado por meio do PowerShell, os usuários convidados devem passar por um processo de consentimento pela primeira vez, afirmando que concordam com os [termos de privacidade](#privacy-policy-agreement). Esse processo acontece de qualquer uma das seguintes maneiras:

- O emissor do convite envia um link direto para um aplicativo compartilhado. O convidado clica no link para entrar, aceita os termos de privacidade e acessa diretamente o recurso compartilhado. (O usuário convidado ainda recebe um email de convite com uma URL de resgate, mas diferente de alguns casos especiais, não é mais necessário para usar o email de convite.)  
- O usuário convidado recebe um email de convite e clica na URL de resgate. Como parte do primeiro logon, os usuários serão solicitados a aceitar os termos de privacidade.

## <a name="redemption-through-a-direct-link"></a>Resgate por meio de um link direto

Um emissor do convite pode convidar um usuário convidado enviando um [link direto para um aplicativo compartilhado](../manage-apps/end-user-experiences.md#direct-sign-on-links). Para o usuário convidado, a experiência de resgate é simples: basta entrar no aplicativo que foi compartilhado com ele. Os usuários convidados podem clicar em um link para o aplicativo, analisar e aceitar os termos de privacidade e acessar diretamente o aplicativo. Na maioria dos casos, os usuários convidados não precisam mais clicar em uma URL de resgate em um email de convite.

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

![Captura de tela mostrando as configurações do usuário no Painel de Acesso](media/redemption-experience/ConsentScreen.png) 

Para obter informações sobre como você, como administrador de locatários, pode fornecer um link para a política de privacidade de sua organização, confira [Como: Adicionar informações de privacidade de sua organização ao Azure Active Directory](https://aka.ms/adprivacystatement).

## <a name="terms-of-use"></a>Termos de uso

Você pode apresentar termos de uso para o usuário convidado durante o processo de resgate inicial usando o recurso Termos de uso do Azure AD. No Azure Active Directory, acesse esse recurso em **Gerenciar** > **Relações organizacionais** > **Termos de uso** ou em **Segurança** > **Acesso Condicional** > **Termos de uso**. Para obter detalhes, confira [Recurso Termos de uso do Azure AD](../governance/active-directory-tou.md).

![Captura de tela mostrando os novos termos de uso](media/redemption-experience/organizational-relationships-terms-of-use.png) 

## <a name="next-steps"></a>Próximas etapas

- [O que é a colaboração B2B do AD do Azure?](what-is-b2b.md)
- [Adicionar usuários de colaboração B2B do Azure Active Directory no Portal do Azure](add-users-administrator.md)
- [Como os operadores de informação adicionam usuários de colaboração B2B ao Azure Active Directory?](add-users-information-worker.md)
- [Adicionar usuários de colaboração B2B do Azure Active Directory usando o PowerShell](customize-invitation-api.md#powershell)
- [Deixar uma organização como usuário convidado](leave-the-organization.md)