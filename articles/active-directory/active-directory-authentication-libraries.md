---
title: Bibliotecas de Autenticação do Active Directory do Azure | Microsoft Docs
description: A Biblioteca (ADAL) de Autenticação do AD do Azure permite que os desenvolvedores de aplicativo cliente autentiquem facilmente os usuários no Active Directory (AD) local ou em nuvem e daí obtenham tokens de acesso para proteger chamadas de API.
services: active-directory
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: mbaldwin

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2016
ms.author: mbaldwin

---
# Bibliotecas de Autenticação do Active Directory do Azure
A Biblioteca de autenticação do AD do Azure (ADAL) permite que os desenvolvedores de aplicativo cliente autentiquem facilmente os usuários no Active Directory (AD) local ou em nuvem e daí obtenham tokens de acesso para proteger chamadas de API. A ADAL tem muitos recursos que tornam a autenticação mais fácil para os desenvolvedores, como o suporte assíncrono, um cache de token configurável que armazena os tokens de acesso e de atualização, atualização automática de token quando um token de acesso expira e um token de atualização está disponível, e muito mais. Controlando a maior parte da complexidade, a ADAL pode ajudar o desenvolvedor a se concentrar na lógica de negócios em seus aplicativos e a proteger facilmente os recursos sem ser um especialista em segurança.

A ADAL está disponível em uma variedade de plataformas.

