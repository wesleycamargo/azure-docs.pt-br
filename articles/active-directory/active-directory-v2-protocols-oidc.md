
---
title: Protocolo do OpenID Connect v2.0 do Azure AD| Microsoft Docs
description: Compilar aplicativos Web usando a implementação v.2.0 do Azure AD do protocolo de autenticação OpenID Connect.
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
ms.date: 09/30/2016
ms.author: dastrock

---
# <a name="v2.0-protocols---openid-connect"></a>Protocolos v2.0 - OpenID Connect
O OpenID Connect é um protocolo de autenticação criado com base em OAuth 2.0 que pode ser usado para conectar com segurança os usuários em aplicativos Web.  Usando a implementação do ponto de extremidade v2.0 do OpenID Connect, você pode adicionar entrada e acesso à API aos seus aplicativos baseados na Web.  Este guia mostrará como fazer isso de maneira independente do idioma, descrevendo como enviar e receber mensagens HTTP sem usar qualquer uma das nossas bibliotecas de software livre.

> [!NOTE]
> Nem todos os recursos e cenários do Azure Active Directory têm suporte no ponto de extremidade v2.0.  Para determinar se você deve usar o ponto de extremidade v2.0, leia sobre as [limitações da v2.0](active-directory-v2-limitations.md).
> 
> 

