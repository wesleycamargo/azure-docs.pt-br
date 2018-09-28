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
ms.component: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 28c5f025b59b4adbb33a59edd225a839e11dad97
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46965085"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-windows-desktop-app"></a>Início Rápido: Adquirir um token e chamar a API do Microsoft Graph de um aplicativo da área de trabalho do Windows

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Neste início rápido, você aprenderá como um aplicativo WPF (Windows Presentation Foundation) .NET de área de trabalho do Windows pode conectar contas corporativas, pessoais ou de estudante, obter um token de acesso e chamar a API do Microsoft Graph.

![Como funciona o aplicativo de exemplo gerado por este início rápido](media/quickstart-v2-windows-desktop/windesktop-intro.png)

> [!div renderon="docs"]
> ## <a name="register-and-download"></a>Registrar e baixar
> ### <a name="register-and-configure-your-application-and-code-sample"></a>Registrar e configurar o aplicativo e o exemplo de código
> #### <a name="step-1-register-your-application"></a>Etapa 1: Registrar seu aplicativo
> Para registrar seu aplicativo e adicionar suas informações de registro de aplicativo à sua solução, faça o seguinte:
> 1. Acesse o [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/portal/register-app) para registrar um aplicativo.
> 1. No campo **Nome do Aplicativo**, insira um nome para o seu aplicativo.
> 1. Desmarque a caixa de seleção **Instalação Interativa** e, depois, selecione **Criar**.
> 1. Selecione **Adicionar Plataforma**, selecione **Aplicativo Nativo** e, em seguida, selecione **Salvar**.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Etapa 1: Configurar seu aplicativo
> Para que o exemplo de código deste início rápido funcione, você precisa adicionar uma URL de resposta como **urn: ietf:wg:oauth:2.0:oob**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Faça essa alteração para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-windows-desktop/green-check.png) Seu aplicativo já está configurado com esses atributos

#### <a name="step-2-download-your-visual-studio-project"></a>Etapa 2: Baixar o projeto do Visual Studio

[Baixar o projeto do Visual Studio 2017](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/master.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Etapa 3: Configurar o projeto do Visual Studio

1. Extraia o arquivo zip para uma pasta local (por exemplo, **C:\Azure-Samples**)
1. Abra o projeto no Visual Studio.
1. Edite **App.Xaml.cs** e substitua a linha que começa com `private static string ClientId` pela ID do aplicativo que você acabou de registrar:

```csharp
private static string ClientId = "Enter_the_Application_Id_here";
```

## <a name="more-information"></a>Mais informações

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) é a biblioteca usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pelo Azure AD (Microsoft Azure Active Directory). Você pode instalá-la executando o comando a seguir no **Console do Gerenciador de Pacotes** do Visual Studio:

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
> | `ClientId` | A ID do aplicativo registrado no *portal.microsoft.com* |

### <a name="requesting-tokens"></a>Solicitando tokens

A MSAL tem dois métodos para adquirir tokens: `AcquireTokenAsync` e `AcquireTokenSilentAsync`.

#### <a name="get-a-user-token-interactively"></a>Obter um token de usuário interativamente

Algumas situações exigem forçar os usuários a interagir com o ponto de extremidade do Azure AD v2.0 por meio de uma janela pop-up para validar suas credenciais ou para dar consentimento. Alguns exemplos incluem:

- A primeira vez que os usuários entram no aplicativo
- Quando os usuários precisam reinserir suas credenciais porque a senha expirou
- Quando seu aplicativo está solicitando acesso a um recurso com o qual o usuário precisa consentir
- Quando a autenticação de dois fatores é necessária

```csharp
authResult = await App.PublicClientApp.AcquireTokenAsync(_scopes);
```

> |Em que:||
> |---------|---------|
> | `_scopes` | Contém os escopos que estão sendo solicitados (ou seja, `{ "user.read" }` para o Microsoft Graph ou `{ "api://<Application ID>/access_as_user" }` para as APIs Web personalizadas) |

#### <a name="get-a-user-token-silently"></a>Obter um token de usuário no modo silencioso

Não convém exigir que o usuário valide suas credenciais sempre que precisar acessar um recurso. Na maioria das vezes, você quer aquisições e renovação de tokens sem nenhuma interação do usuário. Você pode usar o método `AcquireTokenSilentAsync` para obter tokens para acessar recursos protegidos após o método `AcquireTokenAsync` inicial:

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
authResult = await App.PublicClientApp.AcquireTokenSilentAsync(scopes, accounts.FirstOrDefault());
```

> |Em que: ||
> |---------|---------|
> |escopos | Contém os escopos que estão sendo solicitados (ou seja, `{ "user.read" }` para o Microsoft Graph ou `{ "api://<Application ID>/access_as_user" }` para as APIs Web personalizadas) |
> |accounts.FirstOrDefault() | O primeiro usuário no cache (a MSAL dá suporte a vários usuários em um único aplicativo) |

## <a name="next-steps"></a>Próximas etapas

Experimente o tutorial de área de trabalho do Windows para ver um guia passo a passo completo sobre a criação de aplicativos e novos recursos, incluindo uma explicação completa deste início rápido.

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Conheça as etapas para criar o aplicativo usado neste início rápido

> [!div class="nextstepaction"]
> [Tutorial Chamar a API do Graph](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-windesktop)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
