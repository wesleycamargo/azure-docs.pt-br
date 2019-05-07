---
title: Web navegadores na biblioteca de autenticação da Microsoft para o .NET | Azure
description: Saiba mais sobre as considerações específicas ao usar o Xamarin Android com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 350cb3fec4d325d6cf5848733c0bae18d5efacca
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076834"
---
# <a name="using-web-browsers-in-msalnet"></a>Usando navegadores da web em MSAL.NET
Navegadores da Web são necessários para a autenticação interativa. Por padrão, MSAL.NET suporta o [navegador da web de sistema](#system-web-browser-on-xamarinios-and-xamarinandroid) no xamarin. IOS e [xamarin. Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/system-browser). Mas [você também pode habilitar o navegador da Web incorporado](#enable-embedded-webviews) dependendo dos requisitos de (necessidade de logon único (SSO), segurança UX) em [xamarin. IOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) e [xamarin. Android](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) aplicativos. E você pode até mesmo [escolha dinamicamente](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) qual navegador da web para usar com base na presença de Chrome ou um navegador que dão suporte a guias personalizadas do Chrome no Android.

## <a name="web-browsers-in-msalnet"></a>Navegadores da Web em MSAL.NET

É importante entender que ao adquirir um token de forma interativa, o conteúdo da caixa de diálogo não é fornecido pela biblioteca, mas pelo STS (serviço de Token de segurança). O ponto de extremidade de autenticação envia de volta alguns HTML e JavaScript que controla a interação, que é renderizada em um navegador da web ou controle da web. Permitindo que o STS manipular a interação de HTML tem muitas vantagens:

- A senha (se uma foi digitada) nunca é armazenada pelo aplicativo, nem a biblioteca de autenticação.
- Permite que os redirecionamentos para outros provedores de identidade (por exemplo logon-in com uma conta corporativa de estudante ou uma conta pessoal com a MSAL, ou com uma conta social com o Azure AD B2C).
- Permite que o STS de controlar o acesso condicional, por exemplo, fazendo com que o usuário faça várias MFA (autenticação multifator) durante a fase de autenticação (inserir um pin do Windows Hello ou que está sendo chamado em seu telefone ou em um aplicativo de autenticação em seus telefones). Em casos em que o necessário multi-factor authentication não é configurá-lo ainda, o usuário pode configurá-lo no momento na mesma caixa de diálogo.  O usuário insere seu número de telefone celular e é instruído a instalar um aplicativo de autenticação e verificar uma marca de QR para adicionar sua conta. Esse servidor controlado por interação é uma ótima experiência!
- Permite ao usuário alterar sua senha nesta caixa de diálogo mesmo quando a senha tiver expirado (fornecer campos adicionais para a senha antiga e a nova senha).
- Habilita a identidade visual do locatário ou o aplicativo (imagens) controladas pelo administrador de locatário do Azure AD / proprietário do aplicativo.
- Permite que os usuários dar consentimento para permitir que o aplicativo acesse recursos / escopos em seu nome apenas após a autenticação.

## <a name="system-web-browser-on-xamarinios-and-xamarinandroid"></a>Navegador da web de sistema no xamarin. IOS e xamarin. Android

Por padrão, MSAL.NET dá suporte a navegador da web do sistema em xamarin. IOS e xamarin. Android. Para hospedar a interação com o STS, ADAL.NET usa apenas o **embedded** navegador da web. Para todas as plataformas que fornecem a interface do usuário (ou seja, não o .NET Core), uma caixa de diálogo é fornecida pela biblioteca de inserção de um controle de navegador da Web. MSAL.NET também usa uma exibição da web incorporadas para a área de trabalho do .NET e o WAB para a plataforma UWP. No entanto, ele utiliza por padrão o **navegador da web sistema** para aplicativos do Xamarin Android e iOS de Xamarin. No iOS, ele escolhe até mesmo a exibição da web para usar dependendo da versão do sistema operacional (iOS12, iOS11 e versões anteriores).

Usando o navegador do sistema tem a vantagem significativa de compartilhar o estado SSO com outros aplicativos e com aplicativos web sem a necessidade de um agente (portal da empresa / autenticador). O navegador do sistema foi usado, por padrão, em que o MSAL.NET para plataformas do Xamarin Android e iOS Xamarin porque, nessas plataformas, o navegador da web de sistema ocupa a tela inteira e a experiência do usuário é melhor. O modo de exibição do sistema da web não é perceptível a partir de uma caixa de diálogo. No iOS, no entanto, o usuário talvez precise dar consentimento para o navegador para o aplicativo, que pode ser inconveniente de retorno de chamada.

### <a name="uwp-does-not-use-the-system-webview"></a>UWP não usa o System Webview

Para aplicativos da área de trabalho, no entanto, iniciar um System Webview leva a uma experiência de usuário abaixo da média, como o usuário vê o navegador, onde elas talvez já tenha outras guias abertos. E quando a autenticação tenha ocorrido, os usuários obtém uma página solicitando que fechar esta janela. Se o usuário não dá atenção, eles podem fechar o processo inteiro (incluindo outras guias, que não estão relacionados à autenticação). Aproveitar o navegador do sistema na área de trabalho também exigiria a abertura de portas locais e escutando-los, que pode exigir permissões avançadas para o aplicativo. Você, como um desenvolvedor, usuário ou administrador, poderá hesitar sobre este requisito.

## <a name="enable-embedded-webviews"></a>Habilitar modos inseridos 
Você também pode habilitar o webviews inserido em aplicativos xamarin. IOS e xamarin. Android. Começando com o MSAL.NET 2.0.0-preview, MSAL.NET também dá suporte ao uso de **embedded** opção webview. Para ADAL.NET, webview inserido é a única opção compatível.

Como um desenvolvedor usando MSAL.NET direcionados ao Xamarin, você pode optar por usar webviews incorporado ou navegadores de sistema. Isso é de sua escolha, dependendo das preocupações de segurança e a experiência de usuário de destino.

Atualmente, MSAL.NET ainda não dá suporte os agentes do Android e iOS. Portanto, se você precisar fornecer logon único (SSO), do navegador do sistema ainda pode ser uma opção melhor. Agentes com o navegador da web incorporadas de suporte está na lista de pendências MSAL.NET.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Diferenças entre o webview incorporado e navegador de sistema 
Há algumas diferenças visuais entre o webview incorporado e navegador de sistema no MSAL.NET.

**Interativo entrar com MSAL.NET usando a exibição da Web incorporado:**

![Inserido](media/msal-net-web-browsers/embedded-webview.png)

**Interativo entrada com MSAL.NET usando o navegador do sistema:**

![Navegador de sistema](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Opções do desenvolvedor

Como um desenvolvedor usando MSAL.NET, você tem várias opções para exibir a caixa de diálogo interativa do STS:

- **Navegador do sistema.** O navegador do sistema é definido por padrão na biblioteca. Se usar o Android, leia [navegadores sistema](msal-net-system-browser-android-considerations.md) para obter informações específicas sobre quais navegadores têm suporte para autenticação. Ao usar o navegador do sistema no Android, é recomendável que o dispositivo tem um navegador compatível com guias personalizadas do Chrome.  Caso contrário, a autenticação poderá falhar. 
- **Webview incorporado.** Para usar somente inserido webview no MSAL.NET, há sobrecargas do `UIParent()` construtor disponível para Android e iOS.

    iOS:

    ```csharp
    public UIParent(bool useEmbeddedWebview)
    ```

    Android:

    ```csharp
    public UIParent(Activity activity, bool useEmbeddedWebview)
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Escolhendo entre o navegador da web incorporadas ou sistema no xamarin. IOS

Em seu aplicativo iOS, no `AppDelegate.cs` você pode usar o navegador de sistema ou inserido webview.

```csharp
// Use only embedded webview
App.UIParent = new UIParent(true);

// Use only system browser
App.UIParent = new UIParent();
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Escolhendo entre o navegador da web incorporadas ou sistema no xamarin. Android

Em seu aplicativo Android, no `MainActivity.cs` você pode decidir como implementar as opções de exibição da Web.

```csharp
// Use only embedded webview
App.UIParent = new UIParent(Xamarin.Forms.Forms.Context as Activity, true);

// or
// Use only system browser
App.UIParent = new UIParent(Xamarin.Forms.Forms.Context as Activity);
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Detectando a presença das guias personalizadas em xamarin. Android

Se você quiser usar o navegador da web de sistema para habilitar o SSO com aplicativos em execução no navegador, mas estiver preocupado com a experiência do usuário para dispositivos Android não ter um navegador com suporte da guia personalizada, você tem a opção de decidir, chamando o `IsSystemWebViewAvailable()` método na < c 2 > `UIParent` . Esse método retornará `true` se o PackageManager detecta guias personalizadas e `false` se eles não são detectados no dispositivo.

Com base no valor retornado por esse método e seus requisitos, você pode tomar uma decisão:

- Você pode retornar uma mensagem de erro personalizada para o usuário. Por exemplo:  "Instalar o Chrome para continuar com a autenticação" - OR-
- Você pode fazer fallback para a opção de exibição da Web incorporado e inicie a interface do usuário como uma exibição da Web incorporada.

O código a seguir mostra a opção de exibição da Web incorporado:

```csharp
bool useSystemBrowser = UIParent.IsSystemWebviewAvailable();
if (useSystemBrowser)
{
    // A browser with custom tabs is present on device, use system browser
    App.UIParent = new UIParent(Xamarin.Forms.Forms.Context as Activity);
}
else
{
    // A browser with custom tabs is not present on device, use embedded webview
    App.UIParent = new UIParent(Xamarin.Forms.Forms.Context as Activity, true);
}

// Alternative:
App.UIParent = new UIParent(Xamarin.Forms.Forms.Context as Activity, !useSystemBrowser);

```

## <a name="net-core-does-not-support-interactive-authentication"></a>.NET core não dá suporte à autenticação interativa

Para o .NET Core, aquisição de tokens de forma interativa não está disponível. Na verdade, o .NET Core não fornece interface do usuário ainda. Se você quiser fornecer entrada interativa para um aplicativo .NET Core, você pode permitir que o aplicativo apresentar ao usuário um código e uma URL para ir para entrar no modo interativo (consulte [fluxo de código de dispositivo](msal-authentication-flows.md#device-code)).