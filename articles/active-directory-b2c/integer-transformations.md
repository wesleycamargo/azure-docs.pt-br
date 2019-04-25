---
title: Exemplos de transformação de declarações de inteiros para o esquema da Estrutura de Experiência de Identidade do Azure Active Directory B2C | Microsoft Docs
description: Exemplos de transformação de declarações de inteiros para o esquema da Estrutura de Experiência de Identidade do Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 358ee07b8fd32edded084d406e490cae9f557fdd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60397134"
---
# <a name="integer-claims-transformations"></a>Transformações de declarações de inteiros

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para usar as transformações de declarações de inteiros do esquema da Estrutura de Experiência de Identidade no Azure AD (Azure Active Directory) B2C. Para obter mais informações, confira [ClaimsTransformations](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim 

Converte um tipo de dados Long em um tipo de dados String.

| item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | longo | O ClaimType para converter em uma cadeia de caracteres. |
| OutputClaim | outputClaim | cadeia de caracteres | O ClaimType produzido depois de invocar esta ClaimsTransformation. |

Neste exemplo, a declaração `numericUserId` com um tipo de valor Long é convertido em uma declaração `UserId` com um tipo de valor String.

```XML
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim**: 12334 (long)
- Declarações de saída: 
    - **outputClaim**: "12334" (string)

