---
title: O que é a colaboração B2B do Azure Active Directory? | Microsoft Docs
description: A colaboração B2B do Azure Active Directory dá suporte a relações entre empresas, permitindo que os parceiros de negócios acessem de maneira seletiva seus aplicativos corporativos.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/26/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 2bc405e6356113e0423f833868c86890c0c3d5d2
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259455"
---
# <a name="what-is-azure-ad-b2b-collaboration"></a>O que é a colaboração B2B do Azure AD?

Os recursos de colaboração B2B (entre empresas) do Azure AD (Azure Active Directory) permitem que qualquer organização que use o Microsoft Azure AD trabalhe com segurança com usuários de qualquer outra organização, seja ela grande ou pequena. As organizações que podem ser com ou sem o Microsoft Azure AD e não precisam nem ter um departamento de TI.

As organizações que usam o Azure AD podem fornecer acesso a documentos, recursos e aplicativos a seus parceiros, mantendo o controle completo sobre seus próprios dados corporativos. Os desenvolvedores podem usar as APIs entre empresas do Azure AD para programar aplicativos que reúnem duas organizações de forma mais segura. Além disso, a navegação é fácil para os usuários finais.

O vídeo a seguir fornece uma visão geral útil.
>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

## <a name="key-benefits-of-azure-ad-b2b-collaboration"></a>Principais benefícios da colaboração B2B do Azure AD

### <a name="work-with-any-user-from-any-partner"></a>Trabalhar com qualquer usuário de qualquer parceiro

- Os parceiros usam suas próprias credenciais
- Não há requisito para que os parceiros usem o Azure AD
- Diretórios externos nem configuração complexa são necessários

### <a name="simple-and-secure-collaboration"></a>Colaboração simples e segura

- Fornece acesso a quaisquer dados ou aplicativos corporativos, aplicando sofisticadas políticas de autorização com base no AD Azure
- Fácil para os usuários
- Segurança de altíssimo nível para aplicativos e dados

### <a name="no-management-overhead"></a>Sem sobrecarga de gerenciamento

- Sem conta externa ou gerenciamento de senhas
- Sem gerenciamento de ciclo de vida de conta manual ou sincronizada
- Sem sobrecarga administrativa externa

## <a name="easily-add-b2b-collaboration-users"></a>Adicionar facilmente os usuários de colaboração B2B

Como administrador, você pode adicionar facilmente usuários de colaboração (convidados) B2B para sua organização no [Portal do Azure](https://portal.azure.com).

![adicionar usuários convidados](media/what-is-b2b/adding-b2b-users-admin.png)

### <a name="enable-your-collaborators-to-bring-their-own-identity"></a>Habilita que seus colaboradores tragam sua própria identidade

Os colaboradores B2B podem entrar com uma identidade de sua escolha. Se o usuário não tiver uma conta da Microsoft ou uma conta do Azure AD, uma dela será criada facilmente para o usuário no momento do resgate da oferta.

### <a name="delegate-to-application-and-group-owners"></a>Delegar aos proprietários de aplicativos e grupos

Como um proprietário de aplicativos e grupos, você pode adicionar usuários B2B diretamente em qualquer aplicativo de sua importância, seja ele um aplicativo da Microsoft ou não. Os administradores podem delegar permissão para adicionar usuários B2B a não administradores. Não administradores podem usar o [Painel de acesso do aplicativo do Azure AD](https://myapps.microsoft.com) para adicionar usuários de colaboração B2B a aplicativos ou grupos.

![painel de acesso](media/what-is-b2b/access-panel.png)

![adicionar membro](media/what-is-b2b/add-member.png)

### <a name="authorization-policies-protect-your-corporate-content"></a>As políticas de autorização protegem o conteúdo corporativo

Políticas de acesso condicional, como autenticação multifator, podem ser aplicadas:
- No nível do locatário
- No nível do aplicativo
- Para que usuários específicos protejam dados e aplicativos corporativos

### <a name="use-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>Use as APIs e código de exemplo para facilmente criar aplicativos para integração

Integre seus parceiros externos de modo personalizado às necessidades de sua organização.

Use as [APIs de convite de colaboração B2B](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation), para personalizar suas experiências de integração, incluindo a criação de portais de inscrição de autoatendimento. Nós fornecemos o código de exemplo para o portal de autoatendimento [no Github](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

![portal de inscrição](media/what-is-b2b/sign-up-portal.png)

Com a colaboração B2B do Azure AD é possível obter toda a capacidade do Azure AD para proteger seus relacionamentos com parceiros e para que os usuários finais localizem de maneira fácil e intuitiva.

## <a name="next-steps"></a>Próximas etapas

- [Como os administradores do Azure Active Directory adicionam usuários de colaboração B2B?](add-users-administrator.md)
- [Como os operadores de informação adicionam usuários de colaboração B2B?](add-users-information-worker.md)
- [Resgate de convite de colaboração B2B](redemption-experience.md)
- [Licenciamento da colaboração B2B do Azure AD](licensing-guidance.md)
- E, como sempre, conecte-se com a equipe do produto para enviar comentários, sugestões e participar de discussões por meio da [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b) (Comunidade Técnica da Microsoft).