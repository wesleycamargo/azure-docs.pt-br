---
title: Exemplos de transformação de declarações gerais do esquema do Identity Experience Framework do Azure Active Directory B2C | Microsoft Docs
description: Exemplos de transformação de declarações gerais do esquema do Identity Experience Framework do Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 8ff418c24e9171d452bca873c4b8f66ada2adb7c
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47431319"
---
# <a name="general-claims-transformations"></a>Transformações de declarações gerais

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para usar as transformações de declarações gerais do esquema do Identity Experience Framework no Azure AD (Azure Active Directory) B2C. Para obter mais informações, confira [ClaimsTransformations](claimstransformations.md).

## <a name="doesclaimexist"></a>DoesClaimExist

Verifica se a **inputClaim** existe ou não e define **outputClaim** como true ou false adequadamente.

| item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |Qualquer | A declaração de entrada cuja existência deve ser verificada. |
| OutputClaim | outputClaim | booleano | O ClaimType produzido depois de invocar esta ClaimsTransformation. |

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
| InputClaim | texto não criptografado | string | A declaração de entrada a ser criptografada |
| InputClaim | sal | string | O parâmetro sal. Você pode criar um valor aleatório, usando a transformação de declarações `CreateRandomString`. |
| InputParameter | randomizerSecret | string | Aponta para **chaves de política** existentes do Azure AD B2C. Para criar uma nova: no seu locatário do Azure AD B2C, selecione **Configurações do B2C > Identity Experience Framework**. Selecione **Chaves de Política** para exibir as chaves disponíveis no seu locatário. Selecione **Adicionar**. Em **Opções** selecione **Manual**. Forneça um nome (o prefixo B2C_1A_ pode ser adicionado automaticamente). Na caixa Segredo, insira qualquer segredo que você quiser, como 1234567890. Em Uso da chave, selecione **Segredo**. Selecione **Criar**. |
| OutputClaim | hash | string | O ClaimType que é produzido depois de invocar esta transformação de declarações. A declaração configurada na inputClaim `plaintext`. |

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
    - **sal**: 487624568
    - **randomizerSecret**: B2C_1A_AccountTransformSecret
- Declarações de saída: 
    - **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=



