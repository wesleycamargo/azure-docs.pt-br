---
title: Visão geral do ponto de extremidade v2.0 | Microsoft Docs
description: Uma introdução à criação de aplicativos com conexão à conta da Microsoft e ao Active Directory do Azure.
services: active-directory
documentationcenter: ''
author: dstrockis
manager: mbaldwin
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: dastrock

---
# <a name="sign-in-microsoft-account-&-azure-ad-users-in-a-single-app"></a>Conecte-se os usuários da Conta da Microsoft e do Azure AD em um único aplicativo
Antigamente, um desenvolvedor de aplicativo que desejava oferecer suporte a contas da Microsoft e ao Active Directory do Azure tinha que se integrar a dois sistemas distintos.  Agora apresentamos uma nova versão da API de autenticação que permite aos usuários se conectarem com os dois tipos de contas usando o sistema do Azure AD.  Esse sistema de autenticação convergida é conhecido como **o ponto de extremidade v2.0**.  Com o ponto de extremidade v2.0, uma simples integração permite alcançar um público que se estende por milhões de usuários com contas pessoais e corporativas/de estudante.

Aplicativos que usam o ponto de extremidade v2.0 também podem consumir APIs REST do [Microsoft Graph](https://graph.microsoft.io) e do [Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) usando qualquer tipo de conta.

<!-- For a quick introduction to the v2.0 endpoint, please view the [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/) video. -->

## <a name="getting-started"></a>Introdução
[!VIDEO https://channel9.msdn.com/Events/Build/2016/P530/player]


Escolha sua plataforma favorita na lista a seguir para compilar um aplicativo usando nossas bibliotecas e estruturas de software livre.  Como alternativa, é possível usar nossa documentação do protocolo do OAuth 2.0 e OpenID Connect para enviar e receber mensagens de protocolo diretamente sem o uso de uma biblioteca de autenticação.

<!-- TODO: Finalize this table  -->
[!INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## <a name="what's-new"></a>O que há de novo
As informações conceituais aqui serão úteis para entender o que é possível ou não com o ponto de extremidade v2.0.

* Saiba mais sobre os [tipos de aplicativos que podem ser criados com o ponto de extremidade v2.0](active-directory-v2-flows.md).
* Entenda as [limitações e restrições](active-directory-v2-limitations.md) do ponto de extremidade v2.0.
* Recentemente, adicionamos suporte para [escopos de administrador restritos](active-directory-v2-scopes.md) e [Conceder credenciais do cliente OAuth2](active-directory-v2-protocols-oauth-client-creds.md).  Experimente-os!

## <a name="reference"></a>Referência
Estes links serão úteis na exploração em profundidade da plataforma:

* Build 2016: [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/)
* Obtenha ajuda no Stack Overflow usando as marcas [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) ou [adal](http://stackoverflow.com/questions/tagged/adal).
* [Referência do Protocolo v2.0](active-directory-v2-protocols.md)
* [Referência do Token v2.0](active-directory-v2-tokens.md)
* [Referência da Biblioteca v2.0](active-directory-v2-libraries.md)
* [Escopos e Consentimento no ponto de extremidade v2.0](active-directory-v2-scopes.md)
* [O Portal de Registro de Aplicativo da Microsoft](https://apps.dev.microsoft.com)
* [Referência à API REST do Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
* [O Microsoft Graph](https://graph.microsoft.io)

<!--HONumber=Oct16_HO2-->


