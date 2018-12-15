---
title: Definir um perfil técnico OpenId Connect em uma política personalizada no Azure Active Directory B2C | Microsoft Docs
description: Defina um perfil técnico OpenId Connect em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9a20b8df1f0d9cddbde4c4886e11967bc8c04160
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52842400"
---
# <a name="define-a-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico OpenId Connect em uma política personalizada do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure AD (Azure Active Directory) B2C oferece suporte para o provedor de identidade do protocolo [OpenId Connect](https://openid.net/2015/04/17/openid-connect-certification-program/). O OpenID Connect 1.0 define uma camada de identidade com base em OAuth 2.0 e representa a estado de última geração em protocolos de autenticação modernos.  Com o perfil técnico do OpenId Connect, você pode federar com um provedor de identidade baseado em OpenId Connect, como o Azure AD, permitindo que os usuários entrem com suas identidades empresariais ou de redes sociais existentes.

## <a name="protocol"></a>Protocolo

O atributo **Name** do elemento **Protocol** precisa ser definido como `OpenIdConnect`. Por exemplo, o protocolo para o perfil técnico **MSA-OIDC** é `OpenIdConnect`:

```XML
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
  ...    
```

## <a name="input-claims"></a>Declarações de entrada

Os elementos **InputClaims** e **InputClaimsTransformations** não são necessários. Mas talvez você queira enviar parâmetros adicionais para seu provedor de identidade. O exemplo a seguir adiciona o parâmetro de cadeia de caracteres de consulta **domain_hint** com o valor de `contoso.com` à solicitação de autorização.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Declarações de saída

O elemento **OutputClaims** contém uma lista de declarações retornadas pelo provedor de identidade OpenId Connect. Talvez seja necessário mapear o nome da declaração definida em sua política para o nome definido no provedor de identidade. Você também pode incluir declarações que não são retornadas pelo provedor de identidade, desde que defina o atributo `DefaultValue`.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** usados para modificar as declarações de saída ou gerar novas declarações.

O exemplo a seguir mostra as declarações retornadas pelo provedor de identidade de Conta Microsoft:

- A declaração **sub** que é mapeada para a declaração **socialIdpUserId**.
- A declaração **name** que é mapeada para a declaração **displayName**.
- O **email** sem mapeamento de nome.

O perfil técnico também retorna declarações que não são retornadas pelo provedor de identidade:

- A declaração **identityProvider** que contém o nome do provedor de identidade.
- A declaração **authenticationSource** com um valor padrão de **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
  <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
</OutputClaims>
```

## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | DESCRIÇÃO |
| --------- | -------- | ----------- |
| client_id | SIM | O identificador do aplicativo do provedor de identidade. |
| IdTokenAudience | Não  | O público-alvo do id_token. Se for especificado, o Azure AD B2C verificará se o token está em uma declaração retornada pelo provedor de identidade e é igual ao especificado. |
| METADATA | SIM | Uma URL que aponta para um documento de configuração JSON formatado segundo a especificação de descoberta do OpenID Connect, que também é conhecido como um ponto de extremidade de configuração do openid bem conhecido. |
| ProviderName | Não  | O nome do provedor de identidade. |
| response_types | Não  | O tipo de resposta de acordo com a especificação do OpenID Connect Core 1.0. Valores possíveis: `id_token`, `code` ou `token`. |
| response_mode | Não  | O método que o provedor de identidade usa para enviar o resultado de volta ao Azure AD B2C. Valores possíveis: `query`, `form_post` (padrão) ou `fragment`. |
| scope | Não  | O escopo da solicitação de acesso definida de acordo com a especificação do OpenID Connect Core 1.0. Como `openid`, `profile` e `email`. |
| HttpBinding | Não  | A associação HTTP esperada para o token de acesso e pontos de extremidade do token de declarações. Os valores possíveis são `GET` ou `POST`.  |
| ValidTokenIssuerPrefixes | Não  | Uma chave que pode ser usada para entrar em cada um dos locatários ao usando um provedor de identidade multilocatário como o Azure Active Directory. |
| UsePolicyInRedirectUri | Não  | Indica se deve ser usada uma política ao criar o URI de redirecionamento. Quando você configura seu aplicativo no provedor de identidade, precisa especificar o URI de redirecionamento. O URI de redirecionamento aponta para o Azure AD B2C, `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` (login.microsoftonline.com pode mudar com your-tenant-name.b2clogin.com).  Se você especificar `false`, precisará adicionar um URI de redirecionamento a cada política que você usar. Por exemplo: `https://login.microsoftonline.com/te/{tenant}/{policy}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Não  | Indica se uma solicitação para um serviço externo deverá ser marcada como uma falha se o código de status Http estiver no intervalo 5xx. O padrão é `false`. |
| DiscoverMetadataByTokenIssuer | Não  | Indica se os metadados OIDC devem ser descobertos usando o emissor no token JWT. |

## <a name="cryptographic-keys"></a>Chaves de criptografia

O elemento **CryptographicKeys** contém o seguinte atributo:

| Atributo | Obrigatório | DESCRIÇÃO |
| --------- | -------- | ----------- |
| client_secret | SIM | O segredo do cliente do aplicativo do provedor de identidade. A chave de criptografia será necessária apenas se os metadados **response_types** estiverem definidos como `code`. Nesse caso, o Azure AD B2C faz outra chamada para trocar o código de autorização para um token de acesso. Se os metadados forem definidos como `id_token`, você poderá omitir a chave de criptografia.  |  

## <a name="redirect-uri"></a>URI de redirecionamento
 
Ao configurar o URI de redirecionamento do seu provedor de identidade, insira `https://login.microsoftonline.com/te/tenant/oauth2/authresp`. Substitua **tenant** pelo nome do locatário (por exemplo, contosob2c.onmicrosoft.com) ou a ID do locatário. O URI de redirecionamento deve ser todo em letras minúsculas.

Se você estiver usando o domínio **b2clogin.com** em vez de **login.microsoftonline.com**, use b2clogin.com, em vez de login.microsoftonline.com.

Exemplos:

- [Adicionar MSA (Conta Microsoft) como um provedor de identidade usando políticas personalizadas](active-directory-b2c-custom-setup-msa-idp.md)
- [Entrar usando contas do Azure AD](active-directory-b2c-setup-aad-custom.md)
- [Permitir que os usuários entrem em um provedor de identidade multilocatário do Azure AD usando políticas personalizadas](active-directory-b2c-setup-commonaad-custom.md)

 














