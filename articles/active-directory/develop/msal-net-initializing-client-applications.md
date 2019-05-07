---
title: Inicializar os aplicativos cliente (biblioteca de autenticação do Microsoft para .NET) | Azure
description: Saiba mais sobre a inicialização de cliente público e aplicativos de cliente confidencial usando a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
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
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6197d472bdfaf03c9f99baa7691354e735cc91e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65075799"
---
# <a name="initialize-client-applications-using-msalnet"></a>Inicializar os aplicativos cliente usando MSAL.NET
Este artigo descreve como inicializar o cliente público e aplicativos de cliente confidencial usando a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).  Para saber mais sobre os tipos de aplicativos cliente e as opções de configuração de aplicativo, leia as [visão geral](msal-client-applications.md).

Com MSAL.NET 3. x, a maneira recomendada para criar uma instância de um aplicativo está usando os construtores de aplicativo: `PublicClientApplicationBuilder` e `ConfidentialClientApplicationBuilder`. Eles oferecem um mecanismo poderoso para configurar o aplicativo de código, ou em um arquivo de configuração ou até mesmo por combinação de ambas as abordagens.

## <a name="prerequisites"></a>Pré-requisitos
Antes de inicializar um aplicativo, primeiro você precisa [registrá-lo](quickstart-register-app.md) para que seu aplicativo pode ser integrado com a plataforma de identidade da Microsoft.  Após o registro, talvez seja necessário as informações a seguir (que podem ser encontradas no portal do Azure):

- A ID do cliente (uma cadeia de caracteres que representa um GUID)
- A URL do provedor de identidade (a instância nomeada) e o público entrar para seu aplicativo. Esses dois parâmetros são coletivamente conhecidos como a autoridade.
- A ID do locatário se você estiver escrevendo um aplicativo de linha de negócios exclusivamente para sua organização (também chamado aplicativo de locatário único).
- O segredo do aplicativo (cadeia de segredo do cliente) ou o certificado (do tipo X509Certificate2) se for um aplicativo cliente confidencial.
- Para aplicativos web e, às vezes, para aplicativos de cliente público (em especial quando o aplicativo precisa usar um agente), você vai ter também definir o redirectUri em que o provedor de identidade entrará em contato com back seu aplicativo com os tokens de segurança.

## <a name="ways-to-initialize-applications"></a>Maneiras de inicializar aplicativos
Há muitas maneiras diferentes de criar uma instância de aplicativos cliente.

### <a name="initializing-a-public-client-application-from-code"></a>Inicializando um aplicativo cliente público do código

O código a seguir cria uma instância de um aplicativo cliente público, usuários de entrar na nuvem público do Microsoft Azure, com seu trabalho e contas de estudante ou suas contas pessoais da Microsoft.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>Inicializando um aplicativo cliente confidencial do código

Da mesma forma, o código a seguir cria uma instância de um aplicativo confidencial (um aplicativo Web localizado em `https://myapp.azurewebsites.net`) manipulação de tokens de usuários na nuvem público do Microsoft Azure, com seu trabalho e contas de estudante ou suas contas pessoais da Microsoft. O aplicativo é identificado com o provedor de identidade ao compartilhar um segredo do cliente:

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

Como você deve saber, em produção, em vez de usar um segredo do cliente, você talvez queira compartilhar, com o Azure AD, um certificado. O código seria o seguinte:

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>Inicializando um aplicativo cliente público das opções de configuração

O código a seguir cria uma instância de um aplicativo cliente público de um objeto de configuração, que pode ser preenchido programaticamente ou ler de um arquivo de configuração:

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>Inicializando um aplicativo cliente confidencial das opções de configuração

O mesmo tipo de padrão se aplica a aplicativos cliente confidencial. Você também pode adicionar outros parâmetros usando `.WithXXX` modificadores (um certificado aqui).

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>Modificadores de construtor

