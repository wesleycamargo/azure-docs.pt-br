<properties
   pageTitle="Guia do desenvolvedor do Active Directory do Azure | Microsoft Azure"
   description="Este artigo fornece um guia abrangente para recursos para desenvolvedores do Active Directory do Azure."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/02/2016"
   ms.author="mbaldwin"/>


# Guia do desenvolvedor do Active Directory do Azure

## Visão geral
Como uma plataforma IDMaaS (Gerenciamento de Identidade como um Serviço), o Active Directory do Azure fornece aos desenvolvedores uma maneira eficiente de integrar o gerenciamento de identidades em seus aplicativos. Os artigos a seguir fornecem visões gerais sobre a implementação e os principais recursos do Active Directory do Azure. Sugerimos que você os leia na ordem ou pule para a [Introdução](#getting-started) se estiver pronto para se aprofundar.


1. [Os benefícios da integração do Active Directory do Azure](active-directory-how-to-integrate.md): descubra por que a integração com o Active Directory do Azure oferece a melhor solução para entrada e autorização seguras.

1. [Cenários de autenticação do Active Directory](active-directory-authentication-scenarios.md): Aproveite a autenticação simplificada do Active Directory do Azure para fornecer logon em seu aplicativo.

1. [Integrando aplicativos ao Active Directory do Azure](active-directory-integrating-applications.md): saiba mais sobre como adicionar, atualizar e remover aplicativos do Active Directory do Azure e sobre as diretrizes de identidade visual para aplicativos integrados.

1. [Graph API do Active Directory do Azure](active-directory-graph-api.md): use a Graph API do Active Directory do Azure para acessar programaticamente o Active Directory do Azure por meio de pontos de extremidade da API REST. Observe que a Graph API do Azure AD também é acessível por meio da [Microsoft Graph](https://graph.microsoft.io/), uma API unificada que permite acesso a várias APIs do serviço de nuvem da Microsoft por meio de um único ponto de extremidade da API REST e com um token de acesso único.

1. [Bibliotecas de autenticação do Active Directory do Azure](active-directory-authentication-libraries.md): autentique facilmente os usuários para obter tokens de acesso usando as bibliotecas de autenticação do Azure AD para .NET, JavaScript, Objective-C, Android e mais.


## Introdução

Esses tutoriais são adaptados para várias plataformas e permitem que você comece a desenvolver rapidamente com o Active Directory do Azure. Como um pré-requisito, você deve [obter um locatário do Active Directory do Azure](active-directory-howto-tenant.md).

### Guias de início rápido para aplicativo móvel ou PC

|[![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)| [![Windows Phone](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsphone.md)|[![Windows Store](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:
|[iOS](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Windows Phone](active-directory-devquickstarts-windowsphone.md)|[Windows Store](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)

### Guias de início rápido de aplicativo Web

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md)|[![JavaScript](./media/active-directory-developers-guide/javascript.png)](active-directory-devquickstarts-angular.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md)
|:--:|:--:|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapp-dotnet.md)|[Java](active-directory-devquickstarts-webapp-java.md)|[Javascript](active-directory-devquickstarts-angular.md)|[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md)

### Guias de início rápido de API Web

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md)
|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapi-dotnet.md)|[Node.js](active-directory-devquickstarts-webapi-nodejs.md)

### Consultando o guia de início rápido do diretório

| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md)|
|:--:|
|[API gráfica](active-directory-graph-api-quickstart.md)|

## Instruções

Esses artigos descrevem como executar tarefas específicas usando o Active Directory do Azure.

- [Obter um locatário do Active Directory do Azure](active-directory-howto-tenant.md)
- [Listar seu aplicativo na galeria de aplicativos do Active Directory do Azure](active-directory-app-gallery-listing.md)
- [Noções básicas sobre o manifesto do aplicativo do Active Directory do Azure](active-directory-application-manifest.md)
- [Criar um aplicativo com APIs do Office 365](https://msdn.microsoft.com/office/office365/howto/getting-started-Office-365-APIs)
- [Enviar aplicativos Web para Office 365 para o Painel do Vendedor](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- Saiba como habilitar o SSO entre aplicativos usando a ADAL em dispositivos [Android](active-directory-sso-android.md) e [iOS](active-directory-sso-ios.md)
- [Visualização: como criar aplicativos que conectam usuários com as contas pessoais e corporativas ou de estudante](active-directory-appmodel-v2-overview.md)
- [Visualização: como criar aplicativos que registram e conectam consumidores](../active-directory-b2c/active-directory-b2c-overview.md)


## Referência

Esses artigos fornecem referências básicas para REST e biblioteca de autenticação APIs, protocolos, erros, exemplos de código e pontos de extremidade.

###  Suporte
- [Perguntas marcadas](http://stackoverflow.com/questions/tagged/azure-active-directory): encontre soluções do Active Directory do Azure no Excedente de Pilha pesquisando as marcas [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) e [adal](http://stackoverflow.com/questions/tagged/adal).

### Código

- [Bibliotecas de código aberto do Active Directory do Azure](http://github.com/AzureAD): a maneira mais fácil de encontrar o código de uma biblioteca é usando nossa [lista de bibliotecas](active-directory-authentication-libraries.md).

- [Exemplos do Active Directory do Azure](https://github.com/azure-samples?query=active-directory): a maneira mais fácil de navegar pela lista de exemplos é usando o [índice de exemplos de código](active-directory-code-samples.md).

- [ADAL para .NET](https://msdn.microsoft.com/library/azure/mt417579.aspx): documentação para a biblioteca de autenticação do .NET.

### API gráfica

- [Referência da Graph API](https://msdn.microsoft.com/library/azure/hh974476.aspx): referência REST para a Graph API do Active Directory do Azure. [Exiba a experiência de referência da Graph API interativa](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- [Escopos de permissão da Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes): os escopos de permissão do OAuth 2.0 usados para controlar o acesso que um aplicativo tem aos dados do diretório em um locatário.

### Protocolos de autenticação

- [Referência do protocolo SAML 2.0](https://msdn.microsoft.com/library/azure/dn195591.aspx): o protocolo SAML 2.0 permite que os aplicativos forneçam uma experiência de logon único aos usuários.


- [Referência de protocolo do OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx): você pode usar o protocolo do OAuth 2.0 para autorizar o acesso a aplicativos Web e APIs Web em seu locatário do Active Directory do Azure.


- [Referência de protocolo do OpenID Connect 1.0](https://msdn.microsoft.com/library/azure/dn645541.aspx): o protocolo do OpenID Connect 1.0 estende o OAuth 2.0 para uso como um protocolo de autenticação.


- [Referência do protocolo WS-Federation 1.2](https://msdn.microsoft.com/library/azure/dn903702.aspx): o protocolo WS-Federation 1.2 é descrito na especificação Web Services Federation Versão 1.2.

- [Tipos de token e declaração com suporte](active-directory-token-and-claims.md): você pode usar este guia para entender e avaliar as declarações nos tokens SAML 2.0 e JWT (Tokens Web JSON).

## Vídeos

### Compilação

Essas apresentações de visão geral sobre o desenvolvimento de aplicativos usando o Active Directory do Azure apresentam alto-falantes que trabalham diretamente na equipe de engenharia. As apresentações cobrem tópicos fundamentais, incluindo IDMaaS, autenticação, federação de identidade e logon único.

- [Identidade da Microsoft: Estado da União e direção futura](https://azure.microsoft.com/documentation/videos/build-2016-microsoft-identity-state-of-the-union-and-future-direction/)
- [Active Directory do Azure: Gerenciamento de identidade como um serviço para aplicativos modernos](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
- [Desenvolva aplicativos Web modernos com o Active Directory do Azure](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
- [Desenvolva aplicativos nativos modernos com o Active Directory do Azure](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### Azure Friday
O [Azure Friday](https://azure.microsoft.com/documentation/videos/azure-friday/) é uma série de vídeos introdutórios periódicos que tem como objetivo oferecer entrevistas breves (10 a 15 minutos) com especialistas sobre uma variedade de tópicos do Azure. Use o recurso de filtro de serviços na página para ver todos os vídeos do Azure Active Directory.

- [Identidade do Azure 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
- [Identidade do Azure 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
- [Identidade do Azure 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## Redes sociais

- [Blog da equipe do Active Directory](http://blogs.technet.com/b/ad/): os desenvolvimentos mais recentes no mundo do Active Directory do Azure.

- [Blog da equipe da Graph do Azure Active Directory](http://blogs.msdn.com/b/aadgraphteam): informações do Active Directory do Azure específicas à Graph API.

- [Identidade de Nuvem](http://www.cloudidentity.net): considerações sobre o gerenciamento de identidades como serviço de um PM do Azure Active Directory de entidade de segurança.

- [Active Directory do Azure no Twitter](https://twitter.com/azuread): comunicados do Active Directory do Azure em 140 caracteres ou menos.

<!---HONumber=AcomDC_0413_2016-->