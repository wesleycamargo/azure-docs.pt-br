---
title: Alterações feitas em um projeto de MVC quando você se conecta ao Azure AD | Microsoft Docs
description: Descreve o que acontece ao seu projeto do MVC quando você se conecta ao AD do Azure usando os serviços conectados do Visual Studio
services: active-directory
documentationcenter: na
author: kraigb
manager: ghogen
editor: ''
ms.assetid: 8b24adde-547e-4ffe-824a-2029ba210216
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-what-happened
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: kraigb
ms.custom: aaddev
ms.openlocfilehash: b17c5fe500f3e2a8370ec5c4a09b62737d9afb84
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="what-happened-to-my-mvc-project-visual-studio-azure-active-directory-connected-service"></a>O que aconteceu com meu projeto do MVC (serviço conectado do Active Directory do Azure do Visual Studio)?

> [!div class="op_single_selector"]
> - [Introdução](vs-active-directory-dotnet-getting-started.md)
> - [O que aconteceu](vs-active-directory-dotnet-what-happened.md)

Este artigo identifica as alterações exatas feitas em um projeto ASP.NET MVC ao adicionar o [serviço conectado do Azure Active Directory usando o Visual Studio](vs-active-directory-add-connected-service.md).

Para obter informações sobre como trabalhar com o serviço conectado, consulte [Introdução](vs-active-directory-dotnet-getting-started.md).

## <a name="added-references"></a>Referências adicionadas

Afeta as referências *.NET do arquivo de projeto) e `packages.config` (referências de NuGet).

| type | Referência |
| --- | --- |
| .NET; NuGet | Microsoft.IdentityModel.Protocol.Extensions |
| .NET; NuGet | Microsoft.Owin |
| .NET; NuGet | Microsoft.Owin.Host.SystemWeb |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security |
| .NET; NuGet | Microsoft.Owin.Security.OpenIdConnect |
| .NET; NuGet | Owin |
| .NET        | System.IdentityModel |
| .NET; NuGet | System.IdentityModel.Tokens.Jwt |
| .NET        | System.Runtime.Serialization |

Referências adicionais se você selecionou a opção **Ler dados do diretório**:

| type | Referência |
| --- | --- |
| .NET; NuGet | EntityFramework |
| .NET        | EntityFramework.SqlServer (somente Visual Studio 2015) |
| .NET; NuGet | Microsoft.Azure.ActiveDirectory.GraphClient |
| .NET; NuGet | Microsoft.Data.Edm |
| .NET; NuGet | Microsoft.Data.OData |
| .NET; NuGet | Microsoft.Data.Services.Client |
| .NET; NuGet | Microsoft.IdentityModel.Clients.ActiveDirectory |
| .NET        | Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms (somente Visual Studio 2015) |
| .NET; NuGet | System.Spatial |

As seguintes referências são removidas (ASP.NET 4 de projetos, como Visual Studio 2015):

| type | Referência |
| --- | --- |
| .NET; NuGet | Microsoft.AspNet.Identity.Core |
| .NET; NuGet | Microsoft.AspNet.Identity.EntityFramework |
| .NET; NuGet | Microsoft.AspNet.Identity.Owin |

## <a name="project-file-changes"></a>Alterações de arquivo do projeto

- Defina a propriedade `IISExpressSSLPort` para um número distinto.
- Defina a propriedade `WebProject_DirectoryAccessLevelKey` para 0, ou 1 se você selecionou a opção **Ler dados do diretório**.
- Defina a propriedade `IISUrl` e `https://localhost:<port>/` onde `<port>` corresponde com o valor `IISExpressSSLPort`.

## <a name="webconfig-or-appconfig-changes"></a>alterações de web.config ou app.config

- Adicionadas as entradas de configuração a seguir:

    ```xml
    <appSettings>
        <add key="ida:ClientId" value="<ClientId from the new Azure AD app>" />
        <add key="ida:AADInstance" value="https://login.microsoftonline.com/" />
        <add key="ida:Domain" value="<your selected Azure domain>" />
        <add key="ida:TenantId" value="<the Id of your selected Azure AD tenant>" />
        <add key="ida:PostLogoutRedirectUri" value="<project start page, such as https://localhost:44335>" />
    </appSettings>
    ```