O [OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) estende o protocolo de *autorização* do OAuth 2.0 para uso como um protocolo de *autenticação*, o que permite executar o logon único usando o OAuth.  Ele apresenta o conceito de um `id_token`, que é um token de segurança que permite ao cliente verificar a identidade do usuário e obter informações básicas de perfil sobre o usuário.  Como ele estende o OAuth 2.0, também permite que aplicativos adquiram **access_tokens** com segurança, os quais podem ser usados para acessar os recursos protegidos por um [servidor de autorização](active-directory-v2-protocols.md#the-basics).  O OpenID Connect é a nossa recomendação se você estiver criando um [aplicativo Web](active-directory-v2-flows.md#web-apps) que fica hospedado em um servidor e é acessado por meio de um navegador.

## <a name="protocol-diagram---sign-in"></a>Diagrama de Protocolo - Entrar
O fluxo de credenciais mais básico contém as seguintes etapas: cada um delas é descrita em detalhes abaixo.

![Raias do OpenId Connect](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

## <a name="fetch-the-openid-connect-metadata-document"></a>Obter o documento de metadados do OpenID Connect
O OpenID Connect descreve um documento de metadados que contém a maioria das informações necessárias para que um aplicativo inicie uma sessão.  Isso inclui informações como as URLs a serem usadas, o local das chaves de assinatura públicas do serviço e assim por diante.  Para o ponto de extremidade v2.0, você deve usar o seguinte documento de metadados do OpenID Connect:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
```

Em que o `{tenant}` pode ter um de quatro valores diferente:

| Valor | Descrição |
| --- | --- |
| `common` |Permite que os usuários com contas pessoais da Microsoft e contas corporativas/de estudante do Azure Active Directory entrem no aplicativo. |
| `organizations` |Permite apenas somente os usuários com contas corporativas/de estudante do Azure Active Directory entrem no aplicativo. |
| `consumers` |Permite que somente os usuários com MSA (Contas Pessoais da Microsoft) para entrem no aplicativo. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` |Permite que somente os usuários com contas corporativas/de estudante de um locatário específico do Azure Active Directory entrem no aplicativo.  É possível usar o nome de domínio amigável do locatário do Azure AD ou o identificador de guid de locatário. |

Os metadados são um documento json simples, um trecho de código que é fornecido abaixo.  Seu conteúdo é totalmente descrito na [especificação do OpenID Connect](https://openid.net).

```
{
  "authorization_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/authorize",
  "token_endpoint": "https:\/\/login.microsoftonline.com\/common\/oauth2\/v2.0\/token",
  "token_endpoint_auth_methods_supported": [
    "client_secret_post",
    "private_key_jwt"
  ],
  "jwks_uri": "https:\/\/login.microsoftonline.com\/common\/discovery\/v2.0\/keys",

  ...

}
```

Normalmente, você usaria este documento de metadados para configurar uma biblioteca do OpenID Connect ou o SDK. A biblioteca usaria os metadados para fazer seu trabalho.  No entanto, se você não estiver usando uma biblioteca de pré-compilação do OpenID Connect, é possível seguir as etapas no restante deste artigo para iniciar uma sessão em um aplicativo web usando o ponto de extremidade v2.0. 

## <a name="send-the-sign-in-request"></a>Enviar a solicitação de conexão
Quando o aplicativo Web precisa autenticar o usuário, ele pode direcionar o usuário para o ponto de extremidade `/authorize` .  Essa solicitação é semelhante ao primeiro segmento do [Fluxo de código de autorização do OAuth 2.0](active-directory-v2-protocols-oauth-code.md), com algumas diferenças importantes:

* A solicitação deve incluir o escopo `openid` no parâmetro `scope`.
* O parâmetro `response_type` deve incluir `id_token`
* A solicitação deve incluir o parâmetro `nonce`.

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

> [!TIP]
> Clique no link a seguir para executar essa solicitação! Depois de entrar, seu navegador deverá ser redirecionado para `https://localhost/myapp/` com um `id_token` na barra de endereços.  Observe que essa solicitação usa `response_mode=query` (para fins de tutorial apenas).  É recomendável usar `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=query&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

| Parâmetro |  | Descrição |
| --- | --- | --- |
| locatário |obrigatório |O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo.  Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário.  Para obter mais detalhes, consulte [noções básicas de protocolo](active-directory-v2-protocols.md#endpoints). |
| client_id |obrigatório |A Id de Aplicativo que o portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) atribuiu ao aplicativo. |
| response_type |obrigatório |Deve incluir `id_token` para conexão do OpenID Connect.  Também pode incluir outros response_types, como `code`. |
| redirect_uri |recomendável |O redirect_uri do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo.  Ele deve corresponder exatamente a um dos redirect_uris que você registrou no portal, com exceção de que ele deve ser codificado por url. |
| scope |obrigatório |Uma lista de escopos separados por espaços.  Para o OpenID Connect, é necessário incluir o escopo `openid`, que é traduzido para a permissão "Fazer seu logon" na interface do usuário de consentimento.  Você também pode incluir outros escopos nesta solicitação para solicitar o consentimento. |
| nonce |obrigatório |Um valor incluído na solicitação, gerado pelo aplicativo, que será incluído no id_token resultante como uma declaração.  O aplicativo pode verificar esse valor para reduzir os ataques de reprodução de token.  Normalmente, o valor é uma cadeia de caracteres aleatória e exclusiva que pode ser usada para identificar a origem da solicitação. |
| response_mode |recomendável |Especifica o método que deve ser usado para enviar o authorization_code resultante de volta ao aplicativo.  Pode ser um de 'query', 'form_post' ou 'fragment'.  Para aplicativos Web, é recomendável usar `response_mode=form_post` para garantir a transferência mais segura de tokens para seu aplicativo. |
| state |recomendável |Um valor incluído na solicitação também será retornado na resposta do token.  Pode ser uma cadeia de caracteres de qualquer conteúdo desejado.  Um valor exclusivo gerado aleatoriamente normalmente é usado para [impedir ataques de solicitação intersite forjada](http://tools.ietf.org/html/rfc6749#section-10.12).  O estado também é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| prompt |opcional |Indica o tipo de interação do usuário que é necessário.  Os únicos valores válidos no momento são 'login', 'none' e 'consent'.  `prompt=login` forçará o usuário a inserir suas credenciais na solicitação, negando o logon único.  `prompt=none` é o oposto - ele garantirá que o usuário não seja apresentado a nenhum prompt interativo.  Se a solicitação não puder ser concluída silenciosamente por meio de logon único, o ponto de extremidade v2.0 retornará um erro.  `prompt=consent` irá disparar a caixa de diálogo de consentimento do OAuth depois que o usuário iniciar a sessão, solicitando que ele conceda permissões ao aplicativo. |
| login_hint |opcional |Pode ser usado para preencher previamente o campo de nome de usuário/endereço de email da página de entrada do usuário, se você souber o nome de usuário com antecedência.  Geralmente, os aplicativos usarão esse parâmetro durante a reautenticação, após já terem extraído o nome de usuário de uma entrada anterior usando a declaração `preferred_username`. |
| domain_hint |opcional |Pode ser `consumers` ou `organizations`.  Se for incluído, ele ignorará o processo de descoberta baseada em email que o usuário passa na página de entrada v 2.0, resultando em uma experiência de usuário um pouco mais simples.  Geralmente, os aplicativos usarão esse parâmetro durante a reautenticação, extraindo a declaração `tid` do id_token.  Se o valor da declaração `tid` for `9188040d-6c67-4c5b-b112-36a304b66dad`, você deverá usar `domain_hint=consumers`.  Caso contrário, use `domain_hint=organizations`. |

Nesse ponto, será solicitado que o usuário insira suas credenciais e conclua a autenticação.  O ponto de extremidade v2.0 também garantirá que o usuário tenha consentido as permissões indicadas no parâmetro de consulta `scope` .  Se o usuário não tiver consentido nenhuma dessas permissões, ele será solicitado a consentir as permissões necessárias.  Os detalhes dos aplicativos quanto a [permissões, consentimento e multilocatário são fornecidos aqui](active-directory-v2-scopes.md).

Depois que o usuário se autentica e dá consentimento, o ponto de extremidade v2.0 retorna uma resposta ao aplicativo no `redirect_uri` indicado usando o método especificado no parâmetro `response_mode`.

#### <a name="successful-response"></a>Resposta bem-sucedida
Uma resposta bem-sucedida usando `response_mode=form_post` tem a seguinte aparência:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| . | Descrição |
| --- | --- |
| id_token |O id_token que o aplicativo solicitou. Você pode usar o id_token para verificar a identidade do usuário e iniciar uma sessão com o usuário.  Mais detalhes sobre id_tokens e o respectivo conteúdo estão incluídos na [referência ao token do ponto de extremidade v2.0](active-directory-v2-tokens.md). |
| state |Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

#### <a name="error-response"></a>Resposta de erro
As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| . | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Códigos de erro para erros de ponto de extremidade de autorização
A tabela a seguir descreve os vários códigos de erro que podem ser retornados no parâmetro `error` da resposta de erro.

| Código do Erro | Descrição | Ação do Cliente |
| --- | --- | --- |
| invalid_request |Erro de protocolo, como um parâmetro obrigatório ausente. |Corrija e reenvie a solicitação. Esse é um erro de desenvolvimento normalmente identificado durante os testes iniciais. |
| unauthorized_client |O aplicativo cliente não tem permissão para solicitar um código de autorização. |Isso geralmente ocorre quando o aplicativo cliente não está registrado no Azure AD ou não é adicionado ao locatário do Azure AD do usuário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |
| access_denied |Consentimento negado pelo proprietário do recurso |O aplicativo cliente pode notificar o usuário de que não pode continuar, a menos que o usuário consinta. |
| unsupported_response_type |O servidor de autorização não dá suporta ao tipo de resposta na solicitação. |Corrija e reenvie a solicitação. Esse é um erro de desenvolvimento normalmente identificado durante os testes iniciais. |
| server_error |O servidor encontrou um erro inesperado. |Tente novamente a solicitação. Esses erros podem resultar de condições temporárias. O aplicativo cliente pode explicar ao usuário que sua resposta está atrasada devido a um erro temporário. |
| temporarily_unavailable |O servidor está temporariamente muito ocupado para tratar da solicitação. |Tente novamente a solicitação. O aplicativo cliente pode explicar para o usuário que sua resposta está atrasada devido a uma condição temporária. |
| invalid_resource |O recurso de destino é inválido porque não existe, o Azure AD não consegue encontrá-lo ou ele não está configurado corretamente. |Isso indica que o recurso, se ele existe, não foi configurado no locatário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |

## <a name="validate-the-id_token"></a>Validar o id_token
Apenas receber o id_token não é suficiente para autenticar o usuário; você deve validar a assinatura do id_token e verificar as declarações no token de acordo com os requisitos do aplicativo.  O ponto de extremidade v2.0 usa [JWTs (Tokens Web JSON)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para assinar tokens e verificar se eles são válidos.

Você pode escolher validar o `id_token` no código do cliente, mas uma prática comum é enviar o `id_token` para um servidor de back-end e executar a validação nele.  Após a validação da assinatura do id_token, será necessário verificar algumas declarações:  Consulte a [referência do token v2.0](active-directory-v2-tokens.md) para obter mais informações, incluindo [Validando tokens](active-directory-v2-tokens.md#validating-tokens) e [Informações importantes sobre substituição de chave de assinatura](active-directory-v2-tokens.md#validating-tokens).  Há, pelo menos, uma disponível para a maioria das linguagens e plataformas.
<!--TODO: Improve the information on this-->

Talvez você também queira validar declarações adicionais, dependendo do cenário.  Algumas validações comuns incluem:

* Garantir que o usuário/organização tenha assinado para usar o aplicativo.
* Garantir que o usuário tenha autorização/privilégios adequados.
* Garantir que uma determinada intensidade de autenticação tenha ocorrido, como autenticação multifator.

Para saber mais sobre declarações em um id_token, consulte a [referência do token do ponto de extremidade v2.0](active-directory-v2-tokens.md).

Depois de ter validado completamente o id_token, você poderá iniciar uma sessão com o usuário e usar declarações no id_token para obter informações sobre o usuário no seu aplicativo.  Essas informações podem ser usadas para exibição, registros, autorizações, etc.

## <a name="send-a-sign-out-request"></a>Enviar uma solicitação de desconexão
Atualmente, o `end_session_endpoint` do OpenIdConnect não tem suporte no ponto de extremidade v2.0. Isso significa que o aplicativo não pode enviar uma solicitação ao ponto de extremidade v2.0 para encerrar uma sessão do usuário e limpar os cookies definidos pelo ponto de extremidade v2.0.
Para desconectar um usuário, o aplicativo pode simplesmente encerrar sua própria sessão com o usuário e deixar a sessão do usuário com o ponto de extremidade v2.0 intacta.  Na próxima vez que o usuário tentar se conectar, ele verá uma página "escolher conta", com as suas contas ativamente conectadas listadas.
Nessa página, o usuário pode optar por se desconectar de qualquer conta, encerrando a sessão com o ponto de extremidade v2.0.

<!--

When you wish to sign the user out of the app, it is not sufficient to clear your app's cookies or otherwise end the session with the user.  You must also redirect the user to the v2.0 endpoint for sign out.  If you fail to do so, the user will be able to re-authenticate to your app without entering their credentials again, because they will have a valid single sign-on session with the v2.0 endpoint.

You can simply redirect the user to the `end_session_endpoint` listed in the OpenID Connect metadata document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommended | The URL which the user should be redirected to after successful logout.  If not included, the user will be shown a generic message by the v2.0 endpoint.  |

-->

## <a name="protocol-diagram---token-acquisition"></a>Diagrama de Protocolos - Aquisição de Token
Muitos aplicativos Web precisam não apenas conectar o usuário, mas acessar um serviço Web em nome desse usuário usando o OAuth.  Esse cenário combina OpenID Connect para autenticação de usuário enquanto adquire simultaneamente um authorization_code que pode ser usado para obter access_tokens usando o Fluxo do código de autorização do OAuth.

O fluxo completo de credenciais no OpenID Connect e aquisição de tokens se parece com o seguinte.

![Raias do OpenId Connect](../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

## <a name="get-access-tokens"></a>Obter tokens de acesso
Para obter tokens de acesso, você precisará modificar um pouco a solicitação de entrada acima:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e      // Your registered Application Id
&response_type=id_token%20code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F       // Your registered Redirect Uri, url encoded
&response_mode=form_post                              // 'query', 'form_post', or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes your app needs  
offline_access%20                                        
https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345                                         // Any value, provided by your app
&nonce=678910                                        // Any value, provided by your app
```

> [!TIP]
> Clique no link a seguir para executar essa solicitação! Depois de entrar, seu navegador deverá ser redirecionado para `https://localhost/myapp/` com um `id_token` e um `code` na barra de endereços.  Observe que essa solicitação usa `response_mode=query` (para fins de tutorial apenas).  É recomendável usar `response_mode=form_post`.
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token%20code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

Ao incluir escopos de permissão na solicitação e usar `response_type=id_token code`, o ponto de extremidade v 2.0 terá certeza de que o usuário consentiu às permissões indicadas no parâmetro de consulta `scope` e retornará ao seu aplicativo um código de autorização para trocar por um token de acesso.

#### <a name="successful-response"></a>Resposta bem-sucedida
Uma resposta bem-sucedida usando `response_mode=form_post` tem a seguinte aparência:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| . | Descrição |
| --- | --- |
| id_token |O id_token que o aplicativo solicitou. Você pode usar o id_token para verificar a identidade do usuário e iniciar uma sessão com o usuário.  Mais detalhes sobre id_tokens e o respectivo conteúdo estão incluídos na [referência ao token do ponto de extremidade v2.0](active-directory-v2-tokens.md). |
| código |O authorization_code que o aplicativo solicitou. O aplicativo pode usar o código de autorização para solicitar um token de acesso para o recurso de destino.  Authorization_codes têm uma duração bastante curta, geralmente, expirando depois de aproximadamente 10 minutos. |
| state |Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |

#### <a name="error-response"></a>Resposta de erro
As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente:

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| . | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

Para obter uma descrição dos possíveis códigos de erro e sua ação recomendada do cliente, veja [Códigos de erro para erros de ponto de extremidade de autorização](#error-codes-for-authorization-endpoint-errors).

Após você conseguir uma autorização `code` e um `id_token`, poderá conectar o usuário e obter tokens de acesso em seu nome.  Para conectar o usuário, você deve validar o `id_token` exatamente como descrito [acima](#validating-the-id-token).  Para obter tokens de acesso, você pode seguir as etapas descritas em nossa [documentação do protocolo OAuth](active-directory-v2-protocols-oauth-code.md#request-an-access-token).

<!--HONumber=Oct16_HO2-->


