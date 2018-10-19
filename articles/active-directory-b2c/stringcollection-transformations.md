---
title: Exemplos de transformação de declarações StringCollection para o esquema da Estrutura de Experiência de Identidade do Azure Active Directory B2C | Microsoft Docs
description: Exemplos de transformação de declarações StringCollection para o esquema da Estrutura de Experiência de Identidade do Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 924da8c92bad1c5345d5d1833723a0c4e8ad7d29
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47432646"
---
# <a name="stringcollection-claims-transformations"></a>Transformações de declarações StringCollection

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para usar as transformações de declarações de coleções de cadeias de caracteres do esquema da Estrutura de Experiência de Identidade no Azure AD (Azure Active Directory) B2C. Para obter mais informações, confira [ClaimsTransformations](claimstransformations.md).

## <a name="additemtostringcollection"></a>AddItemToStringCollection

Adiciona uma declaração de cadeia de caracteres a uma nova declaração stringCollection. 

| item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | string | O ClaimType a ser adicionado à declaração de saída. |
| InputClaim | collection | stringCollection | [Opcional] Se especificada, a transformação de declarações copiará os itens desta coleção e adicionará o item ao final da declaração da coleção de saída. |
| OutputClaim | collection | stringCollection | Os ClaimTypes produzidos depois de invocar este ClaimsTransformation. |

Use essa transformação de declaração para adicionar uma cadeia de caracteres a uma stringCollection nova ou existente. Ele é normalmente usado em um perfil técnico do **AAD-UserWriteUsingAlternativeSecurityId**. Antes que uma nova conta social seja criada, a transformação da declaração **CreateOtherMailsFromEmail** lê o ClaimType e adiciona o valor ao ClaimType **otherMails**. 

A transformação de declarações a seguir adiciona o ClaimType **email** ao ClaimType **otherMails**.

```XML
<ClaimsTransformation Id="CreateOtherMailsFromEmail" TransformationMethod="AddItemToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **collection**: ["someone@outlook.com"]
    - **item**: "admin@contoso.com"
- Declarações de saída: 
    - **collection**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

Adiciona um parâmetro de cadeia de caracteres a uma nova declaração stringCollection. 

| item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | [Opcional] Se especificada, a transformação de declarações copiará os itens desta coleção e adicionará o item ao final da declaração da coleção de saída. |
| InputParameter | item | string | O valor a ser adicionado à declaração de saída. |
| OutputClaim | collection | stringCollection | Os ClaimTypes que serão produzidos depois de invocar esta ClaimsTransformation. |

Use essa transformação de declaração para adicionar um valor de cadeia de caracteres a uma stringCollection nova ou existente. O exemplo a seguir adiciona um endereço de email constante (admin@contoso.com) à declaração **otherMails**. 

```XML
<ClaimsTransformation Id="SetCompanyEmail" TransformationMethod="AddParameterToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="item" DataType="string" Value="admin@contoso.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **collection**: ["someone@outlook.com"]
- Parâmetros de entrada 
    - **item**: "admin@contoso.com"
- Declarações de saída:
    - **collection**: ["someone@outlook.com", "admin@contoso.com"]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

Obtém o primeiro item da coleção de cadeia de caracteres fornecida. 

| item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | Os ClaimTypes que são usados pela transformação de declarações para obter o item. |
| OutputClaim | extractedItem | string | Os ClaimTypes produzidos depois de invocar este ClaimsTransformation. O primeiro item na coleção. |

O exemplo a seguir lê a declaração **otherMails** e retorna o primeiro item para a declaração **email**. 

```XML
<ClaimsTransformation Id="CreateEmailFromOtherMails" TransformationMethod="GetSingleItemFromStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedItem" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **collection**: ["someone@outlook.com", "someone@contoso.com"]
- Declarações de saída: 
    - **extractedItem**: "someone@outlook.com"

