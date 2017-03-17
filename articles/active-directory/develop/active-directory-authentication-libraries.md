---
title: "Bibliotecas de Autenticação do Azure Active Directory | Microsoft Docs"
description: "A Biblioteca (ADAL) de Autenticação do AD do Azure permite que os desenvolvedores de aplicativo cliente autentiquem facilmente os usuários no Active Directory (AD) local ou em nuvem e daí obtenham tokens de acesso para proteger chamadas de API."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: mbaldwin
ms.assetid: 2e4fc79a-0285-40be-8c77-65edee408a22
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/13/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: b4bfd0288f611ba1f5f1c30d402614d34c473457
ms.lasthandoff: 03/15/2017


---
# <a name="azure-active-directory-authentication-libraries"></a>Bibliotecas de Autenticação do Active Directory do Azure
A Biblioteca de autenticação do AD do Azure (ADAL) permite que os desenvolvedores de aplicativo cliente autentiquem facilmente os usuários no Active Directory (AD) local ou em nuvem e daí obtenham tokens de acesso para proteger chamadas de API. A ADAL tem muitos recursos que tornam a autenticação mais fácil para os desenvolvedores, como o suporte assíncrono, um cache de token configurável que armazena os tokens de acesso e de atualização, atualização automática de token quando um token de acesso expira e um token de atualização está disponível, e muito mais. Controlando a maior parte da complexidade, a ADAL pode ajudar o desenvolvedor a se concentrar na lógica de negócios em seus aplicativos e a proteger facilmente os recursos sem ser um especialista em segurança.

A ADAL está disponível em uma variedade de plataformas.

### <a name="client-libraries"></a>Bibliotecas de cliente

