---
title: Exemplos de transformação de declarações JSON do esquema do Identity Experience Framework do Azure Active Directory B2C | Microsoft Docs
description: Exemplos de transformação de declarações JSON do esquema do Identity Experience Framework do Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e7096773f2aaa39abd965b4697f45a3b3f80f136
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54850834"
---
# <a name="json-claims-transformations"></a>Transformações de declarações JSON

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo fornece exemplos para usar as transformações de declarações JSON do esquema do Identity Experience Framework no Azure AD (Azure Active Directory) B2C. Para obter mais informações, confira [ClaimsTransformations](claimstransformations.md).

## <a name="getclaimfromjson"></a>GetClaimFromJson

Obter um elemento especificado de dados JSON.

| item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | Os ClaimTypes que são usados pela transformação de declarações para obter o item. |
| InputParameter | claimToExtract | string | o nome do elemento JSON a ser extraído. |
| OutputClaim | extractedClaim | string | O ClaimType que é produzido após a invocação dessa transformação de declarações, o valor do elemento especificado no parâmetro de entrada _claimToExtract_. |

No exemplo a seguir, a transformação de declarações extraiu o elemento `emailAddress` dos dados JSON: `{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```XML
<ClaimsTransformation Id="GetEmailClaimFromJson" TransformationMethod="GetClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="customUserData" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="emailAddress" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="extractedEmail" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone"}
- Parâmetro de entrada:
    - **claimToExtract**: emailAddress
- Declarações de saída: 
    - **extractedClaim**: someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

Obter uma lista de elementos especificados de dados JSON.

| item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonSourceClaim | string | Os ClaimTypes que são usados pela transformação de declarações para obter as declarações. |
| InputParameter | errorOnMissingClaims | booleano | Especifica se um erro deverá ser gerado se uma das declarações estiver ausente. |
| InputParameter | includeEmptyClaims | string | Especifica se você deseja incluir declarações vazias. |
| InputParameter | jsonSourceKeyName | string | Nome da chave do elemento |
| InputParameter | jsonSourceValueName | string | Nome do valor do elemento |
| OutputClaim | Coleção | string, int, boolean e datetime |Lista de declarações a serem extraídas. O nome da declaração deve ser igual ao especificado na declaração de entrada _jsonSourceClaim_. |

No exemplo a seguir, a transformação de declarações extrai as declarações a seguir: email (string), displayName (string), membershipNum (int), active (boolean) e birthdate (datetime) dos dados JSON.

```JSON
[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```XML
<ClaimsTransformation Id="GetClaimsFromJson" TransformationMethod="GetClaimsFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="jsonSourceClaim" TransformationClaimType="jsonSource" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="errorOnMissingClaims" DataType="boolean" Value="false" />
    <InputParameter Id="includeEmptyClaims" DataType="boolean" Value="false" />
    <InputParameter Id="jsonSourceKeyName" DataType="string" Value="key" />
    <InputParameter Id="jsonSourceValueName" DataType="string" Value="value" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="membershipNum" />
    <OutputClaim ClaimTypeReferenceId="active" />
    <OutputClaim ClaimTypeReferenceId="birthdate" />
  </OutputClaims>
</ClaimsTransformation>
```    

- Declarações de entrada:
    - **jsonSourceClaim**: [{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value": true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
- Parâmetros de entrada:
    - **errorOnMissingClaims**: false
    - **includeEmptyClaims**: false
    - **jsonSourceKeyName**: key
    - **jsonSourceValueName**: value
- Declarações de saída:
    - **email**: "someone@example.com"
    - **displayName**: "Someone"
    - **membershipNum**: 6353399
    - **active**: true
    - **birthdate**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Obtém um elemento (longo) numérico especificado dos dados JSON.

| item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | string | Os ClaimTypes que são usados pela transformação de declarações para obter a declaração. |
| InputParameter | claimToExtract | string | O nome do elemento JSON a ser extraído. |
| OutputClaim | extractedClaim | longo | O ClaimType que é produzido após a invocação desse ClaimsTransformation, o valor do elemento especificado no parâmetro de entrada _claimToExtract_. |

No exemplo a seguir, a transformação de declarações extrai o elemento `id` dos dados JSON.

```JSON
{
    "emailAddress": "someone@example.com", 
    "displayName": "Someone", 
    "id" : 6353399
}
```

```XML
<ClaimsTransformation Id="GetIdFromResponse" TransformationMethod="GetNumericClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="exampleInputClaim" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="id" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="membershipId" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone", "id" : 6353399}
- Parâmetros de entrada
    - **claimToExtract**: id
- Declarações de saída: 
    - **extractedClaim**: 6353399

## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Obtém o primeiro elemento de uma matriz de dados JSON.

| item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | string | Os ClaimTypes que são usados pela transformação de declarações para obter o item da matriz JSON. |
| OutputClaim | extractedClaim | string | O ClaimType que é produzido após a invocação desse ClaimsTransformation, o primeiro elemento na matriz JSON. |

No exemplo a seguir, a transformação de declarações extrai o primeiro elemento (endereço de email) da matriz JSON `["someone@example.com", "Someone", 6353399]`.

```XML
<ClaimsTransformation Id="GetEmailFromJson" TransformationMethod="GetSingleValueFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userData" TransformationClaimType="inputJsonClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Exemplo

- Declarações de entrada:
    - **inputJsonClaim**: ["someone@example.com", "Someone", 6353399]
- Declarações de saída: 
    - **extractedClaim**: someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

Converte dados XML no formato JSON.

| item | TransformationClaimType | Tipo de Dados | Observações |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | xml | string | Os ClaimTypes que são usados pela transformação de declarações para converter os dados do formato XML para JSON. |
| OutputClaim | json | string | O ClaimType que é produzido após a invocação dessee ClaimsTransformation, os dados no formato JSON. |

```XML
<ClaimsTransformation Id="ConvertXmlToJson" TransformationMethod="XmlStringToJsonString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="intpuXML" TransformationClaimType="xml" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="outputJson" TransformationClaimType="json" />
  </OutputClaims>
</ClaimsTransformation>
```

No exemplo a seguir, a transformação de declarações converte os dados XML a seguir no formato JSON.

#### <a name="example"></a>Exemplo
Declaração de entrada:

```XML
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Declaração de saída:

```JSON
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```

