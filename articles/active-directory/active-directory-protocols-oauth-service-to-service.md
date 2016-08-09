<properties
	pageTitle="Autenticação de serviço para serviço do Azure AD usando OAuth 2.0 | Microsoft Azure"
	description="Este artigo descreve como usar mensagens HTTP para implementar a autenticação de serviço para serviço usando o fluxo de concessão de credenciais de cliente OAuth 2.0."
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/19/2016"
	ms.author="priyamo"/>

# Chamadas de serviço para serviço que usam credenciais do cliente

O Fluxo de Concessão de Credenciais do Cliente do OAuth 2.0 permite que um serviço Web (um *cliente confidencial*) use suas próprias credenciais para se autenticar ao chamar outro serviço Web, em vez de representar um usuário. Nesse cenário, o cliente é geralmente um serviço Web de camada intermediária, um serviço daemon ou um Site.

## Fluxo de concessão de credenciais do cliente

O diagrama a seguir explica o funcionamento do fluxo de concessão de credenciais do cliente no Azure AD (Azure Active Directory).

![Fluxo de concessão de credenciais de cliente do OAuth2.0](media/active-directory-protocols-oauth-service-to-service/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. O aplicativo cliente se autentica no ponto de extremidade de emissão de token do Azure AD e solicita um token de acesso.
2. O ponto de extremidade de emissão de token do Azure AD emite o token de acesso.
3. O token de acesso é usado para a autenticação no recurso protegido.
4. Os dados do recurso protegido são retornados ao aplicativo Web.

## Registrar os serviços no Azure AD

Registre o serviço de chamada e o serviço de recebimento no Azure AD (Azure Active Directory). Para obter instruções detalhadas, confira [Adicionar, atualizar e remover um aplicativo](active-directory-integrating-applications.md#BKMK_Native)

## Solicitar um token de acesso

Para solicitar um token de acesso, use um HTTP POST para o ponto de extremidade do Azure AD específico do locatário.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## Solicitação de token de acesso de serviço para serviço

Uma solicitação de token de acesso de serviço para serviço contém os parâmetros a seguir.

| Parâmetro | | Descrição |
|-----------|------|------------|
| response\_type | obrigatório | Especifica o tipo de resposta solicitada. Em um fluxo de concessão de credenciais de cliente, o valor deve ser **client\_credentials**.|
| client\_id | obrigatório | Especifica a ID de cliente do Azure AD do serviço Web de chamada. Para localizar a ID de cliente do aplicativo de chamada, no Portal de Gerenciamento do Azure, clique em **Active Directory**, clique no diretório, clique no aplicativo e, em seguida, clique em **Configurar**.|
| client\_secret | obrigatório | Insira uma chave registrada para o serviço Web de chamada no Azure AD. Para criar uma chave, no Portal de Gerenciamento do Azure, clique em **Active Directory**, clique no diretório, clique no aplicativo e, em seguida, clique em **Configurar**. |
| recurso | obrigatório | Insira o URI da ID de Aplicativo do serviço Web de recebimento. Para localizar o URI de ID do Aplicativo, no Portal de Gerenciamento do Azure, clique em **Active Directory**, clique no diretório, clique no aplicativo e, em seguida, clique em **Configurar**. |

## Exemplo

O HTTP POST a seguir solicita um token de acesso para o serviço Web https://service.contoso.com/. O `client_id` identifica o serviço Web que solicita o token de acesso.

```
POST contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

## Resposta de token de acesso de serviço para serviço

Uma resposta bem-sucedida contém uma resposta JSON do OAuth 2.0 com os parâmetros a seguir.

| Parâmetro | Descrição |
|-------------|-------------|
|access\_token |O token de acesso solicitado. O serviço Web de chamada pode usar esse token para se autenticar no serviço Web de recebimento. |
|access\_type | Indica o valor do tipo de token. O único tipo com suporte do Azure AD é **Portador**. Para saber mais sobre os tokens de portador, confira [Estrutura de autorização do OAuth 2.0: uso do token de portador (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt).
|expires\_in | Por quanto tempo o token de acesso é válido (em segundos).|
|expires\_on |A hora de expiração do token de acesso. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até a hora de expiração. Esse valor é usado para determinar o tempo de vida de tokens em cache. |
|recurso | O URI da ID de aplicativo do serviço Web de recebimento. |

## Exemplo

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

## Consulte também

* [OAuth 2.0 no Azure AD](active-directory-protocols-oauth-code.md)

<!---HONumber=AcomDC_0727_2016-->