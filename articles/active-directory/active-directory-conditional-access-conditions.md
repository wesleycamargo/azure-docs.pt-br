---
title: "Condições de acesso condicional do Azure Active Directory | Microsoft Docs"
description: "Saiba como as atribuições são usadas no acesso condicional do Azure Active Directory para disparar uma política."
services: active-directory
keywords: "acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos da empresa, políticas de acesso condicional"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/09/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 2415a2c2c0143b4abeb8ec1ecab379a204456874
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2018
---
# <a name="conditions-in-azure-active-directory-conditional-access"></a>Condições de acesso condicional do Azure Active Directory 

Com o [acesso condicional do Azure AD (Azure Active Directory)](active-directory-conditional-access-azure-portal.md), você pode controlar como os usuários autorizados acessam seus aplicativos de nuvem. Em uma política de acesso condicional, você define a resposta ("faça isso") para o motivo do acionamento de sua política ("quando isso acontecer"). 

![Controle](./media/active-directory-conditional-access-conditions/10.png)


No contexto de acesso condicional,

- "**Quando isso acontecer**" é chamado de **condição** e 
- "**Faça isso**" é chamado de **controle de acesso**.

A combinação de suas condições e seus controles de acesso representa uma política de acesso condicional.

![Controle](./media/active-directory-conditional-access-conditions/61.png)

Este artigo fornece uma visão geral das condições e de como elas são usadas em uma política de acesso condicional. 


## <a name="users-and-groups"></a>Usuários e grupos

A condição de usuários e grupos é obrigatória em uma política de acesso condicional. Em sua política, você pode selecionar **Todos os usuários** ou escolher usuários e grupos específicos.

![Controle](./media/active-directory-conditional-access-conditions/02.png)

Ao selecionar:

- **Todos os usuários**, sua política é aplicada a todos os usuários no diretório. Isso inclui usuários convidados.

- **Selecionar usuários e grupos**, você pode especificar conjuntos de usuários. Por exemplo, pode selecionar um grupo que contém todos os membros do departamento de RH quando tiver um aplicativo de RH selecionado como o aplicativo de nuvem. 

- Um grupo pode ser qualquer tipo de grupo no Microsoft Azure Active Directory, incluindo grupos de segurança e de distribuição dinâmicos ou atribuídos.

Você também pode excluir usuários ou grupos específicos de uma política. Um caso de uso comum é para contas de serviço, caso sua política imponha a autenticação multifator. 

Especificar determinados conjuntos de usuários é útil para a implantação de uma nova política. Em uma nova política, você deve ter como destino apenas um conjunto inicial de usuários para validar o comportamento da política. 



## <a name="cloud-apps"></a>Aplicativos na nuvem 

