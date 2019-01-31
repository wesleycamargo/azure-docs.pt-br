---
title: Exemplos de transformação de declarações de data do esquema do Identity Experience Framework do Azure Active Directory B2C | Microsoft Docs
description: Exemplos de transformação de declarações de data para o esquema da Estrutura de Experiência de Identidade do Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 35c5731d260fb63dece3d2b8ae4f4a4522fb91b8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55153397"
---
# <a name="date-claims-transformations"></a>Transformações de declarações de data

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para usar as transformações de declarações de data do esquema do Identity Experience Framework no Azure AD (Azure Active Directory) B2C. Para obter mais informações, confira [ClaimsTransformations](claimstransformations.md).

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeIsGreaterThan 

Verifica se uma declaração de data e hora (tipo de dados de cadeia de caracteres) é posterior a uma segunda declaração de data e hora (tipo de dados de cadeia de caracteres) e gera uma exceção.

| item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | leftOperand | string | O tipo da primeira declaração, que deve ser posterior à segunda declaração. |
| InputClaim | rightOperand | string | O tipo da segunda declaração, que deve ser anterior à primeira declaração. |
| InputParameter | AssertIfEqualTo | booleano | Especifica se essa declaração deve passar se o operando esquerdo for igual ao operando direito. |
| InputParameter | AssertIfRightOperandIsNotPresent | booleano | Especifica se essa declaração deve passar se o operando à direita estiver ausente. |
| InputParameter | TreatAsEqualIfWithinMillseconds | int | Especifica o número de milissegundos para permitir entre as duas datas e horas para considerar os tempos de igual (por exemplo, a conta para defasagem horária). |

A transformação de declarações **AssertDateTimeIsGreaterThan** é sempre executada a partir um [perfil técnico de validação](validation-technical-profile.md) que é chamado por um [perfil técnico autodeclarado](self-asserted-technical-profile.md). Os metadados **DateTimeGreaterThan** do perfil técnico autodeclarado controla a mensagem de erro que o perfil técnico apresenta ao usuário.

![Execução do AssertStringClaimsAreEqual](./media/date-transformations/assert-execution.png)

O exemplo a seguir compara as `currentDateTime` declarações à declaração `approvedDateTime`. Um erro será gerado se `currentDateTime` for posterior a `approvedDateTime`. A transformação trata valores como iguais se eles estiverem dentro de diferença de (30000 milissegundos) de 5 minutos.

```XML
<ClaimsTransformation Id="AssertApprovedDateTimeLaterThanCurrentDateTime" TransformationMethod="AssertDateTimeIsGreaterThan">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="approvedDateTime" TransformationClaimType="leftOperand" />
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="rightOperand" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
    <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
    <InputParameter Id="TreatAsEqualIfWithinMillseconds" DataType="int" Value="300000" />
  </InputParameters>
</ClaimsTransformation>
```

O perfil técnico de validação `login-NonInteractive` chama a transformação de declarações `AssertApprovedDateTimeLaterThanCurrentDateTime`.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

O perfil técnico autodeclarado chama o perfil técnico **login-NonInteractive** de validação.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="DateTimeGreaterThan">Custom error message if the provided left operand is greater than the right operand.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **leftOperand**: 2018-10-01T15:00:00.0000000Z
    - **rightOperand**: 2018-10-01T14:00:00.0000000Z
- Resultado: Erro gerado


## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeClaim

Converte uma **Data** ClaimType para um **DateTime** ClaimType. A transformação de declarações converte o formato de hora e adiciona 12:00:00 AM à data.

| item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | InputClaim | data | O ClaimType a ser convertido. |
| OutputClaim | outputClaim | dateTime | O ClaimType produzido depois de invocar esta ClaimsTransformation. |

O exemplo a seguir demonstra a conversão da declaração `dateOfBirth` (tipo de dados de data) para uma outra declaração `dateOfBirthWithTime` (tipo de dados de data e hora).

```XML
<ClaimsTransformation Id="ConvertToDateTime" TransformationMethod="ConvertDateToDateTimeClaim">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="dateOfBirth" TransformationClaimType="inputClaim" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="dateOfBirthWithTime" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputClaim**: 2019-06-01
- Declarações de saída:
    - **outputClaim**: 1559347200 (June 1, 2019 12:00:00 AM)

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

Obtenha a data UTC atual e a hora e adicione o valor para um ClaimType.

| item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | currentDateTime | dateTime | O ClaimType produzido depois de invocar esta ClaimsTransformation. |

```XML
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

* Declarações de saída:
    * **currentDateTime**: 1534418820 (August 16, 2018 11:27:00 AM)

## <a name="datetimecomparison"></a>DateTimeComparison

Determine se uma dateTime é posterior, anterior ou igual a outra. O resultado é um novo ClaimType booliano com um valor de `true` ou `false`.

| item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | firstDateTime | dateTime | O primeiro dateTime a fim de comparar se é anterior ou posterior ao segundo dateTime. Valor nulo gerará uma exceção. |
| InputClaim | secondDateTime | dateTime | O segundo dateTime a fim de comparar se é anterior ou posterior ao primeiro dateTime. Valor nulo é tratado como o dateTime atual. |
| InputParameter | operator | string | Um dos seguintes valores: mesmo, posterior ou anterior. |
| InputParameter | timeSpanInSeconds | int | Adicione o intervalo de tempo para a primeira data e hora. |
| OutputClaim | result | booleano | O ClaimType produzido depois de invocar esta ClaimsTransformation. |

Use essa transformação de declarações para determinar se duas ClaimTypes são iguais, posteriores ou anteriores em comparação com as outras. Por exemplo, você pode armazenar a última vez em que um usuário aceitou os termos de serviço (TOS). Depois de três meses, você pode pedir ao usuário para acessar o TOS novamente.
Para executar a transformação de declaração, primeiro você precisa obter a data e hora atuais e também o último usuário de tempo aceita os termos de serviço.

```XML
<ClaimsTransformation Id="CompareLastTOSAcceptedWithCurrentDateTime" TransformationMethod="DateTimeComparison">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="firstDateTime" />
    <InputClaim ClaimTypeReferenceId="extension_LastTOSAccepted" TransformationClaimType="secondDateTime" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="later than" />
    <InputParameter Id="timeSpanInSeconds" DataType="int" Value="7776000" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="result" />
  </OutputClaims>      
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **firstDateTime**: 2018-01-01T00:00:00.100000Z
    - **secondDateTime**: 2018-04-01T00:00:00.100000Z
- Parâmetros de entrada:
    - **operator**: posterior a
    - **timeSpanInSeconds**: 7776000 (90 days)
- Declarações de saída: 
    - **resultado**: verdadeiro

