
<properties
	pageTitle="Protocolo do OpenID Connect do modelo de aplicativo v2.0 | Microsoft Azure"
	description="Criando aplicativos Web usando a implementação do Azure AD do protocolo de autenticação OpenID Connect."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/09/2015"
	ms.author="dastrock"/>

# Visualização do modelo de aplicativo v2.0: protocolos — OpenID Connect
O OpenID Connect é um protocolo de autenticação criado com base em OAuth 2.0 que pode ser usado para conectar com segurança os usuários em aplicativos Web. Com a implementação do modelo de aplicativo v2.0 do OpenID Connect, você pode adicionar autenticação e acesso à API a seus aplicativos baseados na Web. Este guia mostrará como fazer isso de maneira independente do idioma, descrevendo como enviar e receber mensagens HTTP sem usar qualquer uma das nossas bibliotecas de software livre.

> [AZURE.NOTE]Essas informações se aplicam à visualização pública do modelo de aplicativo v2.0. Para obter instruções sobre como integrar-se ao serviço AD do Azure disponível ao público geral, consulte o [Guia do desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md).

O [OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) estende o protocolo de *autorização* do OAuth 2.0 para uso como um protocolo de *autenticação*, o que permite executar o logon único usando o OAuth. Ele apresenta o conceito de um `id_token`, que é um token de segurança que permite ao cliente verificar a identidade do usuário e obter informações básicas de perfil sobre o usuário. Como ele estende o OAuth 2.0, também permite que aplicativos adquiram **access\_tokens** com segurança, os quais podem ser usados para acessar os recursos protegidos por um [servidor de autorização](active-directory-v2-protocols.md#the-basics). O OpenID Connect é a nossa recomendação se você estiver criando um[ aplicativo Web](active-directory-v2-flows.md#web-apps) que fica hospedado em um servidor e é acessado por meio de um navegador.

## Enviar a solicitação de conexão
Quando o aplicativo Web precisa autenticar o usuário, ele pode direcionar o usuário para o ponto de extremidade `/authorize`. Essa solicitação é semelhante ao primeiro segmento do [Fluxo de código de autorização do OAuth 2.0](active-directory-v2-protocols-oauth-code.md), com algumas diferenças importantes:

- A solicitação deve incluir o escopo `openid` no parâmetro `scope`.
- O parâmetro `response_type` deve incluir `id_token`
- A solicitação deve incluir o parâmetro `nonce`.

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=query							      // 'query', 'form_post', or 'fragment'
&scope=openid										 // Translates to the 'Read your profile' permission
&state=12345						 				 // Any value, provided by your app
&nonce=678910										 // Any value, provided by your app
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| client\_id | obrigatório | A Id de Aplicativo que o portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) atribuiu ao aplicativo. |
| response\_type | obrigatório | Deve incluir `id_token` para conexão do OpenID Connect. Também pode incluir outros response\_types, como `code`. |
| redirect\_uri | obrigatório | O redirect\_uri do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve corresponder exatamente a um dos redirect\_uris que você registrou no portal, com exceção de que ele deve ser codificado por url. |
| scope | obrigatório | Uma lista de escopos separados por espaços. Para o OpenID Connect, ele deve incluir o escopo `openid`, que é traduzido para a permissão "Conectar e ler seu perfil" na interface de usuário de consentimento. Você também pode incluir outros escopos nesta solicitação para solicitar o consentimento. |
| nonce | obrigatório | Um valor incluído na solicitação, gerado pelo aplicativo, que será incluído no id\_token resultante como uma declaração. O aplicativo pode verificar esse valor para reduzir os ataques de reprodução de token. Normalmente, o valor é uma cadeia de caracteres aleatória e exclusiva que pode ser usada para identificar a origem da solicitação. |
| response\_mode | recomendável | Especifica o método que deve ser usado para enviar o authorization\_code resultante de volta ao aplicativo. Pode ser um de 'query', 'form\_post' ou 'fragment'.  
| state | recomendável | Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor exclusivo gerado aleatoriamente que normalmente é usado para impedir ataques de solicitação intersite forjada. O estado também é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| prompt | opcional | Indica o tipo de interação do usuário que é necessário. Os únicos valores válidos no momento são 'login', 'none', 'consent'. O `prompt=login` forçará o usuário a inserir suas credenciais na solicitação, eliminando o logon único. `prompt=none` é o oposto. Ele garantirá que o usuário não receba qualquer prompt interativo. Se não for possível concluir a solicitação silenciosamente por meio do logon único, o ponto de extremidade v 2.0 retornará um erro. `prompt=consent` acionará a caixa de diálogo de consentimento do OAuth depois que o usuário fazer logon, pedindo que o usuário conceda permissões para o aplicativo. |
| login\_hint | opcional | Pode ser usado para preencher previamente o campo de nome de usuário/endereço de email da página de entrada do usuário. |

