---
title: Listar seu aplicativo na galeria de aplicativos do Azure Active Directory
description: "Como listar um aplicativo que oferece suporte a logon único na galeria de aplicativos do Azure Active Directory"
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
ms.openlocfilehash: 502fb555bd3b381c9be0ff04e210cc07f9bf6cd8
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Listar seu aplicativo na galeria de aplicativos do Azure Active Directory


##  <a name="what-is-the-azure-ad-application-gallery"></a>O que é a galeria de aplicativos do Azure Active Directory?

O Azure Active Directory é um serviço de identidade baseado em nuvem. A [galeria de aplicativos do Azure Active Directory](https://azure.microsoft.com/marketplace/active-directory/all/) está na loja de aplicativos do Azure Marketplace, onde todos os conectores de aplicativos são publicados para logon único e provisionamento de usuário. Os clientes que usam o Azure Active Directory como provedor de identidade localizam diferentes conectores de aplicativos SaaS publicados aqui. Os administradores de TI adicionam conectores da galeria de aplicativos e, em seguida, configuram e usam os conectores para logon único e provisionamento. O Azure Active Directory fornece suporte a todos os principais protocolos de federação para logon único, incluindo SAML 2.0, OpenID Connect, OAuth e WS-Fed. 

## <a name="what-are-the-benefits-of-listing-an-application-in-the-gallery"></a>Quais são os benefícios de listar um aplicativo na galeria?

*  Os clientes encontram a melhor experiência de logon único possível.

*  A configuração do aplicativo é simples e mínima. 

*  Uma pesquisa rápida localiza seu aplicativo na galeria.

*  Clientes do Azure Active Directory Premium, Básico e Gratuito podem usar essa integração. 

*  Os clientes mútuos recebem um tutorial de configuração passo a passo. 

*  Os clientes que usam SCIM podem usar o provisionamento para o mesmo aplicativo.


##  <a name="prerequisites-implement-federation-protocol"></a>Pré-requisitos: implementar protocolo de federação

Para listar um aplicativo na galeria de aplicativos do Azure Active Directory, primeiro é necessário implementar um dos seguintes protocolos de federação com suporte pelo Azure Active Directory. Leia os termos e as condições da galeria de aplicativos do Azure AD aqui. 

*   **OpenID Connect**: crie o aplicativo multilocatário no Azure Active Directory e implemente a [estrutura de consentimento do Azure Active Directory](active-directory-integrating-applications.md#overview-of-the-consent-framework) para seu aplicativo. Envie a solicitação de logon para um ponto de extremidade comum para que qualquer cliente possa dar consentimento ao aplicativo. É possível controlar acesso de usuário com base na ID do locatário e o UPN do usuário recebido no token. Para integrar o aplicativo com o Azure Active Directory, siga as [instruções do desenvolvedor](active-directory-authentication-scenarios.md).

*   **SAML 2.0** ou **WS-Fed**: seu aplicativo deve ter a capacidade de fazer a integração de SSO do SAML/WS-Fed no modo IDP ou SP. Se o aplicativo fornecer suporte para SAML 2.0, você poderá integrá-lo diretamente com um locatário do Azure Active Directory, usando as [instruções para adicionar uma aplicativo personalizado](../active-directory-saas-custom-apps.md).

*   **SSO de senha**: crie um aplicativo Web que tem uma página de entrada HTML para configurar o [logon único baseado em senha](../active-directory-appssoaccess-whatis.md). O SSO baseado em senha, também conhecido como armazenamento de senha em cofre, permite que você gerencie o acesso de usuários e senhas para aplicativos Web que não dão suporte a federação de identidades. Também é útil para cenários, nos quais vários usuários precisam compartilhar uma única conta, como contas de aplicativo de mídia social da sua organização. 

## <a name="submit-the-request-in-the-portal"></a>Enviar a solicitação no portal

Após testar que a integração do aplicativo funciona com o Azure Active Directory, envie sua solicitação para acesso no nosso [Portal de Rede de Aplicativos](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Se você tiver uma conta do Office 365, use-a para entrar nesse portal. Caso contrário, use sua conta da Microsoft (como Outlook ou Hotmail) para entrar.

Após entrar, a página a seguir será exibida. Forneça uma justificativa dos negócios para a necessidade de acesso na caixa de texto e, em seguida, selecione **Solicitar Acesso**. Nossa equipe analisa os detalhes e fornece-lhe o acesso adequadamente. Depois disso, você poderá entrar no portal e enviar sua solicitação detalhada para o aplicativo.

Se você tiver algum problema relacionado ao acesso, contate a [Equipe de Integração de SSO do Azure Active Directory](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

![Solicitação de acesso no portal do SharePoint](./media/active-directory-app-gallery-listing/accessrequest.png)

## <a name="timelines"></a>Linhas do tempo
    
A linha do tempo para o processo de listagem de um aplicativo WS-Fed ou SAML 2.0 na galeria é 7 a 10 dias úteis.

   ![TimeLine de listagem de aplicativos SAML na galeria](./media/active-directory-app-gallery-listing/timeline.png)

A linha do tempo para o processo de listagem de um aplicativo OpenID Connect na galeria é 2 a 5 dias úteis.

   ![TimeLine de listagem de aplicativos SAML na galeria](./media/active-directory-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Escalonamentos

Para quaisquer escalonamentos, envie email para a [Equipe de Integração de SSO do Azure Active Directory](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) e responderemos o mais rápido possível.

