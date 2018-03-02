---
title: "Como adicionar um aplicativo multilocatário à galeria de aplicativos do Azure AD | Microsoft Docs"
description: "Explica como listar seu aplicativo multilocatário personalizado na Galeria de Aplicativos do Azure AD"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: jeedes
ms.openlocfilehash: f29f7cbf118d4d70c1ea2cca174ff0cf0ba9bd14
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-add-a-multi-tenant-application-to-the-azure-ad-application-gallery"></a>Como adicionar um aplicativo multilocatário à Galeria de Aplicativos do Azure AD

## <a name="what-is-the-azure-ad-application-gallery"></a>O que é a Galeria de Aplicativos do Azure AD?

O Azure AD é um serviço de Identidade baseado em nuvem. A [galeria de aplicativos Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) é um repositório comum em que todos os conectores de aplicativos são publicados para logon único e provisionamento de usuário. Nossos clientes mútuos que estão usando o Azure AD como Provedor de identidade procuram conectores de aplicativos SaaS diferentes, que são publicados aqui. O administrador de TI adiciona o conector por meio da galeria de aplicativos e configura-o e usa-o para o Logon único e provisionamento. O Azure Active Directory dá suporte a todos os principais protocolos de federação, como SAML 2.0, OpenID Connect, OAuth e WS-Fed, para logon único. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Se seu aplicativo der suporte a SAML ou OpenIDConnect
Se tiver um aplicativo multilocatário que queira listar na Galeria de Aplicativos do Azure AD, você precisa primeiro assegurar que seu aplicativo dê suporte a uma das seguintes tecnologias de logon único:

1. **OpenID Connect** – crie o aplicativo multilocatário no Azure AD e implemente a [estrutura de consentimento do Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications#overview-of-the-consent-framework) em seu aplicativo. Envie a solicitação de logon para o ponto de extremidade comum, de modo que qualquer cliente possa dar o consentimento para o aplicativo. Controle o acesso do usuário cliente com base na ID de locatário e no nome UPN do usuário recebido no token. Envie o aplicativo conforme mencionado neste [artigo](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing).

2. **SAML** – Se o aplicativo tem suporte para SAML 2.0, então, é possível listar o aplicativo na galeria e as instruções estão listadas [aqui](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing)

Se o aplicativo tiver suporte para um desses modos de logon único e você quiser listar o aplicativo multilocatário na Galeria de Aplicativos do Microsoft Azure Active Directory, você pode seguir as etapas mencionadas [neste](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing) artigo. 

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Se seu aplicativo não der suporte a SAML ou OpenIDConnect
Mesmo que seu aplicativo não dê suporte a um desses modos, ainda assim podemos integrá-lo a nossa galeria usando nossa tecnologia de Logon único com senha.

**SSO de senha** – crie um aplicativo Web que tem uma página de entrada HTML para configurar o [logon único baseado em senha](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-appssoaccess-whatis). O SSO baseado em senha, também conhecido como armazenamento de senha em cofre, permite que você gerencie o acesso de usuários e senhas para aplicativos Web que não dão suporte a federação de identidades. Também é útil para cenários onde vários usuários precisam compartilhar uma única conta, como contas de aplicativo de mídia social da sua organização. 

Se você quiser listar o aplicativo com essa tecnologia, envie a solicitação conforme descrito [neste](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-app-gallery-listing) artigo.

## <a name="escalations"></a>Escalonamentos

Para escalonamentos, envie um email para a [Equipe de Integração de SSO do Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) e responderemos o mais rápido possível.

## <a name="next-steps"></a>Próximas etapas
[Como listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)
