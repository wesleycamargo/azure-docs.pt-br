<properties
	pageTitle="Introdução à Autenticação do Active Directory - O que aconteceu"
	description="Descreve o que aconteceu no seu projeto do Active Directory do Azure no Visual Studio"
	services="active-directory"
	documentationCenter=""
	authors="patshea123"
	manager="douge"
	editor="tglee"/>

<tags
	ms.service="active-directory"
	ms.workload="web"
	ms.tgt_pltfrm="vs-what-happened"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/06/2015"
	ms.author="patshea123"/>

# O que aconteceu com meu projeto?

> [AZURE.SELECTOR]
> - [Getting Started](vs-active-directory-dotnet-getting-started.md)
> - [What Happened](vs-active-directory-dotnet-what-happened.md)

###<span id="whathappened">O que aconteceu com meu projeto?</span>

Referências foram adicionadas.

#####Referências do pacote NuGet

- `Microsoft.IdentityModel.Protocol.Extensions`
- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.Cookies`
- `Microsoft.Owin.Security.OpenIdConnect`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

#####Referências .NET

- `Microsoft.IdentityModel.Protocol.Extensions`
- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.Cookies`
- `Microsoft.Owin.Security.OpenIdConnect`
- `Owin`
- `System.IdentityModel`
- `System.IdentityModel.Tokens.Jwt`
- `System.Runtime.Serialization`

#####Arquivos de código foram adicionados ao seu projeto

Uma classe de inicialização de autenticação `App_Start/Startup.Auth.cs` foi adicionada ao seu projeto contendo a lógica de inicialização para autenticação do AD do Azure. Além disso, uma classe de controlador, Controllers/AccountController.cs, foi adicionada e contém os métodos `SignIn()` e `SignOut()`. Por fim, uma exibição parcial `Views/Shared/_LoginPartial.cshtml` foi adicionada contendo um link de ação para Efetuar login/logout.

#####O código de inicialização foi adicionado ao seu projeto

Se você já tiver uma classe de Inicialização em seu projeto, o método **Configuração** terá sido atualizado para incluir uma chamada para `ConfigureAuth(app)`. Caso contrário, uma classe Startup foi adicionada ao projeto.

#####Seu app.config ou web.config tem novos valores de configuração

As entradas de configuração a seguir foram adicionadas. <pre> `<appSettings>
	    <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
	    <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
	    <add key="ida:Domain" value="The selected Azure AD Domain" />
	    <add key="ida:TenantId" value="The Id of your selected Azure AD Tenant" />
	    <add key="ida:PostLogoutRedirectUri" value="Your project start page" />
	</appSettings>` </pre>

#####Um aplicativo do Azure Active Directory (AD) foi criado
Um Aplicativo Azure AD foi criado no diretório selecionado no assistente.

###Se eu marquei *Desabilitar autenticação de contas de usuários individuais*, que alterações adicionais foram feitas ao meu projeto?
Referências ao pacote NuGet foram removidas e arquivos foram removidos e copiados. Dependendo do estado do seu projeto, você terá de remover manualmente referências ou arquivos adicionais ou modificar o código conforme apropriado.

#####Referências ao pacotes NuGet removidas (para aquelas presentes)

- `Microsoft.AspNet.Identity.Core`
- `Microsoft.AspNet.Identity.EntityFramework`
- `Microsoft.AspNet.Identity.Owin`

#####Arquivos de código copiados e removidos (para aqueles presentes)

Cada um dos seguintes arquivos foi copiado e removido do projeto. Arquivos de backup estão localizados em uma pasta “Backup” na raiz do diretório do projeto.

- `App_Start\IdentityConfig.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`

#####Arquivos de código copiados (para aqueles presentes)

Cada um dos seguintes arquivos foi copiado antes de ser substituído. Arquivos de backup estão localizados em uma pasta “Backup” na raiz do diretório do projeto.

- `Startup.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Views\Shared_LoginPartial.cshtml`

###Se eu marquei *Ler dados do diretório*, que alterações adicionais foram feitas ao meu projeto?
Referências extras foram adicionadas.

#####Referências adicionais do pacote do NuGet

- `EntityFramework`
- `Microsoft.Azure.ActiveDirectory.GraphClient`
- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.IdentityModel.Clients.ActiveDirectory`
- `System.Spatial`

#####Referências adicionais do .NET

- `EntityFramework`
- `EntityFramework.SqlServer`
- `Microsoft.Azure.ActiveDirectory.GraphClient`
- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.IdentityModel.Clients.ActiveDirectory`
- `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms`
- `System.Spatial`

#####Arquivos de código adicional foram adicionados ao seu projeto

Dois arquivos foram adicionados para oferecer suporte ao cache de token: `Models\ADALTokenCache.cs` e `Models\ApplicationDbContext.cs`. Um controlador e uma exibição extras foram adicionados para ilustrar o acesso às informações de perfil do usuário usando as APIs de gráfico do Azure. Esses arquivos são `Controllers\UserProfileController.cs` e `Views\UserProfile\Index.cshtml`.

#####Um código de inicialização extra foi adicionado ao seu projeto

No arquivo `startup.auth.cs`, um novo objeto `OpenIdConnectAuthenticationNotifications` foi adicionado ao membro `Notifications` do `OpenIdConnectAuthenticationOptions`. Isso é para habilitar o recebimento do código de OAuth e a troca dele por um token de acesso.

#####Foram feitas alterações adicionais em sua app.config ou web.config

As entradas de configuração extras a seguir foram adicionadas. <pre> `<appSettings>
	    <add key="ida:ClientSecret" value="Your Azure AD App's new client secret" />
	</appSettings>` </pre>

As seguintes seções de configuração e a cadeia de conexão foram adicionadas. <pre> `<configSections>
	    <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
	    <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
	</configSections>
	<connectionStrings>
	    <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\aspnet-[AppName + Generated Id].mdf;Initial Catalog=aspnet-[AppName + Generated Id];Integrated Security=True" providerName="System.Data.SqlClient" />
	</connectionStrings>
	<entityFramework>
	    <defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
	      <parameters>
	        <parameter value="mssqllocaldb" />
	      </parameters>
	    </defaultConnectionFactory>
	    <providers>
	      <provider invariantName="System.Data.SqlClient" type="System.Data.Entity.SqlServer.SqlProviderServices, EntityFramework.SqlServer" />
	    </providers>
	</entityFramework>`</pre>


#####Seu aplicativo Active Directory do Azure foi atualizado
Seu aplicativo Active Directory do Azure foi atualizado para incluir a permissão *Ler dados do diretório* e uma chave extra foi criada e, em seguida, utilizada como *ida:ClientSecret* no arquivo `web.config`.

[Saiba mais sobre o Active Directory do Azure](http://azure.microsoft.com/services/active-directory/)

<!---HONumber=July15_HO4-->