---
title: Guia do desenvolvedor do Azure Active Directory | Microsoft Docs
description: Este artigo fornece um guia abrangente para recursos para desenvolvedores do Active Directory do Azure.
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/09/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 0500d903438e0fb062ca848e6f488860e214a705
ms.openlocfilehash: 55b6f6e03bfd1877d4f204cb5751ae46379d6951


---
# <a name="azure-active-directory-developers-guide"></a>Guia do desenvolvedor do Active Directory do Azure
## <a name="overview"></a>Visão geral
Como uma plataforma IDMaaS (Gerenciamento de Identidade como um Serviço), o Azure Active Directory (AD) fornece aos desenvolvedores uma maneira eficiente de integrar o gerenciamento de identidades em seus aplicativos. Os artigos a seguir fornecem visões gerais sobre a implementação e os principais recursos do Azure AD. Sugerimos que você os leia na ordem ou pule para a [Introdução](#getting-started) se estiver pronto para se aprofundar.

1. [Os benefícios da integração do Azure AD](develop/active-directory-how-to-integrate.md): descubra por que a integração com o Azure AD oferece a melhor solução para entrada segura e autorização.
2. [Cenários de autenticação do AD](active-directory-authentication-scenarios.md): aproveite a autenticação simplificada do Azure AD para fornecer logon em seu aplicativo.
3. [Integrando aplicativos ao Azure AD](active-directory-integrating-applications.md): saiba mais sobre como adicionar, atualizar e remover aplicativos do Azure AD e sobre as diretrizes de identidade visual para aplicativos integrados.
4. [API do Graph do Azure AD](active-directory-graph-api.md): use a API do Graph do Azure AD para acessar de forma programática o Azure AD por meio de pontos de extremidade da API REST. A API do Graph do Azure AD também está acessível por meio do [Microsoft Graph](https://graph.microsoft.io/). O Microsoft Graph fornece uma API unificada que habilita o acesso a várias APIs de serviço de nuvem da Microsoft, por meio de um único ponto de extremidade de API REST e com um único token de acesso.
5. [Bibliotecas de autenticação do Azure AD](active-directory-authentication-libraries.md): autentique facilmente os usuários para obter tokens de acesso usando as bibliotecas de autenticação do Azure AD para .NET, JavaScript, Objective-C, Android e mais.

## <a name="getting-started"></a>Introdução
Esses tutoriais são adaptados para várias plataformas e permitem que você comece a desenvolver rapidamente com o Active Directory do Azure. Como um pré-requisito, você deve [obter um locatário do Active Directory do Azure](active-directory-howto-tenant.md).

### <a name="mobile-and-pc-application-quick-start-guides"></a>Guias de início rápido para aplicativo móvel ou PC
| [![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md) | [![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md) | [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md) | [![Windows Universal](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md) | [![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md) | [![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md) | [![OAuth 2.0](./media/active-directory-developers-guide/oauth-2.png)](active-directory-protocols-oauth-code.md) |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| [iOS](active-directory-devquickstarts-ios.md) |[Android](active-directory-devquickstarts-android.md) |[.NET](active-directory-devquickstarts-dotnet.md) |[Windows Universal](active-directory-devquickstarts-windowsstore.md) |[Xamarin](active-directory-devquickstarts-xamarin.md) |[Cordova](active-directory-devquickstarts-cordova.md) |[Integrar diretamente a OAuth 2.0](active-directory-protocols-oauth-code.md) |

### <a name="web-application-quick-start-guides"></a>Guias de início rápido de aplicativo Web
| [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md) | [![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md) | [![AngularJS](./media/active-directory-developers-guide/angularjs.png)](active-directory-devquickstarts-angular.md) | [![Javascript](./media/active-directory-developers-guide/javascript.png)](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | [![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md) | [![OpenID Connect](./media/active-directory-developers-guide/openid-connect.png)](active-directory-protocols-openid-connect-code.md) |
|:---:|:---:|:---:|:---:|:---:|:---:|
| [.NET](active-directory-devquickstarts-webapp-dotnet.md) |[Java](active-directory-devquickstarts-webapp-java.md) |[AngularJS](active-directory-devquickstarts-angular.md) |[Javascript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md) |[Integrar diretamente a OpenID Connect](active-directory-protocols-openid-connect-code.md) |

### <a name="web-api-quick-start-guides"></a>Guias de início rápido de API Web
| [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md) | [![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md) |
|:---:|:---:|
| [.NET](active-directory-devquickstarts-webapi-dotnet.md) |[Node.js](active-directory-devquickstarts-webapi-nodejs.md) |

### <a name="querying-the-directory-quickstart-guide"></a>Consultando o guia de início rápido do diretório
| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md) |
|:---:|
| [API gráfica](active-directory-graph-api-quickstart.md) |

## <a name="how-tos"></a>Instruções
Esses artigos descrevem como executar tarefas específicas usando o Active Directory do Azure.

* [Obter um locatário do AD do Azure](active-directory-howto-tenant.md)
* [Entrar em qualquer usuário do Azure AD usando o padrão de aplicativo multilocatário](active-directory-devhowto-multi-tenant-overview.md)
* [Autenticar um aplicativo de serviço/daemon usando um certificado](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/)
* Habilitar o SSO entre aplicativos usando a ADAL em dispositivos [Android](active-directory-sso-android.md) e [iOS](active-directory-sso-ios.md)
* [Tornar seu aplicativo Certificado por AppSource para o Azure AD](active-directory-devhowto-appsource-certified.md)
* [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](active-directory-app-gallery-listing.md)
* [Enviar aplicativos Web para Office 365 para o Painel do Vendedor](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
* [Registrar um aplicativo no Active Directory do Azure usando o portal do Azure](active-directory-app-registration.md)
* [Noções básicas sobre o manifesto do aplicativo do Active Directory do Azure](active-directory-application-manifest.md)
* [Entender as diretrizes de identidade visual para os botões de aquisição de entrada e de aplicativo em seu aplicativo cliente](active-directory-branding-guidelines.md)
* [Visualização: como criar aplicativos que conectam usuários com as contas pessoais e corporativas ou de estudante](active-directory-appmodel-v2-overview.md)
* [Visualização: como criar aplicativos que registram e conectam consumidores](../active-directory-b2c/active-directory-b2c-overview.md)
* [Visualização: configurar tempos de vida de token no Azure AD](active-directory-configurable-token-lifetimes.md) usando o PowerShell. Consulte [Operações de política](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) e [Entidade de política](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity) para obter detalhes sobre como configurar por meio da API do Graph do Azure AD.

## <a name="reference"></a>Referência
Esses artigos fornecem referências básicas para REST e biblioteca de autenticação APIs, protocolos, erros, exemplos de código e pontos de extremidade.  

### <a name="support"></a>Suporte
* [Perguntas marcadas](http://stackoverflow.com/questions/tagged/azure-active-directory): encontre soluções do Azure Active Directory no Stack Overflow pesquisando as marcas [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) e [adal](http://stackoverflow.com/questions/tagged/adal).
* Confira o [Glossário de desenvolvedor do Azure AD](active-directory-dev-glossary.md) para obter definições de alguns dos termos comumente usados relacionados ao desenvolvimento e à integração de aplicativos.

### <a name="code"></a>Código
* [Bibliotecas de código aberto do Azure Active Directory](http://github.com/AzureAD): a maneira mais fácil de encontrar o código de uma biblioteca é usando nossa [lista de bibliotecas](active-directory-authentication-libraries.md).
* [Exemplos do Azure Active Directory](https://github.com/azure-samples?query=active-directory): a maneira mais fácil de navegar pela lista de exemplos é usando o [índice de exemplos de código](active-directory-code-samples.md).
* [Biblioteca de Autenticação do Active Directory (ADAL) para .NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) - A documentação de referência está disponível para [a versão principal mais recente](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory) e para [a versão principal anterior](https://docs.microsoft.com/active-directory/adal/v2/microsoft.identitymodel.clients.activedirectory).

### <a name="graph-api"></a>Graph API
* [Referência da Graph API](https://msdn.microsoft.com/library/azure/hh974476.aspx): referência REST para a Graph API do Active Directory do Azure. [Exiba a experiência de referência da Graph API interativa](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* [Escopos de permissão da Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes): os escopos de permissão do OAuth 2.0 usados para controlar o acesso que um aplicativo tem aos dados do diretório em um locatário.

### <a name="authentication-and-authorization-protocols"></a>Protocolos de autenticação e autorização
* [Substituição de Chave de Assinatura no Azure AD](active-directory-signing-key-rollover.md): saiba mais sobre a cadência de substituição de chave de assinatura do Azure AD e como atualizar a chave para os cenários de aplicativo mais comuns.
* [Protocolo OAuth 2.0: usando a concessão de código de autorização](active-directory-protocols-oauth-code.md): você pode usar a concessão de código de autorização do protocolo OAuth 2.0 para autorizar o acesso a aplicativos Web e APIs Web no Azure Active Directory de seu locatário.
* [Protocolo OAuth 2.0: noções básicas sobre a concessão implícita](active-directory-dev-understanding-oauth2-implicit-grant.md): saiba mais sobre a concessão de autorização implícita e se ela é adequada para seu aplicativo.
* [Protocolo OAuth 2.0: chamadas de serviço para serviço usando credenciais de cliente](active-directory-protocols-oauth-service-to-service.md): a concessão de Credenciais do Cliente do OAuth 2.0 permite que um serviço Web (um cliente confidencial) use suas próprias credenciais para se autenticar ao chamar outro serviço Web, em vez de representar um usuário. Nesse cenário, o cliente é geralmente um serviço Web de camada intermediária, um serviço daemon ou um site.
* [Protocolo OpenID Connect 1.0: entrada e autenticação](active-directory-protocols-openid-connect-code.md): o protocolo OpenID Connect 1.0 estende o OAuth 2.0 para uso como um protocolo de autenticação. Um aplicativo cliente pode receber um id_token para gerenciar o processo de entrada ou aumentar o fluxo de código de autorização de modo a receber um id_token e o código de autorização.
* [Referência do protocolo SAML 2.0](active-directory-saml-protocol-reference.md): o protocolo SAML 2.0 permite que os aplicativos forneçam uma experiência de logon único aos usuários.
* [Protocolo WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html): o Azure Active Directory dá suporte ao WS-Federation 1.2, de acordo com a Especificação Web Services Federation Versão 1.2. Para saber mais sobre o documento de metadados federados, confira [Metadados de Federação](active-directory-federation-metadata.md).
* [Tipos de token e declaração com suporte](active-directory-token-and-claims.md): você pode usar este guia para entender e avaliar as declarações nos tokens SAML 2.0 e JWT (Tokens Web JSON).

## <a name="videos"></a>Vídeos
### <a name="build"></a>Compilação
Essas apresentações de visão geral sobre o desenvolvimento de aplicativos usando o Active Directory do Azure apresentam alto-falantes que trabalham diretamente na equipe de engenharia. As apresentações cobrem tópicos fundamentais, incluindo IDMaaS, autenticação, federação de identidade e logon único.

* [Identidade da Microsoft: Estado da União e direção futura](https://azure.microsoft.com/documentation/videos/build-2016-microsoft-identity-state-of-the-union-and-future-direction/)
* [Active Directory do Azure: Gerenciamento de identidade como um serviço para aplicativos modernos](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
* [Desenvolva aplicativos Web modernos com o Active Directory do Azure](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
* [Desenvolva aplicativos nativos modernos com o Active Directory do Azure](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### <a name="azure-friday"></a>Azure Friday
[Azure Friday](https://azure.microsoft.com/documentation/videos/azure-friday/) é uma série de vídeos introdutórios periódicos que tem como objetivo oferecer entrevistas breves (10 a 15 minutos) com especialistas sobre uma variedade de tópicos do Azure.  Use o recurso de filtro de serviços na página para ver todos os vídeos do Azure Active Directory.

* [Identidade do Azure 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
* [Identidade do Azure 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
* [Identidade do Azure 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## <a name="social"></a>Redes sociais
* [Blog da equipe do Active Directory](http://blogs.technet.com/b/ad/): os desenvolvimentos mais recentes no mundo do Active Directory do Azure.
* [Blog da equipe da Graph do Azure Active Directory](http://blogs.msdn.com/b/aadgraphteam): informações do Active Directory do Azure específicas à Graph API.
* [Identidade de nuvem](http://www.cloudidentity.net): considerações sobre o gerenciamento de identidades como serviço de um PM principal do Azure Active Directory de entidade de segurança.  
* [Active Directory do Azure no Twitter](https://twitter.com/azuread): comunicados do Active Directory do Azure em 140 caracteres ou menos.

## <a name="windows-server-on-premises-development"></a>Desenvolvimento do Windows Server no local
Para obter orientações sobre como usar o desenvolvimento do Windows Server e do ADFS (Serviços de Federação do Active Directory), confira:

* [Cenários do ADFS para desenvolvedores](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/overview/ad-fs-scenarios-for-developers): fornece uma visão geral dos componentes do ADFS e como ele funciona, com detalhes sobre os cenários de autenticação/autorização com suporte.
* [Explicações passo a passo do ADFS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/ad-fs-development): uma lista de artigos de orientação que fornecem instruções passo a passo sobre como implementar os fluxos de autenticação/autorização relacionados.



<!--HONumber=Dec16_HO2-->


