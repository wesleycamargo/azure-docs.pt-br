---
title: Exemplos de transformação de declarações gerais do esquema do Identity Experience Framework do Azure Active Directory B2C | Microsoft Docs
description: Exemplos de transformação de declarações gerais do esquema do Identity Experience Framework do Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
origin.date: 09/10/2018
ms.date: 04/04/2019
ms.author: v-junlch
ms.subservice: B2C
ms.openlocfilehash: 6a9a819e75e487999a2b50ae758b8d9c6c716a4f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60397151"
---
# <a name="general-claims-transformations"></a>Transformações de declarações gerais

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para usar as transformações de declarações gerais do esquema do Identity Experience Framework no Azure AD (Azure Active Directory) B2C. Para obter mais informações, confira [ClaimsTransformations](claimstransformations.md).

## <a name="doesclaimexist"></a>DoesClaimExist

Verifica se a **inputClaim** existe ou não e define **outputClaim** como true ou false adequadamente.

| item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim |Qualquer | A declaração de entrada cuja existência deve ser verificada. |
| OutputClaim | outputClaim | boolean | O ClaimType produzido depois de invocar esta ClaimsTransformation. |

Use essa transformação de declarações para verificar se uma declaração existe ou se contém algum valor. O valor retornado é um valor booliano que indica se a declaração existe. O exemplo a seguir verifica se o endereço de email existe.

```XML
<ClaimsTransformation Id="CheckIfEmailPresent" TransformationMethod="DoesClaimExist">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim" />
  </InputClaims>                    
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isEmailPresent" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
  - **inputClaim**: someone@contoso.com
- Declarações de saída: 
    - **outputClaim**: true

## <a name="hash"></a>Hash

Transforme o texto sem formatação fornecido em hash usando o sal e um segredo.

| item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | texto não criptografado | cadeia de caracteres | A declaração de entrada a ser criptografada |
| InputClaim | sal | cadeia de caracteres | O parâmetro sal. Você pode criar um valor aleatório, usando a transformação de declarações `CreateRandomString`. |
| InputParameter | randomizerSecret | cadeia de caracteres | Aponta para **chaves de política** existentes do Azure AD B2C. Para criar um novo segredo: No seu locatário do Azure AD B2C, selecione **Configurações de B2C > Identity Experience Framework**. Selecione **Chaves de Política** para exibir as chaves disponíveis no seu locatário. Selecione **Adicionar**. Em **Opções** selecione **Manual**. Forneça um nome (o prefixo B2C_1A_ pode ser adicionado automaticamente). Na caixa Segredo, insira qualquer segredo que você quiser, como 1234567890. Em Uso da chave, selecione **Segredo**. Selecione **Criar**. |
| OutputClaim | hash | cadeia de caracteres | O ClaimType que é produzido depois de invocar esta transformação de declarações. A declaração configurada na inputClaim `plaintext`. |

```XML
<ClaimsTransformation Id="HashPasswordWithEmail" TransformationMethod="Hash">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="password" TransformationClaimType="plaintext" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="salt" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="randomizerSecret" DataType="string" Value="B2C_1A_AccountTransformSecret" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hashedPassword" TransformationClaimType="hash" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **texto não criptografado**: MyPass@word1
    - **salt**: 487624568
    - **randomizerSecret**: B2C_1A_AccountTransformSecret
- Declarações de saída: 
    - **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=




