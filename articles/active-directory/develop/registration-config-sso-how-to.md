---
title: Como configurar um novo aplicativo multilocatário | Microsoft Docs
description: Como configurar o logon único para um aplicativo personalizado que você está desenvolvendo e registrar com o Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: 2aa3efcdec57a3e236d2b9739636656b35d16cd6
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44723592"
---
# <a name="how-to-configure-a-new-multi-tenant-application"></a>Como configurar um novo aplicativo multilocatário

Habilitar logon único federado (SSO) em seu aplicativo é habilitado automaticamente por meio do Azure AD para OpenID Connect, SAML 2.0 ou WS-Fed. Se os usuários finais estão precisando entrar, mesmo já tendo uma sessão existente com o Azure AD, é provável que seu aplicativo possa estar configurado incorretamente.

* Se você estiver usando o ADAL/MSAL, verifique se você tem o **PromptBehavior** definido como **Automático** em vez de **Sempre**.

* Se você estiver criando um aplicativo móvel, talvez seja necessário configurações adicionais para habilitar SSO orientado ou não orientado.

Para o Android, consulte [Habilitando SSO entre aplicativos no Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android).<br>

Para o iOS, consulte [Habilitando SSO entre aplicativos no iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios).

## <a name="next-steps"></a>Próximas etapas

[SSO do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Habilitando SSO entre aplicativos no Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[Habilitando SSO entre aplicativos no iOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[Integrando aplicativos ao AzureAD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Consentimento e permissão para aplicativos convergidos do AzureAD v2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[StackOverflow do AzureAD](http://stackoverflow.com/questions/tagged/azure-active-directory)
