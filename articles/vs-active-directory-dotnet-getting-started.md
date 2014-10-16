<properties title="Getting Started with Active Directory Authentication" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

### O que aconteceu?

Referências foram adicionadas ao seu projeto

###### Referências do pacote NuGet

Microsoft.IdentityModel.Protocol.Extensions, Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.Cookies, Microsoft.Owin.Security.OpenIdConnect, Owin e System.IdentityModel.Tokens.Jwt

###### Referências .NET

Microsoft.IdentityModel.Protocol.Extensions, Microsoft.Owin, Microsoft.Owin.Host.SystemWeb, Microsoft.Owin.Security, Microsoft.Owin.Security.Cookies, Microsoft.Owin.Security.OpenIdConnect, Owin, System, System.Data, System.Drawing, System.IdentityModel, System.IdentityModel.Tokens.Jwt e System.Runtime.Serialization

###### Arquivos de código foram adicionados ao seu projeto

Uma classe de inicialização de autenticação, a App\_Start/Startup.Auth.cs, foi adicionada ao seu projeto contendo lógica de inicialização para autenticação do Azure AD. Além disso, uma classe de controlador, a Controllers/AccountController.cs, foi adicionada, contendo os métodos SignIn() e SignOut(). Por fim, uma visualização parcial, a Views/Shared/\_LoginPartial.cshtml, foi adicionada, contendo um link de ação para SignIn/SignOut.

###### O código de inicialização foi adicionado ao seu projeto

Se você já possuía uma classe Startup no projeto, o método Configuration() foi atualizado para incluir uma chamada para ConfigureAuth(app) e adicionado ao método. Caso contrário, uma classe Startup foi adicionada ao projeto.

###### Seu app.config ou web.config possui novos valores de configuração

As entradas de configuração a seguir foram adicionadas.

	<pre>
	`<appSettings>
	    <add key="ida:ClientId" value="ClientId from the new Azure AD App" /> 
	    <add key="ida:Tenant" value="Your selected Azure AD Tenant" /> 
	    <add key="ida:AADInstance" value="https://login.windows.net/{0}" /> 
	    <add key="Ida:PostLogoutRedirectURI" value="Your project start page" /> 
	</appSettings>` </pre>

###### Foi criado um aplicativo do Azure Active Directory (AD)

Um Aplicativo Azure AD foi criado no diretório selecionado no assistente.

## Introdução ao Azure Active Directory (AD)

Veja o que você pode fazer com o código que foi adicionado.

###### Exigir autenticação para acessar os controladores

Todos os controladores do seu projeto receberam o atributo [Authorize]. Este atributo exigirá que o usuário seja autenticado antes de acessar esses controladores. Para permitir que o controlador seja acessado anonimamente, remova este atributo do controlador.

###### Adicionar controles de SignIn / SignOut

Para adicionar os controles de SignIn/SignOut à sua visualização, use a visualização parcial \_LoginPartial.cshtml para adicionar a funcionalidade a uma de suas visualizações. Veja um exemplo da funcionalidade adicionada à visualização standard \_Layout.cshtml:

	<pre> 
	`<!DOCTYPE html> 
	<html> 
	<head> 
	    <meta charset="utf-8" /> 
	    <meta name="viewport" content="width=device-width, initial-scale=1.0"> 
	    <title>@ViewBag.Title - My ASP.NET Application</title> 
	    @Styles.Render("~/Content/css") 
	    @Scripts.Render("~/bundles/modernizr") 
	</head> 
	<body> 
	    <div class="navbar navbar-inverse navbar-fixed-top"> 
	        <div class="container"> 
	            <div class="navbar-header"> 
	                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"> 
	                    <span class="icon-bar"></span> 
	                    <span class="icon-bar"></span> 
	                    <span class="icon-bar"></span> 
	                </button> 
	                @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" }) 
	            </div> 
	            <div class="navbar-collapse collapse"> 
	                <ul class="nav navbar-nav"> 
	                    <li>@Html.ActionLink("Home", "Index", "Home")</li> 
	                    <li>@Html.ActionLink("About", "About", "Home")</li> 
	                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li> 
	                </ul> 
	                @Html.Partial("_LoginPartial") 
	            </div> 
	        </div> 
	    </div> 
	    <div class="container body-content"> 
	        @RenderBody() 
	        <hr /> 
	        <footer> 
	            <p>&copy; @DateTime.Now.Year - My ASP.NET Application</p> 
	        </footer> 
	    </div> 
	    @Scripts.Render("~/bundles/jquery") 
	    @Scripts.Render("~/bundles/bootstrap") 
	    @RenderSection("scripts", required: false) 
	</body> 
	</html>` </pre>

