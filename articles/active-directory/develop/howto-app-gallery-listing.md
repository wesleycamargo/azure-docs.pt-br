---
title: Listar seu aplicativo na galeria de aplicativos do Azure Active Directory | Microsoft Docs
description: Saiba como listar um aplicativo que oferece suporte a logon único na galeria de aplicativos do Azure Active Directory
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/31/2018
ms.author: celested
ms.reviewer: elisol, bryanla
ms.custom: aaddev
ms.openlocfilehash: e5db7b9bed674011c2922f026c301172f347f53f
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666301"
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

## <a name="prerequisites"></a>Pré-requisitos

- Para aplicativos federados (Open ID e SAML / WS-Fed), o aplicativo deve oferecer suporte ao modelo SaaS para ser listado na galeria do Azure AD. Os aplicativos de galeria da empresa devem oferecer suporte a várias configurações de cliente e não a nenhum cliente específico.

- Para o Open ID Connect, o aplicativo deve ser multitenariado e a [estrutura de consentimento do Azure AD](quickstart-v1-integrate-apps-with-azure-ad.md#overview-of-the-consent-framework) deve ser implementada adequadamente para o aplicativo. O usuário pode enviar a solicitação de login para um terminal comum para que qualquer cliente possa fornecer consentimento para o aplicativo. É possível controlar acesso de usuário com base na ID do locatário e o UPN do usuário recebido no token.

- Para o SAML 2.0 / WS-Fed, seu aplicativo precisa ter a capacidade de fazer a integração SSO de SAML / WS-Fed no modo SP ou IDP. Por favor, verifique se está funcionando corretamente antes de enviar a solicitação.

- Para o SSO de Senha, certifique-se de que seu aplicativo ofereça suporte à autenticação de formulário para que o armazenamento de senhas possa ser feito para que o logon único funcione conforme o esperado.

- Para solicitações de aprovisionamento automático de usuários, o aplicativo deve ser listado na galeria com o recurso de conexão única ativado usando qualquer um dos protocolos de federação descritos acima. Você pode solicitar o fornecimento de SSO e Usuário juntos no portal, se ele ainda não estiver listado.

##  <a name="implementing-sso-using-federation-protocol"></a>Implementando o SSO usando o protocolo de federação

Para listar um aplicativo na galeria de aplicativos do Azure AD, primeiro é necessário implementar um dos seguintes protocolos de federação compatíveis com o Azure AD, além de concordar com os termos e condições da Galeria de aplicativos do Azure AD. Leia [aqui](https://azure.microsoft.com/en-us/support/legal/active-directory-app-gallery-terms/) os termos e condições da galeria de aplicativos do Azure AD.

*   **OpenID Connect**: para integrar seu aplicativo ao Azure AD usando o protocolo Open ID Connect, siga as [ instruções dos desenvolvedores ](authentication-scenarios.md).

    ![TimeLine de listagem de aplicativos OpenID Connect na galeria](./media/howto-app-gallery-listing/openid.png)

    * Se você desejar adicionar o aplicativo à lista na galeria usando OpenID Connect, selecione **OpenID Connect & OAuth 2.0** como acima.

    * Se você tiver algum problema relacionado ao acesso, contate a [Equipe de Integração de SSO do Azure Active Directory](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

*   **SAML 2.0** ou **WS-Fed**: se seu aplicativo for compatível com SAML 2.0, você poderá integrá-lo diretamente a um locatário do Azure AD usando as instruções [para adicionar um aplicativo personalizado](../active-directory-saas-custom-apps.md).

    ![TimeLine de listagem de aplicativos SAML 2.0 ou WS-Fed na galeria](./media/howto-app-gallery-listing/saml.png)

    * Se você desejar adicionar o aplicativo à lista na galeria usando **SAML 2.0** ou **WS-Fed**, selecione **SAMl 2.0, WS-Fed** como acima.

    * Se você tiver algum problema relacionado ao acesso, contate a [Equipe de Integração de SSO do Azure Active Directory](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implementing-sso-using-password-sso"></a>Implementando o SSO usando o SSO de senha

Crie um aplicativo da Web que tenha uma página de login HTML para configurar a [conexão única baseada em senha](../manage-apps/what-is-single-sign-on.md). O SSO baseado em senha, também conhecido como armazenamento de senha em cofre, permite que você gerencie o acesso de usuários e senhas para aplicativos Web que não dão suporte a federação de identidades. Também é útil para cenários, nos quais vários usuários precisam compartilhar uma única conta, como contas de aplicativo de mídia social da sua organização.

![TimeLine de listagem de aplicativos SSO de Senha na galeria](./media/howto-app-gallery-listing/passwordsso.png)

* Se você desejar adicionar o aplicativo à lista na galeria usando SSO de Senha, selecione **SSO de Senha** como acima.

* Se você tiver algum problema relacionado ao acesso, contate a [Equipe de Integração de SSO do Azure Active Directory](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

##  <a name="updateremove-existing-listing"></a>Atualizar/remover listagem existente

Para atualizar ou remover um aplicativo existente na galeria de aplicativos do Microsoft Azure AD, primeiro você precisa enviar a solicitação no [Portal de Aplicativos de Rede ](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Se você tiver uma conta do Office 365, use-a para entrar nesse portal. Caso contrário, use sua conta da Microsoft (como Outlook ou Hotmail) para entrar.

* Selecione a opção apropriada na imagem abaixo

    ![TimeLine de listagem de aplicativos SAML na galeria](./media/howto-app-gallery-listing/updateorremove.png)

    * Se você deseja atualizar um aplicativo existente, selecione **Atualizar listagem de aplicativo existente**.

    * Se você quiser remover um aplicativo existente da galeria do Microsoft Azure AD, selecione **Remover a listagem de aplicativo existente**

    * Se você tiver algum problema relacionado ao acesso, contate a [Equipe de Integração de SSO do Azure Active Directory](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

## <a name="submit-the-request-in-the-portal"></a>Enviar a solicitação no portal

Após testar que a integração do aplicativo funciona com o Azure Active Directory, envie sua solicitação para acesso no nosso [Portal de Rede de Aplicativos](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Se você tiver uma conta do Office 365, use-a para entrar nesse portal. Caso contrário, use sua conta da Microsoft (como Outlook ou Hotmail) para entrar.

Após entrar, a página a seguir será exibida. Forneça uma justificativa dos negócios para a necessidade de acesso na caixa de texto e, em seguida, selecione **Solicitar Acesso**. Nossa equipe analisa os detalhes e fornece-lhe o acesso adequadamente. Depois disso, você poderá entrar no portal e enviar sua solicitação detalhada para o aplicativo.

Se você tiver algum problema relacionado ao acesso, contate a [Equipe de Integração de SSO do Azure Active Directory](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

![Solicitação de acesso no portal do SharePoint](./media/howto-app-gallery-listing/accessrequest.png)

## <a name="timelines"></a>Linhas do tempo
    
A linha do tempo para o processo de listagem de um aplicativo WS-Fed ou SAML 2.0 na galeria é 7 a 10 dias úteis.

   ![TimeLine de listagem de aplicativos SAML na galeria](./media/howto-app-gallery-listing/timeline.png)

A linha do tempo para o processo de listagem de um aplicativo OpenID Connect na galeria é 2 a 5 dias úteis.

   ![TimeLine de listagem de aplicativos SAML na galeria](./media/howto-app-gallery-listing/timeline2.png)

A linha do tempo para o processo de listagem do aplicativo na galeria com suporte ao provisionamento de usuário é de 40-45 dias úteis.

   ![TimeLine de listagem de aplicativos SAML na galeria](./media/howto-app-gallery-listing/provisioningtimeline.png)

## <a name="escalations"></a>Escalonamentos

Para quaisquer escalonamentos, envie email para [Equipe de Integração de SSO do Microsoft Azure AD](mailto:SaaSApplicationIntegrations@service.microsoft.com)  que é SaaSApplicationIntegrations@service.microsoft.com e responderemos o mais breve possível.