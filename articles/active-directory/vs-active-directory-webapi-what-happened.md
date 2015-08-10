<properties
	pageTitle=""
	description="Descreve o que foi alterado no projeto do Visual Studio depois que você executou o assistente do Active Directory do Azure"
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
	ms.date="07/22/2015"
	ms.author="patshea123"/>

# O que aconteceu com meu projeto?

> [AZURE.SELECTOR]
> - [Getting Started](vs-active-directory-webapi-getting-started.md)
> - [What Happened](vs-active-directory-webapi-what-happened.md)

##Referências foram adicionadas

###Referências do pacote NuGet

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

###Referências .NET

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

##Alterações de código

###Arquivos de código foram adicionados ao seu projeto

Uma classe de inicialização de autenticação **App\_Start/Startup.Auth.cs** foi adicionada ao seu projeto contendo lógica de inicialização para autenticação do Azure AD authentication.

###O código de inicialização foi adicionado ao seu projeto

Se você já tiver uma classe de Inicialização em seu projeto, o método **Configuração** terá sido atualizado para incluir uma chamada para `ConfigureAuth(app)`. Caso contrário, uma classe Startup foi adicionada ao projeto.


###Seu arquivo app.config ou web.config possui novos valores de configuração.

As entradas de configuração a seguir foram adicionadas. <pre> `<appSettings>
    		<add key="ida:ClientId" value="ClientId from the new Azure AD App" />
    		<add key="ida:Tenant" value="Your selected Azure AD Tenant" />
    		<add key="ida:Audience" value="The App ID Uri from the wizard" />
	</appSettings>` </pre>

###Um aplicativo do Azure AD foi criado

Um Aplicativo Azure AD foi criado no diretório selecionado no assistente.

[Saiba mais sobre o Active Directory do Azure](http://azure.microsoft.com/services/active-directory/)

##Se eu marquei *Desabilitar autenticação de contas de usuários individuais*, que alterações adicionais foram feitas ao meu projeto?
Referências ao pacote NuGet foram removidas e arquivos foram removidos e copiados. Dependendo do estado do seu projeto, você terá de remover manualmente referências ou arquivos adicionais ou modificar o código conforme apropriado.

###Referências ao pacotes NuGet removidas (para aquelas presentes)

- `Microsoft.AspNet.Identity.Core`
- `Microsoft.AspNet.Identity.EntityFramework`
- `Microsoft.AspNet.Identity.Owin`

###Arquivos de código copiados e removidos (para aqueles presentes)

Cada um dos seguintes arquivos foi copiado e removido do projeto. Arquivos de backup estão localizados em uma pasta “Backup” na raiz do diretório do projeto.

- `App_Start\IdentityConfig.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Providers\ApplicationOAuthProvider.cs`

###Arquivos de código copiados (para aqueles presentes)

Cada um dos seguintes arquivos foi copiado antes de ser substituído. Arquivos de backup estão localizados em uma pasta “Backup” na raiz do diretório do projeto.

- `Startup.cs`
- `App_Start\Startup.Auth.cs`

##Se eu marquei *Ler dados do diretório*, que alterações adicionais foram feitas ao meu projeto?

###Foram feitas alterações adicionais em sua app.config ou web.config

As entradas de configuração extras a seguir foram adicionadas. <pre> `<appSettings>
	    <add key="ida:Password" value="Your Azure AD App's new password" />
	</appSettings>` </pre>

###Seu aplicativo Active Directory do Azure foi atualizado
Seu aplicativo Active Directory do Azure foi atualizado para incluir a permissão *Ler dados do diretório* e uma chave extra foi criada e, em seguida, utilizada como *ida:Password* no arquivo `web.config`.

[Saiba mais sobre o Active Directory do Azure](http://azure.microsoft.com/services/active-directory/)

<!---HONumber=July15_HO5-->