- Adicionados os elementos `<dependentAssembly>` no nó `<runtime><assemblyBinding>` para `System.IdentityModel.Tokens.Jwt` e `Microsoft.IdentityModel.Protocol.Extensions`.

Alterações adicionais se você selecionou a opção **Ler dados do diretório**:

- Adicionada a entrada de configuração a seguir em `<appSettings>`:

    ```xml
    <add key="ida:ClientSecret" value="<Azure AD app's new client secret>" />
    ```

- Adicionou os seguintes elementos em `<configuration>`; valores para o project.mdf-file e project-catalog-id variam de:

    ```xml
    <configSections>
      <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
      <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
    </configSections>

    <connectionStrings>
      <add name="DefaultConnection" connectionString="Data Source=(localdb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\<project-mdf-file>.mdf;Initial Catalog=<project-catalog-id>;Integrated Security=True" providerName="System.Data.SqlClient" />
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
    </entityFramework>
    ```

- Adicionados os elementos `<dependentAssembly>` no nó `<runtime><assemblyBinding>` para `Microsoft.Data.Services.Client`, `Microsoft.Data.Edm`, e `Microsoft.Data.OData`.

## <a name="code-changes-and-additions"></a>Adições e alterações de código

- Adicionado o atributo `[Authorize]` para `Controllers/HomeController.cs` e outros controladores existentes.

- Adicionada uma classe de inicialização de autenticação, `App_Start/Startup.Auth.cs`, que contém a lógica de inicialização para a autenticação do Azure AD. Se você tiver selecionado a opção **Ler dados do diretório**, esse arquivo também contém código para receber um código de OAuth e do trocá-lo por um token de acesso.

- Adicionada uma classe de controlador, `Controllers/AccountController.cs`, que contém os métodos `SignIn` e `SignOut`.

- Adicionada uma exibição parcial, `Views/Shared/_LoginPartial.cshtml`, que contém um link de ação para `SignIn` e `SignOut`.

- Adicionada uma exibição parcial, `Views/Account/SignoutCallback.cshtml`, que contém o HTML para a saída da interface do usuário.

- Atualizado o método `Startup.Configuration` para incluir uma chamada para `ConfigureAuth(app)` se a classe já existir; caso contrário adicionada uma classe `Startup` que inclui chamadas para o método.

- Adicionado `Connected Services/AzureAD/ConnectedService.json` (Visual Studio 2017) ou `Service References/Azure AD/ConnectedService.json` (Visual Studio 2015), que contém informações que o Visual Studio usa para acompanhar a adição do serviço conectado.

- Se você selecionou a opção **Ler dados do diretório**, adicionou `Models/ADALTokenCache.cs` e `Models/ApplicationDbContext.cs` para oferecer suporte a cache de token. Também é adicionado um controlador adicional e a exibição para ilustrar acessando informações de perfil de usuário usando os APIs do Azure graph: `Controllers/UserProfileController.cs`, `Views/UserProfile/Index.cshtml`, e `Views/UserProfile/Relogin.cshtml`

### <a name="file-backup-visual-studio-2015"></a>Backup de arquivo (Visual Studio 2015)

Ao adicionar o serviço conectado, Visual Studio 2015 faz backup dos arquivos alterados e removidos. Todos os arquivos afetados são salvos na pasta `Backup/AzureAD`. O Visual Studio 2017 não cria backups.

- `Startup.cs`
- `App_Start\IdentityConfig.cs`
- `App_Start\Startup.Auth.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Models\ManageViewModels.cs`
- `Views\Shared\_LoginPartial.cshtml`

## <a name="changes-on-azure"></a>Alterações no Azure

- Criado um aplicativo do Azure AD no domínio que você selecionou ao adicionar o serviço conectado.
- Atualizado o aplicativo para incluir a permissão **Ler dados do diretório** se essa opção foi selecionada.

[Saiba mais sobre o Active Directory do Azure](https://azure.microsoft.com/services/active-directory/).

## <a name="next-steps"></a>Próximas etapas

- [Cenários de autenticação do Azure Active Directory](active-directory-authentication-scenarios.md)
- [Adicionar entrada com a Microsoft para um aplicativo Web ASP.NET](guidedsetups/active-directory-aspnetwebapp-v1.md)