Nesse ponto, será solicitado que o usuário insira suas credenciais e conclua a autenticação. O ponto de extremidade v2.0 também garantirá que o usuário tenha consentido as permissões indicadas no parâmetro de consulta `scope`. Se o usuário não tiver consentido nenhuma dessas permissões, ele será solicitado a consentir as permissões necessárias. Os detalhes dos aplicativos quanto a [permissões, consentimento e multilocatário são fornecidos aqui](active-directory-v2-scopes.md).

Depois que o usuário se autentica e dá consentimento, o ponto de extremidade v2.0 retorna uma resposta ao aplicativo no `redirect_uri` indicado usando o método especificado no parâmetro `response_mode`.

Uma resposta bem-sucedida usando `response_mode=query` tem a seguinte aparência:

```
GET https://localhost/myapp/?
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q... 	// the id_token, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  	// the value provided in the request
&id_token_expires_in=3600

```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| id\_token | O id\_token que o aplicativo solicitou. Você pode usar o id\_token para verificar a identidade do usuário e iniciar uma sessão com o usuário. Mais detalhes sobre id\_tokens e respectivo conteúdo estão incluídos na [referência ao token do ponto de extremidade v2.0](active-directory-v2-tokens.md). |
| session\_state | Um valor exclusivo que identifica a sessão de usuário atual. Esse valor é um GUID, mas deve ser tratado como um valor opaco que é transmitido sem verificação. |
| state | Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |
| id\_token\_expires\_in | Por quanto tempo o token de ID é válido (em segundos). |


As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente:

```
GET https://localhost/myapp/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| error | Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error\_description | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

## Validar o id\_token
Apenas receber o id\_token não é suficiente para autenticar o usuário; você deve validar a assinatura do id\_token e verificar as declarações no token de acordo com os requisitos do aplicativo. O ponto de extremidade v2.0 usa [JWTs (Tokens Web JSON)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) e criptografia de chave pública para assinar tokens e verificar se eles são válidos.

O modelo de aplicativo v2.0 tem um ponto de extremidade de metadados do OpenID Connect, que permite a um aplicativo buscar informações sobre o modelo de aplicativo v2.0 no tempo de execução. Essas informações incluem pontos de extremidade, conteúdos de token e chaves de assinatura de token. O ponto de extremidade de metadados contém um documento JSON localizado em:

`https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`

Uma das propriedades desse documento de configuração é o `jwks_uri`, cujo valor para o modelo de aplicativo v2.0 será:

`https://login.microsoftonline.com/common/discovery/v2.0/keys`.

Você pode usar as chaves públicas RSA256 localizadas nesse ponto de extremidade para validar a assinatura do id\_token. Há várias chaves listadas nesse ponto de extremidade em qualquer ponto no tempo, cada uma identificada por um `kid`. O cabeçalho do id\_token também contém uma declaração `kid`, que indica quais dessas chaves foi usada para assinar o id\_token.