Um aplicativo de nuvem é um site da Web ou de serviço. Isso inclui sites protegidos pelo Proxy de Aplicativo do Azure. Para saber mais sobre os aplicativos de nuvem compatíveis, confira [Atribuição de aplicativos de nuvem](active-directory-conditional-access-technical-reference.md#cloud-apps-assignments).    

A condição de aplicativos de nuvem é obrigatória em uma política de acesso condicional. Em sua política, é possível selecionar **Todos os aplicativos em nuvem** ou escolher aplicativos específicos.

![Controle](./media/active-directory-conditional-access-conditions/03.png)

Você pode selecionar:

- **Todos os aplicativos em nuvem** para políticas de linha de base a serem aplicadas em toda a organização. Um caso de uso comum para essa seleção é uma política que exige autenticação multifator quando o risco de entrada for detectado para qualquer aplicativo de nuvem.

- Aplicativos de nuvem individuais para atingir serviços específicos pela política. Por exemplo, você pode exigir que os usuários tenham um [dispositivo compatível](active-directory-conditional-access-policy-connected-applications.md) para acessar o SharePoint Online. Essa política também se aplica a outros serviços quando eles acessarem o conteúdo do SharePoint, por exemplo, Microsoft Teams. 

Você também pode excluir aplicativos específicos de uma política; entretanto, esses aplicativos são ainda sujeito às políticas aplicadas ao acessarem os serviços. 



## <a name="sign-in-risk"></a>Risco de entrada

Risco de entrada é uma indicação (alta, média ou baixa) da probabilidade de uma tentativa de logon não ter sido executada pelo proprietário legítimo de uma conta de usuário. O Microsoft Azure Active Directory calcula o nível de risco de entrada durante o login de um usuário. Você pode usar o nível de risco de conexão calculado como uma condição em uma política de acesso condicional. 

![Condições](./media/active-directory-conditional-access-conditions/22.png)

Para usar essa condição, você precisa ter a [Azure Active Directory Identity Protection](active-directory-identityprotection.md) habilitada.
 
Casos de uso comuns para essa condição são políticas que:

- Bloqueiam usuários com um risco de entrada alto para impedir que usuários potencialmente ilegítimos acessem seus aplicativos de nuvem. 
- Exigem autenticação multifator para usuários com um risco de entrada médio. Ao aplicar a autenticação multifator, você pode fornecer confiança adicional de que a entrada é executada pelo proprietário legítimo de uma conta.

Para obter mais informações, consulte [Entradas de risco](active-directory-identityprotection.md#risky-sign-ins).  

## <a name="device-platforms"></a>Plataformas de dispositivo

A plataforma de dispositivo é caracterizada pelo sistema operacional que está em execução no seu dispositivo. O Microsoft Azure Active Directory identifica a plataforma usando as informações fornecidas pelo dispositivo, como o agente do usuário. Como essas informações são verificadas, recomenda-se que todas as plataformas tenham uma política aplicada a eles, bloqueando o acesso, exigindo a conformidade com as políticas do Intune ou exigindo que o dispositivo ingresse no domínio. O padrão é aplicar a política a todas as plataformas de dispositivo. 


![Condições](./media/active-directory-conditional-access-conditions/24.png)

Para obter uma lista completa das plataformas de dispositivo com suporte, consulte [Condição de plataforma de dispositivo](active-directory-conditional-access-technical-reference.md#device-platform-condition).


Um caso de uso comum para essa condição é uma política que restringe o acesso aos seus aplicativos de nuvem para [dispositivos confiáveis](active-directory-conditional-access-policy-connected-applications.md#trusted-devices). Para ver mais cenários, incluindo a condição de plataforma do dispositivo, confira [Acesso condicional com base em aplicativo do Azure Active Directory](active-directory-conditional-access-mam.md).


## <a name="locations"></a>Locais

Com localizações, você tem a opção de definir condições com base no local dedo qual uma tentativa de conexão foi iniciada. 
     
![Condições](./media/active-directory-conditional-access-conditions/25.png)

Casos de uso comuns para essa condição são políticas que:

- Exigir autenticação multifator para usuários que acessam um serviço quando estão fora da rede corporativa.  

- Bloquear o acesso para usuários que acessam um serviço de países ou regiões específicas. 

Para saber mais, confira [Condições de local no acesso condicional do Azure Active Directory](active-directory-conditional-access-locations.md).


## <a name="client-apps"></a>Aplicativos cliente

A condição de aplicativos de cliente permite que você aplique uma política a diferentes tipos de aplicativos, como:

- Sites e serviços da Web.
- Aplicativos móveis e de área de trabalho. 

![Condições](./media/active-directory-conditional-access-conditions/04.png)

Um aplicativo é classificado como:

- Um site ou serviço da Web, se usa os protocolos Web SSO, SAML, WS-Fed ou OpenID Connect para um cliente confidencial.

- Um aplicativo móvel ou de área de trabalho, se usa o aplicativo móvel OpenID Connect para um cliente nativo.

Para obter uma lista completa dos aplicativos de cliente que você pode usar em sua política de acesso condicional, consulte a [referência técnica de acesso condicional do Azure Active Directory](active-directory-conditional-access-technical-reference.md#client-apps-condition).

Casos de uso comuns para essa condição são políticas que:

- Exigir um [dispositivo compatível](active-directory-conditional-access-policy-connected-applications.md) para aplicativos móveis e de área de trabalho que fazem o download de grandes quantidades de dados para o dispositivo, permitindo o acesso pelo navegador usando qualquer dispositivo.

- Bloquear o acesso de aplicativos Web, mas permitir o acesso de aplicativos móveis e de área de trabalho.

Além de usar protocolos de autenticação modernos e SSO da Web, você pode aplicar essa condição para aplicativos de email que usam o Exchange ActiveSync, como os aplicativos de email nativos na maioria dos smartphones. No momento, aplicativos cliente que usam os protocolos herdados precisam ser protegidos usando o AD FS.

 Para obter mais informações, confira:

- [Configurar o SharePoint Online e o Exchange Online para acesso condicional do Azure Active Directory](active-directory-conditional-access-no-modern-authentication.md)
 
- [Acesso condicional baseado em aplicativo do Azure Active Directory](active-directory-conditional-access-mam.md) 








## <a name="next-steps"></a>Próximas etapas

- Se você quiser saber como configurar uma política de acesso condicional, veja [introdução ao acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, confira as [melhores práticas para o acesso condicional no Azure Active Directory](active-directory-conditional-access-best-practices.md). 

