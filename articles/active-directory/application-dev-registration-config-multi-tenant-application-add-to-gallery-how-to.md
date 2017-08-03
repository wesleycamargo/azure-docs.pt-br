---
title: "Como adicionar um aplicativo multilocatário à galeria de aplicativos Azure AD | Microsoft Docs"
description: "Explica como listar seu aplicativo multilocatário personalizado na galeria de aplicativos Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 9f3caff0e998d670ce82b847525a63f2a85572d3
ms.contentlocale: pt-br
ms.lasthandoff: 04/17/2017

---

# <a name="how-to-add-a-multi-tenant-application-to-the-azure-ad-application-gallery"></a>Como adicionar um aplicativo multilocatário à Galeria de Aplicativos Azure AD

## <a name="what-is-the-azure-ad-application-gallery"></a>O que é a Galeria de Aplicativos Azure AD?

A Galeria de Aplicativos Azure AD é uma ótima maneira de colocar seu aplicativo diante de milhões de clientes do Azure Active Directory para ampliar o impacto e o alcance do aplicativo no marketplace. As etapas a seguir explicam como listar seu aplicativo na Galeria de Aplicativos Azure AD.

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Se seu aplicativo der suporte a SAML ou OpenIDConnect
Se você tiver um aplicativo multilocatário que queira listar na Galeria de Aplicativos Azure AD, deverá primeiro assegurar que seu aplicativo dê suporte a uma das seguintes tecnologias de logon único:

1. **OpenID Connect** : integração direta com o Azure AD usando o OpenID Connect para autenticação e a API de consentimento do AD do Azure para configuração. Se você estiver apenas começando uma integração e seu aplicativo não der suporte a SAML, esse é o modo recomendado.
2. **SAML** : seu aplicativo já tem a capacidade de configurar provedores de identidade de terceiros usando o protocolo SAML.

Se seu aplicativo der suporte a um desses modos de logon único e você quiser listar seu aplicativo multilocatário na Galeria de Aplicativos Azure AD, siga as etapas no documento abaixo. Para iniciar rapidamente envie um email para **waadpartners@microsoft.com**.

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Se seu aplicativo não der suporte a SAML ou OpenIDConnect
Mesmo que seu aplicativo não dê suporte a um desses modos, ainda assim podemos integrá-lo em nossa galeria usando nossa tecnologia de Logon único de senha. Para explorar essa opção, envie um email para **waadpartners@microsoft.com**.

## <a name="next-steps"></a>Próximas etapas
[Como listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)

