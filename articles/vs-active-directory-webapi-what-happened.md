<properties title="Introdu&ccedil;&atilde;o &agrave; Autentica&ccedil;&atilde;o do Active Directory" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [Introdução][Introdução]
> -   [O que aconteceu][O que aconteceu]

### <span id="whathappened">O que aconteceu com meu projeto?</span>

Referências foram adicionadas.

##### Referências do pacote NuGet

-   `Microsoft.Owin`
-   `Microsoft.Owin.Host.SystemWeb`
-   `Microsoft.Owin.Security`
-   `Microsoft.Owin.Security.ActiveDirectory`
-   `Microsoft.Owin.Security.Jwt`
-   `Microsoft.Owin.Security.OAuth`
-   `Newtonsoft.Json`
-   `Owin`
-   `System.IdentityModel.Tokens.Jwt`

##### Referências .NET

-   `Microsoft.Owin`
-   `Microsoft.Owin.Host.SystemWeb`
-   `Microsoft.Owin.Security`
-   `Microsoft.Owin.Security.ActiveDirectory`
-   `Microsoft.Owin.Security.Jwt`
-   `Microsoft.Owin.Security.OAuth`
-   `Newtonsoft.Json`
-   `Owin`
-   `System.IdentityModel.Tokens.Jwt`

##### Arquivos de código foram adicionados ao seu projeto

Uma classe de inicialização de autenticação **App\_Start/Startup.Auth.cs** foi adicionada ao seu projeto contendo lógica de inicialização para autenticação do Azure AD authentication.

##### O código de inicialização foi adicionado ao seu projeto

Se você já tiver uma classe de Inicialização em seu projeto, o método **Configuração** foi atuallizado para incluir uma chamada para `ConfigureAuth(app)` que foi incluída nesse método. Caso contrário, uma classe Startup foi adicionada ao projeto.

##### Seu arquivo app.config ou web.config possui novos valores de configuração.

As entradas de configuração a seguir foram adicionadas.

     <appSettings>              <add key="ida:ClientId" value="ClientId from the new Azure AD App" />              <add key="ida:Tenant" value="Your selected Azure AD Tenant" />              <add key="ida:Audience" value="The App ID Uri from the wizard" />      </appSettings> 

</p>
### Um aplicativo do Azure AD foi criado

Um Aplicativo Azure AD foi criado no diretório selecionado no assistente.

[Saiba mais sobre o Azure Active Directory][Saiba mais sobre o Azure Active Directory]

  [Introdução]: /documentation/articles/vs-active-directory-webapi-getting-started/
  [O que aconteceu]: /documentation/articles/vs-active-directory-webapi-what-happened/
  [Saiba mais sobre o Azure Active Directory]: http://azure.microsoft.com/services/active-directory/
