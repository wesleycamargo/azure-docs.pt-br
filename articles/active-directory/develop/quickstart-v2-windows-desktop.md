---
title: Início Rápido à área de trabalho do Windows no Azure AD v2 | Microsoft Docs
description: Saiba como um aplicativo .NET da área de trabalho do Windows (XAML) pode obter um token de acesso e chamar uma API protegida por um ponto de extremidade do Azure Active Directory v2.0
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2018
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 679e091098b9205432f1849b04855d6c121fa7d9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56203821"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-windows-desktop-app"></a>Início Rápido: Adquirir um token e chamar a API do Microsoft Graph de um aplicativo da área de trabalho do Windows

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Neste início rápido, você aprenderá a programar um aplicativo WPF (Windows Presentation Foundation) .NET de área de trabalho do Windows que pode conectar contas corporativas, pessoais ou de estudante, obter um token de acesso e chamar a API do Microsoft Graph.

![Como funciona o aplicativo de exemplo gerado por este início rápido](media/quickstart-v2-windows-desktop/windesktop-intro.png)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrar e baixar o aplicativo de início rápido
> Você tem duas opções para iniciar o aplicativo de início rápido:
> * [Express] [Opção 1: Registrar e configurar o aplicativo automaticamente e, em seguida, baixar seu exemplo de código](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manual] [Opção 2: Registrar e configurar manualmente o aplicativo e o exemplo de código](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Opção 1: Registrar e configurar o aplicativo automaticamente e, em seguida, baixar seu exemplo de código
>
> 1. Vá para o [portal do Azure – Registro de Aplicativo (Versão Prévia)](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
> 1. Insira um nome para seu aplicativo e selecione **Registrar**.
> 1. Siga as instruções para baixar e configurar automaticamente o novo aplicativo com apenas um clique.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opção 2: Registrar e configurar manualmente o aplicativo e o exemplo de código
>
> #### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo
> Para registrar seu aplicativo e adicionar as informações de registro do aplicativo à solução manualmente, siga estas etapas:
>
> 1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta corporativa ou de estudante ou uma conta pessoal da Microsoft.
> 1. Se sua conta fornecer acesso a mais de um locatário, selecione sua conta no canto superior direito e defina sua sessão do portal para o locatário desejado do Azure AD.
> 1. No painel de navegação à esquerda, selecione o serviço **Azure Active Directory** e selecione **Registros de aplicativo (Versão prévia)** > **Novo registro**.
> 1. Quando a página **Registrar um aplicativo** for exibida, insira as informações de registro do aplicativo:
>      - Na seção **Nome**, insira um nome de aplicativo relevante que será exibido aos usuários do aplicativo, por exemplo, `Win-App-calling-MsGraph`.
>      - Na seção **Tipos de conta com suporte**, selecione **Contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)**.
>      - Selecione **Registrar** para criar o aplicativo.
> 1. Na lista de páginas para o aplicativo, selecione **Autenticação**.
> 1. Na seção **URIs de redirecionamento**, localize a seção **URIs de redirecionamento sugeridos para clientes públicos (dispositivos móvel, área de trabalho)** e selecione **"urn:ietf:wg:oauth:2.0:oob**.
> 1. Clique em **Salvar**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Etapa 1: Configurar seu aplicativo no portal do Azure
> Para que o exemplo de código deste início rápido funcione, você precisa adicionar uma URL de resposta como **urn: ietf:wg:oauth:2.0:oob**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer essa alteração para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-windows-desktop/green-check.png) Seu aplicativo já está configurado com esses atributos.

#### <a name="step-2-download-your-visual-studio-project"></a>Etapa 2: Baixar seu projeto do Visual Studio

