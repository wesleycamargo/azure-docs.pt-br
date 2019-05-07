---
title: API da web - configuração de código de aplicativo protegida | Azure
description: Saiba como compilar uma API Web protegida e configurar o código do seu aplicativo.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e206cb29338445e30a7462bcbaf0079236e75510
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074959"
---
# <a name="protected-web-api---code-configuration"></a>API - configuração do código web protegida

Para configurar com êxito o código para sua API web protegida, você precisa entender o que torna as APIs protegidas, o que você precisa configurar o token de portador e como validar o token.

## <a name="what-makes-aspnetaspnet-core-apis-protected"></a>O que torna ASP.NET/ASP.NET principais APIs protegidas?

Como em aplicativos da web, o ASP.NET/ASP.NET núcleo da web, APIs são "protegido" porque suas ações do controlador são prefixadas com o `[Authorize]` atributo. Portanto, as ações do controlador só podem ser chamadas se a API é chamada com uma identidade que está autorizada.

Considere as seguintes perguntas:

- Como a API da web sabe a identidade do aplicativo que chama (apenas um aplicativo pode chamar uma API da web)?
- Se o aplicativo chamou a API da web em nome do usuário, o que é a identidade do usuário?

## <a name="bearer-token"></a>Token de portador

As informações sobre a identidade do aplicativo e sobre o usuário (a menos que o aplicativo web aceita chamadas de serviço a serviço de um aplicativo daemon), é mantido no token de portador é definido no cabeçalho ao chamar o aplicativo.

Aqui está um C# exemplo de código que mostra um cliente chamar a API depois de adquirir um token com MSAL.NET.

```CSharp
var scopes = new[] {$"api://.../access_as_user}";
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> O token de portador foi solicitado por um aplicativo cliente para o ponto de extremidade de plataforma do Microsoft identity **para a API web**. A API da web é o único aplicativo que deve verificar o token e examine as declarações que ele contém. Os aplicativos cliente nunca devem examinar as declarações nos tokens (a API da web poderia optar, no futuro, que ele exige que o token ser criptografados e isso interromperá o aplicativo cliente que pode violar abrir os tokens de acesso).

## <a name="jwtbearer-configuration"></a>Configuração de JwtBearer

Esta seção aborda o que você precisa configurar o token de portador.

### <a name="config-file"></a>Arquivo de configuração

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line of business app)
      Otherwise you can leave them set to common
      This can be:
      - A guid (Tenant ID = Directory ID)
      - 'common' (Any organization and personal accounts)
      - 'organizations' (any organization)
      - 'consumers' (Microsoft personal accounts)
    */
    "TenantId": "common"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="code-initialization"></a>Código de inicialização

Quando o aplicativo é chamado sobre a manutenção de ação do controlador um `[Authorize]` atributo, core ASP.NET/ASP.NET examina o token de portador no cabeçalho de autorização da solicitação de chamada e extrai o token de acesso, que então é encaminhado para o JwtBearer middleware, que chama o modelo do Microsoft Identity Extensions para .NET.

No ASP.NET Core, este middleware é inicializado no `Startup.cs` arquivo.

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

O middleware é adicionado à API da web, a instrução a seguir:

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 Atualmente, os modelos do ASP.NET Core criam APIs da web de v 1.0 do Azure AD. No entanto, você pode alterá-los para usar o ponto de extremidade de plataforma do Microsoft identity, adicionando o código a seguir no facilmente o `Startup.cs` arquivo.

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform v2.0 web API
    options.Authority += "/v2.0";

    // The web API accepts as audiences are both the Client ID (options.Audience) and api://{ClientID}
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line of business apps),
    // we inject our own multi-tenant validation logic (which even accepts both V1 and V2 tokens)
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ValidateAadIssuer;
});
```

## <a name="token-validation"></a>Validação de token

O middleware JwtBearer, como o middleware OpenID Connect em aplicativos web, é direcionado pelo `TokenValidationParameters` para validar o token. O token for descriptografado (se necessário), as declarações são extraídas e a assinatura é verificada. Em seguida, o token é validado verificando se os dados a seguir:

- Ele é direcionado para a web API (público)
- Ele foi emitido para um aplicativo que tem permissão para chamar a API (sub) web
- Ele foi emitido por um confiável Security Token Server (STS) (emissor)
- Tempo de vida do token está no intervalo (validade)
- Ele não foi violado (assinatura)

Também pode haver validação especial. Por exemplo, é possível validar que as chaves de assinatura (quando inserido em um token) são confiáveis e que o token não está sendo repetido. Por fim, alguns protocolos exigem validações específicas.

### <a name="validators"></a>Validadores

As etapas de validação são capturadas em validadores, o qual estão todos na [extensão de modelo de identidade da Microsoft para .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) biblioteca de código-fonte aberto, em um arquivo de origem: [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs)

Os validadores são descritos na tabela a seguir:

| Validador | DESCRIÇÃO |
|---------|---------|
| `ValidateAudience` | Garante que o token para o aplicativo valida o token (para mim). |
| `ValidateIssuer` | Garante que o token foi emitido por um STS confiáveis (de alguém que confio). |
| `ValidateIssuerSigningKey` | Garante que o aplicativo validar as relações de confiança de token a chave que foi usada para assinar o token (caso especial em que a chave é inserida no token, geralmente, não é necessário). |
| `ValidateLifetime` | Garante que o token é válido ainda (ou já). Feito verificando que o tempo de vida do token (`notbefore`, `expires` declarações) está no intervalo. |
| `ValidateSignature` | Garante que o token ainda não foram alterado. |
| `ValidateTokenReplay` | Garante que o token não é reproduzido (caso especial para alguns protocolos de uso onetime). |

Os validadores são todos associados a propriedades do `TokenValidationParameters` classe em si inicializado da configuração ASP.NET/ASP.NET Core. Na maioria dos casos, você não precisará alterar os parâmetros. Há uma exceção para aplicativos que não são de locatário único (ou seja, aplicativos web que aceitam usuários de qualquer organização ou contas pessoais da Microsoft) – nesse caso, o emissor deve ser validado.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Mover para a produção](scenario-protected-web-api-production.md)
