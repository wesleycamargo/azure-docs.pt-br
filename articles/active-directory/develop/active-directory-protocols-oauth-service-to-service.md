---
title: "Serviço do Azure AD para Autenticação de Serviço usando OAuth&2;.0 | Microsoft Docs"
description: "Este artigo descreve como usar mensagens HTTP para implementar a autenticação de serviço para serviço usando o fluxo de concessão de credenciais de cliente OAuth&2;.0."
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mbaldwin
editor: 
ms.assetid: a7f939d9-532d-4b6d-b6d3-95520207965d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: priyamo
translationtype: Human Translation
ms.sourcegitcommit: bcd4b21f8732cfaccf8da7d412cb2024c3bed0e4
ms.openlocfilehash: a7cfe9627c7605e0a5bd4bb86d240f3abe2e1743


---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>Serviço de chamadas de serviço usando as credenciais do cliente (segredo compartilhado ou certificados)
O Fluxo de Concessão de Credenciais do Cliente do OAuth 2.0 permite que um serviço Web (um *cliente confidencial*) use suas próprias credenciais, em vez de representar um usuário, para se autenticar ao chamar outro serviço Web. Nesse cenário, o cliente é geralmente um serviço Web de camada intermediária, um serviço daemon ou um Site. Para um nível mais alto de segurança, o AD do Azure também permite que o serviço de chamada usar um certificado (em vez de um segredo compartilhado) como uma credencial.

## <a name="client-credentials-grant-flow-diagram"></a>Fluxo de concessão de credenciais do cliente
O diagrama a seguir explica o funcionamento do fluxo de concessão de credenciais do cliente no Azure AD (Azure Active Directory).

