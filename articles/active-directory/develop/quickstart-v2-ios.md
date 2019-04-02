---
title: Início rápido do iOS do Azure AD v2 | Microsoft Docs
description: Saiba como conectar usuários e consultar o Microsoft Graph em um aplicativo nativo do iOS.
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57aabb25b960c1135704c62c30b5724026078b08
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439255"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-native-app"></a>Início Rápido: Conectar usuários e chamar a API do Microsoft Graph de um aplicativo iOS nativo

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Este início rápido contém um exemplo de código que demonstra como um aplicativo nativo iOS pode conectar contas corporativas, pessoais ou de estudante, obter um token de acesso e chamar a API do Microsoft Graph.

![Mostra como o aplicativo de exemplo gerado por este início rápido funciona](media/quickstart-v2-ios/ios-intro-updated.png)

> [!div renderon="docs"]
> ## <a name="register-and-download"></a>Registrar e baixar
> ### <a name="register-and-configure-your-application-and-code-sample"></a>Registre e configure seu aplicativo e exemplo de código
> #### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo
> Para registrar seu aplicativo e adicionar suas informações de registro de aplicativo à sua solução, faça o seguinte:
> 1. Acesse o [Portal de Registro de Aplicativos da Microsoft](https://apps.dev.microsoft.com/portal/register-app) para registrar um aplicativo.
> 1. No campo **Nome do Aplicativo**, insira um nome para o seu aplicativo.
> 1. Desmarque a caixa de seleção **Instalação Interativa** e, depois, selecione **Criar**.
> 1. Selecione **Adicionar Plataforma**, selecione **Aplicativo Nativo** e, em seguida, selecione **Salvar**.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Etapa 1: Configurar seu aplicativo
> Para que o exemplo de código deste início rápido funcione, você precisará adicionar uma URL de resposta como `msal<AppId>://auth` (em que msal\<AppId> é a ID desse aplicativo).
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Fazer essa alteração para mim]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Já configurado](media/quickstart-v2-ios/green-check.png) Seu aplicativo já está configurado com esse atributo

#### <a name="step-2-download-your-web-server-or-project"></a>Etapa 2: Baixar o servidor Web ou o projeto

- [Baixar o projeto do XCode](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Etapa 3: Configurar seu projeto

1. Extraia o arquivo zip e abra o projeto no XCode.
1. Edite **ViewController.swift** e substitua a linha que começa com 'let kClientID' pelo seguinte snippet de código:

    > [!div renderon="portal" class="sxs-lookup"]
    > ```swift
    > let kClientID = "Enter_the_Application_Id_here"
    > ```

    > [!div renderon="docs"]
    > ```swift
    > let kClientID = "<ENTER_THE_APPLICATION_ID_HERE>"
    > ```   
1. Pressione a tecla Control + clique **Info.plist** para abrir o menu contextual e, em seguida, selecione **Abrir Como** > **Código-fonte**.
1. No nó raiz dict, adicione o seguinte código:

    > [!div renderon="portal" class="sxs-lookup"]
    > ```xml
    > <key>CFBundleURLTypes</key>
    > <array>
    >     <dict>
    >         <key>CFBundleTypeRole</key>
    >         <string>Editor</string>
    >         <key>CFBundleURLName</key>
    >         <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
    >         <key>CFBundleURLSchemes</key>
    >         <array>
    >             <string>msalEnter_the_Application_Id_here</string>
    >         </array>
    >     </dict>
    > </array>
    > ```

    > [!div renderon="docs"]
    > ```xml
    > <key>CFBundleURLTypes</key>
    > <array>
    >     <dict>
    >         <key>CFBundleTypeRole</key>
    >         <string>Editor</string>
    >         <key>CFBundleURLName</key>
    >         <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
    >         <key>CFBundleURLSchemes</key>
    >         <array>
    >             <string>msal<ENTER_THE_APPLICATION_ID_HERE></string>
    >         </array>
    >     </dict>
    > </array>
    > ```
    
> [!div renderon="docs"]
> <span>5.</span> Substitua `<ENTER_THE_APPLICATION_ID_HERE>` pela *ID do seu aplicativo*. Se você precisar localizar a *ID do aplicativo*, acesse a página *Visão geral*.

## <a name="more-information"></a>Mais informações

Leia estas seções para saber mais sobre este guia de início rápido.

### <a name="msal"></a>MSAL

MSAL ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) é a biblioteca usada para conectar usuários e solicitar tokens usados para acessar uma API protegida pelo Microsoft Azure Active Directory. Você pode adicionar s MSAL ao seu aplicativo usando o seguinte processo:

