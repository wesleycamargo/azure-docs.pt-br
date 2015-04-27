<properties 
	pageTitle="" 
	description="Descreve o que foi alterado no projeto do Visual Studio depois que você executou o assistente do Active Directory do Azure" 
	services="active-directory" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>
 
<tags 
	ms.service="active-directory" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

# O que aconteceu com meu projeto?

> [AZURE.SELECTOR]
> - [Introdução](vs-active-directory-webapi-getting-started.md)
> - [O que aconteceu](vs-active-directory-webapi-what-happened.md)

###<span id="whathappened">O que aconteceu com meu projeto?</span>

Referências foram adicionadas. 

#####Referências do pacote NuGet 

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Newtonsoft.Json`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

#####Referências .NET 

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Newtonsoft.Json`
- `Owin`
- `System.IdentityModel.Tokens.Jwt` 

#####Arquivos de código foram adicionados ao seu projeto 

Uma classe de inicialização de autenticação, a **App_Start/Startup.Auth.cs** foi adicionada ao seu projeto contendo lógica de inicialização para a autenticação do Azure AD. 

#####O código de inicialização foi adicionado ao seu projeto 

Se você já tiver uma classe de Inicialização em seu projeto, o método **Configuração** terá sido atualizado para incluir uma chamada para  `ConfigureAuth(app)`, que foi incluída nesse método.  Caso contrário, uma classe Startup foi adicionada ao projeto. 


#####Seu arquivo app.config ou web.config possui novos valores de configuração.

As entradas de configuração a seguir foram adicionadas. 
	<pre>
	`<appSettings> 
    		<add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
    		<add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
    		<add key="ida:Audience" value="The App ID Uri from the wizard" /> 
	</appSettings>` </pre>

###Um aplicativo do Azure AD foi criado 

Um Aplicativo Azure AD foi criado no diretório selecionado no assistente.


[Saiba mais sobre o Active Directory do Azure](http://azure.microsoft.com/services/active-directory/)

<!--HONumber=49-->