![Fluxo de concessão de credenciais de cliente do OAuth2.0](media/active-directory-protocols-oauth-service-to-service/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. O aplicativo cliente se autentica no ponto de extremidade de emissão de token do Azure AD e solicita um token de acesso.
2. O ponto de extremidade de emissão de token do Azure AD emite o token de acesso.
3. O token de acesso é usado para a autenticação no recurso protegido.
4. Os dados do recurso protegido são retornados ao aplicativo Web.

## <a name="register-the-services-in-azure-ad"></a>Registrar os serviços no Azure AD
Registre o serviço de chamada e o serviço de recebimento no Azure AD (Azure Active Directory). Para obter instruções detalhadas, consulte [Integrando aplicativos com o Azure Active Directory](active-directory-integrating-applications.md).

## <a name="request-an-access-token"></a>Solicitar um token de acesso
Para solicitar um token de acesso, use um HTTP POST para o ponto de extremidade do Azure AD específico do locatário.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Solicitação de token de acesso de serviço para serviço
Há dois casos, dependendo se o aplicativo cliente escolhe a ser protegida por um segredo compartilhado ou um certificado.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Na primeira ocorrência: solicitação de token de acesso com um segredo compartilhado
Ao usar um segredo compartilhado, uma solicitação de token de acesso de serviço para serviço contém estes parâmetros:

| Parâmetro |  | Descrição |
| --- | --- | --- |
| grant_type |obrigatório |Especifica o tipo de resposta solicitada. Em um fluxo de Concessão de Credenciais de Cliente, o valor deve ser **client_credentials**. |
| client_id |obrigatório |Especifica a ID de cliente do Azure AD do serviço Web de chamada. Para localizar a ID do cliente do aplicativo de chamada, no [portal do Azure](https://portal.azure.com), clique em ** Active Directory**, troque o diretório, clique no aplicativo. A client_id é a *ID do Aplicativo* |
| client_secret |obrigatório |Insira uma chave registrada para a chamada daemon ou o serviço de aplicativo web no AD do Azure. Para criar uma chave no portal do Azure, clique em **do Active Directory**, alternar o diretório, clique no aplicativo, clique em **configurações**, clique em **chaves**, e adicionar uma chave.|
| recurso |obrigatório |Insira o URI da ID de Aplicativo do serviço Web de recebimento. Para localizar o URI de ID do aplicativo, no portal do Azure, clique em **do Active Directory**, alternar o diretório, clique no aplicativo de serviço e, em seguida, clique em **configurações** e **propriedades** |

#### <a name="example"></a>Exemplo
O HTTP POST a seguir solicita um token de acesso para o serviço Web https://service.contoso.com/. O `client_id` identifica o serviço Web que solicita o token de acesso.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>Segundo caso: solicitação de token de acesso com um certificado
Uma solicitação de token de acesso de serviço para serviço com certificado contém estes parâmetros:

| Parâmetro |  | Descrição |
| --- | --- | --- |
| grant_type |obrigatório |Especifica o tipo de resposta solicitada. Em um fluxo de Concessão de Credenciais de Cliente, o valor deve ser **client_credentials**. |
| client_id |obrigatório |Especifica a ID de cliente do Azure AD do serviço Web de chamada. Para localizar a ID do cliente do aplicativo de chamada, no [portal do Azure](https://portal.azure.com), clique em ** Active Directory**, troque o diretório, clique no aplicativo. A client_id é a *ID do Aplicativo* |
| client_assertion_type |obrigatório |O valor deve ser `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |obrigatório | Uma asserção (um Jason Web Token) que você precisa para criar e assinar o certificado registrado como credenciais do seu aplicativo. Consulte os seguintes detalhes sobre como registrar seu certificado e o formato da asserção.|
| recurso | obrigatório |Insira o URI da ID de Aplicativo do serviço Web de recebimento. Para localizar o URI da ID do Aplicativo, no portal do Azure, clique em **Active Directory**, clique no diretório, clique no aplicativo e em **Configurar**. |

Você observou que os parâmetros são praticamente as mesmas como no caso da solicitação, o segredo compartilhado, exceto pelo fato do parâmetro client_secret é substituído por dois parâmetros: um client_assertion_type e client_assertion.

#### <a name="format-of-the-assertion"></a>Formato da asserção
Para calcular a asserção, você provavelmente desejará usar um dos muitos [Jason Web Token](https://jwt.io/) bibliotecas no idioma de sua escolha. A informação transferida por token é:

##### <a name="header"></a>Cabeçalho

| Parâmetro |  Comentário |
| --- | --- | --- |
| `alg` | Deve ser **RS256** |
| `typ` | Deve ser **JWT** |
| `x5t` | Deve ser a impressão digital do certificado de x.&509; SHA-1 |

##### <a name="claims-payload"></a>Declarações (carga)

| Parâmetro |  Comentário |
| --- | --- | --- |
| `aud` | Público-alvo: Deve ser * *https://login.microsoftonline/*tenant_Id*oauth2/token * * |
| `exp` | Data de validade |
| `iss` | Emissor: deve ser a client_id (Id do aplicativo de serviço do cliente) |
| `jti` | GUID: a ID de JWT |
| `nbf` | Não antes: antes que o token não pode ser usado data |
| `sub` | Assunto: para `iss`, deve ser o client_id (Id do aplicativo de serviço do cliente) |

##### <a name="signature"></a>Signature
A assinatura é calculada aplicando o certificado conforme descrito na [especificação Token Web JSON RFC7519](https://tools.ietf.org/html/rfc7519)

##### <a name="example-of-a-decoded-jwt-assertion"></a>Exemplo de uma declaração JWT decodificada
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

##### <a name="example-of-encoded-jwt-assertion"></a>Exemplo de asserção codificado do JWT
A cadeia de caracteres a seguir é um exemplo de asserção codificado. Se você olhar com cuidado, você observe três seções separadas por pontos (.). A primeira seção codifica o cabeçalho, o segundo a carga e o último é a assinatura computada com os certificados do conteúdo das duas primeiras seções.
```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

#### <a name="how-to-register-your-certificate-with-azure-ad"></a>Como registrar seu certificado com o Azure AD?
Para associar as credenciais de certificado com o aplicativo cliente no AD do Azure, você precisa editar o manifesto do aplicativo. Com a suspensão de um certificado, você precisa calcular:
- `$base64Thumbprint`, que é o base64 codificação do certificado Hash
- `$base64Value`, que é o base64 codificação dos dados brutos do certificado

Você também precisa fornecer um GUID para identificar a chave no manifesto do aplicativo (`$keyId`)

No registro do aplicativo do portal do Azure para o aplicativo cliente, clique em **manifesto**, e **baixar**.
Abra o manifesto se seu editor de texto favorito e substitua a propriedade *keycredentials* com as novas informações de certificado usando o esquema a seguir:
```
"keyCredentials": [
    {
        "customKeyIdentifier": "$base64Thumbprint",
        "keyId": "$keyid",
        "type": "AsymmetricX509Cert",
        "usage": "Verify",
        "value":  "$base64Value"
    }
],
```
Salvar as edições ao manifesto do aplicativo e carregá-lo novamente no AD do Azure clicando em **manifesto** e **carregar**. A propriedade keycredentials tem valores múltiplos, portanto você pode carregar vários certificados de gerenciamento de chave mais avançados.


#### <a name="example-of-request"></a>Exemplo de solicitação
O HTTP POST a seguir solicita um token de acesso para o serviço Web https://service.contoso.com/ com um certificado. O `client_id` identifica o serviço Web que solicita o token de acesso.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>Resposta de token de acesso de serviço para serviço

Uma resposta bem-sucedida contém uma resposta JSON do OAuth 2.0 com os seguintes parâmetros:

| Parâmetro | Descrição |
| --- | --- |
| access_token |O token de acesso solicitado. O serviço Web de chamada pode usar esse token para se autenticar no serviço Web de recebimento. |
| token_type |Indica o valor do tipo de token. O único tipo com suporte do Azure AD é **Portador**. Para saber mais sobre os tokens de portador, confira [Estrutura de autorização do OAuth 2.0: uso do token de portador (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt). |
| expires_in |Por quanto tempo o token de acesso é válido (em segundos). |
| expires_on |A hora de expiração do token de acesso. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até a hora de expiração. Esse valor é usado para determinar o tempo de vida de tokens em cache. |
| not_before |A hora na qual o token de acesso se torna útil. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até a hora de validade do token.|
| recurso |O URI da ID de aplicativo do serviço Web de recebimento. |

#### <a name="example-of-response"></a>Exemplo de resposta
O exemplo a seguir mostra uma resposta bem-sucedida a uma solicitação de um token de acesso para um serviço Web.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>Consulte também
* [OAuth 2.0 no Azure AD](active-directory-protocols-oauth-code.md)
* [Exemplo em C# da chamada de serviços com um segredo compartilhado](https://github.com/Azure-Samples/active-directory-dotnet-daemon) e [Exemplo em C# da chamada de serviços com um certificado](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)



<!--HONumber=Jan17_HO3-->


