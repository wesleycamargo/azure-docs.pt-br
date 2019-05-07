---
title: Considerações sobre o Xamarin iOS (biblioteca de autenticação do Microsoft para .NET) | Azure
description: Saiba mais sobre as considerações específicas ao usar o Xamarin iOS com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
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
ms.openlocfilehash: 7621b18f32a90b81dc075c534367981cf44f16e0
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076324"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>Considerações de específicas do iOS Xamarin com MSAL.NET
No Xamarin iOS, há várias considerações que você deve levar em conta ao usar MSAL.NET

- [Problemas conhecidos com o iOS 12 e autenticação](#known-issues-with-ios-12-and-authentication)
- [Substituir e implementar o `OpenUrl` funcionar a `AppDelegate`](#implement-openurl)
- [Habilitar grupos de conjunto de chaves](#enable-keychain-groups)
- [Habilitar o compartilhamento de cache de token](#enable-token-cache-sharing-across-ios-applications)
- [Habilitar o acesso ao conjunto de chaves](#enable-keychain-access)

## <a name="known-issues-with-ios-12-and-authentication"></a>Problemas conhecidos com o iOS 12 e autenticação
A Microsoft lançou um [comunicado de segurança](https://github.com/aspnet/AspNetCore/issues/4647) para fornecer informações sobre uma incompatibilidade entre iOS12 e alguns tipos de autenticação. A incompatibilidade quebras social, WSFed e OIDC logons. Este comunicado também fornece orientação sobre o que os desenvolvedores podem fazer para remover as restrições de segurança atual adicionadas pelo ASP.NET para seus aplicativos se tornem compatíveis com iOS12.  

Ao desenvolver aplicativos MSAL.NET no Xamarin iOS, você pode ver um loop infinito ao tentar entrar sites do iOS 12 (semelhante a esta [problema ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329). 

Você também poderá ver uma quebra na autenticação OIDC do ASP.NET Core com 12 do iOS Safari, conforme descrito nesta [WebKit problema](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="implement-openurl"></a>Implementar OpenUrl

Primeiro você precisa substituir os `OpenUrl` método da `FormsApplicationDelegate` derivado da classe e chame `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs`.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Você também precisa definir um esquema de URL, exigem permissões para seu aplicativo chamar outro aplicativo, ter uma forma específica para a URL de redirecionamento e registrar a URL de redirecionamento na [portal do Azure](https://portal.azure.com).

## <a name="enable-keychain-groups"></a>Habilitar grupos de conjunto de chaves

Para tornar o token de trabalho de cache e têm o `AcquireTokenSilentAsync` trabalho de método, várias etapas devem ser seguidas:
1. Habilitar o acesso ao conjunto de chaves em seu *`* Entitlements.plist* de arquivo e especifique o **grupos de conjunto de chaves** em seu identificador de pacote.
2. Selecione *`*Entitlements.plist*`* de arquivo no **direitos personalizados** campo da janela do iOS projeto opções **exibição de assinatura de pacote**.
3. Quando um certificado de assinatura, verifique se o que Xcode usa a mesma ID da Apple.

## <a name="enable-token-cache-sharing-across-ios-applications"></a>Habilitar o cache de token de compartilhamento entre aplicativos iOS

A partir do MSAL 2.x, você pode especificar um grupo de segurança do conjunto de chaves a ser usado para persistir o cache de token em vários aplicativos. Isso permite que você possa compartilhar entre vários aplicativos tendo o mesmo grupo de segurança do conjunto de chaves incluindo aqueles desenvolvidos com o cache de token [ADAL.NET](https://aka.ms/adal-net), xamarin. IOS de MSAL.NET e aplicativos nativos de iOS desenvolvidos com o [ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) ou [MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc)).

O cache de token de compartilhamento permite logon único (SSO) entre todos os aplicativos que usam o mesmo grupo de segurança do conjunto de chaves.

Para habilitar o logon único, você precisará definir o `PublicClientApplication.iOSKeychainSecurityGroup` propriedade para o mesmo valor em todos os aplicativos.

Um exemplo disso usando MSAL v3 seria:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.msalrocks")
     .Build();
```

Um exemplo disso usando MSAL v2.7.x seria:

```csharp
PublicClientApplication.iOSKeychainSecurityGroup = "com.microsoft.msalrocks";
```

> [!NOTE]
> O `KeychainSecurityGroup` propriedade foi preterida. Anteriormente, no MSAL 2. x, os desenvolvedores eram forçados a incluir o prefixo de TeamId ao usar o `KeychainSecurityGroup` propriedade. 
> 
> Agora, a partir do MSAL 2.7.x, MSAL resolverá o prefixo TeamId durante o tempo de execução ao usar o `iOSKeychainSecurityGroup` propriedade. Ao usar essa propriedade, o valor não deve conter o prefixo TeamId. 
> 
> Usar o novo `iOSKeychainSecurityGroup` propriedade, que não exige que os desenvolvedores forneçam o TeamId. O `KeychainSecurityGroup` propriedade agora é obsoleta. 

## <a name="enable-keychain-access"></a>Habilitar o acesso ao conjunto de chaves

Na MSAL 2. x e 4. x ADAL, o TeamId é usado para acessar o conjunto de chaves, que permite que as bibliotecas de autenticação fornecer logon único (SSO) entre os aplicativos do mesmo editor. 

O que é o [TeamIdentifierPrefix](/xamarin/ios/deploy-test/provisioning/entitlements?tabs=vsmac) (TeamId)? É um identificador exclusivo (da empresa ou pessoal) na App Store. A AppId é exclusiva para um aplicativo. Se você tiver mais de um aplicativo, o TeamId para todos os aplicativos serão os mesmos, mas o AppId será diferente. O grupo de acesso ao conjunto de chaves é prefixado por TeamId automaticamente para cada grupo pelo sistema. É como o sistema operacional impõe que os aplicativos do mesmo editor podem acessar o conjunto de chaves compartilhado. 

Ao inicializar o `PublicClientApplication`, se você receber um `MsalClientException` com a mensagem: `TeamId returned null from the iOS keychain...`, você precisará fazer o seguinte no aplicativo do Xamarin iOS:

1. No VS, na guia de depuração, vá para nameOfMyApp.iOS propriedades...
2. Vá para a assinatura do pacote iOS 
3. Em direitos personalizados, clique em de... e selecione o arquivo Entitlements. plist do seu aplicativo
4. No arquivo csproj do aplicativo iOS, você deve ter essa linha agora incluída: `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. **Recompilar** o projeto.

Isso é *Além disso* para habilitar o acesso ao conjunto de chaves no `Entitlements.plist` de arquivo, usando o abaixo do grupo de acesso ou seu próprio:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
</plist>
```

## <a name="next-steps"></a>Próximas etapas

Mais detalhes são fornecidos a [considerações específicas do iOS](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations) parágrafo do arquivo readme.md do exemplo a seguir:

Amostra | Plataforma | DESCRIÇÃO 
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Um aplicativo Xamarin Forms simples, mostrando como usar MSAL para autenticar a MSA e o Azure AD por meio do ponto de extremidade V2.0 do AAD e acessar o Microsoft Graph com o token resultante. <br>![Topologia](media/msal-net-xamarin-ios-considerations/topology.png)