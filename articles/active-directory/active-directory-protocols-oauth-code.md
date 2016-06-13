<properties
	pageTitle="Visão geral do protocolo .NET do Azure AD | Microsoft Azure"
	description="Este artigo descreve como usar mensagens HTTP para autorizar o acesso a aplicativos Web e a APIs da Web em seu locatário usando o Azure Active Directory e o OAuth 2.0."
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
	ms.date="01/21/2016"
	ms.author="priyamo"/>


# Autorizar o acesso aos aplicativos Web usando o OAuth 2.0 e o Azure Active Directory

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

O Azure AD (Azure Active Directory) usa o OAuth 2.0 para permitir que você autorize o acesso a aplicativos Web e APIs da Web em seu locatário do Azure AD. Este guia independe do idioma e descreve como enviar e receber mensagens HTTP sem usar qualquer uma das nossas bibliotecas de software livre.

O fluxo do código de autorização do OAuth 2.0 é descrito na [seção 4.1 da especificação do OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.1). Ele é usado para realizar a autenticação e a autorização na maioria dos tipos de aplicativo, incluindo aplicativos web e aplicativos instalados nativamente.

[AZURE.INCLUDE [active-directory-protocols-getting-started](../../includes/active-directory-protocols-getting-started.md)]


## Fluxo de autorização do OAuth 2.0

Em um alto nível, todo o fluxo de autorização de um aplicativo é semelhante a:

![Fluxo do código de autenticação do OAuth](media/active-directory-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)


## Solicitar um código de autorização

O fluxo do código de autorização começa com o cliente direcionando o usuário para o ponto de extremidade `/authorize`. Nessa solicitação, o cliente indica as permissões que precisa adquirir do usuário: Você pode obter os pontos de extremidade do OAuth 2.0 da página do seu aplicativo no Portal Clássico do Azure, no botão **Exibir Pontos de Extremidade** na gaveta inferior.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| locatário | obrigatório | O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são identificadores de locatário, por exemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` para tokens independentes de locatário |
| client\_id | obrigatório | A ID de aplicativo atribuída ao seu aplicativo quando você o registra no Azure AD. Você pode encontrar isso no Portal de Gerenciamento do Azure. Clique em **Active Directory**, clique no diretório, clique no aplicativo e clique em **Configurar** |
| response\_type | obrigatório | Deve incluir `code` para o fluxo do código de autorização. |
| redirect\_uri | recomendável | O redirect\_uri do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve corresponder exatamente a um dos redirect\_uris que você registrou no portal, com exceção de que ele deve ser codificado por url. Para aplicativos nativos e móveis, você deve usar o valor padrão `urn:ietf:wg:oauth:2.0:oob`. |
| scope | obrigatório | Uma lista separada por espaços de [escopos](active-directory-v2-scopes.md) para os quais você deseja o consentimento do usuário. |
| response\_mode | recomendável | Especifica o método que deve ser usado para enviar o token resultante de volta ao aplicativo. Pode ser `query` ou `form_post`. |
| state | recomendável | Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor exclusivo gerado aleatoriamente que normalmente é usado para [impedir ataques de solicitação intersite forjada](http://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| prompt | opcional | Indica o tipo de interação do usuário que é necessário. Os únicos valores válidos no momento são 'login', 'none', 'consent'. O `prompt=login` forçará o usuário a inserir suas credenciais na solicitação, eliminando o logon único. `prompt=none` é o oposto. Ele garantirá que o usuário não receba qualquer prompt interativo. Se não for possível concluir a solicitação silenciosamente por meio do logon único, o ponto de extremidade v 2.0 retornará um erro. `prompt=consent` acionará a caixa de diálogo de consentimento do OAuth depois que o usuário fazer logon, pedindo que o usuário conceda permissões para o aplicativo. |
| login\_hint | opcional | Pode ser usado para preencher previamente o campo de nome de usuário/endereço de email da página de entrada do usuário, se você souber o nome de usuário com antecedência. Geralmente, os aplicativos usarão esse parâmetro durante a reautenticação, após já terem extraído o nome de usuário de uma entrada anterior usando a declaração `preferred_username`. |
| domain\_hint | opcional | Pode ser um `consumers` ou `organizations`. Se for incluído, ele ignorará o processo de descoberta baseada em email que o usuário passa na página de entrada v 2.0, resultando em uma experiência de usuário um pouco mais simples. Geralmente, os aplicativos usam esse parâmetro durante a reautenticação, extraindo `tid` de uma entrada anterior. Se o valor da declaração `tid` for `9188040d-6c67-4c5b-b112-36a304b66dad`, você deverá usar `domain_hint=consumers`. Caso contrário, use `domain_hint=organizations`. |

> [AZURE.NOTE] Se o usuário fizer parte de uma organização, um administrador da organização poderá consentir ou recusar em nome do usuário ou permitir que o usuário consinta. O usuário terá a opção de consentir apenas quando o administrador permitir.

Neste ponto, o usuário deverá inserir suas credenciais e consentir as permissões indicadas no parâmetro de consulta `scope`. Depois que o usuário é autenticado e recebe a permissão de consentimento, o Azure AD envia uma resposta ao seu aplicativo no endereço `redirect_uri` em sua solicitação.

### Resposta bem-sucedida

Uma resposta bem-sucedida se parece com esta:

```
http://localhost:12345/?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&state=12345
&session_state=733ad279-b681-49c3-9215-951abf94d2c5
```

| Parâmetro | Descrição |
| -----------------------| --------------- |
| admin\_consent | O valor será True se um administrador tiver consentido um prompt de solicitação de consentimento.|
| código | O código de autorização solicitado pelo aplicativo. O aplicativo pode usar o código de autorização para solicitar um token de acesso para o recurso de destino. |
| session\_state | Um valor exclusivo que identifica a sessão de usuário atual. Esse valor é um GUID, mas deve ser tratado como um valor opaco que é transmitido sem verificação. |
| state | Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação
Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor aparecerá na resposta. É uma boa prática fazer o aplicativo verificar se os valores de estado na solicitação e na resposta são idênticos.

### Resposta de erro

As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

## Usar o código de autorização para solicitar um token de acesso

Agora que você já adquiriu um código de autorização e recebeu permissão do usuário, poderá resgatar o código de um token de acesso para o recurso desejado ao enviar uma solicitação POST para o ponto de extremidade `/token`:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: client_secret only required for web apps
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------------- |
| locatário | obrigatório | O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são identificadores de locatário, por exemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` para tokens independentes de locatário |
| client\_id | obrigatório | A ID de aplicativo atribuída ao seu aplicativo quando você o registra no Azure AD. Você poderá encontrá-la no Portal Clássico do Azure. Clique em **Active Directory**, clique no diretório, clique no aplicativo e clique em **Configurar** |
| grant\_type | obrigatório | Deve ser `authorization_code` para o fluxo do código de autorização. |
| código | obrigatório | O `authorization_code` que você adquiriu na seção anterior |
| redirect\_uri | obrigatório | O mesmo valor `redirect_uri` usado para adquirir o `authorization_code`. |
| client\_secret | obrigatório para aplicativos Web | O segredo do aplicativo que você criou no portal de registro do aplicativo para seu aplicativo. Ele não deve ser usado em um aplicativo nativo, pois client\_secrets não podem ser armazenados de modo confiável em dispositivos. Ele é obrigatório para aplicativos Web e APIs Web, que têm a capacidade de armazenar o `client_secret` com segurança no servidor. |


