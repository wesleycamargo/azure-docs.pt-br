<properties
	pageTitle="Fluxo implícito do modelo de aplicativo v 2.0 | Microsoft Azure"
	description="Compilando aplicativos Web usando a implementação do Azure AD do fluxo implícito para aplicativos de página única."
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
	ms.date="11/19/2015"
	ms.author="dastrock"/>

# Visualização do modelo de aplicativo v2.0: protocolos — Fluxo implícito
Com o modelo de aplicativo v2.0 do Azure AD, você pode autenticar usuários em seus aplicativos de página única com contas pessoais e corporativas/de estudante da Microsoft. Aplicativos de página única e outros aplicativos JavaScript executados principalmente em um navegador enfrentam desafios interessantes quando o assunto é autenticação:

- As características de segurança desses aplicativos são consideravelmente diferentes dos aplicativos Web tradicionais baseados em servidor.
- Muitos servidores de autorização e provedores de identidade não oferecem suporte a solicitações CORS por motivos de segurança bem documentados.
- Redirecionamentos para fora do aplicativo em páginas inteiras do navegador se tornam algo particularmente invasivo para a experiência do usuário.

Para esses aplicativos (por exemplo: AngularJS, Ember.js, React.js etc), o Azure AD oferece suporte ao Fluxo de concessão implícita do OAuth 2.0. O fluxo está descrito na [Especificação do OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.2). O principal benefício é que ele permite que o aplicativo obtenha tokens do Azure AD sem executar uma troca de credenciais de servidor back-end. Isso permite que o aplicativo autentique o usuário, mantenha a sessão e obtenha tokens para outras APIs da Web, tudo dentro do código JavaScript do cliente. Há algumas considerações de segurança importantes que você deve levar em conta ao usar o fluxo implícito, especificamente sobre [cliente](http://tools.ietf.org/html/rfc6749#section-10.3) e [representação de usuário](http://tools.ietf.org/html/rfc6749#section-10.3).

Se você quiser usar o fluxo implícito e o Azure AD para adicionar autenticação ao seu aplicativo JavaScript, recomendamos nossa biblioteca JavaScript de código aberto, a [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js). Há alguns tutoriais sobre AngularJS [aqui](active-directory-appmodel-v2-overview.md#getting-started) para ajudá-lo a começar.

No entanto, se você preferir não usar uma biblioteca em seu aplicativo de página única e enviar mensagens de protocolo por conta própria, siga as etapas gerais a seguir.

> [AZURE.NOTE]Essas informações se aplicam à visualização pública do modelo de aplicativo v2.0. Para obter instruções sobre como integrar-se ao serviço AD do Azure disponível ao público geral, consulte o [Guia do desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md).

## Enviar a solicitação de conexão

Para autenticar inicialmente o usuário em seu aplicativo, você pode enviar uma solicitação de autorização [OpenID Connect](active-directory-v2-protocols-oidc.md) e obter um `id_token` do ponto de extremidade v 2.0:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=fragment
&scope=openid%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
&state=12345
&nonce=678910
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

Uma resposta bem-sucedida usando `response_mode=fragment` tem a seguinte aparência:

```
GET https://localhost/myapp/#
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
GET https://localhost/myapp/#
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

Consulte a [referência ao token do modelo de aplicativo v2.0](active-directory-v2-tokens.md) para obter mais informações, incluindo [Validando tokens](active-directory-v2-tokens.md#validating-tokens) e [Informações importantes sobre substituição de chave de assinatura](active-directory-v2-tokens.md#validating-tokens). <!--TODO: Improve the information on this-->

Você pode escolher validar o `id_token` no código do cliente, mas uma prática comum é enviar o `id_token` a um servidor back-end e executar a validação lá. Há muitas bibliotecas disponíveis para analisar e validar os tokens JWT. Depois que tiver validado a assinatura do id\_token, haverá algumas declarações que você precisará verificar:

- Você deve validar a declaração `nonce` para evitar ataques de reprodução de token. Seu valor deve ser o que você especificou na solicitação de conexão.
- Você deve validar a declaração `aud` para garantir que o id\_token foi emitido pelo seu aplicativo. Seu valor deve ser o `client_id` do seu aplicativo.
- Você deve validar as declarações `iat` e `exp` para garantir que o id\_token não expirou.

Talvez você também queira validar declarações adicionais, dependendo do cenário. Algumas validações comuns incluem:

- Garantir que o usuário/organização tenha assinado para usar o aplicativo.
- Garantir que o usuário tenha autorização/privilégios adequados.
- Garantir que uma determinada intensidade de autenticação tenha ocorrido, como autenticação multifator.

Para saber mais sobre declarações em um id\_token, consulte a [referência ao token do modelo de aplicativo v2.0](active-directory-v2-tokens.md).

Depois de ter validado completamente o id\_token, você poderá iniciar uma sessão com o usuário e usar declarações no id\_token para obter informações sobre o usuário no seu aplicativo. Essas informações podem ser usadas para exibição, registros, autorizações, etc.

## Obter tokens de acesso

Agora que você autenticou o usuário em seu aplicativo de página única, pode obter tokens de acesso para chamar APIs da Web protegidas pelo Azure AD, como o [Microsoft Graph](https://graph.microsoft.io). No fluxo normal de OpenID Connect/OAuth, você faria isso por meio de uma solicitação para o ponto de extremidade `/token` do v2.0. No entanto, o ponto de extremidade v 2.0 não suporta solicitações CORS, portanto, fazer chamadas AJAX para obter e atualizar tokens está fora de cogitação. Em vez disso, você pode usar o fluxo implícito em um iframe oculto para obter novos tokens para outras APIs da Web:

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=fragment
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read  // The space separated scope(s) you want an access token for
&state=12345
&prompt=none
&login_hint=joe.user@outlook.com
&domain_hint=consumers
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| client\_id | obrigatório | A Id de Aplicativo que o portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) atribuiu ao aplicativo. |
| response\_type | obrigatório | Deve ser `token` para o fluxo implícito. |
| redirect\_uri | obrigatório | O redirect\_uri do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve corresponder exatamente a um dos redirect\_uris que você registrou no portal, com exceção de que ele deve ser codificado por url. |
| scope | obrigatório | Uma lista separada por espaço de escopos dos quais você precisa no token de acesso. |
| response\_mode | recomendável | Especifica o método que deve ser usado para enviar o authorization\_code resultante de volta ao aplicativo. Pode ser um de 'query', 'form\_post' ou 'fragment'.  
| state | recomendável | Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor exclusivo gerado aleatoriamente que normalmente é usado para impedir ataques de solicitação intersite forjada. O estado também é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| prompt | obrigatório | Aqui, usamos `prompt=none` para especificar que o Azure AD não deve apresentar qualquer interface de usuário. Como essa solicitação ocorre em um iframe oculto, convém garantir que se o token não puder ser adquirido silenciosamente, o Azure AD deverá retornar um erro em vez de pedir ao usuário para entrar. |
| login\_hint | obrigatório | Necessário para a autenticação sem confirmação. Essa dica ajuda o Azure AD a distinguir entre várias contas, se o usuário tiver uma sessão ativa com mais de uma conta. O valor fornecido aqui pode ser o valor exato que você recebe na declaração `preferred_uesrname` do id\_token. |
| domain\_hint | obrigatório | Necessário para a autenticação sem confirmação. Para esse fluxo, o domain\_hint pode ser um dos dois valores: `consumers` ou `organizations`. Se o valor da declaração `tid` no `id_token` recebido for exatamente '9188040d-6c 67-4c5b-b112-36a304b66dad', use `consumers` como o domain\_hint. Caso contrário, use `organizations`. |

Graças ao parâmetro `prompt=none`, essa solicitação terá êxito ou falhará imediatamente e retornará ao seu aplicativo. Uma resposta bem-sucedida será enviada ao seu aplicativo no `redirect_uri` indicado, usando o método especificado no parâmetro `response_mode`.

Uma resposta bem-sucedida usando `response_mode=fragment` tem a seguinte aparência:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q... 	// the access_token, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  	// the value provided in the request
&expires_in=3600
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read

```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| access\_token | O token solicitado pelo aplicativo. |
| session\_state | Um valor exclusivo que identifica a sessão de usuário atual. Esse valor é um GUID, mas deve ser tratado como um valor opaco que é transmitido sem verificação. |
| state | Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |
| expires\_in | Por quanto tempo o token de acesso é válido (em segundos). |
| scope | Os escopos para os quais o access\_token é válido. |

As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente. No caso de `prompt=none`, um erro esperado será:

```
GET https://localhost/myapp/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| error | Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error\_description | Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |

Se você receber esse erro na solicitação do iframe, o usuário deverá entrar novamente de forma interativa para recuperar um novo token. Você pode escolher lidar com isso da maneira que fizer mais sentido para seu aplicativo.

## Atualizando tokens

`id_token`s e `access_token`s expirarão após um curto período. Portanto, seu aplicativo deve estar preparado para atualizar esses tokens periodicamente. Para atualizar qualquer um desses tipos de tokens, você pode executar a mesma solicitação de iframe oculto mencionada acima usando o parâmetro `prompt=none` para controlar o comportamento do Azure AD. Se você quiser receber um novo `id_token`, use `response_type=id_token` e `scope=openid`, bem como um parâmetro `nonce`.


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

<!---HONumber=AcomDC_1203_2015-->