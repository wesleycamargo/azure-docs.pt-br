---
title: Exemplos de transformação de declarações boolianas do esquema do Identity Experience Framework do Azure Active Directory B2C | Microsoft Docs
description: Exemplos de transformação de declarações boolianas do esquema do Identity Experience Framework do Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c6e9337fb5e336c506fc43e13eeb7fdbfaf636a7
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47432555"
---
# <a name="boolean-claims-transformations"></a>Transformações de declarações boolianas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para usar as transformações de declarações boolianas do esquema do Identity Experience Framework no Azure AD (Azure Active Directory) B2C. Para obter mais informações, confira [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>AndClaims

Executa uma operação And de dois inputClaims boolianos e define o outputClaim com o resultado da operação.

| item  | TransformationClaimType  | Tipo de Dados  | Observações |
|-------| ------------------------ | ---------- | ----- |
| InputClaim | inputClaim1 | booleano | O primeiro ClaimType a ser avaliado. |
| InputClaim | inputClaim2  | booleano | O segundo ClaimType a ser avaliado. |
|OutputClaim | outputClaim | booleano | Os ClaimTypes que serão produzidos após a invocação dessa transformação de declarações (true ou false). |

A transformação de declarações a seguir demonstra como executar And em dois ClaimTypes boolianos: `isEmailNotExist`, e `isSocialAccount`. A declaração de saída `presentEmailSelfAsserted` será definida como `true` se o valor de ambas as declarações de entrada for `true`. Em uma etapa de orquestração, você poderá usar uma pré-condição para predefinir uma página autodeclarada, somente se o email da conta social estiver vazio.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="AndClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isEmailNotExist" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isSocialAccount" TransformationClaimType="inputClaim2" />
  </InputClaims>                    
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentEmailSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim1**: true
    - **inputClaim2**: false
- Declarações de saída:
    - **outputClaim**: false


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Verifica se os valores boolianos de duas declarações são iguais e gera uma exceção se eles não são.

| item | TransformationClaimType  | Tipo de Dados  | Observações |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | booleano | O ClaimType a ser declarado. |
| InputParameter |valueToCompareTo | booleano | O valor a ser comparado (true ou false). |

A transformação de declarações **AssertBooleanClaimIsEqualToValue** é sempre executada por meio de um [perfil técnico de validação](validation-technical-profile.md) que é chamado por um [perfil técnico autodeclarado](self-asserted-technical-profile.md). Os metadados do perfil técnico autodeclarado **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** controlam a mensagem de erro que o perfil técnico apresenta ao usuário.

![Execução de AssertStringClaimsAreEqual](./media/boolean-transformations/assert-execution.png)

A transformação de declarações a seguir demonstra como verificar o valor de um ClaimType booliano com um valor `true`. Se o valor de ClaimType `accountEnabled` for false, uma mensagem de erro será gerada.

```XML
<ClaimsTransformation Id="AssertAccountEnabledIsTrue" TransformationMethod="AssertBooleanClaimIsEqualToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="accountEnabled" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```


O perfil técnico de validação `login-NonInteractive` chama a transformação de declarações `AssertAccountEnabledIsTrue`.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

O perfil técnico autodeclarado chama o perfil técnico **login-NonInteractive** de validação.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Custom error message if account is disabled.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim**: false
    - **valueToCompareTo**: true
- Resultado: erro gerado

## <a name="notclaims"></a>NotClaims

Executa uma operação Not do inputClaim booliano e define o outputClaim com o resultado da operação.

| item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | booleano | A declaração a ser operada. |
| OutputClaim | outputClaim | booleano | Os ClaimTypes que são produzidos após a invocação desse ClaimsTransformation (true ou false). |

Use essa transformação de declaração para realizar a negação lógica em uma declaração.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim**: false
- Declarações de saída:
    - **outputClaim**: true

## <a name="orclaims"></a>OrClaims 

Calcula um Or entre dois inputClaims boolianos e define o outputClaim com o resultado da operação.

| item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | booleano | O primeiro ClaimType a ser avaliado. |
| InputClaim | inputClaim2 | booleano | O segundo ClaimType a ser avaliado. |
| OutputClaim | outputClaim | booleano | Os ClaimTypes que serão produzidos após a invocação desse ClaimsTransformation (true ou false). |

A transformação de declarações a seguir demonstra como executar `Or` em dois ClaimTypes boolianos. Na etapa de orquestração, você poderá usar uma pré-condição para predefinir uma página autodeclarada se o valor de uma das declarações for `true`.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="OrClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedNotExists" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="inputClaim2" />
  </InputClaims>                    
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentTOSSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim1**: true
    - **inputClaim2**: false
- Declarações de saída:
    - **outputClaim**: true

