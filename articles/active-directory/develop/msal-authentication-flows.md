---
title: Fluxos de autenticação (biblioteca de autenticação da Microsoft) | Azure
description: Saiba mais sobre as fluxos/concessões de autenticação usadas pela biblioteca de autenticação (MSAL) da Microsoft.
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
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39f323c2ac86e8d42319b3d99221f6c20beff3e4
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406681"
---
# <a name="authentication-flows"></a>Fluxos de autenticação

Este artigo descreve os fluxos de autenticação diferentes fornecidos pela biblioteca de autenticação da Microsoft (MSAL).  Esses fluxos podem ser usados em uma variedade de cenários de aplicativo diferente.

| Flow | Descrição | Usado em|  
| ---- | ----------- | ------- | 
| [Interativo](#interactive) | Obtém o token por meio de um processo interativo que solicita ao usuário credenciais por meio de um navegador ou a janela pop-up. | [Aplicativos da área de trabalho](scenario-desktop-overview.md), [aplicativos móveis](scenario-mobile-overview.md) |
| [Concessão implícita](#implicit-grant) | Permite que o aplicativo obter tokens sem executar uma troca de credenciais do servidor de back-end. Isso permite que o aplicativo autentique o usuário, mantenha a sessão e obtenha tokens para outras APIs da Web, tudo dentro do código JavaScript do cliente.| [Aplicativos de página única (SPA)](scenario-spa-overview.md) |
| [Código de autorização](#authorization-code) | Usado em aplicativos que são instalados em um dispositivo para obter acesso a recursos protegidos, como APIs da web. Isso permite que você adicionar entrada e acesso à API aos seus aplicativos móveis e da área de trabalho. | [Aplicativos da área de trabalho](scenario-desktop-overview.md), [aplicativos móveis](scenario-mobile-overview.md), [aplicativos Web](scenario-web-app-call-api-overview.md) | 
| [On-behalf-of](#on-behalf-of) | Um aplicativo chama uma serviço/API web, que por sua vez precisa chamar outro serviço/API web. A ideia é propagar as permissões e identidade de usuário delegado por meio da cadeia de solicitações. | [APIs da Web](scenario-web-api-call-api-overview.md) |
| [Credenciais do cliente](#client-credentials) | Permite que você acesse recursos hospedados na web usando a identidade de um aplicativo. Normalmente usado para interações de servidor para servidor que devem ser executado em segundo plano, sem interação imediata com um usuário. | [Aplicativos daemon](scenario-daemon-overview.md) |
| [Código de dispositivo](#device-code) | Permite que os usuários entrarem dispositivos restritos a entrada como uma smart TV, dispositivo IoT ou impressora. | [Aplicativos de área de trabalho/móvel](scenario-desktop-acquire-token.md#command-line-tool-without-web-browser) |
| [Autenticação Integrada do Windows](scenario-desktop-acquire-token.md#integrated-windows-authentication) | Permite que aplicativos no Azure AD ou de domínio Unido computadores para adquirir um token silenciosamente (sem nenhuma interação da interface do usuário do usuário).| [Aplicativos de área de trabalho/móvel](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [Nome de usuário/senha](scenario-desktop-acquire-token.md#username--password) | Permite que um aplicativo para a entrada do usuário manipulando diretamente sua senha. Este fluxo não é recomendado. | [Aplicativos de área de trabalho/móvel](scenario-desktop-acquire-token.md#username--password) | 

## <a name="interactive"></a>Interativo
A MSAL oferece suporte a capacidade de solicitar interativamente ao usuário suas credenciais para entrar e obter um token usando essas credenciais.

![Fluxo interativo](media/msal-authentication-flows/interactive.png)

Para obter mais informações sobre como usar MSAL.NET interativamente adquirir tokens em plataformas específicas, leia o seguinte:
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [Plataforma Universal do Windows](msal-net-uwp-considerations.md)

Para obter mais informações sobre chamadas interativas no msal, leia [solicitará o comportamento em solicitações interativas de msal](msal-js-prompt-behavior.md)

## <a name="implicit-grant"></a>Concessão implícita

A MSAL dá suporte a [fluxo de concessão implícita do OAuth 2](v2-oauth2-implicit-grant-flow.md), que permite que o aplicativo obter tokens de plataforma de identidade da Microsoft sem executar um servidor de back-end exchange de credencial. Isso permite que o aplicativo autentique o usuário, mantenha a sessão e obtenha tokens para outras APIs da Web, tudo dentro do código JavaScript do cliente.

![Fluxo de concessão implícita](media/msal-authentication-flows/implicit-grant.svg)

Muitos aplicativos web modernos são criados como aplicativos de página única do lado do cliente escritos usando JavaScript ou uma estrutura de SPA como Angular, VUE e React. js. Esses aplicativos executados em um navegador da web e têm características de autenticação diferente de aplicativos web tradicionais do lado do servidor. A plataforma de identidade da Microsoft permite que os aplicativos de página única conectar usuários e obter tokens para acessar os serviços de back-end ou usando o fluxo de concessão implícita de APIs da web. O fluxo implícito permite que o aplicativo obter tokens de ID para representar o usuário autenticado e também o necessário para chamar as APIs protegidas de tokens de acesso.

Esse fluxo de autenticação não inclui cenários de aplicativos usando estruturas de JavaScript de plataforma cruzada como Electron e React-Native, já que exigem mais recursos para interação com plataformas nativas.

## <a name="authorization-code"></a>Código de autorização
A MSAL dá suporte a [concessão de código de autorização do OAuth 2](v2-oauth2-auth-code-flow.md), que pode ser usado em aplicativos que são instalados em um dispositivo para obter acesso a recursos protegidos, como APIs da web. Isso permite que você adicionar entrada e acesso à API aos seus aplicativos móveis e da área de trabalho. 

Quando os usuários entram aplicativos da web (sites da web), o aplicativo web recebe um código de autorização.  O código de autorização é resgatado para adquirir um token para chamar APIs da web. No ASP.NET / aplicativos web, o único objetivo de núcleo do ASP.NET `AcquireTokenByAuthorizationCode` é adicionar um token ao cache de token, para que ele pode ser usado pelo aplicativo (normalmente em controladores) que apenas obter um token para uma API usando `AcquireTokenSilent`.

![Fluxo de código de autorização](media/msal-authentication-flows/authorization-code.png)

1. Solicita um código de autorização, que é resgatado para um token de acesso.
2. Usa o token de acesso para chamar uma API da web.

### <a name="considerations"></a>Considerações
- O código de autorização é utilizável apenas uma vez resgatar um token. Não tente adquirir um token várias vezes com o mesmo código de autorização (ele é explicitamente proibido pela especificação do padrão de protocolo). Se você resgatar o código várias vezes intencionalmente ou porque você não estiver ciente de que uma estrutura também faz isso para você, você obterá um erro: `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- Se você estiver escrevendo um aplicativo ASP.NET/ASP.NET Core, isso pode acontecer que se você não informar o framework ASP.NET/Core que você já resgatou o código de autorização. Para isso, você precisará chamar `context.HandleCodeRedemption()` método da `AuthorizationCodeReceived` manipulador de eventos.

- Evite compartilhar o token de acesso com o ASP.NET, que pode impedir que o consentimento incremental ocorrendo corretamente.  Para obter mais informações, consulte [emitir 693 #](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693).

## <a name="on-behalf-of"></a>On-behalf-of

A MSAL dá suporte a [fluxo de autenticação em nome do OAuth 2](v2-oauth2-on-behalf-of-flow.md).  Esse fluxo é usado quando um aplicativo chama uma serviço/API web, que por sua vez precisa chamar outro serviço/API web. A ideia é propagar as permissões e identidade de usuário delegado por meio da cadeia de solicitações. Para o serviço de camada intermediária fazer solicitações autenticadas para o serviço downstream, ele precisa proteger um token de acesso da plataforma Microsoft identity, em nome do usuário.

![Fluxo em nome de](media/msal-authentication-flows/on-behalf-of.png)

1. Adquire um token de acesso para a API Web
2. Um cliente (Web, móvel, aplicativo de página única área de trabalho,) chama uma API Web protegida, adicionando o token de acesso como um token de portador no cabeçalho de autenticação da solicitação HTTP. A API da Web autentica o usuário.
3. Quando o cliente chama a API da Web, a API da Web solicita outro token em nome de usuário.  
4. A API Web protegida usa esse token para chamar uma API Web downstream em nome de usuário.  A API da Web também mais tarde pode solicitar tokens para outras APIs de downstream (mas ainda em nome do usuário mesmo).

## <a name="client-credentials"></a>Credenciais de cliente

A MSAL dá suporte a [fluxo de credenciais de cliente OAuth 2](v2-oauth2-client-creds-grant-flow.md). Esse fluxo permite que você acesse recursos hospedados na web usando a identidade de um aplicativo. Esse tipo de concessão normalmente é usado para interações de servidor para servidor que devem ser executadas em segundo plano, sem interação imediata com um usuário. Esses tipos de aplicativos são geralmente denominados daemons ou contas de serviço. 

As credenciais do cliente conceder permite que o fluxo de um serviço web (cliente confidencial) para usar suas próprias credenciais, em vez de representar um usuário para autenticação quando chama outro serviço web. Nesse cenário, o cliente é geralmente um serviço Web de camada intermediária, um serviço daemon ou um site. Para um nível mais alto de garantia, a plataforma de identidade da Microsoft também permite que o serviço de chamada use um certificado (em vez de um segredo compartilhado) como uma credencial.

> [!NOTE]
> O fluxo de cliente confidencial não está disponível nas plataformas móveis (UWP, xamarin. IOS e xamarin. Android), uma vez que eles somente oferecem suporte a aplicativos de cliente público.  Aplicativos de cliente público não sabem como provar a identidade do aplicativo para o provedor de identidade. Uma conexão segura pode ser obtido no aplicativo web ou API da web back-ends, ao implantar um certificado.

MSAL.NET dá suporte a dois tipos de credenciais do cliente. Essas credenciais de cliente precisam ser registrados com o Azure AD. As credenciais são passadas em para os construtores do aplicativo cliente confidencial em seu código.

### <a name="application-secrets"></a>Segredos do aplicativo 

![Cliente confidencial com senha](media/msal-authentication-flows/confidential-client-password.png)

1. Adquire um token usando credenciais de segredo e a senha de aplicativo.
2. Usa o token para fazer solicitações de recurso.

### <a name="certificates"></a>Certificados 

![Cliente confidencial com certificado](media/msal-authentication-flows/confidential-client-certificate.png)

1. Adquire um token usando as credenciais de certificado.
2. Usa o token para fazer solicitações de recurso.

Essas credenciais de cliente precisam ser:
- Registrado com o Azure AD.
- Passado na construção do aplicativo cliente confidencial em seu código.


## <a name="device-code"></a>Código de dispositivo
A MSAL dá suporte a [OAuth 2 fluxo de código de dispositivo](v2-oauth2-device-code.md), que permite aos usuários entrar em dispositivos restritos a entrada como uma smart TV, dispositivo IoT ou impressora. A autenticação interativa com o Azure AD requer um navegador da web. O fluxo de código de dispositivo permite que o usuário use outro dispositivo (por exemplo, outro computador ou um telefone celular) para entrar no modo interativo em que o dispositivo ou sistema operacional não fornece um navegador da Web.

Usando o fluxo de código do dispositivo, o aplicativo obtém tokens por meio de um processo de duas etapas, projetado especialmente para esse dispositivos/sistema operacional. Exemplos de tais aplicativos são aplicativos executados em dispositivos iOT ou ferramentas de linha de comando (CLI). 

![Fluxo de código do dispositivo](media/msal-authentication-flows/device-code.png)

1. Sempre que a autenticação do usuário é necessária, o aplicativo fornece um código e pede ao usuário para usar outro dispositivo (como um smartphone conectado à internet) para navegar até uma URL (por exemplo, https://microsoft.com/devicelogin), em que o usuário será solicitado a inserir o código. Se feito, a página da web levará o usuário por meio de uma experiência de autenticação normal, incluindo solicitações de consentimento e a autenticação multifator, se necessário.

2. Após a autenticação bem-sucedida, o aplicativo de linha de comando receberá os símbolos necessários por meio de um canal de retorno e os usará para executar as chamadas à API da web que precisa.

### <a name="constraints"></a>Restrições

- Fluxo de código do dispositivo só está disponível em aplicativos de cliente público.
- A autoridade passados ao construir o aplicativo cliente público deve ser:
  - com locatários (no formato `https://login.microsoftonline.com/{tenant}/` onde `{tenant}` é o GUID que representa a ID de locatário ou em um domínio associado ao locatário.
  - ou então, todas as contas corporativas e de Estudante (`https://login.microsoftonline.com/organizations/`).
- Contas pessoais da Microsoft ainda não têm suporte pelo ponto de extremidade v2.0 do Azure AD (não é possível usar o `/common` ou `/consumers` locatários).

## <a name="integrated-windows-authentication"></a>Autenticação Integrada do Windows
A MSAL dá suporte à autenticação integrada do Windows (IWA) para área de trabalho ou aplicativos móveis que são executados em um Azure AD ou ingressado no domínio ingressado no computador do Windows. Usando a IWA, esses aplicativos podem adquirir um token silenciosamente (sem nenhuma interação da interface do usuário do usuário). 

![Autenticação Integrada do Windows](media/msal-authentication-flows/integrated-windows-authentication.png)

1. Adquire um token usando a autenticação integrada do Windows.
2. Usa o token para fazer solicitações de recurso.

### <a name="constraints"></a>Restrições

Dá suporte a IWA **federados** somente usuários.  Os usuários criados em um Active Directory e com suporte pelo Azure Active Directory. Os usuários criados diretamente no Azure AD, sem suporte do AD (**gerenciados** usuários) não é possível usar esse fluxo de autenticação. Essa limitação não afeta a [fluxo do nome de usuário/senha](#usernamepassword).

IWA é para aplicativos escritos para plataformas do .NET Framework, .NET Core e a plataforma Universal do Windows.

IWA sem ignorar o MFA (multi-factor authentication). Se MFA estiver configurada, IWA poderá falhar se um desafio de MFA é necessário porque a MFA requer interação do usuário. Esse é complicado. IWA não é interativo, mas a autorização de dois fatores (2FA) exige interatividade de usuário. Você não controlar quando o provedor de identidade solicita 2FA a ser executada, o administrador do locatário faz. De nosso observações 2FA é necessário quando você fizer logon em um país diferente, quando conectados por meio de VPN a uma rede corporativa e, às vezes, mesmo quando conectado por meio de VPN. Não espere um determinístico conjunto de regras, Active Directory do Azure usa inteligência Artificial para aprender continuamente se 2FA é necessária. Você deve fazer fallback para um prompt do usuário (https://aka.ms/msal-net-interactive) se IWA falhar.

A autoridade passados ao construir o aplicativo cliente público deve ser:
- com locatários (no formato `https://login.microsoftonline.com/{tenant}/` onde `tenant` é o guid que representa a ID de locatário ou em um domínio associado ao locatário.
- para qualquer trabalho e contas corporativas (`https://login.microsoftonline.com/organizations/`). Não há suporte para contas pessoais da Microsoft (não é possível usar `/common` ou `/consumers` locatários).

Como IWA é um fluxo silencioso:
- o usuário do seu aplicativo deve ter consentido anteriormente para usar o aplicativo. 
- ou, o administrador de locatário deve ter consentido anteriormente a todos os usuários no locatário para usar o aplicativo.
- Isso significa que:
    - ou você como desenvolvedor ter pressionado a **Grant** botão no portal do Azure por conta própria, 
    - ou, um administrador de locatários pressionou a **conceder/revogar o consentimento do administrador de domínio do locatário {}** botão na **permissões de API** guia do registro para o aplicativo (consulte [adicionar permissões para acessem APIs web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis))
    - ou, você forneceu uma maneira para os usuários dar consentimento ao aplicativo (consulte [solicitar o consentimento do usuário individual](v2-permissions-and-consent.md#requesting-individual-user-consent))
    - ou, você forneceu uma maneira para que o administrador de locatários dar consentimento para o aplicativo (consulte [consentimento do administrador](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant))

O fluxo IWA está habilitado para a área de trabalho do .NET, .NET Core e aplicativos da plataforma Universal do Windows. No .NET Core apenas a sobrecarga de colocar o nome de usuário está disponível como a plataforma .NET Core não pode solicitar o nome de usuário para o sistema operacional.
  
Para obter mais informações sobre o consentimento, consulte [v 2.0 permissões e consentimento](v2-permissions-and-consent.md).

## <a name="usernamepassword"></a>Nome de usuário/senha 
A MSAL dá suporte a [concessão de credenciais de senha de proprietário de recurso OAuth 2](v2-oauth-ropc.md), que permite que um aplicativo para a entrada do usuário manipulando diretamente sua senha. Em seu aplicativo da área de trabalho, você pode usar o fluxo de nome de usuário e senha para adquirir um token silenciosamente. Nenhuma interface do usuário é necessária ao usar o aplicativo.

![Fluxo de nome de usuário/senha](media/msal-authentication-flows/username-password.png)

1. Adquire um token, enviando o nome de usuário e a senha para o provedor de identidade.
2. Chama uma API da web usando o token.

> [!WARNING]
> Esse fluxo é **não recomendável** porque ele requer um alto grau de exposição de confiança e o usuário.  Você deve usar apenas esse fluxo quando fluxos de outros, mais seguros, não podem ser usados. Para obter mais informações sobre esse problema, consulte [deste artigo](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

É o fluxo preferencial para adquirir um token silenciosamente em máquinas do Windows ingressados no domínio [autenticação integrada do Windows](#integrated-windows-authentication). Caso contrário, você também pode usar [fluxo de código do dispositivo](#device-code)

Embora isso seja útil em alguns casos (cenários de DevOps), se você quiser usar o nome de usuário e senha em cenários interativos em que você fornece sua própria interface do usuário, você deve realmente pensar sobre como mover para longe dela. Usando o nome de usuário e senha, você estiver dando-up várias coisas:
- principais locatários de identidade moderno: senha obtém fished, reproduzidos. Como temos esse conceito de um segredo de compartilhamento pode ser interceptado.
Isso é incompatível com sem senha.
- os usuários que precisem de MFA não conseguirá entrar (pois não há nenhuma interação)
- Os usuários não poderão logon único

### <a name="constraints"></a>Restrições

Além do [restrições de autenticação integrada do Windows](#integrated-windows-authentication), as seguintes restrições também se aplicam:

- O fluxo de nome de usuário e senha não é compatível com o acesso condicional e autenticação multifator: Como consequência, se seu aplicativo for executado em um locatário do Azure AD em que o administrador do locatário requer a autenticação multifator, é possível usar esse fluxo. Muitas organizações fazem isso.
- Ele funciona somente para o trabalho e contas de Estudante (não MSA)
- O fluxo está disponível na área de trabalho do .NET e .NET core, mas não em plataforma Universal do Windows.

### <a name="azure-ad-b2c-specifics"></a>Especificações do Azure AD B2C

Para obter mais informações sobre como usar MSAL.NET e o Azure AD B2C, leia [ROPC usando com o Azure AD B2C (MSAL.NET)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c).