| Plataforma | Biblioteca | Baixar | Código-fonte | Amostra | Referência
| --- | --- | --- | --- | --- | --- |
| Cliente .NET, Windows Store, UWP, Xamarin iOS e Android |ADAL .NET v3 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) | [Aplicativo de área de trabalho](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-dotnet) |[Referência](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory) | 
| Cliente .NET, Windows Store, Windows Phone 8.1 |ADAL .NET v2 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.2) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/releases/tag/v2.28.2) | [Aplicativo de área de trabalho](https://github.com/AzureADQuickStarts/NativeClient-DotNet/releases/tag/v2.X) |[Referência](https://docs.microsoft.com/en-us/active-directory/adal//v2/microsoft.identitymodel.clients.activedirectory) | 
| JavaScript |ADAL.js |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[Aplicativo de Página Única](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | |
| iOS, macOS |ADAL |[CocoaPods](http://cocoadocs.org/docsets/ADAL/) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |[Aplicativo iOS](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-ios) | [Referência](http://cocoadocs.org/docsets/ADAL/)|
| Android |ADAL |[O Repositório Central](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-android) |[Aplicativo Android](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-android) | [JavaDocs](http://javadoc.io/doc/com.microsoft.aad/adal/)|
| Node.js |ADAL |[npm](https://www.npmjs.com/package/adal-node) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) | | |
| Java |ADAL4J |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Github](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[Aplicativo Web Java](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-webapp-java) | |

### <a name="server-libraries"></a>Bibliotecas do servidor 

| Plataforma | Biblioteca | Baixar | Código-fonte | Amostra | Referência
| --- | --- | --- | --- | --- | --- |
| .NET |OWIN para AzureAD|[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[CodePlex](http://katanaproject.codeplex.com) |[Aplicativo MVC](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-webapp-dotnet) | |
| .NET |OWIN para OpenIDConnect |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[CodePlex](http://katanaproject.codeplex.com) |[Aplicativo Web](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) | |
| Node.js |Azure AD Passport |[npm](https://www.npmjs.com/package/passport-azure-ad) |[Github](https://github.com/AzureAD/passport-azure-ad) | [API da Web](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-devquickstarts-webapi-nodejs)| |
| .NET |OWIN para WS-Federation |[NuGet](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[CodePlex](http://katanaproject.codeplex.com) |[Aplicativo Web MVC](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) | |
| .NET |Extensões do protocolo de identidade para .NET 4.5 |[NuGet](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[Github](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |
| .NET |Manipulador JWT para .NET 4.5 |[NuGet](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[Github](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | | |



## <a name="scenarios"></a>Cenários
Aqui estão os três cenários comuns nos quais a ADAL pode ser usada para autenticação.  

### <a name="authenticating-users-of-a-client-application-to-a-remote-resource"></a>Autenticação de Usuários de um Aplicativo Cliente para um Recurso Remoto
Nesse cenário, um desenvolvedor tem um cliente, como um aplicativo WPF, que precisa acessar um recurso remoto protegido pelo AD do Azure, como uma API da Web. Ele tem uma assinatura do Azure, sabe como chamar a API da Web downstream e conhece o locatário do AD do Azure que usa a API da Web. Como resultado, ele pode usar a ADAL para facilitar a autenticação com o AD do Azure, delegando completamente a experiência de autenticação para a ADAL ou tratando explicitamente as credenciais do usuário. A ADAL facilita autenticar o usuário, obter um token de acesso e um token de atualização do AD do Azure, e então, usar o token de acesso para fazer solicitações para a API da Web.

Para um exemplo de código que demonstra esse cenário usando a autenticação do AD do Azure, consulte [Aplicativo WPF de cliente nativo para API Web](https://github.com/azureadsamples/nativeclient-dotnet).

### <a name="authenticating-a-server-application-to-a-remote-resource"></a>Autenticação de um Aplicativo Servidor para um Recurso Remoto
Nesse cenário, um desenvolvedor tem um aplicativo em execução num servidor que precisa acessar um recurso remoto protegido pelo AD do Azure, como uma API da Web. Ele tem uma assinatura do Azure, sabe como chamar o serviço downstream e conhece o locatário do AD do Azure que a API da Web usa. Como resultado, ele pode usar a ADAL para facilitar a autenticação com o AD do Azure tratando explicitamente as credenciais do aplicativo. A ADAL facilita recuperar um token do AD do Azure usando a credencial do cliente do aplicativo e, em seguida, usar esse token para fazer solicitações para a API da Web. A ADAL também controla o gerenciamento da vida útil do token de acesso armazenando em cache e renovando-o conforme necessário. Para um exemplo de código que demonstra este cenário, consulte [Aplicativo de Console para API da Web](https://github.com/AzureADSamples/Daemon-DotNet).

### <a name="authenticating-a-server-application-on-behalf-of-a-user-to-access-a-remote-resource"></a>Autenticar um Aplicativo de Servidor em Nome de um Usuário para Acessar um Recurso Remoto
Nesse cenário, um desenvolvedor tem um aplicativo em execução num servidor que precisa acessar um recurso remoto protegido pelo AD do Azure, como uma API da Web. A solicitação também precisa ser feita em nome de um usuário no AD do Azure. Ele tem uma assinatura do Azure, sabe como chamar a API da Web downstream e conhece o locatário do AD do Azure que o serviço usa. Quando o usuário é autenticado para o aplicativo Web, o aplicativo pode obter um código de autorização para o usuário do AD do Azure. O aplicativo Web pode então usar a ADAL para obter um token de acesso e atualizar o token em nome de um usuário usando o código de autorização e as credenciais de cliente associadas ao aplicativo do AD do Azure. Depois que o aplicativo Web estiver em posse do token de acesso, ele pode chamar a API da Web até o token expirar. Quando o token expirar, o aplicativo Web pode usar a ADAL para obter um novo token de acesso usando o token de atualização recebido anteriormente.

## <a name="see-also"></a>Consulte também
[Guia do desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md)

[Cenários de autenticação do Active Directory do Azure](active-directory-authentication-scenarios.md)

[Exemplos de código do Active Directory do Azure](active-directory-code-samples.md)

