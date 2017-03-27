---
title: "O que é a visualização da colaboração B2B do Azure Active Directory? | Microsoft Docs"
description: "A colaboração B2B do Azure Active Directory dá suporte a relações entre empresas, permitindo que os parceiros de negócios acessem de maneira seletiva seus aplicativos corporativos."
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 1464387b-ee8b-4c7c-94b3-2c5567224c27
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/18/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 1f6ce516629fd32b5efabce8682b45a3e575eaab
ms.lasthandoff: 03/10/2017


---

# <a name="about-the-azure-ad-b2b-collaboration-preview"></a>O que é a visualização da colaboração B2B do Azure AD?
Este artigo discute os objetivos, recursos e benefícios da atualização da visualização pública de colaboração B2B do Azure Active Directory (Azure AD).

Se você for um profissional de TI ou um operador de informações, você pode tirar proveito dos muitos recursos de colaboração B2B do Azure AD. Você pode trabalhar em conjunto com parceiros de outras organizações em diversos lugares, independentemente de seu tamanho, indústria, requisitos do setor e conformidade. E você pode fornecer a eles acesso a documentos, recursos e aplicativos enquanto mantêm controle total sobre os dados internos.

Se você é um desenvolvedor, você pode usar as APIs B2B do Azure AD para escrever aplicativos que reúnem as organizações com segurança. Para usuários profissionais de informações, o processo e a navegação são simples.

## <a name="how-b2b-collaboration-works"></a>Como funciona a colaboração B2B?

Na versão de visualização atual, os profissionais de TI e profissionais de informações estabelecem relações com organizações parceiras, adicionando seus usuários um ou alguns de cada vez através do portal do Azure ou da API do gerenciador de convites.

Os administradores podem usar as novas experiências no portal do Azure (https://portal.azure.com) e no PowerShell para relações.

Profissionais de informações podem usar as experiências do painel de acesso em http://myapps.microsoft.com.

Os desenvolvedores podem criar aplicativos usando a API do gerenciador de convites B2B do Azure AD para adicionar usuários de colaboração B2B e personalizar os fluxos de trabalho de convite e integração.

Usuários de colaboração B2B são normalmente adicionados por meio de um convite e o processo de resgate. Veja como ele funciona:

1. João Silva da WoodGrove deseja adicionar Sam Oogle como um usuário usando seu email do Gmail (gsamoogle@gmail.com).

2. João Silva vai até o portal da WoodGrove (portal.azure.com) ou painel de acesso (myapps.microsoft.com), entra e adiciona o usuário Sam Oogle ao aplicativo, grupo ou diretório da WoodGrove.

3. João Silva especifica um email de convite personalizado para enviar ao Sam Oogle.

4. Quando João Silva envia o convite, o usuário Sam Oogle é criado no Azure AD para o WoodGrove, conforme mostrado aqui:

  ![A interface de usuário do administrador no portal.azure.com](media/active-directory-b2b-what-is-azure-ad-b2b/user-is-added.png)

5. Depois que o usuário é criado, o Azure AD envia um email de convite para Sam Oogle:

  ![Email de convite para Sam Oogle](media/active-directory-b2b-what-is-azure-ad-b2b/invitation-mail-sent-to-sam.png)

6. No convite, Sam Oogle seleciona **Introdução**.  
O portal do Azure abre.

7. Sam Oogle entra no portal do Azure.

8. O Azure AD atualiza o objeto de usuário Sam Oogle no Azure AD com informações do seu token, como mostrado aqui:

  ![O perfil de usuário do Sam Oogle no portal do Azure](media/active-directory-b2b-what-is-azure-ad-b2b/user-profile-is-populated.png)

9. Agora que o usuário Sam Oogle resgatou o convite, ele pode acessar recursos do WoodGrove. E, como qualquer outro usuário no Azure AD, o usuário Sam Oogle pode ser gerenciado pelo administrador. A lista de usuários é mostrada aqui:

  ![A lista de usuários do Azure AD](media/active-directory-b2b-what-is-azure-ad-b2b/sam-now-user-in-azure-ad.png)

## <a name="public-preview-features"></a>Recursos da visualização pública
Com base nos comentários do usuário, a atualização da visualização pública da colaboração B2B fornece recursos importantes que incluem:

* Para administradores: obter aprimoramentos da interface de usuário no [portal do Azure](https://portal.azure.com). Por exemplo, os administradores podem convidar usuários de B2B para o diretório, qualquer grupo ou aplicativo.  

* Para os profissionais de informações: obtenha recursos de convite de autoatendimento de colaboração B2B no [painel de acesso](https://myapps.microsoft.com). Os profissionais de informações podem convidar usuários de colaboração B2B para qualquer grupo ou aplicativo de autoatendimento que eles gerenciam.

* Permitir que usuários convidados tenham qualquer endereço de email. Se ele for um endereço Microsoft Exchange local ou Office 365, um endereço outlook.com ou qualquer endereço social (Gmail, Yahoo! etc.), os usuários podem acessar a organização convidada com a criação de uma conta da Microsoft ou Azure AD.

* Crie um email de convite profissional com a marca do locatário.

* Personalize a orientação do usuário usando as APIs de convite.

* Autenticação multifator para usuários de colaboração B2B na organização que está convidando.

* Delegue convites para não administradores.

* Suporte do PowerShell para colaboração B2B.

* Forneça recursos de auditoria e relatório.

## <a name="next-steps"></a>Próximas etapas

Procure nossos outros artigos sobre a colaboração B2B do AD do Azure:

* [Como os administradores do Azure Active Directory adicionam usuários de colaboração B2B?](active-directory-b2b-admin-add-users.md)
* [Como os operadores de informação adicionam usuários de colaboração B2B?](active-directory-b2b-iw-add-users.md)
* [The elements of the B2B collaboration invitation email](active-directory-b2b-invitation-email.md) (Os elementos do email de convite para colaboração B2B)
* [Resgate de convite de colaboração B2B](active-directory-b2b-redemption-experience.md)
* [Licenciamento da colaboração B2B do Azure AD](active-directory-b2b-licensing.md)
* [Solução de problemas de colaboração B2B do Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Perguntas frequentes sobre a colaboração B2B do Azure Active Directory](active-directory-b2b-faq.md)
* [API e personalização da colaboração B2B do Azure Active Directory](active-directory-b2b-api.md)
* [Autenticação multifator para usuários de colaboração B2B](active-directory-b2b-mfa-instructions.md)
* [Adicionar usuários de colaboração B2B sem um convite](active-directory-b2b-add-user-without-invite.md)
* [Auditoria e relatórios de um usuário de colaboração B2B](active-directory-b2b-auditing-and-reporting.md)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)

