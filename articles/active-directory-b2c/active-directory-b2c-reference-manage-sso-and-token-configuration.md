---
title: Gerenciar a personalização de SSO e de token usando políticas personalizadas no Azure Active Directory B2C | Microsoft Docs
description: Saiba mais sobre como gerenciar SSO e personalização de token usando políticas personalizadas no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c150c63bfdba70b256e639497c8daaf2a8cc7dbe
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54843405"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Gerenciar a personalização de SSO e de token usando políticas personalizadas no Azure Active Directory B2C

Este artigo fornece informações de como gerenciar as configurações de token, sessão e SSO (logon único) usando [políticas personalizadas](active-directory-b2c-overview-custom.md) no Azure AD (Azure Active Directory) B2C.

## <a name="token-lifetimes-and-claims-configuration"></a>Configuração de declarações e de tempos de vida de token

Para alterar as configurações nos tempos de vida de token, você adiciona um elemento [ClaimsProviders](claimsproviders.md) ao arquivo de terceira parte confiável da política que você deseja afetar.  O elemento **ClaimsProviders** é um filho do elemento [TrustFrameworkPolicy](trustframeworkpolicy.md). Nele, você precisará colocar as informações que afetam os tempos de vida de token. O XML se parece com este exemplo:

```XML
<ClaimsProviders>
   <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
         <TechnicalProfile Id="JwtIssuer">
            <Metadata>
               <Item Key="token_lifetime_secs">3600</Item>
               <Item Key="id_token_lifetime_secs">3600</Item>
               <Item Key="refresh_token_lifetime_secs">1209600</Item>
               <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
               <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
               <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
            </Metadata>
         </TechnicalProfile>
      </TechnicalProfiles>
   </ClaimsProvider>
</ClaimsProviders>
```

Os seguintes valores são definidos no exemplo anterior:

- **Tempos de vida do token de acesso** – o valor do tempo de vida do token de acesso é definido com o item de metadados **token_lifetime_secs**. O valor padrão é de 3600 segundos (60 minutos).
- **Tempo de vida do token de ID** – o valor do tempo de vida do token de ID é definido com o item de metadados **id_token_lifetime_secs**. O valor padrão é de 3600 segundos (60 minutos).
- **Tempo de vida do token de atualização** – o valor do tempo de vida do token de atualização é definido com o item de metadados **refresh_token_lifetime_secs**. O valor padrão é de 1209600 segundos (14 dias).
- **Tempo de vida do token de atualização de janela deslizante** – se quiser definir um tempo de vida de janela deslizante para o token de atualização, defina o valor do item de metadados **rolling_refresh_token_lifetime_secs**. O valor padrão é 7776000 (90 dias). Se não quiser impor um tempo de vida de janela deslizante, substitua o item por `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`.
- **Declaração iss (Issuer)** – a declaração iss (Issuer) é definida com o item de metadados **IssuanceClaimPattern**. Os valores aplicáveis são `AuthorityAndTenantGuid` e `AuthorityWithTfp`.
- **Configuração de declaração que representa a ID de política** – as opções para definir esse valor são `TFP` (política de confiança da estrutura) e `ACR` (referência do contexto de autenticação). `TFP` é o valor recomendado. Defina **AuthenticationContextReferenceClaimPattern** com o valor de `None`. No item **OutputClaims**, adicione este elemento:
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    Para o ACR, remova o item **AuthenticationContextReferenceClaimPattern**.

- **Declaração sub (Subject)** – essa opção é padronizada para a ObjectID. Se você quer mudar essa configuração para `Not Supported`, substitua esta linha: 

    ```XML
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```
    
    com esta linha:
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="session-behavior-and-sso"></a>Comportamento da sessão e SSO

Para alterar o comportamento da sessão e as configurações de SSO, você adiciona um elemento **UserJourneyBehaviors** dentro do elemento [RelyingParty](relyingparty.md).  O elemento **UserJourneyBehaviors** deve vir logo após o **DefaultUserJourney**. Dentro do elemento **UserJourneyBehavors** deve ficar parecido com este exemplo:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

Os seguintes valores são configurados no exemplo anterior:

- **(SSO) logon único** – o logon único é configurado com o **SingleSignOn**. Os valores aplicáveis são `Tenant`, `Application`, `Policy` e `Suppressed`. 
- **Tempo de vida de sessão de aplicativo Web (minutos)** – o tempo de vida de sessão do aplicativo Web é definido com o elemento **SessionExpiryInSeconds**. O valor padrão é de 86400 segundos (1440 minutos).
- **Tempo limite de sessão do aplicativo Web** – o tempo limite de sessão do aplicativo Web é definido com o elemento **SessionExpiryType**. Os valores aplicáveis são `Absolute` e `Rolling`.
