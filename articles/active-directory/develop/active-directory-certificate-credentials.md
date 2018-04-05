---
title: Credenciais de certificado no Azure AD | Microsoft Docs
description: Este artigo aborda o registro e uso de credenciais de certificado para autenticação do aplicativo
services: active-directory
documentationcenter: .net
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f7c58b4ebd840aca555b52a03cf44ace311b64e3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="certificate-credentials-for-application-authentication"></a>Credenciais de certificado para autenticação do aplicativo

O Azure Active Directory permite que um aplicativo use suas próprias credenciais para autenticação. Por exemplo, no fluxo de Concessão de Credenciais de Cliente do OAuth 2.0 ([v1](active-directory-protocols-oauth-service-to-service.md), [v2](active-directory-v2-protocols-oauth-client-creds.md)) no fluxo On-Behalf-Of ([v1](active-directory-protocols-oauth-on-behalf-of.md), [v2](active-directory-v2-protocols-oauth-on-behalf-of.md)).
Uma forma de credencial que pode ser usada é uma declaração JWT (Token Web JSON) assinada com um certificado que o aplicativo possui.

## <a name="format-of-the-assertion"></a>Formato da asserção
Para calcular a declaração, provavelmente, você desejará usar uma das muitas bibliotecas [Token Web JSON](https://jwt.ms/) no idioma de sua escolha. A informação transferida por token é:

#### <a name="header"></a>Cabeçalho

| Parâmetro |  Comentário |
| --- | --- |
| `alg` | Deve ser **RS256** |
| `typ` | Deve ser **JWT** |
| `x5t` | Deve ser a impressão digital do certificado de x. 509 SHA-1 |

#### <a name="claims-payload"></a>Declarações (carga)

| Parâmetro |  Comentário |
| --- | --- |
| `aud` | Público-alvo: Deve ser **https://login.microsoftonline.com/*tenant_Id*/oauth2/token** |
| `exp` | Data de expiração: a data de expiração do token. A hora é representada como o número de segundos de 1º de janeiro de 1970 (1970-01-01T0:0:0Z) UTC até a hora em que a validade do token expira.|
| `iss` | Emissor: deve ser a client_id (ID do aplicativo de serviço do cliente) |
| `jti` | GUID: a ID de JWT |
| `nbf` | Não Antes de: a data anterior à qual o token não pode ser usado. A hora é representada como o número de segundos de 1º de janeiro de 1970 (1970-01-01T0:0:0Z) UTC até a hora em que o token foi emitido. |
| `sub` | Assunto: para `iss`, deve ser a client_id (ID do aplicativo de serviço do cliente) |

#### <a name="signature"></a>Signature

A assinatura é calculada aplicando o certificado conforme descrito na [especificação Token Web JSON RFC7519](https://tools.ietf.org/html/rfc7519)

### <a name="example-of-a-decoded-jwt-assertion"></a>Exemplo de uma declaração JWT decodificada

```
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,  
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"

```

### <a name="example-of-an-encoded-jwt-assertion"></a>Exemplo de uma declaração JWT codificada

A cadeia de caracteres a seguir é um exemplo de asserção codificado. Se você olhar com cuidado, você observe três seções separadas por pontos (.).
A primeira seção codifica o cabeçalho, o segundo a carga e o último é a assinatura computada com os certificados do conteúdo das duas primeiras seções.
```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

### <a name="register-your-certificate-with-azure-ad"></a>Registrar o certificado com o Azure AD

Você pode associar a credencial de certificado com o aplicativo cliente no Microsoft Azure AD por meio do Portal do Azure usando qualquer um dos métodos a seguir:

**Carregando o arquivo de certificado**

No registro do aplicativo do Azure para o aplicativo cliente, clique em **Configurações**, clique em **Chaves** e, em seguida, clique em **Carregar a chave pública**. Selecione o arquivo de certificado que você deseja carregar e clique em **Salvar**. Depois que você salva, o certificado é carregado e os valores de impressão digital, data de início e vencimento são exibidos. 

**Atualizando o manifesto do aplicativo**

Com a suspensão de um certificado, você precisa calcular:

- `$base64Thumbprint`, que é o base64 codificação do certificado Hash
- `$base64Value`, que é o base64 codificação dos dados brutos do certificado

Você também deve fornecer um GUID para identificar a chave no manifesto do aplicativo (`$keyId`).

No registro de aplicativo do Azure do aplicativo cliente, abra o manifesto do aplicativo e substitua a propriedade *keyCredentials* pelas novas informações de certificado usando o seguinte esquema:

```
"keyCredentials": [
    {
        "customKeyIdentifier": "$base64Thumbprint",
        "keyId": "$keyid",
        "type": "AsymmetricX509Cert",
        "usage": "Verify",
        "value":  "$base64Value"
    }
]
```

Salve as edições no manifesto do aplicativo e carregue-o no Azure AD. A propriedade keycredentials tem valores múltiplos, portanto você pode carregar vários certificados de gerenciamento de chave mais avançados.
