<properties
	pageTitle="Fluxo implícito do Azure AD v 2.0 | Microsoft Azure"
	description="Compilando aplicativos Web usando a implementação v2.0 do Azure AD do fluxo implícito para aplicativos de página única."
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
	ms.date="02/20/2016"
	ms.author="dastrock"/>

# Protocolos v2.0 - SPAs que usam o fluxo implícito
Com o ponto de extremidade v2.0, você pode autenticar usuários em seus aplicativos de página única com contas pessoais e corporativas/de estudante da Microsoft. Aplicativos de página única e outros aplicativos JavaScript executados principalmente em um navegador enfrentam desafios interessantes quando o assunto é autenticação:

- As características de segurança desses aplicativos são consideravelmente diferentes dos aplicativos Web tradicionais baseados em servidor.
- Muitos servidores de autorização e provedores de identidade não dão suporte a solicitações CORS.
- Redirecionamentos para fora do aplicativo em páginas inteiras do navegador se tornam algo particularmente invasivo para a experiência do usuário.

Para esses aplicativos (por exemplo: AngularJS, Ember.js, React.js etc), o Azure AD oferece suporte ao Fluxo de concessão implícita do OAuth 2.0. O fluxo está descrito na [Especificação do OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-4.2). O principal benefício é que ele permite que o aplicativo obtenha tokens do Azure AD sem executar uma troca de credenciais de servidor back-end. Isso permite que o aplicativo autentique o usuário, mantenha a sessão e obtenha tokens para outras APIs da Web, tudo dentro do código JavaScript do cliente. Há algumas considerações de segurança importantes que você deve levar em conta ao usar o fluxo implícito, especificamente sobre [cliente](http://tools.ietf.org/html/rfc6749#section-10.3) e [representação de usuário](http://tools.ietf.org/html/rfc6749#section-10.3).

Se você quiser usar o fluxo implícito e o Azure AD para adicionar autenticação ao seu aplicativo JavaScript, recomendamos nossa biblioteca JavaScript de código aberto, a [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js). Há alguns tutoriais sobre AngularJS [aqui](active-directory-appmodel-v2-overview.md#getting-started) para ajudá-lo a começar.

No entanto, se você preferir não usar uma biblioteca em seu aplicativo de página única e enviar mensagens de protocolo por conta própria, siga as etapas gerais a seguir.

> [AZURE.NOTE]
	Nem todos os recursos e cenários do Azure Active Directory têm suporte no ponto de extremidade v2.0. Para determinar se você deve usar o ponto de extremidade v2.0, leia sobre as [limitações da v2.0](active-directory-v2-limitations.md).
    
## Diagrama de protocolo
O fluxo completo de entrada implícita é semelhante a este. Cada uma das etapas são descritas em detalhes abaixo.

![Raias do OpenId Connect](../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

## Enviar a solicitação de conexão

Para autenticar inicialmente o usuário em seu aplicativo, você pode enviar uma solicitação de autorização [OpenID Connect](active-directory-v2-protocols-oidc.md) e obter um `id_token` do ponto de extremidade v 2.0:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token+token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&response_mode=fragment
&state=12345
&nonce=678910
```

> [AZURE.TIP] Tente colar a solicitação abaixo em um navegador!

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token+token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| locatário | obrigatório | O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas do protocolo](active-directory-v2-protocols.md#endpoints). |
| client\_id | obrigatório | A Id de Aplicativo que o portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) atribuiu ao aplicativo. |
| response\_type | obrigatório | Deve incluir `id_token` para conexão do OpenID Connect. Também pode incluir o response\_type `token`. O uso de `token` aqui permitirá que seu aplicativo receba imediatamente um token de acesso do ponto de extremidade de autorização sem precisar fazer uma segunda solicitação ao ponto de extremidade de autorização. Se você usar o response\_type de `token`, o parâmetro `scope` deverá conter um escopo indicando para quais recursos o token será emitido. |
| redirect\_uri | recomendável | O redirect\_uri do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve corresponder exatamente a um dos redirect\_uris que você registrou no portal, com exceção de que ele deve ser codificado por url. |
| scope | obrigatório | Uma lista de escopos separados por espaços. Para o OpenID Connect, é necessário incluir o escopo `openid`, que é traduzido como a permissão “Fazer seu logon” na interface do usuário de consentimento. Como opção, convém incluir os [escopos](active-directory-v2-scopes.md) `email` ou `profile` para obter acesso a dados adicionais do usuário. Você também pode incluir outros escopos nessa solicitação a fim de solicitar o consentimento a vários recursos. |
| response\_mode | recomendável | Especifica o método que deve ser usado para enviar o token resultante de volta ao aplicativo. Deve ser `fragment` para o fluxo implícito. |
| state | recomendável | Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor exclusivo gerado aleatoriamente que normalmente é usado para [impedir ataques de solicitação entre sites forjada](http://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| nonce | obrigatório | Um valor incluído na solicitação, gerado pelo aplicativo, que será incluído no id\_token resultante como uma declaração. O aplicativo pode verificar esse valor para reduzir os ataques de reprodução de token. Normalmente, o valor é uma cadeia de caracteres aleatória e exclusiva que pode ser usada para identificar a origem da solicitação. |
| prompt | opcional | Indica o tipo de interação do usuário que é necessário. Os únicos valores válidos no momento são 'login', 'none', 'consent'. O `prompt=login` forçará o usuário a inserir suas credenciais na solicitação, eliminando o logon único. `prompt=none` é o oposto. Ele garantirá que o usuário não receba qualquer prompt interativo. Se não for possível concluir a solicitação silenciosamente por meio do logon único, o ponto de extremidade v 2.0 retornará um erro. `prompt=consent` acionará a caixa de diálogo de consentimento do OAuth depois que o usuário fazer logon, pedindo que o usuário conceda permissões para o aplicativo. |
| login\_hint | opcional | Pode ser usado para preencher previamente o campo de nome de usuário/endereço de email da página de entrada do usuário, se você souber o nome de usuário com antecedência. Geralmente, os aplicativos usarão esse parâmetro durante a reautenticação, após já terem extraído o nome de usuário de uma entrada anterior usando a declaração `preferred_username`. |
| domain\_hint | opcional | Pode ser `consumers` ou `organizations`. Se for incluído, ele ignorará o processo de descoberta baseada em email que o usuário passa na página de entrada v 2.0, resultando em uma experiência de usuário um pouco mais simples. Geralmente, os aplicativos usam esse parâmetro durante a reautenticação, extraindo a declaração `tid` do id\_token. Se o valor da declaração `tid` for `9188040d-6c67-4c5b-b112-36a304b66dad`, você deverá usar `domain_hint=consumers`. Caso contrário, use `domain_hint=organizations`. |

Nesse ponto, será solicitado que o usuário insira suas credenciais e conclua a autenticação. O ponto de extremidade v2.0 também garantirá que o usuário tenha consentido as permissões indicadas no parâmetro de consulta `scope`. Se o usuário não tiver consentido nenhuma dessas permissões, ele será solicitado a consentir as permissões necessárias. Os detalhes dos aplicativos quanto a [permissões, consentimento e multilocatário são fornecidos aqui](active-directory-v2-scopes.md).

Depois que o usuário se autentica e dá consentimento, o ponto de extremidade v2.0 retorna uma resposta ao aplicativo no `redirect_uri` indicado usando o método especificado no parâmetro `response_mode`.

#### Resposta bem-sucedida

Uma resposta bem-sucedida usando `response_mode=fragment` e `response_type=id_token+token` é semelhante ao seguinte, com quebras de linha para legibilidade:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope=https%3a%2f%2fgraph.microsoft.com%2fmail.read 
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| access\_token | Incluído se `response_type` incluir `token`. O token de acesso solicitado pelo aplicativo, nesse caso para o Microsoft Graph. O token de acesso não deve ser decodificado ou inspecionado de outra forma, ele pode ser tratado como uma cadeia de caracteres opaca. |
| token\_type | Incluído se `response_type` incluir `token`. Sempre será `Bearer`. |
| expires\_in | Incluído se `response_type` incluir `token`. Indica o número de segundos durante os quais o token é válido, para fins de caching. |
| scope | Incluído se `response_type` incluir `token`. Indica os escopos para os quais access\_token será válido. |
| id\_token | O id\_token que o aplicativo solicitou. Você pode usar o id\_token para verificar a identidade do usuário e iniciar uma sessão com o usuário. Mais detalhes sobre id\_tokens e respectivo conteúdo estão incluídos na [referência ao token do ponto de extremidade v2.0](active-directory-v2-tokens.md). |
| state | Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |


#### Resposta de erro
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

Você pode escolher validar o `id_token` no código do cliente, mas uma prática comum é enviar o `id_token` para um servidor de back-end e executar a validação nele. Após a validação da assinatura do id\_token, será necessário verificar algumas declarações: Consulte a [referência do token da v2.0](active-directory-v2-tokens.md) para saber mais, incluindo [Validação de Tokens](active-directory-v2-tokens.md#validating-tokens) e [Informações Importantes sobre a Substituição de Chave de Assinatura](active-directory-v2-tokens.md#validating-tokens). Recomendamos o uso de uma biblioteca para analisar e validar os tokens. Há pelo menos uma disponível para a maioria das linguagens e plataformas. <!--TODO: Improve the information on this-->

Talvez você também queira validar declarações adicionais, dependendo do cenário. Algumas validações comuns incluem:

- Garantir que o usuário/organização tenha assinado para usar o aplicativo.
- Garantir que o usuário tenha autorização/privilégios adequados.
- Garantir que uma determinada intensidade de autenticação tenha ocorrido, como autenticação multifator.

Para saber mais sobre declarações em um id\_token, consulte a [referência do token do ponto de extremidade v2.0](active-directory-v2-tokens.md).

Depois de ter validado completamente o id\_token, você poderá iniciar uma sessão com o usuário e usar declarações no id\_token para obter informações sobre o usuário no seu aplicativo. Essas informações podem ser usadas para exibição, registros, autorizações, etc.

## Obter tokens de acesso

Agora que você autenticou o usuário em seu aplicativo de página única, pode obter tokens de acesso para chamar APIs da Web protegidas pelo Azure AD, como o [Microsoft Graph](https://graph.microsoft.io). Mesmo se já tiver recebido um token usando o response\_type `token`, você poderá usar esse método para adquirir tokens para recursos adicionais sem precisar redirecionar o usuário para entrar novamente.

No fluxo normal de OpenID Connect/OAuth, você faria isso por meio de uma solicitação para o ponto de extremidade `/token` do v2.0. No entanto, o ponto de extremidade v 2.0 não suporta solicitações CORS, portanto, fazer chamadas AJAX para obter e atualizar tokens está fora de cogitação. Em vez disso, você pode usar o fluxo implícito em um iframe oculto para obter novos tokens para outras APIs da Web:

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment
&state=12345&nonce=678910
&prompt=none
&domain_hint=organizations
&login_hint=myuser@mycompany.com
```

> [AZURE.TIP] Tente colar a solicitação abaixo em um navegador (mas, se você quiser ter êxito, modifique os valores de domain\_hint e login\_hint primeiro)

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&response_mode=fragment&state=12345&nonce=678910&prompt=none&domain_hint=organizations&login_hint=myuser@mycompany.com
```

| Parâmetro | | Descrição |
| ----------------------- | ------------------------------- | --------------- |
| locatário | obrigatório | O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são `common`, `organizations`, `consumers` e identificadores de locatário. Para obter mais detalhes, consulte [noções básicas do protocolo](active-directory-v2-protocols.md#endpoints). |
| client\_id | obrigatório | A Id de Aplicativo que o portal de registro ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) atribuiu ao aplicativo. |
| response\_type | obrigatório | Deve incluir `id_token` para conexão do OpenID Connect. Também pode incluir outros response\_types, como `code`. |
| redirect\_uri | recomendável | O redirect\_uri do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve corresponder exatamente a um dos redirect\_uris que você registrou no portal, com exceção de que ele deve ser codificado por url. |
| scope | obrigatório | Uma lista de escopos separados por espaços. Para obter tokens, inclua todos os [escopos](active-directory-v2-scopes.md) necessários para o recurso no qual você está interessado. |
| response\_mode | recomendável | Especifica o método que deve ser usado para enviar o token resultante de volta ao aplicativo. Pode ser `query`, `form_post` ou `fragment`. |
| state | recomendável | Um valor incluído na solicitação também será retornado na resposta do token. Pode ser uma cadeia de caracteres de qualquer conteúdo desejado. Um valor exclusivo gerado aleatoriamente que normalmente é usado para impedir ataques de solicitação intersite forjada. O estado também é usado para codificar as informações sobre o estado do usuário no aplicativo antes da solicitação de autenticação ocorrida, como a página ou exibição em que ele estava. |
| nonce | obrigatório | Um valor incluído na solicitação, gerado pelo aplicativo, que será incluído no id\_token resultante como uma declaração. O aplicativo pode verificar esse valor para reduzir os ataques de reprodução de token. Normalmente, o valor é uma cadeia de caracteres aleatória e exclusiva que pode ser usada para identificar a origem da solicitação. |
| prompt | obrigatório | Para atualizar e obter tokens em um iframe oculto, você deve usar o `prompt=none` para garantir que o iframe não pare de responder na página de entrada v2.0 e retorne imediatamente. |
| login\_hint | obrigatório | Para atualizar e obter tokens em um iframe oculto, você deve incluir o nome de usuário nessa dica a fim de distinguir entre várias sessões que o usuário pode ter em um determinado momento. Você pode extrair o nome de usuário de uma entrada anterior usando a declaração `preferred_username`. |
| domain\_hint | obrigatório | Pode ser `consumers` ou `organizations`. Para atualizar e obter tokens em um iframe oculto, você deve incluir o domain\_hint na solicitação. Você deve extrair a declaração `tid` do id\_token de uma entrada anterior para determinar qual valor usar. Se o valor da declaração `tid` for `9188040d-6c67-4c5b-b112-36a304b66dad`, você deverá usar `domain_hint=consumers`. Caso contrário, use `domain_hint=organizations`. |

Graças ao parâmetro `prompt=none`, essa solicitação terá êxito ou falhará imediatamente e retornará ao seu aplicativo. Uma resposta bem-sucedida será enviada ao seu aplicativo no `redirect_uri` indicado, usando o método especificado no parâmetro `response_mode`.

#### Resposta bem-sucedida
Uma resposta bem-sucedida usando `response_mode=fragment` tem a seguinte aparência:

```
GET https://localhost/myapp/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=12345
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read
```

| Parâmetro | Descrição |
| ----------------------- | ------------------------------- |
| access\_token | O token solicitado pelo aplicativo. |
| token\_type | Sempre será `Bearer`. |
| state | Se um parâmetro de estado estiver incluído na solicitação, o mesmo valor deverá aparecer na resposta. O aplicativo deve verificar se os valores de estado na solicitação e na resposta são idênticos. |
| expires\_in | Por quanto tempo o token de acesso é válido (em segundos). |
| scope | Os escopos para os quais o access\_token é válido. |

#### Resposta de erro
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

Atualmente, o `end_session_endpoint` do OpenIdConnect não tem suporte no ponto de extremidade v2.0. Isso significa que o aplicativo não pode enviar uma solicitação ao ponto de extremidade v2.0 para encerrar uma sessão do usuário e limpar os cookies definidos pelo ponto de extremidade v2.0. Para desconectar um usuário, o aplicativo pode simplesmente encerrar sua própria sessão com o usuário e deixar a sessão do usuário com o ponto de extremidade v2.0 intacta. Na próxima vez que o usuário tentar se conectar, ele verá uma página "escolher conta", com as suas contas ativamente conectadas listadas. Nessa página, o usuário pode optar por se desconectar de qualquer conta, encerrando a sessão com o ponto de extremidade v2.0.

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

<!---HONumber=AcomDC_0224_2016-->