### Resposta bem-sucedida

Uma resposta bem-sucedida se parece com esta:

```
{
  "access_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
"id_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.”
}

```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| access\_token | O token de acesso solicitado. O aplicativo pode usar esse token para se autenticar no recurso protegido, como uma API Web. |
| token\_type | Indica o valor do tipo de token. O único tipo com suporte do Azure AD é Portador Para saber mais sobre os tokens de portador, confira [Estrutura de autorização do OAuth 2.0: uso do token de portador (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires\_in | Por quanto tempo o token de acesso é válido (em segundos). |
| scope | As permissões de representação concedidas ao aplicativo cliente. A permissão padrão é `user_impersonation`. O proprietário do recurso protegido pode registrar valores adicionais no AD do Azure.|
| refresh\_token | Um token de atualização do OAuth 2.0. O aplicativo pode usar esse token para adquirir tokens de acesso adicionais depois que o token de acesso atual expira. Os tokens de atualização têm longa duração e podem ser usados para reter acesso a recursos por períodos estendidos. |
| id\_token | Um JWT (Token Web JSON) não assinado. O aplicativo pode decodificar com base64Url os segmentos desse token para solicitar informações sobre o usuário que se conectou. O aplicativo pode armazenar em cache os valores e exibi-los, mas não deve depender deles para qualquer autorização ou limites de segurança. |

### Declarações de token JWT
O token JWT no valor do parâmetro `id_token` pode ser decodificado para as seguintes declarações:

```
{
 "typ": "JWT",
 "alg": "none"
}.
{
 "aud": "2d4d11a2-f814-46a7-890a-274a72a7309e",
 "iss": "https://sts.windows.net/7fe81447-da57-4385-becb-6de57f21477e/",
 "iat": 1388440863,
 "nbf": 1388440863,
 "exp": 1388444763,
 "ver": "1.0",
 "tid": "7fe81447-da57-4385-becb-6de57f21477e",
 "oid": "68389ae2-62fa-4b18-91fe-53dd109d74f5",
 "upn": "frank@contoso.com",
 "unique_name": "frank@contoso.com",
 "sub": "JWvYdCWPhhlpS1Zsf7yYUxShUwtUm5yzPmw_-jX3fHY",
 "family_name": "Miller",
 "given_name": "Frank"
}.
```

O parâmetro `id_token` inclui os tipos de declaração a seguir. Para saber mais sobre os tokens Web JSON, confira a [especificação de rascunho IETF JWT](http://go.microsoft.com/fwlink/?LinkId=392344). Para saber mais sobre os tipos de token e declarações, consulte [Tipos de token e de declaração com suporte](active-directory-token-and-claims.md)

| Tipo de declaração | Descrição |
|------------|-------------|
| aud | Público-alvo do token. Quando o token é emitido para um aplicativo cliente, o público-alvo será o `client_id` do cliente.
| exp | Hora de expiração. A hora em que o token expira. Para que o token seja válido, a data/hora atual deve ser menor ou igual ao valor `exp`. A hora é representada como o número de segundos de 1º de janeiro de 1970 (1970-01-01T0:0:0Z) UTC até a hora em que o token foi emitido. |
| family\_name | Sobrenome do usuário. O aplicativo pode exibir esse valor. |
| given\_name | Nome do usuário. O aplicativo pode exibir esse valor. |
| iat | Hora da emissão. A hora em que o JWT foi emitido. A hora é representada como o número de segundos de 1º de janeiro de 1970 (1970-01-01T0:0:0Z) UTC até a hora em que o token foi emitido. |
| iss | Identifica o emissor do token |
| nbf | Não antes de. A hora em que o token entra em vigor. Para que o token seja válido, a data/hora atual deve ser maior ou igual ao valor de Nbf. A hora é representada como o número de segundos de 1º de janeiro de 1970 (1970-01-01T0:0:0Z) UTC até a hora em que o token foi emitido. |
| oid | O identificador (ID) do objeto do usuário no Azure AD. |
| sub | Identificador do assunto do token. Este é um identificador persistente e imutável para o usuário descrito pelo token. Use esse valor na lógica de cache. |
| tid | O identificador (ID) do locatário do Azure AD que emitiu o token. |
| unique\_name | Um identificador exclusivo que pode ser exibido para o usuário. Geralmente é um nome de usuário principal (UPN). |
| upn | Nome UPN do usuário. |
| ver | Versão. A versão do token JWT, normalmente 1.0. |

### Resposta de erro

Uma resposta de erro de exemplo se parece com esta:

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| error | Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error\_description | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |
| error\_codes | Uma lista de códigos de erro específicos do STS que pode ajudar no diagnóstico. |
| timestamp | A hora na qual o erro ocorreu. |
| trace\_id | Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico. |
| correlation\_id | Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico entre os componentes.|

## Usar o token de acesso para acessar o recurso

Agora que você já adquiriu com êxito um `access_token`, você pode usar o token em solicitações para APIs Web incluindo-o no cabeçalho `Authorization`. A especificação [RFC 6750](http://www.rfc-editor.org/rfc/rfc6750.txt) explica como usar os tokens de portador em solicitações HTTP para acessar recursos protegidos.

### Solicitação de exemplo

```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

## Atualização dos tokens de acesso

Os Tokens de Acesso têm curta duração e deverão ser atualizados depois de expirados para continuarem acessando recursos. Você pode atualizar o `access_token` ao enviar outra solicitação `POST` ao ponto de extremidade `/token`, mas dessa vez fornecendo o `refresh_token` em vez do `code`.

O tempo de vida de um token de atualização não é fornecido e varia de acordo com as configurações de política e com a hora em que a concessão de código de autorização é revogada pelo Azure AD. Seu aplicativo deve esperar e lidar com casos em que a solicitação de um novo token de acesso falha. Nesse caso, ele deverá retornar para o código que solicita um novo token de acesso.

Uma solicitação de exemplo para o ponto de extremidade **específico do locatário** (você também pode usar o ponto de extremidade **comum**) para acessar um novo token usando um token de atualização que tem esta aparência:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```
| Parâmetro | Descrição |
|-----------|-------------|
| access\_token | O novo token de acesso solicitado.|
| expires\_in | O tempo de vida restante do token em segundos. Um valor típico é 3600 (uma hora). |
| expires\_on | A data e a hora em que o token expira. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até a hora de expiração. |
| refresh\_token | Um novo refresh\_token do OAuth 2.0 que pode ser usado para solicitar novos tokens de acesso quando um expirar nesta resposta. |
| recurso | Identifica o recurso protegido que pode ser acessado pelo token de acesso. |
| scope | As permissões de representação concedidas ao aplicativo cliente nativo. A permissão padrão é **user\_impersonation**. O proprietário do recurso de destino pode registrar valores alternativos no Azure AD. |
| token\_type | O tipo de token. O único valor com suporte é **portador**. |

### Resposta bem-sucedida

Uma resposta de token bem-sucedida se parecerá com esta:

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```

### Resposta de erro

Uma resposta de erro de exemplo se parece com esta:

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872.  This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant.  You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

<!---HONumber=AcomDC_0601_2016-->