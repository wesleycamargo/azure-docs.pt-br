---
title: Início rápido do aplicativo Web do Azure AD v2.0 ASP.NET Core | Microsoft Docs
description: Saiba como implementar a Entrada da Microsoft em um aplicativo Web do ASP.NET Core usando o OpenID Connect
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: f57dc1707a9e25e4ea308142e804cdbc80d4308a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984935"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Adicionar entrada com a Microsoft para um aplicativo Web do ASP.NET Core

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Este início rápido oferece um exemplo de código que demonstra como um aplicativo Web do ASP.NET Core pode conectar contas pessoais (hotmail.com, live.com, entre outras), corporativas e de estudante de qualquer instância do Azure Active Directory.

![Como funciona o aplicativo de exemplo gerado por esse Início rápido](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.png)


> [!div renderon="docs"]
> ## <a name="register-your-application-and-download-your-quickstart-app"></a>Registre seu aplicativo e faça o download de seu aplicativo de início rápido
>
> ### <a name="register-and-configure-your-application-and-code-sample"></a>Registre e configure seu aplicativo e exemplo de código
> #### <a name="step-1-register-your-application"></a>Etapa 1: Registrar seu aplicativo
> 
> 1. Vá até o [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/portal/register-app).
> 1. Insira um nome para seu aplicativo, verifique se a opção **Instalação Guiada** está desmarcada e clique em **Criar**.
> 1. Clique em `Add Platform` e escolha `Web`.
> 1. Verifique se **Permitir Fluxo Implícito** está *marcado*.
> 1. Em **URLs de Redirecionamento**, insira `https://localhost:3110/`.
> 1. Role para baixo até a base da página e clique em **Salvar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Etapa 1: Configurar seu aplicativo no portal do Azure
> Para que o exemplo de código deste início rápido funcione, você precisa adicionar uma URL de resposta como `http://localhost:3110/`.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer essa alteração para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-aspnet-core-webapp/green-check.png) Seu aplicativo já está configurado com esse atributo

#### <a name="step-2-download-your-aspnet-core-project"></a>Etapa 2: Fazer o download do projeto do ASP.NET Core

- [Fazer o download do projeto do ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Etapa 3: Configurar seu projeto

1. Extraia o arquivo zip para uma pasta local (por exemplo, **C:\Azure-Samples**)
1. Se você usa o Visual Studio 2017, abra o projeto no Visual Studio (opcional)
1. Edite **appsettings.json** e substitua o valor de `ClientId` pela ID do aplicativo no aplicativo que você acabou de registrar:

    ```json
    "ClientId": "Enter_the_Application_Id_here"
    "TenantId": "common"
    ```
1. Se seu aplicativo for um *aplicativo de locatário único* (direcionado às contas neste diretório apenas), no arquivo **appsettings.json**, localize o valor para `TenantId` e substitua `common` pela **Id do locatário** ou pelo **Nome do locatário** (por exemplo, contoso.microsoft.com). Você pode obter o nome do locatário na **página Visão Geral**.

## <a name="more-information"></a>Mais informações

Esta seção apresenta uma visão geral do código necessário para a entrada de usuários. Isso pode ser útil para entender como o código funciona, argumentos principais e também se você quiser adicionar entrada a um aplicativo do ASP.NET Core existente.

### <a name="startup-class"></a>Classe de inicialização

O middleware *Microsoft.AspNetCore.Authentication* usa a classe de inicialização executada quando o processo de hospedagem é inicializado:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(sharedOptions =>
    {
        sharedOptions.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        sharedOptions.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddAzureAd(options => Configuration.Bind("AzureAd", options))
    .AddCookie();

    services.AddMvc();
}
```

O método `AddAuthentication` configura o serviço para adicionar a autenticação baseada em cookies, usada em cenários de navegador, além de definir o desafio para OpenIdConnect. 

A linha que contém `.AddAzureAd` adiciona a autenticação do Azure Active Directory ao aplicativo.

Além disso, o arquivo **AzureAdAuthenticationBuilderExtensions.cs** adiciona o método de extensão ao pipeline de autenticação do Azure AD. Esse método de extensão configura os atributos necessários para a autenticação do Azure AD. O método `Configure` na interface `IConfigureNamedOptions` contém o seguinte:

```csharp
public void Configure(string name, OpenIdConnectOptions options)
{
    options.ClientId = _azureOptions.ClientId;
    options.Authority = $"{_azureOptions.Instance}common/v2.0";   // V2 specific
    options.UseTokenLifetime = true;
    options.RequireHttpsMetadata = false;
    options.TokenValidationParameters.ValidateIssuer = false;     // accept several tenants (here simplified)
}
```
> |Where  |  |
> |---------|---------|
> |ClientId     |ID do aplicativo referente ao aplicativo registrado no portal do Azure|
> |Authority | O ponto de extremidade do STS para o usuário autenticar. Geralmente https://login.microsoftonline.com/{tenant}/v2.0 para a nuvem pública, em que {tenant} é o nome do seu locatário, sua Id do locatário ou *common* para uma referência ao ponto de extremidade comum (usado para aplicativos multilocatário)|
> |UseTokenLifetime |Indica que o cookie de autenticação deve coincidir com o token de autenticação|
> |RequireHttpsMetadata     |Exige HTTPS para o endereço de metadados ou a autoridade. Não é recomendável alterar esse valor para `True`|
> |TokenValidationParameters     | Uma lista de parâmetros para validação de token. Nesse caso, `ValidateIssuer` é definido como `false` para indicar que ele pode aceitar entradas de quaisquer contas corporativas, de estudante ou pessoais|

### <a name="initiate-an-authentication-challenge"></a>Iniciar um desafio de autenticação

Você pode forçar um usuário a entrar solicitando um desafio de autenticação em seu controlador como em **AccountController.cs**:

```csharp
public IActionResult SignIn()
{
    var redirectUrl = Url.Action(nameof(HomeController.Index), "Home");
    return Challenge(
        new AuthenticationProperties { RedirectUri = redirectUrl },
        OpenIdConnectDefaults.AuthenticationScheme);
}
```

> [!TIP]
> Solicitar um desafio de autenticação usando o método acima é opcional e normalmente é usado quando você deseja que uma exibição fique disponível para usuários autenticados e não autenticados. Você pode proteger os controladores usando o método descrito na próxima seção.

### <a name="protect-a-controller-or-a-controllers-method"></a>Proteger um controlador ou um método do controlador

Você pode proteger um controlador ou métodos do controlador usando o atributo `[Authorize]`. Esse atributo restringe o acesso ao controlador ou métodos, permitindo apenas usuários autenticados, ou seja, o desafio de autenticação poderá ser iniciado para acessar o controlador se o usuário não estiver autenticado.

## <a name="next-steps"></a>Próximas etapas

Confira o repositório do GitHub deste Início rápido do ASP.NET Core para obter mais informações, incluindo instruções sobre como adicionar autenticação a um aplicativo Web do ASP.NET Core totalmente novo:

> [!div class="nextstepaction"]
> [Exemplo de código do aplicativo Web do ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]