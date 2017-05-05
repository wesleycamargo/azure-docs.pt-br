---
title: "O que é a colaboração B2B do Azure Active Directory? | Microsoft Docs"
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
ms.date: 04/12/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: 9cd8edf47059a5f574489a604156890d157c8b1a
ms.lasthandoff: 04/13/2017


---

# <a name="what-is-azure-ad-b2b-collaboration"></a>O que é a colaboração B2B do Azure AD?

<iframe width="560" height="315" src="https://www.youtube.com/embed/AhwrweCBdsc" frameborder="0" allowfullscreen></iframe>

As capacidades de colaboração entre empresas (B2) do Azure AD permitem que qualquer organização usando o Azure AD trabalhe de forma segura com usuários de qualquer outra organização – de pequeno ou grande porte, com Azure AD ou sem; de fato, com uma organização de TI ou sem. 

As organizações que usam o Azure AD podem fornecer acesso a documentos, recursos e aplicativos aos seus parceiros, mantendo o controle completo sobre os dados empresariais. Os desenvolvedores podem usar as APIs entre empresas do Azure AD para gravar aplicativos que reúnem duas organizações de uma maneira segura, que é também intuitiva e contínua, para que os usuários finais possam navegar.

Uma elevada taxa de **97%** dos nossos clientes declarou que a colaboração B2B do Azure AD é muito importante para eles.

![gráfico de pizza](media/active-directory-b2b-what-is-azure-ad-b2b/97-percent-support.png)

No início de abril de 2017, tínhamos cerca de 3 milhões de usuários que já usavam as capacidades de colaboração B2B do Azure AD. E mais de 23% das organizações do Azure AD que têm mais de 10 usuários já estão se beneficiando desses recursos.

## <a name="the-key-benefits-of-azure-ad-b2b-collaboration-to-your-organization"></a>Os principais benefícios da colaboração B2B do Azure AD para sua organização

### <a name="work-with-any-user-from-any-partner"></a>Trabalhar com qualquer usuário de qualquer parceiro

* Os parceiros usam suas próprias credenciais

* Não há requisito para que os parceiros usem o Azure AD

* Diretórios externos nem configuração complexa são necessários

### <a name="simple-and-secure-collaboration"></a>Colaboração simples e segura

* Fornece acesso a quaisquer dados ou aplicativos corporativos, aplicando sofisticadas políticas de autorização com base no AD Azure

* Experiências de usuário integrada

* Segurança de altíssimo nível para aplicativos e dados

### <a name="no-management-overhead"></a>Sem sobrecarga de gerenciamento

* Sem conta externa ou gerenciamento de senhas

* Sem gerenciamento de ciclo de vida de conta manual ou sincronizada

* Sem sobrecarga administrativa externa

## <a name="you-can-easily-add-b2b-collaboration-users-to-your-organization"></a>É possível adicionar usuários de colaboração B2B facilmente à sua organização

Administradores podem adicionar usuários de colaboração B2B (convidado) no [Portal do Azure](https://portal.azure.com).

![gráfico de pizza](media/active-directory-b2b-what-is-azure-ad-b2b/adding-b2b-users-admin.png)

### <a name="enable-your-collaborators-to-bring-their-own-identity"></a>Habilita que seus colaboradores tragam sua própria identidade

Os colaboradores B2B podem entrar com uma identidade de sua escolha. Se o usuário não tiver uma conta Microsoft e/ou uma conta Azure AD – uma delas será facilmente criada para o usuário no momento do resgate da oferta.

![Escolha de identidade de logon](media/active-directory-b2b-what-is-azure-ad-b2b/sign-in-identity-choice.png)

### <a name="delegate-to-application-and-group-owners"></a>Delegar aos proprietários de aplicativos e grupos 
Os proprietários de aplicativos e grupos podem adicionar usuários B2B diretamente em qualquer aplicativo de sua importância, seja ele um aplicativo da Microsoft ou não. Os administradores podem delegar permissão para adicionar usuários B2B a não-administradores. Não-administradores podem usar o [Painel de acesso do aplicativo do Azure AD](https://myapps.microsoft.com) para adicionar usuários de colaboração B2B a aplicativos ou grupos.

![painel de acesso](media/active-directory-b2b-what-is-azure-ad-b2b/access-panel.png)

![adicionar membro](media/active-directory-b2b-what-is-azure-ad-b2b/add-member.png)

### <a name="authorization-policies-protect-your-corporate-content"></a>As políticas de autorização protegem o conteúdo corporativo

Os administradores podem estabelecer políticas de acesso condicional, como a Autenticação Multifator, que podem ser impostas no nível do locatário, nível de aplicativo ou a usuários específicos para proteger aplicativos e dados corporativos.

![adicionar membro](media/active-directory-b2b-what-is-azure-ad-b2b/add-member.png)

### <a name="use-our-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>Use nossas APIs e código de exemplo para facilmente criar aplicativos para integração
Integre seus parceiros externos de modo personalizado às necessidades de sua organização.

Usando as [APIs de convite B2B do Azure AD](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation), as organizações podem personalizar suas experiências de integração, incluindo a criação de portais de inscrição de autoatendimento, código de exemplo para o qual fornecemos no Github: <https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web>

![portal de inscrição](media/active-directory-b2b-what-is-azure-ad-b2b/sign-up-portal.png)

Com a colaboração B2B do Azure AD é possível obter toda a capacidade do Azure AD para proteger seus relacionamentos com parceiros e para que os usuários finais localizem de maneira fácil e intuitiva. Então siga em frente, una-se às milhares de organizações que já estão usando B2B do Azure AD para sua colaboração externa!

## <a name="next-steps"></a>Próximas etapas

* Experiências de administrador são encontradas no [Portal do Azure](https://portal.azure.com).

* Experiências de operadores de informações estão disponíveis no [Painel de Acesso](https://myapps.microsoft.com).

* E, como sempre, conecte-se com a equipe do produto para quaisquer comentários, discussões e sugestões através da nossa [Comunidade Técnica Microsoft](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).

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