| Plataforma | Nome do Pacote | Cliente/Servidor | Baixar | Código-fonte | Documentação e exemplos |
| --- | --- | --- | --- | --- | --- |
| Cliente .NET, Windows Store, Windows Phone (8.1) |Biblioteca de Autenticação do Active Directory (ADAL) para .NET |Cliente |[Microsoft.IdentityModel.Clients.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) |[ADAL for .NET (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) |[Documentação](https://msdn.microsoft.com/library/azure/mt417579.aspx) |
| JavaScript |Biblioteca de Autenticação do Active Directory (ADAL) para JavaScript |Cliente |[ADAL for JavaScript (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js) |[ADAL for JavaScript (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js) |Exemplo: [SinglePageApp-DotNet (Github)](https://github.com/AzureADSamples/SinglePageApp-DotNet) |
| OS X, iOS |Biblioteca de Autenticação do Active Directory (ADAL) para Objective-C |Cliente |[ADAL para Objective-C (CocoaPods)](https://cocoapods.org/?q=adal%20io) |[ADAL para Objective-C (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-objc) |Exemplo: [NativeClient-iOS (Github)](https://github.com/AzureADSamples/NativeClient-iOS) |
| Android |Biblioteca de Autenticação do Active Directory (ADAL) para Android |Cliente |[ADAL para Android (O Repositório Central)](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/) |[ADAL for Android (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-android) |Exemplo: [NativeClient-Android (Github)](https://github.com/AzureADSamples/NativeClient-Android) |
| Node.js |Biblioteca de Autenticação do Active Directory (ADAL) para for Node.js |Cliente |[ADAL para Node. js (npm)](https://www.npmjs.com/package/adal-node) |[ADAL para Node. js (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs) |Exemplo: [WebAPI-Nodejs (Github)](https://github.com/AzureADSamples/WebAPI-Nodejs) |
| Node.js |Middleware de autenticação do Passport do Active Directory do Microsoft Azure para Nó |Cliente |[Passport do Active Directory do Azure para Node.js (npm)](https://www.npmjs.com/package/passport-azure-ad) |[Active Directory do Azure para Node.js (Github)](https://github.com/AzureAD/passport-azure-ad) | |
| Java |Biblioteca de Autenticação do Active Directory (ADAL) para Java |Cliente |[ADAL para Java (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java) |[ADAL para Java (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java) | |
| .NET |Extensões do Protocolo de Identidade para o Microsoft .NET Framework 4.5 |Servidor |[Microsoft.IdentityModel.Protocol.Extensions (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions) |[Extensões do modelo de identidade do AD do Azure para .NET (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | |
| .NET |JSON Web Token Handler para o Microsoft .Net Framework 4.5 |Servidor |[System.IdentityModel.Tokens.Jwt (NuGet)](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt) |[Extensões do modelo de identidade do AD do Azure para .NET (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | |
| .NET |O OWIN middleware que permite que um aplicativo use a tecnologia da Microsoft para autenticação. |Servidor |[Microsoft.Owin.Security.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/) |[OWIN (CodePlex)](http://katanaproject.codeplex.com) | |
| .NET |O OWIN middleware que permite que um aplicativo use o OpenIDConnect para autenticação. |Servidor |[Microsoft.Owin.Security.OpenIdConnect (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect) |[OWIN (CodePlex)](http://katanaproject.codeplex.com) |Exemplo: [WebApp-OpenIDConnecty-DotNet (Github)](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet) |
| .NET |O OWIN middleware que permite que um aplicativo use a WS-Federation para autenticação. |Servidor |[Microsoft.Owin.Security.WsFederation (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) |[OWIN (CodePlex)](http://katanaproject.codeplex.com) |Exemplo: [WebApp-WSFederation-DotNet (Github)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet) |

## Cenários
Aqui estão os três cenários comuns nos quais a ADAL pode ser usada para autenticação.

### Autenticação de Usuários de um Aplicativo Cliente para um Recurso Remoto
Nesse cenário, um desenvolvedor tem um cliente, como um aplicativo WPF, que precisa acessar um recurso remoto protegido pelo AD do Azure, como uma API da Web. Ele tem uma assinatura do Azure, sabe como chamar a API da Web downstream e conhece o locatário do AD do Azure que usa a API da Web. Como resultado, ele pode usar a ADAL para facilitar a autenticação com o AD do Azure, delegando completamente a experiência de autenticação para a ADAL ou tratando explicitamente as credenciais do usuário. A ADAL facilita autenticar o usuário, obter um token de acesso e um token de atualização do AD do Azure, e então, usar o token de acesso para fazer solicitações para a API da Web.

Para um exemplo de código que demonstra esse cenário usando a autenticação do AD do Azure, consulte [Aplicativo WPF de cliente nativo para API Web](https://github.com/azureadsamples/nativeclient-dotnet).

### Autenticação de um Aplicativo Servidor para um Recurso Remoto
Nesse cenário, um desenvolvedor tem um aplicativo em execução num servidor que precisa acessar um recurso remoto protegido pelo AD do Azure, como uma API da Web. Ele tem uma assinatura do Azure, sabe como chamar o serviço downstream e conhece o locatário do AD do Azure que a API da Web usa. Como resultado, ele pode usar a ADAL para facilitar a autenticação com o AD do Azure tratando explicitamente as credenciais do aplicativo. A ADAL facilita recuperar um token do AD do Azure usando a credencial do cliente do aplicativo e, em seguida, usar esse token para fazer solicitações para a API da Web. A ADAL também controla o gerenciamento da vida útil do token de acesso armazenando em cache e renovando-o conforme necessário. Para um exemplo de código que demonstra este cenário, consulte [Aplicativo de Console para API da Web](https://github.com/AzureADSamples/Daemon-DotNet).

### Autenticar um Aplicativo de Servidor em Nome de um Usuário para Acessar um Recurso Remoto
Nesse cenário, um desenvolvedor tem um aplicativo em execução num servidor que precisa acessar um recurso remoto protegido pelo AD do Azure, como uma API da Web. A solicitação também precisa ser feita em nome de um usuário no AD do Azure. Ele tem uma assinatura do Azure, sabe como chamar a API da Web downstream e conhece o locatário do AD do Azure que o serviço usa. Quando o usuário é autenticado para o aplicativo Web, o aplicativo pode obter um código de autorização para o usuário do AD do Azure. O aplicativo Web pode então usar a ADAL para obter um token de acesso e atualizar o token em nome de um usuário usando o código de autorização e as credenciais de cliente associadas ao aplicativo do AD do Azure. Depois que o aplicativo Web estiver em posse do token de acesso, ele pode chamar a API da Web até o token expirar. Quando o token expirar, o aplicativo Web pode usar a ADAL para obter um novo token de acesso usando o token de atualização recebido anteriormente.

## Consulte também
[Guia do desenvolvedor do Active Directory do Azure ](active-directory-developers-guide.md)

[Cenários de autenticação do Active Directory do Azure](active-directory-authentication-scenarios.md)

[Exemplos de código do Active Directory do Azure](active-directory-code-samples.md)

<!---HONumber=AcomDC_0921_2016-->