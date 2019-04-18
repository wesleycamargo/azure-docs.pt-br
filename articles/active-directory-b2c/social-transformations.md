---
title: Exemplos de transformação de declarações da conta social para o esquema da Estrutura de Experiência de Identidade do Azure Active Directory B2C | Microsoft Docs
description: Exemplos de transformação de declarações da conta social para o esquema da Estrutura de Experiência de Identidade do Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 53608654392d7efb73b6dadac14f01a94bb035a7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58893513"
---
# <a name="social-accounts-claims-transformations"></a>Transformações de declarações de contas sociais

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

No Azure AD (Azure Active Directory) B2C, as identidades de conta social são armazenadas em um atributo `userIdentities` de um tipo de declaração **alternativeSecurityIdCollection**. Cada item no **alternativeSecurityIdCollection** especifica o emissor (nome do provedor de identidade) como facebook.com e o `issuerUserId`, que é um identificador de usuário único para o emissor.

```JSON
"userIdentities": [{
    "issuer": "google.com",
    "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"
  },
  {
    "issuer": "facebook.com",
    "issuerUserId": "MTIzNDU="
  }]
```

Este artigo fornece exemplos para usar as transformações de declarações da conta social do esquema da Estrutura de Experiência de Identidade no Azure AD B2C. Para obter mais informações, confira [ClaimsTransformations](claimstransformations.md).

## <a name="createalternativesecurityid"></a>CreateAlternativeSecurityId