[Baixar o projeto do Visual Studio 2017](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Etapa 3: Configurar o projeto do Visual Studio

1. Extraia o arquivo zip para uma pasta local mais próxima da raiz do disco, por exemplo, **C:\Azure-Samples**.
1. Abra o projeto no Visual Studio.
1. Edite **App.Xaml.cs** e substitua os valores dos campos `ClientId` e `Tenant` pelo seguinte código:

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    private static string Tenant = "Enter_the_Tenant_Info_Here";
    ```

> [!div renderon="docs"]
> Em que:
> - `Enter_the_Application_Id_here` - é a **ID do aplicativo (cliente)** que você registrou.
> - `Enter_the_Tenant_Info_Here` - é definido como uma das seguintes opções:
>   - Se seu aplicativo dá suporte a **Contas neste diretório organizacional**, substitua esse valor pela **ID do Locatário** ou pelo **Nome do locatário** (por exemplo, contoso.microsoft.com)
>   - Se seu aplicativo dá suporte a **Contas em qualquer diretório organizacional**, substitua esse valor por `organizations`
>   - Se seu aplicativo dá suporte a **Contas em qualquer diretório organizacional e contas pessoais da Microsoft**, substitua esse valor por `common`
>
> > [!TIP]
> > Para encontrar os valores de **ID do aplicativo (cliente)**, **ID de diretório (locatário)** e **Tipos de conta com suporte**, vá para a página **Visão Geral** do aplicativo no portal do Azure.

## <a name="more-information"></a>Mais informações

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) é a biblioteca usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pelo Azure AD (Microsoft Azure Active Directory). Você pode instalar a MSAL executando o comando abaixo no **Console do Gerenciador de Pacotes** do Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client -Pre
```

### <a name="msal-initialization"></a>Inicialização da MSAL

Você pode adicionar a referência da MSAL adicionando o seguinte código:

```csharp
using Microsoft.Identity.Client;
```

Em seguida, inicialize a MSAL usando o seguinte código:

```csharp
public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
```

> |Em que: ||
> |---------|---------|
> | `ClientId` | É a **ID do aplicativo (cliente)** relativa ao aplicativo registrado no portal do Azure. Você pode encontrar esse valor na página **Visão Geral** do aplicativo no portal do Azure. |

### <a name="requesting-tokens"></a>Solicitando tokens

A MSAL tem dois métodos para adquirir tokens: `AcquireTokenAsync` e `AcquireTokenSilentAsync`.

#### <a name="get-a-user-token-interactively"></a>Obter um token de usuário interativamente

Algumas situações exigem obrigar os usuários a interagir com o ponto de extremidade v2.0 do Azure AD por meio de uma janela pop-up para validar suas credenciais ou para dar consentimento. Alguns exemplos incluem:

- A primeira vez que os usuários entram no aplicativo
- Quando os usuários precisam reinserir suas credenciais porque a senha expirou
- Quando seu aplicativo está solicitando acesso a um recurso com o qual o usuário precisa consentir
- Quando a autenticação de dois fatores é necessária

```csharp
authResult = await App.PublicClientApp.AcquireTokenAsync(_scopes);
```

> |Em que:||
> |---------|---------|
> | `_scopes` | Contém os escopos que estão sendo solicitados, por exemplo, `{ "user.read" }` para o Microsoft Graph ou `{ "api://<Application ID>/access_as_user" }` para as APIs Web personalizadas. |

#### <a name="get-a-user-token-silently"></a>Obter um token de usuário no modo silencioso

Não convém exigir que o usuário valide suas credenciais sempre que precisar acessar um recurso. Na maioria das vezes, você quer aquisições e renovação de tokens sem nenhuma interação do usuário. Você pode usar o método `AcquireTokenSilentAsync` para obter tokens para acessar recursos protegidos após o método `AcquireTokenAsync` inicial:

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
authResult = await App.PublicClientApp.AcquireTokenSilentAsync(scopes, accounts.FirstOrDefault());
```

> |Em que: ||
> |---------|---------|
> | `scopes` | Contém os escopos que estão sendo solicitados, por exemplo, `{ "user.read" }` para o Microsoft Graph ou `{ "api://<Application ID>/access_as_user" }` para as APIs Web personalizadas. |
> | `accounts.FirstOrDefault()` | Especifica primeiro usuário no cache (a MSAL dá suporte a vários usuários em um mesmo aplicativo). |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Próximas etapas

Experimente o tutorial de área de trabalho do Windows para ver um guia passo a passo completo sobre a criação de aplicativos e novos recursos, incluindo uma explicação completa deste início rápido.

> [!div class="nextstepaction"]
> [Tutorial Chamar a API do Graph](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-windesktop)

