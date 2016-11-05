

---
title: Fluxo de Código de Autorização de OAuth v.2.0 do Azure AD | Microsoft Docs
description: Criando aplicativos Web usando a implementação do AD do Azure do protocolo de autenticação OAuth 2.0.
services: active-directory
documentationcenter: ''
author: dstrockis
manager: msmbaldwin
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: dastrock

---
# Protocolos v2.0 - Fluxo de código de autorização do OAuth 2.0
A concessão de código de autorização OAuth 2.0 pode ser usada em aplicativos instalados em um dispositivo para obter acesso a recursos protegidos, como APIs Web. Com a implementação do modelo de aplicativo v2.0 do OAuth 2.0, você pode adicionar conexão e acesso à API aos seus aplicativos móveis e de área de trabalho. Este guia independe do idioma e descreve como enviar e receber mensagens HTTP sem usar qualquer uma das nossas bibliotecas de software livre.

<!-- TODO: Need link to libraries -->

> [!NOTE]
> Nem todos os recursos e cenários do Azure Active Directory têm suporte no ponto de extremidade v2.0. Para determinar se você deve usar o ponto de extremidade v2.0, leia sobre as [limitações da v2.0](active-directory-v2-limitations.md).
> 
> 

O fluxo do código de autorização do OAuth 2.0 é descrito na [seção 4.1 da especificação do OAuth 2.0](http://tools.ietf.org/html/rfc6749). Ele é usado para realizar a autenticação e a autorização na maioria dos tipos de aplicativo, incluindo [aplicativos web](active-directory-v2-flows.md#web-apps) e [aplicativos instalados nativamente](active-directory-v2-flows.md#mobile-and-native-apps). Ele permite que aplicativos adquiram access\_tokens com segurança, que podem ser usados para acessar recursos que são protegidos usando o ponto de extremidade v2.0.

## Diagrama de protocolo
Em um alto nível, todo o fluxo de autenticação de um aplicativo nativo/móvel é um pouco semelhante a:

![Fluxo do código de autenticação do OAuth](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## Solicitar um código de autorização
O fluxo do código de autorização começa com o cliente direcionando o usuário para o ponto de extremidade `/authorize`. Nessa solicitação, o cliente indica as permissões que precisa adquirir do usuário:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

> [!TIP]
> Clique no link a seguir para executar essa solicitação! Depois de entrar, seu navegador deverá ser redirecionado para `https://localhost/myapp/` com um `code` na barra de endereço. <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

| Parâmetro |  | Descrição |
| --- | --- | --- |
| locatário |obrigatório |O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| client\_id |obrigatório |A Id de Aplicativo que o portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) atribuiu ao aplicativo. |
| response\_type |obrigatório |Deve incluir `code` para o fluxo do código de autorização. |
| redirect\_uri |recomendável |O redirect\_uri do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve corresponder exatamente a um dos redirect\_uris que você registrou no portal, com exceção de que ele deve ser codificado por url. Para aplicativos nativos e móveis, você deve usar o valor padrão de `https://login.microsoftonline.com/common/oauth2/nativeclient`. |
| scope |obrigatório |Uma lista separada por espaços de [escopos](active-directory-v2-scopes.md) para os quais você deseja o consentimento do usuário. |
| response\_mode |recomendável |Especifica o método que deve ser usado para enviar o token resultante de volta ao aplicativo. Pode ser `query` ou `form_post`. |
| state |recomendável |Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor exclusivo gerado aleatoriamente normalmente é usado para [impedir ataques de solicitação intersite forjada](http://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| prompt |opcional |Indica o tipo de interação do usuário que é necessário. Os únicos valores válidos no momento são 'login', 'none', 'consent'. O `prompt=login` forçará o usuário a inserir suas credenciais na solicitação, eliminando o logon único. `prompt=none` é o oposto. Ele garantirá que o usuário não receba qualquer prompt interativo. Se não for possível concluir a solicitação silenciosamente por meio do logon único, o ponto de extremidade v 2.0 retornará um erro. `prompt=consent` acionará a caixa de diálogo de consentimento do OAuth depois que o usuário fazer logon, pedindo que o usuário conceda permissões para o aplicativo. |
| login\_hint |opcional |Pode ser usado para preencher previamente o campo de nome de usuário/endereço de email da página de entrada do usuário, se você souber o nome de usuário com antecedência. Geralmente, os aplicativos usarão esse parâmetro durante a reautenticação, após já terem extraído o nome de usuário de uma entrada anterior usando a declaração `preferred_username`. |
| domain\_hint |opcional |Pode ser `consumers` ou `organizations`. Se for incluído, ele ignorará o processo de descoberta baseada em email que o usuário passa na página de entrada v 2.0, resultando em uma experiência de usuário um pouco mais simples. Geralmente, os aplicativos usam esse parâmetro durante a reautenticação, extraindo `tid` de uma entrada anterior. Se o valor da declaração `tid` for `9188040d-6c67-4c5b-b112-36a304b66dad`, você deverá usar `domain_hint=consumers`. Caso contrário, use `domain_hint=organizations`. |

Nesse ponto, será solicitado que o usuário insira suas credenciais e conclua a autenticação. O ponto de extremidade v2.0 também garantirá que o usuário tenha consentido as permissões indicadas no parâmetro de consulta `scope`. Se o usuário não tiver consentido nenhuma dessas permissões, ele será solicitado a consentir as permissões necessárias. Os detalhes dos aplicativos quanto a [permissões, consentimento e multilocatário são fornecidos aqui](active-directory-v2-scopes.md).

Depois que o usuário se autentica e dá consentimento, o ponto de extremidade v2.0 retorna uma resposta ao aplicativo no `redirect_uri` indicado usando o método especificado no parâmetro `response_mode`.

#### Resposta bem-sucedida
Uma resposta bem-sucedida usando `response_mode=query` tem a seguinte aparência:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| Parâmetro | Descrição |
| --- | --- |
| código |O authorization\_code que o aplicativo solicitou. O aplicativo pode usar o código de autorização para solicitar um token de acesso para o recurso de destino. Authorization\_codes têm uma duração bastante curta, geralmente, expirando depois de aproximadamente 10 minutos. |
| state |Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

#### Resposta de erro
As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente:

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error\_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

#### Códigos de erro para erros de ponto de extremidade de autorização
A tabela a seguir descreve os vários códigos de erro que podem ser retornados no parâmetro `error` da resposta de erro.

| Código do Erro | Descrição | Ação do Cliente |
| --- | --- | --- |
| invalid\_request |Erro de protocolo, como um parâmetro obrigatório ausente. |Corrija e reenvie a solicitação. Esse é um erro de desenvolvimento normalmente identificado durante os testes iniciais. |
| unauthorized\_client |O aplicativo cliente não tem permissão para solicitar um código de autorização. |Isso geralmente ocorre quando o aplicativo cliente não está registrado no Azure AD ou não é adicionado ao locatário do Azure AD do usuário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |
| access\_denied |Consentimento negado pelo proprietário do recurso |O aplicativo cliente pode notificar o usuário de que não pode continuar, a menos que o usuário consinta. |
| unsupported\_response\_type |O servidor de autorização não dá suporta ao tipo de resposta na solicitação. |Corrija e reenvie a solicitação. Esse é um erro de desenvolvimento normalmente identificado durante os testes iniciais. |
| server\_error |O servidor encontrou um erro inesperado. |Tente novamente a solicitação. Esses erros podem resultar de condições temporárias. O aplicativo cliente pode explicar ao usuário que sua resposta está atrasada devido a um erro temporário. |
| temporarily\_unavailable |O servidor está temporariamente muito ocupado para tratar da solicitação. |Tente novamente a solicitação. O aplicativo cliente pode explicar para o usuário que sua resposta está atrasada devido a uma condição temporária. |
| invalid\_resource |O recurso de destino é inválido porque não existe, o Azure AD não consegue encontrá-lo ou ele não está configurado corretamente. |Isso indica que o recurso, se ele existe, não foi configurado no locatário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |

## Solicitar um token de acesso
Agora que você já adquiriu um authorization\_code e recebeu permissão do usuário, é possível resgatar o `code` de um `access_token` para o recurso desejado enviando uma solicitação `POST` ao ponto de extremidade `/token`:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [!TIP]
> Tente executar a solicitação no Postman! (Não se esqueça de substituir o `code`) [![Executar no Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)
> 
> 

| Parâmetro |  | Descrição |
| --- | --- | --- |
| locatário |obrigatório |O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| client\_id |obrigatório |A Id de Aplicativo que o portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) atribuiu ao aplicativo. |
| grant\_type |obrigatório |Deve ser `authorization_code` para o fluxo do código de autorização. |
| scope |obrigatório |Uma lista de escopos separados por espaços. Os escopos solicitados nessa ramificação devem ser equivalentes aos escopos solicitados na primeira ramificação, ou um subconjunto desses escopos. Se os escopos especificados nessa solicitação incluírem vários servidores de recursos, o ponto de extremidade v2.0 retornará um token para o recurso especificado no primeiro escopo. Para obter uma explicação mais detalhada de escopos, confira [permissões, consentimento e escopos](active-directory-v2-scopes.md). |
| código |obrigatório |O authorization\_code que você adquiriu na primeira ramificação do fluxo. |
| redirect\_uri |obrigatório |O mesmo valor de redirect\_uri que foi usado para adquirir o authorization\_code. |
| client\_secret |obrigatório para aplicativos Web |O segredo do aplicativo que você criou no portal de registro do aplicativo para seu aplicativo. Ele não deve ser usado em um aplicativo nativo, pois client\_secrets não podem ser armazenados de modo confiável em dispositivos. Ele é obrigatório para aplicativos Web e APIs Web, que têm a capacidade de armazenar o client\_secret com segurança no servidor. |

#### Resposta bem-sucedida
Uma resposta de token bem-sucedida se parecerá com esta:

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parâmetro | Descrição |
| --- | --- |
| access\_token |O token de acesso solicitado. O aplicativo pode usar esse token para se autenticar no recurso protegido, como uma API Web. |
| token\_type |Indica o valor do tipo de token. O único tipo que oferece suporte ao AD do Azure é Portador |
| expires\_in |Por quanto tempo o token de acesso é válido (em segundos). |
| scope |Os escopos para os quais o access\_token é válido. |
| refresh\_token |Um token de atualização do OAuth 2.0. O aplicativo pode usar esse token para adquirir tokens de acesso adicionais depois que o token de acesso atual expira. Os Refresh\_tokens têm longa duração e podem ser usados para reter acesso a recursos por períodos estendidos. Para obter mais detalhes, confira a [referência ao token v2.0](active-directory-v2-tokens.md). |
| id\_token |Um JWT (Token Web JSON) não assinado. O aplicativo pode decodificar com base64Url os segmentos desse token para solicitar informações sobre o usuário que se conectou. O aplicativo pode armazenar em cache os valores e exibi-los, mas não deve depender deles para qualquer autorização ou limites de segurança. Para saber mais sobre id\_tokens, veja a [referência do token do ponto de extremidade v2.0](active-directory-v2-tokens.md). |

#### Resposta de erro
As respostas de erro serão parecidas com esta:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parâmetro | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error\_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |
| error\_codes |Uma lista de códigos de erro específicos do STS que pode ajudar no diagnóstico. |
| timestamp |A hora na qual o erro ocorreu. |
| trace\_id |Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico. |
| correlation\_id |Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico entre os componentes. |

#### Códigos de erro para erros de ponto de extremidade de token
| Código do Erro | Descrição | Ação do Cliente |
| --- | --- | --- |
| invalid\_request |Erro de protocolo, como um parâmetro obrigatório ausente. |Corrija e reenvie a solicitação |
| invalid\_grant |O código de autorização é inválido ou expirou. |Tente uma nova solicitação para o `/authorize` ponto de extremidade |
| unauthorized\_client |O cliente autenticado não está autorizado a usar esse tipo de concessão de autorização. |Isso geralmente ocorre quando o aplicativo cliente não está registrado no Azure AD ou não é adicionado ao locatário do Azure AD do usuário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |
| invalid\_client |Falha na autenticação de cliente. |As credenciais do cliente não são válidas. Para corrigi-las, o administrador do aplicativo atualiza as credenciais. |
| unsupported\_grant\_type |O servidor de autorização não dá suporte ao tipo de concessão de autorização. |Altere o tipo de concessão na solicitação. Esse tipo de erro deve ocorrer somente durante o desenvolvimento e ser detectado durante os testes iniciais. |
| invalid\_resource |O recurso de destino é inválido porque não existe, o Azure AD não consegue encontrá-lo ou ele não está configurado corretamente. |Isso indica que o recurso, se ele existe, não foi configurado no locatário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |
| interaction\_required |A solicitação requer interação do usuário. Por exemplo, é necessária uma etapa de autenticação adicional. |Repita a solicitação com o mesmo recurso. |
| temporarily\_unavailable |O servidor está temporariamente muito ocupado para tratar da solicitação. |Tente novamente a solicitação. O aplicativo cliente pode explicar para o usuário que sua resposta está atrasada devido a uma condição temporária. |

## Usar o token de acesso
Agora que você já adquiriu com êxito um `access_token`, você pode usar o token em solicitações para APIs Web incluindo-o no cabeçalho `Authorization`:

> [!TIP]
> Execute essa solicitação no Postman! (Substitua o cabeçalho `Authorization` primeiro) [![Executar no Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)
> 
> 

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## Atualizar o token de acesso
Os access\_tokens têm curta duração e você deve atualizá-los depois que eles expiram para continuar acessando recursos. Faça isso enviando outra solicitação `POST` ao ponto de extremidade `/token`, dessa vez fornecendo `refresh_token` em vez de `code`:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps
```

> [!TIP]
> Tente executar a solicitação no Postman! (Não se esqueça de substituir o `refresh_token`) [![Executar no Postman](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)
> 
> 

| Parâmetro |  | Descrição |
| --- | --- | --- |
| locatário |obrigatório |O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| client\_id |obrigatório |A Id de Aplicativo que o portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) atribuiu ao aplicativo. |
| grant\_type |obrigatório |Deve ser `refresh_token` para essa ramificação do código de autorização. |
| scope |obrigatório |Uma lista de escopos separados por espaços. Os escopos solicitados nessa ramificação devem ser equivalentes aos escopos solicitados na ramificação de solicitação authorization\_code original, ou um subconjunto desses escopos. Se os escopos especificados nessa solicitação incluírem vários servidores de recursos, o ponto de extremidade v2.0 retornará um token para o recurso especificado no primeiro escopo. Para obter uma explicação mais detalhada de escopos, confira [permissões, consentimento e escopos](active-directory-v2-scopes.md). |
| refresh\_token |obrigatório |O refresh\_token que você adquiriu na segunda ramificação do fluxo. |
| redirect\_uri |obrigatório |O mesmo valor de redirect\_uri que foi usado para adquirir o authorization\_code. |
| client\_secret |obrigatório para aplicativos Web |O segredo do aplicativo que você criou no portal de registro do aplicativo para seu aplicativo. Ele não deve ser usado em um aplicativo nativo, pois client\_secrets não podem ser armazenados de modo confiável em dispositivos. Ele é obrigatório para aplicativos Web e APIs Web, que têm a capacidade de armazenar o client\_secret com segurança no servidor. |

#### Resposta bem-sucedida
Uma resposta de token bem-sucedida se parecerá com esta:

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Parâmetro | Descrição |
| --- | --- |
| access\_token |O token de acesso solicitado. O aplicativo pode usar esse token para se autenticar no recurso protegido, como uma API Web. |
| token\_type |Indica o valor do tipo de token. O único tipo que oferece suporte ao AD do Azure é Portador |
| expires\_in |Por quanto tempo o token de acesso é válido (em segundos). |
| scope |Os escopos para os quais o access\_token é válido. |
| refresh\_token |Um novo token de atualização OAuth 2.0. Você deve substituir o token de atualização antigo por esse token de atualização recém-adquirido para garantir que seus tokens de atualização permaneçam válidos pelo máximo tempo possível. |
| id\_token |Um JWT (Token Web JSON) não assinado. O aplicativo pode decodificar com base64Url os segmentos desse token para solicitar informações sobre o usuário que se conectou. O aplicativo pode armazenar em cache os valores e exibi-los, mas não deve depender deles para qualquer autorização ou limites de segurança. Para saber mais sobre id\_tokens, veja a [referência do token do ponto de extremidade v2.0](active-directory-v2-tokens.md). |

#### Resposta de erro
```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parâmetro | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error\_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |
| error\_codes |Uma lista de códigos de erro específicos do STS que pode ajudar no diagnóstico. |
| timestamp |A hora na qual o erro ocorreu. |
| trace\_id |Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico. |
| correlation\_id |Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico entre os componentes. |

Para obter uma descrição dos códigos de erro e a ação recomendada do cliente, veja [Códigos de erro para erros de ponto de extremidade de token](#error-codes-for-token-endpoint-errors).

<!---HONumber=AcomDC_0928_2016-->