Confira a [referência ao token do modelo de aplicativo v2.0](active-directory-v2-tokens.md) para saber mais, incluindo [Validando tokens](active-directory-v2-tokens.md#validating-tokens) e [Informações importantes sobre substituição de chave de assinatura](active-directory-v2-tokens.md#validating-tokens). <!--TODO: Improve the information on this-->

Depois que tiver validado a assinatura do id\_token, haverá algumas declarações que você precisará verificar:

- Você deve validar a declaração `nonce` para evitar ataques de reprodução de token. Seu valor deve ser o que você especificou na solicitação de conexão.
- Você deve validar a declaração `aud` para garantir que o id\_token foi emitido pelo seu aplicativo. Seu valor deve ser o `client_id` do seu aplicativo.
- Você deve validar as declarações `iat` e `exp` para garantir que o id\_token não expirou.

Talvez você também queira validar declarações adicionais, dependendo do cenário. Algumas validações comuns incluem:

- Garantir que o usuário/organização tenha assinado para usar o aplicativo.
- Garantir que o usuário tenha autorização/privilégios adequados.
- Garantir que uma determinada intensidade de autenticação tenha ocorrido, como autenticação multifator.

Para saber mais sobre declarações em um id\_token, confira a [referência ao token do modelo de aplicativo v2.0](active-directory-v2-tokens.md).

Depois de ter validado completamente o id\_token, você poderá iniciar uma sessão com o usuário e usar declarações no id\_token para obter informações sobre o usuário no seu aplicativo. Essas informações podem ser usadas para exibição, registros, autorizações, etc.

## Enviar uma solicitação de desconexão

Atualmente, o `end_session_endpoint` do OpenIdConnect não tem suporte na visualização do modelo de aplicativo v2.0. Isso significa que o aplicativo não pode enviar uma solicitação ao ponto de extremidade v2.0 para encerrar uma sessão do usuário e limpar os cookies definidos pelo ponto de extremidade v2.0. Para desconectar um usuário, o aplicativo pode simplesmente encerrar sua própria sessão com o usuário e deixar a sessão do usuário com o ponto de extremidade v2.0 intacta. Na próxima vez que o usuário tentar se conectar, ele verá uma página "escolher conta", com as suas contas ativamente conectadas listadas. Nessa página, o usuário pode optar por se desconectar de qualquer conta, encerrando a sessão com o ponto de extremidade v2.0.

<!--

When you wish to sign the user out of the  app, it is not sufficient to clear your app's cookies or otherwise end the session with the user.  You must also redirect the user to the v2.0 endpoint for sign out.  If you fail to do so, the user will be able to re-authenticate to your app without entering their credentials again, because they will have a valid single sign-on session with the v2.0 endpoint.

You can simply redirect the user to the `end_session_endpoint` listed in the OpenID Connect metadata document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommended | The URL which the user should be redirected to after successful logout.  If not included, the user will be shown a generic message by the v2.0 endpoint.  |

-->

## Diagrama de resumo de entrada
O fluxo de conexão mais básico contém as seguintes etapas:

![Raias do OpenId Connect](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

## Obter tokens de acesso
Muitos aplicativos Web precisam não apenas conectar o usuário, mas acessar um serviço Web em nome desse usuário usando o OAuth. Esse cenário combina OpenID Connect para autenticação de usuário enquanto adquire simultaneamente um authorization\_code que pode ser usado para obter access\_tokens usando o Fluxo do código de autorização do OAuth. Para obter tokens de acesso, você precisará modificar um pouco a solicitação de entrada acima:


```
// Line breaks for legibility only

GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=query							      // 'query', 'form_post', or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes your app needs  
offline_access%20										 
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
&state=12345						 				 // Any value, provided by your app
&nonce=678910										 // Any value, provided by your app
```

Ao incluir escopos de permissão na solicitação e usar `response_type=code+id_token`, o ponto de extremidade v 2.0 terá certeza de que o usuário consentiu às permissões indicadas no parâmetro de consulta `scope` e retornará ao seu aplicativo um código de autorização para trocar por um token de acesso.

Uma resposta bem-sucedida usando `response_mode=query` tem a seguinte aparência:

```
GET https://localhost/myapp/?
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q... 	// the id_token, truncated
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq... 	// the authorization_code, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  	// the value provided in the request
&id_token_expires_in=3599

```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| id\_token | O id\_token que o aplicativo solicitou. Você pode usar o id\_token para verificar a identidade do usuário e iniciar uma sessão com o usuário. Mais detalhes sobre id\_tokens e respectivo conteúdo estão incluídos na [referência ao token do modelo de aplicativo v2.0](active-directory-v2-tokens.md). |
| código | O authorization\_code que o aplicativo solicitou. O aplicativo pode usar o código de autorização para solicitar um token de acesso para o recurso de destino. Authorization\_codes têm uma duração bastante curta, geralmente, expirando depois de aproximadamente 10 minutos. |
| session\_state | Um valor exclusivo que identifica a sessão de usuário atual. Esse valor é um GUID, mas deve ser tratado como um valor opaco que é transmitido sem verificação. |
| state | Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |
| id\_token\_expires\_in | Por quanto tempo o token de ID é válido (em segundos). |

As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente:

```
GET https://localhost/myapp/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| error | Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error\_description | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

Após você conseguir uma autorização `code` e um `id_token`, poderá conectar o usuário e obter tokens de acesso em seu nome. Para conectar o usuário, você deve validar o `id_token` exatamente como descrito [acima](#validating-the-id-token). Para obter tokens de acesso, você pode seguir as etapas descritas em nossa [documentação do protocolo OAuth](active-directory-v2-protocols-oidc.md#request-an-access-token).

## Diagrama de resumo de aquisição de token

Para referência, o fluxo inteiro de entrada e aquisição de token do OpenID Connect parece com o seguinte:

![Raias do OpenId Connect](../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

<!---HONumber=AcomDC_1217_2015-->