Nos trechos de código usando os criadores de aplicativos, um número de `.With` métodos podem ser aplicados como modificadores (por exemplo, `.WithCertificate` e `.WithRedirectUri`). 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>Modificadores comuns para aplicativos públicos e os segredos do cliente

Os modificadores que você pode definir em um cliente público ou o construtor do aplicativo cliente confidencial são:

|Parâmetro | DESCRIÇÃO|
|--------- | --------- |
|`.WithAuthority()` substituições de 7 | Define a autoridade de padrão de aplicativo a uma autoridade do AD do Azure, com a possibilidade de escolha de nuvem do Azure, o público-alvo, o locatário (locatário ID ou nome de domínio), ou diretamente fornecendo o URI de autoridade.|
|`.WithAdfsAuthority(string)` | Define a autoridade de padrão de aplicativo para ser uma autoridade do AD FS.|
|`.WithB2CAuthority(string)` | Define a autoridade de padrão de aplicativo para ser uma autoridade do Azure AD B2C.|
|`.WithClientId(string)` | Substitui a ID do cliente.|
|`.WithComponent(string)` | Define o nome da biblioteca usando MSAL.NET (por motivos de telemetria). |
|`.WithDebugLoggingCallback()` | Se chamado, o aplicativo chama `Debug.Write` simplesmente habilitando rastreamentos de depuração. Ver [registro em log](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) para obter mais informações.|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | Defina os parâmetros de consulta adicional de nível de aplicativo que serão enviados em todas as solicitações de autenticação. Isso é substituível em cada nível de método de aquisição de token (com o mesmo `.WithExtraQueryParameters pattern`).|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | Permite cenários como a configuração para um proxy HTTP, ou para forçar a MSAL para usar um HttpClient específico (por exemplo, em aplicativos de web do ASP.NET Core/APIs) avançados.|
|`.WithLogging()` | Se chamado, o aplicativo chama um retorno de chamada com rastreamentos de depuração. Ver [registro em log](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) para obter mais informações.|
|`.WithRedirectUri(string redirectUri)` | Substitui o URI de redirecionamento padrão. No caso de aplicativos de cliente público, isso será útil para cenários que envolvem o broker.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | Define o delegado usado para enviar a telemetria.|
|`.WithTenantId(string tenantId)` | Substitui a ID de locatário ou a descrição de locatário.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Modificadores específicos para aplicativos xamarin. IOS

Os modificadores que você pode definir em um construtor de aplicativo cliente público no xamarin. IOS são:

|Parâmetro | DESCRIÇÃO|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Xamarin. IOS somente**: Define o grupo de segurança de conjunto de chaves do iOS (para a persistência de cache).|

### <a name="modifiers-specific-to-confidential-client-applications"></a>Modificadores específicos para aplicativos de cliente confidencial

Os modificadores que você pode definir em um construtor de aplicativo cliente confidencial são:

|Parâmetro | DESCRIÇÃO|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Define o certificado que identifica o aplicativo com o Azure AD.|
|`.WithClientSecret(string clientSecret)` | Define o segredo do cliente (senha de aplicativo) identifica o aplicativo com o Azure AD.|

Esses modificadores são mutuamente exclusivos. Se você fornecer tanto, a MSAL lançará uma exceção significativa.

### <a name="example-of-usage-of-modifiers"></a>Exemplo de uso dos modificadores

Vamos supor que seu aplicativo é um aplicativo de linha de negócios, que é apenas para sua organização.  Em seguida, você pode escrever:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

Onde ele se torna interessante é que agora simplificou programação para nuvens nacionais. Se você desejar que o aplicativo seja um aplicativo multilocatário em uma nuvem nacional, você poderia escrever, por exemplo:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

Também é uma substituição para o AD FS (AD FS 2019 atualmente não há suporte para):
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Por fim, se você for um desenvolvedor do Azure AD B2C, você pode especificar seu locatário como este:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```
