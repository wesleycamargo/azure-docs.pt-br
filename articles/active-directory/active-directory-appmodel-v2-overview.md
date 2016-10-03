<properties
	pageTitle="Visão geral do modelo de aplicativo v2.0 | Microsoft Azure"
	description="Uma introdução à criação de aplicativos com conexão à conta da Microsoft e ao Active Directory do Azure."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="dastrock"/>

# Conecte-se os usuários da Conta da Microsoft e do Azure AD em um único aplicativo

Antigamente, um desenvolvedor de aplicativo que desejava oferecer suporte a contas da Microsoft e ao Active Directory do Azure tinha que se integrar a dois sistemas distintos. Agora apresentamos uma nova versão da API de autenticação que permite aos usuários se conectarem com os dois tipos de contas usando o sistema do Azure AD. Esse sistema de autenticação convergida é conhecido como **o ponto de extremidade v2.0**. Com o ponto de extremidade v2.0, uma simples integração permite alcançar um público que se estende por milhões de usuários com contas pessoais e corporativas/de estudante.

Aplicativos que usam o ponto de extremidade v2.0 também podem consumir APIs REST do [Microsoft Graph](https://graph.microsoft.io) e do [Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) usando qualquer tipo de conta.

<!-- For a quick introduction to the v2.0 endpoint, please view the [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/) video. -->

## Introdução
[AZURE.VIDEO build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps]

Escolha sua plataforma favorita abaixo para compilar um aplicativo usando nossas bibliotecas e estruturas de software livre. Como alternativa, é possível usar nossa documentação do protocolo do OAuth 2.0 e OpenID Connect para enviar e receber mensagens de protocolo diretamente sem o uso de uma biblioteca de autenticação.

<!-- TODO: Finalize this table  -->
[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## O que há de novo
As informações conceituais aqui serão úteis para entender o que é possível ou não com o ponto de extremidade v2.0.

- Se você criou um aplicativo durante o período de preview do ponto de extremidade v2.0 2015, não deixe de [ler sobre essas alterações de protocolo interruptivas](active-directory-v2-preview-oidc-changes.md) recentes.
- Saiba mais sobre os [tipos de aplicativos que podem ser criados com o ponto de extremidade v2.0](active-directory-v2-flows.md).
- Para os desenvolvedores que já estão familiarizados com o Azure Active Directory, é necessário verificar as [atualizações em nossos protocolos e as diferenças do ponto de extremidade v2.0](active-directory-v2-compare.md).
- Entenda as [limitações e restrições](active-directory-v2-limitations.md) do ponto de extremidade v2.0.

## Referência
Estes links serão úteis na exploração em profundidade da plataforma:

- Build 2016: [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/) (Introdução às identidades da Microsoft: conexão de classe empresarial para seus aplicativos)
- Obtenha ajuda sobre o Estouro de Pilha usando as marcas [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) ou [adal](http://stackoverflow.com/questions/tagged/adal).
- [Referência do Protocolo v2.0](active-directory-v2-protocols.md)
- [Referência do Token v2.0](active-directory-v2-tokens.md)
- [Escopos e Consentimento no ponto de extremidade v2.0](active-directory-v2-scopes.md)
- [O Portal de Registro de Aplicativo da Microsoft](https://apps.dev.microsoft.com)
- [Referência à API REST do Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [O Microsoft Graph](https://graph.microsoft.io)
- Abaixo estão listados os exemplos e as bibliotecas de clientes de software livre que foram testados com o ponto de extremidade da v2.0.

  - [Servidor de Identidade Java WSO2](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server)
  - [Java Gluu Federation](https://github.com/GluuFederation/oxAuth)
  - [Node.Js passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect)
  - [Cliente Básico do OpenID Connect PHP](https://github.com/jumbojett/OpenID-Connect-PHP)
  - [Cliente OAuth2 do iOS](https://github.com/nxtbgthng/OAuth2Client)
  - [Cliente OAuth2 do Android](https://github.com/wuman/android-oauth-client)
  - [Cliente OpenID Connect do Android](https://github.com/kalemontes/OIDCAndroidLib)

<!---HONumber=AcomDC_0921_2016-->