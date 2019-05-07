---
title: Criar uma instância de um aplicativo cliente público, com opções (biblioteca de autenticação do Microsoft para .NET) | Azure
description: Saiba como criar uma instância de um aplicativo cliente público com opções de configuração usando a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
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
ms.openlocfilehash: 3a483cd6e22ce3c9fafb9b2b4d839e22c248f020
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158694"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Criar uma instância de um aplicativo cliente público com opções de configuração usando MSAL.NET

Este artigo descreve como criar uma instância de um [aplicativo cliente público](msal-client-applications.md) usando a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).  O aplicativo é instanciado com as opções de configuração definidas em um arquivo de configurações.

Antes de inicializar um aplicativo, primeiro você precisa [registrar](quickstart-register-app.md) -lo para que seu aplicativo pode ser integrado com a plataforma de identidade da Microsoft. Após o registro, talvez seja necessário as informações a seguir (que podem ser encontradas no portal do Azure):

- A ID do cliente (uma cadeia de caracteres que representa um GUID)
- A URL do provedor de identidade (a instância nomeada) e o público entrar para seu aplicativo. Esses dois parâmetros são coletivamente conhecidos como a autoridade.
- A ID do locatário se você estiver escrevendo um aplicativo de linha de negócios exclusivamente para sua organização (também chamado aplicativo de locatário único).
- Para aplicativos web e, às vezes, para aplicativos de cliente público (em especial quando o aplicativo precisa usar um agente), você vai ter também definir o redirectUri em que o provedor de identidade entrará em contato com back seu aplicativo com os tokens de segurança.


Um aplicativo de console .NET Core poderia ter a seguinte *appSettings. JSON* arquivo de configuração:

```json
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

O código a seguir lê esse arquivo usando a configuração do .NET framework:

```csharp
public class SampleConfiguration
{
    /// <summary>
    /// Authentication options
    /// </summary>
    public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

    /// <summary>
    /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
    /// in Microsoft Azure public clouds or national / sovereign clouds
    /// </summary>
    public string MicrosoftGraphBaseEndpoint { get; set; }

    /// <summary>
    /// Reads the configuration from a json file
    /// </summary>
    /// <param name="path">Path to the configuration json file</param>
    /// <returns>SampleConfiguration as read from the json file</returns>
    public static SampleConfiguration ReadFromJsonFile(string path)
    {
        // .NET configuration
        IConfigurationRoot Configuration;
        var builder = new ConfigurationBuilder()
          .SetBasePath(Directory.GetCurrentDirectory())
        .AddJsonFile(path);
        Configuration = builder.Build();

        // Read the auth and graph endpoint config
        SampleConfiguration config = new SampleConfiguration()
        {
            PublicClientApplicationOptions = new PublicClientApplicationOptions()
        };
        Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

O código a seguir cria o seu aplicativo, usando a configuração do arquivo de configurações:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