```
$ vi Podfile
```
Adicione o seguinte a esse podfile:

```
 target 'QuickStart' do
   use_frameworks!
 pod 'MSAL'
 end
```

### <a name="msal-initialization"></a>Inicialização da MSAL

Você pode adicionar a referência da MSAL adicionando o seguinte código:

```swift
import MSAL
```

Em seguida, inicialize a MSAL usando o seguinte código:

```swift
let authority = MSALAuthority(url: URL(string: kAuthority)!)
self.applicationContext = try MSALPublicClientApplication(clientId: kClientID, authority: authority)
```

> |Em que: ||
> |---------|---------|
> | `clientId` | A ID do aplicativo registrado em *portal.azure.com* |
> | `authority` | O ponto de extremidade do Azure AD v2.0. Na maioria dos casos ele será *https<span/>://login.microsoftonline.com/common* |

### <a name="requesting-tokens"></a>Solicitando tokens

A MSAL tem dois métodos usados para adquirir tokens: `acquireToken` e `acquireTokenSilent`.

#### <a name="getting-an-access-token-interactively"></a>Obter um token de acesso de forma interativa

Algumas situações exigem que os usuários sejam forçados a interagir com o ponto de extremidade do Azure AD (Azure Active Directory) v2.0, o que resultará em uma mudança de contexto no navegador do sistema para validar as credenciais dos usuários ou para solicitar consentimento. Alguns exemplos incluem:

* A primeira vez que os usuários entram no aplicativo
* Quando os usuários precisam reinserir suas credenciais porque a senha expirou
* Quando seu aplicativo está solicitando acesso a um recurso com o qual o usuário precisa consentir
* Quando a autenticação de dois fatores é necessária

```swift
applicationContext.acquireToken(forScopes: self.kScopes) { (result, error) in /* Add your handling logic */}
```

> |Em que:||
> |---------|---------|
> | `forScopes` | Contém os escopos que estão sendo solicitados [ou seja, `[ "user.read" ]` para o Microsoft Graph ou `[ "<Application ID URL>/scope" ]` para as APIs Web personalizadas (ou seja, `api://<Application ID>/access_as_user`)] |

#### <a name="getting-an-access-token-silently"></a>Acessando um token de acesso silenciosamente

Não convém exigir que o usuário valide suas credenciais sempre que precisar acessar um recurso. Na maioria das vezes, você quer aquisições e renovação de tokens sem nenhuma interação do usuário. Você pode usar o método `acquireTokenSilent` para obter tokens para acessar recursos protegidos após o método `acquireToken` inicial:

```swift
applicationContext.acquireTokenSilent(forScopes: self.kScopes, account: applicationContext.allAccounts().first) { (result, error) in /* Add your handling logic */}
```

> |Em que: ||
> |---------|---------|
> | `forScopes` | Contém os escopos que estão sendo solicitados [ou seja, `[ "user.read" ]` para o Microsoft Graph ou `[ "<Application ID URL>/scope" ]` para as APIs Web personalizadas (ou seja, `api://<Application ID>/access_as_user`)] |
> | `account` | A conta que está solicitando o token (MSAL dá suporte a várias contas em um único aplicativo). No caso deste Início Rápido, o valor aponta para a primeira conta no cache (`applicationContext.allAccounts().first`). |

## <a name="next-steps"></a>Próximas etapas

Experimente o tutorial do iOS para obter um guia passo a passo completo sobre a criação de aplicativos e recursos, incluindo uma explicação completa sobre este Início Rápido.

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Conheça as etapas para criar o aplicativo usado neste início rápido

> [!div class="nextstepaction"]
> [Tutorial Chamar a API do Graph do iOS](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
