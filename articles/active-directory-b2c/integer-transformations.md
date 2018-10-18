---
title: Exemplos de transformação de declarações de inteiros para o esquema da Estrutura de Experiência de Identidade do Azure Active Directory B2C | Microsoft Docs
description: Exemplos de transformação de declarações de inteiros para o esquema da Estrutura de Experiência de Identidade do Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 988e25b6a5ef3f99ae7df9076a40e06b403bb029
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44382333"
---
# <a name="integer-claims-transformations"></a>Transformações de declarações de inteiros

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para usar as transformações de declarações de inteiros do esquema da Estrutura de Experiência de Identidade no Azure AD (Azure Active Directory) B2C. Para obter mais informações, confira [ClaimsTransformations](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim 

Converte um tipo de dados Long em um tipo de dados String.

| item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | longo | O ClaimType para converter em uma cadeia de caracteres. |
| OutputClaim | outputClaim | string | O ClaimType produzido depois de invocar esta ClaimsTransformation. |

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
    - **inputClaim**: 12334 (Long)
- Declarações de saída: 
    - **outputClaim**: "12334" (String)

