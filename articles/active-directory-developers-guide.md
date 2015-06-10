<properties
   pageTitle="Guia do desenvolvedor do Azure Active Directory"
   description="Um guia abrangente para recursos para desenvolvedores do Azure Active Directory"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/08/2015"
   ms.author="mbaldwin"/>


# Guia do desenvolvedor do Azure Active Directory

## Visão geral
Como uma plataforma IDMaaS (Gerenciamento de Identidade como um Serviço), o Azure Active Directory fornece aos desenvolvedores uma maneira eficiente de integrar o gerenciamento de identidades em seus aplicativos. Os artigos a seguir fornecem visões gerais sobre a implementação e os principais recursos do Azure Active Directory. Sugerimos que você os leia na ordem ou pule para a [Introdução](#getting-started) se estiver pronto para se aprofundar.


1. **[Como para integrar-se ao Azure AD](active-directory-how-to-integrate.md)**: descubra por que a integração com o Azure Active Directory oferece a melhor solução para login seguro e autorização.

1. **[Usando o Azure AD para logon](active-directory-authentication-scenarios.md)**: aproveite a autenticação simplificada do Azure Active Directory para logon em seu aplicativo.

1. **[Consultando o diretório](https://msdn.microsoft.com/library/azure/hh974476.aspx)**: use a Graph API do Azure Active Directory para acessar programaticamente o Azure AD por meio de pontos de extremidade da API REST.

1. **[Entendendo o modelo de aplicativo](https://msdn.microsoft.com/library/azure/dn151122.aspx)**: Saiba mais sobre como registrar seu aplicativo e as diretrizes de identidade visual para aplicativos multilocatários.

1. **[Bibliotecas](https://msdn.microsoft.com/library/azure/dn151135.aspx)**: autentique facilmente os usuários para obter tokens de acesso com as bibliotecas de autenticação do Azure.

#### Vídeos
Se você prefere aprender com vídeos, consulte estas apresentações de visão geral sobre como desenvolver aplicativos com o Azure Active Directory. Normalmente, eles apresentam alto-falantes que funcionam diretamente na equipe de engenharia e tópicos fundamentais de instrução, incluindo IDMaaS, autenticação, federação de identidade e logon único.

- **[Azure Active Directory: Gerenciamento de identidade como um serviço para aplicativos modernos](http://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications)**
- **[Desenvolva aplicativos Web modernos com o Azure Active Directory](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory)**
- **[Desenvolva aplicativos nativos modernos com o Azure Active Directory](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory)**

## Introdução

Esses tutoriais são adaptados para várias plataformas e permitem que você inicie rapidamente o desenvolvimento com o Azure Active Directory. Como um pré-requisito, você deve [obter um locatário do Azure Active Directory](active-directory-howto-tenant.md).

#### Guias de início rápido de aplicativo móvel ou para PC

- [iOS](active-directory-devquickstarts-ios.md)
- [Android](active-directory-devquickstarts-android.md)
- [.NET](active-directory-devquickstarts-dotnet.md)
- [Windows Phone](active-directory-devquickstarts-windowsphone.md)
- [Windows Store](active-directory-devquickstarts-windowsstore.md)
- [Xamarin](active-directory-devquickstarts-xamarin.md)
- [Cordova](active-directory-devquickstarts-cordova.md)


####Guias de início rápido para aplicativos Web ou API da Web

- [Aplicativo Web .NET](active-directory-devquickstarts-webapp-dotnet.md)
- [API da Web .NET](active-directory-devquickstarts-webapi-dotnet.md)
- [Javascript](active-directory-devquickstarts-angular.md)
- [Node.js](active-directory-devquickstarts-webapi-nodejs.md)


## Instruções

Esses artigos descrevem como executar tarefas específicas usando o Azure Active Directory (AD).

- [Como obter um locatário do Azure AD](active-directory-howto-tenant.md)
- [Como listar seu aplicativo na galeria de aplicativos do Azure Active Directory](active-directory-app-gallery-listing.md)
- [Introdução às APIs do Office 365 em aplicativos](https://msdn.microsoft.com/office/office365/howto/getting-started-Office-365-APIs)


## Referência

Esses artigos fornecem referências básicas para APIs REST e de biblioteca de autenticação, protocolos, erros, exemplos de código e pontos de extremidade.

####  Suporte
- **[Onde obter suporte](http://stackoverflow.com/questions/tagged/azure-active-directory)**: localize soluções do Azure AD no estouro de pilha procurando pelas guias [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) e [adal](http://stackoverflow.com/questions/tagged/adal).

#### Código

- **[Bibliotecas de código livre do Azure AD](http://github.com/AzureAD)**: a maneira mais fácil de localizar o código-fonte de uma biblioteca é usando nossa [lista de bibliotecas](https://msdn.microsoft.com/library/azure/dn151135.aspx).

- **[Exemplos do Azure AD](http://github.com/AzureADSamples)**: A maneira mais fácil para navegar pela lista de exemplos é usando o [índice de exemplos de código](active-directory-code-samples.md).


#### API gráfica

- **[Referência de Graph API](https://msdn.microsoft.com/library/azure/hh974476.aspx)**: referência REST para a Graph API do Azure Active Directory. [Exiba a nova experiência de referência de Graph API interativa](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- **[Escopos de permissão de Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/graph-api-permission-scopes)**: os escopos de permissão do OAuth 2.0 que são usados para controlar o acesso que um aplicativo tem aos dados de diretório em um locatário.


#### Protocolos de autenticação

- **[Referência de protocolo do SAML 2.0](https://msdn.microsoft.com/library/azure/dn195591.aspx)**: o protocolo do SAML 2.0 permite que os aplicativos forneçam uma experiência de logon único para seus usuários.


- **[Referência de protocolo do OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx)**: o protocolo do OAuth 2.0 permite que você autorize o acesso a aplicativos Web e APIs da Web em seu locatário do Azure AD.


- **[Referência de protocolo do OpenID Connect 1.0](https://msdn.microsoft.com/library/azure/dn645541.aspx)**: o protocolo do OpenID Connect 1.0 estende o OAuth 2.0 para uso como um protocolo de autenticação.


- **[Referência de protocolo do WS-Federation 1.2](https://msdn.microsoft.com/library/azure/dn903702.aspx)**: o protocolo do WS-Federation 1.2, conforme especificado na especificação do Web Services Federation versão 1.2.

- **[Declarações e tokens de segurança suportados](active-directory-token-and-claims.md)**: um guia para compreender e avaliar as declarações nos tokens SAML 2.0 e JSON Web Tokens (JWT).

#### Vídeos

[Azure Friday](http://azure.microsoft.com/documentation/videos/azure-friday/) é uma série de vídeos introdutórios recorrente dedicada a oferece a você entrevistas curtas de 10 a 15 minutos com especialistas em uma variedade de tópicos do Azure. Use o recurso de filtro de serviços na página para ver todos os vídeos do Azure Active Directory.

- **[Identidade do Azure 101](http://azure.microsoft.com/documentation/videos/azure-identity-basics/)**
- **[Identidade do Azure 102](http://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)**
- **[Identidade do Azure 103](http://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)**

## Redes sociais

- **[Blog da equipe do Active Directory](http://blogs.technet.com/b/ad/)**: mantenha-se atualizado quanto aos desenvolvimentos mais recentes no mundo do Azure AD.

- **[Blog de Graph do Azure](http://blogs.msdn.com/b/aadgraphteam)**: informações do Azure AD específicas à Graph API.

- **[Identidade em nuvem](http://www.cloudidentity.net)**: considerações sobre gerenciamento de identidades como um serviço de um PM de princípio do Azure Active Directory.

- **[Azure AD no Twitter](https://twitter.com/azuread)**: anúncios do Azure AD em 140 caracteres ou menos.

<!---HONumber=58-->