---
title: Listando seu aplicativo na galeria de aplicativos do Azure Active Directory
description: "Como listar um aplicativo que oferece suporte a logon único na galeria do Active Directory do Azure | Microsoft Azure"
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/09/2018
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: feb09aa8f8e22ad6fbda6a490d251c500bedf3ee
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2018
---
# <a name="listing-your-application-in-the-azure-active-directory-application-gallery"></a>Listando seu aplicativo na galeria de aplicativos do Azure Active Directory


##  <a name="what-is-azure-ad-app-gallery"></a>O que é a galeria de aplicativos Azure AD?

O Azure AD é um serviço de Identidade baseado em nuvem. A [galeria de aplicativos Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) é um repositório comum em que todos os conectores de aplicativos são publicados para logon único e provisionamento de usuário. Nossos clientes mútuos que estão usando o Azure AD como Provedor de identidade procuram conectores de aplicativos SaaS diferentes, que são publicados aqui. O administrador de TI adiciona o conector por meio da galeria de aplicativos e configura-o e usa-o para o Logon único e provisionamento. O Azure AD dá suporte a todos os principais protocolos de federação, como SAML 2.0, OpenID Connect, OAuth e WS-Fed, para logon único. 

## <a name="what-are-the-benefits-of-listing-the-application-in-the-gallery"></a>Quais são os benefícios da listagem do aplicativo na galeria?

*  Fornece a melhor experiência de logon único possível para os clientes.

*  Configuração simples e mínima do aplicativo.

*  Os clientes podem pesquisar o aplicativo e encontrá-lo na galeria. 

*  Qualquer cliente pode usar essa integração, seja qual for seu SKU do Azure AD: Gratuito, Básico ou Premium.

*  Tutorial de configuração passo a passo para os clientes mútuos.

*  Habilite o provisionamento de usuário para o mesmo aplicativo, caso esteja usando o SCIM.


##  <a name="what-are-the-pre-requisites"></a>Quais são os pré-requisitos?

Para listar um aplicativo na galeria do Azure AD, o aplicativo precisa antes implementar um dos protocolos de federação compatíveis com o Azure AD. Leia os termos e as condições da galeria de aplicativos do Azure AD aqui. Se estiver usando: 

*   **OpenID Connect** – crie o aplicativo multilocatário no Azure AD e implemente a [estrutura de consentimento do Azure AD](active-directory-integrating-applications.md#overview-of-the-consent-framework) em seu aplicativo. Envie a solicitação de logon para o ponto de extremidade comum, de modo que qualquer cliente possa dar o consentimento para o aplicativo. Controle o acesso do usuário cliente com base na ID de locatário e no nome UPN do usuário recebido no token. Para integrar seu aplicativo ao Azure AD, siga as [instruções do desenvolvedor](active-directory-authentication-scenarios.md).

*   **SAML 2.0 ou WS-Fed** – seu aplicativo deve ter a capacidade de fazer a integração de SSO do SAML/WS-Fed no modo SP ou IDP. Qualquer aplicativo compatível com o SAML 2.0 pode ser integrado diretamente a um locatário do Azure AD usando as [instruções para adicionar um aplicativo personalizado](../active-directory-saas-custom-apps.md).

*   **SSO de senha** – crie um aplicativo Web que tem uma página de entrada HTML para configurar o [logon único baseado em senha](../active-directory-appssoaccess-whatis.md). O SSO baseado em senha, também conhecido como armazenamento de senha em cofre, permite que você gerencie o acesso de usuários e senhas para aplicativos Web que não dão suporte a federação de identidades. Também é útil para cenários onde vários usuários precisam compartilhar uma única conta, como contas de aplicativo de mídia social da sua organização. 

## <a name="process-for-submitting-the-request-in-the-portal"></a>Processo para enviar a solicitação no portal

Depois de testar se a integração de aplicativos funciona com o Azure AD, você precisa enviar sua solicitação de acesso em nosso [Portal de Rede de Aplicativos](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Se você tem uma conta do Office 365, use-a para fazer logon nesse portal ou use sua ID da Microsoft (Live ID, Outlook, Hotmail, etc.) para fazer logon. Você verá a página a seguir para solicitar o acesso. Forneça uma justificativa de negócios na caixa de texto e clique em **Solicitar Acesso**. Nossa equipe examinará todos os detalhes e fornecerá a você o acesso de acordo. Depois disso, faça logon no portal e envie sua solicitação detalhada para o aplicativo.

Em caso de problemas com relação ao acesso, contate a [Equipe de Integração de SSO do Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

![Solicitação de acesso no portal do SharePoint](./media/active-directory-app-gallery-listing/accessrequest.png)

## <a name="timelines"></a>Linhas do tempo
    
*   Processo de listagem do aplicativo SAML 2.0 ou WS-Fed na galeria – **7 a 10 dias úteis**

   ![TimeLine de listagem de aplicativos SAML na galeria](./media/active-directory-app-gallery-listing/timeline.png)

*   Processo de listagem do aplicativo OpenID Connect na galeria – **2 a 5 dias úteis**

   ![TimeLine de listagem de aplicativos SAML na galeria](./media/active-directory-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Escalonamentos

Para escalonamentos, envie um email para a [Equipe de Integração de SSO do Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) e responderemos o mais rápido possível.

