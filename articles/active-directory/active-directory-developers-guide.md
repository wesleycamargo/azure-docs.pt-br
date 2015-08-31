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
   ms.date="08/12/2015"
   ms.author="mbaldwin"/>


# Guia do desenvolvedor do Active Directory do Azure

## Visão geral
Como uma plataforma IDMaaS (Gerenciamento de Identidade como um Serviço), o Active Directory do Azure fornece aos desenvolvedores uma maneira eficiente de integrar o gerenciamento de identidades em seus aplicativos. Os artigos a seguir fornecem visões gerais sobre a implementação e os principais recursos do Active Directory do Azure. Sugerimos que você os leia na ordem ou pule para a [Introdução](#getting-started) se estiver pronto para se aprofundar.


1. [Como integrar-se ao Active Directory do Azure](active-directory-how-to-integrate.md): Descubra por que a integração com o Active Directory do Azure oferece a melhor solução para autorização e entrada seguras.

1. [Cenários de autenticação do Active Directory](active-directory-authentication-scenarios.md): Aproveite a autenticação simplificada do Active Directory do Azure para fornecer logon em seu aplicativo.

1. [Graph API do Active Directory do Azure](https://msdn.microsoft.com/library/azure/hh974476.aspx): Use o Graph API do Active Directory do Azure para acessar programaticamente o Active Directory do Azure por meio de pontos de extremidade da REST API.

1. [Integrar aplicações no Active Directory do Azure](active-directory-integrating-applications.md): Saiba mais sobre como registrar seu aplicativo e as diretrizes de identidade visual para aplicativos multilocatários.

1. [Bibliotecas de Autenticação do Active Directory do Azure](active-directory-authentication-libraries.md): Autentique facilmente os usuários para obter tokens de acesso usando as bibliotecas de autenticação do Azure.

Para exibir as visões gerais do Active Directory do Azure apresentadas na conferência Build 2015, consulte a seção abaixo [Vídeos](#videos).


## Introdução

Esses tutoriais são adaptados para várias plataformas e permitem que você comece a desenvolver rapidamente com o Active Directory do Azure. Como um pré-requisito, você deve [Obter um locatário do Active Directory do Azure](active-directory-howto-tenant.md).

### Guias de início rápido de aplicativo móvel ou para PC

|[![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)| [![Windows Phone](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsphone.md)|[![Windows Store](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:
|[iOS](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Windows Phone](active-directory-devquickstarts-windowsphone.md)|[Windows Store](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)


### Guias de início rápido para aplicativos Web ou API da Web

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md)|[![JavaScript](./media/active-directory-developers-guide/javascript.png)](active-directory-devquickstarts-angular.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md)
|:--:|:--:|:--:|:--:
|[Aplicativo Web .NET](active-directory-devquickstarts-webapp-dotnet.md)|[API da Web .NET](active-directory-devquickstarts-webapi-dotnet.md)|[Javascript](active-directory-devquickstarts-angular.md)|[Node.js](active-directory-devquickstarts-webapi-nodejs.md)


## Instruções

Esses artigos descrevem como executar tarefas específicas usando o Active Directory do Azure.

- [Como obter um locatário do Active Directory do Azure](active-directory-howto-tenant.md)
- [Listando seu aplicativo na galeria de aplicativos do Azure Active Directory](active-directory-app-gallery-listing.md)
- [Criar um aplicativo com APIs do Office 365](https://msdn.microsoft.com/office/office365/howto/getting-started-Office-365-APIs)
- [Enviar aplicativos Web para Office 365 para o Painel do Vendedor](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- [Visualização: como criar aplicativos que conectam usuários com as contas pessoais e corporativas ou de estudante](active-directory-appmodel-v2-overview.md)


## Referência

Esses artigos fornecem referências básicas para REST e biblioteca de autenticação APIs, protocolos, erros, exemplos de código e pontos de extremidade.

###  Suporte
- [Perguntas marcadas](http://stackoverflow.com/questions/tagged/azure-active-directory): Localize soluções em Stack Overflow no Azure Active do Azure procurando pelas guias [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) e [adal](http://stackoverflow.com/questions/tagged/adal).

### Código

- [Bibliotecas de código livre do Active Directory do Azure](http://github.com/AzureAD): A maneira mais fácil de localizar o código-fonte de uma biblioteca é usando nossa [lista de bibliotecas](active-directory-authentication-libraries.md).

- [Exemplos do Active Directory do Azure](http://github.com/AzureADSamples): A maneira mais fácil para navegar pela lista de exemplos é usando o [índice de exemplos de código](active-directory-code-samples.md).


### API gráfica

- [Referência da Graph API](https://msdn.microsoft.com/library/azure/hh974476.aspx): referência REST para a Graph API do Active Directory do Azure. [Exiba a experiência de referência da Graph API interativa](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- [Escopos de permissão da Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/graph-api-permission-scopes): Os escopos de permissão do OAuth 2.0 que são usados para controlar o acesso que um aplicativo tem aos dados do diretório em um locatário.


### Protocolos de autenticação

- [Referência de protocolo do SAML 2.0](https://msdn.microsoft.com/library/azure/dn195591.aspx): O protocolo do SAML 2.0 permite que os aplicativos forneçam uma experiência de logon único para seus usuários.


- [Referência de protocolo do OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx): Vocês pode usar o protocolo do OAuth 2.0 para autorizar o acesso a aplicativos Web e APIs da Web em seu locatário do Active Directory do Azure.


- [Referência de protocolo do OpenID Connect 1.0](https://msdn.microsoft.com/library/azure/dn645541.aspx): O protocolo do OpenID Connect 1.0 estende o OAuth 2.0 para uso como um protocolo de autenticação.


- [Referência de protocolo do WS-Federation 1.2](https://msdn.microsoft.com/library/azure/dn903702.aspx): O protocolo do WS-Federation 1.2 é descrito na Especificação Web Services Federation Versão 1.2.

- [Suporte para tipos de token e declaração](active-directory-token-and-claims.md): Você pode usar esta guia para compreender e avaliar as declarações em tokens SAML 2.0 e JSON Web Tokens (JWT).

## Vídeos

### Build 2015

Essas apresentações de visão geral sobre o desenvolvimento de aplicativos usando o Active Directory do Azure apresentam alto-falantes que trabalham diretamente na equipe de engenharia. As apresentações cobrem tópicos fundamentais, incluindo IDMaaS, autenticação, federação de identidade e logon único.

- [Active Directory do Azure: Gerenciamento de identidade como um serviço para aplicativos modernos](http://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications)
- [Desenvolva aplicativos Web modernos com o Active Directory do Azure](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory)
- [Desenvolva aplicativos nativos modernos com o Active Directory do Azure](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory)

### Azure Friday
[Azure Friday](http://azure.microsoft.com/documentation/videos/azure-friday/) é uma série de vídeos introdutórios periódicos com o objetivo de oferecer entrevistas curtas de 10 a 15 minutos com especialistas sobre uma variedade de tópicos do Azure. Use o recurso de filtro de serviços na página para ver todos os vídeos do Azure Active Directory.

- [Identidade do Azure 101](http://azure.microsoft.com/documentation/videos/azure-identity-basics/)
- [Identidade do Azure 102](http://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
- [Identidade do Azure 103](http://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## Redes sociais

- [Blog da Equipe do Active Directory](http://blogs.technet.com/b/ad/): Os desenvolvimentos mais recentes no mundo do Active Directory do Azure.

- [Blog da Equipe do Active Directory do Azure Graph](http://blogs.msdn.com/b/aadgraphteam): Informações do Active Directory do Azure que são específicas à Graph API.

- [Identidade em Nuvem](http://www.cloudidentity.net): Considerações sobre gerenciamento de identidades como um serviço de um PM principal do Active Directory do Azure.

- [Active Directory do Azure no Twitter](https://twitter.com/azuread): Avisos do Active Directory do Azure em 140 caracteres ou menos.

<!---HONumber=August15_HO8-->