Cria uma declaração JSON da propriedade alternativeSecurityId do usuário que pode ser usada em chamadas para o Azure Active Directory. Para obter mais informações, confira [Esquema do AlternativeSecurityId](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#AlternativeSecurityIdType).

| item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | chave | cadeia de caracteres | O ClaimType que especifica o identificador de usuário único usado pelo provedor de identidade social. |
| InputClaim | identityProvider | cadeia de caracteres | O ClaimType que especifica o nome do provedor de identidade de conta social, como facebook.com. |
| OutputClaim | alternativeSecurityId | cadeia de caracteres | O ClaimType produzido depois de invocar ClaimsTransformation. Contém informações sobre a identidade de um usuário da conta social. O **emissor** é o valor da declaração `identityProvider`. O **issuerUserId** é o valor da declaração `key` no formato base64. |

Use essa transformação de declarações para gerar um ClaimType `alternativeSecurityId`. Ele é usado por todos os perfis técnicos do provedor de identidade, como `Facebook-OAUTH`. A transformação de declarações a seguir recebe a ID de conta social do usuário e o nome do provedor de identidade. A saída desse perfil técnico é um formato de cadeia de caracteres JSON que pode ser usado em serviços de diretório do Azure AD.

```XML
<ClaimsTransformation Id="CreateAlternativeSecurityId" TransformationMethod="CreateAlternativeSecurityId">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="socialIdpUserId" TransformationClaimType="key" />
    <InputClaim ClaimTypeReferenceId="identityProvider" TransformationClaimType="identityProvider" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="alternativeSecurityId" TransformationClaimType="alternativeSecurityId" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **key**: 12334
    - **identityProvider**: Facebook.com
- Declarações de saída:
    - **alternativeSecurityId**: { "issuer": "facebook.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}

## <a name="additemtoalternativesecurityidcollection"></a>AddItemToAlternativeSecurityIdCollection

Adiciona um `AlternativeSecurityId` a uma declaração `alternativeSecurityIdCollection`.

| item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | cadeia de caracteres | O ClaimType a ser adicionado à declaração de saída. |
| InputClaim | coleção | alternativeSecurityIdCollection | Os ClaimTypes usados pela transformação de declarações se disponíveis na política. Se fornecida, a transformação de declarações adiciona o `item` no final da coleção. |
| OutputClaim | coleção | alternativeSecurityIdCollection | Os ClaimTypes produzidos depois de invocar este ClaimsTransformation. A nova coleção que contém os itens de entrada `collection` e `item`. |

O exemplo a seguir vincula uma nova identidade social com uma conta existente. Para vincular uma nova identidade social:
1. Nos perfis técnicos **AAD-UserReadUsingAlternativeSecurityId** e **AAD-UserReadUsingObjectId**, emita a declaração **alternativeSecurityIds** do usuário.
1. Peça ao usuário para entrar com um dos provedores de identidade que não estão associados este usuário.
1. Usando a transformação de declarações **CreateAlternativeSecurityId**, crie um tipo de declaração **alternativeSecurityId** com o nome de `AlternativeSecurityId2`
1. Chame a transformação de declarações **AddItemToAlternativeSecurityIdCollection** para adicionar a declaração **AlternativeSecurityId2** à declaração **AlternativeSecurityIds** existente.
1. Persistir a declaração **alternativeSecurityIds** para a conta de usuário

```XML
<ClaimsTransformation Id="AddAnotherAlternativeSecurityId" TransformationMethod="AddItemToAlternativeSecurityIdCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId2" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **item**: { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" }
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
- Declarações de saída:
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation

Retorna a lista de emissores da declaração **alternativeSecurityIdCollection** em uma nova declaração **stringCollection**.

| item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | alternativeSecurityIdCollection | alternativeSecurityIdCollection | O ClaimType a ser usado para obter a lista de provedores de identidade (emissor). |
| OutputClaim | identityProvidersCollection | stringCollection | Os ClaimTypes produzidos depois de invocar este ClaimsTransformation. Lista de provedores de identidade associados com a declaração de entrada alternativeSecurityIdCollection |

A transformação de declarações a seguir lê a declaração **alternativeSecurityIds** do usuário e extrai a lista de nomes de provedores de identidade associada à conta. Usar a saída **identityProvidersCollection** para mostrar ao usuário a lista de provedores de identidade associada à conta. Ou, na página de seleção do provedor de identidade, filtre a lista de provedores de identidade com base na declaração **identityProvidersCollection** de saída. Portanto, o usuário pode selecionar para vincular a nova identidade social que ainda não esteja associada à conta.

```XML
<ClaimsTransformation Id="ExtractIdentityProviders" TransformationMethod="GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityIds" TransformationClaimType="alternativeSecurityIdCollection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="identityProviders" TransformationClaimType="identityProvidersCollection" />
  </OutputClaims>
</ClaimsTransformation>
```

- Declarações de entrada:
    - **alternativeSecurityIdCollection**: [ { "issuer": "google.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- Declarações de saída:
    - **identityProvidersCollection**: [ "facebook.com", "google.com" ]

## <a name="removealternativesecurityidbyidentityprovider"></a>RemoveAlternativeSecurityIdByIdentityProvider

Remove um **AlternativeSecurityId** de uma declaração **alternativeSecurityIdCollection**.

| item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | identityProvider | cadeia de caracteres | O ClaimType que contém o nome do provedor de identidade a ser removido da coleção. |
| InputClaim | coleção | alternativeSecurityIdCollection | Os ClaimTypes usados pela transformação de declarações. A transformação de declarações remove o identityProvider da coleção. |
| OutputClaim | coleção | alternativeSecurityIdCollection | Os ClaimTypes produzidos depois de invocar este ClaimsTransformation. A nova coleção, depois de remover o identityProvider da coleção. |

O exemplo a seguir desvincula uma das identidades sociais de uma conta existente. Para desvincular uma identidade social:
1. Nos perfis técnicos **AAD-UserReadUsingAlternativeSecurityId** e **AAD-UserReadUsingObjectId**, emita a declaração **alternativeSecurityIds** do usuário.
2. Peça ao usuário para selecionar qual conta social será removida da lista de provedores de identidade que estão associados a este usuário.
3. Chame um perfil técnico de transformação de declarações que chama a transformação de declarações **RemoveAlternativeSecurityIdByIdentityProvider**, que removeu a identidade social selecionada, usando o nome do provedor de identidade.
4. Persista a declaração **alternativeSecurityIds** para a conta de usuário.

```XML
<ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider" TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="secondIdentityProvider" TransformationClaimType="identityProvider" />
        <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformations>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **identityProvider**: facebook.com
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- Declarações de saída:
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
