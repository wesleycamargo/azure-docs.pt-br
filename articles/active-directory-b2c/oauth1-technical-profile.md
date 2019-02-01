---
title: Definir um perfil técnico OAuth1 em uma política personalizada no Azure Active Directory B2C | Microsoft Docs
description: Defina um perfil técnico OAuth1 em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 08fb0baf85b09b4804c83617dbae9ae9c69f0224
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55162728"
---
# <a name="define-a-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico OAuth1 em uma política personalizada do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure AD (Azure Active Directory) B2C oferece suporte para o provedor de identidade do [protocolo OAuth 1.0](https://tools.ietf.org/html/rfc5849). Este artigo descreve as especificações de um perfil técnico para interagir com um provedor de declarações compatível com esse protocolo padronizado. Com o perfil técnico OAuth1, você pode federar com um provedor de identidade baseado em OAuth1, como o Twitter, permitindo que os usuários entrem com suas identidades empresariais ou de redes sociais existentes.

## <a name="protocol"></a>Protocolo

O atributo **Name** do elemento **Protocol** precisa ser definido como `OAuth1`. Por exemplo, o protocolo para o perfil técnico **Twitter-OAUTH1** é `OAuth1`.

```XML
<TechnicalProfile Id="Twitter-OAUTH1">
  <DisplayName>Twitter</DisplayName>
  <Protocol Name="OAuth1" />
  ...    
```

## <a name="input-claims"></a>Declarações de entrada

Os elementos **InputClaims** e **InputClaimsTransformations** são vazios ou ausentes.

## <a name="output-claims"></a>Declarações de saída

O elemento **OutputClaims** contém uma lista de declarações retornadas pelo provedor de identidade OAuth1. Talvez seja necessário mapear o nome da declaração definida em sua política para o nome definido no provedor de identidade. Você também pode incluir declarações que não são retornadas pelo provedor de identidade, desde que defina o atributo **DefaultValue**.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** usados para modificar as declarações de saída ou gerar novas declarações.

O exemplo a seguir mostra as declarações retornadas pelo provedor de identidade do Twitter:

- A declaração **user_id** que é mapeada para a declaração **socialIdpUserId**.
- A declaração **screen_name** que é mapeada para a declaração **displayName**.
- A declaração **email** sem mapeamento de nome.

O perfil técnico também retorna declarações que não são retornadas pelo provedor de identidade: 

- A declaração **identityProvider** que contém o nome do provedor de identidade.
- A declaração **authenticationSource** com um valor padrão de `socialIdpAuthentication`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="user_id" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | DESCRIÇÃO |
| --------- | -------- | ----------- |
| client_id | SIM | O identificador do aplicativo do provedor de identidade. |
| ProviderName | Não  | O nome do provedor de identidade. |
| request_token_endpoint | SIM | A URL do ponto de extremidade do token de solicitação, de acordo com RFC 5849. |
| authorization_endpoint | SIM | A URL do ponto de extremidade da autorização, de acordo com RFC 5849. |
| access_token_endpoint | SIM | A URL do ponto de extremidade do token, de acordo com RFC 5849. |
| ClaimsEndpoint | Não  | A URL do ponto de extremidade de informações do usuário. | 
| ClaimsResponseFormat | Não  | O formato de resposta de declarações.|

## <a name="cryptographic-keys"></a>Chaves de criptografia

O elemento **CryptographicKeys** contém o seguinte atributo:

| Atributo | Obrigatório | DESCRIÇÃO |
| --------- | -------- | ----------- |
| client_secret | SIM | O segredo do cliente do aplicativo do provedor de identidade.   | 

## <a name="redirect-uri"></a>URI de redirecionamento

Ao configurar a URL de redirecionamento do seu provedor de identidade, insira `https://login.microsoftonline.com/te/tenant/policyId/oauth1/authresp`. Certifique-se de substituir **tenant** pelo nome do locatário (por exemplo, contosob2c.onmicrosoft.com) e **policyId** pelo identificador da sua política (por exemplo, b2c_1a_policy). O URI de redirecionamento deve ser todo em letras minúsculas. Você deve adicionar um UR de redirecionamento para todas as políticas que usam o logon do provedor de identidade. 

Se você estiver usando o domínio **b2clogin.com** em vez de **login.microsoftonline.com**, use b2clogin.com, em vez de login.microsoftonline.com.

Exemplos:

- [Adicionar o Twitter como um provedor de identidade OAuth1 usando políticas personalizadas](active-directory-b2c-custom-setup-twitter-idp.md)













