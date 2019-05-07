---
title: Criar uma instância de um aplicativo cliente confidencial com opções (biblioteca de autenticação do Microsoft para .NET) | Azure
description: Saiba como criar uma instância de um aplicativo cliente confidencial com opções de configuração usando a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
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
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60b3b2fa2cdb7808e45f9142f1c1a351f470af50
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158829"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Criar uma instância de um aplicativo cliente confidencial com opções de configuração usando MSAL.NET

Este artigo descreve como criar uma instância de um [aplicativo cliente confidencial](msal-client-applications.md) usando a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).  O aplicativo é instanciado com as opções de configuração definidas em um arquivo de configurações.

Antes de inicializar um aplicativo, primeiro você precisa [registrar](quickstart-register-app.md) -lo para que seu aplicativo pode ser integrado com a plataforma de identidade da Microsoft. Após o registro, talvez seja necessário as informações a seguir (que podem ser encontradas no portal do Azure):

- A ID do cliente (uma cadeia de caracteres que representa um GUID)
- A URL do provedor de identidade (a instância nomeada) e o público entrar para seu aplicativo. Esses dois parâmetros são coletivamente conhecidos como a autoridade.
- A ID do locatário se você estiver escrevendo um aplicativo de linha de negócios exclusivamente para sua organização (também chamado aplicativo de locatário único).
- O segredo do aplicativo (cadeia de segredo do cliente) ou o certificado (do tipo X509Certificate2) se for um aplicativo cliente confidencial.
- Para aplicativos web e, às vezes, para aplicativos de cliente público (em especial quando o aplicativo precisa usar um agente), você vai ter também definir o redirectUri em que o provedor de identidade entrará em contato com back seu aplicativo com os tokens de segurança.

## <a name="configure-the-application-from-the-config-file"></a>Configurar o aplicativo do arquivo de configuração
O nome das propriedades das opções no MSAL.NET corresponder ao nome das propriedades do `AzureADOptions` no ASP.NET Core, portanto, você não precisa escrever nenhum código de cola.

Uma configuração de aplicativo do ASP.NET Core é descrita em uma *appSettings. JSON* arquivo:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
    "TenantId": "[Enter 'common', or 'organizations' or the Tenant Id (Obtained from the Azure portal. Select 'Endpoints' from the 'App registrations' blade and use the GUID in any of the URLs), e.g. da41245a5-11b3-996c-00a8-4d99re19f292]",
    "ClientId": "[Enter the Client Id (Application ID obtained from the Azure portal), e.g. ba74781c2-53c2-442a-97c2-3d60re42f403]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    "ClientSecret": "[Copy the client secret added to the app from the Azure portal]"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

A partir de MSAL.NET v3, você pode configurar seu aplicativo cliente confidencial do arquivo de configuração. As classes relacionadas à configuração de aplicativo estão localizadas no `Microsoft.Identity.Client.AppConfig` namespace.

Na classe em que deseja configurar e criar uma instância de seu aplicativo, você precisa declarar uma `ConfidentialClientApplicationOptions` objeto.  Associe a configuração lido da origem (incluindo o arquivo appconfig.json) para a instância das opções de aplicativo:

```csharp
using Microsoft.Identity.Client.AppConfig;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

Isso permite que o conteúdo da seção de "AzureAD" a *appSettings. JSON* arquivo a ser associado às propriedades correspondentes do `ConfidentialClientApplicationOptions` objeto.  Em seguida, compile um `ConfidentialClientApplication` objeto:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Adicionar configuração de tempo de execução
Em um aplicativo cliente confidencial, você geralmente têm um cache por usuário. Portanto, você precisará obter o cache associado ao usuário e informar o construtor do aplicativo que você deseja usá-lo. Da mesma forma, você pode ter um URI de redirecionamento computado dinamicamente. Nesse caso, o código é o seguinte:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

